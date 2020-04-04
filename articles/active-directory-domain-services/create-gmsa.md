---
title: Azure AD 도메인 서비스에 대한 그룹 관리 서비스 계정 | 마이크로 소프트 문서
description: Azure Active Directory 도메인 서비스 관리 도메인과 함께 사용할 그룹 관리 서비스 계정(gMSA)을 만드는 방법에 대해 알아봅니다.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: e6faeddd-ef9e-4e23-84d6-c9b3f7d16567
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: 5955f52cda73630f371a46f83ac0fb9a252b80e3
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655480"
---
# <a name="create-a-group-managed-service-account-gmsa-in-azure-ad-domain-services"></a>Azure AD 도메인 서비스에서 그룹 관리 서비스 계정(gMSA) 만들기

응용 프로그램 및 서비스에는 다른 리소스로 자신을 인증하기 위해 ID가 필요한 경우가 많습니다. 예를 들어 웹 서비스는 데이터베이스 서비스로 인증해야 할 수 있습니다. 응용 프로그램 이나 서비스에 웹 서버 팜 과 같은 여러 인스턴스가 있는 경우 해당 리소스에 대한 ID를 수동으로 만들고 구성하는 데 시간이 많이 걸립니다.

대신 Azure Active Directory 도메인 서비스(Azure AD DS) 관리 도메인에서 그룹 관리 서비스 계정(gMSA)을 만들 수 있습니다. Windows OS는 gMSA에 대한 자격 증명을 자동으로 관리하므로 대규모 리소스 그룹의 관리가 간소화됩니다.

이 문서에서는 Azure PowerShell을 사용하여 Azure AD DS 관리 도메인에서 gMSA를 만드는 방법을 보여 주며 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서를 완료하려면 다음과 같은 리소스와 권한이 필요합니다.

