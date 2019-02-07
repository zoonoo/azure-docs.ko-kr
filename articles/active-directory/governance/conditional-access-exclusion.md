---
title: Azure AD 액세스 검토를 사용하여 조건부 액세스 정책에서 제외된 사용자 관리 | Microsoft Docs
description: Azure AD(Azure Active Directory) 액세스 검토를 사용하여 조건부 액세스 정책에서 제외된 사용자를 관리합니다.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 09/25/2018
ms.author: rolyon
ms.reviewer: mwahl
ms.openlocfilehash: 12a9602ecd61da5dc497790ecca6d8d6f874252e
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55184294"
---
# <a name="use-azure-ad-access-reviews-to-manage-users-that-have-been-excluded-from-conditional-access-policies"></a>Azure AD 액세스 검토를 사용하여 조건부 액세스 정책에서 제외된 사용자 관리

이상적인 환경에서 모든 사용자는 조직의 리소스에 대한 액세스를 보호하는 액세스 정책을 따릅니다. 그러나 경우에 따라 예외가 있는 비즈니스 사례가 있습니다. 이 문서에서는 제외되어야 하는 경우 및 IT 관리자로서 Azure AD(Azure Active Directory) 액세스 검토를 사용하여 이 작업을 관리하고, 정책 예외의 감독을 방지하고, 감사자에게 정기적으로 이러한 예외를 검토했다는 증명을 제공하는 방법에 대한 몇 가지 예제를 설명합니다.

> [!NOTE]
> Azure AD 액세스 검토를 사용하는 데 유효한 Azure AD Premium P2, Enterprise Mobility + Security E5 유료 또는 평가판 라이선스가 필요합니다. 자세한 내용은 [Azure Active Directory 버전](../fundamentals/active-directory-whatis.md)을 참조하세요.

## <a name="why-would-you-exclude-users-from-policies"></a>정책에서 사용자를 제외하는 이유는?

IT 관리자 권한으로 [Azure AD 조건부 액세스](../conditional-access/overview.md)를 사용하여 사용자가 MFA(다단계 인증)을 사용하여 인증하거나 신뢰할 수 있는 네트워크 또는 디바이스에서 로그인하도록 할 수 있습니다. 배포 계획 중에 일부 사용자가 이러한 일부 요구 사항을 충족할 수 없다는 점을 알게 되었습니다. 예를 들어, 내부 네트워크의 일부가 아닌 원격 사무실에서 작업하는 사용자가 있거나 지원되지 않는 이전 전화를 사용하는 경영진이 있습니다. 비즈니스에서는 이러한 사용자가 로그인하고 작업을 수행할 수 있어야 하므로 해당 사용자는 조건부 액세스 정책에서 제외됩니다.

또 다른 예로 조건부 액세스에서 [명명된 위치](../conditional-access/location-condition.md)를 사용하여 사용자가 해당 테넌트에 액세스할 수 있도록 하지 않으려는지방 및 지역 집합을 구성할 수 있습니다.

![명명된 위치](./media/conditional-access-exclusion/named-locations.png)

그러나 경우에 따라 사용자에게는 이러한 차단된 국가에서 로그인할 정당한 이유가 있을 수 있습니다. 예를 들어 사용자가 출장이나 개인적인 여행 중일 수도 있습니다. 이 예제에서 이러한 국가를 차단하는 조건부 액세스 정책에는 정책에서 제외된 사용자 전용 클라우드 보안 그룹이 있을 수 있습니다. 여행하는 동안 액세스해야 하는 사용자는 [Azure AD 셀프 서비스 그룹 관리](../users-groups-roles/groups-self-service-management.md)를 사용하여 그룹에 자신을 추가할 수 있습니다.

또 다른 예로 [대부분의 사용자에 대해 레거시 인증을 차단하는](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/07/azure-ad-conditional-access-support-for-blocking-legacy-auth-is-in-public-preview/) 조건부 액세스 정책이 있는 경우를 들 수 있습니다. 보안 태세를 개선하기 위해 테넌트에서 레거시 프로토콜의 사용을 차단하는 것이 좋습니다. 그러나 일부 사용자가 반드시 레거시 인증 방법을 사용하여 Office 2010 또는 POP/IMAP/SMTP 기반 클라이언트를 통해 리소스에 액세스해야 하는 경우 이러한 사용자를 레거시 인증 방법을 차단하는 정책에서 제외할 수 있습니다.

## <a name="why-are-exclusions-challenging"></a>제외하기 어려운 이유는?

