---
title: Azure AD 앱을 포함한 Azure Disk Encryption 필수 구성 요소(이전 릴리스)
description: 이 문서에서는 IaaS VM용 Microsoft Azure Disk Encryption을 사용하기 위한 필수 구성 요소를 설명합니다.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: b0e3e683b2c103bc7f9b6812115e2e7a5d871034
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828674"
---
# <a name="azure-disk-encryption-with-azure-ad-previous-release"></a>Azure AD를 사용 하 여 Azure Disk Encryption (이전 릴리스)

**Azure Disk Encryption의 새 릴리스는 Azure AD 애플리케이션 매개 변수 제공에 대한 요구 사항을 제거하여 VM 디스크 암호화를 사용하도록 설정합니다. 새 릴리스를 사용하면 암호화 단계를 사용하는 동안 더 이상 Azure AD 자격 증명을 제공할 필요가 없습니다. 모든 새 VM은 새 릴리스를 사용하는 Azure AD 애플리케이션 매개 변수를 사용하지 않고 암호화되어야 합니다. 새 릴리스를 사용 하 여 VM 디스크 암호화를 사용 하도록 설정 하는 지침을 보려면 [Linux vm에 대 한 Azure Disk Encryption](disk-encryption-overview.md)를 참조 하세요. Azure AD 애플리케이션 매개 변수를 사용하여 이미 암호화된 VM도 여전히 지원되며 AAD 구문을 사용하여 계속 유지 관리되어야 합니다.**

이 문서에서는 Azure AD (이전 릴리스)와 Azure Disk Encryption에 대 한 추가 요구 사항 및 필수 구성 요소를 포함 하는 [Linux vm에 대 한 추가 Azure Disk Encryption](disk-encryption-overview.md)

이러한 섹션의 정보는 동일 하 게 유지 됩니다.

- [지원 되는 Vm 및 운영 체제](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [추가 VM 요구 사항](disk-encryption-overview.md#additional-vm-requirements)


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="networking-and-group-policy"></a>네트워킹 및 그룹 정책

**기존 AAD 매개 변수 구문을 사용하여 Azure Disk Encryption 기능을 사용하도록 설정하려면 IaaS VM이 다음 네트워크 엔드포인트 구성 요구 사항을 충족해야 합니다.** 
  - Key Vault에 연결할 토큰을 얻으려면 IaaS VM에서 Azure Active Directory 엔드포인트인 \[login.microsoftonline.com\]에 연결할 수 있어야 합니다.
  - 암호화 키를 고객 Key Vault에 쓰려면 IaaS VM에서 Key Vault 엔드포인트에 연결할 수 있어야 합니다.
  - IaaS VM은 Azure 확장 리포지토리를 호스팅하는 Azure Storage 엔드포인트 및 VHD 파일을 호스팅하는 Azure Storage 계정에 연결할 수 있어야 합니다.
  -  보안 정책이 Azure VM에서 인터넷으로 액세스를 제한하는 경우 이전 URI를 확인하고 IP에 대한 아웃바운드 연결을 허용하도록 특정 규칙을 구성할 수 있습니다. 자세한 내용은 [방화벽 뒤에 있는 Azure Key Vault](../../key-vault/key-vault-access-behind-firewall.md)를 참조하세요.
  - Windows에서 TLS 1.0이 명시적으로 비활성화되고 .NET 버전이 4.6 이상으로 업데이트되지 않은 경우 다음과 같이 레지스트리 변경 시 ADE가 최신 TLS 버전을 선택하도록 설정됩니다.
    
        [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
        "SystemDefaultTlsVersions"=dword:00000001
        "SchUseStrongCrypto"=dword:00000001

        [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
        "SystemDefaultTlsVersions"=dword:00000001
        "SchUseStrongCrypto"=dword:00000001` 
     

**그룹 정책:**
 - Azure Disk Encryption 솔루션은 Windows IaaS VM에 대해 BitLocker 외부 키 보호기를 사용합니다. 도메인 가입 VM의 경우 TPM 보호기를 적용하는 그룹 정책을 푸시하지 않습니다. "호환되는 TPM이 없이 BitLocker 허용"에 대한 그룹 정책 정보는 [BitLocker 그룹 정책 참조](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1)를 참조하세요.

-  사용자 지정 그룹 정책을 사용하는 도메인 가입 가상 머신의 BitLocker 정책은 다음 설정을 포함해야 합니다. [BitLocker 복구 정보의 사용자 저장소 구성-256 비트 복구 키를 허용 >](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings)합니다. BitLocker에 대한 사용자 지정 그룹 정책 설정이 호환되지 않으면 Azure Disk Encryption이 실패합니다. 올바른 정책 설정이 없는 머신에서 새 정책을 적용하고, 새 정책을 강제로 업데이트한(gpupdate.exe /force) 다음, 다시 시작해야 할 수 있습니다.  

## <a name="encryption-key-storage-requirements"></a>암호화 키 저장소 요구 사항  

Azure Disk Encryption에서 디스크 암호화 키와 암호를 제어 하 고 관리 하는 Azure Key Vault 필요 합니다. 주요 자격 증명 모음 및 Vm은 동일한 Azure 지역 및 구독에 있어야 합니다.

자세한 내용은 [AZURE AD를 사용 하 여 Azure Disk Encryption에 대 한 key Vault 만들기 및 구성 (이전 릴리스)](disk-encryption-key-vault-aad.md)을 참조 하세요.
 
## <a name="next-steps"></a>다음 단계

- [Azure AD (이전 릴리스)를 사용 하 여 Azure Disk Encryption 키 자격 증명 모음 만들기 및 구성](disk-encryption-key-vault-aad.md)
- [Linux Vm에서 Azure AD를 사용 하 여 Azure Disk Encryption 사용 (이전 릴리스)](disk-encryption-linux-aad.md)
- [Azure Disk Encryption 필수 구성 요소 CLI 스크립트](https://github.com/ejarvi/ade-cli-getting-started)
- [필수 조건 PowerShell 스크립트 Azure Disk Encryption](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)