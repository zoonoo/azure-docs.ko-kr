Cloud Shell에서 [az webapp create](/cli/azure/webapp#create) 명령을 사용하여 `myAppServicePlan` App Service 계획에 [웹앱](../articles/app-service/containers/app-service-linux-intro.md)을 만듭니다. `<app_name>`을 고유한 앱 이름으로 대체해야 합니다.

다음 명령에서 런타임은 `dotnetcore|1.1`로 설정됩니다. 지원되는 모든 런타임을 보려면 [az webapp list-runtimes](/cli/azure/webapp#list-runtimes)를 실행합니다. 

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --runtime "dotnetcore|1.1" --deployment-local-git
```

웹앱이 만들어지면 Azure CLI에서 다음 예제와 비슷한 출력을 표시합니다.

```json
Local git is configured with url of 'https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

git 배포를 활성화하여 Linux 컨테이너에 새 빈 웹앱을 만들었습니다.

> [!NOTE]
> Git 원격의 URL은 `https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git` 형식으로 `deploymentLocalGitUrl` 속성에 표시됩니다. 나중에 필요하므로 이 URL을 저장합니다.
>