* 활성화된 Azure 구독.
    * Azure 구독이 없는 경우 [계정을 만듭니다.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* 온-프레미스 디렉터리 또는 클라우드 전용 디렉터리와 동기화되어 구독과 연결된 Azure Active Directory 테넌트
    * 필요한 경우 [Azure Active Directory 테넌트를 만들거나][create-azure-ad-tenant][Azure 구독을 계정에 연결합니다][associate-azure-ad-tenant].
* Azure AD 테넌트에서 사용하도록 설정되고 구성된 Azure Active Directory Domain Services 관리되는 도메인
    * 필요한 경우 자습서를 완료하여 [Azure Active Directory 도메인 서비스 인스턴스를 만들고 구성합니다.][create-azure-ad-ds-instance]
* Azure AD DS 관리 도메인에 조인된 Windows 서버 관리 VM입니다.
    * 필요한 경우 자습서를 완료하여 [관리 VM을 만듭니다.][tutorial-create-management-vm]

## <a name="managed-service-accounts-overview"></a>관리 서비스 계정 개요

독립 실행형 관리 서비스 계정(sMSA)은 암호가 자동으로 관리되는 도메인 계정입니다. 이 방법을 사용하면 SPN(서비스 주체 이름) 관리가 단순화되고 다른 관리자에게 위임된 관리가 가능합니다. 계정에 대한 자격 증명을 수동으로 만들고 회전할 필요가 없습니다.

그룹 관리 서비스 계정(gMSA)은 도메인의 여러 서버에 대해 동일한 관리 단순화를 제공합니다. gMSA를 사용하면 서버 팜에서 호스팅되는 서비스의 모든 인스턴스가 상호 인증 프로토콜을 작동하기 위해 동일한 서비스 주체를 사용할 수 있습니다. gMSA를 서비스 주체로 사용하는 경우 Windows 운영 체제는 관리자에게 의존하는 대신 계정의 암호를 다시 관리합니다.

자세한 내용은 [그룹 관리 서비스 계정(gMSA) 개요를][gmsa-overview]참조하십시오.

## <a name="using-service-accounts-in-azure-ad-ds"></a>Azure AD DS에서 서비스 계정 사용

Azure AD DS 관리 도메인이 잠겨 있고 Microsoft에서 관리하므로 서비스 계정을 사용할 때 몇 가지 고려 사항이 있습니다.

* 관리되는 도메인의 사용자 지정 조직 단위(OU)에서 서비스 계정을 만듭니다.
    * 기본 제공 *AADDC 사용자* 또는 *AADDC 컴퓨터* OUs에서는 서비스 계정을 만들 수 없습니다.
    * 대신 Azure AD DS 관리 도메인에서 [사용자 지정 OU를 만든][create-custom-ou] 다음 해당 사용자 지정 OU에서 서비스 계정을 만듭니다.
* 키 배포 서비스(KDS) 루트 키가 미리 만들어집니다.
    * KDS 루트 키는 gMSA에 대한 암호를 생성하고 검색하는 데 사용됩니다. Azure AD DS에서 KDS 루트가 만들어집니다.
    * 다른 키를 만들거나 기본 KDS 루트 키를 볼 수 있는 권한이 없습니다.

## <a name="create-a-gmsa"></a>gMSA를 만듭니다.

먼저 [새 ADOrganizationalUnit][New-AdOrganizationalUnit] cmdlet을 사용하여 사용자 지정 OU를 만듭니다. 사용자 지정 O를 만들고 관리하는 방법에 대한 자세한 내용은 [Azure AD DS의 사용자 지정 O를][create-custom-ou]참조하십시오.

> [!TIP]
> 이러한 단계를 완료하여 gMSA를 만들려면 [관리 VM을 사용합니다.][tutorial-create-management-vm] 이 관리 VM에는 필요한 AD PowerShell cmdlet및 관리되는 도메인에 대한 연결이 이미 있어야 합니다.

다음 예제에서는 *aaddscontoso.com*라는 Azure AD DS 관리 도메인에서 *myNewOU라는* 사용자 지정 OU를 만듭니다. 고유한 OU 및 관리되는 도메인 이름을 사용합니다.

```powershell
New-ADOrganizationalUnit -Name "myNewOU" -Path "DC=aaddscontoso,DC=COM"
```

이제 [새 ADServiceAccount][New-ADServiceAccount] cmdlet을 사용하여 gMSA를 만듭니다. 다음 예제 매개 변수는 정의됩니다.

* **- 이름은** *웹 팜스vc로* 설정됩니다
* **-Path** 매개 변수는 이전 단계에서 만든 gMSA에 대한 사용자 지정 OU를 지정합니다.
* *dNS* 항목 및 서비스 주체 이름은 WebFarmSvc.aaddscontoso.com 대해 설정됩니다.
* *AADDSCONTOSO-SERVER$의* 보안 주체는 ID를 사용하여 암호를 검색할 수 있습니다.

고유한 이름과 도메인 이름을 지정합니다.

```powershell
New-ADServiceAccount -Name WebFarmSvc `
    -DNSHostName WebFarmSvc.aaddscontoso.com `
    -Path "OU=MYNEWOU,DC=aaddscontoso,DC=com" `
    -KerberosEncryptionType AES128, AES256 `
    -ManagedPasswordIntervalInDays 30 `
    -ServicePrincipalNames http/WebFarmSvc.aaddscontoso.com/aaddscontoso.com, `
        http/WebFarmSvc.aaddscontoso.com/aaddscontoso, `
        http/WebFarmSvc/aaddscontoso.com, `
        http/WebFarmSvc/aaddscontoso `
    -PrincipalsAllowedToRetrieveManagedPassword AADDSCONTOSO-SERVER$
```

이제 필요에 따라 gMSA를 사용하도록 응용 프로그램 및 서비스를 구성할 수 있습니다.

## <a name="next-steps"></a>다음 단계

gMSA에 대한 자세한 내용은 [그룹 관리 서비스 계정 시작][gmsa-start]을 참조하십시오.

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[create-custom-ou]: create-ou.md

<!-- EXTERNAL LINKS -->
[New-ADOrganizationalUnit]: /powershell/module/addsadministration/New-AdOrganizationalUnit
[New-ADServiceAccount]: /powershell/module/addsadministration/New-AdServiceAccount
[gmsa-overview]: /windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview
[gmsa-start]: /windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts
