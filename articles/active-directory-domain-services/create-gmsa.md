---
title: 'Azure Active Directory Domain Services: 그룹 관리 서비스 계정 만들기 | Microsoft Docs'
description: Azure Active Directory Domain Services 관리되는 도메인 관리
services: active-directory-ds
documentationcenter: ''
author: MikeStephens-MS
manager: daveba
editor: curtand
ms.assetid: e6faeddd-ef9e-4e23-84d6-c9b3f7d16567
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: mstephen
ms.openlocfilehash: 0f4e57359797e9c28ea62397e5c92c5fca05c69e
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66246346"
---
# <a name="create-a-group-managed-service-account-gmsa-on-an-azure-ad-domain-services-managed-domain"></a>Azure Active Directory Domain Services 관리되는 도메인에서 그룹 관리 서비스 계정(gMSA) 만들기
이 문서는 Azure AD Domain Services 관리되는 도메인에서 관리 서비스 계정을 만드는 방법을 보여 줍니다.

## <a name="managed-service-accounts"></a>관리 서비스 계정
독립 실행형 관리 서비스 계정(sMSA)은 암호가 자동으로 관리되는 관리 도메인 계정입니다. SPN(서비스 주체 이름) 관리를 단순화하고 다른 관리자에게 위임 관리를 사용하도록 설정합니다. 이 유형의 관리 서비스 계정(MSA)은 Windows Server 2008 R2 및 Windows 7에 도입되었습니다.

그룹 관리 서비스 계정(gMSA)은 도메인의 많은 서버에 대해 동일한 혜택을 제공합니다. 서버 팜에 호스팅된 서비스의 모든 인스턴스는 상호 인증 프로토콜에 대해 동일한 서비스 주체를 사용해야 합니다. gMSA가 서비스 주체로 사용되는 경우 Windows 운영 체제는 관리자에게 의존하는 대신 해당 계정의 암호를 관리합니다.

**자세한 정보:**
- [그룹 관리 서비스 개요](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)
- [그룹 관리 서비스 계정 시작](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts)


## <a name="using-service-accounts-in-azure-ad-domain-services"></a>Azure AD Domain Services에서 서비스 계정 사용
Azure AD Domain Services 관리되는 도메인은 Microsoft에서 잠그고 관리합니다. Azure AD Domain Services으로 서비스 계정을 사용하는 경우 몇 가지 주요 고려 사항이 있습니다.

### <a name="create-service-accounts-within-custom-organizational-units-ou-on-the-managed-domain"></a>관리되는 도메인에서 사용자 지정 조직 구성 단위(OU) 내에서 서비스 계정 만들기
기본 제공 'AADDC 사용자' 또는 'AADDC 컴퓨터' 조직 구성 단위에서는 서비스 계정을 만들 수 없습니다. 관리되는 도메인에서 [사용자 지정 OU를 만든](create-ou.md) 다음, 해당 사용자 지정 OU 내에서 서비스 계정을 만듭니다.

### <a name="the-key-distribution-services-kds-root-key-is-already-pre-created"></a>KDS(Key Distribution Services) 루트 키는 이미 사전 생성
KDS(Key Distribution Services) 루트 키는 Azure AD Domain Services 관리되는 도메인에서 사전 생성됩니다. KDS 루트 키를 만들 필요가 없으며 그렇게 할 권한도 없습니다. 관리되는 도메인에서도 KDS 루트 키를 볼 수 없습니다.

## <a name="sample---create-a-gmsa-using-powershell"></a>샘플 - PowerShell을 사용하여 gMSA 만들기
다음 샘플은 PowerShell을 사용하여 사용자 지정 OU를 만드는 방법을 보여줍니다. OU를 지정하려면 ```-Path``` 매개 변수를 사용하여 해당 OU 내에서 gMSA를 만들 수 있습니다.

```powershell
# Create a new custom OU on the managed domain
New-ADOrganizationalUnit -Name "MyNewOU" -Path "DC=CONTOSO100,DC=COM"

# Create a service account 'WebFarmSvc' within the custom OU.
New-ADServiceAccount -Name WebFarmSvc  `
-DNSHostName ` WebFarmSvc.contoso100.com  `
-Path "OU=MYNEWOU,DC=CONTOSO100,DC=com"  `
-KerberosEncryptionType AES128, AES256  ` -ManagedPasswordIntervalInDays 30  `
-ServicePrincipalNames http/WebFarmSvc.contoso100.com/contoso100.com, `
http/WebFarmSvc.contoso100.com/contoso100,  `
http/WebFarmSvc/contoso100.com, http/WebFarmSvc/contoso100  `
-PrincipalsAllowedToRetrieveManagedPassword CONTOSO-SERVER$
```

**PowerShell cmdlet 설명서:**
- [New-ADOrganizationalUnit cmdlet](https://docs.microsoft.com/powershell/module/addsadministration/new-adorganizationalunit)
- [New-ADServiceAccount cmdlet](https://docs.microsoft.com/powershell/module/addsadministration/New-ADServiceAccount)


## <a name="next-steps"></a>다음 단계
- [관리되는 도메인에서 사용자 지정 OU 만들기](create-ou.md)
- [그룹 관리 서비스 개요](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)
- [그룹 관리 서비스 계정 시작](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts)
