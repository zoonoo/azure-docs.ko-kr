---
title: Azure CLI를 사용 하 여 VM 확장 사용
description: 이 문서에서는 Azure CLI를 사용 하 여 하이브리드 클라우드 환경에서 실행 되는 Azure Arc 사용 서버에 가상 머신 확장을 배포 하는 방법을 설명 합니다.
ms.date: 11/06/2020
ms.topic: conceptual
ms.custom: devx-track-azurecli
ms.openlocfilehash: bf0a3e0940efc7e79adbe9f763ffdf34ea690fac
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94833268"
---
# <a name="enable-azure-vm-extensions-using-the-azure-cli"></a>Azure CLI를 사용 하 여 Azure VM 확장을 사용 하도록 설정

이 문서에서는 Azure CLI를 사용 하 여 Azure Arc 사용 서버에서 지 원하는 Azure VM 확장을 Linux 또는 Windows 하이브리드 컴퓨터에 배포 하 고 제거 하는 방법을 보여 줍니다.

[!INCLUDE [Azure CLI Prepare your environment](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="install-the-azure-cli-extension"></a>Azure CLI 확장 설치

ConnectedMachine 명령은 Azure CLI의 일부로 제공 되지 않습니다. Azure CLI를 사용 하 여 Arc 사용 서버에서 관리 하는 하이브리드 서버에서 VM 확장을 관리 하기 전에 ConnectedMachine 확장을 로드 해야 합니다. 다음 명령을 실행 하 여 가져옵니다.

```azurecli
az extension add --name connectedmachine
```

## <a name="enable-extension"></a>확장 사용

Arc 사용 서버에서 VM 확장을 사용 하도록 설정 하려면, [az connectedmachine machine-extension create](/cli/azure/ext/connectedmachine/connectedmachine/machine-extension#ext_connectedmachine_az_connectedmachine_machine_extension_create) ,,, `--machine-name` `--extension-name` `--location` `--type` `settings` 및 `--publisher` 매개 변수와 함께 az connectedmachine create를 사용 합니다.

다음 예제에서는 Arc 사용 Linux 서버에서 Log Analytics VM 확장을 사용 하도록 설정 합니다.

```azurecli
az connectedmachine machine-extension create --machine-name "myMachineName" --name "OmsAgentforLinux" --location "eastus" --type "CustomScriptExtension" --publisher "Microsoft.EnterpriseCloud.Monitoring" --settings "{\"workspaceId\":\"workspaceId"}" --protected-settings "{\workspaceKey\":"\workspaceKey"} --type-handler-version "1.10" --resource-group "myResourceGroup"
```

다음 예에서는 Arc 사용 서버에서 사용자 지정 스크립트 확장을 사용 하도록 설정 합니다.

```azurecli
az connectedmachine machine-extension create --machine-name "myMachineName" --name "CustomScriptExtension" --location "eastus" --type "CustomScriptExtension" --publisher "Microsoft.Compute" --settings "{\"commandToExecute\":\"powershell.exe -c \\\"Get-Process | Where-Object { $_.CPU -gt 10000 }\\\"\"}" --type-handler-version "1.10" --resource-group "myResourceGroup"
```

다음 예에서는 Arc 사용 서버에서 Key Vault VM 확장 (미리 보기)을 사용 하도록 설정 합니다.

```azurecli
az connectedmachine machine-extension create --resource-group "resourceGroupName" --machine-name "myMachineName" --location "regionName" --publisher "Microsoft.Azure.KeyVault" --type "KeyVaultForLinux or KeyVaultForWindows" --name "KeyVaultForLinux or KeyVaultForWindows" --settings '{"secretsManagementSettings": { "pollingIntervalInS": "60", "observedCertificates": ["observedCert1"] }, "authenticationSettings": { "msiEndpoint": "http://localhost:40342/metadata/identity" }}'
```

## <a name="list-extensions-installed"></a>설치 된 목록 확장

Arc 사용 서버에서 VM 확장 목록을 가져오려면 및 매개 변수와 함께 [az connectedmachine list](/cli/azure/ext/connectedmachine/connectedmachine/machine-extension#ext_connectedmachine_az_connectedmachine_machine_extension_list) 를 사용 `--machine-name` `--resource-group` 합니다.

예제:

```azurecli
az connectedmachine machine-extension list --machine-name "myMachineName" --resource-group "myResourceGroup"
```

기본적으로 Azure CLI 명령의 출력은 JSON(JavaScript Object Notation)으로 제공됩니다. 기본 출력을 목록이나 테이블로 변경하려면 [az configure --output](/cli/azure/reference-index)을 사용합니다. 출력 형식을 1번 변경하기 위해 명령에 `--output`을 추가할 수도 있습니다.

다음 예제에서는 명령의 부분 JSON 출력을 보여 줍니다 `az connectedmachine machine-extension -list` .

```json
[
  {
    "autoUpgradingMinorVersion": "false",
    "forceUpdateTag": null,
    "id": "/subscriptions/subscriptionId/resourceGroups/resourceGroupName/providers/Microsoft.HybridCompute/machines/SVR01/extensions/DependencyAgentWindows",
    "location": "eastus",
    "name": "DependencyAgentWindows",
    "namePropertiesInstanceViewName": "DependencyAgentWindows",
```

## <a name="remove-an-installed-extension"></a>설치 된 확장 제거

Arc 사용 서버에서 설치 된 VM 확장을 제거 하려면, 및 매개 변수와 함께 [az connectedmachine delete](/cli/azure/ext/connectedmachine/connectedmachine/machine-extension#ext_connectedmachine_az_connectedmachine_machine_extension_delete) 를 사용 `--extension-name` `--machine-name` `--resource-group` 합니다.

예를 들어 Linux 용 Log Analytics VM 확장을 제거 하려면 다음 명령을 실행 합니다.

```azurecli
az connectedmachine machine-extension delete --machine-name "myMachineName" --name "OmsAgentforLinux" --resource-group "myResourceGroup"
```

## <a name="next-steps"></a>다음 단계

- [Azure PowerShell](manage-vm-extensions-powershell.md), [Azure Portal](manage-vm-extensions-portal.md)또는 [AZURE RESOURCE MANAGER 템플릿을](manage-vm-extensions-template.md)사용 하 여 VM 확장을 배포, 관리 및 제거할 수 있습니다.

- 문제 해결 정보는 [VM 확장 문제 해결 가이드](troubleshoot-vm-extensions.md)에서 찾을 수 있습니다.
