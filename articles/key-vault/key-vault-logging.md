---
title: Azure Key Vault 로깅 | Microsoft Docs
description: 이 자습서를 사용하여 Azure Key Vault 로깅을 시작할 수 있습니다.
services: key-vault
author: barclayn
manager: barbkess
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/18/2019
ms.author: barclayn
ms.openlocfilehash: 89f9ef37ed7c53817854442b3a32b32b7d11ae27
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "64706014"
---
# <a name="azure-key-vault-logging"></a>Azure Key Vault 로깅

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

하나 이상의 키 자격 증명 모음을 만든 후에 모니터링 하려는 가능성이 키 자격 증명 모음 액세스 및 사용자는 방법 및 시기입니다. 사용자가 제공한 Azure storage 계정에 정보를 저장 하는 Azure Key Vault에 대 한 로깅을 사용 하 여이 수행할 수 있습니다. 라는 새 컨테이너 **insights-로그-auditevent** 지정 된 저장소 계정에 대해 자동으로 만들어집니다. 여러 키 자격 증명 모음에 대 한 로그를 수집 하기 위한이 저장소 계정을 사용할 수 있습니다.

액세스할 수 있습니다 로깅 정보에 10 분 (최대) 키 자격 증명 모음 작업 후. 대부분의 경우 이것보다 빠릅니다.  저장소 계정의 로그 관리에 따라 다릅니다.

* 표준 Azure 액세스 제어 메서드를 사용하여 액세스할 수 있는 사용자를 제한하여 로그를 보호합니다.
* 더 이상 스토리지 계정에 유지하지 않으려는 로그를 삭제합니다.

이 자습서를 사용하여 Azure Key Vault 로깅을 시작할 수 있습니다. 저장소 계정 만들기, 로깅을 활성화 하 고 수집 된 로그 정보를 해석 됩니다.  

> [!NOTE]
> 이 자습서는 키 자격 증명 모음, 키 또는 암호를 만드는 방법에 대한 지침을 다루지 않습니다. 자세한 내용은 [Azure Key Vault란?](key-vault-overview.md)을 참조하세요. 플랫폼 간 Azure CLI 지침을 참조 하세요. 또는 [이 해당 자습서](key-vault-manage-with-cli2.md)합니다.
>
> 이 문서에서는 진단 로깅 업데이트에 대 한 Azure PowerShell 지침을 제공 합니다. Azure Monitor를 사용 하 여 진단 로깅을 업데이트할 수도 있습니다는 **진단 로그** Azure 포털의 섹션입니다. 
>

Key Vault에 대 한 개요 정보를 참조 하세요 [Azure Key Vault 란?](key-vault-whatis.md)합니다. Key Vault를 사용할 수 있는 정보에 대 한 참조를 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/key-vault/)합니다.

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음이 필요합니다.

* 사용하고 있는 기존 키 자격 증명 모음  
* Azure PowerShell, 1.0.0의 최소 버전입니다. Azure PowerShell을 설치하고 Azure 구독에 연결하려면 [Azure PowerShell 설치 및 구성하는 방법](/powershell/azure/overview)을 참조하세요. 이미 Azure PowerShell을 설치 하 고 Azure PowerShell 콘솔에서 버전을 모르는 경우 입력 `$PSVersionTable.PSVersion`합니다.  
* 키 자격 증명 모음 로그에 대한 Azure의 충분한 스토리지.

## <a id="connect"></a>키 자격 증명 모음 구독에 연결

로그인 하려는 키 자격 증명 모음에 Azure PowerShell이 방법은 키 로깅을 설정 하는 첫 번째 단계입니다.

Azure PowerShell 세션을 시작하고 다음 명령을 사용하여 Azure 계정에 로그인합니다.  

```powershell
Connect-AzAccount
```

팝업 브라우저 창에 Azure 계정 사용자 이름 및 암호를 입력합니다. Azure PowerShell이 계정과 연결 된 모든 구독을 가져옵니다. 기본적으로 PowerShell은 첫 번째를 사용 합니다.

키 자격 증명 모음을 만드는 데는 구독을 지정 해야 합니다. 계정에 대 한 구독을 보려면 다음 명령을 입력 합니다.

```powershell
Get-AzSubscription
```

그런 다음 로깅하려에서는 key vault와 사용 하 여 연결 된 구독을 지정 하려면 다음을 입력:

