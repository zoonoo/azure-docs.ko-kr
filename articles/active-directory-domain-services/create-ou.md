---
title: Azure AD Domain Services에 OU(조직 구성 단위) 만들기 | Microsoft Docs'
description: Azure AD Domain Services 관리되는 도메인에서 사용자 지정 OU(조직 구성 단위)를 만들고 관리하는 방법을 알아봅니다.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 52602ad8-2b93-4082-8487-427bdcfa8126
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: fbdfcc23553a27aaa4d7bbd4bff9c4f33cb8fd19
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96620021"
---
# <a name="create-an-organizational-unit-ou-in-an-azure-active-directory-domain-services-managed-domain"></a>Azure Active Directory Domain Services 관리되는 도메인에 OU(조직 구성 단위) 만들기

AD DS(Active Directory Domain Services) 관리되는 도메인에서 OU(조직 구성 단위)를 사용하면 사용자 계정, 서비스 계정 또는 컴퓨터 계정과 같은 개체를 논리적으로 그룹화할 수 있습니다. 그런 다음 특정 OU에 관리자를 할당하고 그룹 정책을 적용하여 대상 구성 설정을 적용할 수 있습니다.

Azure AD DS 관리되는 도메인은 다음과 같은 두 가지 기본 제공 OU를 포함합니다.

* *AADDC 컴퓨터* - 관리되는 도메인에 가입된 모든 컴퓨터에 대한 컴퓨터 개체를 포함합니다.
* *AADDC 사용자* -Azure AD 테넌트에서 동기화된 사용자 및 그룹을 포함합니다.

Azure AD DS를 사용하는 워크로드를 만들고 실행할 때 애플리케이션이 자체적으로 인증할 수 있도록 서비스 계정을 만들어야 할 수 있습니다. 이러한 서비스 계정을 구성하려면 일반적으로 관리되는 도메인에 사용자 지정 OU를 만든 다음 해당 OU 내에 서비스 계정을 만듭니다.

하이브리드 환경에서는 온-프레미스 AD DS 환경에서 만든 OU는 관리되는 도메인에 동기화되지 않습니다. 관리되는 도메인은 플랫 OU 구조를 사용합니다. 온-프레미스에 계층적 OU 구조를 구성한 경우에도, 모든 사용자 계정과 그룹은 다른 온-프레미스 도메인이나 포리스트에서 동기화되더라도 *AADDC 사용자* 컨테이너에 저장됩니다.

이 문서에서는 관리되는 도메인에 OU를 만드는 방법을 보여 줍니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서를 완료하는 데 필요한 리소스와 권한은 다음과 같습니다.

