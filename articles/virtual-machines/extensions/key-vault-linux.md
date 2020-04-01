---
title: 리눅스에 대 한 Azure 키 볼트 VM 확장
description: 가상 시스템 확장을 사용하여 가상 시스템에 Key Vault 인증서의 자동 새로 고침을 수행하는 에이전트를 배포합니다.
services: virtual-machines-linux
author: msmbaldwin
tags: keyvault
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 12/02/2019
ms.author: mbaldwin
ms.openlocfilehash: add2d515e4f8e8c56a98a7292e137e601332d10c
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80410876"
---
# <a name="key-vault-virtual-machine-extension-for-linux"></a>리눅스에 대 한 키 볼트 가상 머신 확장

키 볼트 VM 확장은 Azure 키 자격 증명 모음에 저장된 인증서를 자동으로 새로 고칩니다. 특히 확장은 키 자격 증명 모음에 저장된 관찰된 인증서 목록을 모니터링합니다.  변경 을 감지하면 확장이 해당 인증서를 검색하고 설치합니다. 키 볼트 VM 확장 게시 및 마이크로소프트에 의해 지원, 현재 리눅스 VM에. 이 문서에서는 Linux용 키 볼트 VM 확장을 위해 지원되는 플랫폼, 구성 및 배포 옵션에 대해 자세히 설명합니다. 

### <a name="operating-system"></a>운영 체제

키 볼트 VM 확장 은 이러한 리눅스 배포판을 지원합니다:

- 우분투-1604
- 우분투-1804
- 데비안-9
- 수세-15 

### <a name="supported-certificate-content-types"></a>지원되는 인증서 콘텐츠 유형

- PKCS #12
- Pem

## <a name="extension-schema"></a>확장 스키마

다음 JSON은 Key Vault VM 확장에 대한 스키마를 보여 줍니다. 확장에는 protected 설정이 필요하지 않습니다. 모든 설정은 보안의 영향을 받지 않는 정보로 간주됩니다. 확장에는 모니터링되는 비밀 목록, 폴링 빈도 및 대상 인증서 저장소가 필요합니다. 특히 다음에 대한 내용을 설명합니다.  
```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KVVMExtensionForLinux",
      "apiVersion": "2019-07-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForLinux",
      "typeHandlerVersion": "1.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "secretsManagementSettings": {
          "pollingIntervalInS": <polling interval in seconds, e.g. "3600">,
          "certificateStoreName": <certificate store name, e.g.: "MY">,
          "linkOnRenewal": <Not available on Linux e.g.: false>,
          "certificateStoreLocation": <certificate store location, currently it works locally only e.g.: "LocalMachine">,
          "requireInitialSync": <initial synchronization of certificates e..g: true>,
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net/secrets/mycertificate"
        }      
      }
      }
    }
```

> [!NOTE]
> 관찰된 인증서 URL은 양식이어야 `https://myVaultName.vault.azure.net/secrets/myCertName`합니다.
> 
> 경로는 `/secrets` 개인 키를 포함한 전체 인증서를 반환하지만 `/certificates` 경로는 반환하지 않기 때문입니다. 인증서에 대한 자세한 내용은 여기에서 확인할 수 [있습니다.](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-certificates)


### <a name="property-values"></a>속성 값

| 속성 | 값/예제 | 데이터 형식 |
| ---- | ---- | ---- |
| apiVersion | 2019-07-01 | date |
| publisher | Microsoft.Azure.KeyVault | 문자열 |
| type | 키볼트포리눅스 | 문자열 |
| typeHandlerVersion | 1.0 | int |
| pollingIntervalInS | 3600 | 문자열 |
| certificateStoreName | MY | 문자열 |
| 링크온리뉴얼 | false | boolean |
| certificateStoreLocation  | LocalMachine | 문자열 |
| 필수초기동기화 | true | boolean |
| observedCertificates  | ["https://myvault.vault.azure.net/secrets/mycertificate"] | 문자열 배열


## <a name="template-deployment"></a>템플릿 배포

Azure Resource Manager 템플릿을 사용하여 Azure VM 확장을 배포할 수 있습니다. 배포 후에 인증서를 새로 고칠 필요가 있는 하나 이상의 가상 머신을 배포하는 경우 템플릿을 사용하는 것이 좋습니다. 확장은 개별 VM 또는 가상 시스템 규모 집합에 배포할 수 있습니다. 스키마와 구성은 두 템플릿 형식 모두에 공통적으로 적용됩니다. 

가상 시스템 확장을 위한 JSON 구성은 템플릿의 가상 컴퓨터 리소스 조각 `"resources": []` 내부에 중첩되어야 하며, 특히 가상 컴퓨터 `"virtualMachineProfile":"extensionProfile":{"extensions" :[]` 템플릿에 대한 개체와 개체 아래에 설정된 가상 시스템 축척의 경우 개체입니다.

