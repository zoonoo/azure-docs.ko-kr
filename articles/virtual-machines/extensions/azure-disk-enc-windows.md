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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066860"
---
# <a name="azure-disk-encryption-for-windows-microsoftazuresecurityazurediskencryption"></a>Windows용 Azure Disk Encryption(Microsoft.Azure.Security.AzureDiskEncryption)

## <a name="overview"></a>개요

Azure Disk Encryption은 BitLocker를 활용하여 Windows를 실행하는 Azure 가상 머신에서 전체 디스크 암호화를 제공합니다.  이 솔루션은 Azure Key Vault와 통합되어 키 자격 증명 모음 구독의 디스크 암호화 키와 비밀을 관리합니다. 

## <a name="prerequisites"></a>사전 요구 사항

필수 구성 프로그램의 전체 목록은 [Windows VM에 대한 Azure 디스크 암호화,](../windows/disk-encryption-overview.md)특히 다음 섹션을 참조하십시오.

- [지원되는 VM 및 운영 체제](../windows/disk-encryption-overview.md#supported-vms-and-operating-systems)
- [네트워킹 요구 사항](../windows/disk-encryption-overview.md#networking-requirements)
- [그룹 정책 요구 사항](../windows/disk-encryption-overview.md#group-policy-requirements)

## <a name="extension-schema"></a>확장 스키마

Azure 디스크 암호화(ADE)에 대한 두 가지 확장 스키마 버전이 있습니다.
- v2.2 - Azure Active Directory(AAD) 속성을 사용하지 않는 최신 권장 스키마입니다.
- v1.1 - Azure Active Directory(AAD) 속성이 필요한 이전 스키마입니다. 

대상 스키마를 선택하려면 `typeHandlerVersion` 속성을 사용하려는 스키마 버전과 동일하게 설정해야 합니다.

### <a name="schema-v22-no-aad-recommended"></a>스키마 v2.2: AAD 없음(권장)

v2.2 스키마는 모든 새 VM에 권장되며 Azure Active Directory 속성이 필요하지 않습니다.

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


### <a name="schema-v11-with-aad"></a>스키마 v1.1: AAD 

1.1 스키마는 `aadClientID` 새 `aadClientSecret` VM을 필요로 하거나 `AADClientCertificate` 사용하지 않는 것이 좋습니다.

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

| 이름 | 값/예제 | 데이터 형식 |
| ---- | ---- | ---- |
| apiVersion | 2019-07-01 | date |
| publisher | Microsoft.Azure.Security | 문자열 |
| type | AzureDisk암호화 | 문자열 |
| typeHandlerVersion | 2.2, 1.1 | 문자열 |
| (1.1 스키마) AAD클라이언트ID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | guid | 
| (1.1 스키마) AAD클라이언트시크릿 | password | 문자열 |
| (1.1 스키마) AAD클라이언트인증서 | thumbprint | 문자열 |
| EncryptionOperation | EnableEncryption, EnableEncryptionFormatAll | 문자열 | 
| (선택 사항 - 기본 RSA-OAEP) 키 암호화 알고리즘 | 'RSA-OAEP', 'RSA-OAEP-256', 'RSA1_5' | 문자열 |
| KeyVaultURL | url | 문자열 |
| 키볼트자원 | url | 문자열 |
| (선택 사항) 키 암호화키URL | url | 문자열 |
| (선택 사항) 케크볼트자원하이드 | url | 문자열 |
| (선택 사항) 시퀀스버전 | uniqueidentifier | 문자열 |
| VolumeType | OS, Data, All | 문자열 |

## <a name="template-deployment"></a>템플릿 배포

스키마 v2.2를 기반으로 하는 템플릿 배포의 예는 Azure QuickStart 템플릿 [201-암호화 실행-windows-vm-aad 를](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad)참조하십시오.

스키마 v1.1을 기반으로 하는 템플릿 배포의 예는 Azure QuickStart 템플릿 [201-암호화 실행-windows-vm](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm)을 참조하십시오.

>[!NOTE]
> 또한 `VolumeType` 매개 변수가 모두로 설정된 경우 데이터 디스크는 올바르게 포맷된 경우에만 암호화됩니다. 

## <a name="troubleshoot-and-support"></a>문제 해결 및 지원

### <a name="troubleshoot"></a>문제 해결

문제 해결 방법은 [Azure Disk Encryption 문제 해결 가이드](../windows/disk-encryption-troubleshooting.md)를 참조하세요.

### <a name="support"></a>고객 지원팀

이 문서의 어느 시점에서든 도움이 필요한 경우 [MSDN Azure 및 스택 오버플로 포럼의](https://azure.microsoft.com/support/community/)Azure 전문가에게 문의할 수 있습니다. 

또는 Azure 기술 지원 인시던트를 제출할 수 있습니다. Azure [지원으로](https://azure.microsoft.com/support/options/) 이동하여 지원 받기를 선택합니다. Azure 지원 사용에 대한 자세한 내용은 [Microsoft Azure 지원 FAQ를](https://azure.microsoft.com/support/faq/)참조하십시오.

## <a name="next-steps"></a>다음 단계

* 확장에 대한 자세한 내용은 [Windows용 가상 머신 확장 및 기능](features-windows.md)을 참조하세요.
* Windows용 Azure 디스크 암호화에 대한 자세한 내용은 [Windows 가상 컴퓨터를](../../security/fundamentals/azure-disk-encryption-vms-vmss.md#windows-virtual-machines)참조하십시오.
