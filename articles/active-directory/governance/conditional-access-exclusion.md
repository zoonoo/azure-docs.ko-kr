---
title: 조건부 액세스 정책에서 제외 된 사용자 관리-Azure AD
description: Azure AD (Azure Active Directory) 액세스 검토를 사용 하 여 조건부 액세스 정책에서 제외 된 사용자를 관리 하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/24/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 91bb5a342eea079b6e9abcf109ad472151d3c13d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82144494"
---
# <a name="use-azure-ad-access-reviews-to-manage-users-excluded-from-conditional-access-policies"></a>Azure AD 액세스 검토를 사용 하 여 조건부 액세스 정책에서 제외 된 사용자 관리

이상적인 세계에서는 모든 사용자가 액세스 정책을 따라 조직의 리소스에 안전 하 게 액세스할 수 있습니다. 그러나 경우에 따라 예외가 있는 비즈니스 사례가 있습니다. 이 문서에서는 제외가 필요할 수 있는 상황의 몇 가지 예를 설명 합니다. IT 관리자는이 작업을 관리 하 고, 정책 예외를 감독 하지 않으며, 감사자에 게 Azure AD (Azure Active Directory) 액세스 검토를 사용 하 여 이러한 예외를 정기적으로 검토 하는 증거를 제공할 수 있습니다.

>[!NOTE]
> Azure AD 액세스 검토를 사용하는 데 유효한 Azure AD Premium P2, Enterprise Mobility + Security E5 유료 또는 평가판 라이선스가 필요합니다. 자세한 내용은 [Azure Active Directory 버전](../fundamentals/active-directory-whatis.md)을 참조하세요.

## <a name="why-would-you-exclude-users-from-policies"></a>정책에서 사용자를 제외하는 이유는?

관리자는 [AZURE AD 조건부 액세스](../conditional-access/overview.md) 를 사용 하 여 MFA (multi-factor authentication)를 요구 하 고 특정 네트워크 또는 장치에 대 한 인증 요청을 제한 하는 경우를 가정해 보겠습니다. 배포 계획 중에는 모든 사용자가 이러한 요구 사항을 충족 하지 못할 수 있습니다. 예를 들어, 내부 네트워크의 일부가 아니라 원격 사무실에서 작업 하는 사용자가 있을 수 있습니다. 또한 지원 되지 않는 장치를 사용 하 여 연결 하는 사용자에 게 이러한 장치를 교체할 때까지 기다리는 것이 필요할 수 있습니다. 간단히 말해서 비즈니스는 이러한 사용자에 게 로그인 하 고 작업을 수행 하 여 조건부 액세스 정책에서 제외할 수 있도록 요구 합니다.

또 다른 예로, 조건부 액세스의 [명명 된 위치](../conditional-access/location-condition.md) 를 사용 하 여 사용자가 테 넌 트에 액세스 하지 못하도록 할 국가 및 지역 집합을 지정할 수 있습니다.

![조건부 액세스의 명명 된 위치](./media/conditional-access-exclusion/named-locations.png)

불행 하 게도 일부 사용자는 이러한 차단 된 국가/지역에서 로그인 하는 데 유효한 이유가 있을 수 있습니다. 예를 들어 사용자가 회사 리소스에 액세스 해야 하는 작업을 이동할 수 있습니다. 이 경우 이러한 국가/지역을 차단 하는 조건부 액세스 정책은 정책에서 제외 된 사용자에 게 클라우드 보안 그룹을 사용할 수 있습니다. 여행하는 동안 액세스해야 하는 사용자는 [Azure AD 셀프 서비스 그룹 관리](../users-groups-roles/groups-self-service-management.md)를 사용하여 그룹에 자신을 추가할 수 있습니다.

또 다른 예로는 [대다수의 사용자에 대 한 레거시 인증을 차단](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/07/azure-ad-conditional-access-support-for-blocking-legacy-auth-is-in-public-preview/)하는 조건부 액세스 정책이 있는 경우를 들 수 있습니다. 그러나 일부 사용자가 기존 인증 방법을 사용 하 여 Office 2010 또는 IMAP/SMTP/POP 기반 클라이언트를 통해 리소스에 액세스 해야 하는 경우 레거시 인증 방법을 차단 하는 정책에서 이러한 사용자를 제외할 수 있습니다.

>[!NOTE]
>보안 태세를 개선하기 위해 테넌트에서 레거시 프로토콜의 사용을 차단하는 것이 좋습니다.

## <a name="why-are-exclusions-challenging"></a>제외하기 어려운 이유는?

