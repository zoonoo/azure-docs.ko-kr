---
title: Azure Disk Encryption 및 Azure 가상 머신 확장 집합 확장 시퀀싱
description: 이 문서에서는 Linux IaaS VM용 Microsoft Azure Disk Encryption을 사용하도록 설정하는 방법에 대한 지침을 제공합니다.
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 10/10/2019
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 1c93359486379ecfc8bf6df1f29978ba369f551a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "83117260"
---
# <a name="use-azure-disk-encryption-with-virtual-machine-scale-set-extension-sequencing"></a>가상 머신 확장 집합 확장 시퀀싱을 통한 Azure Disk Encryption 사용

Azure disk encryption과 같은 확장은 지정 된 순서에 따라 Azure 가상 머신 확장 집합에 추가할 수 있습니다. 이렇게 하려면 [확장 시퀀싱](virtual-machine-scale-sets-extension-sequencing.md)을 사용 합니다. 

일반적으로 디스크에는 암호화를 적용 해야 합니다.

- 디스크 또는 볼륨을 준비 하는 확장 또는 사용자 지정 스크립트
- 암호화 된 디스크 또는 볼륨에서 데이터를 액세스 하거나 사용 하는 확장 또는 사용자 지정 스크립트

어떤 경우 든 속성은 `provisionAfterExtensions` 시퀀스에서 나중에 추가 해야 하는 확장을 지정 합니다.

## <a name="sample-azure-templates"></a>샘플 Azure 템플릿

다른 확장 후에 Azure Disk Encryption 적용 하려면 `provisionAfterExtensions` AzureDiskEncryption 확장 블록에 속성을 추가 합니다. 

다음은 Windows 디스크를 초기화 하 고 포맷 한 다음 "AzureDiskEncryption"을 사용 하는 Powershell 스크립트인 "CustomScriptExtension"을 사용 하는 예입니다.

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

다른 확장 이전에 Azure Disk Encryption 적용 하려면 `provisionAfterExtensions` 다음에 사용할 확장의 블록에 속성을 추가 합니다.

Windows 기반 Azure VM에서 모니터링 및 진단 기능을 제공 하는 확장인 "AzureDiskEncryption" 다음에 "Microsoft.insights.vmdiagnosticssettings로"를 사용 하는 예제는 다음과 같습니다.


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

자세한 내용은 다음을 참조 하세요.
* 디스크를 포맷 하는 사용자 지정 셸 스크립트 뒤에 Azure Disk Encryption 확장 적용 (Linux): [deploy-extseq-linux-ADE-after-customscript.js](https://github.com/Azure-Samples/compute-automation-configurations/blob/master/ade-vmss/deploy-extseq-linux-ADE-after-customscript.json)


## <a name="next-steps"></a>다음 단계
- 확장 시퀀싱에 대 한 자세한 내용은 [가상 머신 확장 집합의 시퀀스 확장 프로 비전을](virtual-machine-scale-sets-extension-sequencing.md)확인 하세요.
- 속성에 대 한 자세한 내용은 `provisionAfterExtensions` [virtualMachineScaleSets/extensions 템플릿 참조를 참조](/azure/templates/microsoft.compute/2018-10-01/virtualmachinescalesets/extensions)하세요.
- [가상 머신 확장 집합을 위한 Azure Disk Encryption](disk-encryption-overview.md)
- [Azure CLI를 사용하는 가상 머신 확장 집합 암호화](disk-encryption-cli.md)
- [Azure PowerShell을 사용하는 가상 머신 확장 집합 암호화](disk-encryption-powershell.md)
- [Azure Disk Encryption을 위한 Key Vault 만들기 및 구성](disk-encryption-key-vault.md)
