---
title: Azure AD를 사용 하 여 Azure Disk Encryption (이전 릴리스)
description: 이 문서에서는 IaaS VM용 Microsoft Azure Disk Encryption을 사용하기 위한 필수 구성 요소를 설명합니다.
author: msmbaldwin
ms.service: virtual-machines-windows
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: 025d02ccdf38e72682cf67cc07a8b2edd549e599
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82081577"
---
# <a name="azure-disk-encryption-with-azure-ad-previous-release"></a>Azure AD를 사용 하 여 Azure Disk Encryption (이전 릴리스)

**Azure Disk Encryption의 새 릴리스는 Azure AD 응용 프로그램 매개 변수를 제공 하 여 VM 디스크 암호화를 사용 하도록 설정 하는 요구 사항을 제거 합니다. 새 릴리스부터는 암호화 사용 단계에서 더 이상 Azure AD 자격 증명을 제공할 필요가 없습니다. 새 릴리스를 사용 하 여 Azure AD 응용 프로그램 매개 변수 없이 모든 새 Vm을 암호화 해야 합니다. 새 릴리스를 사용 하 여 VM 디스크 암호화를 사용 하도록 설정 하는 지침을 보려면 [Windows vm에 대 한 Azure Disk Encryption](disk-encryption-overview.md)를 참조 하세요. 이미 Azure AD 응용 프로그램 매개 변수를 사용 하 여 암호화 된 Vm은 계속 지원 되며 AAD 구문을 사용 하 여 계속 유지 되어야 합니다.**

이 문서에서는 Azure AD (이전 릴리스)와 Azure Disk Encryption에 대 한 추가 요구 사항 및 필수 구성 요소가 포함 된 [Windows vm에 대 한 Azure Disk Encryption를](disk-encryption-overview.md) 보완 [지원 되는 vm 및 운영 체제](disk-encryption-overview.md#supported-vms-and-operating-systems) 섹션은 동일 하 게 유지 됩니다.

## <a name="networking-and-group-policy"></a> 네트워킹 및 그룹 정책

**기존 AAD 매개 변수 구문을 사용하여 Azure Disk Encryption 기능을 사용하도록 설정하려면 IaaS VM이 다음 네트워크 엔드포인트 구성 요구 사항을 충족해야 합니다.** 
  - Key Vault에 연결할 토큰을 얻으려면 IaaS VM에서 Azure Active Directory 엔드포인트인 \[login.microsoftonline.com\]에 연결할 수 있어야 합니다.
  - 암호화 키를 고객 Key Vault에 쓰려면 IaaS VM에서 Key Vault 엔드포인트에 연결할 수 있어야 합니다.
  - IaaS VM은 Azure 확장 리포지토리를 호스팅하는 Azure Storage 엔드포인트 및 VHD 파일을 호스팅하는 Azure Storage 계정에 연결할 수 있어야 합니다.
  -  보안 정책이 Azure VM에서 인터넷으로 액세스를 제한하는 경우 이전 URI를 확인하고 IP에 대한 아웃바운드 연결을 허용하도록 특정 규칙을 구성할 수 있습니다. 자세한 내용은 [방화벽 뒤에 있는 Azure Key Vault](../../key-vault/key-vault-access-behind-firewall.md)를 참조하세요.
  - 암호화 될 VM은 기본 프로토콜로 TLS 1.2을 사용 하도록 구성 되어야 합니다. TLS 1.0을 명시적으로 사용 하지 않도록 설정 하 고 .NET 버전이 4.6 이상으로 업데이트 되지 않은 경우 다음 레지스트리 변경 내용을 사용 하 여 ADE에서 최신 TLS 버전을 선택할 수 있습니다.

```console
[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
"SystemDefaultTlsVersions"=dword:00000001
"SchUseStrongCrypto"=dword:00000001

[HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
"SystemDefaultTlsVersions"=dword:00000001
"SchUseStrongCrypto"=dword:00000001` 
```

**그룹 정책:**
 - Azure Disk Encryption 솔루션은 Windows IaaS VM에 대해 BitLocker 외부 키 보호기를 사용합니다. 도메인 가입 VM의 경우 TPM 보호기를 적용하는 그룹 정책을 푸시하지 않습니다. "호환되는 TPM이 없이 BitLocker 허용"에 대한 그룹 정책 정보는 [BitLocker 그룹 정책 참조](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1)를 참조하세요.

-  사용자 지정 그룹 정책을 사용 하는 도메인에 가입 된 가상 컴퓨터의 BitLocker 정책에는 [bitlocker 복구 정보의 사용자 저장소 구성-256 비트 복구 키 허용 >](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings)설정이 포함 되어야 합니다. BitLocker에 대한 사용자 지정 그룹 정책 설정이 호환되지 않으면 Azure Disk Encryption이 실패합니다. 올바른 정책 설정이 없는 머신에서 새 정책을 적용하고, 새 정책을 강제로 업데이트한(gpupdate.exe /force) 다음, 다시 시작해야 할 수 있습니다.  

## <a name="encryption-key-storage-requirements"></a>암호화 키 저장소 요구 사항  

Azure Disk Encryption에서 디스크 암호화 키와 암호를 제어 하 고 관리 하는 Azure Key Vault 필요 합니다. 주요 자격 증명 모음 및 Vm은 동일한 Azure 지역 및 구독에 있어야 합니다.

자세한 내용은 [AZURE AD를 사용 하 여 Azure Disk Encryption에 대 한 key Vault 만들기 및 구성 (이전 릴리스)](disk-encryption-key-vault-aad.md)을 참조 하세요.
 
## <a name="next-steps"></a>다음 단계

- [Azure AD (이전 릴리스)를 사용 하 여 Azure Disk Encryption 키 자격 증명 모음 만들기 및 구성](disk-encryption-key-vault-aad.md)
- [Windows Vm에서 Azure AD를 사용 하 여 Azure Disk Encryption 사용 (이전 릴리스)](disk-encryption-windows-aad.md)
- [Azure Disk Encryption 필수 구성 요소 CLI 스크립트](https://github.com/ejarvi/ade-cli-getting-started)
- [필수 조건 PowerShell 스크립트 Azure Disk Encryption](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
