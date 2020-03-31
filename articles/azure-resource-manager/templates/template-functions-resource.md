---
title: 템플릿 기능 - 리소스
description: Azure Resource Manager 템플릿에서 리소스에 대한 값을 검색하는 데 사용할 수 있는 함수에 대해 설명합니다.
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: e9e1d700282652304f0bede5e697ba8625f5a5d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80156296"
---
# <a name="resource-functions-for-arm-templates"></a>ARM 템플릿에 대한 리소스 기능

리소스 관리자는 ARM(Azure 리소스 관리자) 템플릿에서 리소스 값을 가져오는 데 대한 다음 함수를 제공합니다.

* [확장리소스 ID](#extensionresourceid)
* [list*](#list)
* [providers](#providers)
* [참조](#reference)
* [리소스 그룹](#resourcegroup)
* [Resourceid](#resourceid)
* [구독](#subscription)
* [구독리소스 ID](#subscriptionresourceid)
* [테넌트리소스ID](#tenantresourceid)

매개 변수, 변수 또는 현재 배포에서 값을 가져오려면 [배포 값 함수](template-functions-deployment.md)를 참조하세요.

## <a name="extensionresourceid"></a>확장리소스 ID

```json
extensionResourceId(resourceId, resourceType, resourceName1, [resourceName2], ...)
```

[확장 리소스에](../management/extension-resource-types.md)대 한 리소스 ID를 반환 합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | Description |
|:--- |:--- |:--- |:--- |
| resourceId |yes |문자열 |확장 리소스가 적용되는 리소스의 리소스 ID입니다. |
| resourceType |yes |문자열 |리소스 공급자 네임스페이스를 포함하는 리소스 유형입니다. |
| resourceName1 |yes |문자열 |리소스의 이름입니다. |
| resourceName2 |예 |문자열 |필요한 경우 다음 리소스 이름 세그먼트입니다. |

리소스 유형에 더 많은 세그먼트가 포함된 경우 리소스 이름을 매개 변수로 계속 추가합니다.

### <a name="return-value"></a>반환 값

이 함수에서 반환되는 리소스 ID의 기본 형식은 다음과 입니다.

```json
{scope}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

범위 세그먼트는 확장되는 리소스에 따라 다릅니다.

확장 리소스가 **리소스에**적용되면 리소스 ID가 다음 형식으로 반환됩니다.

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{baseResourceProviderNamespace}/{baseResourceType}/{baseResourceName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

확장 리소스가 **리소스 그룹에**적용되면 형식은 다음과 입니다.

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

**멤버쉽에**확장 리소스가 적용되면 형식은 다음과 같은 것입니다.

```json
/subscriptions/{subscriptionId}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

확장 리소스가 **관리 그룹에**적용되면 형식은 다음과 입니다.

```json
/providers/Microsoft.Management/managementGroups/{managementGroupName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

### <a name="extensionresourceid-example"></a>확장리소스ID 예제

다음 예제는 리소스 그룹 잠금에 대 한 리소스 ID를 반환 합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "lockName":{
            "type": "string"
        }
    },
    "variables": {},
    "resources": [],
    "outputs": {
        "lockResourceId": {
            "type": "string",
            "value": "[extensionResourceId(resourceGroup().Id , 'Microsoft.Authorization/locks', parameters('lockName'))]"
        }
    }
}
```

<a id="listkeys" />
<a id="list" />

## <a name="list"></a>list*

```json
list{Value}(resourceName or resourceIdentifier, apiVersion, functionValues)
```

이 함수의 구문은 목록 작업의 이름에 따라 달라집니다. 각 구현은 목록 작업을 지원하는 리소스 종류의 값을 반환합니다. 작업 이름은 `list`로 시작해야 합니다. 일부 일반적으로 사용되는 함수는 `listKeys` 및 `listSecrets`입니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | Description |
|:--- |:--- |:--- |:--- |
| resourceName 또는 resourceIdentifier |yes |문자열 |리소스에 대한 고유 식별자. |
| apiVersion |yes |문자열 |리소스 런타임 상태의 API 버전입니다. 일반적으로 **yyyy-mm-dd** 형식입니다. |
| functionValues |예 |object | 함수에 대한 값이 있는 개체입니다. 스토리지 계정의 **listAccountSas** 같은 매개 변수 값을 가진 개체를 받는 것을 지원하는 함수에 대해 이 개체를 제공합니다. 함수 값을 전달하는 예제가 이 문서에 나와 있습니다. |

### <a name="valid-uses"></a>유효한 사용

목록 함수는 리소스 정의의 속성과 템플릿 또는 배포의 출력 섹션에서만 사용할 수 있습니다. [속성 반복과](copy-properties.md)함께 사용할 경우 식이 리소스 `input` 속성에 할당되어 있으므로 목록 함수를 사용할 수 있습니다. 목록 함수가 확인되기 `count` 전에 개수를 결정해야 하므로 사용할 수 없습니다.

### <a name="implementations"></a>구현

다음 표에 list*의 가능한 용도가 나와 있습니다.

| 리소스 유형 | 함수 이름 |
| ------------- | ------------- |
| Microsoft.AnalysisServices/servers | [listGatewayStatus](/rest/api/analysisservices/servers/listgatewaystatus) |
| 마이크로소프트.앱 구성/구성스토어 | 목록 키 |
| Microsoft.Automation/automationAccounts | [목록 키](/rest/api/automation/keys/listbyautomationaccount) |
| Microsoft.Batch/batchAccounts | [listkeys](/rest/api/batchmanagement/batchaccount/getkeys) |
| Microsoft.BatchAI/workspaces/experiments/jobs | [listoutputfiles](/rest/api/batchai/jobs/listoutputfiles) |
| 마이크로소프트.블록체인/블록체인 회원 | [목록Api키](/rest/api/blockchain/2019-06-01-preview/blockchainmembers/listapikeys) |
| 마이크로소프트.블록체인/블록체인 회원/트랜잭션노드 | [목록Api키](/rest/api/blockchain/2019-06-01-preview/transactionnodes/listapikeys) |
| Microsoft.Cache/redis | [목록 키](/rest/api/redis/redis/listkeys) |
| Microsoft.CognitiveServices/accounts | [목록 키](/rest/api/cognitiveservices/accountmanagement/accounts/listkeys) |
| Microsoft.ContainerRegistry/registries | [listBuildSourceUploadUrl](/rest/api/containerregistry/registries%20(tasks)/getbuildsourceuploadurl) |
| Microsoft.ContainerRegistry/registries | [listCredentials](/rest/api/containerregistry/registries/listcredentials) |
| Microsoft.ContainerRegistry/registries | [listUsages](/rest/api/containerregistry/registries/listusages) |
| Microsoft.ContainerRegistry/registries/webhooks | [listEvents](/rest/api/containerregistry/webhooks/listevents) |
| 마이크로소프트.컨테이너레지스트리/레지스트리/실행 | [목록로그사스Url](/rest/api/containerregistry/runs/getlogsasurl) |
| 마이크로소프트.컨테이너레지스트리/레지스트리/작업 | [목록 세부 정보](/rest/api/containerregistry/tasks/getdetails) |
| Microsoft.ContainerService/managedClusters | [listClusterAdminCredential](/rest/api/aks/managedclusters/listclusteradmincredentials) |
| Microsoft.ContainerService/managedClusters | [listClusterUserCredential](/rest/api/aks/managedclusters/listclusterusercredentials) |
| 마이크로소프트.컨테이너 서비스/관리 클러스터/액세스 프로필 | [목록 자격 증명](/rest/api/aks/managedclusters/getaccessprofile) |
| 마이크로소프트.데이터 박스/작업 | listCredentials |
| Microsoft.DataFactory/datafactories/gateways | listauthkeys |
| Microsoft.DataFactory/factories/integrationruntimes | [listauthkeys](/rest/api/datafactory/integrationruntimes/listauthkeys) |
| Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers | [listSasTokens](/rest/api/datalakeanalytics/storageaccounts/listsastokens) |
| 마이크로소프트.데이터 쉐어/계정/공유 | [목록 동기화](/rest/api/datashare/shares/listsynchronizations) |
| 마이크로소프트.데이터 쉐어/계정/공유 구독 | [목록소스공유동기화설정](/rest/api/datashare/sharesubscriptions/listsourcesharesynchronizationsettings) |
| 마이크로소프트.데이터 쉐어/계정/공유 구독 | [목록 동기화 세부 정보](/rest/api/datashare/sharesubscriptions/listsynchronizationdetails) |
| 마이크로소프트.데이터 쉐어/계정/공유 구독 | [목록 동기화](/rest/api/datashare/sharesubscriptions/listsynchronizations) |
| Microsoft.Devices/iotHubs | [listkeys](/rest/api/iothub/iothubresource/listkeys) |
| 마이크로 소프트.장치 / iotHubs / iotHubKeys | [listkeys](/rest/api/iothub/iothubresource/getkeysforkeyname) |
| Microsoft.Devices/provisioningServices/keys | [listkeys](/rest/api/iot-dps/iotdpsresource/listkeysforkeyname) |
| Microsoft.Devices/provisioningServices | [listkeys](/rest/api/iot-dps/iotdpsresource/listkeys) |
| Microsoft.DevTestLab/labs | [ListVhds](/rest/api/dtl/labs/listvhds) |
| Microsoft.DevTestLab/labs/schedules | [ListApplicable](/rest/api/dtl/schedules/listapplicable) |
| Microsoft.DevTestLab/labs/users/serviceFabrics | [ListApplicableSchedules](/rest/api/dtl/servicefabrics/listapplicableschedules) |
| Microsoft.DevTestLab/labs/virtualMachines | [ListApplicableSchedules](/rest/api/dtl/virtualmachines/listapplicableschedules) |
| Microsoft.DocumentDB/databaseAccounts | [listConnectionStrings](/rest/api/cosmos-db-resource-provider/databaseaccounts/listconnectionstrings) |
| Microsoft.DocumentDB/databaseAccounts | [목록 키](/rest/api/cosmos-db-resource-provider/databaseaccounts/listkeys) |
| Microsoft.DomainRegistration | [listDomainRecommendations](/rest/api/appservice/domains/listrecommendations) |
| Microsoft.DomainRegistration/topLevelDomains | [listAgreements](/rest/api/appservice/topleveldomains/listagreements) |
| 마이크로소프트.이벤트 그리드/도메인 | [목록 키](/rest/api/eventgrid/domains/listsharedaccesskeys) |
| Microsoft.EventGrid/topics | [목록 키](/rest/api/eventgrid/topics/listsharedaccesskeys) |
| Microsoft.EventHub/namespaces/authorizationRules | [listkeys](/rest/api/eventhub/namespaces/listkeys) |
| Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules | [listkeys](/rest/api/eventhub/disasterrecoveryconfigs/listkeys) |
| Microsoft.EventHub/namespaces/eventhubs/authorizationRules | [listkeys](/rest/api/eventhub/eventhubs/listkeys) |
| Microsoft.ImportExport/jobs | [listBitLockerKeys](/rest/api/storageimportexport/bitlockerkeys/list) |
| 마이크로소프트.쿠스토/클러스터/데이터베이스 | [목록 교장](/rest/api/azurerekusto/databases/listprincipals) |
| 마이크로소프트.랩서비스/사용자 | [목록환경](/rest/api/labservices/globalusers/listenvironments) |
| 마이크로소프트.랩서비스/사용자 | [리스트랩](/rest/api/labservices/globalusers/listlabs) |
| Microsoft.Logic/integrationAccounts/agreements | [listContentCallbackUrl](/rest/api/logic/agreements/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts/assemblies | [listContentCallbackUrl](/rest/api/logic/integrationaccountassemblies/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts | [listCallbackUrl](/rest/api/logic/integrationaccounts/getcallbackurl) |
| Microsoft.Logic/integrationAccounts | [listKeyVaultKeys](/rest/api/logic/integrationaccounts/listkeyvaultkeys) |
| Microsoft.Logic/integrationAccounts/maps | [listContentCallbackUrl](/rest/api/logic/maps/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts/partners | [listContentCallbackUrl](/rest/api/logic/partners/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts/schemas | [listContentCallbackUrl](/rest/api/logic/schemas/listcontentcallbackurl) |
| Microsoft.Logic/workflows | [listCallbackUrl](/rest/api/logic/workflows/listcallbackurl) |
| Microsoft.Logic/workflows | [listSwagger](/rest/api/logic/workflows/listswagger) |
| 마이크로 소프트.로직 / 워크 플로우 / 실행 / 작업 | [목록표현추적](/rest/api/logic/workflowrunactions/listexpressiontraces) |
| Microsoft.Logic/워크플로/실행/작업/반복 | [목록표현추적](/rest/api/logic/workflowrunactionrepetitions/listexpressiontraces) |
| 마이크로 소프트.로직 / 워크 플로우 / 트리거 | [listCallbackUrl](/rest/api/logic/workflowtriggers/listcallbackurl) |
| 마이크로 소프트.로직 / 워크 플로우 / 버전 / 트리거 | [listCallbackUrl](/rest/api/logic/workflowversions/listcallbackurl) |
| Microsoft.MachineLearning/webServices | [listkeys](/rest/api/machinelearning/webservices/listkeys) |
| Microsoft.MachineLearning/Workspaces | listworkspacekeys |
| Microsoft.MachineLearningServices/workspaces/computes | [목록 키](/rest/api/azureml/workspacesandcomputes/machinelearningcompute/listkeys) |
| Microsoft.MachineLearningServices/workspaces/computes | [목록노드](/rest/api/azureml/workspacesandcomputes/machinelearningcompute/listnodes) |
| Microsoft.MachineLearningServices/workspaces | [목록 키](/rest/api/azureml/workspacesandcomputes/workspaces/listkeys) |
| Microsoft.Maps/accounts | [목록 키](/rest/api/maps-management/accounts/listkeys) |
| Microsoft.Media/mediaservices/assets | [listContainerSas](/rest/api/media/assets/listcontainersas) |
| Microsoft.Media/mediaservices/assets | [listStreamingLocators](/rest/api/media/assets/liststreaminglocators) |
| Microsoft.Media/mediaservices/streamingLocators | [listContentKeys](/rest/api/media/streaminglocators/listcontentkeys) |
| Microsoft.Media/mediaservices/streamingLocators | [listPaths](/rest/api/media/streaminglocators/listpaths) |
| 마이크로소프트.네트워크/응용 프로그램보안그룹 | 목록Ip 구성 |
| Microsoft.NotificationHubs/Namespaces/authorizationRules | [listkeys](/rest/api/notificationhubs/namespaces/listkeys) |
| Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules | [listkeys](/rest/api/notificationhubs/notificationhubs/listkeys) |
| Microsoft.OperationalInsights/workspaces | [목록 키](/rest/api/loganalytics/workspaces%202015-03-20/listkeys) |
| Microsoft.정책 인사이트/업데이트 관리 | [목록 배포](/rest/api/policy-insights/remediations/listdeploymentsatresourcegroup) |
| Microsoft.Relay/namespaces/authorizationRules | [listkeys](/rest/api/relay/namespaces/listkeys) |
| Microsoft.릴레이/네임스페이스/재해 복구구성/권한 부여규칙 | listkeys |
| Microsoft.Relay/namespaces/HybridConnections/authorizationRules | [listkeys](/rest/api/relay/hybridconnections/listkeys) |
| Microsoft.Relay/namespaces/WcfRelays/authorizationRules | [listkeys](/rest/api/relay/wcfrelays/listkeys) |
| Microsoft.Search/searchServices | [listAdminKeys](/rest/api/searchmanagement/adminkeys/get) |
| Microsoft.Search/searchServices | [listQueryKeys](/rest/api/searchmanagement/querykeys/listbysearchservice) |
| Microsoft.ServiceBus/namespaces/authorizationRules | [listkeys](/rest/api/servicebus/namespaces/listkeys) |
| Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules | [listkeys](/rest/api/servicebus/disasterrecoveryconfigs/listkeys) |
| Microsoft.ServiceBus/namespaces/queues/authorizationRules | [listkeys](/rest/api/servicebus/queues/listkeys) |
| Microsoft.ServiceBus/namespaces/topics/authorizationRules | [listkeys](/rest/api/servicebus/topics/listkeys) |
| Microsoft.SignalRService/SignalR | [listkeys](/rest/api/signalr/signalr/listkeys) |
| Microsoft.Storage/storageAccounts | [listAccountSas](/rest/api/storagerp/storageaccounts/listaccountsas) |
| Microsoft.Storage/storageAccounts | [listkeys](/rest/api/storagerp/storageaccounts/listkeys) |
| Microsoft.Storage/storageAccounts | [listServiceSas](/rest/api/storagerp/storageaccounts/listservicesas) |
| Microsoft.StorSimple/managers/devices | [listFailoverSets](/rest/api/storsimple/devices/listfailoversets) |
| Microsoft.StorSimple/managers/devices | [listFailoverTargets](/rest/api/storsimple/devices/listfailovertargets) |
| Microsoft.StorSimple/managers | [listActivationKey](/rest/api/storsimple/managers/getactivationkey) |
| Microsoft.StorSimple/managers | [listPublicEncryptionKey](/rest/api/storsimple/managers/getpublicencryptionkey) |
| Microsoft.Web/connectionGateways | ListStatus |
| microsoft.web/connections | listconsentlinks |
| Microsoft.Web/customApis | listWsdlInterfaces |
| microsoft.web/locations | listwsdlinterfaces |
| 마이크로 소프트.웹 / apimanagement계정 / APIS / 연결 | 목록연결키 |
| 마이크로 소프트.웹 / apimanagement계정 / APIS / 연결 | listsecrets |
| 마이크로 소프트 웹 / 사이트 / 백업 | [목록](/rest/api/appservice/webapps/listbackups) |
| 마이크로소프트.웹/사이트/구성 | [목록](/rest/api/appservice/webapps/listconfigurations) |
| microsoft.web/sites/functions | [listkeys](/rest/api/appservice/webapps/listfunctionkeys)
| microsoft.web/sites/functions | [listsecrets](/rest/api/appservice/webapps/listfunctionsecrets) |
| microsoft.web/sites/hybridconnectionnamespaces/relays | [listkeys](/rest/api/appservice/appserviceplans/listhybridconnectionkeys) |
| microsoft.web/sites | [listsyncfunctiontriggerstatus](/rest/api/appservice/webapps/listsyncfunctiontriggers) |
| microsoft.web/sites/slots/functions | [listsecrets](/rest/api/appservice/webapps/listfunctionsecretsslot) |
| 마이크로 소프트.웹 / 사이트 / 슬롯 / 백업 | [목록](/rest/api/appservice/webapps/listbackupsslot) |
| 마이크로소프트.웹/사이트/슬롯/구성 | [목록](/rest/api/appservice/webapps/listconfigurationsslot) |
| microsoft.web/sites/slots/functions | [listsecrets](/rest/api/appservice/webapps/listfunctionsecretsslot) |

list 작업이 있는 리소스 유형을 확인할 수 있게 다음 PowerShell 옵션이 제공됩니다.

* 리소스 공급자에 대한 [REST API 작업](/rest/api/)을 보고 list 작업을 찾습니다. 예를 들어 스토리지 계정에는 [listKeys 작업](/rest/api/storagerp/storageaccounts)이 있습니다.
* [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) PowerShell cmdlet을 사용합니다. 다음 예제에서는 스토리지 계정에 대한 모든 list 작업을 가져옵니다.

  ```powershell
  Get-AzProviderOperation -OperationSearchString "Microsoft.Storage/*" | where {$_.Operation -like "*list*"} | FT Operation
  ```
* 다음 Azure CLI 명령을 사용하여 목록 작업만 필터링합니다.

  ```azurecli
  az provider operation show --namespace Microsoft.Storage --query "resourceTypes[?name=='storageAccounts'].operations[].name | [?contains(@, 'list')]"
  ```

### <a name="return-value"></a>반환 값

반환된 개체는 사용하는 list 함수에 따라 다릅니다. 예를 들어 스토리지 계정에 대한 listKeys는 다음 형식을 반환합니다.

```json
{
  "keys": [
    {
      "keyName": "key1",
      "permissions": "Full",
      "value": "{value}"
    },
    {
      "keyName": "key2",
      "permissions": "Full",
      "value": "{value}"
    }
  ]
}
```

다른 list 함수는 다른 반환 형식을 갖습니다. 함수의 형식을 보려면 예제 템플릿에 표시된 것처럼 outputs 섹션에 포함합니다.

### <a name="remarks"></a>설명

리소스 이름 또는 [resourceId 함수](#resourceid)를 사용하여 리소스를 지정합니다. 참조된 리소스를 배포하는 동일한 템플릿에서 list 함수를 사용하는 경우 리소스 이름을 사용합니다.

조건부로 배포된 리소스에서 **목록** 함수를 사용하는 경우 리소스가 배포되지 않은 경우에도 함수가 평가됩니다. **목록** 함수가 존재하지 않는 리소스를 참조하면 오류가 발생합니다. **if** 함수를 사용하여 리소스가 배포될 때만 함수가 평가되는지 확인합니다. 조건부배포 된 리소스로 if 및 list를 사용하는 샘플 템플릿에 대한 [if 함수를](template-functions-logical.md#if) 참조하십시오.

### <a name="list-example"></a>목록 예제

다음 [예제 템플릿](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/listkeys.json)에서는 출력 섹션의 스토리지 계정에서 기본 및 보조 키를 반환하는 방법을 보여줍니다. 또한 스토리지 계정에 대한 SAS 토큰을 반환합니다.

SAS 토큰을 얻으려면 만료 시간 동안 개체를 전달합니다. 만료 시간은 미래 시간이어야 합니다. 이 예제에서는 목록 함수를 사용하는 방법을 표시할 것입니다. 일반적으로 출력 값으로 SAS 토큰을 반환하지 않고 리소스 값에서 해당 토큰을 사용합니다. 출력 값은 배포 기록에 저장되므로 안전하지 않습니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storagename": {
            "type": "string"
        },
        "location": {
            "type": "string",
            "defaultValue": "southcentralus"
        },
        "accountSasProperties": {
            "type": "object",
            "defaultValue": {
                "signedServices": "b",
                "signedPermission": "r",
                "signedExpiry": "2018-08-20T11:00:00Z",
                "signedResourceTypes": "s"
            }
        }
    },
    "resources": [
        {
            "apiVersion": "2018-02-01",
            "name": "[parameters('storagename')]",
            "location": "[parameters('location')]",
            "type": "Microsoft.Storage/storageAccounts",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "StorageV2",
            "properties": {
                "supportsHttpsTrafficOnly": false,
                "accessTier": "Hot",
                "encryption": {
                    "services": {
                        "blob": {
                            "enabled": true
                        },
                        "file": {
                            "enabled": true
                        }
                    },
                    "keySource": "Microsoft.Storage"
                }
            },
            "dependsOn": []
        }
    ],
    "outputs": {
        "keys": {
            "type": "object",
            "value": "[listKeys(parameters('storagename'), '2018-02-01')]"
        },
        "accountSAS": {
            "type": "object",
            "value": "[listAccountSas(parameters('storagename'), '2018-02-01', parameters('accountSasProperties'))]"
        }
    }
}
```

## <a name="providers"></a>providers

```json
providers(providerNamespace, [resourceType])
```

리소스 공급자와 지원되는 리소스 유형에 대한 정보를 반환합니다. 리소스 유형을 제공하지 않는 경우 함수는 리소스 공급자에 대한 모든 지원되는 유형을 반환합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | Description |
|:--- |:--- |:--- |:--- |
| providerNamespace |yes |문자열 |공급자의 네임스페이스입니다. |
| resourceType |예 |문자열 |지정된 네임스페이스 내의 리소스 유형입니다. |

### <a name="return-value"></a>반환 값

각 지원되는 형식이 다음 형식으로 반환됩니다.

```json
{
    "resourceType": "{name of resource type}",
    "locations": [ all supported locations ],
    "apiVersions": [ all supported API versions ]
}
```

반환된 값의 배열 순서는 보장되지 않습니다.

### <a name="providers-example"></a>공급자 예제

다음 [예제 템플릿](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/providers.json)에서는 공급자 함수를 사용하는 방법을 보여줍니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "providerNamespace": {
            "type": "string"
        },
        "resourceType": {
            "type": "string"
        }
    },
    "resources": [],
    "outputs": {
        "providerOutput": {
            "value": "[providers(parameters('providerNamespace'), parameters('resourceType'))]",
            "type" : "object"
        }
    }
}
```

**Microsoft.Web** 리소스 공급자 및 **사이트** 리소스 유형의 경우 앞의 예제는 다음과 같은 형식의 개체를 반환합니다.

```json
{
  "resourceType": "sites",
  "locations": [
    "South Central US",
    "North Europe",
    "West Europe",
    "Southeast Asia",
    ...
  ],
  "apiVersions": [
    "2016-08-01",
    "2016-03-01",
    "2015-08-01-preview",
    "2015-08-01",
    ...
  ]
}
```

## <a name="reference"></a>reference

```json
reference(resourceName or resourceIdentifier, [apiVersion], ['Full'])
```

리소스의 런타임 상태를 나타내는 개체를 반환합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | Description |
|:--- |:--- |:--- |:--- |
| resourceName 또는 resourceIdentifier |yes |문자열 |리소스의 이름 또는 고유 식별자입니다. 현재 템플릿의 리소스를 참조할 경우 리소스 이름만 매개 변수로 지정합니다. 이전에 배포된 리소스를 참조하거나 리소스 이름이 모호한 경우 리소스 ID를 제공합니다. |
| apiVersion |예 |문자열 |지정된 리소스의 API 버전입니다. 리소스가 동일한 템플릿 내에서 프로비전되지 않은 경우 이 매개 변수를 포함합니다. 일반적으로 **yyyy-mm-dd** 형식입니다. 리소스에 대한 유효한 API 버전은 [템플릿 참조](/azure/templates/)를 참조하십시오. |
| 'Full' |예 |문자열 |전체 리소스 개체를 반환할지 여부를 지정하는 값입니다. `'Full'`을 지정하지 않으면 리소스의 속성 개체만 반환됩니다. 전체 개체에는 리소스 ID 및 위치와 같은 값이 포함됩니다. |

### <a name="return-value"></a>반환 값

모든 리소스 형식은 reference 함수에 대해 다른 속성을 반환합니다. 이 함수는 미리 정의된 단일 형식을 반환하지 않습니다. 또한 반환된 값은 전체 개체를 지정했는지 여부에 따라 다릅니다. 리소스 형식에 대한 속성을 보려면 예제와 같이 outputs 섹션의 개체를 반환합니다.

### <a name="remarks"></a>설명

참조 함수는 이전에 배포한 리소스 또는 현재 템플릿에 배포된 리소스의 런타임 상태를 검색합니다. 이 문서는 두 시나리오에 대한 예제를 보여 줍니다.

일반적으로 **참조** 함수를 사용하여 Blob 끝점 URI 또는 정규화된 도메인 이름과 같은 개체에서 특정 값을 반환합니다.

```json
"outputs": {
    "BlobUri": {
        "value": "[reference(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))).primaryEndpoints.blob]",
        "type" : "string"
    },
    "FQDN": {
        "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', parameters('ipAddressName'))).dnsSettings.fqdn]",
        "type" : "string"
    }
}
```

속성 스키마의 일부가 아닌 리소스 값이 필요하면 `'Full'`을 사용합니다. 예를 들어 키 자격 증명 모음 액세스 정책을 설정하려면 가상 머신에 대한 ID 속성을 가져옵니다.

```json
{
  "type": "Microsoft.KeyVault/vaults",
  "properties": {
    "tenantId": "[subscription().tenantId]",
    "accessPolicies": [
      {
        "tenantId": "[reference(resourceId('Microsoft.Compute/virtualMachines', variables('vmName')), '2019-03-01', 'Full').identity.tenantId]",
        "objectId": "[reference(resourceId('Microsoft.Compute/virtualMachines', variables('vmName')), '2019-03-01', 'Full').identity.principalId]",
        "permissions": {
          "keys": [
            "all"
          ],
          "secrets": [
            "all"
          ]
        }
      }
    ],
    ...
```

### <a name="valid-uses"></a>유효한 사용

참조 함수는 리소스 정의의 속성과 템플릿 또는 배포의 출력 섹션에서만 사용할 수 있습니다. [속성 반복과](copy-properties.md)함께 사용할 경우 식이 `input` 리소스 속성에 할당되어 있으므로 참조 함수를 사용할 수 있습니다. 참조 함수가 확인되기 `count` 전에 개수를 결정해야 하기 때문에 사용할 수 없습니다.

[중첩된 템플릿의](linked-templates.md#nested-template) 출력에서 참조 함수를 사용하여 중첩된 템플릿에 배포한 리소스를 반환할 수 없습니다. 대신 연결된 [템플릿을](linked-templates.md#linked-template)사용합니다.

조건부로 배포된 리소스에서 **참조** 함수를 사용하는 경우 리소스가 배포되지 않은 경우에도 함수가 평가됩니다.  **참조** 함수가 존재하지 않는 리소스를 참조하는 경우 오류가 발생합니다. **if** 함수를 사용하여 리소스가 배포될 때만 함수가 평가되는지 확인합니다. 조건부배포 된 리소스와 함께 if 및 참조를 사용하는 샘플 템플릿에 대한 [if 함수를](template-functions-logical.md#if) 참조하십시오.

### <a name="implicit-dependency"></a>암시적 종속성

참조 함수를 사용하여 참조되는 리소스가 동일한 템플릿 내에서 프로비전되는 경우 한 리소스가 다른 리소스에 종속되도록 암시적으로 선언하고, 해당 이름별로 리소스를 참조합니다(리소스 ID 아님). 또한 dependsOn 속성도 사용할 필요가 없습니다. 참조 리소스가 배포를 완료할 때까지 함수는 평가되지 않습니다.

### <a name="resource-name-or-identifier"></a>리소스 이름 또는 식별자

동일한 템플릿에 배포된 리소스를 참조할 때 리소스 이름을 제공합니다.

```json
"value": "[reference(parameters('storageAccountName'))]"
```

동일한 템플릿에 배포되지 않은 리소스를 참조할 때 리소스 ID를 제공합니다.

```json
"value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2018-07-01')]"
```

참조하는 리소스에 대한 모호성을 방지하려면 정규화된 리소스 식별자를 제공할 수 있습니다.

```json
"value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', parameters('ipAddressName')))]"
```

리소스에 대한 정규화된 참조를 생성할 때 형식과 이름의 세그먼트를 결합하는 순서는 단순히 두 세그먼트의 연결이 아닙니다. 대신, 네임스페이스 뒤에 구체성이 낮은 순으로 *형식/이름* 쌍의 시퀀스를 사용합니다.

**{리소스-공급자-네임스페이스}/{부모-리소스 유형}/{부모-리소스 이름}[/{자식-리소스 유형}/{자식-리소스 이름}]**

예를 들어:

`Microsoft.Compute/virtualMachines/myVM/extensions/myExt`는 올바릅니다. `Microsoft.Compute/virtualMachines/extensions/myVM/myExt`는 올바르지 않습니다.

리소스 ID 생성을 단순화하려면 함수 `resourceId()` 대신 이 문서에 설명된 `concat()` 함수를 사용합니다.

### <a name="get-managed-identity"></a>관리되는 ID 얻기

[Azure 리소스에 대한 관리되는 ID는](../../active-directory/managed-identities-azure-resources/overview.md) 일부 리소스에 대해 암시적으로 생성된 [확장 리소스 유형입니다.](../management/extension-resource-types.md) 관리되는 ID는 템플릿에 명시적으로 정의되지 않으므로 ID가 적용되는 리소스를 참조해야 합니다. 암시적으로 생성된 `Full` ID를 포함하여 모든 속성을 가져옵니다.

예를 들어 가상 시스템 규모 집합에 적용되는 관리되는 ID에 대한 테넌트 ID를 얻으려면 다음을 사용합니다.

```json
"tenantId": "[reference(resourceId('Microsoft.Compute/virtualMachineScaleSets',  variables('vmNodeType0Name')), '2019-03-01', 'Full').Identity.tenantId]"
```

### <a name="reference-example"></a>참조 예

다음 [예제 템플릿](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/referencewithstorage.json)에서는 리소스를 배포하고 해당 리소스를 참조합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "storageAccountName": {
          "type": "string"
      }
  },
  "resources": [
    {
      "name": "[parameters('storageAccountName')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-12-01",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
      }
    }
  ],
  "outputs": {
      "referenceOutput": {
          "type": "object",
          "value": "[reference(parameters('storageAccountName'))]"
      },
      "fullReferenceOutput": {
        "type": "object",
        "value": "[reference(parameters('storageAccountName'), '2016-12-01', 'Full')]"
      }
    }
}
```

앞의 예제에서는 두 개의 개체를 반환합니다. 속성 개체의 형식은 다음과 같습니다.

```json
{
   "creationTime": "2017-10-09T18:55:40.5863736Z",
   "primaryEndpoints": {
     "blob": "https://examplestorage.blob.core.windows.net/",
     "file": "https://examplestorage.file.core.windows.net/",
     "queue": "https://examplestorage.queue.core.windows.net/",
     "table": "https://examplestorage.table.core.windows.net/"
   },
   "primaryLocation": "southcentralus",
   "provisioningState": "Succeeded",
   "statusOfPrimary": "available",
   "supportsHttpsTrafficOnly": false
}
```

전체 개체의 형식은 다음과 같습니다.

```json
{
  "apiVersion":"2016-12-01",
  "location":"southcentralus",
  "sku": {
    "name":"Standard_LRS",
    "tier":"Standard"
  },
  "tags":{},
  "kind":"Storage",
  "properties": {
    "creationTime":"2017-10-09T18:55:40.5863736Z",
    "primaryEndpoints": {
      "blob":"https://examplestorage.blob.core.windows.net/",
      "file":"https://examplestorage.file.core.windows.net/",
      "queue":"https://examplestorage.queue.core.windows.net/",
      "table":"https://examplestorage.table.core.windows.net/"
    },
    "primaryLocation":"southcentralus",
    "provisioningState":"Succeeded",
    "statusOfPrimary":"available",
    "supportsHttpsTrafficOnly":false
  },
  "subscriptionId":"<subscription-id>",
  "resourceGroupName":"functionexamplegroup",
  "resourceId":"Microsoft.Storage/storageAccounts/examplestorage",
  "referenceApiVersion":"2016-12-01",
  "condition":true,
  "isConditionTrue":true,
  "isTemplateResource":false,
  "isAction":false,
  "provisioningOperation":"Read"
}
```

다음 [예제 템플릿](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/reference.json)에서는 이 템플릿에 배포되지 않는 스토리지 계정을 참조합니다. 스토리지 계정은 동일한 구독 내에 있어야 합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageResourceGroup": {
            "type": "string"
        },
        "storageAccountName": {
            "type": "string"
        }
    },
    "resources": [],
    "outputs": {
        "ExistingStorage": {
            "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2018-07-01')]",
            "type": "object"
        }
    }
}
```

## <a name="resourcegroup"></a>resourceGroup

```json
resourceGroup()
```

현재 리소스 그룹을 나타내는 개체를 반환합니다.

### <a name="return-value"></a>반환 값

반환된 개체는 다음 형식으로 되어 있습니다.

```json
{
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
  "name": "{resourceGroupName}",
  "type":"Microsoft.Resources/resourceGroups",
  "location": "{resourceGroupLocation}",
  "managedBy": "{identifier-of-managing-resource}",
  "tags": {
  },
  "properties": {
    "provisioningState": "{status}"
  }
}
```

**managedBy** 속성은 다른 서비스에서 관리하는 리소스를 포함하는 리소스 그룹에 대해서만 반환됩니다. 관리되는 응용 프로그램, Databricks 및 AKS의 경우 속성 값은 관리 리소스의 리소스 ID입니다.

### <a name="remarks"></a>설명

`resourceGroup()` 함수는 [구독 수준에서 배포](deploy-to-subscription.md)된 템플릿에서 사용할 수 없습니다. 리소스 그룹에 배포된 템플릿에서만 사용할 수 있습니다. 상위 템플릿이 `resourceGroup()` 구독에 배포된 경우에도 리소스 그룹을 대상으로 하는 [링크된 또는 중첩된 템플릿(내부 범위)에서](linked-templates.md) 이 함수를 사용할 수 있습니다. 이 시나리오에서는 링크된 또는 중첩 된 템플릿 리소스 그룹 수준에서 배포 됩니다. 구독 수준 배포에서 리소스 그룹을 대상으로 하는 것에 대한 자세한 내용은 [하나 이상의 구독 또는 리소스 그룹에 Azure 리소스 배포를](cross-resource-group-deployment.md)참조합니다.

resourceGroup 함수는 일반적으로 리소스 그룹과 동일한 위치에 리소스를 만드는 데 사용됩니다. 다음 예제에서는 기본 매개 변수 값에 대 한 리소스 그룹 위치를 사용 합니다.

```json
"parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    }
}
```

resourceGroup 함수를 사용하여 리소스 그룹의 태그를 리소스에 적용할 수도 있습니다. 자세한 내용은 [리소스 그룹의 태그 적용을](../management/tag-resources.md#apply-tags-from-resource-group)참조하십시오.

중첩된 템플릿을 사용하여 여러 리소스 그룹에 배포하는 경우 resourceGroup 함수를 평가하기 위한 범위를 지정할 수 있습니다. 자세한 내용은 [둘 이상의 구독 또는 리소스 그룹에 Azure 리소스 배포](cross-resource-group-deployment.md)를 참조하세요.

### <a name="resource-group-example"></a>리소스 그룹 예제

다음 [예제 템플릿](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/resourcegroup.json)은 리소스 그룹의 속성을 반환합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "resourceGroupOutput": {
            "value": "[resourceGroup()]",
            "type" : "object"
        }
    }
}
```

앞의 예제는 다음과 같은 형식의 개체를 반환합니다.

```json
{
  "id": "/subscriptions/{subscription-id}/resourceGroups/examplegroup",
  "name": "examplegroup",
  "type":"Microsoft.Resources/resourceGroups",
  "location": "southcentralus",
  "properties": {
    "provisioningState": "Succeeded"
  }
}
```

## <a name="resourceid"></a>resourceId

```json
resourceId([subscriptionId], [resourceGroupName], resourceType, resourceName1, [resourceName2], ...)
```

리소스의 고유 식별자를 반환합니다. 리소스 이름이 모호하거나 동일한 템플릿 내에서 프로비전되지 않은 경우 이 함수를 사용합니다. 반환된 식별자의 형식은 배포가 리소스 그룹, 구독, 관리 그룹 또는 테넌트의 범위에서 발생하는지 여부에 따라 달라집니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | Description |
|:--- |:--- |:--- |:--- |
| subscriptionId |예 |문자열(GUID 형식) |기본값은 현재 구독입니다. 다른 구독에서 리소스를 검색해야 하는 경우 이 값을 지정합니다. 리소스 그룹 또는 구독의 범위에서 배포할 때만 이 값을 제공합니다. |
| resourceGroupName |예 |문자열 |기본값은 현재 리소스 그룹입니다. 다른 리소스 그룹에서 리소스를 검색해야 하는 경우 이 값을 지정합니다. 리소스 그룹의 범위에 배포할 때만 이 값을 제공합니다. |
| resourceType |yes |문자열 |리소스 공급자 네임스페이스를 포함하는 리소스 유형입니다. |
| resourceName1 |yes |문자열 |리소스의 이름입니다. |
| resourceName2 |예 |문자열 |필요한 경우 다음 리소스 이름 세그먼트입니다. |

리소스 유형에 더 많은 세그먼트가 포함된 경우 리소스 이름을 매개 변수로 계속 추가합니다.

### <a name="return-value"></a>반환 값

템플릿이 리소스 그룹의 범위에 배포되면 리소스 ID는 다음 형식으로 반환됩니다.

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

구독 수준 [배포에서](deploy-to-subscription.md)사용되는 경우 리소스 ID는 다음 형식으로 반환됩니다.

```json
/subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

관리 그룹 [수준 배포](deploy-to-management-group.md) 또는 테넌트 수준 배포에서 사용할 경우 리소스 ID는 다음과 같은 형식으로 반환됩니다.

```json
/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

다른 형식으로 ID를 얻으려면 다음을 참조하십시오.

* [확장리소스 ID](#extensionresourceid)
* [구독리소스 ID](#subscriptionresourceid)
* [테넌트리소스ID](#tenantresourceid)

### <a name="remarks"></a>설명

제공하는 매개 변수 의 수는 리소스가 부모 또는 하위 리소스인지 여부와 리소스가 동일한 구독 또는 리소스 그룹에 있는지 여부에 따라 달라집니다.

동일한 구독 및 리소스 그룹의 상위 리소스에 대한 리소스 ID를 얻으려면 리소스의 유형과 이름을 제공합니다.

```json
"[resourceId('Microsoft.ServiceBus/namespaces', 'namespace1')]"
```

자식 리소스에 대한 리소스 ID를 얻으려면 리소스 유형의 세그먼트 수에 주의하십시오. 리소스 유형의 각 세그먼트에 대한 리소스 이름을 제공합니다. 세그먼트의 이름은 계층의 해당 부분에 대해 존재하는 리소스에 해당합니다.

```json
"[resourceId('Microsoft.ServiceBus/namespaces/queues/authorizationRules', 'namespace1', 'queue1', 'auth1')]"
```

동일한 구독이지만 다른 리소스 그룹에 있는 리소스에 대한 리소스 ID를 얻으려면 리소스 그룹 이름을 제공합니다.

```json
"[resourceId('otherResourceGroup', 'Microsoft.Storage/storageAccounts', 'examplestorage')]"
```

다른 구독 및 리소스 그룹의 리소스에 대한 리소스 ID를 얻으려면 구독 ID 및 리소스 그룹 이름을 제공합니다.

```json
"[resourceId('xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', 'otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]"
```

대체 리소스 그룹의 스토리지 계정 또는 가상 네트워크를 사용할 경우 이 함수를 사용해야 합니다. 다음 예에서는 외부 리소스 그룹의 리소스를 쉽게 사용할 수 있는 방법을 보여 줍니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "virtualNetworkName": {
          "type": "string"
      },
      "virtualNetworkResourceGroup": {
          "type": "string"
      },
      "subnet1Name": {
          "type": "string"
      },
      "nicName": {
          "type": "string"
      }
  },
  "variables": {
      "subnet1Ref": "[resourceId(parameters('virtualNetworkResourceGroup'), 'Microsoft.Network/virtualNetworks/subnets', parameters('virtualNetworkName'), parameters('subnet1Name'))]"
  },
  "resources": [
  {
      "apiVersion": "2015-05-01-preview",
      "type": "Microsoft.Network/networkInterfaces",
      "name": "[parameters('nicName')]",
      "location": "[parameters('location')]",
      "properties": {
          "ipConfigurations": [{
              "name": "ipconfig1",
              "properties": {
                  "privateIPAllocationMethod": "Dynamic",
                  "subnet": {
                      "id": "[variables('subnet1Ref')]"
                  }
              }
          }]
       }
  }]
}
```

### <a name="resource-id-example"></a>리소스 ID 예제

다음 [예제 템플릿](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/resourceid.json)에서는 리소스 그룹의 스토리지 계정에 대한 리소스 ID를 반환합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "sameRGOutput": {
            "value": "[resourceId('Microsoft.Storage/storageAccounts','examplestorage')]",
            "type" : "string"
        },
        "differentRGOutput": {
            "value": "[resourceId('otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]",
            "type" : "string"
        },
        "differentSubOutput": {
            "value": "[resourceId('11111111-1111-1111-1111-111111111111', 'otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]",
            "type" : "string"
        },
        "nestedResourceOutput": {
            "value": "[resourceId('Microsoft.SQL/servers/databases', 'serverName', 'databaseName')]",
            "type" : "string"
        }
    }
}
```

기본 값을 사용한 이전 예제의 출력은 다음과 같습니다.

| 이름 | Type | 값 |
| ---- | ---- | ----- |
| sameRGOutput | String | /subscriptions/{current-sub-id}/resourceGroups/examplegroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| differentRGOutput | String | /subscriptions/{current-sub-id}/resourceGroups/otherResourceGroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| differentSubOutput | String | /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/otherResourceGroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| nestedResourceOutput | String | /subscriptions/{current-sub-id}/resourceGroups/examplegroup/providers/Microsoft.SQL/servers/serverName/databases/databaseName |

## <a name="subscription"></a>subscription

```json
subscription()
```

현재 배포에 대한 구독 관련 세부 정보를 반환합니다.

### <a name="return-value"></a>반환 값

이 함수는 다음 형식을 반환합니다.

```json
{
    "id": "/subscriptions/{subscription-id}",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}",
    "displayName": "{name-of-subscription}"
}
```

### <a name="remarks"></a>설명

중첩된 템플릿을 사용하여 여러 구독에 배포하는 경우 구독 함수를 평가하기 위한 범위를 지정할 수 있습니다. 자세한 내용은 [둘 이상의 구독 또는 리소스 그룹에 Azure 리소스 배포](cross-resource-group-deployment.md)를 참조하세요.

### <a name="subscription-example"></a>구독 예제

다음 [예제 템플릿](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/subscription.json)에서는 출력 섹션에서 호출되는 구독 함수를 보여줍니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "subscriptionOutput": {
            "value": "[subscription()]",
            "type" : "object"
        }
    }
}
```

## <a name="subscriptionresourceid"></a>구독리소스 ID

```json
subscriptionResourceId([subscriptionId], resourceType, resourceName1, [resourceName2], ...)
```

구독 수준에서 배포된 리소스에 대한 고유 식별자를 반환합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | Description |
|:--- |:--- |:--- |:--- |
| subscriptionId |예 |문자열(GUID 형식) |기본값은 현재 구독입니다. 다른 구독에서 리소스를 검색해야 하는 경우 이 값을 지정합니다. |
| resourceType |yes |문자열 |리소스 공급자 네임스페이스를 포함하는 리소스 유형입니다. |
| resourceName1 |yes |문자열 |리소스의 이름입니다. |
| resourceName2 |예 |문자열 |필요한 경우 다음 리소스 이름 세그먼트입니다. |

리소스 유형에 더 많은 세그먼트가 포함된 경우 리소스 이름을 매개 변수로 계속 추가합니다.

### <a name="return-value"></a>반환 값

식별자는 다음 형식으로 반환됩니다.

```json
/subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

### <a name="remarks"></a>설명

이 함수를 사용하여 리소스 그룹이 아닌 [구독에 배포된](deploy-to-subscription.md) 리소스에 대한 리소스 ID를 가져옵니다. 반환된 ID는 [resourceId](#resourceid) 함수에서 반환되는 값과 다르며 리소스 그룹 값을 포함하지 않습니다.

### <a name="subscriptionresourceid-example"></a>구독리소스ID 예제

다음 템플릿은 기본 제공 역할을 할당합니다. 리소스 그룹 또는 구독에 배포할 수 있습니다. 구독ResourceId 함수를 사용하여 기본 제공 역할에 대한 리소스 ID를 가져옵니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string",
            "metadata": {
                "description": "The principal to assign the role to"
            }
        },
        "builtInRoleType": {
            "type": "string",
            "allowedValues": [
                "Owner",
                "Contributor",
                "Reader"
            ],
            "metadata": {
                "description": "Built-in role to assign"
            }
        },
        "roleNameGuid": {
            "type": "string",
            "defaultValue": "[newGuid()]",
            "metadata": {
                "description": "A new GUID used to identify the role assignment"
            }
        }
    },
    "variables": {
        "Owner": "[subscriptionResourceId('Microsoft.Authorization/roleDefinitions', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')]",
        "Contributor": "[subscriptionResourceId('Microsoft.Authorization/roleDefinitions', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
        "Reader": "[subscriptionResourceId('Microsoft.Authorization/roleDefinitions', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[parameters('roleNameGuid')]",
            "properties": {
                "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

## <a name="tenantresourceid"></a>테넌트리소스ID

```json
tenantResourceId(resourceType, resourceName1, [resourceName2], ...)
```

테넌트 수준에서 배포된 리소스에 대한 고유 식별자를 반환합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | Description |
|:--- |:--- |:--- |:--- |
| resourceType |yes |문자열 |리소스 공급자 네임스페이스를 포함하는 리소스 유형입니다. |
| resourceName1 |yes |문자열 |리소스의 이름입니다. |
| resourceName2 |예 |문자열 |필요한 경우 다음 리소스 이름 세그먼트입니다. |

리소스 유형에 더 많은 세그먼트가 포함된 경우 리소스 이름을 매개 변수로 계속 추가합니다.

### <a name="return-value"></a>반환 값

식별자는 다음 형식으로 반환됩니다.

```json
/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

### <a name="remarks"></a>설명

이 함수를 사용하여 테넌트에 배포되는 리소스에 대한 리소스 ID를 가져옵니다. 반환된 ID는 리소스 그룹 또는 구독 값을 포함하지 않음으로써 다른 리소스 ID 함수에서 반환된 값과 다릅니다.

## <a name="next-steps"></a>다음 단계

* Azure 리소스 관리자 템플릿의 섹션에 대한 설명은 [Azure 리소스 관리자 템플릿 작성을](template-syntax.md)참조하십시오.
* 여러 템플릿을 병합하려면 [연결된 템플릿 사용을 Azure 리소스 관리자와](linked-templates.md)함께 참조합니다.
* 리소스 형식을 만들 때 지정된 횟수를 반복하려면 [Azure 리소스 관리자에서 여러 리소스 인스턴스 만들기를](copy-resources.md)참조하십시오.
* 만든 템플릿을 배포하는 방법을 보려면 [Azure Resource Manager 템플릿을 사용하여 애플리케이션 배포](deploy-powershell.md)를 참조하세요.