```powershell
Set-AzContext -SubscriptionId <subscription ID>
```

계정과 연결 된 구독이 여러 개 있는 경우에 특히 중요 한 단계에는 올바른 구독을 사용 하 여 PowerShell 가리키는 합니다. Azure PowerShell 구성에 관한 자세한 내용은 [Azure PowerShell 설치 및 구성 방법](/powershell/azure/overview)을 참조하세요.

## <a id="storage"></a>로그에 대 한 저장소 계정 만들기

로그에 대 한 기존 저장소 계정을 사용할 수 있지만 키 자격 증명 모음 로그 전용 저장소 계정을 만듭니다. 나중에이 지정 해야 경우에 대 한 편의 위해 라는 변수에 세부 정보를 저장할 것 **sa**합니다.

추가 관리 편의성을 위해 key vault를 포함 하는 것으로 동일한 리소스 그룹을 사용할 수도 됩니다. [-시작 자습서](key-vault-get-started.md),이 리소스 그룹의 이름은 **ContosoResourceGroup**, 동아시아 위치를 사용 하도록 계속 됩니다. 해당 하는 경우 고유의 작업으로 이러한 값을 바꿉니다.

```powershell
 $sa = New-AzStorageAccount -ResourceGroupName ContosoResourceGroup -Name contosokeyvaultlogs -Type Standard_LRS -Location 'East Asia'
```

> [!NOTE]
> 기존 저장소 계정을 사용 하려는 경우 key vault와 동일한 구독을 사용 해야 합니다. 및 클래식 배포 모델 보다는 Azure Resource Manager 배포 모델을 사용 해야 합니다.
>
>

## <a id="identify"></a>로그에 대한 주요 자격 증명 모음 식별

에 [-시작 자습서](key-vault-get-started.md), 키 자격 증명 모음 이름은 **ContosoKeyVault**합니다. 해당 이름을 사용 하 여 라는 변수에 세부 정보를 저장 및 계속 **kv**:

```powershell
$kv = Get-AzKeyVault -VaultName 'ContosoKeyVault'
```

## <a id="enable"></a>로깅 사용

Key Vault에 대 한 로깅을 사용 하려면 사용 합니다 **집합 AzDiagnosticSetting** 새 저장소 계정 및 key vault 용으로 만든 변수와 함께 cmdlet. 설정 된 **-사용 하도록 설정** 플래그를 **$true** 범주를 설정 하 고 **AuditEvent** (Key Vault 로깅에 대 한 유일한 범주):

```powershell
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent
```

출력은 다음과 같이 표시됩니다.

    StorageAccountId   : /subscriptions/<subscription-GUID>/resourceGroups/ContosoResourceGroup/providers/Microsoft.Storage/storageAccounts/ContosoKeyVaultLogs
    ServiceBusRuleId   :
    StorageAccountName :
        Logs
        Enabled           : True
        Category          : AuditEvent
        RetentionPolicy
        Enabled : False
        Days    : 0

이 출력 확인 키 자격 증명 모음에 대 한 로깅을 사용할 수 정보 저장소 계정에 저장 됩니다.

필요에 따라 오래 된 로그는 자동으로 삭제 되도록 로그에 대 한 보존 정책을 설정할 수 있습니다. 예를 들어 보존 정책을 설정 하 여 설정 합니다 **-RetentionEnabled** 플래그를 **$true**를 설정 합니다 **-RetentionInDays** 매개 변수를 **90**90 일 보다 오래 된 로그는 자동으로 삭제 되도록 합니다.

```powershell
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent -RetentionEnabled $true -RetentionInDays 90
```

다음이 로깅됩니다.

* 모든 액세스 권한, 시스템 오류 또는 잘못 된 요청으로 인해 실패 한 요청을 포함 하 여 REST API 요청을 인증 합니다.
* 키에 대 한 작업 자격 증명 모음 자체를 만들기, 삭제, 키 자격 증명 모음 액세스 정책 설정, 등 태그와 같은 키 자격 증명 모음 특성을 업데이트 합니다.
* Key vault에서 키와 비밀에 대 한 작업 포함:
  * 만들기, 수정 또는 이러한 키 또는 암호를 삭제 합니다.
  * 서명, 확인, 암호화, 암호 해독, 래핑 및 래핑 해제 키, 비밀 및 목록 키와 비밀 (및 해당 버전)를 시작 합니다.
