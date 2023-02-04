<!--
  _____   ____    _   _  ____ _______   ______ _____ _____ _______ 
  |  __  / __   |  | |/ __ __   __| |  ____|  __ _   _|__   __|
  | |  | | |  | | |  | | |  | | | |    | |__  | |  | || |    | |   
  | |  | | |  | | | . ` | |  | | | |    |  __| | |  | || |    | |   
  | |__| | |__| | | |  | |__| | | |    | |____| |__| || |_   | |   
  |_____/ ____/  |_| _|____/  |_|    |______|_____/_____|  |_|   
  This file is auto-generated by script/generate_graphql_api_content.sh,
  please build the schema.json by running `rails api:graph:export`
  with https://github.com/buildkite/buildkite/,
  replace the content in data/graphql_data_schema.json
  and run the generation script `./scripts/generate-graphql-api-content.sh`.
-->
<!-- vale off -->
<h1 class="has-pills" data-algolia-exclude>
  TeamMemberRole
  <span class="pill pill--enum pill--normal-case pill--large"><code>ENUM</code></span>
</h1>
<!-- vale on -->


<p>The roles a user can be within a team</p>


{:notoc}









<table class="responsive-table responsive-table--single-column-rows">
  <thead>
    <th>
      <h2 data-algolia-exclude>ENUM Values</h2>
    </th>
  </thead>
  <tbody>
    <tr><td><p><strong><code>MEMBER</code></strong></p><p>The user is a regular member of the team</p></td></tr><tr><td><p><strong><code>MAINTAINER</code></strong></p><p>The user can manage pipelines and users within the team</p></td></tr>
  </tbody>
</table>