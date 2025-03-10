# Build webhook events


## Events

<table>
<tbody>
  <tr><th><code>build.scheduled</code></th><td>A build has been scheduled</td></tr>
  <tr><th><code>build.running</code></th><td>A build has started running</td></tr>
  <tr><th><code>build.finished</code></th><td>A build has finished</td></tr>
</tbody>
</table>

## Request body data

<table>
<tbody>
  <tr><th><code>build</code></th><td>The <a href="/docs/api/builds">Build</a> this notification relates to</td></tr>
  <tr><th><code>pipeline</code></th><td>The <a href="/docs/api/pipelines">Pipeline</a> this notification relates to</td></tr>
  <tr><th><code>sender</code></th><td>The user who created the webhook</td></tr>
</tbody>
</table>

Example request body:

```json
{
  "event": "build.scheduled",
  "build": {
    "...": "..."
  },
  "pipeline": {
    "...": "..."
  },
  "sender": {
    "id": "8a7693f8-dbae-4783-9137-84090fce9045",
    "name": "Some Person"
  }
}
```
## Finding out if a build is blocked

To if a build is blocked, look for `blocked: true` in the `build.finished` event

Example request body for blocked build:

```json
{
  "event": "build.finished",
  "build": {
    "...": "...",
    "blocked": true,
    "...": "..."
  },
  "pipeline": {
    "...": "..."
  },
  "sender": {
    "id": "0adfbc27-5f72-4a91-bf61-5693da0dd9c5",
    "name": "Some person"
  }
}
```

>📘 To determine if an Eventbridge notification is blocked
> However, to determine if an Eventbridge notification is blocked, look for <code>"state": "blocked". </code>, like in this <a href="/docs/integrations/amazon-eventbridge#events-build-blocked">sample Eventbridge request</a>.
