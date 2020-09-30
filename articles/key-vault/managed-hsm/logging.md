---
title: Azure 관리형 HSM 로깅
description: 이 자습서를 사용하여 관리형 HSM 로깅을 시작할 수 있습니다.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: mbaldwin
ms.openlocfilehash: 22abd38ead1257b49eeae98acfcd74349f563811
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90992296"
---
# <a name="managed-hsm-logging"></a>관리형 HSM 로깅 

하나 이상의 관리형 HSM을 만든 후에는 HSM에 액세스하는 방법, 시기 및 사용자를 모니터링하려고 할 수도 있습니다. Azure 스토리지 계정에 제공하는 정보를 저장하는 로깅을 사용하여 이를 수행할 수 있습니다. 지정한 스토리지 계정에 대해 **insights-logs-auditevent**라는 새 컨테이너가 자동으로 만들어집니다. 여러 관리형 HSM에 대한 로그를 수집하는 데 이 동일한 스토리지 계정을 사용할 수 있습니다.

관리형 HSM 작업 후 최대 10분 동안 로깅 정보에 액세스할 수 있습니다. 대부분의 경우 이것보다 빠릅니다.  스토리지 계정의 로그 관리에 따라 다릅니다.

* 표준 Azure 액세스 제어 메서드를 사용하여 액세스할 수 있는 사용자를 제한하여 로그를 보호합니다.
* 더 이상 스토리지 계정에 유지하지 않으려는 로그를 삭제합니다.

이 자습서를 사용하여 관리형 HSM 로깅을 시작할 수 있습니다. 스토리지 계정을 만들고, 로깅을 사용하도록 설정하고, 수집된 로그 정보를 해석합니다.  

> [!NOTE]
> 이 자습서는 관리형 HSM 또는 키를 만드는 방법에 대한 지침을 다루지 않습니다. 이 문서에서는 진단 로깅을 업데이트하기 위한 Azure CLI 지침을 제공합니다.

## <a name="prerequisites"></a>사전 요구 사항

이 문서의 단계를 완료하려면 다음 항목이 있어야 합니다.

