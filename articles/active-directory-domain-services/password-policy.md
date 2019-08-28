---
title: Azure AD Domain Services |에서 암호 정책 만들기 및 사용 Microsoft Docs
description: 세분화 된 암호 정책을 사용 하 여 Azure AD DS 관리 되는 도메인에서 계정 암호를 보호 하 고 제어 하는 방법과 이유를 알아봅니다.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 1a14637e-b3d0-4fd9-ba7a-576b8df62ff2
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: article
ms.date: 08/08/2019
ms.author: iainfou
ms.openlocfilehash: 45fb2daaeaf9ee788207d43d805e070320372ca0
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2019
ms.locfileid: "69617182"
---
# <a name="password-and-account-lockout-policies-on-managed-domains"></a>관리되는 도메인의 암호 및 계정 잠금 정책

Azure Active Directory Domain Services (Azure AD DS)에서 계정 보안을 관리 하려면 최소 암호 길이, 암호 만료 시간 또는 암호 복잡성 등의 설정을 제어 하는 세분화 된 암호 정책을 정의할 수 있습니다. 기본 암호 정책은 Azure AD DS 관리 되는 도메인의 모든 사용자에 게 적용 됩니다. 세부적인 제어를 제공 하 고 특정 비즈니스 또는 규정 준수 요구 사항을 충족 하기 위해 특정 사용자 그룹에 추가 정책을 만들고 적용할 수 있습니다.

이 문서에서는 Active Directory 관리 센터를 사용 하 여 세분화 된 암호 정책을 만들고 구성 하는 방법을 보여 줍니다.

## <a name="before-you-begin"></a>시작하기 전 주의 사항

이 문서를 완료 하려면 다음 리소스와 권한이 필요 합니다.

