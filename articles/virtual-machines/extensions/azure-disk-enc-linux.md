---
title: Linux에 대 한 Azure Disk Encryption
description: 가상 머신 확장을 사용하여 Linux용 Azure Disk Encryption을 가상 머신에 배포합니다.
services: virtual-machines-linux
documentationcenter: ''
author: ejarvi
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/19/2020
ms.author: ejarvi
ms.openlocfilehash: cc82109c60ac077d9e92e4d4a52abdaf8577771f
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91971186"
---
# <a name="azure-disk-encryption-for-linux-microsoftazuresecurityazurediskencryptionforlinux"></a>Linux용 Azure Disk Encryption(Microsoft.Azure.Security.AzureDiskEncryptionForLinux)

## <a name="overview"></a>개요

Azure Disk Encryption은 Linux에서 dm-crypt 서브 시스템을 활용하여 [Azure Linux 배포판 선택](../linux/disk-encryption-overview.md)에서 전체 디스크 암호화를 제공합니다.  이 솔루션은 Azure Key Vault와 통합되어 디스크 암호화 키와 비밀을 관리합니다.

## <a name="prerequisites"></a>필수 구성 요소

필수 구성 요소의 전체 목록은 [Linux vm에 대 한 Azure Disk Encryption](../linux/disk-encryption-overview.md), 특히 다음 섹션을 참조 하세요.