* Microsoft Azure에 대한 구독. 아직 구독하지 않은 경우 [평가판](https://azure.microsoft.com/pricing/free-trial)에 등록할 수 있습니다.
* Azure CLI 버전 2.12.0 이상 `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치]( /cli/azure/install-azure-cli)를 참조하세요.
* 구독의 관리형 HSM [빠른 시작: Azure CLI를 사용하여 관리형 HSM 프로비저닝 및 활성화](quick-create-cli.md)를 참조하여 관리형 HSM을 프로비저닝하고 활성화합니다.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="connect-to-your-azure-subscription"></a>Azure 구독에 연결

키 로깅을 설정하는 첫 번째 단계는 Azure CLI를 로깅하려는 관리형 HSM으로 가리키는 것입니다.

```azurecli-interactive
az login
```

CLI를 통한 로그인 옵션에 대한 자세한 내용은 [Azure CLI로 로그인](/cli/azure/authenticate-azure-cli?view=azure-cli-latest&preserve-view=true)을 살펴보세요.

관리형 HSM을 만드는 데 사용한 구독을 지정해야 할 수도 있습니다. 다음 명령을 입력하여 계정에 대한 구독을 확인합니다.

## <a name="identify-the-managed-hsm-and-storage-account"></a>관리형 HSM 및 스토리지 계정 식별

```azurecli-interactive
hsmresource=$(az keyvault show --hsm-name ContosoMHSM --query id -o tsv)
storageresource=$(az storage account show --name ContosoMHSMLogs --query id -o tsv)
```

## <a name="enable-logging"></a>로깅 사용

관리형 HSM에 대한 로깅을 사용하도록 설정하려면 새 스토리지 계정 및 관리형 HSM에 대해 만든 변수와 함께 **az monitor diagnostic-settings create** 명령을 사용합니다. 또한 **-Enabled** 플래그를 **$true**로 설정하고, 범주를 **AuditEvent**(관리형 HSM 로깅에 대한 유일한 범주)로 설정합니다.

이 출력에서는 이제 관리형 HSM에 대한 로깅을 사용하도록 설정되었음을 확인하고, 정보를 스토리지 계정에 저장합니다.

필요에 따라 오래된 로그가 자동으로 삭제되도록 로그 보존 정책을 설정할 수 있습니다. 예를 들어 **-RetentionEnabled** 플래그를 **$true**로 설정하여 보존 정책을 설정하고, 90일보다 오래된 로그가 자동으로 삭제되도록 **-RetentionInDays** 매개 변수를 **90**으로 설정합니다.

```azurecli-interactive
az monitor diagnostic-settings create --name ContosoMHSM-Diagnostics --resource $hsmresource --logs '[{"category": "AuditEvent","enabled": true}]' --storage-account $storageresource
```

다음이 로깅됩니다.

* 인증된 모든 REST API 요청(예: 액세스 권한, 시스템 오류 또는 잘못된 요청으로 인해 실패한 요청)
* 태그와 같은 특성을 만들고 삭제하고 업데이트하는 작업을 포함하여 관리형 HSM에 대한 작업입니다.
* 초기화 및 다운로드, 초기화 복구, 업로드와 같은 보안 도메인 관련 작업
* 전체 HSM 백업, 복원 및 선택적 복원 작업
* 다음을 포함하여 키에 대한 작업
  * 키 만들기, 수정 또는 삭제
  * 키 서명, 확인, 암호화, 암호 해독, 래핑 및 래핑 해제, 키 나열
  * 키 백업, 복원, 제거
* 401 응답이 발생하는 인증되지 않은 요청. 예를 들어 전달자 토큰이 없거나, 형식이 잘못되었거나 만료되었거나, 잘못된 토큰이 있는 요청입니다.  

## <a name="access-your-logs"></a>로그에 액세스

관리형 HSM 로그는 제공한 스토리지 계정의 **insights-logs-auditevent** 컨테이너에 저장됩니다. 로그를 보려면 Blob을 다운로드해야 합니다. Azure Storage에 대한 자세한 내용은 [Azure CLI를 사용하여 Blob 만들기, 다운로드 및 나열](../../storage/blobs/storage-quickstart-blobs-cli.md)을 참조하세요.

개별 Blob은 JSON 형식으로 텍스트로 저장됩니다. 로그 항목 예제를 살펴보겠습니다. 아래 예제에서는 전체 백업을 만드는 요청이 관리형 HSM으로 전송되는 경우의 로그 항목을 보여 줍니다.

```json
[
  {
    "TenantId": "766eaf62-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "time": "2020-08-31T19:52:39.763Z",
    "resourceId": "/SUBSCRIPTIONS/A1BA9AAA-xxxx-xxxx-xxxx-xxxxxxxxxxxx/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/MANAGEDHSMS/CONTOSOMHSM",
    "operationName": "BackupCreate",
    "operationVersion": "7.0",
    "category": "AuditEvent",
    "resultType": "Success",
    "properties": {
        "PoolType": "M-HSM",
        "sku_Family": "B",
        "sku_Name": "Standard_B1"
    },
    "durationMs": 488,
    "callerIpAddress": "X.X.X.X",
    "identity": "{\"claim\":{\"appid\":\"04b07795-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"http_schemas_microsoft_com_identity\":{\"claims\":{\"objectidentifier\":\"b1c52bf0-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"}},\"http_schemas_xmlsoap_org_ws_2005_05_identity\":{\"claims\":{\"upn\":\"admin@contoso.com\"}}}}",
    "clientInfo": "azsdk-python-core/1.7.0 Python/3.8.2 (Linux-4.19.84-microsoft-standard-x86_64-with-glibc2.29) azsdk-python-azure-keyvault/7.2",
    "correlationId": "8806614c-ebc3-11ea-9e9b-00155db778ad",
    "subnetId": "(unknown)",
    "httpStatusCode": 202,
    "PoolName": "mhsmdemo",
    "requestUri": "https://ContosoMHSM.managedhsm.azure.net/backup",
    "resourceGroup": "ContosoResourceGroup",
    "resourceProvider": "MICROSOFT.KEYVAULT",
    "resource": "ContosoMHSM",
    "resourceType": "managedHSMs"
  }
]
```

다음 표에는 필드 이름 및 설명이 나와 있습니다.

| 필드 이름 | Description |
| --- | --- |
| **TenantId** | 관리형 HSM을 만드는 구독의 Azure Active Directory 테넌트 ID |
| **time** |UTC 형식의 날짜 및 시간입니다. |
| **resourceId** |Azure Resource Manager 리소스 ID입니다. 관리형 HSM 로그의 경우 이는 항상 관리형 HSM 리소스 ID입니다. |
| **operationName** |다음 표에 설명된 대로 작업의 이름입니다. |
| **operationVersion** |클라이언트에서 요청한 REST API 버전입니다. |
| **category** |결과 유형입니다. 관리형 HSM 로그의 경우 **AuditEvent**는 사용 가능한 단일 값입니다. |
| **resultType** |REST API 요청의 결과입니다. |
| **properties** |작업(**operationName**)에 따라 달라지는 정보입니다.|
| **resultSignature** |HTTP 상태입니다. |
| **resultDescription** |사용 가능한 경우 결과에 대한 추가 설명입니다. |
| **durationMs** |밀리초 단위로 REST API 요청을 처리하는 데 걸린 시간입니다. 네트워크 대기 시간을 포함하지 않으므로 클라이언트 쪽에서 측정한 시간은 이 시간과 일치하지 않을 수 있습니다. |
| **callerIpAddress** |요청한 클라이언트의 IP 주소입니다. |
| **correlationId** |클라이언트가 서비스 쪽 로그를 사용하여 클라이언트 쪽 로그 상관 관계를 지정하도록 전달할 수 있는 선택적 GUID입니다. |
| **identity** |REST API 요청에 제공된 토큰의 ID입니다. 일반적으로 "사용자," "서비스 사용자"입니다. |
| **requestUri** | REST API 요청 URI |
| **clientInfo** | 

## <a name="use-azure-monitor-logs"></a>Azure Monitor 로그 사용

Azure Monitor 로그에서 Key Vault 솔루션을 사용하여 관리형 HSM **AuditEvent** 로그를 검토할 수 있습니다. Azure Monitor 로그에서 로그 쿼리를 사용하여 데이터를 분석하고 필요한 정보를 가져옵니다. 

## <a name="next-steps"></a>다음 단계

- 관리형 HSM을 프로비저닝하고 사용하는 [모범 사례](best-practices.md)에 대해 알아봅니다.
- 관리형 HSM [백업 및 복원 방법](backup-restore.md)에 대해 알아봅니다.
