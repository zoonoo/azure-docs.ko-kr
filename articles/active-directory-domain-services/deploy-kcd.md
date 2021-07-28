---
title: Azure AD Domain Services에 대한 Kerberos 제한 위임 | Microsoft Docs
description: Azure Active Directory Domain Services 관리되는 도메인에서 리소스 기반 KCD(Kerberos 제한 위임)를 사용하도록 설정하는 방법을 알아봅니다.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: d9807920ae3f9ec39c8b211785832bae708728cf
ms.sourcegitcommit: 2e123f00b9bbfebe1a3f6e42196f328b50233fc5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2021
ms.locfileid: "108070428"
---
# <a name="configure-kerberos-constrained-delegation-kcd-in-azure-active-directory-domain-services"></a>Azure Active Directory Domain Services에서 KCD(Kerberos 제한 위임) 구성

애플리케이션을 실행할 때 이러한 애플리케이션이 다른 사용자의 컨텍스트에서 리소스에 액세스해야 할 수 있습니다. AD DS(Active Directory Domain Services)는 이 사용 사례를 사용하도록 설정하는 *Kerberos 위임* 이라는 메커니즘을 지원합니다. 그런 다음 KCD(Kerberos *제한* 위임)는 이 메커니즘을 기반으로 하여 사용자의 컨텍스트에서 액세스할 수 있는 특정 리소스를 정의합니다.

Azure AD DS(Azure Active Directory Domain Services) 관리되는 도메인은 기존의 온-프레미스 AD DS 환경보다 더 안전하게 잠겨 있으므로 더 안전한 *리소스 기반* KCD를 사용하세요.

이 문서에서는 Azure AD DS 관리되는 도메인에서 리소스 기반 Kerberos 제한 위임을 구성하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>사전 요구 사항

이 문서를 완료하려면 다음 리소스가 필요합니다.