Azure AD에서 조건부 액세스 정책의 범위를 사용자 집합으로 지정할 수 있습니다. Azure AD 역할, 개별 사용자 또는 게스트를 선택 하 여 제외를 구성할 수도 있습니다. 제외를 구성 하면 제외 된 사용자에 게 정책 의도가 적용 되지 않는다는 점에 유의 해야 합니다. 사용자 목록을 사용 하거나 레거시 온-프레미스 보안 그룹을 사용 하 여 제외를 구성 하는 경우 제외를 제한적으로 볼 수 있습니다. 결과는 다음과 같습니다.

- 사용자가 제외 된 것을 알지 못할 수 있습니다.

- 사용자는 정책을 무시 하기 위해 보안 그룹에 가입할 수 있습니다.

- 제외 된 사용자는 이전 제외에 한정 될 수 있지만 더 이상 한정 되지 않을 수 있습니다.

일반적으로 제외를 처음 구성할 때 정책을 우회 하는 사용자의 넣도록이 있습니다. 시간이 지남에 따라 더 많은 사용자가 제외에 추가 되 고 목록이 증가 합니다. 특정 시점에서 목록을 검토 하 고 이러한 각 사용자가 여전히 제외 대상이 되는지 확인 해야 합니다. 기술적인 관점에서 제외 목록을 관리 하는 작업은 비교적 쉽지만 비즈니스 의사 결정을 내리는 방법 및 감사를 모두 확인 하는 방법은 무엇 인가요? 그러나 Azure AD 그룹을 사용 하 여 제외를 구성 하는 경우 액세스 검토를 보정 컨트롤로 사용 하 여 표시 유형을 구동 하 고 제외 된 사용자 수를 줄일 수 있습니다.

## <a name="how-to-create-an-exclusion-group-in-a-conditional-access-policy"></a>조건부 액세스 정책에서 제외 그룹을 만드는 방법

새 Azure AD 그룹 및 해당 그룹에 적용 되지 않는 조건부 액세스 정책을 만들려면 다음 단계를 수행 합니다.

### <a name="create-an-exclusion-group"></a>제외 그룹 만들기

1. Azure Portal에 로그인합니다.

2. 왼쪽 탐색에서 **Azure Active Directory**를 클릭한 다음, **그룹**을 클릭합니다.

3. 위쪽 메뉴에서 **새 그룹**을 클릭하여 그룹 창을 엽니다.

4. **그룹 형식** 목록에서 **보안**을 선택합니다. 이름 및 설명을 지정합니다.

5. **멤버 자격** 형식을 **할당됨**으로 설정해야 합니다.

6. 이 제외 그룹의 일부인 사용자를 선택한 다음, **만들기**를 클릭합니다.

![Azure Active Directory의 새 그룹 창](./media/conditional-access-exclusion/new-group.png)

### <a name="create-a-conditional-access-policy-that-excludes-the-group"></a>그룹을 제외 하는 조건부 액세스 정책 만들기

이제이 제외 그룹을 사용 하는 조건부 액세스 정책을 만들 수 있습니다.

1. 왼쪽 탐색 영역에서 **Azure Active Directory** 클릭 하 고 **조건부 액세스** 를 클릭 하 여 **정책** 블레이드를 엽니다.

2. **새 정책**을 클릭하여 **새로 만들기** 창을 엽니다.

3. 이름을 지정합니다.

4. 할당 아래에서 **사용자 및 그룹**을 클릭합니다.

5. **포함** 탭에서 **모든 사용자**를 선택합니다.

6. **제외** 탭에서 확인 표시를 **사용자 및 그룹** 에 추가한 다음 **제외 된 사용자 선택**을 클릭 합니다.

7. 만든 제외 그룹을 선택합니다.

   > [!NOTE] 
   > 모범 사례로 테넌트가 잠기지 않았는지 테스트하는 경우 정책에서 하나 이상의 관리자 계정을 제외하는 것이 좋습니다.

8. 조직의 요구 사항에 따라 조건부 액세스 정책 설정을 계속 합니다.

![조건부 액세스에서 제외 된 사용자 창 선택](./media/conditional-access-exclusion/select-excluded-users.png)
  
액세스 검토를 사용 하 여 조건부 액세스 정책에서 제외를 관리할 수 있는 두 가지 예를 살펴보겠습니다.

## <a name="example-1-access-review-for-users-accessing-from-blocked-countriesregions"></a>예 1: 차단 된 국가/지역에서 액세스 하는 사용자에 대 한 액세스 검토

특정 국가/지역에서 액세스를 차단 하는 조건부 액세스 정책이 있다고 가정해 보겠습니다. 여기에는 정책에서 제외된 그룹이 포함됩니다. 그룹의 멤버를 검토하는 권장되는 액세스 검토는 다음과 같습니다.

