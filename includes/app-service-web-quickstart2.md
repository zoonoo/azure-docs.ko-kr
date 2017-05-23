[az appservice web create](/cli/azure/appservice/web#create) 명령을 사용하여 Web App을 만듭니다. 다음 명령에서 `<app_name>` 자리 표시자를 고유한 앱 이름으로 대체합니다. `<app_name>`은 웹앱의 기본 DNS 사이트에 사용됩니다. `<app_name>`이 고유하지 않으면 "지정된 이름이 <app_name>인 웹 사이트가 이미 있습니다."라는 오류 메시지가 표시됩니다.

```azurecli
az appservice web create --name <app_name> --resource-group myResourceGroup --plan quickStartPlan
```

Web App을 만든 경우 Azure CLI는 다음 예제와 비슷한 정보를 표시합니다.

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "<app_name>.azurewebsites.net",
    "<app_name>.scm.azurewebsites.net"
  ],
  "gatewaySiteName": null,
  "hostNameSslStates": [
    {
      "hostType": "Standard",
      "name": "<app_name>.azurewebsites.net",
      "sslState": "Disabled",
      "thumbprint": null,
      "toUpdate": null,
      "virtualIp": null
    }
    < JSON data removed for brevity. >
}
```

사이트(`http://<app_name>.azurewebsites.net`)로 이동하여 새로 만든 웹앱을 봅니다.

![app-service-web-service-created](../articles/app-service-web/media/app-service-web-get-started-nodejs-poc/app-service-web-service-created.png)


## <a name="configure-local-git-deployment"></a>로컬 Git 배포 구성

App Service는 FTP, 로컬 Git, GitHub, Visual Studio Team Services, Bitbucket 등의 웹앱에 콘텐츠를 배포하는 여러 방법을 지원합니다. 

이 빠른 시작을 위해 로컬 Git을 사용하여 배포합니다. 즉, 로컬 리포지토리에서 Azure의 리포지토리로 푸시하기 위해 Git 명령을 사용하여 배포합니다. 

[az appservice web source-control config-local-git](/cli/azure/appservice/web/source-control#config-local-git) 명령을 사용하여 웹앱에 대한 로컬 Git 액세스 권한을 구성합니다.

```azurecli
az appservice web source-control config-local-git --name <app_name> --resource-group myResourceGroup --query url --output tsv
```

출력 형식은 다음과 같습니다.

```
https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git
```

표시된 URI를 저장합니다. 다음 단계에서 사용합니다. `<username>`은 이전 단계에서 만든 [배포 사용자](#configure-a-deployment-user)입니다.

## <a name="push-to-azure-from-git"></a>git에서 Azure에 푸시

로컬 Git 리포지토리에 Azure 원격을 추가합니다.

```bash
git remote add azure <URI from previous step>
```

Azure 원격에 푸시하여 앱을 배포합니다. 앞서 배포 사용자를 만들 때 만든 암호를 묻는 메시지가 나타납니다. Azure Portal에 로그인할 때 사용하는 암호가 아닌, [배포 사용자 구성](#configure-a-deployment-user)에서 만든 암호를 입력해야 합니다.

```azurecli
git push azure master
```

이전 명령은 다음 예와 유사한 정보를 출력합니다.

```bash
Counting objects: 13, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (11/11), done.
Writing objects: 100% (13/13), 2.07 KiB | 0 bytes/s, done.
Total 13 (delta 2), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'cc39b1e4cb'.
remote: Generating deployment script.
remote: Generating deployment script for Web Site
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling Basic Web Site deployment.
remote: KuduSync.NET from: 'D:\home\site\repository' to: 'D:\home\site\wwwroot'
remote: Deleting file: 'hostingstart.html'
remote: Copying file: '.gitignore'
remote: Copying file: 'LICENSE'
remote: Copying file: 'README.md'
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
To https://<app_name>.scm.azurewebsites.net/<app_name>.git
 * [new branch]      master -> master
```

### <a name="browse-to-the-app"></a>앱으로 이동


배포된 앱으로 이동합니다.

```
http://<app_name>.azurewebsites.net
```

