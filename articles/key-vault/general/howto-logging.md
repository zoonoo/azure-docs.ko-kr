---
title: Azure Key Vault 로깅을 사용 하도록 설정 하는 방법
description: 사용자가 제공 하는 Azure storage 계정에 정보를 저장 하는 Azure Key Vault에 대 한 로깅을 사용 하도록 설정 하는 방법입니다.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 10/01/2020
ms.author: mbaldwin
ms.openlocfilehash: 5e0007f3b0dad8a68e9d81cebbe9fe24b5a7db3c
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93285656"
---
# <a name="how-to-enable-key-vault-logging"></a>키 자격 증명 모음 로깅을 사용하는 방법

하나 이상의 키 자격 증명 모음을 만든 후에는 키 자격 증명 모음에 액세스하는 방법, 시기 및 사용자를 모니터링하려고 할 수도 있습니다. 기능에 대 한 자세한 내용은 [Key Vault 로깅](logging.md)을 참조 하세요.

## <a name="prerequisites"></a>필수 구성 요소

이 자습서를 완료하려면 다음 항목이 필요합니다.

* 사용하고 있는 기존 키 자격 증명 모음  
* Azure CLI 또는 Azure PowerShell.
* 키 자격 증명 모음 로그에 대한 Azure의 충분한 스토리지.

CLI를 로컬로 설치 하 고 사용 하도록 선택 하는 경우에는 Azure CLI 버전 2.0.4 이상을 이상이 필요 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요. CLI를 사용하여 Azure에 로그인하려면 다음을 입력합니다.

```azurecli-interactive
az login
```

PowerShell을 로컬로 설치 하 고 사용 하도록 선택 하는 경우에는 Azure PowerShell 모듈 버전 1.0.0 이상이 필요 합니다. `$PSVersionTable.PSVersion` 명령을 실행하여 버전을 찾습니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzAccount`를 실행하여 Azure와 연결해야 합니다.

```powershell-interactive
Connect-AzAccount
```

## <a name="connect-to-your-key-vault-subscription"></a>Key Vault 구독에 연결

키 로깅을 설정 하는 첫 번째 단계는 주요 자격 증명 모음을 포함 하는 구독에 연결 하는 것입니다. 계정과 연결 된 구독이 여러 개 있는 경우에 특히 중요 합니다.

Azure CLI를 사용 하 여 [az account list](/cli/azure/account?view=azure-cli-latest#az_account_list) 명령을 사용 하 여 모든 구독을 확인 한 다음 [az account set](/cli/azure/account?view=azure-cli-latest#az_account_set)를 사용 하 여 하나에 연결할 수 있습니다.

```azurecli-interactive
az account list

az account set --subscription "<subscriptionID>"
```

Azure PowerShell를 사용 하 여 먼저 [AzSubscription](/powershell/module/az.accounts/get-azsubscription?view=azps-4.7.0) cmdlet을 사용 하 여 구독을 나열 한 다음 [AzContext](/powershell/module/az.accounts/set-azcontext?view=azps-4.7.0) cmdlet을 사용 하 여 구독에 연결할 수 있습니다. 

```powershell-interactive
Get-AzSubscription

Set-AzContext -SubscriptionId "<subscriptionID>"
```

## <a name="create-a-storage-account-for-your-logs"></a>로그에 대한 스토리지 계정 만들기

로그에 기존 저장소 계정을 사용할 수 있지만 Key Vault 로그 전용 새 저장소 계정을 만듭니다. 

추가로 쉽게 관리하기 위해 키 자격 증명 모음이 포함된 것과 동일한 리소스 그룹을 사용합니다. [Azure CLI 빠른](quick-create-cli.md) 시작 및 [Azure PowerShell 빠른](quick-create-powershell.md)시작에서이 리소스 그룹의 이름은 **myresourcegroup** 이 고 위치는 *에서는 eastus* 입니다. 해당 하는 경우 이러한 값을 사용자 고유의 값으로 바꿉니다. 

또한 저장소 계정 이름을 제공 해야 합니다. 저장소 계정 이름은 길이가 3 자에서 24 자 사이이 고 숫자 및 소문자만 사용 해야 합니다.  마지막으로 "Standard_LRS" SKU의 저장소 계정을 만듭니다.

Azure CLI를 사용 하 여 [az storage account create](/cli/azure/storage/account?view=azure-cli-latest#az_storage_account_create) 명령을 사용 합니다.

```azurecli-interactive
az storage account create --name "<your-unique-storage-account-name>" -g "myResourceGroup" --sku "Standard_LRS"
```

Azure PowerShell를 사용 하 여 [AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount?view=azps-4.7.0) cmdlet을 사용 합니다. 리소스 그룹에 해당 하는 위치를 제공 해야 합니다.

```powershell
 New-AzStorageAccount -ResourceGroupName myResourceGroup -Name "<your-unique-storage-account-name>" -Type "Standard_LRS" -Location "eastus"
