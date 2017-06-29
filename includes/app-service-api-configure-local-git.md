[az webapp source-control config-local-git](/cli/azure/appservice/web/source-control#config-local-git) 명령을 사용하여 API 앱에 로컬 Git 배포를 구성합니다.   

App Service는 FTP, 로컬 Git, GitHub, Visual Studio Team Services, Bitbucket 등의 웹앱에 콘텐츠를 배포하는 여러 방법을 지원합니다. 이 빠른 시작을 위해 로컬 Git을 사용하여 배포합니다. 즉, 로컬 리포지토리에서 Azure의 리포지토리로 푸시하기 위해 Git 명령을 사용하여 배포합니다.  

아래 스크립트를 사용하여 코드를 푸시할 때 사용할 계정 수준 배포 자격 증명을 설정합니다. 사용자 이름 및 암호에 대한 고유한 값을 포함해야 합니다.   

```azurecli-interactive
az webapp deployment user set --user-name <desired user name> --password <desired password>
```
