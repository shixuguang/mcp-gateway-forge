### deploy mcp-context-forge to byoa

```
  cpd-cli manage create-gitapp-application --cpd_instance_ns=zen \
    --app_name=mcp-context-forge  \
    --app_port=4444 \
    --app_port_tls=true \
    --repo_url=https://github.com/IBM/mcp-context-forge.git \
    --repo_branch=main  \
    --dockerfile=Containerfile  \
    --app_envs_json=/tmp/work/app-envs-json.json \
    --pvc_info={"size":"2Gi","mount_path":"/data"}  \
    --app_proxy_config_yaml=/tmp/work/mcp-gateway-forge.conf.yaml \
    --cpu=400m  \
    --memory=200Mi  \
    --cpu_limit=500m  \
    --memory_limit=400Mi
```

##### download proxy-config-yaml file:  
```
curl -ko cpd-cli-workspace/olm-utils-workspace/work/mcp-gateway-forge.conf.yaml https://raw.githubusercontent.com/shixuguang/mcp-gateway-forge/refs/heads/main/mcp-gateway-forge.conf.yaml
```
##### create app-envs-json.json:  
```
$ cat cpd-cli-workspace/olm-utils-workspace/work/app-env-json.json  
[{"name":"HOST","value":"0.0.0.0"},{"name":"JWT_SECRET_KEY","value":"my-test-key"},{"name":"BASIC_AUTH_USER","value":"admin@example.com"},{"name":"BASIC_AUTH_PASSWORD","value":"changeme"},{"name":"AUTH_REQUIRED","value":"true"},{"name":"DATABASE_URL","value":"sqlite:////data/mcp.db"},{"name":"SSL","value":"true"},{"name":"CERT_FILE","value":"/etc/certs/tls.crt"},{"name":"KEY_FILE","value":"/etc/certs/tls.key"},{"name": "MCPGATEWAY_UI_ENABLED","value":"true"},{"name":"MCPGATEWAY_ADMIN_API_ENABLED","value":"true"}]
```