* 401 응답이 발생하는 인증되지 않은 요청. 예로 없는 요청 하는 전달자 토큰 형식이 잘못 되거나 만료 되거나 토큰이 잘못 된는 있습니다.  

## <a id="access"></a>로그에 액세스

키 자격 증명 모음 로그에 저장 됩니다는 **insights-로그-auditevent** 사용자가 제공한 저장소 계정의 컨테이너입니다. 로그를 보려면 blob을 다운로드 해야 합니다.

먼저, 컨테이너 이름에 대한 변수를 만듭니다. 이 연습의 나머지 전체에서이 변수를 사용할 수 있습니다.

```powershell
$container = 'insights-logs-auditevent'
```

이 컨테이너에 있는 모든 blob을 나열 하려면 다음을 입력 합니다.

```powershell
Get-AzStorageBlob -Container $container -Context $sa.Context
```

출력은 다음과 비슷합니다.

```
Container Uri: https://contosokeyvaultlogs.blob.core.windows.net/insights-logs-auditevent

Name

- - -
resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=05/h=01/m=00/PT1H.json

resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=02/m=00/PT1H.json

resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=18/m=00/PT1H.json
```

이 출력에서 확인할 수 있듯이 Blob은 명명 규칙 `resourceId=<ARM resource ID>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json`을 따릅니다.

날짜 및 시간 값은 UTC를 사용합니다.

동일한 저장소 계정을 여러 리소스에 대 한 로그를 수집 하려면를 사용할 수 있으므로 blob 이름에 전체 리소스 ID에 액세스 하거나 다운로드 해야 하는 blob만 유용 합니다. 하지만 그 전에 먼저 모든 Blob을 다운로드하는 방법을 다룹니다.

Blob을 다운로드할 폴더를 만듭니다. 예를 들면 다음과 같습니다.

```powershell 
New-Item -Path 'C:\Users\username\ContosoKeyVaultLogs' -ItemType Directory -Force
```

그런 다음 모든 Blob 목록을 가져옵니다.  

```powershell
$blobs = Get-AzStorageBlob -Container $container -Context $sa.Context
```

통해이 목록을 파이프 **Get AzStorageBlobContent** 대상 폴더에 blob을 다운로드 하려면:

```powershell
$blobs | Get-AzStorageBlobContent -Destination C:\Users\username\ContosoKeyVaultLogs'
```

