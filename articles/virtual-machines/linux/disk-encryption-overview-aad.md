---
title: Azure AD 앱 필수 구성 을 사용한 Azure 디스크 암호화(이전 릴리스)
description: 이 문서에서는 IaaS VM용 Microsoft Azure Disk Encryption을 사용하기 위한 필수 구성 요소를 설명합니다.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: 029743dbb44ab0ebb6ab8f695c0c966137c45107
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81459800"
---
# <a name="azure-disk-encryption-with-azure-ad-previous-release"></a>Azure AD를 사용한 Azure 디스크 암호화(이전 릴리스)

Azure 디스크 암호화의 새로운 릴리스는 VM 디스크 암호화를 사용하도록 설정하는 Azure Active Directory(Azure AD) 응용 프로그램 매개 변수를 제공하는 데 대한 요구 사항을 제거합니다. 새 릴리스를 사용하면 암호화 단계를 사용하는 동안 더 이상 Azure AD 자격 증명을 제공할 필요가 없습니다. 모든 새 VM은 새 릴리스를 사용하여 Azure AD 응용 프로그램 매개 변수 없이 암호화되어야 합니다. 새 릴리스를 사용하여 VM 디스크 암호화를 사용하도록 설정하는 방법에 대한 지침은 [Linux VM용 Azure 디스크 암호화를](disk-encryption-overview.md)참조하십시오. Azure AD 애플리케이션 매개 변수를 사용하여 이미 암호화된 VM도 여전히 지원되며 AAD 구문을 사용하여 계속 유지 관리되어야 합니다.

이 문서에서는 Azure AD(이전 릴리스)를 사용한 Azure 디스크 암호화에 대한 추가 요구 사항 및 필수 구성 조건이 있는 [Linux VM용](disk-encryption-overview.md) Azure 디스크 암호화에 대한 보충을 제공합니다.

이 섹션의 정보는 동일하게 유지됩니다.

- [지원되는 VM 및 운영 체제](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [추가 VM 요구 사항](disk-encryption-overview.md#additional-vm-requirements)


 

## <a name="networking-and-group-policy"></a> 네트워킹 및 그룹 정책

이전 AAD 매개 변수 구문을 사용하여 Azure 디스크 암호화 기능을 활성화하려면 서비스(IaaS) VM이 다음 네트워크 끝점 구성 요구 사항을 충족해야 합니다. 
  - 키 자격 증명 모음에 연결하는 토큰을 얻으려면 IaaS VM이 Azure AD 끝점인 \[login.microsoftonline.com\]연결할 수 있어야 합니다.
  - 암호화 키를 고객 Key Vault에 쓰려면 IaaS VM에서 Key Vault 엔드포인트에 연결할 수 있어야 합니다.
  - IaaS VM은 Azure 확장 리포지토리를 호스팅하는 Azure Storage 엔드포인트 및 VHD 파일을 호스팅하는 Azure Storage 계정에 연결할 수 있어야 합니다.
  -  보안 정책에서 Azure VM에서 인터넷으로의 액세스를 제한하는 경우 이전 URI를 해결하고 특정 규칙을 구성하여 IP에 대한 아웃바운드 연결을 허용할 수 있습니다. 자세한 내용은 [방화벽 뒤에 있는 Azure Key Vault](../../key-vault/general/access-behind-firewall.md)를 참조하세요.
  - Windows에서 TLS 1.0을 명시적으로 사용하지 않도록 설정하고 .NET 버전이 4.6 이상으로 업데이트되지 않은 경우 다음 레지스트리 변경을 통해 Azure Disk 암호화를 사용하여 최신 TLS 버전을 선택할 수 있습니다.
    
            [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
            "SystemDefaultTlsVersions"=dword:00000001
            "SchUseStrongCrypto"=dword:00000001
    
            [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
            "SystemDefaultTlsVersions"=dword:00000001
            "SchUseStrongCrypto"=dword:00000001` 
         
    
### <a name="group-policy"></a>그룹 정책
 - Azure Disk Encryption 솔루션은 Windows IaaS VM에 대해 BitLocker 외부 키 보호기를 사용합니다. 도메인 에 가입된 VM의 경우 TPM 보호를 적용하는 그룹 정책을 푸시하지 마십시오. **호환되는 TPM 없이 BitLocker 허용**옵션에 대한 그룹 정책에 대한 자세한 내용은 [BitLocker 그룹 정책 참조를](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1)참조하십시오.

- 사용자 지정 그룹 정책이 있는 도메인 에 가입된 가상 시스템에 대한 BitLocker 정책에는 다음과 같은 설정이 포함되어야 합니다 [>.](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings) BitLocker에 대한 사용자 지정 그룹 정책 설정이 호환되지 않는 경우 Azure 디스크 암호화가 실패합니다. 올바른 정책 설정이 없는 컴퓨터에서새 정책을 적용하고 새 정책을 강제로 업데이트(gpupdate.exe /force)한 다음 필요한 경우 다시 시작합니다. 

## <a name="encryption-key-storage-requirements"></a>암호화 키 스토리지 요구 사항 

Azure 디스크 암호화를 사용하려면 Azure Key Vault에서 디스크 암호화 키 및 비밀을 제어하고 관리해야 합니다. 키 자격 증명 모음 및 VM은 동일한 Azure 리전 및 구독에 있어야 합니다.

자세한 내용은 [Azure AD(이전 릴리스)를 사용한 Azure 디스크 암호화에 대한 키 자격 증명 모음 만들기 및 구성을](disk-encryption-key-vault-aad.md)참조하십시오.
 
## <a name="next-steps"></a>다음 단계

- [Azure AD를 사용한 Azure 디스크 암호화에 대한 키 자격 증명 모음 만들기 및 구성(이전 릴리스)](disk-encryption-key-vault-aad.md)
- [Linux VM에서 Azure AD를 사용하여 Azure 디스크 암호화 사용(이전 릴리스)](disk-encryption-linux-aad.md)
- [Azure 디스크 암호화 필수 구성 조건 CLI 스크립트](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure 디스크 암호화 필수 구성 조건 PowerShell 스크립트](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)