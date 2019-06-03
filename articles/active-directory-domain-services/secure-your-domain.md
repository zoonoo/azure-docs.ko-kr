---
title: 'Azure Active Directory Domain Services: 관리되는 도메인 보호 | Microsoft Docs'
description: 관리되는 도메인 보호
services: active-directory-ds
documentationcenter: ''
author: MikeStephens-MS
manager: daveba
editor: curtand
ms.assetid: 6b4665b5-4324-42ab-82c5-d36c01192c2a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/20/2019
ms.author: mstephen
ms.openlocfilehash: ab371553a96f3a8d393c8b773c4024d04fd171a1
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66246736"
---
# <a name="secure-your-azure-ad-domain-services-managed-domain"></a>Azure AD Domain Services 관리되는 도메인 보호
이 문서에서는 관리되는 도메인을 보호하는 방법을 설명합니다. 취약한 암호 그룹 사용을 해제하고 NTLM 자격 증명 해시 동기화를 사용하지 않도록 설정할 수 있습니다.

## <a name="install-the-required-powershell-modules"></a>필수 PowerShell 모듈 설치

### <a name="install-and-configure-azure-ad-powershell"></a>Azure AD PowerShell 설치 및 구성
이 문서의 지침에 따라 [Azure AD PowerShell 모듈을 설치하고 Azure AD에 연결](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?toc=%2fazure%2factive-directory-domain-services%2ftoc.json)합니다.

### <a name="install-and-configure-azure-powershell"></a>Azure PowerShell 설치 및 구성
이 문서의 지침에 따라 [Azure PowerShell 모듈을 설치하고 Azure 구독에 연결](https://docs.microsoft.com/powershell/azure/install-az-ps?toc=%2fazure%2factive-directory-domain-services%2ftoc.json)합니다.


## <a name="disable-weak-cipher-suites-and-ntlm-credential-hash-synchronization"></a>취약한 암호 그룹 및 NTLM 자격 증명 해시 동기화를 사용하지 않도록 설정
아래 PowerShell 스크립트를 사용하여 다음과 같은 작업을 수행할 수 있습니다.
1. 관리되는 도메인에 대해 NTLM v1을 지원하지 않도록 설정합니다.
2. 온-프레미스 AD에서 NTLM 암호 해시 동기화를 사용하지 않도록 설정합니다.
3. 관리되는 도메인에서 TLS v1을 사용하지 않도록 설정합니다.

```powershell
// Login to your Azure AD tenant
Login-AzAccount

// Retrieve the Azure AD Domain Services resource.
$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"

// 1. Disable NTLM v1 support on the managed domain.
// 2. Disable the synchronization of NTLM password hashes from
//    on-premises AD to Azure AD and Azure AD Domain Services
// 3. Disable TLS v1 on the managed domain.
$securitySettings = @{"DomainSecuritySettings"=@{"NtlmV1"="Disabled";"SyncNtlmPasswords"="Disabled";"TlsV1"="Disabled"}}

// Apply the settings to the managed domain.
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $securitySettings -Verbose -Force
```

## <a name="next-steps"></a>다음 단계
* [Azure AD Domain Services의 동기화 이해](synchronization.md)
