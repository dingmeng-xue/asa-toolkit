The sample project comes from https://angular.io/tutorial/first-app/first-app-lesson-01

# Notes
* Change `"zone.js": "~0.13.0"` in package.json because of the upstream dependency conflict
* Change npm start script in `package.json` and append parameters `ng serve --host 0.0.0.0 --port 8080 --disable-host-check`[^1].
  - `host`, 
  - `port`, enterprise plan needs 8080 port rather than default 4200 port.
  - `disable-host-check`, development-oriented application server only binds to localhost by default. It means traffic directed to machine is allowed to pass through. The tunnel cannot work. It tells the server to allow connections from outside localhost. 
* Public endpoint assigned is required because path of Angular js libraries is leading with slash. If you hope test endpoint works, remove slash from `<base href="/">` in `src\index.html`.
* Add environment variable `NODE_ENV=development` to Azure CLI command to enable `ng` cli[^1].

[^1]: [Node.js Sample app using Npm and a Angular framework](https://github.com/paketo-buildpacks/samples/tree/main/nodejs/angular-npm#note)


# Deploy script
```shell
az spring app deploy -s {service-name} -g {resouce-group} -n {app-name} --source-path . --build-env BP_NODE_RUN_SCRIPTS=build BP_WEB_SERVER=nginx BP_WEB_SERVER_ROOT=dist BP_WEB_SERVER_ENABLE_PUSH_STATE=true NODE_ENV=development
```