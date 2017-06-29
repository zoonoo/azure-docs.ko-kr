[az group create](/cli/azure/group#create) 명령을 사용하여 리소스 그룹을 만듭니다.

[!INCLUDE [resource group intro text](resource-group.md)]

다음 예제에서는 *westeurope* 위치에 *myResourceGroup*이라는 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```

일반적으로 사용자와 가까운 지역에서 리소스 그룹 및 리소스를 만듭니다. Azure Web Apps에 대한 지원되는 모든 위치를 확인하려면 `az appservice list-locations` 명령을 실행합니다. 