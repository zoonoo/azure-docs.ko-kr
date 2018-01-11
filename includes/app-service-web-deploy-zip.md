## <a name="deploy-uploaded-zip-file"></a>업로드된 ZIP 파일 배포

Cloud Shell에서 [az webapp deployment source config-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az_webapp_deployment_source_config_zip) 명령을 사용하여 업로드된 ZIP 파일을 웹앱에 배포합니다. *\<app_name>*을 웹앱의 이름으로 바꿉니다.

```azurecli-interactive
az webapp deployment source config-zip --resource-group myResouceGroup --name <app_name> --src clouddrive/myAppFiles.zip
```

이 명령은 ZIP 파일의 파일과 디렉터리를 기본 App Service 응용 프로그램 폴더(`\home\site\wwwroot`)에 배포하고 앱을 다시 시작합니다. 추가 사용자 정의 빌드 프로세스가 구성된 경우 이 프로세스도 실행됩니다.