```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KeyVaultForLinux",
      "apiVersion": "2019-07-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForLinux",
      "typeHandlerVersion": "1.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
          "secretsManagementSettings": {
          "pollingIntervalInS": <polling interval in seconds, e.g. "3600">,
          "certificateStoreName": <certificate store name, e.g.: "MY">,
          "certificateStoreLocation": <certificate store location, currently it works locally only e.g.: "LocalMachine">,
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net/secrets/mycertificate"
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
        $extName =  "KeyVaultForLinux"
        $extPublisher = "Microsoft.Azure.KeyVault"
        $extType = "KeyVaultForLinux"
       
    
        # Start the deployment
        Set-AzVmExtension -TypeHandlerVersion "1.0" -ResourceGroupName <ResourceGroupName> -Location <Location> -VMName <VMName> -Name $extName -Publisher $extPublisher -Type $extType -SettingString $settings
    
    ```

* 가상 시스템 규모 집합에 확장을 배포하려면 다음을 수행합니다.

    ```powershell
    
        # Build settings
        $settings = '{"secretsManagementSettings": 
        { "pollingIntervalInS": "' + <pollingInterval> + 
        '", "certificateStoreName": "' + <certStoreName> + 
        '", "certificateStoreLocation": "' + <certStoreLoc> + 
        '", "observedCertificates": ["' + <observedCerts> + '"] } }'
        $extName = "KeyVaultForLinux"
        $extPublisher = "Microsoft.Azure.KeyVault"
        $extType = "KeyVaultForLinux"
        
        # Add Extension to VMSS
        $vmss = Get-AzVmss -ResourceGroupName <ResourceGroupName> -VMScaleSetName <VmssName>
        Add-AzVmssExtension -VirtualMachineScaleSet $vmss  -Name $extName -Publisher $extPublisher -Type $extType -TypeHandlerVersion "1.0" -Setting $settings

        # Start the deployment
        Update-AzVmss -ResourceGroupName <ResourceGroupName> -VMScaleSetName <VmssName> -VirtualMachineScaleSet $vmss 
    
    ```

## <a name="azure-cli-deployment"></a>Azure CLI 배포

Azure CLI를 사용하여 키 볼트 VM 확장을 기존 가상 시스템 또는 가상 시스템 규모 집합에 배포할 수 있습니다. 
 
* VM에 확장을 배포하려면 다음과 같습니다.
    
    ```azurecli
       # Start the deployment
         az vm extension set -n "KeyVaultForLinux" `
         --publisher Microsoft.Azure.KeyVault `
         -g "<resourcegroup>" `
         --vm-name "<vmName>" `
         --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\ <observedCerts>\"] }}'
    ```

* 가상 시스템 규모 집합에 확장을 배포하려면 다음을 수행합니다.

   ```azurecli
        # Start the deployment
        az vmss extension set -n "KeyVaultForLinux" `
        --publisher Microsoft.Azure.KeyVault `
        -g "<resourcegroup>" `
        --vm-name "<vmName>" `
        --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\ <observedCerts>\"] }}'
    ```

다음 제한 사항/요구 사항에 주의하세요.
- Key Vault 제한 사항:
  - 배포 시점에 있어야 합니다. 
  - MSI를 사용 하 여 VM/VMSS ID에 대 한 키 볼트 액세스 정책 설정 됩니다.


## <a name="troubleshoot-and-support"></a>문제 해결 및 지원

### <a name="troubleshoot"></a>문제 해결

확장 배포 상태에 대한 데이터는 Azure PowerShell 또는 Azure Portal을 통해 검색할 수 있습니다. 지정된 VM에 대한 확장의 배포 상태를 보려면 Azure PowerShell을 사용하여 다음 명령을 실행합니다.

## <a name="azure-powershell"></a>Azure PowerShell
```powershell
Get-AzVMExtension -VMName <vmName> -ResourceGroupname <resource group name>
```

## <a name="azure-cli"></a>Azure CLI
```azurecli
 az vm get-instance-view --resource-group <resource group name> --name  <vmName> --query "instanceView.extensions"
```

### <a name="support"></a>고객 지원팀

이 문서의 어느 시점에서든 도움이 필요한 경우 [MSDN Azure 및 스택 오버플로 포럼의](https://azure.microsoft.com/support/forums/)Azure 전문가에게 문의할 수 있습니다. 또는 Azure 기술 지원 인시던트를 제출할 수 있습니다. [Azure 지원 사이트로](https://azure.microsoft.com/support/options/) 이동하여 지원 받기를 선택합니다. Azure 지원을 사용하는 방법에 대한 자세한 내용은 [Microsoft Azure 지원 FAQ](https://azure.microsoft.com/support/faq/)를 참조하세요.
