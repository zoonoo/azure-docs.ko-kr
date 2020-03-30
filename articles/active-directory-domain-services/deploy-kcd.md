---
title: Kerberos Azure AD 도메인 서비스에 대한 제한된 위임 | 마이크로 소프트 문서
description: Azure Active Directory 도메인 서비스 관리 도메인에서 리소스 기반 Kerberos 제한 위임(KCD)을 활성화하는 방법을 알아봅니다.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: iainfou
ms.openlocfilehash: 216fdeca9893f4e290474512617f13382d22890f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77614019"
---
# <a name="configure-kerberos-constrained-delegation-kcd-in-azure-active-directory-domain-services"></a>Azure Active Directory 도메인 서비스에서 Kerberos 제한 위임(KCD) 구성

응용 프로그램을 실행할 때 해당 응용 프로그램이 다른 사용자의 컨텍스트에서 리소스에 액세스해야 할 수 있습니다. AD DS(활성 디렉터리 도메인 서비스)는 *Kerberos 위임이라는* 메커니즘을 지원하여 이 사용 사례를 사용할 수 있습니다. 그런 다음 Kerberos 제한 위임(KCD)은 이 메커니즘을 기반으로 사용자의 컨텍스트에서 액세스할 수 있는 특정 리소스를 정의합니다. *constrained* Azure Active Directory 도메인 서비스(Azure AD DS) 관리 되는 도메인은 기존 온-프레미스 AD DS 환경 보다 더 안전하게 잠겨 있으므로 보다 안전한 *리소스 기반* KCD를 사용합니다.

이 문서에서는 Azure AD DS 관리 도메인에서 리소스 기반 Kerberos 제한 위임을 구성하는 방법을 보여 주며, 이 문서에서는

## <a name="prerequisites"></a>사전 요구 사항

이 문서를 완료하려면 다음 리소스가 필요합니다.