Azure AD에서는 사용자 집합에 조건부 액세스 정책의 범위를 지정할 수 있습니다. 디렉터리 역할, 개별 사용자 또는 게스트 사용자를 선택하여 이러한 사용자 중 일부를 제외할 수도 있습니다. 이러한 제외를 구성하는 경우 정책 의도는 해당 사용자에게 적용될 수 없다는 점에 유의하세요. 이러한 제외를 개별 사용자의 목록으로 구성하거나 레거시 온-프레미스 보안 그룹을 통해 구성한 경우 이 제외 목록의 가시성(사용자가 존재 여부를 모를 수 있음) 및 IT 관리자의 제어(사용자가 보안 그룹을 조인하여 정책을 무시할 수 있음)를 제한합니다. 또한 일회성 제외로 한정된 사용자가 더 이상 이 제한을 필요로 하지 않거나 적합하지 않을 수도 있습니다.

제외를 시작할 때 정책을 무시하는 사용자의 짧은 목록이 있습니다. 시간이 지남에 따라 점점 더 많은 사용자가 제외되면 이 목록이 증가합니다. 특정 시점에 해당 목록을 검토하고 이러한 사용자 각각이 여전히 제외되어야 하는지 확인해야 합니다. 기술 관점에서 목록을 관리하는 것은 비교적 간단하지만 비즈니스 의사 결정을 하는 사용자는 누구이고 감사 가능하도록 만드는 방법은 무엇인가요?

그러나 Azure AD 그룹을 사용하여 조건부 액세스 정책에 대한 제외를 구성하는 경우 가시성을 위해 액세스 검토를 보정 컨트롤로 사용하고, 예외가 있는 사용자 수를 줄일 수 있습니다.

## <a name="how-to-create-an-exclusion-group-in-a-conditional-access-policy"></a>조건부 액세스 정책에서 제외 그룹을 만드는 방법

이러한 단계를 따라 해당 그룹에 적용되지 않는 Azure AD 그룹 및 조건부 액세스 정책을 만듭니다.

### <a name="create-an-exclusion-group"></a>제외 그룹 만들기

1. Azure 포털에 로그인합니다.

1. 왼쪽 탐색에서 **Azure Active Directory**를 클릭한 다음, **그룹**을 클릭합니다.

1. 위쪽 메뉴에서 **새 그룹**을 클릭하여 그룹 창을 엽니다.

1. **그룹 형식** 목록에서 **보안**을 선택합니다. 이름 및 설명을 지정합니다.

1. **멤버 자격** 형식을 **할당됨**으로 설정해야 합니다.

1. 이 제외 그룹의 일부인 사용자를 선택한 다음, **만들기**를 클릭합니다.

    ![새 그룹 창](./media/conditional-access-exclusion/new-group.png)

### <a name="create-a-conditional-access-policy-that-excludes-the-group"></a>그룹을 제외하는 조건부 액세스 정책 만들기

이제 이 제외 그룹을 사용하는 조건부 액세스 정책을 만들 수 있습니다.

1. 왼쪽 탐색에서 **Azure Active Directory**를 클릭한 다음, **조건부 액세스**를 클릭하여 **정책** 블레이드를 엽니다.

1. **새 정책**을 클릭하여 **새로 만들기** 창을 엽니다.

1. 이름을 지정합니다.

1. 할당 아래에서 **사용자 및 그룹**을 클릭합니다.

1. **포함** 탭에서 **모든 사용자**를 선택합니다.

1. **제외** 탭에서 **사용자 및 그룹**에 대한 확인 표시를 추가한 다음, **제외된 사용자 선택**을 클릭합니다.

1. 만든 제외 그룹을 선택합니다.

    > [!NOTE]
    > 모범 사례로 테넌트가 잠기지 않았는지 테스트하는 경우 정책에서 하나 이상의 관리자 계정을 제외하는 것이 좋습니다.

1. 조직의 요구 사항에 따라 조건부 액세스 정책을 계속 설정합니다.

    ![제외된 사용자 선택](./media/conditional-access-exclusion/select-excluded-users.png)

조건부 액세스 정책에서 제외를 관리하기 위해 액세스 검토를 사용할 수 있는 두 가지 예제를 살펴보겠습니다.

## <a name="example-1-access-review-for-users-accessing-from-blocked-countries"></a>예제 1: 차단된 국가에서 액세스하는 사용자에 대한 액세스 검토