이 두 번째 명령을 실행할 때 Blob 이름의 **/** 구분 기호는 대상 폴더 아래에 전체 폴더 구조를 만듭니다. 다운로드 하 여 blob을 파일로 저장 하는이 구조를 사용 합니다.

선택적으로 Blob을 다운로드하려면 와일드카드를 사용합니다. 예를 들면 다음과 같습니다.

* 여러 키 자격 증명 모음이 있고 CONTOSOKEYVAULT3이라는 하나의 키 자격 증명 모음에 대한 로그를 다운로드하려는 경우:

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/VAULTS/CONTOSOKEYVAULT3
  ```

* 리소스 그룹이 여러 개이고 하나의 리소스 그룹에 대한 로그를 다운로드하려는 경우 `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`을(를) 사용합니다.

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
  ```

* 2019 년 1 월에 대 한 모든 로그를 다운로드 하려는 경우 사용 하 여 `-Blob '*/year=2019/m=01/*'`:

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/year=2016/m=01/*'
  ```

이제 로그에 있는 것을 확인할 준비가 되었습니다. 하지만 두 개의 명령도 알아야 할 사실에 진행 하기 전에:

* 주요 자격 증명 모음 리소스의 진단 설정 상태를 쿼리하려면: `Get-AzDiagnosticSetting -ResourceId $kv.ResourceId`
* 주요 자격 증명 모음 리소스의 로깅을 사용하지 않으려면: `Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $false -Category AuditEvent`

## <a id="interpret"></a>Key Vault 로그 해석

개별 Blob은 JSON Blob 형식으로 텍스트로 저장됩니다. 로그 항목의 예제를 살펴보겠습니다. 다음 명령을 실행합니다.

```powershell
Get-AzKeyVault -VaultName 'contosokeyvault'`
```

반환 로그 항목을이 이와 비슷합니다.

```json
    {
        "records":
        [
            {
                "time": "2016-01-05T01:32:01.2691226Z",
                "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
                "operationName": "VaultGet",
                "operationVersion": "2015-06-01",
                "category": "AuditEvent",
                "resultType": "Success",
                "resultSignature": "OK",
                "resultDescription": "",
                "durationMs": "78",
                "callerIpAddress": "104.40.82.76",
                "correlationId": "",
                "identity": {"claim":{"http://schemas.microsoft.com/identity/claims/objectidentifier":"d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn":"live.com#username@outlook.com","appid":"1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},
                "properties": {"clientInfo":"azure-resource-manager/2.0","requestUri":"https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id":"https://contosokeyvault.vault.azure.net/","httpStatusCode":200}
            }
        ]
    }
```

다음 표에서 필드 이름 및 설명을 나열합니다.

| 필드 이름 | 설명 |
| --- | --- |
| **time** |날짜 및 시간 (UTC)입니다. |
| **resourceId** |Azure Resource Manager 리소스 ID입니다. 키 자격 증명 모음 로그의 경우 이는 항상 키 자격 증명 모음 리소스 ID입니다. |
| **operationName** |다음 표에 설명된 대로 작업의 이름입니다. |
| **operationVersion** |클라이언트에서 요청한 REST API 버전입니다. |
| **category** |결과의 형식입니다. Key Vault 로그의 경우 **AuditEvent** 단일의 사용 가능한 값입니다. |
| **resultType** |REST API 요청의 결과입니다. |
| **resultSignature** |HTTP 상태입니다. |
| **resultDescription** |사용 가능한 경우 결과에 대한 추가 설명입니다. |
| **durationMs** |밀리초 단위로 REST API 요청을 처리하는 데 걸린 시간입니다. 네트워크 대기 시간을 포함하지 않으므로 클라이언트 쪽에서 측정한 시간은 이 시간과 일치하지 않을 수 있습니다. |
| **callerIpAddress** |요청한 클라이언트의 IP 주소입니다. |
| **correlationId** |클라이언트가 서비스 쪽(키 자격 증명 모음) 로그를 사용하여 클라이언트 쪽 로그 상관 관계를 지정하도록 전달할 수 있는 선택적 GUID입니다. |
| **identity** |REST API 요청에 제공 된 토큰의 id입니다. "User"는 일반적으로 "서비스 주체" 또는 Azure PowerShell cmdlet에서 생성 되는 요청의 경우와 같이 "사용자 + appId" 조합 합니다. |
| **properties** |작업에 따라 달라 지는 정보 (**operationName**). 대부분의 경우이 필드는 클라이언트 정보 (클라이언트에서 전달 된 사용자 에이전트 문자열), 정확한 REST API 요청 URI 및 HTTP 상태 코드를 포함 합니다. 개체가 요청의 결과로 반환 되는 경우에 또한 (예를 들어 **: KeyCreate** 또는 **VaultGet**), 키도 포함 된 URI ("id"), URI 또는 암호 정보 URI에 자격 증명 모음. |

합니다 **operationName** 필드 값은 *ObjectVerb* 형식입니다. 예를 들면 다음과 같습니다.

* 모든 주요 자격 증명 모음 작업에는 `Vault<action>` 형식으로 `VaultGet` 및 `VaultCreate`합니다.
* 모든 주요 작업은는 `Key<action>` 형식으로 `KeySign` 고 `KeyList`입니다.
* 모든 암호 작업은는 `Secret<action>` 형식으로 `SecretGet` 고 `SecretListVersions`입니다.

다음 표에서 **operationName** 값 및 해당 REST API 명령:

| operationName | REST API 명령 |
| --- | --- |
| **인증** |Azure Active Directory 끝점을 통해 인증 |
| **VaultGet** |[키 자격 증명 모음에 대한 정보 가져오기](https://msdn.microsoft.com/library/azure/mt620026.aspx) |
| **VaultPut** |[키 자격 증명 모음 만들기 또는 업데이트](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| **VaultDelete** |[키 자격 증명 모음 삭제](https://msdn.microsoft.com/library/azure/mt620022.aspx) |
| **VaultPatch** |[키 자격 증명 모음 업데이트](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| **VaultList** |[리소스 그룹의 모든 키 자격 증명 모음 목록](https://msdn.microsoft.com/library/azure/mt620027.aspx) |
| **KeyCreate** |[키 만들기](https://msdn.microsoft.com/library/azure/dn903634.aspx) |
| **KeyGet** |[키에 대한 정보 가져오기](https://msdn.microsoft.com/library/azure/dn878080.aspx) |
| **KeyImport** |[자격 증명 모음으로 키 가져오기](https://msdn.microsoft.com/library/azure/dn903626.aspx) |
| **KeyBackup** |[키 백업](https://msdn.microsoft.com/library/azure/dn878058.aspx) |
| **KeyDelete** |[키 삭제](https://msdn.microsoft.com/library/azure/dn903611.aspx) |
| **KeyRestore** |[키 복원](https://msdn.microsoft.com/library/azure/dn878106.aspx) |
| **KeySign** |[키로 서명](https://msdn.microsoft.com/library/azure/dn878096.aspx) |
| **KeyVerify** |[키로 확인](https://msdn.microsoft.com/library/azure/dn878082.aspx) |
| **KeyWrap** |[키 래핑](https://msdn.microsoft.com/library/azure/dn878066.aspx) |
| **KeyUnwrap** |[키 래핑 취소](https://msdn.microsoft.com/library/azure/dn878079.aspx) |
| **KeyEncrypt** |[키로 암호화](https://msdn.microsoft.com/library/azure/dn878060.aspx) |
| **KeyDecrypt** |[키로 암호 해독](https://msdn.microsoft.com/library/azure/dn878097.aspx) |
| **KeyUpdate** |[키 업데이트](https://msdn.microsoft.com/library/azure/dn903616.aspx) |
| **KeyList** |[자격 증명 모음에 키 나열](https://msdn.microsoft.com/library/azure/dn903629.aspx) |
| **KeyListVersions** |[키 버전 나열](https://msdn.microsoft.com/library/azure/dn986822.aspx) |
| **SecretSet** |[암호 만들기](https://msdn.microsoft.com/library/azure/dn903618.aspx) |
| **SecretGet** |[암호를 가져오려는 경우](https://msdn.microsoft.com/library/azure/dn903633.aspx) |
| **SecretUpdate** |[암호 업데이트](https://msdn.microsoft.com/library/azure/dn986818.aspx) |
| **SecretDelete** |[암호 삭제](https://msdn.microsoft.com/library/azure/dn903613.aspx) |
| **SecretList** |[자격 증명 모음에 암호 나열](https://msdn.microsoft.com/library/azure/dn903614.aspx) |
| **SecretListVersions** |[암호 버전 나열](https://msdn.microsoft.com/library/azure/dn986824.aspx) |

## <a id="loganalytics"></a>Azure Monitor 로그 사용

Key Vault를 검토 하려면 Azure Monitor 로그에서 Key Vault 솔루션을 사용할 수 있습니다 **AuditEvent** 로그 합니다. Azure Monitor 로그 로그 쿼리를 사용 하 여 데이터 분석에 필요한 정보를 가져옵니다. 

설정 하는 방법을 비롯 한 자세한 내용은 참조 하세요. [Azure Monitor 로그에서 Azure Key Vault 솔루션](../azure-monitor/insights/azure-key-vault.md)합니다. 이 문서에서는 또한 Azure storage 계정에 로그 먼저 라우트된 미리 보기 및 구성 된 Azure Monitor에서에서 읽을 로그는 Azure Monitor 로그 중 제공 된 이전 Key Vault 솔루션에서 마이그레이션해야 할 경우 지침이 포함 되어 있습니다.

## <a id="next"></a>다음 단계

.NET 웹 응용 프로그램에서 Azure Key Vault를 사용 하는 자습서를 참조 하세요 [웹 응용 프로그램에서 사용 하 여 Azure Key Vault](tutorial-net-create-vault-azure-web-app.md)합니다.

프로그래밍 참조는 [Azure Key Vault 개발자 가이드](key-vault-developers-guide.md)를 참조하세요.

Azure Key Vault에 대 한 Azure PowerShell 1.0 cmdlet 목록은 참조 하세요 [Azure Key Vault cmdlet](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault)합니다.

키 회전 및 Azure Key Vault를 사용 하 여 감사 로그에 대 한 자습서를 참조 하세요 [종단 간 키 회전 및 감사를 사용 하 여 Key Vault 설정](key-vault-key-rotation-log-monitoring.md)합니다.
