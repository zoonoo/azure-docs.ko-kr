---
title: Windows용 Key Vault VM 확장 | Microsoft Docs
description: 가상 머신 확장을 사용하여 가상 머신에 Key Vault 비밀의 자동 새로 고침을 수행하는 에이전트를 배포합니다.
services: virtual-machines-windows
documentationcenter: ''
author: dragav
manager: jeconnoc
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/14/2018
ms.author: dragosav
ms.openlocfilehash: 19e177f086ea9fa817a36e67ace77aed39ee89b5
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
ms.locfileid: "33944755"
---
# <a name="key-vault-virtual-machine-extension-for-windows"></a>Windows용 Key Vault 가상 머신 확장

## <a name="overview"></a>개요

Key Vault VM 확장은 Azure Key Vault에 저장된 비밀의 자동 새로 고침을 제공합니다. 특히 확장은 키 자격 증명 모음에 저장된 인증서에 대해 관찰된 목록을 모니터링하고, 변경 내용이 검색되면 해당 인증서를 검색 및 설치합니다. Microsoft는 현재 Windows VM에 대한 Key Vault VM 확장을 게시하고 지원합니다. 이 문서에서는 Windows용 Key Vault VM 확장에 지원되는 플랫폼, 구성 및 배포 옵션에 대해 자세히 설명합니다. 

## <a name="prerequisites"></a>필수 조건

Key Vault VM 확장은 Key Vault 서비스에 대한 자체 인증을 위해 관리 서비스 ID VM 확장에 따라 달라집니다. 자세한 내용은 MSI VM 확장에 대한 설명서를 참조하세요.

### <a name="operating-system"></a>운영 체제

Key Vault VM 확장은 현재 모든 Windows 버전을 지원합니다.

| 배포 | 버전 |
|---|---|
| Windows | 코어 |

### <a name="internet-connectivity"></a>인터넷 연결

Windows용 Key Vault VM 확장을 사용하려면 대상 가상 머신이 인터넷에 연결되어 있어야 합니다. 

## <a name="extension-schema"></a>확장 스키마

다음 JSON은 Key Vault VM 확장에 대한 스키마를 보여 줍니다. 확장에는 protected 설정이 필요하지 않습니다. 모든 설정은 보안의 영향을 받지 않는 정보로 간주됩니다. 확장에는 모니터링되는 비밀 목록, 폴링 빈도 및 대상 인증서 저장소가 필요합니다. 구체적으로 살펴보면 다음과 같습니다.  

```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KVVMExtensionForWindows",
      "apiVersion": "2016-10-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
            "publisher": "Microsoft.Azure.KeyVault.Edp",
            "type": "KeyVaultForWindows",
            "typeHandlerVersion": "0.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "secretsManagementSettings": {
                    "pollingIntervalInS": <polling interval in seconds>,
                    "certificateStoreName": <certificate store name, e.g.: "MY">,
                    "certificateStoreLocation": <certificate store location, e.g.: "LOCAL_MACHINE">,
                    "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net:443/secrets/mycertificate"
                }         
            }
      }
    }
```

### <a name="property-values"></a>속성 값

| Name | 값/예제 | 데이터 형식 |
| ---- | ---- | ---- |
| apiVersion | 2016-10-01 | date |
| publisher | Microsoft.Azure.KeyVault.Edp | string |
| 형식 | KeyVaultForWindows | string |
| typeHandlerVersion | 0.0 | int |
| pollingIntervalInS | 3600 | int |
| certificateStoreName | MY | string |
| certificateStoreLocation  | LOCAL_MACHINE | string |
| observedCertificates  | ["https://myvault.vault.azure.net:443/secrets/mycertificate"] | 문자열 배열


## <a name="template-deployment"></a>템플릿 배포

Azure Resource Manager 템플릿을 사용하여 Azure VM 확장을 배포할 수 있습니다. 배포 후에 인증서를 새로 고칠 필요가 있는 하나 이상의 가상 머신을 배포하는 경우 템플릿을 사용하는 것이 좋습니다. 확장은 개별 VM 또는 VM 확장 집합에 배포할 수 있습니다. 스키마와 구성은 두 템플릿 형식 모두에 공통적으로 적용됩니다. 

가상 머신 확장에 대한 JSON 구성은 템플릿의 가상 머신 리소스 조각, 특히 가상 머신의 `"resources": []` 개체 내에 중첩되어야 합니다.

