---
title: Azure 디스크 암호화 및 Azure 가상 시스템 규모 설정 확장 시퀀싱
description: 이 문서에서는 Linux IaaS VM용 Microsoft Azure Disk Encryption을 사용하도록 설정하는 방법에 대한 지침을 제공합니다.
author: msmbaldwin
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 10/10/2019
ms.openlocfilehash: aa638b86b0788b8c274f9dcb3c04c1fc385b4ae1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76279022"
---
# <a name="use-azure-disk-encryption-with-virtual-machine-scale-set-extension-sequencing"></a>가상 머신 확장 집합 확장 시퀀싱을 통한 Azure Disk Encryption 사용

Azure 디스크 암호화와 같은 확장을 지정된 순서로 설정된 Azure 가상 컴퓨터 규모에 추가할 수 있습니다. 이렇게 하려면 [확장 시퀀싱을](virtual-machine-scale-sets-extension-sequencing.md)사용합니다. 

일반적으로 암호화는 디스크에 적용되어야 합니다.

- 디스크 또는 볼륨을 준비하는 확장 또는 사용자 지정 스크립트 후.
- 암호화된 디스크 또는 볼륨의 데이터에 액세스하거나 사용하는 확장 또는 사용자 지정 스크립트 이전.

두 경우 모두 `provisionAfterExtensions` 속성은 시퀀스의 나중에 추가할 확장을 지정합니다.

## <a name="sample-azure-templates"></a>Azure 템플릿 샘플

다른 확장 후에 Azure 디스크 암호화를 적용하려면 `provisionAfterExtensions` AzureDisk암호화 확장 블록에 속성을 넣습니다. 

다음은 "사용자 정의 스크립트 확장"을 사용 하 여 예제, "AzureDiskEncryption"다음Windows 디스크를 초기화 하 고 서식을 하는 Powershell 스크립트:

```json
"virtualMachineProfile": {
  "extensionProfile": {
    "extensions": [
      {
        "type": "Microsoft.Compute/virtualMachineScaleSets/extensions",
        "name": "CustomScriptExtension",
        "location": "[resourceGroup().location]",
        "properties": {
          "publisher": "Microsoft.Compute",
          "type": "CustomScriptExtension",
          "typeHandlerVersion": "1.9",
          "autoUpgradeMinorVersion": true,
          "forceUpdateTag": "[parameters('forceUpdateTag')]",
          "settings": {
            "fileUris": [
              "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/ade-vmss/FormatMBRDisk.ps1"
            ]
          },
          "protectedSettings": {
           "commandToExecute": "powershell -ExecutionPolicy Unrestricted -File FormatMBRDisk.ps1"
          }
        }
      },
      {
        "type": "Microsoft.Compute/virtualMachineScaleSets/extensions",
        "name": "AzureDiskEncryption",
        "location": "[resourceGroup().location]",
        "properties": {
          "provisionAfterExtensions": [
            "CustomScriptExtension"
          ],
          "publisher": "Microsoft.Azure.Security",
          "type": "AzureDiskEncryption",
          "typeHandlerVersion": "2.2",
          "autoUpgradeMinorVersion": true,
          "forceUpdateTag": "[parameters('forceUpdateTag')]",
          "settings": {
            "EncryptionOperation": "EnableEncryption",
            "KeyVaultURL": "[reference(variables('keyVaultResourceId'),'2018-02-14-preview').vaultUri]",
            "KeyVaultResourceId": "[variables('keyVaultResourceID')]",
            "KeyEncryptionKeyURL": "[parameters('keyEncryptionKeyURL')]",
            "KekVaultResourceId": "[variables('keyVaultResourceID')]",
            "KeyEncryptionAlgorithm": "[parameters('keyEncryptionAlgorithm')]",
            "VolumeType": "[parameters('volumeType')]",
            "SequenceVersion": "[parameters('sequenceVersion')]"
          }
        }
      },
    ]
  }
}
```

다른 확장 전에 Azure 디스크 암호화를 적용하려면 `provisionAfterExtensions` 속성을 다음 확장의 블록에 넣습니다.