> [!NOTE] 
> 전역 관리자 또는 사용자 관리자 역할은 액세스 검토를 만드는 데 필요 합니다.

1. 검토는 매주 수행 됩니다.

2. 는이 제외 그룹을 최신 상태로 유지 하기 위해 끝나지 않습니다.

3. 이 그룹의 모든 멤버는 검토할 범위에 있습니다.

4. 각 사용자는 이러한 차단 된 국가/지역에서 계속 액세스 해야 하는 자체를 증명 해야 하므로 그룹의 구성원 이어야 합니다.

5. 사용자가 검토 요청에 응답 하지 않으면 해당 사용자는 그룹에서 자동으로 제거 되 고 해당 국가/지역으로 이동 하는 동안 더 이상 테 넌 트에 액세스할 수 없게 됩니다.

6. 전자 메일 알림을 사용 하도록 설정 하 여 사용자에 게 액세스 검토의 시작과 완료를 알려 주세요.

    ![예제 1에 대 한 액세스 검토 창 만들기](./media/conditional-access-exclusion/create-access-review-1.png)

## <a name="example-2-access-review-for-users-accessing-with-legacy-authentication"></a>예제 2: 레거시 인증을 사용하여 액세스하는 사용자에 대한 액세스 검토

레거시 인증 및 이전 버전의 클라이언트 버전을 사용 하는 사용자에 대 한 액세스를 차단 하는 조건부 액세스 정책이 있고 정책에서 제외 되는 그룹이 포함 되어 있다고 가정해 보겠습니다. 그룹의 멤버를 검토하는 권장되는 액세스 검토는 다음과 같습니다.

1. 이 검토는 되풀이 검토여야 합니다.

2. 그룹의 모든 사용자가 검토되어야 합니다.

3. 비즈니스 단위 소유자를 선택한 검토자로 나열하도록 구성할 수 없습니다.

4. 결과를 자동으로 적용하고 레거시 인증 방법을 계속 사용하도록 승인되지 않은 사용자를 제거합니다.

5. 대규모 그룹의 검토자가 쉽게 결정할 수 있도록 권장 사항을 사용하도록 설정하는 것이 유용할 수 있습니다.

6. 사용자가 액세스 검토의 시작 및 완료를 알 수 있도록 메일 알림을 사용합니다.

    ![예제 2에 대 한 액세스 검토 창 만들기](./media/conditional-access-exclusion/create-access-review-2.png)

>[!IMPORTANT] 
>제외 그룹이 많고 여러 액세스 검토를 만들어야 하는 경우 이제 Microsoft Graph beta 끝점에서 프로그래밍 방식으로 만들고 관리할 수 있는 API가 있습니다. 시작하려면 [Azure AD 액세스 검토 API 참조](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/accessreviews_root) 및 [Microsoft Graph를 통해 Azure AD 액세스 검토를 검색하는 예제](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/td-p/236096)를 참조하세요.

## <a name="access-review-results-and-audit-logs"></a>액세스 검토 결과 및 감사 로그

이제 모든 항목, 그룹, 조건부 액세스 정책 및 액세스 검토를 했으므로 이러한 검토 결과를 모니터링 하 고 추적할 수 있습니다.

1. Azure Portal에서 **액세스 검토** 블레이드를 엽니다.

2. 제외 그룹을 관리하기 위해 만든 컨트롤 및 프로그램을 엽니다.

3. **결과**를 클릭하여 목록에 유지되도록 승인된 사용자 및 제거된 사용자를 확인합니다.

    ![액세스 검토 결과는 승인 된 사용자를 표시 합니다.](./media/conditional-access-exclusion/access-reviews-results.png)

4. 그런 다음, **감사 로그**를 클릭하여 검토하는 동안 수행된 작업을 확인합니다.

    ![액세스 검토 감사 로그 목록 작업](./media/conditional-access-exclusion/access-reviews-audit-logs.png)

IT 관리자인 경우 경우에 따라 정책에 대한 제외 그룹을 관리하는 작업을 피할 수 없습니다. 그러나 이러한 그룹을 유지 관리 하 고, 비즈니스 소유자나 사용자 스스로 정기적으로 검토 하 고, 이러한 변경 내용을 감사 하는 작업은 Azure AD 액세스 검토를 통해 보다 쉽게 수행할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [그룹 또는 응용 프로그램에 대 한 액세스 검토 만들기](create-access-review.md)
- [Azure Active Directory의 조건부 액세스란?](../conditional-access/overview.md)
