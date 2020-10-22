---
title: Azure Monitor에 사용 현황 데이터, 메트릭 및 로그 업로드
description: 리소스 인벤토리, 사용 현황 데이터, 메트릭 및 로그를 Azure Monitor에 업로드 합니다.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
zone_pivot_groups: client-operating-system-macos-and-linux-windows-powershell
ms.openlocfilehash: c333b95ed762c905511ab1d4a84050d50f0e023c
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92371327"
---
# <a name="upload-usage-data-metrics-and-logs-to-azure-monitor"></a>Azure Monitor에 사용 현황 데이터, 메트릭 및 로그 업로드

정기적으로 청구 목적, 모니터링 메트릭 및 로그에 대 한 사용 정보를 내보낸 후 Azure에 업로드할 수 있습니다. 이러한 세 가지 유형의 데이터를 내보내기와 업로드 하면 Azure에서 데이터 컨트롤러, SQL 관리 되는 인스턴스 및 PostgreSQL Hyperscale 서버 그룹 리소스도 생성 및 업데이트 됩니다.

> [!NOTE] 
> 미리 보기 기간 중에는 Azure Arc 사용 데이터 서비스를 사용 하는 비용이 없습니다.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

다음 작업을 수행 해야 하는 사용 현황 데이터, 메트릭 또는 로그를 업로드할 수 있습니다.

