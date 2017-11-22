Cloud Shell에서 [az group create](/cli/azure/group#create) 명령을 사용하여 리소스 그룹을 만듭니다.

[!INCLUDE [resource group intro text](resource-group.md)]

다음 예제에서는 *유럽 서부* 위치에 *myResourceGroup*이라는 리소스 그룹을 만듭니다. App Service에 지원되는 모든 위치를 확인하려면 `az appservice list-locations` 명령을 실행합니다.

```azurecli-interactive
az group create --name myResourceGroup --location "West Europe"
```

일반적으로 사용자와 가까운 지역에서 리소스 그룹 및 리소스를 만듭니다. 