The sample project comes from https://angular.io/tutorial/first-app/first-app-lesson-01

# Option 1, Using customized builder without code change
* Create customized builder and remove 
  * Create builder file, such as test.json
    ``` json
    {
        "stack": {
            "id": "io.buildpacks.stacks.bionic",
            "version": "base"
        },
        "buildpackGroups": [
            {
                "name": "mix",
                "buildpacks": [
                    {
                        "id": "tanzu-buildpacks/web-servers"
                    }
                ]
            }
        ]
    }
    ```
  * Create builder using Azure CLI
    ```azurecli
    az spring build-service builder create -s {service-name} -g {resource-group} -n {builder-name} --builder-file test.json
    ```
* Public endpoint assigned is required because path of Angular js libraries is leading with slash. If you hope test endpoint works, remove slash from `<base href="/">` in `src\index.html`.

## Deployment script
```shell
az spring app deploy --subscription 0753feba-86f1-4242-aff1-27938fb04531 -s {service-name} -g {resource-group} -n {app-name} --source-path . --build-env BP_NODE_RUN_SCRIPTS=build BP_WEB_SERVER=nginx BP_WEB_SERVER_ROOT=dist BP_WEB_SERVER_ENABLE_PUSH_STATE=true NPM_CONFIG_LEGACY_PEER_DEPS=true --builder {builder-name}
```

* `NPM_CONFIG_LEGACY_PEER_DEPS=true`, bypass peerDependency auto-installation because conflicts between NPM v7 and Angular. 

# Option 2, Using default builder of ASA, but with code change
## Notes
* Change `"zone.js": "~0.13.0"` in package.json because of the upstream dependency conflict
* Change npm start script in `package.json` and append parameters `ng serve --host 0.0.0.0 --port 8080 --disable-host-check`[^1].
  - `host`, 
  - `port`, enterprise plan needs 8080 port rather than default 4200 port.
  - `disable-host-check`, development-oriented application server only binds to localhost by default. It means traffic directed to machine is allowed to pass through. The tunnel cannot work. It tells the server to allow connections from outside localhost. 
* Public endpoint assigned is required because path of Angular js libraries is leading with slash. If you hope test endpoint works, remove slash from `<base href="/">` in `src\index.html`.

## Deployment script
```shell
az spring app deploy -s {service-name} -g {resource-group} -n {app-name} --source-path . --build-env BP_NODE_RUN_SCRIPTS=build BP_WEB_SERVER=nginx BP_WEB_SERVER_ROOT=dist BP_WEB_SERVER_ENABLE_PUSH_STATE=true NODE_ENV=development
```

* `NODE_ENV=development`, enable `ng` cli and create service with express server[^1].

[^1]: [Node.js Sample app using Npm and a Angular framework](https://github.com/paketo-buildpacks/samples/tree/main/nodejs/angular-npm#note)