다음은 "AzureDiskEncryption"을 사용하는 예이며 Windows 기반 Azure VM에서 모니터링 및 진단 기능을 제공하는 확장인 "VMDiagnosticsSettings"이 있습니다.


```json
"virtualMachineProfile": {
  "extensionProfile": {
    "extensions": [
      {
        "name": "AzureDiskEncryption",
        "type": "Microsoft.Compute/virtualMachineScaleSets/extensions",
        "location": "[resourceGroup().location]",
        "properties": {
          "publisher": "Microsoft.Azure.Security",
          "type": "AzureDiskEncryption",
          "typeHandlerVersion": "2.2",
          "autoUpgradeMinorVersion": true,
          "forceUpdateTag": "[parameters('forceUpdateTag')]",
          "settings": {
            "EncryptionOperation": "EnableEncryption",
            "KeyVaultURL": "[reference(variables('keyVaultResourceId'),'2018-02-14-preview').vaultUri]",
            "KeyVaultResourceId": "[variables('keyVaultResourceID')]",
            "KeyEncryptionKeyURL": "[parameters('keyEncryptionKeyURL')]",
            "KekVaultResourceId": "[variables('keyVaultResourceID')]",
            "KeyEncryptionAlgorithm": "[parameters('keyEncryptionAlgorithm')]",
            "VolumeType": "[parameters('volumeType')]",
            "SequenceVersion": "[parameters('sequenceVersion')]"
          }
        }
      },
      { 
        "name": "Microsoft.Insights.VMDiagnosticsSettings", 
        "type": "extensions", 
        "location": "[resourceGroup().location]", 
        "apiVersion": "2016-03-30", 
        "dependsOn": [ 
          "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]" 
        ], 
        "properties": { 
          "provisionAfterExtensions": [
            "AzureDiskEncryption"
          ],
        "publisher": "Microsoft.Azure.Diagnostics", 
          "type": "IaaSDiagnostics", 
          "typeHandlerVersion": "1.5", 
          "autoUpgradeMinorVersion": true, 
          "settings": { 
            "xmlCfg": "[base64(concat(variables('wadcfgxstart'), 
            variables('wadmetricsresourceid'), 
            concat('myVM', copyindex()),
            variables('wadcfgxend')))]", 
            "storageAccount": "[variables('storageName')]" 
          }, 
          "protectedSettings": { 
            "storageAccountName": "[variables('storageName')]", 
            "storageAccountKey": "[listkeys(variables('accountid'), 
              '2015-06-15').key1]", 
            "storageAccountEndPoint": "https://core.windows.net" 
          } 
        } 
      },
    ]
  }
}
```

자세한 템플릿은 다음을 참조하십시오.
* 디스크(Linux)를 포맷하는 사용자 지정 셸 스크립트 다음 Azure 디스크 암호화 확장 적용: [deploy-extseq-linux-ADE-after-customscript.json](https://github.com/Azure-Samples/compute-automation-configurations/blob/master/ade-vmss/deploy-extseq-linux-ADE-after-customscript.json)


## <a name="next-steps"></a>다음 단계
- 확장 시퀀싱에 대해 자세히 알아보기: [가상 시스템 크기 집합의 시퀀스 확장 프로비저닝.](virtual-machine-scale-sets-extension-sequencing.md)
- 속성에 `provisionAfterExtensions` 대해 자세히 알아보기: [Microsoft.Compute 가상머신스케일Sets/확장 템플릿 참조](/azure/templates/microsoft.compute/2018-10-01/virtualmachinescalesets/extensions).
- [가상 머신 확장 집합을 위한 Azure Disk Encryption](disk-encryption-overview.md)
- [Azure CLI를 사용하는 가상 머신 확장 집합 암호화](disk-encryption-cli.md)
- [Azure PowerShell을 사용하는 가상 머신 확장 집합 암호화](disk-encryption-powershell.md)
- [Azure Disk Encryption을 위한 Key Vault 만들기 및 구성](disk-encryption-key-vault.md)