* 활성화된 Azure 구독.
    * Azure 구독이 없는 경우 [계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* 온-프레미스 디렉터리 또는 클라우드 전용 디렉터리와 동기화되어 구독과 연결된 Azure Active Directory 테넌트
    * 필요한 경우 [Azure Active Directory 테넌트를 만들거나][create-azure-ad-tenant][Azure 구독을 계정에 연결합니다][associate-azure-ad-tenant].
* Azure AD 테넌트에서 사용하도록 설정되고 구성된 Azure Active Directory Domain Services 관리되는 도메인
    * 필요한 경우 자습서를 완료하여 [Azure Active Directory Domain Services 관리되는 도메인을 만들고 구성][create-azure-ad-ds-instance]합니다.
* Azure AD DS 관리되는 도메인에 연결된 Windows Server 관리 VM입니다.
    * 필요한 경우 자습서를 완료하여 [관리 VM을 만듭니다][tutorial-create-management-vm].
* Azure AD 테넌트의 *Azure AD DC Administrators* 그룹에 속한 멤버인 사용자 계정

## <a name="custom-ou-considerations-and-limitations"></a>사용자 지정 OU 고려 사항 및 제한 사항

관리되는 도메인에서 사용자 지정 OU를 만드는 경우 사용자 관리 및 그룹 정책 적용을 위한 추가 관리 유연성을 얻을 수 있습니다. 온-프레미스 AD DS 환경에 비해 관리되는 도메인에서 사용자 지정 OU 구조를 만들고 관리하는 경우 몇 가지 제한 사항 및 고려 사항이 있습니다.

* 사용자 지정 OU를 만들려면 사용자가 *AAD DC 관리자* 그룹의 멤버여야 합니다.
* 사용자 지정 OU를 만드는 사용자는 해당 OU에 대한 관리 권한(모든 권한)이 부여되며 리소스 소유자입니다.
    * 기본적으로 *AAD DC 관리자* 그룹에는 사용자 지정 OU에 대한 모든 권한이 있습니다.
* Azure AD 테넌트에서 동기화된 모든 사용자 계정을 포함하는 *AADDC 사용자* 에 대한 기본 OU가 만들어집니다.
    * *AADDC 사용자* OU에서 사용자가 만든 사용자 지정 OU로 사용자 또는 그룹을 이동할 수 없습니다. 관리되는 도메인에서 만든 사용자 계정 또는 리소스만 사용자 지정 OU로 이동할 수 있습니다.
* 사용자 지정 OU에서 만든 사용자 계정, 그룹, 서비스 계정 및 컴퓨터 개체는 Azure AD 테넌트에서 사용할 수 없습니다.
    * 이러한 개체는 Microsoft Graph API 또는 Azure AD UI에 표시되지 않습니다. 관리되는 도메인에서만 확인할 수 있습니다.

## <a name="create-a-custom-ou"></a>사용자 지정 OU 만들기

사용자 지정 OU를 만들려면 도메인에 가입된 VM에서 Active Directory 관리 도구를 사용합니다. Active Directory 관리 센터를 사용하면 OU를 포함하여 관리되는 도메인에서 리소스를 보고, 편집하고, 만들 수 있습니다.

> [!NOTE]
> 관리되는 도메인에서 사용자 지정 OU를 만들려면 *AAD DC Administrators* 그룹의 멤버인 사용자 계정에 로그인해야 합니다.

1. 관리 VM에 로그인합니다. Azure Portal을 사용하여 연결하는 방법에 대한 단계는 [Windows Server VM에 연결][connect-windows-server-vm]을 참조하세요.
1. 시작 화면에서 **관리 도구** 를 선택합니다. [관리 VM을 만들기][tutorial-create-management-vm]위해 자습서에 설치된 사용 가능한 관리 도구 목록이 표시됩니다.
1. OU를 만들고 관리하려면 관리 도구 목록에서 **Active Directory 관리 센터** 를 선택합니다.
1. 왼쪽 창에서 관리되는 도메인(예: *aaddscontoso.com*)을 선택합니다. 다음과 같이 기존 OU 및 리소스 목록이 표시됩니다.

    ![Active Directory 관리 센터에서 관리되는 도메인 선택](./media/create-ou/create-ou-adac-overview.png)

1. **작업** 창은 Active Directory 관리 센터 오른쪽에 표시됩니다. *aaddscontoso.com* 과 같은 도메인에서 **새로 만들기 > 조직 구성 단위** 를 선택합니다.

    ![Active Directory 관리 센터에서 새 OU를 만드는 옵션 선택](./media/create-ou/create-ou-adac-new-ou.png)

1. **조직 구성 단위 만들기** 대화 상자에서 새 OU의 **이름**(예를 들어, *MyCustomOu*)을 지정합니다. *서비스 계정에 대한 사용자 지정 OU* 와 같이 OU에 대한 간단한 설명을 제공합니다. 원하는 경우 OU에 대해 **관리자** 필드를 설정할 수도 있습니다. 사용자 지정 OU를 만들려면 **확인** 을 선택합니다.

    ![Active Directory 관리 센터에서 사용자 지정 OU 만들기](./media/create-ou/create-ou-dialog.png)

1. 다시 Active Directory 관리 센터에서 이제 사용자 지정 OU가 나열되고 사용 가능합니다.

    ![Active Directory 관리 센터에서 사용할 수 있는 사용자 지정 OU](./media/create-ou/create-ou-done.png)

## <a name="next-steps"></a>다음 단계

관리 도구 사용 또는 서비스 계정 만들기 및 사용에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Active Directory 관리 센터: 시작](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd560651(v=ws.10))
* [서비스 계정 단계별 가이드](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd548356(v=ws.10))

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[connect-windows-server-vm]: join-windows-vm.md#connect-to-the-windows-server-vm