* 활성화된 Azure 구독.
  * Azure 구독이 없는 경우 [계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* 온-프레미스 디렉터리 또는 클라우드 전용 디렉터리와 동기화 된 구독과 연결 된 Azure Active Directory 테 넌 트.
  * 필요한 경우 [Azure Active Directory 테 넌 트][create-azure-ad-tenant] 를 만들거나 [사용자 계정에 Azure 구독을 연결][associate-azure-ad-tenant]합니다.
* Azure AD 테 넌 트에서 사용 하도록 설정 되 고 구성 된 Azure Active Directory Domain Services 관리 되는 도메인입니다.
  * 필요한 경우 자습서를 완료 하 여 [Azure Active Directory Domain Services 인스턴스를 만들고 구성][create-azure-ad-ds-instance]합니다.
* Azure AD DS 관리 되는 도메인에 가입 된 Windows Server 관리 VM입니다.
  * 필요한 경우 자습서를 완료 하 여 [관리 VM을 만듭니다][tutorial-create-management-vm].
* Azure AD 테 넌 트에서 *AZURE AD DC administrators* 그룹의 구성원 인 사용자 계정

## <a name="fine-grained-password-policies-fgpp-overview"></a>FGPP (세분화 암호 정책) 개요

Fgpp입니다 (세분화 된 암호 정책)를 사용 하면 암호 및 계정 잠금 정책에 대 한 특정 제한을 도메인의 다른 사용자에 게 적용할 수 있습니다. 예를 들어 권한 있는 계정을 보호 하기 위해 일반적인 권한 없는 계정 보다 엄격한 암호 설정을 적용할 수 있습니다. Azure AD DS 관리 되는 도메인 내에서 암호 정책을 지정 하는 여러 Fgpp입니다를 만들 수 있습니다.

FGPP를 사용 하 여 다음 암호 설정을 구성할 수 있습니다.

* 최소 암호 길이
* 암호 기록
* 암호는 복잡성을 만족해야 함
* 최소 암호 사용 기간
* 최대 암호 사용 기간
* 계정 잠금 정책
  * 계정 잠금 기간
  * 허용되는 로그온 시도 실패 횟수
  * 다음 시간 이후 로그온 시도 실패 횟수 다시 설정

FGPP는 Azure AD DS에서 만든 사용자 에게만 영향을 줍니다. Azure AD에서 Azure AD DS 관리 되는 도메인으로 동기화 되는 클라우드 사용자 및 도메인 사용자는 암호 정책의 영향을 받지 않습니다.

정책은 Azure AD DS 관리 되는 도메인의 그룹 연결을 통해 배포 되 고 변경 내용은 다음 사용자 로그인 시 적용 됩니다. 정책을 변경 하면 이미 잠겨 있는 사용자 계정의 잠금이 해제 되지 않습니다.

## <a name="default-fine-grained-password-policy-settings"></a>세분화 되는 기본 암호 정책 설정

Azure AD DS 관리 되는 도메인에서 다음 암호 정책은 기본적으로 구성 되며 모든 사용자에 게 적용 됩니다.

* **최소 암호 길이 (문자):** 7
* **최대 암호 사용 기간 (수명):** 90일
* **암호는 복잡성 요구 사항을 충족 해야 합니다.**

다음 계정 잠금 정책은 기본적으로 구성 됩니다.

* **계정 잠금 기간:** 30
* **허용 되는 실패 한 로그온 시도 횟수:** 5
* **다음 이후 실패 한 로그온 시도 횟수 다시 설정:** 30분

이러한 기본 설정을 사용 하면 2 분 내에 잘못 된 암호 5 개가 사용 되는 경우 사용자 계정이 30 분 동안 잠깁니다. 계정은 30분 후 자동으로 잠금 해제됩니다.

기본 제공 세분화 된 암호 정책은 수정 하거나 삭제할 수 없습니다. 대신, *AAD DC 관리자* 그룹의 구성원은 사용자 지정 fgpp를 만들고 다음 섹션과 같이 기본 제공 되는 기본 제공 fgpp를 재정의 하도록 구성할 수 있습니다.

## <a name="create-a-custom-fine-grained-password-policy"></a>사용자 지정 세분화 된 암호 정책 만들기

Azure에서 및 응용 프로그램을 빌드할 때 사용자 지정 FGPP를 구성 하는 것이 좋습니다. 사용자 지정 FGPP를 만들어야 하는 몇 가지 예를 들어 다른 계정 잠금 정책을 설정 하거나 관리 되는 도메인에 대 한 기본 암호 수명 설정을 구성할 수 있습니다.

사용자 지정 FGPP를 만들어 Azure AD DS 관리 되는 도메인의 특정 그룹에 적용할 수 있습니다. 이 구성은 기본 FGPP를 효과적으로 재정의 합니다. 사용자 지정 세분화 된 암호 정책을 만들어 Azure AD DS 관리 되는 도메인에서 만든 사용자 지정 Ou에 적용할 수도 있습니다.

세분화 된 암호 정책을 만들려면 도메인에 가입 된 VM에서 Active Directory 관리 도구를 사용 합니다. Active Directory 관리 센터를 사용 하면 Ou를 포함 하 여 Azure AD DS 관리 되는 도메인에서 리소스를 보고 편집 하 고 만들 수 있습니다.

> [!NOTE]
> Azure AD DS 관리 되는 도메인에서 세분화 된 암호 정책을 만들려면 *AAD DC Administrators* 그룹의 구성원 인 사용자 계정에 로그인 해야 합니다.

1. 시작 화면에서 **관리 도구**를 선택 합니다. [관리 VM을 만드는][tutorial-create-management-vm]자습서에 설치 된 사용 가능한 관리 도구 목록이 표시 됩니다.
1. Ou를 만들고 관리 하려면 관리 도구 목록에서 **Active Directory 관리 센터** 을 선택 합니다.
1. 왼쪽 창에서 Azure AD DS 관리 되는 도메인 (예: *contoso.com*)을 선택 합니다.
1. 오른쪽의 **작업** 패널에서 **새로 만들기 > 암호 설정**을 선택 합니다.
1. **암호 설정 만들기** 대화 상자에서 *Mycustomfgpp*와 같이 정책의 이름을 입력 합니다. 기본 FGPP ( *200*) (예: *1*)를 재정의 하려면 우선 순위를 적절 하 게 설정 합니다.

    사용자가 이전 *24* 암호와 다른 암호를 만들도록 요구 하도록 **암호 기록 강제 적용** 과 같은 다른 암호 정책 설정을 원하는 대로 편집 합니다.

    ![사용자 지정 세분화 된 암호 정책 만들기](./media/how-to/custom-fgpp.png)

1. **실수로 삭제 되지 않도록 보호를**선택 취소 합니다. 이 옵션을 선택 하면 FGPP를 저장할 수 없습니다.
1. 에 **직접 적용** 섹션에서 **추가** 단추를 선택 합니다. **사용자 또는 그룹 선택** 대화 상자에서 **위치** 단추를 클릭합니다.

    ![암호 정책을 적용할 사용자 및 그룹을 선택 합니다.](./media/how-to/fgpp-applies-to.png)

1. 세분화 된 암호 정책은 그룹에만 적용할 수 있습니다. **위치** 대화 상자에서 도메인 이름 (예: *contoso.com*)을 확장 한 다음 **AADDC 사용자**와 같은 OU를 선택 합니다. 적용 하려는 사용자 그룹을 포함 하는 사용자 지정 OU가 있는 경우 해당 OU를 선택 합니다.

    ![그룹이 속하는 OU를 선택 합니다.](./media/how-to/fgpp-container.png)

1. 정책을 적용할 그룹의 이름을 입력 하 고 **이름 확인** 을 선택 하 여 그룹이 존재 하는지 확인 합니다.

    ![FGPP를 적용할 그룹을 검색 하 고 선택 합니다.](./media/how-to/fgpp-apply-group.png)

1. 이제 선택한 그룹의 이름이에 **직접 적용** 됨 섹션에 표시 되 면 **확인** 을 선택 하 여 사용자 지정 암호 정책을 저장 합니다.

## <a name="next-steps"></a>다음 단계

세분화 된 암호 정책 및 Active Directory 관리 센터 사용에 대 한 자세한 내용은 다음 문서를 참조 하세요.

* [세분화 되는 암호 정책에 대 한 자세한 정보](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770394(v=ws.10))
* [AD 관리 센터를 사용 하 여 세분화 되는 암호 정책 구성](/windows-server/identity/ad-ds/get-started/adac/introduction-to-active-directory-administrative-center-enhancements--level-100-#fine_grained_pswd_policy_mgmt)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
