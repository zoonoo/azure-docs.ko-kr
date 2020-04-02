---
title: Azure AD 도메인 서비스에 대한 Azure AD 응용 프로그램 프록시 배포 | 마이크로 소프트 문서
description: Azure Active Directory 도메인 관리 도메인에 Azure Active Directory 응용 프로그램 프록시를 배포 하고 구성 하여 원격 작업자에 대 한 내부 응용 프로그램에 대 한 보안 액세스를 제공 하는 방법을 알아봅니다.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 03/31/2020
ms.author: iainfou
ms.openlocfilehash: 3fb731736941293a82baba86165a1205bd8a05c5
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80519043"
---
# <a name="deploy-azure-ad-application-proxy-for-secure-access-to-internal-applications-in-an-azure-ad-domain-services-managed-domain"></a>Azure AD 도메인 서비스 관리 도메인의 내부 응용 프로그램에 대한 보안 액세스를 위해 Azure AD 응용 프로그램 프록시 배포

Azure AD 도메인 서비스(Azure AD DS)를 사용하면 온-프레미스에서 실행되는 레거시 응용 프로그램을 Azure로 리프트 앤 시프트할 수 있습니다. 그런 다음 AD(Azure Active Directory) 응용 프로그램 프록시를 사용하면 인터넷을 통해 액세스할 수 있도록 Azure AD DS 관리 도메인의 내부 응용 프로그램 일부를 안전하게 게시하여 원격 작업자를 지원할 수 있습니다.

Azure AD 응용 프로그램 프록시를 새로 접하고 자세한 내용을 보려면 [내부 응용 프로그램에 대한 보안 원격 액세스를 제공하는 방법을 참조하세요.](../active-directory/manage-apps/application-proxy.md)

이 문서에서는 Azure AD DS 관리 도메인의 응용 프로그램에 대한 보안 액세스를 제공하도록 Azure AD 응용 프로그램 프록시 커넥터를 만들고 구성하는 방법을 보여 주며 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서를 완료하려면 다음과 같은 리소스와 권한이 필요합니다.

