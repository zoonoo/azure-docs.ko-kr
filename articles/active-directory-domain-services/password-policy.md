---
title: Azure AD 도메인 서비스에서 암호 정책 생성 및 사용 | 마이크로 소프트 문서
description: Azure AD DS 관리 도메인에서 계정 암호를 보호하고 제어하기 위해 세분화된 암호 정책을 사용하는 방법과 이유를 알아봅니다.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 1a14637e-b3d0-4fd9-ba7a-576b8df62ff2
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: article
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: cc1863ff4200ee4cca94f18e3adfffa7ea42db96
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80476004"
---
# <a name="password-and-account-lockout-policies-on-managed-domains"></a>관리되는 도메인의 암호 및 계정 잠금 정책

Azure Active Directory 도메인 서비스(Azure AD DS)에서 사용자 보안을 관리하려면 계정 잠금 설정 또는 최소 암호 길이 및 복잡성을 제어하는 세분화된 암호 정책을 정의할 수 있습니다. Azure AD DS 관리 도메인의 모든 사용자에게 기본 세분화된 암호 정책이 만들어지고 적용됩니다. 세부적인 제어를 제공하고 특정 비즈니스 또는 규정 준수 요구 사항을 충족하기 위해 특정 사용자 그룹에 추가 정책을 만들고 적용할 수 있습니다.

이 문서에서는 Active Directory 관리 센터를 사용하여 Azure AD DS에서 세분화된 암호 정책을 만들고 구성하는 방법을 보여 줍니다.

> [!NOTE]
> 암호 정책은 리소스 관리자 배포 모델을 사용하여 만든 Azure AD DS 관리 도메인에만 사용할 수 있습니다. 클래식을 사용하여 만든 이전 관리형 도메인의 경우 [클래식 가상 네트워크 모델에서 리소스 관리자로 마이그레이션합니다.][migrate-from-classic]

## <a name="before-you-begin"></a>시작하기 전에

이 문서를 완료하려면 다음과 같은 리소스와 권한이 필요합니다.