```

두 경우 모두 저장소 계정의 "id"를 확인 합니다. Azure CLI 작업은 출력에서 "id"를 반환 합니다. Azure PowerShell에서 "id"를 얻으려면 [AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount?view=azps-4.7.0) 를 사용 하 고 $sa 변수에 출력을 할당 합니다. 그런 다음 $sa. id를 사용 하 여 저장소 계정을 볼 수 있습니다. ("$Sa. Context "속성도이 문서의 뒷부분에서 사용 됩니다.

```powershell-interactive
$sa = Get-AzStorageAccount -Name "<your-unique-storage-account-name>" -ResourceGroup "myResourceGroup"
$sa.id
```

저장소 계정의 "id"는 "/subscriptions/<>/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/<" 형식으로 되어 있습니다.--ID> "입니다.

> [!NOTE]
> 기존 저장소 계정을 사용 하기로 결정 한 경우에는 키 자격 증명 모음과 동일한 구독을 사용 해야 하며 클래식 배포 모델이 아닌 Azure Resource Manager 배포 모델을 사용 해야 합니다.

## <a name="obtain-your-key-vault-resource-id"></a>주요 자격 증명 모음 리소스 ID 가져오기

[CLI 빠른](quick-create-cli.md) 시작 및 [PowerShell 빠른](quick-create-powershell.md)시작에서 고유 이름으로 키를 만들었습니다.  아래 단계에서 해당 이름을 다시 사용 합니다.  키 자격 증명 모음의 이름을 기억할 수 없는 경우 Azure CLI [az keyvault list](/cli/azure/keyvault?view=azure-cli-latest#az_keyvault_list) 명령 또는 Azure PowerShell [AzKeyVault](/powershell/module/az.keyvault/get-azkeyvault?view=azps-4.7.0) cmdlet을 사용 하 여 나열할 수 있습니다.

키 자격 증명 모음 이름을 사용 하 여 리소스 ID를 찾습니다.  Azure CLI를 사용 하 여 [az keyvault show](/cli/azure/keyvault?view=azure-cli-latest#az_keyvault_show) 명령을 사용 합니다.

```azurecli-interactive
az keyvault show --name "<your-unique-keyvault-name>"
```

Azure PowerShell를 사용 하 여 [AzKeyVault](/powershell/module/az.keyvault/get-azkeyvault?view=azps-4.7.0) cmdlet을 사용 합니다.

```powershell-interactive
Get-AzKeyVault -VaultName "<your-unique-keyvault-name>"
```

키 자격 증명 모음에 대 한 리소스 ID는 "/subscriptions/<>/resourceGroups/myResourceGroup/providers/Microsoft.KeyVault/vaults/<.-ID>" 형식입니다. 다음 단계를 확인 합니다.

## <a name="enable-logging-using-azure-powershell"></a>Azure PowerShell을 통해 로깅 사용

Key Vault에 대 한 로깅을 사용 하도록 설정 하려면 저장소 계정 ID 및 키 자격 증명 모음 리소스 ID와 함께 Azure CLI [az monitor 진단-설정 create](/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest) 명령 또는 [AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting?view=azps-4.7.0) cmdlet을 사용 합니다.

```azurecli-interactive
az monitor diagnostic-settings create --storage-account "<storage-account-id>" --resource "<key-vault-resource-id>" --name "Key vault logs" --logs '[{"category": "AuditEvent","enabled": true}]' --metrics '[{"category": "AllMetrics","enabled": true}]'
```

Azure PowerShell에서는 **-Enabled** 플래그가 **$true** 로 설정 되 고 범주가로 설정 된 [Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting?view=azps-4.7.0) `AuditEvent` (Key Vault 로깅의 유일한 범주) AzDiagnosticSetting cmdlet을 사용 합니다.

```powershell-interactive
Set-AzDiagnosticSetting -ResourceId "<key-vault-resource-id>" -StorageAccountId $sa.id -Enabled $true -Category "AuditEvent"
```

필요에 따라 로그에 대 한 보존 정책을 설정 하 여 오래 된 로그가 지정 된 시간 후에 자동으로 삭제 되도록 할 수 있습니다. 예를 들어 90 일 보다 오래 된 로그를 자동으로 삭제 하는 보존 정책을 설정할 수 있습니다.

<!-- With the Azure CLI, use the [az monitor diagnostic-settings update](/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest#az_monitor_diagnostic_settings_update) command. 

```azurecli-interactive
az monitor diagnostic-settings update 
```
-->

Azure PowerShell를 사용 하 여 [AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting?view=azps-4.7.0) cmdlet을 사용 합니다. 

```powershell-interactive
Set-AzDiagnosticSetting "<key-vault-resource-id>" -StorageAccountId $sa.id -Enabled $true -Category AuditEvent -RetentionEnabled $true -RetentionInDays 90
```

기록 되는 내용:

* 인증된 모든 REST API 요청(예: 액세스 권한, 시스템 오류 또는 잘못된 요청으로 인해 실패한 요청)
* 키 자격 증명 모음 자체에 대한 작업(예: 키 자격 증명 모음 액세스 정책 만들기, 삭제, 설정 및 태그와 같은 키 자격 증명 모음 특성 업데이트)
* 다음을 포함하여 키 자격 증명 모음의 키 및 비밀에 대한 작업
  * 이러한 키 또는 비밀 만들기, 수정 또는 삭제
  * 서명, 확인, 암호화, 암호 해독, 키 래핑 및 래핑 해제, 비밀 가져오기, 키 및 비밀(및 해당 버전) 나열
* 401 응답이 발생하는 인증되지 않은 요청. 예를 들어 전달자 토큰이 없거나, 형식이 잘못되었거나 만료되었거나, 잘못된 토큰이 있는 요청입니다.  
* 곧 만료되는 정책, 만료된 정책 및 자격 증명 모음 액세스 정책에 대한 Event Grid 알림 이벤트가 변경되었습니다(새 버전 이벤트가 기록되지 않음). 키 자격 증명 모음에 생성된 이벤트 구독이 있는지 여부에 관계 없이 이벤트가 기록됩니다. 자세한 내용은 [Key Vault에 대한 Event Grid 이벤트 스키마](../../event-grid/event-schema-key-vault.md)를 참조하세요.

## <a name="access-your-logs"></a>로그에 액세스

Key Vault 로그는 제공 된 저장소 계정의 "insights-로그-auditevent" 컨테이너에 저장 됩니다. 로그를 보려면 Blob을 다운로드해야 합니다.

먼저 컨테이너의 모든 blob을 나열 합니다.  Azure CLI를 사용 하 여 [az storage blob list](/cli/azure/storage/blob?view=azure-cli-latest#az_storage_blob_list) 명령을 사용 합니다.

```azurecli-interactive
az storage blob list --account-name "<your-unique-storage-account-name>" --container-name "insights-logs-auditevent"
```

Azure PowerShell를 사용 하 여이 컨테이너의 모든 blob을 [AzStorageBlob](/powershell/module/az.storage/get-azstorageblob?view=azps-4.7.0) 나열 하 고 다음을 입력 합니다.

```powershell
Get-AzStorageBlob -Container $container -Context $sa.Context
```

Azure CLI 명령 또는 Azure PowerShell cmdlet의 출력에서 볼 수 있듯이 blob의 이름은 형식으로 지정 됩니다 `resourceId=<ARM resource ID>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json` . 날짜 및 시간 값은 UTC를 사용합니다.

동일한 스토리지 계정을 사용하여 여러 리소스에 대한 로그를 수집할 수 있으므로 Blob 이름의 전체 리소스 ID는 필요한 Blob에 액세스하거나 다운로드하는 데 유용합니다. 하지만 그 전에 먼저 모든 Blob을 다운로드하는 방법을 다룹니다.

Azure CLI를 사용 하 여 [az storage blob download](/cli/azure/storage/blob?view=azure-cli-latest#az_storage_blob_download) 명령을 사용 하 고 blob의 이름과 결과를 저장 하려는 파일의 경로를 전달 합니다.

```azurecli-interactive
az storage blob download --container-name "insights-logs-auditevent" --file <path-to-file> --name "<blob-name>" --account-name "<your-unique-storage-account-name>"
```

Azure PowerShell를 사용 하 여 [AzStorageBlobs](/powershell/module/az.storage/get-azstorageblob?view=azps-4.7.0) cmdlet을 사용 하 여 blob 목록을 가져온 다음 [AzStorageBlobContent](/powershell/module/az.storage/get-azstorageblobcontent?view=azps-4.7.0) cmdlet으로 파이프 하 여 선택한 경로에 로그를 다운로드 합니다.

```powershell-interactive
$blobs = Get-AzStorageBlob -Container $container -Context $sa.Context | Get-AzStorageBlobContent -Destination "<path-to-file>"
```

PowerShell에서이 두 번째 cmdlet을 실행 하는 경우 **/** blob 이름의 구분 기호는 대상 폴더 아래에 전체 폴더 구조를 만듭니다. 이 구조를 사용하여 Blob을 다운로드하고 파일로 저장합니다.

선택적으로 Blob을 다운로드하려면 와일드카드를 사용합니다. 다음은 그 예입니다.

* 여러 키 자격 증명 모음이 있고 CONTOSOKEYVAULT3이라는 하나의 키 자격 증명 모음에 대한 로그를 다운로드하려는 경우:

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/VAULTS/CONTOSOKEYVAULT3
  ```

