---
title: Azure AD 도메인 서비스에서 조직 단위(OU) 만들기 | 마이크로소프트 문서'
description: Azure AD 도메인 서비스 관리 도메인에서 사용자 지정 조직 단위(OU)를 만들고 관리하는 방법을 알아봅니다.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 52602ad8-2b93-4082-8487-427bdcfa8126
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 03/31/2020
ms.author: iainfou
ms.openlocfilehash: 4b95a3e32bc2b8df3d02453e42fa9bbc3719134b
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80519116"
---
# <a name="create-an-organizational-unit-ou-in-an-azure-ad-domain-services-managed-domain"></a>Azure AD 도메인 서비스 관리 도메인에 조직 단위(OU) 만들기

AD DS(Active Directory 도메인 서비스)의 조직 단위(OUs)를 사용하면 사용자 계정, 서비스 계정 또는 컴퓨터 계정과 같은 개체를 논리적으로 그룹화할 수 있습니다. 그런 다음 관리자를 특정 OUs에 할당하고 그룹 정책을 적용하여 대상 구성 설정을 적용할 수 있습니다.

Azure AD DS 관리 도메인에는 다음과 같은 두 가지 기본 제공 US가 포함됩니다.

* *AADDC 컴퓨터* - 관리되는 도메인에 조인된 모든 컴퓨터에 대한 컴퓨터 개체가 포함되어 있습니다.
* *AADDC 사용자* - Azure AD 테넌트에서 동기화된 사용자 및 그룹을 포함합니다.

Azure AD DS를 사용하는 워크로드를 만들고 실행할 때 응용 프로그램이 자신을 인증하기 위한 서비스 계정을 만들어야 할 수 있습니다. 이러한 서비스 계정을 구성하려면 Azure AD DS 관리 도메인에서 사용자 지정 OU를 만든 다음 해당 OU 내에서 서비스 계정을 만듭니다.

하이브리드 환경에서 온-프레미스 AD DS 환경에서 만든 US는 Azure AD DS와 동기화되지 않습니다. Azure AD DS 관리 도메인은 플랫 OU 구조를 사용합니다. 모든 사용자 계정 및 그룹은 계층적 OU 구조를 구성한 경우에도 다른 온-프레미스 도메인 또는 포리스트에서 동기화되더라도 *AADDC Users* 컨테이너에 저장됩니다.

이 문서에서는 Azure AD DS 관리 도메인에서 OU를 만드는 방법을 보여 줍니다.

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
* Azure AD 테넌트의 *Azure AD DC Administrators* 그룹에 속한 멤버인 사용자 계정

## <a name="custom-ou-considerations-and-limitations"></a>사용자 지정 OU 고려 사항 및 제한 사항

Azure AD DS 관리 도메인에서 사용자 지정 OUs를 만들면 사용자 관리 및 그룹 정책을 적용하기 위한 추가관리 유연성을 얻을 수 있습니다. 온-프레미스 AD DS 환경과 비교하여 Azure AD DS에서 사용자 지정 OU 구조를 만들고 관리할 때 몇 가지 제한 사항과 고려 사항이 있습니다.

* 사용자 지정 US를 만들려면 사용자가 *AAD DC 관리자* 그룹의 구성원이어야 합니다.
* 사용자 지정 OU를 만드는 사용자에게는 해당 OU에 대한 관리 권한(전체 제어)이 부여되며 리소스 소유자입니다.
    * 기본적으로 *AAD DC 관리자* 그룹은 사용자 지정 OU를 완전히 제어할 수도 있습니다.
* *AADDC 사용자에* 대한 기본 OU는 Azure AD 테넌트의 모든 동기화된 사용자 계정을 포함하는 만들어집니다.
    * *AADDC Users* OU에서 사용자 또는 그룹을 사용자 지정 OU로 이동할 수 없습니다. Azure AD DS 관리 도메인에서 만든 사용자 계정 또는 리소스만 사용자 지정 OUS로 이동할 수 있습니다.
* 사용자 지정 OUs에서 만든 사용자 계정, 그룹, 서비스 계정 및 컴퓨터 개체는 Azure AD 테넌트에서 사용할 수 없습니다.
    * 이러한 개체는 Microsoft 그래프 API또는 Azure AD UI를 사용하여 표시되지 않습니다. Azure AD DS 관리 도메인에서만 사용할 수 있습니다.

## <a name="create-a-custom-ou"></a>사용자 지정 OU 만들기

사용자 지정 OU를 만들려면 도메인에 가입한 VM의 Active Directory 관리 도구를 사용합니다. Active Directory 관리 센터를 사용하면 OUs를 포함하여 Azure AD DS 관리 도메인에서 리소스를 보고 편집하고 만들 수 있습니다.

> [!NOTE]
> Azure AD DS 관리 도메인에서 사용자 지정 OU를 만들려면 *AAD DC Administrators* 그룹의 구성원인 사용자 계정에 로그인해야 합니다.

1. 관리 VM에 로그인합니다. Azure 포털을 사용하여 연결하는 방법에 대한 단계는 [Windows 서버 VM에 연결 참조][connect-windows-server-vm]
1. 시작 화면에서 관리 **도구를**선택합니다. 사용 가능한 관리 도구 목록이 자습서에 설치된 [관리 VM을 만드는][tutorial-create-management-vm]데 표시됩니다.
1. OUs를 만들고 관리하려면 관리 도구 목록에서 **Active Directory 관리 센터를** 선택합니다.
1. 왼쪽 창에서 *aaddscontoso.com*와 같은 Azure AD DS 관리 도메인을 선택합니다. 기존 US 및 리소스 목록이 표시됩니다.

    ![활성 디렉터리 관리 센터에서 Azure AD DS 관리 도메인 선택](./media/create-ou/create-ou-adac-overview.png)

1. **작업** 창은 활성 디렉터리 관리 센터의 오른쪽에 표시됩니다. *aaddscontoso.com*같은 도메인에서 새 **> 조직 단위를**선택합니다.

    ![활성 디렉터리 관리 센터에서 새 OU를 만드는 옵션을 선택합니다.](./media/create-ou/create-ou-adac-new-ou.png)

1. 조직 **단위 만들기** 대화 상자에서 MyCustomOu 와 같은 새 OU의 **이름을** *지정합니다.* 서비스 계정에 대한 사용자 지정 *OU와*같은 OU에 대한 간략한 설명을 제공합니다. 원하는 경우 OU에 대한 **관리별** 필드를 설정할 수도 있습니다. 사용자 지정 OU를 만들려면 **확인을**선택합니다.

    ![활성 디렉터리 관리 센터에서 사용자 지정 OU 만들기](./media/create-ou/create-ou-dialog.png)

1. Active Directory 관리 센터로 돌아가면 사용자 지정 OU가 나열되어 있으며 사용할 수 있습니다.

    ![Active Directory 관리 센터에서 사용할 수 있는 사용자 지정 OU](./media/create-ou/create-ou-done.png)

## <a name="next-steps"></a>다음 단계

관리 도구 사용 또는 서비스 계정 만들기 및 사용에 대한 자세한 내용은 다음 문서를 참조하십시오.

* [Active Directory 관리 센터: 시작](https://technet.microsoft.com/library/dd560651.aspx)
* [서비스 계정 단계별 가이드](https://technet.microsoft.com/library/dd548356.aspx)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[connect-windows-server-vm]: join-windows-vm.md#connect-to-the-windows-server-vm