* 도구 설치 
* [`Microsoft.AzureData`리소스 공급자 등록](#register-the-resource-provider) 
* [서비스 주체 만들기](#create-service-principal)

## <a name="install-tools"></a>도구 설치

필요한 도구는 다음과 같습니다. 
* Azure CLI (az) 
* [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] 

[도구 설치](./install-client-tools.md)를 참조 하세요.

## <a name="register-the-resource-provider"></a>리소스 공급자 등록

메트릭 또는 사용자 데이터를 Azure에 업로드 하기 전에 Azure 구독에 리소스 공급자가 등록 되어 있는지 확인 해야 `Microsoft.AzureData` 합니다.

리소스 공급자를 확인 하려면 다음 명령을 실행 합니다.

```azurecli
az provider show -n Microsoft.AzureData -o table
```

리소스 공급자가 현재 구독에 등록 되어 있지 않으면 등록할 수 있습니다. 등록 하려면 다음 명령을 실행 합니다.  이 명령을 완료하는 데 1~2분 정도 걸릴 수 있습니다.

```azurecli
az provider register -n Microsoft.AzureData --wait
```

## <a name="create-service-principal"></a>서비스 주체 만들기

서비스 주체는 사용 및 메트릭 데이터를 업로드 하는 데 사용 됩니다.

다음 명령을 수행 하 여 메트릭 업로드 서비스 주체를 만듭니다.

> [!NOTE]
> 서비스 주체를 만들려면 [Azure에서 특정 권한이](/azure/active-directory/develop/howto-create-service-principal-portal#permissions-required-for-registering-an-app)필요 합니다.

서비스 주체를 만들려면 다음 예제를 업데이트 합니다. 를 `<ServicePrincipalName>` 서비스 사용자의 이름으로 바꾸고 명령을 실행 합니다.

```azurecli
az ad sp create-for-rbac --name <ServicePrincipalName>
``` 

이전에 서비스 주체를 만들고 현재 자격 증명을 가져와야 하는 경우 다음 명령을 실행 하 여 자격 증명을 다시 설정 합니다.

```azurecli
az ad sp credential reset --name <ServicePrincipalName>
```

예를 들어 라는 서비스 주체를 만들려면 `azure-arc-metrics` 다음 명령을 실행 합니다.

```
az ad sp create-for-rbac --name azure-arc-metrics
```

예제 출력:

```output
"appId": "2e72adbf-de57-4c25-b90d-2f73f126e123",
"displayName": "azure-arc-metrics",
"name": "http://azure-arc-metrics",
"password": "5039d676-23f9-416c-9534-3bd6afc78123",
"tenant": "72f988bf-85f1-41af-91ab-2d7cd01ad1234"
```

`appId` `password` `tenant` 나중에 사용 하기 위해, 및 값을 환경 변수에 저장 합니다. 

::: zone pivot="client-operating-system-windows-command"

```console
SET SPN_CLIENT_ID=<appId>
SET SPN_CLIENT_SECRET=<password>
SET SPN_TENANT_ID=<tenant>
```

::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```console
export SPN_CLIENT_ID='<appId>'
export SPN_CLIENT_SECRET='<password>'
export SPN_TENANT_ID='<tenant>'
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```console
$Env:SPN_CLIENT_ID="<appId>"
$Env:SPN_CLIENT_SECRET="<password>"
$Env:SPN_TENANT_ID="<tenant>"
```

::: zone-end

서비스 주체를 만든 후에는 해당 역할에 서비스 주체를 할당 합니다. 

## <a name="assign-roles-to-the-service-principal"></a>서비스 사용자에 게 역할 할당

다음 명령을 실행 하 여 `Monitoring Metrics Publisher` 데이터베이스 인스턴스 리소스가 있는 구독의 역할에 서비스 주체를 할당 합니다.

::: zone pivot="client-operating-system-windows-command"

> [!NOTE]
> Windows 환경에서 실행할 때 역할 이름에 큰따옴표를 사용 해야 합니다.

```azurecli
az role assignment create --assignee <appId> --role "Monitoring Metrics Publisher" --scope subscriptions/<Subscription ID>
az role assignment create --assignee <appId> --role "Contributor" --scope subscriptions/<Subscription ID>
```
::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```azurecli
az role assignment create --assignee <appId> --role 'Monitoring Metrics Publisher' --scope subscriptions/<Subscription ID>
az role assignment create --assignee <appId> --role 'Contributor' --scope subscriptions/<Subscription ID>
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```powershell
az role assignment create --assignee <appId> --role 'Monitoring Metrics Publisher' --scope subscriptions/<Subscription ID>
az role assignment create --assignee <appId> --role 'Contributor' --scope subscriptions/<Subscription ID>
```

::: zone-end

예제 출력:

```output
{
  "canDelegate": null,
  "id": "/subscriptions/<Subscription ID>/providers/Microsoft.Authorization/roleAssignments/f82b7dc6-17bd-4e78-93a1-3fb733b912d",
  "name": "f82b7dc6-17bd-4e78-93a1-3fb733b9d123",
  "principalId": "5901025f-0353-4e33-aeb1-d814dbc5d123",
  "principalType": "ServicePrincipal",
  "roleDefinitionId": "/subscriptions/<Subscription ID>/providers/Microsoft.Authorization/roleDefinitions/3913510d-42f4-4e42-8a64-420c39005123",
  "scope": "/subscriptions/<Subscription ID>",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

적절 한 역할에 할당 된 서비스 주체를 사용 하 여 계속 해 서 메트릭 또는 사용자 데이터를 업로드할 수 있습니다. 

## <a name="upload-logs-metrics-or-user-data"></a>로그, 메트릭 또는 사용자 데이터 업로드

로그, 메트릭 또는 사용자 데이터를 업로드 하기 위한 구체적인 단계는 업로드 하는 정보의 유형에 따라 달라 집니다. 

[Azure Monitor에 로그 업로드](upload-logs.md)

[Azure Monitor에 메트릭 업로드](upload-metrics.md)

[Azure Monitor에 사용 현황 데이터 업로드](upload-usage-data.md)

## <a name="general-guidance-on-exporting-and-uploading-usage-metrics"></a>사용, 메트릭 내보내기 및 업로드에 대 한 일반 지침

Azure Arc 활성화 된 데이터 서비스에 대 한 만들기, 읽기, 업데이트 및 삭제 (CRUD) 작업은 청구 및 모니터링을 위해 기록 됩니다. 이러한 CRUD 작업을 모니터링 하 고 소비를 적절 하 게 계산 하는 백그라운드 서비스가 있습니다. 사용 또는 소비의 실제 계산은 일정에 따라 수행 되며 백그라운드에서 수행 됩니다. 

미리 보기 중에이 프로세스는 밤에 발생 합니다. 일반적인 지침은 하루에 한 번만 사용을 업로드 하는 것입니다. 사용 정보를 동일한 24 시간 내에 여러 번 내보내고 업로드 하면 리소스 인벤토리가 Azure Portal 업데이트 되지만 리소스 사용량은 업데이트 되지 않습니다.

메트릭 업로드의 경우 Azure monitor는 최근 30 분의 데이터만 수락 합니다 ([자세한 정보](../../azure-monitor/platform/metrics-store-custom-rest-api.md#troubleshooting)). 메트릭을 업로드 하기 위한 지침은 내보내기 파일을 만든 후 즉시 메트릭을 업로드 하 여 Azure Portal에서 전체 데이터 집합을 볼 수 있도록 하는 것입니다. 예를 들어, 메트릭을 2:00 PM으로 내보내고 2:50 PM에 업로드 명령을 실행 한 경우 Azure Monitor는 최근 30 분 동안의 데이터만 허용 하므로 포털에 데이터가 표시 되지 않을 수 있습니다. 

## <a name="next-steps"></a>다음 단계

[서비스 주체에 대 한 자세한 정보](/powershell/azure/azurerm/create-azure-service-principal-azureps#what-is-a-service-principal)

[청구 데이터를 Azure에 업로드 하 고 Azure Portal에서 확인](view-billing-data-in-azure.md)

[Azure Portal에서 Azure Arc 데이터 컨트롤러 리소스 보기](view-data-controller-in-azure-portal.md)