* 활성화된 Azure 구독.
    * Azure 구독이 없는 경우 [계정을 만듭니다.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* 온-프레미스 디렉터리 또는 클라우드 전용 디렉터리와 동기화되어 구독과 연결된 Azure Active Directory 테넌트
    * 필요한 경우 [Azure Active Directory 테넌트를 만들거나][create-azure-ad-tenant][Azure 구독을 계정에 연결합니다][associate-azure-ad-tenant].
    * **Azure AD 프리미엄 라이선스는** Azure AD 응용 프로그램 프록시를 사용해야 합니다.
* Azure AD 테넌트에서 사용하도록 설정되고 구성된 Azure Active Directory Domain Services 관리되는 도메인
    * 필요한 경우 [Azure Active Directory Domain Services 인스턴스를 만들고 구성합니다][create-azure-ad-ds-instance].

## <a name="create-a-domain-joined-windows-vm"></a>도메인 에 가입된 Windows VM 만들기

사용자 환경에서 실행 중인 응용 프로그램에 트래픽을 라우팅하려면 Azure AD 응용 프로그램 프록시 커넥터 구성 요소를 설치합니다. 이 Azure AD 응용 프로그램 프록시 커넥터는 Azure AD DS 관리 도메인에 조인된 Windows 서버 가상 시스템(VM)에 설치되어야 합니다. 일부 응용 프로그램의 경우 각각 커넥터가 설치된 여러 서버를 배포할 수 있습니다. 이 배포 옵션을 선택하면 가용성이 높아지고 더 많은 인증 부하를 처리하는 데 도움이 됩니다.

Azure AD 응용 프로그램 프록시 커넥터를 실행하는 VM은 Azure AD DS를 사용하도록 설정한 피어있는 가상 네트워크와 동일해야 합니다. 그런 다음 응용 프로그램 프록시를 사용하여 게시하는 응용 프로그램을 호스트하는 VM도 동일한 Azure 가상 네트워크에 배포되어야 합니다.

Azure AD 응용 프로그램 프록시 커넥터에 대한 VM을 만들려면 다음 단계를 완료하십시오.

1. [사용자 지정 OU를 만듭니다](create-ou.md). Azure AD DS 관리 도메인 내의 사용자에게 이 사용자 지정 OU를 관리할 권한을 위임할 수 있습니다. Azure AD 응용 프로그램 프록시및 응용 프로그램을 실행하는 VM은 기본 *AAD DC 컴퓨터* OU가 아니라 사용자 지정 OU의 일부여야 합니다.
1. Azure AD 응용 프로그램 프록시 커넥터를 실행하는 가상 컴퓨터와 응용 프로그램을 실행하는 가상 컴퓨터를 Azure AD DS 관리 도메인에 [도메인 으로 조인합니다.][create-join-windows-vm] 이전 단계에서 사용자 지정 OU에서 이러한 컴퓨터 계정을 만듭니다.

## <a name="download-the-azure-ad-application-proxy-connector"></a>Azure AD 응용 프로그램 프록시 커넥터 다운로드

Azure AD 응용 프로그램 프록시 커넥터를 다운로드하려면 다음 단계를 수행합니다. 다운로드한 설치 파일은 다음 섹션의 앱 프록시 VM에 복사됩니다.

1. Azure AD에 *엔터프라이즈 관리자* 권한이 있는 사용자 계정으로 Azure [포털에](https://portal.azure.com) 로그인합니다.
1. 포털 상단에서 **Azure Active Directory를** 검색하고 선택한 다음 **엔터프라이즈 응용 프로그램을 선택합니다.**
1. 왼쪽 의 메뉴에서 **응용 프로그램 프록시를** 선택합니다. 첫 번째 커넥터를 만들고 앱 프록시를 사용하려면 링크를 선택하여 **커넥터를 다운로드합니다.**
1. 다운로드 페이지에서 라이센스 조건 및 개인 정보 보호 계약에 동의한 다음 **용어 수락을 & 다운로드를**선택합니다.

    ![Azure AD 앱 프록시 커넥터 다운로드](./media/app-proxy/download-app-proxy-connector.png)

## <a name="install-and-register-the-azure-ad-application-proxy-connector"></a>Azure AD 응용 프로그램 프록시 커넥터 설치 및 등록

Azure AD 응용 프로그램 프록시 커넥터로 사용할 준비가 된 VM을 사용하면 Azure 포털에서 다운로드한 설치 파일을 복사하여 실행합니다.

1. Azure AD 응용 프로그램 프록시 커넥터 설정 파일을 VM에 복사합니다.
1. *AADApplication프록시커넥터 설치자 설치기와*같은 설치 파일을 실행합니다. 소프트웨어 사용 조건에 동의합니다.
1. 설치하는 동안 Azure AD 디렉터리의 응용 프로그램 프록시에 커넥터를 등록하라는 메시지가 표시됩니다.
   * Azure AD 디렉터리에서 전역 관리자에 대한 자격 증명을 제공합니다. Azure AD 글로벌 관리자 자격 증명은 포털의 Azure 자격 증명과 다를 수 있습니다.

        > [!NOTE]
        > 커넥터를 등록하는 데 사용되는 전역 관리자 계정은 응용 프로그램 프록시 서비스를 사용하도록 설정하는 동일한 디렉터리에 속해야 합니다.
        >
        > 예를 들어 Azure AD 도메인이 *aaddscontoso.com*경우 전역 `admin@aaddscontoso.com` 관리자는 해당 도메인의 다른 유효한 별칭이어야 합니다.

   * 커넥터를 설치하는 VM에 대해 Internet Explorer 고급 보안 구성이 켜져 있으면 등록 화면이 차단될 수 있습니다. 액세스를 허용하려면 오류 메시지의 지침을 따르거나 설치 프로세스 중에 Internet Explorer 고급 보안을 끕니다.
   * 커넥터 등록에 실패하면 [응용 프로그램 프록시 문제를 해결합니다.](../active-directory/manage-apps/application-proxy-troubleshoot.md)
1. 설정이 끝나면 아웃바운드 프록시가 있는 환경에 대한 메모가 표시됩니다. 아웃바운드 프록시를 통해 작동하도록 Azure AD 응용 프로그램 프록시 커넥터를 `C:\Program Files\Microsoft AAD App Proxy connector\ConfigureOutBoundProxy.ps1`구성하려면 와 같은 제공된 스크립트를 실행합니다.
1. Azure 포털의 응용 프로그램 프록시 페이지에서 새 커넥터는 다음 예제와 같이 *활성*상태입니다.

    ![Azure 포털에서 활성으로 표시된 새 Azure AD 응용 프로그램 프록시 커넥터](./media/app-proxy/connected-app-proxy.png)

> [!NOTE]
> Azure AD 응용 프로그램 프록시를 통해 인증하는 응용 프로그램에 대한 고가용성을 제공하려면 여러 VM에 커넥터를 설치할 수 있습니다. 이전 섹션에 나열된 동일한 단계를 반복하여 Azure AD DS 관리 도메인에 가입한 다른 서버에 커넥터를 설치합니다.

## <a name="enable-resource-based-kerberos-constrained-delegation"></a>리소스 기반 Kerberos 제한 위임 사용

IWA(통합 Windows 인증)를 사용하여 응용 프로그램에 단일 사인온을 사용하려는 경우 Azure AD 응용 프로그램 프록시 커넥터에 사용자를 가장하고 토큰을 대신하여 보내고 받을 수 있는 권한을 부여합니다. 이러한 권한을 부여하려면 커넥터가 Azure AD DS 관리 도메인의 리소스에 액세스하도록 Kerberos 제한 위임(KCD)을 구성합니다. Azure AD DS 관리 도메인에 도메인 관리자 권한이 없으므로 관리되는 도메인에서 기존 계정 수준 KCD를 구성할 수 없습니다. 대신 리소스 기반 KCD를 사용합니다.

자세한 내용은 [Azure Active Directory 도메인 서비스에서 Kerberos 제한 위임(KCD) 구성을](deploy-kcd.md)참조하십시오.

> [!NOTE]
> 다음 PowerShell cmdlet을 실행하려면 Azure *AD DC 관리자* 그룹의 구성원인 사용자 계정에 로그인해야 합니다.
>
> 앱 프록시 커넥터 VM 및 응용 프로그램 VM에 대한 컴퓨터 계정은 리소스 기반 KCD를 구성할 수 있는 권한이 있는 사용자 지정 OU에 있어야 합니다. 기본 제공 *AAD DC 컴퓨터* 컨테이너의 컴퓨터 계정에 대한 리소스 기반 KCD를 구성할 수 없습니다.

[Get-ADComputer를][Get-ADComputer] 사용하여 Azure AD 응용 프로그램 프록시 커넥터가 설치된 컴퓨터의 설정을 검색합니다. 도메인 에 가입한 관리 VM에서 *Azure AD DC 관리자* 그룹의 구성원인 사용자 계정으로 로그인하여 다음 cmdlet을 실행합니다.

다음 예제는 *appproxy.aaddscontoso.com*라는 컴퓨터 계정에 대 한 정보를 가져옵니다. 이전 단계에서 구성된 Azure AD 응용 프로그램 프록시 VM에 대해 고유한 컴퓨터 이름을 제공합니다.

```powershell
$ImpersonatingAccount = Get-ADComputer -Identity appproxy.aaddscontoso.com
```

Azure AD 응용 프로그램 프록시 뒤에 있는 앱을 실행하는 각 응용 프로그램 서버에 대해 [Set-ADComputer][Set-ADComputer] PowerShell cmdlet을 사용하여 리소스 기반 KCD를 구성합니다. 다음 예제에서는 Azure AD 응용 프로그램 프록시 커넥터에 *appserver.aaddscontoso.com* 컴퓨터를 사용할 수 있는 권한이 부여됩니다.

```powershell
Set-ADComputer appserver.aaddscontoso.com -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
```

여러 Azure AD 응용 프로그램 프록시 커넥터를 배포하는 경우 각 커넥터 인스턴스에 대해 리소스 기반 KCD를 구성해야 합니다.

## <a name="next-steps"></a>다음 단계

Azure AD 응용 프로그램 프록시가 Azure AD DS와 통합된 경우 사용자가 액세스할 수 있도록 응용 프로그램을 게시합니다. 자세한 내용은 [Azure AD 응용 프로그램 프록시를 사용하여 응용 프로그램 게시를](../active-directory/manage-apps/application-proxy-publish-azure-portal.md)참조하세요.

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[azure-bastion]: ../bastion/bastion-create-host-portal.md
[Get-ADComputer]: /powershell/module/addsadministration/get-adcomputer
[Set-ADComputer]: /powershell/module/addsadministration/set-adcomputer
