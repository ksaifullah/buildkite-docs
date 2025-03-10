# Securing your Buildkite Agent

In cases where a Buildkite Agent is being deployed into a sensitive environment there are a few default settings and techniques which may be adjusted.


## Securely storing secrets

For best practices and recommendations about secret storage in the Agent, see the [Managing secrets](/docs/pipelines/secrets) guide.

## Disabling automatic ssh-keyscan

By default the agent will automatically accept the Git SSH host using the `ssh-keyscan` command when doing the first checkout on a new agent host. The agent runs a similar command to this:

```bash
ssh-keyscan "<host>" >> "~/.ssh/known_hosts"
```

If you choose to disable this functionality, you'll need to manually perform your first checkout, or ensure the SSH fingerprint of your source code host is already present on your build machine.

Automatic ssh-keyscan can be disabled by setting [`no-ssh-keyscan`](/docs/agent/v3/configuration#no-ssh-keyscan):

* Environment variable: `BUILDKITE_NO_SSH_KEYSCAN=true`
* Command line flag: `--no-ssh-keyscan`
* Configuration setting: `no-ssh-keyscan=true`

## Disabling command eval

By default the agent allows you to run any command on the build server (for example, `make test`). You can disable command evaluation and allow only the execution of scripts (with no ability to pass command line flags). Disabling command evaluation will also disable plugin execution.

Once disabled your build steps will need to be checked into your repository as scripts, and the only way to pass arguments is using environment variables.


This option is intended to protect your infrastructure from a scenario where Buildkite itself gets compromised, and subsequently sends malicious commands to your agents. It is not designed, nor effective at protecting against malicious actors with commit access to your repositories.


Command line evaluation can be disabled by setting [`no-command-eval`](/docs/agent/v3/configuration#no-command-eval):

* Environment variable: `BUILDKITE_NO_COMMAND_EVAL=1`
* Command line flag: `--no-command-eval`
* Configuration setting: `no-command-eval=true`

>🚧 Custom hooks
> If you have a custom <code>command</code> hook, using <code>no-command-eval</code> will have no effect on your command execution. See <a href="#allowing-a-list-of-plugins">Allowing a list of plugins</a> and <a href="#customizing-the-bootstrap">Custom bootstrap scripts</a> for examples of how to completely lock down your agent from arbitrary code execution.

## Disabling plugins

As plugins execute in the same way as local hooks, they can pose a potential security risk. If you're using third party plugins, you'll be executing the third party's code on your agent.

You can disable plugins with the command line flag: `--no-plugins` or the [`no-plugins`](/docs/agent/v3/configuration#no-plugins) setting.

If you still want to use plugins, you can check out a tool for [signing pipelines](/docs/agent/v3/securing#signing-pipelines).



## Disabling local hooks

Local hooks are hooks defined in pipeline's repository.

If you have enforced a security policy using agent hooks (for example, you force commands to
run within a Docker container or a chroot environment), you should also disable
local hooks so that your security measures cannot be evaded with local hooks.
Disabling local hooks also disables plugins from all sources.

You can disable local hooks with the [`no-local-hooks`](/docs/agent/v3/configuration#no-local-hooks)
setting.

If local hooks are disabled and one is in the checkout, the job will fail.

>🚧 Building untrusted commits
>If you build untrusted commits, be careful to contain the build scripts and anything else that may be influenced by the repository contents within chroots, containers, VMs, etc as is appropriate for your needs.

## Strict checks using a `pre-bootstrap` hook

You can use a [`pre-bootstrap` hook](hooks#agent-lifecycle-hooks) to add strict
checks for which repositories, commands, and plugins are allowed to run on your
agent. The `pre-bootstrap` hook is executed before any source code is checked
out, and before any commands are executed.

For example, the following `pre-bootstrap` hook allows only a single file from a
single repository to be executed by the agent:

```bash
#!/bin/bash
set -euo pipefail

for line in "$(grep "^BUILDKITE_REPO=" "${BUILDKITE_ENV_FILE}")"
do
  repo="$(echo "${line}" | cut -d= -f2 | sed -e 's/^"//' -e 's/"$//')"
  if [ "${repo}" != "git@server:repo.git" ]
  then
    echo "Repository not allowed: ${repo}"
    exit 1
  fi
done

for line in "$(grep "^BUILDKITE_COMMAND=" "${BUILDKITE_ENV_FILE}")"
do
  command="$(echo "${line}" | cut -d= -f2 | sed -e 's/^"//' -e 's/"$//')"
  if [ "${command}" != "some-script.sh" ]
  then
    echo "Command not allowed: ${command}"
    exit 1
  fi
done
```

## Signing pipelines

If using plugins is crucial to your workflow and you would still like to preserve strong security guarantees, take a look at the [buildkite-signed-pipeline](https://github.com/seek-oss/buildkite-signed-pipeline) tool. This tool allows uploaded steps to be signed with a secret shared by all agents, so that plugins can run without concerns of tampering by third parties.  

## Allowing a list of plugins

Defining an [environment hook](hooks#job-lifecycle-hooks) in the
[agent `hooks-path`](hooks#hook-locations-agent-hooks), you can create a
list of plugins that an agent is allowed to run by inspecting the
`BUILDKITE_PLUGINS` [environment variable](https://buildkite.com/docs/pipelines/environment-variables).
For an example of this, see the [buildkite/buildkite-allowed-plugins-hook-example](https://github.com/buildkite/buildkite-allowed-plugins-hook-example)
repository on GitHub.

## Customizing the bootstrap

The Buildkite Agent comes with a default bootstrap handler, but can be
[configured](/docs/agent/v3/configuration#bootstrap-script) to run your own
instead. Providing your own bootstrap provides the highest level of security and
control of your agent. You can use it to customize your agent, sanitize command
output, and implement your own security logic.

The Buildkite Agent is separated into a daemon executable and a bootstrap
executable. The daemon is responsible for communicating with the Buildkite API
and executing the bootstrap for each assigned job. The bootstrap is responsible
for checking out source code, calling hooks, running commands, and uploading
build artifacts. The bootstrap is passed environment variables by the daemon
process, and has its output streams and exit status captured.

For example, the following custom bootstrap will print out the environment
variables passed by the main agent process, print a hello world, and exit with a
failure status:

```bash
#!/bin/bash
set -euo pipefail

env

echo "Hello world"

exit 1
```

## Forcing clean checkouts

By default Buildkite will reuse (after cleaning) a previous checkout. This may not be safe if building commits from untrusted sources (for example, 3rd party pull requests). To force a clean checkout every time, set `BUILDKITE_CLEAN_CHECKOUT=true` in the environment.

## Running the Agent behind a proxy

To run the agent behind a proxy, you'll need to export the following proxy environment variables for your process manager:

* `http_proxy`
* `https_proxy`

Both of these variables should be set to the URL for your proxy server.

For example, if using systemd, create a directory named `/etc/systemd/system/buildkite-agent.service.d` that contains a `proxy.conf` file.

An example systemd `proxy.conf` file:

```
[Service]
# Proxy Env Vars
Environment=http_proxy=http://username:password@proxyserver:8080/
Environment=https_proxy=http://username:password@proxyserver:8080/
```
{: codeblock-file="proxy.conf"}

After creating this file, systemd will require a reload and the `buildkite-agent` service will require a restart.

## Restricting access by the Buildkite agent controller

To safeguard your organization's infrastructure in case of Buildkite infrastructure being compromised, you want to restrict what the agent can and cannot do. All of the information above about securing the Buildkite agent applies, specifically:

* Disallow execution of arbitrary commands by [customizing the list of allowed plugins](#allowing-a-list-of-plugins) or [disabling plugins](#disabling-plugins) entirely
* [Disable command-eval](#disabling-command-eval)
* [Disable local hooks](#disabling-local-hooks)
* [Enable strict validation](#strict-checks-using-a-pre-bootstrap-hook)

As a result, your Buildkite agent will refuse to run anything that's not a single argumentless invocation of a script that exists locally (after the `git clone` step of the setup) unless it's explicitly allowed by you.
Since the [agent](https://github.com/buildkite/agent) is open-source, if necessary you can verify that assertion to whatever degree of certainty is required.
