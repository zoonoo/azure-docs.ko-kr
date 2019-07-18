---
title: Linux용 Azure Disk Encryption | Microsoft Docs
description: 가상 머신 확장을 사용하여 Linux용 Azure Disk Encryption을 가상 머신에 배포합니다.
services: virtual-machines-linux
documentationcenter: ''
author: ejarvi
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/10/2019
ms.author: ejarvi
ms.openlocfilehash: d544aae33faf60be00a2b4ea0a45f405efcedb39
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706135"
---
# <a name="azure-disk-encryption-for-linux-microsoftazuresecurityazurediskencryptionforlinux"></a>Linux용 Azure Disk Encryption(Microsoft.Azure.Security.AzureDiskEncryptionForLinux)

## <a name="overview"></a>개요

Azure Disk Encryption은 Linux에서 dm-crypt 서브 시스템을 활용하여 [Azure Linux 배포판 선택](https://aka.ms/adelinux)에서 전체 디스크 암호화를 제공합니다.  이 솔루션은 Azure Key Vault와 통합되어 디스크 암호화 키와 비밀을 관리합니다.

## <a name="prerequisites"></a>필수 구성 요소

필수 조건에 대한 전체 목록은 [Azure Disk Encryption 필수 조건](
../../security/azure-security-disk-encryption-prerequisites.md)을 참조하세요.

### <a name="operating-system"></a>운영 체제

Azure Disk Encryption은 현재 배포판 및 버전 선택에서 지원됩니다.  참조 된 [Azure Disk Encryption 지원 되는 운영 체제: Linux](../../security/azure-security-disk-encryption-prerequisites.md#linux) 목록은 지원 되는 Linux 배포판입니다.

### <a name="internet-connectivity"></a>인터넷 연결

Linux용 Azure Disk Encryption을 사용하려면 Active Directory, Key Vault, Storage 및 패키지 관리 엔드포인트에 액세스하기 위한 인터넷 연결이 필요합니다.  자세한 내용은 [Azure Disk Encryption 필수 조건](../../security/azure-security-disk-encryption-prerequisites.md)을 참조하세요.

## <a name="extension-schemata"></a>확장 스키마

Azure Disk Encryption에 대 한 스키마를 두 가지: v1.1, Azure Active Directory (AAD) 속성과 v0.1 사용 하지 않는 최신, 권장 되는 스키마, AAD 등록 정보를 필요로 하는 이전 스키마입니다. 사용 하는 확장 프로그램에 해당 하는 스키마 버전을 사용 해야 합니다: 스키마 v1.1 AzureDiskEncryptionForLinux 확장 버전 1.1에서는 AzureDiskEncryptionForLinux 확장 버전 0.1에 대 한 스키마 v0.1에 대 한 합니다.
### <a name="schema-v11-no-aad-recommended"></a>Schema v1.1: (권장) AAD 없음

V1.1 스키마는 것이 좋습니다와 Azure Active Directory 속성은 필요 하지 않습니다.

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2015-06-15",
  "location": "[location]",
  "properties": {
        "publisher": "Microsoft.Azure.Security",
        "settings": {
          "DiskFormatQuery": "[diskFormatQuery]",
          "EncryptionOperation": "[encryptionOperation]",
          "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
          "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
          "KeyVaultURL": "[keyVaultURL]",
          "SequenceVersion": "sequenceVersion]",
          "VolumeType": "[volumeType]"
        },
        "type": "AzureDiskEncryptionForLinux",
        "typeHandlerVersion": "[extensionVersion]"
  }
}
```


### <a name="schema-v01-with-aad"></a>스키마 v0.1: AAD를 사용 하 여 

0\.1 스키마 필요 `aadClientID` 고 `aadClientSecret` 또는 `AADClientCertificate`합니다.

`aadClientSecret`사용:

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2015-06-15",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientSecret": "[aadClientSecret]",
      "Passphrase": "[passphrase]"
    },
    "publisher": "Microsoft.Azure.Security",
    "settings": {
      "AADClientID": "[aadClientID]",
      "DiskFormatQuery": "[diskFormatQuery]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KeyVaultURL": "[keyVaultURL]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    },
    "type": "AzureDiskEncryptionForLinux",
    "typeHandlerVersion": "[extensionVersion]"
  }
}
```

`AADClientCertificate`사용:

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2015-06-15",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientCertificate": "[aadClientCertificate]",
      "Passphrase": "[passphrase]"
    },
    "publisher": "Microsoft.Azure.Security",
    "settings": {
      "AADClientID": "[aadClientID]",
      "DiskFormatQuery": "[diskFormatQuery]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KeyVaultURL": "[keyVaultURL]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    },
    "type": "AzureDiskEncryptionForLinux",
    "typeHandlerVersion": "[extensionVersion]"
  }
}
```


### <a name="property-values"></a>속성 값

| 이름 | 값/예제 | 데이터 형식 |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | 날짜 |
| publisher | Microsoft.Azure.Security | string |
| type | AzureDiskEncryptionForLinux | string |
| typeHandlerVersion | 0.1, 1.1 | ssNoversion |
| (0.1 스키마) AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | GUID | 
| (0.1 스키마) AADClientSecret | password | string |
| (0.1 스키마) AADClientCertificate | thumbprint | string |
| DiskFormatQuery | {"dev_path":"","name":"","file_system":""} | JSON 사전 |
| EncryptionOperation | EnableEncryption, EnableEncryptionFormatAll | string | 
| KeyEncryptionAlgorithm | 'RSA-OAEP', 'RSA-OAEP-256', 'RSA1_5' | string |
| KeyEncryptionKeyURL | url | string |
| (선택 사항) KeyVaultURL | url | string |
| Passphrase | password | string | 
| SequenceVersion | uniqueidentifier | string |
| VolumeType | OS, Data, All | string |

## <a name="template-deployment"></a>템플릿 배포

템플릿 배포에 대한 예제는 [실행 중인 Linux VM에서 암호화 사용](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm)을 참조하세요.

## <a name="azure-cli-deployment"></a>Azure CLI 배포

지침은 최신 [Azure CLI 설명서](/cli/azure/vm/encryption?view=azure-cli-latest)에서 찾을 수 있습니다. 

## <a name="troubleshoot-and-support"></a>문제 해결 및 지원

### <a name="troubleshoot"></a>문제 해결

문제 해결 방법은 [Azure Disk Encryption 문제 해결 가이드](../../security/azure-security-disk-encryption-tsg.md)를 참조하세요.

### <a name="support"></a>Support(지원)

이 문서의 어디에서든 도움이 필요한 경우 [MSDN Azure 및 Stack Overflow 포럼](https://azure.microsoft.com/support/community/)에서 Azure 전문가에게 문의할 수 있습니다. 또는 Azure 기술 지원 인시던트를 제출할 수 있습니다. [Azure 지원 사이트](https://azure.microsoft.com/support/options/)로 가서 지원 받기를 선택합니다. Azure 지원을 사용하는 방법에 대한 자세한 내용은 [Microsoft Azure 지원 FAQ](https://azure.microsoft.com/support/faq/)를 참조하세요.

## <a name="next-steps"></a>다음 단계

VM 확장에 대한 자세한 내용은 [Linux용 가상 머신 확장 및 기능 개요](features-linux.md)를 참조하세요.