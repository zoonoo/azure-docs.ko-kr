---
title: Azure AD 도메인 서비스에서 원격 VM 액세스 보안 | 마이크로 소프트 문서
description: Azure Active Directory 도메인 관리 도메인의 원격 데스크톱 서비스 배포를 사용하여 NPS(네트워크 정책 서버) 및 Azure 다단계 인증을 사용하여 VM에 대한 원격 액세스를 보호하는 방법을 알아봅니다.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: 8bc36dfdf3010b2bde485228f6ee110b0b826d31
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654760"
---
# <a name="secure-remote-access-to-virtual-machines-in-azure-active-directory-domain-services"></a>Azure Active Directory 도메인 서비스에서 가상 시스템에 대한 원격 액세스 보안

Azure Active Directory 도메인 서비스(Azure AD DS) 관리 도메인에서 실행되는 VM(가상 컴퓨터)에 대한 원격 액세스를 보호하려면 RDS(원격 데스크톱 서비스) 및 NPS(네트워크 정책 서버)를 사용할 수 있습니다. Azure AD DS는 사용자가 RDS 환경을 통해 액세스를 요청할 때 사용자를 인증합니다. 보안을 강화하려면 Azure 다단계 인증을 통합하여 로그인 이벤트 중에 추가 인증 프롬프트를 제공할 수 있습니다. Azure 다단계 인증은 NPS에 대한 확장을 사용하여 이 기능을 제공합니다.

> [!IMPORTANT]
> Azure AD DS 관리 도메인의 VM에 안전하게 연결하는 권장 방법은 가상 네트워크 내에서 프로비전하는 완전 플랫폼 관리 PaaS 서비스인 Azure Bastion을 사용하는 것입니다. 대위 호스트는 SSL을 통해 Azure 포털에서 직접 VM에 안전하고 원활한 RDP(원격 데스크톱 프로토콜) 연결을 제공합니다. 배스턴 호스트를 통해 연결할 때 VM은 공용 IP 주소가 필요하지 않으며 네트워크 보안 그룹을 사용하여 TCP 포트 3389에서 RDP에 대한 액세스를 노출할 필요가 없습니다.
>
> 지원되는 모든 리전에서 Azure 요새를 사용하는 것이 좋습니다. Azure 요새 가용성이 없는 지역에서는 Azure 요새를 사용할 수 있게 될 때까지 이 문서에서 자세히 설명한 단계를 따릅니다. 들어오는 모든 RDP 트래픽이 허용되는 Azure AD DS에 합류한 VM에 공용 IP 주소를 할당하는 데 주의하십시오.
>
> 자세한 내용은 [Azure 요새란 무엇입니까?][bastion-overview]

이 문서에서는 Azure AD DS에서 RDS를 구성하고 선택적으로 Azure 다단계 인증 NPS 확장을 사용하는 방법을 보여 주며, 선택적으로 사용합니다.

![RDS(원격 데스크톱 서비스) 개요](./media/enable-network-policy-server/remote-desktop-services-overview.png)

## <a name="prerequisites"></a>사전 요구 사항

이 문서를 완료하려면 다음 리소스가 필요합니다.

