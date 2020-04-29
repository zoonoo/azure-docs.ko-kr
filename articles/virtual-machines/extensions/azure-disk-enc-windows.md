---
title: Windows용 Azure Disk Encryption
description: 가상 머신 확장을 사용하여 Azure Disk Encryption을 Windows 가상 머신에 배포합니다.
services: virtual-machines-windows
documentationcenter: ''
author: ejarvi
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/19/2020
ms.author: ejarvi
ms.openlocfilehash: e975e1757b77b4aab52a59d1f0709ef9cadae94e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80066860"
---
# <a name="azure-disk-encryption-for-windows-microsoftazuresecurityazurediskencryption"></a>Windows용 Azure Disk Encryption(Microsoft.Azure.Security.AzureDiskEncryption)

## <a name="overview"></a>개요

Azure Disk Encryption은 BitLocker를 활용하여 Windows를 실행하는 Azure 가상 머신에서 전체 디스크 암호화를 제공합니다.  이 솔루션은 Azure Key Vault와 통합되어 키 자격 증명 모음 구독의 디스크 암호화 키와 비밀을 관리합니다. 

## <a name="prerequisites"></a>사전 요구 사항

필수 조건에 대 한 전체 목록은 [Windows vm에 대 한 Azure Disk Encryption](../windows/disk-encryption-overview.md), 특히 다음 섹션을 참조 하십시오.

- [지원 되는 Vm 및 운영 체제](../windows/disk-encryption-overview.md#supported-vms-and-operating-systems)
- [네트워킹 요구 사항](../windows/disk-encryption-overview.md#networking-requirements)
- [그룹 정책 요구 사항](../windows/disk-encryption-overview.md#group-policy-requirements)

## <a name="extension-schema"></a>확장 스키마

Azure Disk Encryption (ADE)에는 두 가지 버전의 확장 스키마가 있습니다.
- v. 2.2-AAD (Azure Active Directory) 속성을 사용 하지 않는 최신 권장 스키마입니다.
- v. 1.1-AAD (Azure Active Directory) 속성이 필요한 이전 스키마입니다. 

대상 스키마를 선택 하려면 `typeHandlerVersion` 속성을 사용 하려는 스키마 버전과 동일 하 게 설정 해야 합니다.

### <a name="schema-v22-no-aad-recommended"></a>Schema v 2.2: AAD 없음 (권장)

V 2.2 스키마는 모든 새 Vm에 권장 되며 Azure Active Directory 속성이 필요 하지 않습니다.

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
        "publisher": "Microsoft.Azure.Security",
        "type": "AzureDiskEncryption",
        "typeHandlerVersion": "2.2",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "EncryptionOperation": "[encryptionOperation]",
          "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
          "KeyVaultURL": "[keyVaultURL]",
          "KekVaultResourceId": "[keyVaultResourceID]",
          "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
          "KeyVaultResourceId": "[keyVaultResourceID]",
          "SequenceVersion": "sequenceVersion]",
          "VolumeType": "[volumeType]"
        }
  }
}
```


### <a name="schema-v11-with-aad"></a>Schema v1.0: AAD 사용 

1.1 스키마에는 `aadClientID` 및 중 `aadClientSecret` 하나가 `AADClientCertificate` 필요 하며 새 vm에는 권장 되지 않습니다.

`aadClientSecret`사용:

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientSecret": "[aadClientSecret]"
    },    
    "publisher": "Microsoft.Azure.Security",
    "type": "AzureDiskEncryption",
    "typeHandlerVersion": "1.1",
    "settings": {
      "AADClientID": "[aadClientID]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "KekVaultResourceId": "[keyVaultResourceID]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    }
  }
}
```

`AADClientCertificate`사용:

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientCertificate": "[aadClientCertificate]"
    },    
    "publisher": "Microsoft.Azure.Security",
    "type": "AzureDiskEncryption",
    "typeHandlerVersion": "1.1",
    "settings": {
      "AADClientID": "[aadClientID]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "KekVaultResourceId": "[keyVaultResourceID]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    }
  }
}
```


### <a name="property-values"></a>속성 값

| 속성 | 값/예제 | 데이터 형식 |
| ---- | ---- | ---- |
| apiVersion | 2019-07-01 | date |
| 게시자 | Microsoft.Azure.Security | string |
| type | 용 azurediskencryption | string |
| typeHandlerVersion | 2.2, 1.1 | string |
| (1.1 스키마) AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | guid | 
| (1.1 스키마) AADClientSecret | password | string |
| (1.1 스키마) AADClientCertificate | thumbprint | string |
| EncryptionOperation | EnableEncryption, EnableEncryptionFormatAll | string | 
| (옵션-기본 RSA-OAEP) KeyEncryptionAlgorithm | 'RSA-OAEP', 'RSA-OAEP-256', 'RSA1_5' | string |
| KeyVaultURL | url | string |
| KeyVaultResourceId | url | string |
| 필드 하면 keyencryptionkeyurl | url | string |
| 필드 KekVaultResourceId | url | string |
| 필드 SequenceVersion | uniqueidentifier | string |
| VolumeType | OS, Data, All | string |

## <a name="template-deployment"></a>템플릿 배포

Schema v 2.2를 기반으로 하는 템플릿 배포의 예는 Azure 빠른 시작 템플릿 [201-실행-windows-vm-aad 없음](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad)을 참조 하세요.

스키마 v 1.1을 기반으로 하는 템플릿 배포의 예는 Azure 빠른 시작 템플릿 [201-암호화-실행-windows-vm](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm)을 참조 하세요.

>[!NOTE]
> 또한 매개 `VolumeType` 변수를 All로 설정 하는 경우 데이터 디스크는 형식이 올바르게 지정 된 경우에만 암호화 됩니다. 

## <a name="troubleshoot-and-support"></a>문제 해결 및 지원

### <a name="troubleshoot"></a>문제 해결

문제 해결 방법은 [Azure Disk Encryption 문제 해결 가이드](../windows/disk-encryption-troubleshooting.md)를 참조하세요.

### <a name="support"></a>Support(지원)

이 문서의 어느 시점에서 든 도움이 필요한 경우 [MSDN azure 및 Stack Overflow 포럼](https://azure.microsoft.com/support/community/)에서 Azure 전문가에 게 문의할 수 있습니다. 

또는 Azure 기술 지원 인시던트를 제출할 수 있습니다. [Azure 지원](https://azure.microsoft.com/support/options/) 으로 이동 하 여 지원 받기를 선택 합니다. Azure 지원 사용에 대 한 자세한 내용은 [Microsoft Azure 지원 FAQ](https://azure.microsoft.com/support/faq/)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

* 확장에 대한 자세한 내용은 [Windows용 가상 머신 확장 및 기능](features-windows.md)을 참조하세요.
* Windows Azure Disk Encryption에 대 한 자세한 내용은 [windows virtual machines](../../security/fundamentals/azure-disk-encryption-vms-vmss.md#windows-virtual-machines)를 참조 하세요.