```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KVVMExtensionForWindows",
      "apiVersion": "2016-10-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
            "publisher": "Microsoft.Azure.KeyVault.Edp",
            "type": "KeyVaultForWindows",
            "typeHandlerVersion": "0.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "secretsManagementSettings": {
                    "pollingIntervalInS": <polling interval in seconds>,
                    "certificateStoreName": <certificate store name, e.g.: "MY">,
                    "certificateStoreLocation": <certificate store location, e.g.: "LOCAL_MACHINE">,
                    "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net:443/secrets/mycertificate"
                }         
            }
      }
    }
```

## <a name="azure-powershell-deployment"></a>Azure PowerShell 배포

Azure PowerShell은 기존 가상 머신 또는 가상 머신 확장 집합에 Key Vault VM 확장을 배포하는 데 사용할 수 있습니다. 

* VM에 확장을 배포하려면 다음과 같습니다.
    
    ```powershell
        # Build settings
        $settings = '{"secretsManagementSettings": 
            { "pollingIntervalInS": "' + <pollingInterval> + 
            '", "certificateStoreName": "' + <certStoreName> + 
            '", "certificateStoreLocation": "' + <certStoreLoc> + 
            '", "observedCertificates": ["' + <observedCerts> + '"] } }'
        $extName = <VMName> + '/KeyVaultForWindows' 
        $extPublisher = "Microsoft.Azure.KeyVault.Edp"
        $extType = "KeyVaultForWindows"
    
        # Start the deployment
        Set-AzureRmVmExtension -VMName <VMName> -Name $extName -Publisher $extPublisher -Type $extType -SettingString $settings
    
    ```

* 예를 들어 VM 확장 집합에 확장을 Service Fabric 클러스터의 일부로 배포하려면 다음과 같습니다.

    ```powershell
    
        # Build settings
        $settings = '{"secretsManagementSettings": 
            { "pollingIntervalInS": "' + <pollingInterval> + 
            '", "certificateStoreName": "' + <certStoreName> + 
            '", "certificateStoreLocation": "' + <certStoreLoc> + 
            '", "observedCertificates": ["' + <observedCerts> + '"] } }'
        $extName = <VMSSName> + '_KeyVaultForWindows' 
        $extPublisher = "Microsoft.Azure.KeyVault.Edp"
        $extType = "KeyVaultForWindows"
    
        # Start the deployment
        Add-AzureRmVmssExtension -VirtualMachineScaleSet <VMSS> -Name $extName -Publisher $extPublisher -Type $extType -Setting $settings
    
    ```

다음 제한 사항/요구 사항에 주의하세요.
- 배포는 미국 중남부 지역에서 수행해야 합니다.
- Key Vault 제한 사항:
    - 배포 시점에 있어야 합니다. 
    - 배포와 동일한 지역 및 자원 그룹에 있어야 합니다.
    - 배포 및 템플릿 배포에 사용할 수 있어야 합니다.

## <a name="azure-cli-deployment"></a>Azure CLI 배포

Azure CLI에서 Key Vault VM 확장을 배포하기 위한 샘플은 곧 제공될 예정입니다. 

## <a name="troubleshoot-and-support"></a>문제 해결 및 지원

### <a name="troubleshoot"></a>문제 해결

확장 배포 상태에 대한 데이터는 Azure PowerShell 또는 Azure Portal을 통해 검색할 수 있습니다. 지정된 VM에 대한 확장의 배포 상태를 보려면 Azure PowerShell을 사용하여 다음 명령을 실행합니다.

```powershell
Get-AzureRMVMExtension -VMName <vmName> -ResourceGroupname <resource group name>
```

확장 실행 출력은 다음 파일에 기록됩니다.

```
%windrive%\WindowsAzure\Logs\Plugins\Microsoft.Azure.KeyVault.Edp.KeyVaultForWindows\<version>\akvvm_service_<date>.log
```


### <a name="support"></a>지원

이 문서의 어디에서든 도움이 필요한 경우 [MSDN Azure 및 Stack Overflow 포럼](https://azure.microsoft.com/support/forums/)에서 Azure 전문가에게 문의할 수 있습니다. 또는 Azure 기술 지원 인시던트를 제출할 수 있습니다. [Azure 지원 사이트](https://azure.microsoft.com/support/options/)로 가서 지원 받기를 선택합니다. Azure 지원을 사용하는 방법에 대한 자세한 내용은 [Microsoft Azure 지원 FAQ](https://azure.microsoft.com/support/faq/)를 참조하세요.
