## <a name="create-a-resource-group"></a>리소스 그룹 만들기

 [Azure 리소스 그룹](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups)은 웹앱 및 데이터베이스 등의 리소스를 관리하는 논리적 컨테이너입니다. 리소스 그룹에서 리소스를 배포, 업데이트 및 삭제합니다.

[az group create](/cli/azure/group#create) 명령을 사용하여 리소스 그룹을 만듭니다.

```azurecli
az group create --name myResourceGroup --location westeurope
```

사용 가능한 위치를 보려면 `az appservice list-locations` 명령을 사용합니다. 일반적으로 사용자와 가까운 지역에서 리소스를 만듭니다.

## <a name="create-an-azure-app-service-plan"></a>Azure App Service 계획 만들기

[az appservice plan create](/cli/azure/appservice/plan#create) 명령을 사용하여 "체험" [App Service 계획](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)을 만듭니다.

[!INCLUDE [app-service-plan](app-service-plan.md)]

다음 명령에서는 **체험** 가격 책정 계층을 사용하여 `quickStartPlan`이라는 App Service 계획을 만듭니다.

```azurecli
az appservice plan create --name quickStartPlan --resource-group myResourceGroup --sku FREE
```

App Service 계획을 만든 경우 Azure CLI는 다음 예제와 비슷한 정보를 표시합니다.

```json
{ 
  "adminSiteName": null,
  "appServicePlanName": "quickStartPlan",
  "geoRegion": "West Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/quickStartPlan",
  "kind": "app",
  "location": "West Europe",
  "maximumNumberOfWorkers": 1,
  "name": "quickStartPlan",
  < JSON data removed for brevity. >
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
} 
```

## <a name="create-a-web-app"></a>웹앱 만들기