* 활성화된 Azure 구독.
    * Azure 구독이 없는 경우 [계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* 온-프레미스 디렉터리 또는 클라우드 전용 디렉터리와 동기화되어 구독과 연결된 Azure Active Directory 테넌트
    * 필요한 경우 [Azure Active Directory 테넌트를 만들거나][create-azure-ad-tenant][Azure 구독을 계정에 연결합니다][associate-azure-ad-tenant].
* Azure AD 테넌트에서 사용하도록 설정되고 구성된 Azure Active Directory Domain Services 관리되는 도메인
    * 필요한 경우 [Azure Active Directory Domain Services 관리형 도메인을 만들고 구성합니다][create-azure-ad-ds-instance].
* Azure AD DS 관리되는 도메인에 연결된 Windows Server 관리 VM입니다.
    * 필요한 경우 자습서를 완료하여 [Windows Server VM을 만들고 관리되는 도메인에 가입][create-join-windows-vm]한 다음 [AD DS 관리 도구를 설치][tutorial-create-management-vm]합니다.
* Azure AD 테넌트의 *Azure AD DC Administrators* 그룹에 속한 멤버인 사용자 계정

## <a name="kerberos-constrained-delegation-overview"></a>Kerberos 제한 위임 개요

Kerberos 위임을 사용하면 리소스에 액세스하기 위해 한 계정에서 다른 계정을 가장할 수 있습니다. 예를 들어 백 엔드 웹 구성 요소에 액세스하는 웹 애플리케이션은 백 엔드 연결을 만들 때 다른 사용자 계정으로 자신을 가장할 수 있습니다. Kerberos 위임은 가장 계정이 액세스할 수 있는 리소스를 제한하지 않으므로 안전하지 않습니다.

KCD(Kerberos *제한* 위임)은 다른 ID를 가장할 때 지정된 서버 또는 애플리케이션이 연결할 수 있는 서비스 또는 리소스를 제한합니다. 기존의 KCD는 서비스에 대한 도메인 계정을 구성하기 위해 도메인 관리자 권한이 필요하고 단일 도메인에서 실행되도록 계정을 제한합니다.

기존 KCD에도 몇 가지 문제가 있습니다. 예를 들어 이전 운영 체제에서 서비스 관리자는 자신이 소유한 리소스 서비스에 위임된 프런트 엔드 서비스를 알 수 있는 유용한 방법이 없었습니다. 리소스 서비스에 위임할 수 있는 프런트 엔드 서비스는 잠재적인 공격 지점입니다. 리소스 서비스로 위임하도록 구성된 프런트 엔드 서비스를 호스트하는 서버가 손상된 경우 리소스 서비스도 손상될 수 있습니다.

관리되는 도메인에는 도메인 관리자 권한이 없습니다. 따라서 기존 계정 기반 KCD를 관리되는 도메인에서 구성할 수 없습니다. 리소스 기반 KCD를 대신 사용할 수 있으며, 이는 더 안전합니다.

### <a name="resource-based-kcd"></a>리소스 기반 KCD

Windows Server 2012 이상에서는 서비스 관리자에게 서비스에 대한 제한 위임을 구성할 수 있는 기능을 제공합니다. 이 모델은 리소스 기반 KCD라고도 합니다. 이 접근 방식을 사용하여 백 엔드 서비스 관리자는 KCD를 사용하여 특정 프런트 엔드 서비스를 허용하거나 거부할 수 있습니다.

리소스 기반 KCD는 PowerShell을 사용하여 구성됩니다. 가장하는 계정이 컴퓨터 계정인지 또는 사용자 계정/서비스 계정인지에 따라 [Set-ADComputer][Set-ADComputer] 또는 [Set-ADUser][Set-ADUser] cmdlet을 사용합니다.

## <a name="configure-resource-based-kcd-for-a-computer-account"></a>컴퓨터 계정에 대한 리소스 기반 KCD 구성

이 시나리오에서는 *contoso-webapp.aaddscontoso.com* 이라는 컴퓨터에서 실행되는 웹앱이 있다고 가정합니다.

웹앱은 도메인 사용자의 컨텍스트에서 *contoso-api.aaddscontoso.com* 이라는 컴퓨터에서 실행되는 웹 API에 액세스해야 합니다.

다음 단계를 완료하여 이 시나리오를 구성합니다.

1. [사용자 지정 OU를 만듭니다](create-ou.md). 이 사용자 지정 OU를 관리할 수 있는 권한을 관리되는 도메인 내의 사용자에게 위임할 수 있습니다.
1. 웹앱을 실행하는 가상 머신과 웹 API를 실행하는 가상 머신 모두를 관리형 도메인에 [도메인 가입][create-join-windows-vm]합니다. 이전 단계의 사용자 지정 OU에 이러한 컴퓨터 계정을 만듭니다.

    > [!NOTE]
    > 웹앱 및 웹 API에 대한 컴퓨터 계정은 리소스 기반 KCD를 구성할 수 있는 권한이 있는 사용자 지정 OU에 있어야 합니다. 기본 제공 *AAD DC 컴퓨터* 컨테이너의 컴퓨터 계정에 대해서는 리소스 기반 KCD를 구성할 수 없습니다.

1. 마지막으로 [Set-ADComputer][Set-ADComputer] PowerShell cmdlet을 사용하여 리소스 기반 KCD를 구성합니다.

    도메인 조인 관리 VM에서 *Azure AD DC 관리자* 그룹의 구성원인 사용자 계정으로 로그인하고 다음 cmdlet을 실행합니다. 필요에 따라 사용자 고유의 컴퓨터 이름을 제공합니다.
    
    ```powershell
    $ImpersonatingAccount = Get-ADComputer -Identity contoso-webapp.aaddscontoso.com
    Set-ADComputer contoso-api.aaddscontoso.com -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
    ```

## <a name="configure-resource-based-kcd-for-a-user-account"></a>사용자 계정에 대한 리소스 기반 KCD 구성

이 시나리오에서는 *appsvc* 라는 서비스 계정으로 실행되는 웹앱이 있다고 가정합니다. 웹앱은 도메인 사용자의 컨텍스트에서 *backendsvc* 라는 서비스 계정으로 실행되는 웹 API에 액세스해야 합니다. 다음 단계를 완료하여 이 시나리오를 구성합니다.

1. [사용자 지정 OU를 만듭니다](create-ou.md). 이 사용자 지정 OU를 관리할 수 있는 권한을 관리되는 도메인 내의 사용자에게 위임할 수 있습니다.
1. 관리되는 도메인에 백 엔드 웹 API/리소스를 실행하는 [가상 머신을 도메인 조인합니다][create-join-windows-vm]. 사용자 지정 OU에 해당 컴퓨터 계정을 만듭니다.
1. 사용자 지정 OU 내에서 웹앱을 실행하는 데 사용되는 서비스 계정(예: *appsvc*)을 만듭니다.

    > [!NOTE]
    > 웹 API VM에 대한 컴퓨터 계정과 웹앱에 대한 서비스 계정은 리소스 기반 KCD를 구성할 수 있는 권한이 있는 사용자 지정 OU에 있어야 합니다. 기본 제공 *AAD DC 컴퓨터* 또는 *AAD DC 사용자* 컨테이너의 계정에 대해 리소스 기반 KCD를 구성할 수 없습니다. 이는 또한 Azure AD에서 동기화된 사용자 계정을 사용하여 리소스 기반 KCD를 설정할 수 없음을 의미합니다. 특히 Azure AD DS에서 만든 서비스 계정을 만들고 사용해야 합니다.

1. 마지막으로 [Set-ADUser][Set-ADUser] PowerShell cmdlet을 사용하여 리소스 기반 KCD를 구성합니다.

    도메인 조인 관리 VM에서 *Azure AD DC 관리자* 그룹의 구성원인 사용자 계정으로 로그인하고 다음 cmdlet을 실행합니다. 필요에 따라 사용자 고유의 서비스 이름을 제공합니다.

    ```powershell
    $ImpersonatingAccount = Get-ADUser -Identity appsvc
    Set-ADUser backendsvc -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
    ```

## <a name="next-steps"></a>다음 단계

Active Directory Domain Services에서의 위임 방식에 대한 자세한 내용은 [Kerberos 제한 위임 개요][kcd-technet]를 참조하세요.

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[Set-ADComputer]: /powershell/module/activedirectory/set-adcomputer
[Set-ADUser]: /powershell/module/activedirectory/set-aduser

<!-- EXTERNAL LINKS -->
[kcd-technet]: /previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj553400(v=ws.11)
