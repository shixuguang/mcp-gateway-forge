## deploy mcp-context-forge to byoa

#### deploy mcp-context-forge with sql lite database:  

- download proxy-config-yaml file:  
  ```
  curl -ko cpd-cli-workspace/olm-utils-workspace/work/mcp-gateway-forge.conf.yaml https://raw.githubusercontent.com/shixuguang/mcp-gateway-forge/refs/heads/main/mcp-gateway-forge.conf.yaml
  ```

- create app-envs-json-sqlite.json:  

  ```
  $ cat cpd-cli-workspace/olm-utils-workspace/work/app-env-json.json
    {"HOST":"0.0.0.0","JWT_SECRET_KEY":"my-test-key","BASIC_AUTH_USER":"admin@example.com","BASIC_AUTH_PASSWORD":"changeme","AUTH_REQUIRED":"true","DATABASE_URL":"sqlite:////data/mcp.db","SSL":"true","CERT_FILE":"/etc/certs/tls.crt","KEY_FILE":"/etc/certs/tls.key","MCPGATEWAY_UI_ENABLED":"true","MCPGATEWAY_ADMIN_API_ENABLED":"true"}
  ```

- run cpd-cli create-git-application:  
  ```
  cpd-cli manage create-git-application --cpd_instance_ns=zen \
    --app_name=mcp-context-forge-sqlite  \
    --app_port=4444 \
    --app_port_tls=true \
    --repo_url=https://github.com/IBM/mcp-context-forge.git \
    --repo_branch=main  \
    --dockerfile=Containerfile  \
    --app_envs_json=/tmp/work/app-envs-json-sqlite.json \
    --pvc_info={"size":"2Gi","mount_path":"/data"}  \
    --app_proxy_config_yaml=/tmp/work/mcp-gateway-forge.conf.yaml \
    --cpu=400m  \
    --memory=200Mi  \
    --cpu_limit=500m  \
    --memory_limit=400Mi
  ```

#### deploy mcp-context-forge with postgres database:  

  ##### deploy postgresql  

  - create postgres app template parameters json file:
    ```
    $ cat cpd-cli-workspace/olm-utils-workspace/work/app-parameters-json.json  
      {"DATABASE_SERVICE_NAME":"postgresql-mcp-context-forge","POSTGRESQL_USER":"postgres","POSTGRESQL_PASSWORD":"secret","POSTGRESQL_DATABASE":"mcp"}
    ```

  - run following cpd-cli command:  
    ```
    ./cpd-cli manage create-template-application --cpd_instance_ns=zen  \
        --app_name=postgresql-mcp-context-forge  \
        --app_template=https://raw.githubusercontent.com/openshift/origin/master/examples/db-templates/postgresql-persistent-template.json  \
        --app_template_parameters_json=/tmp/work/app-parameters-json.json \
        --cpu=400m  \
        --memory=200Mi  \
        --cpu_limit=500m  \
        --memory_limit=400Mi
    ```
  ##### deploy mcp-context-forge

  - create app-envs-json.json:  
    ```
    $ cat cpd-cli-workspace/olm-utils-workspace/work/app-envs-json-postgresql.json  

    [{"name":"HOST","value":"0.0.0.0"},{"name":"JWT_SECRET_KEY","value":"my-test-key"},{"name":"BASIC_AUTH_USER","value":"admin@example.com"},{"name":"BASIC_AUTH_PASSWORD","value":"changeme"},{"name":"AUTH_REQUIRED","value":"true"},{"name":"DATABASE_URL","value":"postgresql://postgres:secret@postgresql-mcp-context-forge:5432/mcp"},{"name":"SSL","value":"true"},{"name":"CERT_FILE","value":"/etc/certs/tls.crt"},{"name":"KEY_FILE","value":"/etc/certs/tls.key"},{"name": "MCPGATEWAY_UI_ENABLED","value":"true"},{"name":"MCPGATEWAY_ADMIN_API_ENABLED","value":"true"}]
    ```
  - run cpd-cli create-git-application command:  
    ```
    cpd-cli manage create-git-application --cpd_instance_ns=zen \
      --app_name=mcp-context-forge-postgresql  \
      --app_port=4444 \
      --app_port_tls=true \
      --repo_url=https://github.com/IBM/mcp-context-forge.git \
      --repo_branch=main  \
      --dockerfile=Containerfile  \
      --app_envs_json=/tmp/work/app-envs-json-postgresql.json \
      --app_proxy_config_yaml=/tmp/work/mcp-gateway-forge.conf.yaml \
      --cpu=400m  \
      --memory=200Mi  \
      --cpu_limit=500m  \
      --memory_limit=400Mi
    ```