* 활성화된 Azure 구독.
    * Azure 구독이 없는 경우 [계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* 온-프레미스 디렉터리 또는 클라우드 전용 디렉터리와 동기화되어 구독과 연결된 Azure Active Directory 테넌트
    * 필요한 경우 [Azure Active Directory 테넌트를 만들거나][create-azure-ad-tenant][Azure 구독을 계정에 연결합니다][associate-azure-ad-tenant].
* Azure AD 테넌트에서 사용하도록 설정되고 구성된 Azure Active Directory Domain Services 관리되는 도메인
    * 필요한 경우 [Azure Active Directory Domain Services 인스턴스를 만들고 구성합니다][create-azure-ad-ds-instance].
* Azure AD DS 관리 도메인에 조인된 Windows 서버 관리 VM입니다.
    * 필요한 경우 자습서를 완료하여 [Windows Server VM을 만들고 관리되는 도메인에 참여한][create-join-windows-vm] 다음 [AD DS 관리 도구를 설치합니다.][tutorial-create-management-vm]
* Azure AD 테넌트의 *Azure AD DC Administrators* 그룹에 속한 멤버인 사용자 계정

## <a name="kerberos-constrained-delegation-overview"></a>커베로스 구속된 위임 개요

Kerberos 위임을 사용하면 한 계정이 다른 계정을 가장하여 리소스에 액세스할 수 있습니다. 예를 들어 백 엔드 웹 구성 요소에 액세스하는 웹 응용 프로그램은 백 엔드 연결을 만들 때 자신을 다른 사용자 계정으로 가장할 수 있습니다. Kerberos 위임은 가장하는 계정이 액세스할 수 있는 리소스를 제한하지 않으므로 안전하지 않습니다.

Kerberos 제한 된 위임 (KCD) 다른 ID를 가장 할 때 지정 된 서버 또는 응용 프로그램 연결할 수 있는 서비스 또는 리소스를 제한 합니다. 기존 KCD에는 서비스에 대한 도메인 계정을 구성하는 도메인 관리자 권한이 필요하며 단일 도메인에서 실행되는 계정이 제한됩니다.

전통적인 KCD에도 몇 가지 문제가 있습니다. 예를 들어 이전 운영 체제에서 서비스 관리자는 자신이 소유한 리소스 서비스에 위임된 프런트 엔드 서비스를 알 수 있는 유용한 방법이 없었습니다. 리소스 서비스에 위임할 수 있는 모든 프런트 엔드 서비스는 잠재적인 공격 지점이었습니다. 리소스 서비스에 위임하도록 구성된 프런트 엔드 서비스를 호스팅하는 서버가 손상된 경우 리소스 서비스도 손상될 수 있습니다.

Azure AD DS 관리 도메인에는 도메인 관리자 권한이 없습니다. 따라서 기존 계정 기반 KCD는 Azure AD DS관리 도메인에서 구성할 수 없습니다. 대신 리소스 기반 KCD를 사용할 수 있으며, 이는 더욱 안전합니다.

### <a name="resource-based-kcd"></a>리소스 기반 KCD

Windows Server 2012 이상은 서비스 관리자에게 제한된 위임을 서비스에 대해 구성할 수 있는 기능을 제공합니다. 이 모델은 리소스 기반 KCD라고도 합니다. 이 방법을 사용하면 백 엔드 서비스 관리자가 특정 프런트 엔드 서비스가 KCD를 사용하지 못하도록 허용하거나 거부할 수 있습니다.

리소스 기반 KCD는 PowerShell을 사용하여 구성됩니다. 가장하는 계정이 컴퓨터 계정인지 사용자 계정/서비스 계정인지에 따라 [Set-ADComputer][Set-ADComputer] 또는 [Set-ADUser][Set-ADUser] cmdlet을 사용합니다.

## <a name="configure-resource-based-kcd-for-a-computer-account"></a>컴퓨터 계정에 대한 리소스 기반 KCD 구성

이 시나리오에서는 *contoso-webapp.aaddscontoso.com*라는 컴퓨터에서 실행 되는 웹 응용 프로그램을 가정 해 봅시다. 웹 앱은 도메인 사용자의 컨텍스트에서 *contoso-api.aaddscontoso.com라는* 컴퓨터에서 실행되는 웹 API에 액세스해야 합니다. 다음 단계를 완료하여 이 시나리오를 구성합니다.

1. [사용자 지정 OU를 만듭니다](create-ou.md). Azure AD DS 관리 도메인 내의 사용자에게 이 사용자 지정 OU를 관리할 권한을 위임할 수 있습니다.
1. [도메인-웹][create-join-windows-vm]앱을 실행하는 컴퓨터와 웹 API를 실행하는 가상 컴퓨터를 Azure AD DS 관리 도메인에 도메인에 조인합니다. 이전 단계에서 사용자 지정 OU에서 이러한 컴퓨터 계정을 만듭니다.

    > [!NOTE]
    > 컴퓨터는 웹 앱에 대한 계정과 웹 API가 리소스 기반 KCD를 구성할 수 있는 권한이 있는 사용자 지정 OU에 있어야 합니다. 기본 제공 *AAD DC 컴퓨터* 컨테이너의 컴퓨터 계정에 대한 리소스 기반 KCD를 구성할 수 없습니다.

1. 마지막으로 [Set-ADComputer][Set-ADComputer] PowerShell cmdlet을 사용하여 리소스 기반 KCD를 구성합니다. 도메인 에 가입한 관리 VM에서 *Azure AD DC 관리자* 그룹의 구성원인 사용자 계정으로 로그인하여 다음 cmdlet을 실행합니다. 필요에 따라 사용자 고유의 컴퓨터 이름을 제공합니다.
    
    ```powershell
    $ImpersonatingAccount = Get-ADComputer -Identity contoso-webapp.aaddscontoso.com
    Set-ADComputer contoso-api.aaddscontoso.com -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
    ```

## <a name="configure-resource-based-kcd-for-a-user-account"></a>사용자 계정에 대한 리소스 기반 KCD 구성

이 시나리오에서는 *appsvc*라는 서비스 계정으로 실행 되는 웹 응용 프로그램을 가정 해 봅시다. 웹 앱은 도메인 사용자의 컨텍스트에서 *backendsvc라는* 서비스 계정으로 실행되는 웹 API에 액세스해야 합니다. 다음 단계를 완료하여 이 시나리오를 구성합니다.

1. [사용자 지정 OU를 만듭니다](create-ou.md). Azure AD DS 관리 도메인 내의 사용자에게 이 사용자 지정 OU를 관리할 권한을 위임할 수 있습니다.
1. 백 엔드 웹 API/리소스를 Azure AD DS 관리 [도메인에][create-join-windows-vm] 실행하는 가상 시스템에 도메인 가입합니다. 사용자 지정 OU에 해당 컴퓨터 계정을 만듭니다.
1. 사용자 지정 OU 내에서 웹앱을 실행하는 데 사용되는 서비스 계정(예: 'appsvc')을 만듭니다.

    > [!NOTE]
    > 다시 말하지만 웹 API VM에 대한 컴퓨터 계정과 웹 앱의 서비스 계정은 리소스 기반 KCD를 구성할 수 있는 권한이 있는 사용자 지정 OU에 있어야 합니다. 기본 제공 *AAD DC 컴퓨터* 또는 *AAD DC 사용자* 컨테이너의 계정에 대해 리소스 기반 KCD를 구성할 수 없습니다. 또한 Azure AD에서 동기화된 사용자 계정을 사용하여 리소스 기반 KCD를 설정할 수 없습니다. Azure AD DS에서 특별히 만든 서비스 계정을 만들고 사용해야 합니다.

1. 마지막으로 [Set-ADUser][Set-ADUser] PowerShell cmdlet을 사용하여 리소스 기반 KCD를 구성합니다. 도메인 에 가입한 관리 VM에서 *Azure AD DC 관리자* 그룹의 구성원인 사용자 계정으로 로그인하여 다음 cmdlet을 실행합니다. 필요에 따라 사용자 고유의 서비스 이름을 제공합니다.

    ```powershell
    $ImpersonatingAccount = Get-ADUser -Identity appsvc
    Set-ADUser backendsvc -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
    ```

## <a name="next-steps"></a>다음 단계

Active Directory 도메인 서비스에서 위임이 작동하는 방식에 대한 자세한 내용은 [Kerberos 제한된 위임 개요를][kcd-technet]참조하십시오.

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[Set-ADComputer]: /powershell/module/addsadministration/set-adcomputer
[Set-ADUser]: /powershell/module/addsadministration/set-aduser

<!-- EXTERNAL LINKS -->
[kcd-technet]: https://technet.microsoft.com/library/jj553400.aspx
