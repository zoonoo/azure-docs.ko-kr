[az webapp deployment source config-local-git](/cli/azure/webapp/deployment/source#config-local-git) 명령을 사용하여 웹앱에 로컬 Git 배포를 구성합니다.

App Service는 FTP, 로컬 Git, GitHub, Visual Studio Team Services, Bitbucket 등의 웹앱에 콘텐츠를 배포하는 여러 방법을 지원합니다. 이 빠른 시작을 위해 로컬 Git을 사용하여 배포합니다. 즉, 로컬 리포지토리에서 Azure의 리포지토리로 푸시하기 위해 Git 명령을 사용하여 배포합니다. 

다음 명령에서 *\<app_name>*을 웹앱의 이름으로 바꿉니다.

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group myResourceGroup --query url --output tsv
```

출력 형식은 다음과 같습니다.

```bash
https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git
```

`<username>`은 이전 단계에서 만든 [배포 사용자](#configure-a-deployment-user)입니다.

표시된 URI를 저장합니다. 다음 단계에서 사용합니다. 
