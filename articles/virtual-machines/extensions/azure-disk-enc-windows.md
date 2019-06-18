---
title: Windows용 Azure Disk Encryption | Microsoft Docs
description: 가상 머신 확장을 사용하여 Azure Disk Encryption을 Windows 가상 머신에 배포합니다.
services: virtual-machines-windows
documentationcenter: ''
author: ejarvi
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/12/2018
ms.author: ejarvi
ms.openlocfilehash: ff77f9fc017627143b14544af03d0d5e80813db9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67051700"
---
# <a name="azure-disk-encryption-for-windows-microsoftazuresecurityazurediskencryption"></a>Windows용 Azure Disk Encryption(Microsoft.Azure.Security.AzureDiskEncryption)

## <a name="overview"></a>개요

Azure Disk Encryption은 BitLocker를 활용하여 Windows를 실행하는 Azure 가상 머신에서 전체 디스크 암호화를 제공합니다.  이 솔루션은 Azure Key Vault와 통합되어 키 자격 증명 모음 구독의 디스크 암호화 키와 비밀을 관리합니다. 

## <a name="prerequisites"></a>필수 조건

필수 조건에 대한 전체 목록은 [Azure Disk Encryption 필수 조건](
../../security/azure-security-disk-encryption-prerequisites.md)을 참조하세요.

### <a name="operating-system"></a>운영 체제

현재 Windows 버전에 대한 목록은 [Azure Disk Encryption 필수 조건](../../security/azure-security-disk-encryption-prerequisites.md)을 참조하세요.

### <a name="internet-connectivity"></a>인터넷 연결

Azure Disk Encryption을 사용하려면 Active Directory, Key Vault, Storage 및 패키지 관리 엔드포인트에 액세스하기 위한 인터넷 연결이 필요합니다.  네트워크 보안 설정에 대한 자세한 내용은 [Azure Disk Encryption 필수 조건](
../../security/azure-security-disk-encryption-prerequisites.md)을 참조하세요.

## <a name="extension-schemata"></a>확장 스키마

Azure Disk Encryption에 대 한 스키마를 두 가지: v1.1, Azure Active Directory (AAD) 속성과 v0.1 사용 하지 않는 최신, 권장 되는 스키마, AAD 등록 정보를 필요로 하는 이전 스키마입니다. 사용 하는 확장 프로그램에 해당 하는 스키마 버전을 사용 해야 합니다: 확장 버전 1.1에서 AzureDiskEncryption 확장 버전 0.1에 대 한 스키마 v0.1 AzureDiskEncryption에 대 한 스키마 v1.1.

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
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KekVaultResourceId": "[keyVaultResourceID]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    },
  "type": "AzureDiskEncryption",
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
      "AADClientSecret": "[aadClientSecret]"
    },    
    "publisher": "Microsoft.Azure.Security",
    "settings": {
      "AADClientID": "[aadClientID]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KekVaultResourceId": "[keyVaultResourceID]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    },
  "type": "AzureDiskEncryption",
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
      "AADClientCertificate": "[aadClientCertificate]"
    },    
    "publisher": "Microsoft.Azure.Security",
    "settings": {
      "AADClientID": "[aadClientID]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KekVaultResourceId": "[keyVaultResourceID]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    },
  "type": "AzureDiskEncryption",
  "typeHandlerVersion": "[extensionVersion]"
  }
}
```


### <a name="property-values"></a>속성 값

| 이름 | 값/예제 | 데이터 형식 |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publisher | Microsoft.Azure.Security | 문자열 |
| type | AzureDiskEncryptionForLinux | 문자열 |
| typeHandlerVersion | 0.1, 1.1 | int |
| (0.1 스키마) AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | GUID | 
| (0.1 스키마) AADClientSecret | password | 문자열 |
| (0.1 스키마) AADClientCertificate | thumbprint | 문자열 |
| DiskFormatQuery | {"dev_path":"","name":"","file_system":""} | JSON 사전 |
| EncryptionOperation | EnableEncryption, EnableEncryptionFormatAll | 문자열 | 
| KeyEncryptionAlgorithm | 'RSA-OAEP', 'RSA-OAEP-256', 'RSA1_5' | 문자열 |
| KeyEncryptionKeyURL | url | 문자열 |
| KeyVaultURL | url | 문자열 |
| (선택 사항) Passphrase | password | 문자열 | 
| SequenceVersion | uniqueidentifier | 문자열 |
| VolumeType | OS, Data, All | 문자열 |

## <a name="template-deployment"></a>템플릿 배포
템플릿 배포에 대한 예제는 [갤러리 이미지에서 암호화된 새 Windows VM 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image)를 참조하세요.

## <a name="azure-cli-deployment"></a>Azure CLI 배포

지침은 최신 [Azure CLI 설명서](/cli/azure/vm/encryption?view=azure-cli-latest)에서 찾을 수 있습니다. 

## <a name="troubleshoot-and-support"></a>문제 해결 및 지원

### <a name="troubleshoot"></a>문제 해결

[Azure Disk Encryption 문제 해결 가이드](../../security/azure-security-disk-encryption-tsg.md)를 참조하세요.

### <a name="support"></a>지원

이 문서의 어디에서든 도움이 필요한 경우 [MSDN Azure 및 Stack Overflow 포럼](https://azure.microsoft.com/support/community/)에서 Azure 전문가에게 문의할 수 있습니다. 또는 Azure 기술 지원 인시던트를 제출할 수 있습니다. [Azure 지원 사이트](https://azure.microsoft.com/support/options/)로 가서 지원 받기를 선택합니다. Azure 지원을 사용하는 방법에 대한 자세한 내용은 [Microsoft Azure 지원 FAQ](https://azure.microsoft.com/support/faq/)를 참조하세요.

## <a name="next-steps"></a>다음 단계
확장에 대한 자세한 내용은 [Windows용 가상 머신 확장 및 기능](features-windows.md)을 참조하세요.