* 리소스 그룹이 여러 개이고 하나의 리소스 그룹에 대한 로그를 다운로드하려는 경우 `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`을(를) 사용합니다.

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
  ```

* 2019년 1월의 모든 로그를 다운로드하려면 `-Blob '*/year=2019/m=01/*'`을 사용합니다.

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/year=2016/m=01/*'
  ```

이제 로그에 있는 것을 확인할 준비가 되었습니다. 그러나 이 작업을 진행하기 전에 추가적으로 다음 두 가지 명령을 알아야 합니다.

로그를 읽는 방법에 대 한 자세한 내용은 [Key Vault 로깅: Key Vault 로그 해석](logging.md#interpret-your-key-vault-logs)

## <a name="use-azure-monitor-logs"></a>Azure Monitor 로그 사용

Azure Monitor 로그에서 Key Vault 솔루션을 사용하여 Key Vault `AuditEvent` 로그를 검토할 수 있습니다. Azure Monitor 로그에서 로그 쿼리를 사용하여 데이터를 분석하고 필요한 정보를 가져옵니다.

이를 설정하는 방법을 포함한 자세한 내용은 [Azure Monitor의 Azure Key Vault](../../azure-monitor/insights/key-vault-insights-overview.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- Key Vault 로그를 해석 하는 방법을 비롯 한 개념 정보는 [Key Vault 로깅](logging.md) 을 참조 하세요.
- .NET 웹 애플리케이션에서 Azure Key Vault를 사용하는 자습서는 [웹 애플리케이션에서 Azure Key Vault 사용](tutorial-net-create-vault-azure-web-app.md)을 참조하세요.
- 프로그래밍 참조는 [Azure Key Vault 개발자 가이드](developers-guide.md)를 참조하세요.