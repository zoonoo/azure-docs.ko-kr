---
title: Azure Active Directory에서 응용 프로그램에 대 한 동의 관리 및 승인 요청 평가
description: 사용자 동의가 사용 하지 않도록 설정 되거나 제한 되는 경우 동의 요청을 관리 하는 방법 및 Azure Active Directory 응용 프로그램에 대 한 테 넌 트 전체 관리자 동의에 대 한 요청을 평가 하는 방법에 대해 알아봅니다.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 12/27/2019
ms.author: kenwith
ms.reviewer: phsignor
ms.openlocfilehash: 4664bdd515c815dbd742c0d4e05f1e5c03978526
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94649195"
---
# <a name="managing-consent-to-applications-and-evaluating-consent-requests"></a>응용 프로그램에 대 한 동의 관리 및 승인 요청 평가

응용 프로그램에 최종 사용자 동의를 사용 하지 않도록 설정 하는 것 [이 좋습니다](../../security/fundamentals/steps-secure-identity.md#restrict-user-consent-operations) . 이렇게 하면 조직의 보안 및 id 관리자 팀과 의사 결정 프로세스를 중앙 집중화할 수 있습니다.

최종 사용자 동의를 사용 하지 않도록 설정 하거나 제한 한 후에는 업무상 중요 한 응용 프로그램을 계속 사용할 수 있는 동시에 조직이 안전 하 게 유지 되도록 하기 위해 몇 가지 중요 한 고려 사항이 있습니다. 이러한 단계는 조직의 지원 팀과 IT 관리자에 게 미치는 영향을 최소화 하는 동시에 타사 응용 프로그램에서 관리 되지 않는 계정을 사용 하지 못하도록 하는 데 중요 합니다.

## <a name="process-changes-and-education"></a>변경 내용 및 교육 처리

 1. 사용자가 동의 화면에서 직접 관리자 승인을 요청할 수 있도록 [관리자 동의 워크플로 (미리 보기)](configure-admin-consent-workflow.md) 를 사용 하도록 설정 하는 것이 좋습니다.

 2. 모든 관리자가 [권한 및 승인 프레임 워크](../develop/consent-framework.md)를 이해 하 고, [동의 확인 메시지가](../develop/application-consent-experience.md) 작동 하는 방법, [테 넌 트 전체 관리자 동의에 대 한 요청을 평가](#evaluating-a-request-for-tenant-wide-admin-consent)하는 방법을 이해 해야 합니다.
 3. 사용자가 응용 프로그램에 대 한 관리자 승인을 요청 하 고 필요한 경우 업데이트 하도록 조직의 기존 프로세스를 검토 합니다. 프로세스가 변경 된 경우:
    * 관련 설명서, 모니터링, 자동화 등을 업데이트 합니다.
    * 영향을 받는 모든 사용자, 개발자, 지원 팀 및 IT 관리자의 프로세스 변경 사항을 전달 합니다.

## <a name="auditing-and-monitoring"></a>감사 및 모니터링

1. 불필요 한 상승을 또는 의심 스러운 응용 프로그램이 이전에 데이터에 대 한 액세스 권한을 부여 받지 않았는지 확인 하기 위해 조직에서 [앱을 감사 하 고 권한을 부여](../../security/fundamentals/steps-secure-identity.md#audit-apps-and-consented-permissions) 합니다.

2. OAuth 동의를 요청 하는 의심 스러운 응용 프로그램에 대 한 추가 모범 사례 및 보호를 위해 [Office 365의 불법 승인 승인 검색 및 재구성](/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants) 을 검토 합니다.

3. 조직에 적절 한 라이선스가 있는 경우:

    * [Microsoft Cloud App Security의 추가 OAuth 응용 프로그램 감사 기능을](/cloud-app-security/investigate-risky-oauth)사용 합니다.
    * [Azure Monitor 통합 문서를 사용 하 여 권한 및 동의 관련 작업을 모니터링할 수](../reports-monitoring/howto-use-azure-monitor-workbooks.md) 있습니다. *동의 Insights* 통합 문서는 실패 한 승인 요청 수 별 앱 보기를 제공 합니다. 이는 관리자가 관리자에 게 동의를 부여할지 여부를 결정 하는 응용 프로그램의 우선 순위를 지정 하는 데 도움이 될 수 있습니다.

### <a name="additional-considerations-for-reducing-friction"></a>마찰을 줄이기 위한 추가 고려 사항

이미 사용 중인 신뢰할 수 있는 비즈니스에 중요 한 응용 프로그램에 대 한 영향을 최소화 하려면 많은 사용자 동의가 부여 된 응용 프로그램에 관리자의 동의를 사전에 부여 하는 것이 좋습니다.

1. 로그인 로그 또는 동의 부여 작업을 기반으로 하 여 이미 조직에 추가 된 앱의 인벤토리를 사용 합니다. PowerShell [스크립트](https://gist.github.com/psignoret/41793f8c6211d2df5051d77ca3728c09) 를 사용 하 여 많은 수의 사용자 동의 부여로 응용 프로그램을 쉽고 빠르게 검색할 수 있습니다.

2. 아직 관리자 동의가 부여 되지 않은 상위 응용 프로그램을 평가 합니다.

   > [!IMPORTANT]
   > 조직에서 많은 사용자가 이미 동의한 한 경우에도 테 넌 트 전체 관리자 동의를 부여 하기 전에 응용 프로그램을 신중 하 게 평가 합니다.

3. 승인 된 각 응용 프로그램에 대해 아래에서 설명 하는 방법 중 하나를 사용 하 여 테 넌 트 전체 관리자 동의를 부여 합니다.

4. 승인 된 각 응용 프로그램에 대해 [사용자 액세스를 제한](configure-user-consent.md)하는 것이 좋습니다.

## <a name="evaluating-a-request-for-tenant-wide-admin-consent"></a>테 넌 트 전체 관리자 동의에 대 한 요청 평가

테 넌 트 전체 관리자 동의를 부여 하는 것은 중요 한 작업입니다.  전체 조직을 대신 하 여 사용 권한을 부여 하 고, 높은 권한의 작업을 시도 하는 권한을 포함할 수 있습니다. 예를 들어 역할 관리, 모든 사서함 또는 모든 사이트에 대 한 모든 권한, 전체 사용자 가장.

테 넌 트 전체 관리자 동의를 부여 하기 전에 허용 되는 액세스 수준에 대해 응용 프로그램 및 응용 프로그램 게시자를 신뢰할 수 있는지 확인 해야 합니다. 응용 프로그램을 제어 하는 사람과 응용 프로그램이 사용 권한을 요청 하는 이유를 이해 하지 못하는 경우 *동의를 부여 하지 마십시오*.

다음 목록에서는 관리자 동의를 부여 하는 요청을 평가할 때 고려해 야 할 몇 가지 권장 사항을 제공 합니다.

* **Microsoft id 플랫폼에서 [사용 권한 및 승인 프레임 워크](../develop/consent-framework.md) 를 이해 합니다.**

* **[위임 된 권한 및 응용 프로그램 권한](../develop/v2-permissions-and-consent.md#permission-types)간의 차이점을 이해 합니다.**

   응용 프로그램 사용 권한을 통해 응용 프로그램은 사용자 개입 없이 전체 조직의 데이터에 액세스할 수 있습니다. 위임 된 사용 권한을 통해 응용 프로그램은 특정 시점에 응용 프로그램에 로그인 한 사용자를 대신 하 여 작업을 수행할 수 있습니다.

* **요청 된 사용 권한을 이해 합니다.**

   응용 프로그램에서 요청 하는 권한은 [동의 프롬프트](../develop/application-consent-experience.md)에 나열 됩니다. 권한 제목을 확장 하면 사용 권한의 설명이 표시 됩니다. 응용 프로그램 사용 권한에 대 한 설명은 일반적으로 "로그인 한 사용자가 없는" 상태로 종료 됩니다. 위임 된 권한에 대 한 설명은 일반적으로 "로그인 한 사용자를 대신 하 여"로 끝납니다. Microsoft Graph API에 대 한 권한은 [Microsoft Graph 사용 권한 참조](/graph/permissions-reference) 에 설명 되어 있습니다. 다른 api에 대 한 설명서를 참조 하 여 노출 된 사용 권한을 이해 합니다.

   요청 된 사용 권한을 이해 하지 못하는 경우 동의를 *부여 하지 마십시오*.

* **사용 권한을 요청 하는 응용 프로그램 및 응용 프로그램을 게시 한 사용자를 이해 합니다.**

   다른 응용 프로그램 처럼 보이는 악의적인 응용 프로그램의 경우 주의 해야 합니다.

   응용 프로그램 또는 해당 게시자의 적법성 잘 모르겠으면 *동의를 부여 하지 마십시오*. 대신 추가 확인을 검색 합니다 (예: 응용 프로그램 게시자에서 직접).

* **요청 된 사용 권한이 응용 프로그램에서 원하는 기능과 일치 하는지 확인 합니다.**

   예를 들어 SharePoint 사이트 관리를 제공 하는 응용 프로그램은 모든 사이트 모음을 읽기 위해 위임 된 액세스 권한을 필요로 할 수 있지만 모든 사서함에 대 한 전체 액세스 또는 디렉터리의 전체 가장 권한이 반드시 필요한 것은 아닙니다.

   응용 프로그램에 필요한 것 보다 많은 권한을 요청 하는 것으로 의심 되는 경우 *동의를 부여 하지 마십시오*. 자세한 내용은 응용 프로그램 게시자에 게 문의 하세요.

## <a name="granting-consent-as-an-administrator"></a>관리자 권한 부여

### <a name="granting-tenant-wide-admin-consent"></a>테 넌 트 전체 관리자 동의 부여
Azure Portal에서 테 넌 트 전체 관리자 동의를 부여 하는 단계별 지침은 Azure AD PowerShell을 사용 하거나 동의 프롬프트 자체에서 [응용 프로그램에 대 한 테 넌 트 전체 관리자 동의 부여](grant-admin-consent.md) 를 참조 하세요.

### <a name="granting-consent-on-behalf-of-a-specific-user"></a>특정 사용자를 대신 하 여 동의 부여
관리자는 전체 조직에 대 한 동의를 부여 하는 대신 [MICROSOFT GRAPH API](/graph/use-the-api) 를 사용 하 여 단일 사용자를 대신 하 여 위임 된 권한에 대 한 동의를 부여할 수도 있습니다. 자세한 내용은 [사용자를 대신 하 여 액세스 권한 가져오기](/graph/auth-v2-user)를 참조 하세요.

## <a name="limiting-user-access-to-applications"></a>응용 프로그램에 대 한 사용자 액세스 제한
테 넌 트 전체 관리자 동의가 부여 된 경우에도 응용 프로그램에 대 한 사용자 액세스는 계속 제한 될 수 있습니다. 응용 프로그램에 사용자 할당을 요구 하는 방법에 대 한 자세한 내용은 [사용자 및 그룹을 할당](./assign-user-or-group-access-portal.md)하는 방법을 참조 하세요.

추가 복잡 한 시나리오를 처리 하는 방법을 비롯 한 광범위 한 개요는 [응용 프로그램 액세스 관리에 AZURE AD 사용](what-is-access-management.md)을 참조 하세요.

## <a name="disable-all-future-user-consent-operations-to-any-application"></a>모든 응용 프로그램에 대 한 모든 이후 사용자 동의 작업을 사용 하지 않도록 설정
전체 디렉터리에 대한 사용자 동의를 비활성화하면 모든 애플리케이션에 대한 최종 사용자 동의를 방지합니다. 관리자는 사용자를 대신 하 여 동의할 수 있습니다. 애플리케이션 동의 및 동의하거나 동의하지 않을 수 있는 이유에 대해 자세히 알아보려면 [사용자 및 관리자 동의 이해하기](../develop/howto-convert-app-to-be-multi-tenant.md)를 참조하세요.

전체 디렉터리에서 모든 이후 사용자 동의 작업을 비활성화하려면 다음 단계를 따릅니다.
1.  [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 권한으로 로그인합니다.
2.  왼쪽 주 탐색 메뉴의 맨 위에서 **모든 서비스** 를 클릭하여 **Azure Active Directory 확장** 을 엽니다.
3.  필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.
4.  탐색 메뉴에서 **사용자 및 그룹을** 선택 합니다.
5.  **사용자 설정** 을 선택 합니다.
6.  **사용자가 앱이 데이터에 액세스하도록 허용할 수 있음** 토글을 **아니요** 로 설정하고 **저장** 단추를 클릭하여 모든 이후 사용자 동의 작업을 비활성화합니다.

## <a name="next-steps"></a>다음 단계
* [ID 인프라를 보호하기 위한 5단계](../../security/fundamentals/steps-secure-identity.md#before-you-begin-protect-privileged-accounts-with-mfa)
* [관리자 동의 워크플로 구성](configure-admin-consent-workflow.md)
* [최종 사용자가 애플리케이션에 동의하는 방법 구성](configure-user-consent.md)
* [Microsoft ID 플랫폼의 권한 및 동의](../develop/v2-permissions-and-consent.md)