특정 국가로부터 액세스를 차단하는 조건부 액세스 정책이 있다고 가정하겠습니다. 여기에는 정책에서 제외된 그룹이 포함됩니다. 그룹의 멤버를 검토하는 권장되는 액세스 검토는 다음과 같습니다.

> [!NOTE]
> 액세스 검토를 만드는 데 글로벌 관리자 또는 사용자 계정 관리자 역할이 필요합니다.

1. 검토는 매주 다시 발생합니다.

2. 이 제외 그룹을 최신 상태로 유지하기 위해 종료되지 않습니다.

3. 이 그룹의 모든 멤버는 검토할 범위에 있습니다.

4. 각 사용자는 이렇게 차단된 국가에서 액세스되어야 하는지를 스스로 증명해야 하므로 그룹의 멤버여야 합니다.

5. 사용자가 검토 요청에 응답하지 않는 경우 그룹에서 자동으로 제거됩니다. 따라서 이 국가를 여행하는 동안 테넌트에 더 이상 액세스할 수 없습니다.

6. 사용자가 액세스 검토의 시작 및 완료를 알 수 있도록 메일 알림을 사용합니다.

    ![액세스 검토 만들기](./media/conditional-access-exclusion/create-access-review-1.png)

## <a name="example-2-access-review-for-users-accessing-with-legacy-authentication"></a>예 2: 레거시 인증을 사용하여 액세스하는 사용자에 대한 액세스 검토

레거시 인증 및 이전 버전의 클라이언트를 사용하여 사용자에 대한 액세스를 차단하는 조건부 액세스 정책이 있다고 가정하겠습니다. 여기에는 정책에서 제외된 그룹이 포함됩니다. 그룹의 멤버를 검토하는 권장되는 액세스 검토는 다음과 같습니다.

1. 이 검토는 되풀이 검토여야 합니다.

2. 그룹의 모든 사용자가 검토되어야 합니다.

3. 비즈니스 단위 소유자를 선택한 검토자로 나열하도록 구성할 수 없습니다.

4. 결과를 자동으로 적용하고 레거시 인증 방법을 계속 사용하도록 승인되지 않은 사용자를 제거합니다.

5. 대규모 그룹의 검토자가 쉽게 결정할 수 있도록 권장 사항을 사용하도록 설정하는 것이 유용할 수 있습니다.

6. 사용자가 액세스 검토의 시작 및 완료를 알 수 있도록 메일 알림을 사용합니다.

    ![액세스 검토 만들기](./media/conditional-access-exclusion/create-access-review-2.png)

**Pro 팁**: 많은 제외 그룹이 있고 따라서 여러 액세스 검토를 만들어야 하는 경우 이제 Microsoft Graph 베타 엔드포인트에서 API가 있으므로 프로그래밍 방식으로 만들고 관리할 수 있습니다. 시작하려면 [Azure AD 액세스 검토 API 참조](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/accessreviews_root) 및 [Microsoft Graph를 통해 Azure AD 액세스 검토를 검색하는 예제](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/td-p/236096)를 참조하세요.

## <a name="access-review-results-and-audit-logs"></a>액세스 검토 결과 및 감사 로그

이제 그룹, 조건부 액세스 정책 및 액세스 검토를 모두 준비했으므로 이러한 검토의 결과를 모니터링하고 추적하겠습니다.

1. Azure Portal에서 **액세스 검토** 블레이드를 엽니다.

1. 제외 그룹을 관리하기 위해 만든 컨트롤 및 프로그램을 엽니다.

1. **결과**를 클릭하여 목록에 유지되도록 승인된 사용자 및 제거된 사용자를 확인합니다.

    ![액세스 검토 결과](./media/conditional-access-exclusion/access-reviews-results.png)

1. 그런 다음, **감사 로그**를 클릭하여 검토하는 동안 수행된 작업을 확인합니다.

    ![액세스 검토 감사 로그](./media/conditional-access-exclusion/access-reviews-audit-logs.png)

IT 관리자인 경우 경우에 따라 정책에 대한 제외 그룹을 관리하는 작업을 피할 수 없습니다. 그러나 이러한 그룹을 유지 관리하고, 비즈니스 소유자 또는 사용자가 정기적으로 검토하고, 이러한 변경 내용을 감사하는 작업은 Azure AD 액세스 검토를 사용하여 더욱 간편해졌습니다.

## <a name="next-steps"></a>다음 단계

- [Azure AD를 사용하여 그룹 멤버 또는 응용 프로그램 액세스에 대한 액세스 검토 만들기](create-access-review.md)
- [Azure Active Directory의 조건부 액세스란?](../conditional-access/overview.md)