* 활성화된 Azure 구독.
    * Azure 구독이 없는 경우 [계정을 만듭니다.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* 온-프레미스 디렉터리 또는 클라우드 전용 디렉터리와 동기화되어 구독과 연결된 Azure Active Directory 테넌트
    * 필요한 경우 [Azure Active Directory 테넌트를 만들거나][create-azure-ad-tenant][Azure 구독을 계정에 연결합니다][associate-azure-ad-tenant].
* Azure AD 테넌트에서 사용하도록 설정되고 구성된 Azure Active Directory Domain Services 관리되는 도메인
    * 필요한 경우 [Azure Active Directory Domain Services 인스턴스를 만들고 구성합니다][create-azure-ad-ds-instance].
* Azure Active Directory 도메인 서비스 가상 네트워크에서 만든 *워크로드* 서브넷입니다.
    * 필요한 경우 [Azure Active Directory 도메인 서비스 관리 도메인에 대한 가상 네트워킹 구성.][configure-azureadds-vnet]
* Azure AD 테넌트의 *Azure AD DC Administrators* 그룹에 속한 멤버인 사용자 계정

## <a name="deploy-and-configure-the-remote-desktop-environment"></a>원격 데스크톱 환경 배포 및 구성

시작하려면 Windows Server 2016 또는 Windows Server 2019를 실행하는 최소 두 개의 Azure VM을 만듭니다. 원격 데스크톱(RD) 환경의 중복성 및 고가용성을 위해 나중에 추가 호스트를 추가하고 로드균형을 조정할 수 있습니다.

제안된 RDS 배포에는 다음 두 VM이 포함됩니다.

* *RDGVM01* - RD 연결 브로커 서버, RD 웹 액세스 서버 및 RD 게이트웨이 서버를 실행합니다.
* *RDSHVM01* - RD 세션 호스트 서버를 실행합니다.

VM이 Azure AD DS 가상 네트워크의 *워크로드* 서브넷에 배포되었는지 확인한 다음 VM을 Azure AD DS 관리 도메인에 조인합니다. 자세한 내용은 [Windows 서버 VM을 만들고 Azure AD DS 관리 도메인에 가입하는][tutorial-create-join-vm]방법을 참조하세요.

RD 환경 배포에는 여러 단계가 포함되어 있습니다. 기존 RD 배포 가이드는 Azure AD DS 관리 도메인에서 사용할 특정 변경 사항 없이 사용할 수 있습니다.

1. *Contosoadmin*과 같은 *Azure AD DC 관리자* 그룹의 일부인 계정을 사용하여 RD 환경에 대해 만든 VM에 로그인합니다.
1. RDS를 만들고 구성하려면 기존 [원격 데스크톱 환경 배포 가이드를][deploy-remote-desktop]사용합니다. 원하는 대로 Azure VM에 RD 서버 구성 요소를 배포합니다.
1. 웹 브라우저를 사용하여 액세스를 제공하려면 [사용자에 대한 원격 데스크톱 웹 클라이언트를 설정합니다.][rd-web-client]

Rd를 Azure AD DS 관리 도메인에 배포하면 온-프레미스 AD DS 도메인과 마찬가지로 서비스를 관리하고 사용할 수 있습니다.

## <a name="deploy-and-configure-nps-and-the-azure-mfa-nps-extension"></a>NPS 및 Azure MFA NPS 확장 배포 및 구성

사용자 로그인 환경의 보안을 강화하려는 경우 선택적으로 Rd 환경을 Azure 다단계 인증과 통합할 수 있습니다. 이 구성을 사용하면 로그인하는 동안 추가 프롬프트를 수신하여 ID를 확인합니다.

이 기능을 제공하기 위해 Azure 다단계 인증 NPS 확장과 함께 사용자 환경에 추가 네트워크 정책 서버(NPS)가 설치됩니다. 이 확장은 Azure AD와 통합되어 다단계 인증 프롬프트의 상태를 요청하고 반환합니다.

추가 Azure AD 라이선스가 필요할 수 있는 [Azure 다단계 인증을 사용하려면][user-mfa-registration]사용자를 등록해야 합니다.

Azure 다단계 인증을 Azure AD DS 원격 데스크톱 환경에 통합하려면 NPS 서버를 만들고 확장을 설치합니다.

1. Azure AD DS 가상 네트워크의 워크로드 서브넷에 연결된 *NPSVM01과*같은 추가 Windows Server 2016 또는 2019 *VM을* 만듭니다. VM을 Azure AD DS 관리 도메인에 조인합니다.
1. *contosoadmin*과 같은 *Azure AD DC 관리자* 그룹의 일부인 계정으로 NPS VM에 로그인합니다.
1. **서버 관리자에서** **역할 및 기능 추가를**선택한 다음 *네트워크 정책 및 액세스 서비스* 역할을 설치합니다.
1. 기존 방법 문서를 사용하여 [Azure MFA NPS 확장을 설치하고 구성합니다.][nps-extension]

NPS 서버와 Azure 다단계 인증 NPS 확장이 설치되면 다음 섹션을 완료하여 RD 환경에서 사용하도록 구성합니다.

## <a name="integrate-remote-desktop-gateway-and-azure-multi-factor-authentication"></a>원격 데스크톱 게이트웨이 및 Azure 다단계 인증 통합

Azure 다단계 인증 NPS 확장을 통합하려면 기존 방법 문서를 사용하여 [NPS(네트워크 정책 서버) 확장 및 Azure AD를 사용하여 원격 데스크톱 게이트웨이 인프라를 통합합니다.][azure-mfa-nps-integration]

Azure AD DS 관리 도메인과 통합하려면 다음과 같은 추가 구성 옵션이 필요합니다.

1. [활성 디렉터리에서 NPS 서버를 등록하지][register-nps-ad]마십시오. 이 단계는 Azure AD DS 관리 도메인에서 실패합니다.
1. [4단계에서 네트워크 정책을 구성하려면][create-nps-policy] **사용자 계정 전화 접속 속성 무시**확인란을 선택합니다.
1. NPS 서버 및 Azure 다단계 인증 NPS 확장에 Windows Server 2019를 사용하는 경우 다음 명령을 실행하여 보안 채널을 업데이트하여 NPS 서버가 올바르게 통신할 수 있도록 합니다.

    ```powershell
    sc sidtype IAS unrestricted
    ```

이제 사용자는 Microsoft 인증자 앱에서 문자 메시지 또는 프롬프트와 같이 로그인할 때 추가 인증 요소를 묻는 메시지가 표시됩니다.

## <a name="next-steps"></a>다음 단계

배포의 복원력 향상에 대한 자세한 내용은 [원격 데스크톱 서비스 - 고가용성][rds-high-availability]을 참조하십시오.

사용자 로그인 보안에 대한 자세한 내용은 [작동 방식: Azure 다단계 인증][concepts-mfa]을 참조하십시오.

<!-- INTERNAL LINKS -->
[bastion-overview]: ../bastion/bastion-overview.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[configure-azureadds-vnet]: tutorial-configure-networking.md
[tutorial-create-join-vm]: join-windows-vm.md
[user-mfa-registration]: ../active-directory/authentication/howto-mfa-nps-extension.md#register-users-for-mfa
[nps-extension]: ../active-directory/authentication/howto-mfa-nps-extension.md
[azure-mfa-nps-integration]: ../active-directory/authentication/howto-mfa-nps-extension-rdg.md
[register-nps-ad]:../active-directory/authentication/howto-mfa-nps-extension-rdg.md#register-server-in-active-directory
[create-nps-policy]: ../active-directory/authentication/howto-mfa-nps-extension-rdg.md#configure-network-policy
[concepts-mfa]: ../active-directory/authentication/concept-mfa-howitworks.md

<!-- EXTERNAL LINKS -->
[deploy-remote-desktop]: https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure
[rd-web-client]: https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client-admin
[rds-high-availability]: https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-plan-high-availability