- [지원되는 VM 및 운영 체제](../linux/disk-encryption-overview.md#supported-vms-and-operating-systems)
- [추가 VM 요구 사항](../linux/disk-encryption-overview.md#additional-vm-requirements)
- [네트워킹 요구 사항](../linux/disk-encryption-overview.md#networking-requirements)
- [암호화 키 스토리지 요구 사항](../linux/disk-encryption-overview.md#encryption-key-storage-requirements)

## <a name="extension-schema"></a>확장 스키마

Azure Disk Encryption (ADE)에는 두 가지 버전의 확장 스키마가 있습니다.
- v. 1.1-AAD (Azure Active Directory) 속성을 사용 하지 않는 최신 권장 스키마
- v2.0-AAD (Azure Active Directory) 속성이 필요한 이전 스키마입니다. 

대상 스키마를 선택 하려면 속성을 `typeHandlerVersion` 사용 하려는 스키마 버전과 동일 하 게 설정 해야 합니다.

### <a name="schema-v11-no-aad-recommended"></a>Schema v1.0: AAD 없음 (권장)

V 1.1 스키마는 권장 되며 AAD (Azure Active Directory) 속성은 필요 하지 않습니다.

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
        "publisher": "Microsoft.Azure.Security",
        "type": "AzureDiskEncryptionForLinux",
        "typeHandlerVersion": "1.1",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "DiskFormatQuery": "[diskFormatQuery]",
          "EncryptionOperation": "[encryptionOperation]",
          "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
          "KeyVaultURL": "[keyVaultURL]",
          "KeyVaultResourceId": "[KeyVaultResourceId]",
          "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
          "KekVaultResourceId": "[KekVaultResourceId",
          "SequenceVersion": "sequenceVersion]",
          "VolumeType": "[volumeType]"
        }
  }
}
```


### <a name="schema-v01-with-aad"></a>Schema v 0.1: AAD 사용 

0.1 스키마에는 `AADClientID` 및 중 하나가 필요 합니다 `AADClientSecret` `AADClientCertificate` .

`AADClientSecret`사용:

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientSecret": "[aadClientSecret]",
      "Passphrase": "[passphrase]"
    },
    "publisher": "Microsoft.Azure.Security",
    "type": "AzureDiskEncryptionForLinux",
    "typeHandlerVersion": "0.1",
    "settings": {
      "AADClientID": "[aadClientID]",
      "DiskFormatQuery": "[diskFormatQuery]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KeyVaultURL": "[keyVaultURL]",
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
      "AADClientCertificate": "[aadClientCertificate]",
      "Passphrase": "[passphrase]"
    },
    "publisher": "Microsoft.Azure.Security",
    "type": "AzureDiskEncryptionForLinux",
    "typeHandlerVersion": "0.1",
    "settings": {
      "AADClientID": "[aadClientID]",
      "DiskFormatQuery": "[diskFormatQuery]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KeyVaultURL": "[keyVaultURL]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    }
  }
}
```


### <a name="property-values"></a>속성 값

| Name | 값/예제 | 데이터 형식 |
| ---- | ---- | ---- |
| apiVersion | 2019-07-01 | date |
| publisher | Microsoft.Azure.Security | 문자열 |
| type | AzureDiskEncryptionForLinux | 문자열 |
| typeHandlerVersion | 1.1, 0.1 | int |
| (0.1 스키마) AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | guid | 
| (0.1 스키마) AADClientSecret | password | 문자열 |
| (0.1 스키마) AADClientCertificate | thumbprint | 문자열 |
| 필드 (0.1 스키마) 전달 | password | 문자열 |
| DiskFormatQuery | {"dev_path":"","name":"","file_system":""} | JSON 사전 |
| EncryptionOperation | EnableEncryption, EnableEncryptionFormatAll | 문자열 | 
| (옵션-기본 RSA-OAEP) KeyEncryptionAlgorithm | 'RSA-OAEP', 'RSA-OAEP-256', 'RSA1_5' | 문자열 |
| KeyVaultURL | url | 문자열 |
| KeyVaultResourceId | url | 문자열 |
| 필드 하면 keyencryptionkeyurl | url | 문자열 |
| 필드 KekVaultResourceId | url | 문자열 |
| 필드 SequenceVersion | uniqueidentifier | 문자열 |
| VolumeType | OS, Data, All | 문자열 |

## <a name="template-deployment"></a>템플릿 배포

Schema v 1.1을 기반으로 하는 템플릿 배포의 예는 Azure 빠른 시작 템플릿 [201-실행](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad)

Schema v 0.1을 기반으로 하는 템플릿 배포의 예는 Azure 빠른 시작 템플릿 [201-암호화-실행-linux-vm](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm)을 참조 하세요.

>[!WARNING]
> - 이전에 VM을 암호화하기 위해 Azure AD에서 Azure Disk Encryption을 사용한 적이 있다면 VM을 암호화하는 데 이 옵션을 계속 사용해야 합니다.
> - Linux OS 볼륨을 암호화하는 경우, VM은 사용할 수 없는 것으로 간주해야 합니다. 암호화 프로세스 중에 액세스해야 할 모든 열린 파일을 차단하는 문제를 방지하려면 암호화가 진행 중일 때 SSH 로그인을 하지 말 것을 적극 권장합니다. 진행률을 확인 하려면 [AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) PowerShell cmdlet 또는 [vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) CLI 명령을 사용 합니다. 이 프로세스는 30GB OS 볼륨을 처리하는 데 몇 시간이 걸리고 데이터 볼륨을 암호화하는 데 추가 시간이 걸릴 수 있습니다. 데이터 볼륨 암호화 시간은 encrypt format all 옵션이 사용되지 않는 한 데이터 볼륨의 크기 및 수량에 비례합니다. 
> - Linux VM에서 암호화 사용 안 함은 데이터 볼륨에 대해서만 지원됩니다. OS 볼륨이 암호화된 경우 이 설정은 데이터 또는 OS 볼륨에서 지원되지 않습니다. 

>[!NOTE]
> 또한 `VolumeType` 매개 변수를 All로 설정 하는 경우 데이터 디스크는 적절히 탑재 된 경우에만 암호화 됩니다.

## <a name="troubleshoot-and-support"></a>문제 해결 및 지원

### <a name="troubleshoot"></a>문제 해결

문제 해결 방법은 [Azure Disk Encryption 문제 해결 가이드](../linux/disk-encryption-troubleshooting.md)를 참조하세요.

### <a name="support"></a>지원

이 문서의 어디에서든 도움이 필요한 경우 [MSDN Azure 및 Stack Overflow 포럼](https://azure.microsoft.com/support/community/)에서 Azure 전문가에게 문의할 수 있습니다. 

또는 Azure 기술 지원 인시던트를 제출할 수 있습니다. [Azure 지원](https://azure.microsoft.com/support/options/) 으로 이동 하 여 지원 받기를 선택 합니다. Azure 지원 사용에 대 한 자세한 내용은 [Microsoft Azure 지원 FAQ](https://azure.microsoft.com/support/faq/)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

* VM 확장에 대한 자세한 내용은 [Linux용 가상 머신 확장 및 기능 개요](features-linux.md)를 참조하세요.
* Linux의 Azure Disk Encryption에 대 한 자세한 내용은 [linux virtual machines](../../security/fundamentals/azure-disk-encryption-vms-vmss.md#linux-virtual-machines)를 참조 하세요.