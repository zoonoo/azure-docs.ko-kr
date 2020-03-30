---
title: 리눅스에 대 한 Azure 디스크 암호화
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
ms.openlocfilehash: 22568c7c23771f143f6cd583114949c380d15e3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066909"
---
# <a name="azure-disk-encryption-for-linux-microsoftazuresecurityazurediskencryptionforlinux"></a>Linux용 Azure Disk Encryption(Microsoft.Azure.Security.AzureDiskEncryptionForLinux)

## <a name="overview"></a>개요

Azure Disk Encryption은 Linux에서 dm-crypt 서브 시스템을 활용하여 [Azure Linux 배포판 선택](https://aka.ms/adelinux)에서 전체 디스크 암호화를 제공합니다.  이 솔루션은 Azure Key Vault와 통합되어 디스크 암호화 키와 비밀을 관리합니다.

## <a name="prerequisites"></a>사전 요구 사항

필수 구성 프로그램의 전체 목록은 [Linux VM에 대한 Azure 디스크 암호화,](../linux/disk-encryption-overview.md)특히 다음 섹션을 참조하십시오.

- [지원되는 VM 및 운영 체제](../linux/disk-encryption-overview.md#supported-vms-and-operating-systems)
- [추가 VM 요구 사항](../linux/disk-encryption-overview.md#additional-vm-requirements)
- [네트워킹 요구 사항](../linux/disk-encryption-overview.md#networking-requirements)
- [암호화 키 스토리지 요구 사항](../linux/disk-encryption-overview.md#encryption-key-storage-requirements)

## <a name="extension-schema"></a>확장 스키마

Azure 디스크 암호화(ADE)에 대한 두 가지 확장 스키마 버전이 있습니다.
- v1.1 - Azure Active Directory(AAD) 속성을 사용하지 않는 최신 권장 스키마입니다.
- v0.1 - Azure Active Directory(AAD) 속성이 필요한 이전 스키마입니다. 

대상 스키마를 선택하려면 `typeHandlerVersion` 속성을 사용하려는 스키마 버전과 동일하게 설정해야 합니다.

### <a name="schema-v11-no-aad-recommended"></a>스키마 v1.1: AAD 없음(권장)

v1.1 스키마를 권장하며 AAD(Azure Active Directory) 속성이 필요하지 않습니다.

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


### <a name="schema-v01-with-aad"></a>스키마 v0.1: AAD 

0.1 스키마에는 `AADClientID` 또는 `AADClientSecret` `AADClientCertificate`.

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

| 이름 | 값/예제 | 데이터 형식 |
| ---- | ---- | ---- |
| apiVersion | 2019-07-01 | date |
| publisher | Microsoft.Azure.Security | 문자열 |
| type | AzureDiskEncryptionForLinux | 문자열 |
| typeHandlerVersion | 1.1, 0.1 | int |
| (0.1 스키마) AAD클라이언트ID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | guid | 
| (0.1 스키마) AAD클라이언트시크릿 | password | 문자열 |
| (0.1 스키마) AAD클라이언트인증서 | thumbprint | 문자열 |
| (선택 사항) (0.1 스키마) 암호 | password | 문자열 |
| DiskFormatQuery | {"dev_path":"","name":"","file_system":""} | JSON 사전 |
| EncryptionOperation | EnableEncryption, EnableEncryptionFormatAll | 문자열 | 
| (선택 사항 - 기본 RSA-OAEP) 키 암호화 알고리즘 | 'RSA-OAEP', 'RSA-OAEP-256', 'RSA1_5' | 문자열 |
| KeyVaultURL | url | 문자열 |
| 키볼트자원 | url | 문자열 |
| (선택 사항) 키 암호화키URL | url | 문자열 |
| (선택 사항) 케크볼트자원하이드 | url | 문자열 |
| (선택 사항) 시퀀스버전 | uniqueidentifier | 문자열 |
| VolumeType | OS, Data, All | 문자열 |

## <a name="template-deployment"></a>템플릿 배포

스키마 v1.1을 기반으로 하는 템플릿 배포의 예는 Azure Quickstart Template [201-암호화 실행-linux-vm-without-aad를](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad)참조하십시오.

스키마 v0.1을 기반으로 하는 템플릿 배포의 예는 Azure 빠른 시작 템플릿 [201-암호화 실행-linux-vm](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm)을 참조하십시오.

>[!WARNING]
> - 이전에 Azure AD와 함께 Azure 디스크 암호화를 사용하여 VM을 암호화한 경우 이 옵션을 계속 사용하여 VM을 암호화해야 합니다.
> - Linux OS 볼륨을 암호화할 때 VM을 사용할 수 없는 것으로 간주해야 합니다. 암호화 프로세스 중에 액세스해야 하는 열려 있는 파일을 차단하는 문제를 피하기 위해 암호화가 진행되는 동안 SSH 로그인을 방지하는 것이 좋습니다. 진행률을 확인하려면 [Get-AzVMDisk암호화 상태](/powershell/module/az.compute/get-azvmdiskencryptionstatus) PowerShell cmdlet 또는 [VM 암호화 표시](/cli/azure/vm/encryption#az-vm-encryption-show) CLI 명령을 사용합니다. 이 프로세스는 30GB OS 볼륨을 처리하는 데 몇 시간이 걸리고 데이터 볼륨을 암호화하는 데 추가 시간이 걸릴 수 있습니다. 데이터 볼륨 암호화 시간은 encrypt format all 옵션이 사용되지 않는 한 데이터 볼륨의 크기 및 수량에 비례합니다. 
> - Linux VM에서 암호화 사용 안 함은 데이터 볼륨에 대해서만 지원됩니다. OS 볼륨이 암호화된 경우 이 설정은 데이터 또는 OS 볼륨에서 지원되지 않습니다. 

>[!NOTE]
> 또한 `VolumeType` 매개 변수가 모두로 설정된 경우 데이터 디스크가 올바르게 탑재된 경우에만 암호화됩니다.

## <a name="troubleshoot-and-support"></a>문제 해결 및 지원

### <a name="troubleshoot"></a>문제 해결

문제 해결 방법은 [Azure Disk Encryption 문제 해결 가이드](../linux/disk-encryption-troubleshooting.md)를 참조하세요.

### <a name="support"></a>고객 지원팀

이 문서의 어느 시점에서든 도움이 필요한 경우 [MSDN Azure 및 스택 오버플로 포럼의](https://azure.microsoft.com/support/community/)Azure 전문가에게 문의할 수 있습니다. 

또는 Azure 기술 지원 인시던트를 제출할 수 있습니다. Azure [지원으로](https://azure.microsoft.com/support/options/) 이동하여 지원 받기를 선택합니다. Azure 지원 사용에 대한 자세한 내용은 [Microsoft Azure 지원 FAQ를](https://azure.microsoft.com/support/faq/)참조하십시오.

## <a name="next-steps"></a>다음 단계

* VM 확장에 대한 자세한 내용은 [Linux용 가상 머신 확장 및 기능 개요](features-linux.md)를 참조하세요.
* Linux용 Azure 디스크 암호화에 대한 자세한 내용은 [Linux 가상 컴퓨터를](../../security/fundamentals/azure-disk-encryption-vms-vmss.md#linux-virtual-machines)참조하십시오.