* 활성화된 Azure 구독.
  * Azure 구독이 없는 경우 [계정을 만듭니다.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* 온-프레미스 디렉터리 또는 클라우드 전용 디렉터리와 동기화되어 구독과 연결된 Azure Active Directory 테넌트
  * 필요한 경우 [Azure Active Directory 테넌트를 만들거나][create-azure-ad-tenant][Azure 구독을 계정에 연결합니다][associate-azure-ad-tenant].
* Azure AD 테넌트에서 사용하도록 설정되고 구성된 Azure Active Directory Domain Services 관리되는 도메인
  * 필요한 경우 자습서를 완료하여 [Azure Active Directory 도메인 서비스 인스턴스를 만들고 구성합니다.][create-azure-ad-ds-instance]
  * Azure AD DS 인스턴스는 리소스 관리자 배포 모델을 사용하여 만들어졌어야 합니다. 필요한 경우 [클래식 가상 네트워크 모델에서 리소스 관리자로 마이그레이션합니다.][migrate-from-classic]
* Azure AD DS 관리 도메인에 조인된 Windows 서버 관리 VM입니다.
  * 필요한 경우 자습서를 완료하여 [관리 VM을 만듭니다.][tutorial-create-management-vm]
* Azure AD 테넌트의 *Azure AD DC Administrators* 그룹에 속한 멤버인 사용자 계정

## <a name="default-password-policy-settings"></a>기본 암호 정책 설정

세분화된 암호 정책(FGPP)을 사용하면 도메인의 다른 사용자에게 암호 및 계정 잠금 정책에 대한 특정 제한을 적용할 수 있습니다. 예를 들어 권한 있는 계정을 보호하기 위해 일반 비권한 계정보다 더 엄격한 계정 잠금 설정을 적용할 수 있습니다. Azure AD DS 관리 도메인 내에서 여러 FGP를 만들고 사용자에게 적용할 우선 순위 순서를 지정할 수 있습니다.

정책은 Azure AD DS 관리 도메인의 그룹 연결을 통해 배포되며 변경 한 내용은 다음 사용자 로그인시 적용됩니다. 정책을 변경해도 이미 잠긴 사용자 계정의 잠금이 해제되지 않습니다.

암호 정책은 적용된 사용자 계정이 만들어진 방식에 따라 약간 다르게 행동합니다. Azure AD DS에서 사용자 계정을 만들 수 있는 방법에는 두 가지가 있습니다.

* 사용자 계정은 Azure AD에서 동기화할 수 있습니다. 여기에는 Azure에서 직접 만든 클라우드 전용 사용자 계정과 Azure AD Connect를 사용하여 온-프레미스 AD DS 환경에서 동기화된 하이브리드 사용자 계정이 포함됩니다.
    * Azure AD DS의 대부분의 사용자 계정은 Azure AD의 동기화 프로세스를 통해 만들어집니다.
* 사용자 계정은 Azure AD DS 관리 도메인에서 수동으로 만들 수 있으며 Azure AD에 존재하지 않습니다.

모든 사용자는 생성 방법에 관계없이 Azure AD DS의 기본 암호 정책에 의해 다음 계정 잠금 정책이 적용됩니다.

* **계정 잠금 기간:** 30
* **실패한 로그온 시도 수:** 5
* **실패한 로그온 시도 재설정 후 30분**
* **최대 암호 사용 연령(수명):** 90일

이러한 기본 설정을 사용하면 2분 이내에 5개의 잘못된 암호를 사용하는 경우 사용자 계정이 30분 동안 잠깁니다. 계정은 30분 후 자동으로 잠금 해제됩니다.

계정 잠금은 관리되는 도메인 내에서만 발생합니다. 사용자 계정은 Azure AD DS에서만 잠겨 있으며 관리되는 도메인에 대한 로그인 시도가 실패했기 때문입니다. Azure AD 또는 온-프레미스에서 동기화된 사용자 계정은 Azure AD DS에서만 원본 디렉터리에서 잠기지 않습니다.

최대 암호 사용 연령이 90일을 초과하는 Azure AD 암호 정책이 있는 경우 해당 암호 사용 연령은 Azure AD DS의 기본 정책에 적용됩니다. Azure AD DS에서 다른 최대 암호 나이를 정의하도록 사용자 지정 암호 정책을 구성할 수 있습니다. Azure AD DS 암호 정책에 구성된 최대 암호 사용 기간이 Azure AD 또는 온-프레미스 AD DS 환경보다 짧은 경우 주의해야 합니다. 이 시나리오에서는 사용자의 암호가 Azure AD DS또는 온-프레미스 AD DS 환경에서 변경하라는 메시지가 표시되기 전에 Azure AD DS에서 만료될 수 있습니다.

Azure AD DS 관리 도메인에서 수동으로 만든 사용자 계정의 경우 기본 정책에서 다음과 같은 추가 암호 설정도 적용됩니다. 이러한 설정은 사용자가 Azure AD DS에서 직접 암호를 업데이트할 수 없기 때문에 Azure AD에서 동기화된 사용자 계정에는 적용되지 않습니다.

* **최소 암호 길이(문자):** 7
* **암호는 복잡성을 만족해야 함**

기본 암호 정책에서는 계정 잠금 또는 암호 설정을 수정할 수 없습니다. 대신 *AAD DC Administrators* 그룹의 구성원은 다음 섹션과 같이 사용자 지정 암호 정책을 만들고 기본 기본 제공 정책을 재정의하도록 구성할 수 있습니다.

## <a name="create-a-custom-password-policy"></a>사용자 지정 암호 정책 만들기

Azure에서 응용 프로그램을 빌드하고 실행할 때 사용자 지정 암호 정책을 구성할 수 있습니다. 예를 들어 다른 계정 잠금 정책 설정을 설정하는 정책을 만들 수 있습니다.

사용자 지정 암호 정책은 Azure AD DS 관리 도메인의 그룹에 적용됩니다. 이 구성은 기본 정책을 효과적으로 재정의합니다.

사용자 지정 암호 정책을 만들려면 도메인에 가입한 VM의 Active Directory 관리 도구를 사용합니다. Active Directory 관리 센터를 사용하면 OUs를 포함하여 Azure AD DS 관리 도메인에서 리소스를 보고 편집하고 만들 수 있습니다.

> [!NOTE]
> Azure AD DS 관리 도메인에서 사용자 지정 암호 정책을 만들려면 *AAD DC Administrators* 그룹의 구성원인 사용자 계정에 로그인해야 합니다.

1. 시작 화면에서 관리 **도구를**선택합니다. 사용 가능한 관리 도구 목록이 자습서에 설치된 [관리 VM을 만드는][tutorial-create-management-vm]데 표시됩니다.
1. OUs를 만들고 관리하려면 관리 도구 목록에서 **Active Directory 관리 센터를** 선택합니다.
1. 왼쪽 창에서 *aaddscontoso.com*와 같은 Azure AD DS 관리 도메인을 선택합니다.
1. **시스템** 컨테이너를 연 다음 **암호 설정 컨테이너를**엽니다.

    Azure AD DS 관리 도메인에 대한 기본 제공 암호 정책이 표시됩니다. 이 기본 제공 정책은 수정할 수 없습니다. 대신 사용자 지정 암호 정책을 만들어 기본 정책을 재정의합니다.

    ![Active Directory 관리 센터에서 암호 정책 만들기](./media/password-policy/create-password-policy-adac.png)

1. 오른쪽의 **작업** 패널에서 **암호 > 새 설정을**선택합니다.
1. 암호 **설정 만들기** 대화 상자에서 *MyCustomFGPP*와 같은 정책의 이름을 입력합니다.
1. 여러 암호 정책이 있는 경우 우선 순위가 가장 높은 정책 또는 우선 순위가 있는 정책이 사용자에게 적용됩니다. 번호가 낮을수록 우선 순위가 높습니다. 기본 암호 정책의 우선 순위는 *200입니다.*

    사용자 지정 암호 정책의 우선 순위를 설정하여 기본값(예: *1.)*

1. 원하는 대로 다른 암호 정책 설정을 편집합니다. 다음 핵심 사항을 기억하십시오.

    * Azure AD DS 관리 도메인에서 수동으로 만든 사용자에게만 암호 복잡성, 연령 또는 만료 시간과 같은 설정입니다.
    * 계정 잠금 설정은 모든 사용자에게 적용되지만 Azure AD 자체가 아닌 관리되는 도메인 내에서만 적용됩니다.

    ![사용자 지정 세분화된 암호 정책 만들기](./media/password-policy/custom-fgpp.png)

1. 선택 취소 **실수로 삭제되지 않도록 보호.** 이 옵션을 선택하면 FGPP를 저장할 수 없습니다.
1. 에 **직접 적용** 섹션에서 **추가** 단추를 선택합니다. 사용자 **또는 그룹 선택** 대화 상자에서 **위치** 단추를 선택합니다.

    ![사용자 및 그룹을 선택하여 암호 정책을 적용합니다.](./media/password-policy/fgpp-applies-to.png)

1. 암호 정책은 그룹에만 적용할 수 있습니다. **위치** 대화 상자에서 *aaddscontoso.com*같은 도메인 이름을 확장한 다음 **AADDC 사용자와**같은 OU를 선택합니다. 적용하려는 사용자 그룹을 포함하는 사용자 지정 OU가 있는 경우 해당 OU를 선택합니다.

    ![그룹이 속한 OU를 선택합니다.](./media/password-policy/fgpp-container.png)

1. 정책을 적용할 그룹의 이름을 입력한 다음 **이름 확인을** 선택하여 그룹이 있는지 확인합니다.

    ![FGPP를 적용할 그룹을 검색하고 선택합니다.](./media/password-policy/fgpp-apply-group.png)

1. 선택한 그룹의 이름이 바로 **적용** 섹션에 표시되면 **확인을** 선택하여 사용자 지정 암호 정책을 저장합니다.

## <a name="next-steps"></a>다음 단계

암호 정책 및 Active Directory 관리 센터 사용에 대한 자세한 내용은 다음 문서를 참조하십시오.

* [세분화된 암호 정책에 대해 알아보기](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770394(v=ws.10))
* [AD 관리 센터를 사용하여 세분화된 암호 정책 구성](/windows-server/identity/ad-ds/get-started/adac/introduction-to-active-directory-administrative-center-enhancements--level-100-#fine_grained_pswd_policy_mgmt)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[migrate-from-classic]: migrate-from-classic-vnet.md
