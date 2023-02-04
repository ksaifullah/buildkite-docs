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
  AnnotationStyle
  <span class="pill pill--enum pill--normal-case pill--large"><code>ENUM</code></span>
</h1>
<!-- vale on -->


<p>The visual style of the annotation</p>


{:notoc}









<table class="responsive-table responsive-table--single-column-rows">
  <thead>
    <th>
      <h2 data-algolia-exclude>ENUM Values</h2>
    </th>
  </thead>
  <tbody>
    <tr><td><p><strong><code>DEFAULT</code></strong></p><p>The default styling of an annotation</p></td></tr><tr><td><p><strong><code>SUCCESS</code></strong></p><p>The annotation has a green border with a tick next to it</p></td></tr><tr><td><p><strong><code>INFO</code></strong></p><p>The annotation has a blue border with an information icon next to it</p></td></tr><tr><td><p><strong><code>WARNING</code></strong></p><p>The annotation has an orange border with a warning icon next to it</p></td></tr><tr><td><p><strong><code>ERROR</code></strong></p><p>The annotation has a red border with a cross next to it</p></td></tr>
  </tbody>
</table>