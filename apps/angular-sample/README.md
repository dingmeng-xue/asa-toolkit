https://angular.io/tutorial/first-app/first-app-lesson-01


# Deploy script
```shell
az spring app deploy -s {service-name} -g {resouce-group} -n {app-name} --source-path ./src --build-env BP_NODE_RUN_SCRIPTS=build BP_WEB_SERVER=nginx BP_WEB_SERVER_ROOT=dist BP_WEB_SERVER_ENABLE_PUSH_STATE=true
```