---
title: 내 앱 구성 Azure Active Directory 계획
description: 조직에서 내 앱을 효과적으로 사용 하기 위한 계획 가이드입니다.
services: active-directory
author: barbaraselden
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 02/29/2020
ms.author: kenwith
ms.reviewer: baselden
ms.openlocfilehash: f63a8fd05e1a6ed5e41eeb64aa852ff01db295af
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101645470"
---
# <a name="plan-azure-active-directory-my-apps-configuration"></a>내 앱 구성 Azure Active Directory 계획

> [!NOTE]
> 이 문서는 조직의 내 앱 포털의 구성을 계획 해야 하는 IT 전문가를 위해 작성 되었습니다. 
>
> **최종 사용자 설명서는 [My apps 포털에서 로그인 및 앱 시작](../user-help/my-apps-portal-end-user-access.md)을 참조** 하세요.

Azure Active Directory (Azure AD) 내 앱은 앱을 시작 하 고 관리 하기 위한 웹 기반 포털입니다. 내 앱 페이지를 사용 하면 사용자가 작업을 시작 하 고 액세스 권한이 있는 모든 응용 프로그램을 찾을 수 있습니다. 사용자는에서 내 앱에 액세스 [https://myapps.microsoft.com](https://myapps.microsoft.com/) 합니다.

> [!VIDEO https://www.youtube.com/embed/atj6Ivn5m0k]

## <a name="why-configure-my-apps"></a>내 앱을 구성 하는 이유

사용자는 내 앱 포털을 기본적으로 사용할 수 있으며 해제할 수 없습니다. 최상의 환경을 제공 하도록 구성 하는 것이 중요 하며 포털은 유용 합니다. 

다음 두 조건이 모두 충족 되 면 Azure Active Directory 엔터프라이즈 응용 프로그램 목록에 있는 모든 응용 프로그램이 표시 됩니다.

* 앱에 대 한 visibility 속성이 true로 설정 됩니다. 

* 앱은 모든 사용자 또는 그룹에 할당 됩니다. 할당 된 사용자에 대해 표시 됩니다.

포털을 구성 하면 적절 한 사람들이 적절 한 앱을 쉽게 찾을 수 있습니다.

 
### <a name="how-is-the-my-apps-portal-used"></a>내 앱 포털은 어떻게 사용 되나요?

사용자는 내 앱 포털에 액세스 합니다.

* 액세스 권한이 있는 모든 조직의 Azure AD 연결 응용 프로그램을 검색 하 고 액세스 합니다.

   * 사용자에 게 최상의 환경을 제공 하기 위해 앱이 SSO (Single Sign-On)에 대해 구성 되었는지 확인 하는 것이 가장 좋습니다.

* 셀프 서비스를 사용 하도록 구성 된 새 앱에 대 한 액세스를 요청 합니다.

* 앱의 개인 컬렉션을 만듭니다.

* 응용 프로그램에 대 한 액세스 권한을 부여 하는 데 사용 되는 그룹 소유자 또는 위임 된 컨트롤의 역할을 할당 받은 경우 다른 사용자를 위해 앱에 대 한 액세스를 관리 합니다.

관리자는 다음을 구성할 수 있습니다.

* 서비스 약관을 포함 한 [승인 환경](../manage-apps/configure-user-consent.md) .

* [셀프 서비스 응용 프로그램 검색 및 액세스 요청](../manage-apps/access-panel-manage-self-service-access.md)

* [응용 프로그램 컬렉션](../manage-apps/access-panel-collections.md).

* 응용 프로그램에 아이콘 할당

* 사용자에 게 친숙 한 응용 프로그램 이름

* 내 앱에 표시 되는 회사 브랜딩

 

## <a name="plan-consent-configuration"></a>계획 승인 구성

승인에는 사용자 동의와 데이터 액세스 앱에 대 한 동의의 두 가지 유형이 있습니다.

![동의 구성 스크린샷](./media/my-apps-deployment-plan/my-apps-consent.png)

### <a name="user-consent-for-applications"></a>응용 프로그램에 대 한 사용자 동의 

사용자 또는 관리자는 모든 응용 프로그램의 사용 약관 및 개인 정보 취급 방침에 동의 해야 합니다. 사용자 또는 관리자만 응용 프로그램에 동의할 수 있는지 여부를 결정 해야 합니다. **비즈니스 규칙에서 허용 하는 경우 관리자 동의를 사용 하 여 테 넌 트의 응용 프로그램에 대 한 제어를 유지 하는 것이 좋습니다**.

관리자 동의를 사용 하려면 조직의 전역 관리자 여야 하며 응용 프로그램은 다음 중 하나 여야 합니다.

* 조직에 등록 되어 있습니다.

* 다른 Azure AD 조직에 등록 되어 있고 이전에 한 명 이상의 사용자가 동의한 합니다.

사용자에 게 동의할 수 있도록 허용 하려면 앱에 동의할 지 아니면 특정 상황 에서만 동의할 지 결정 해야 합니다.

자세한 내용은 [Azure Active Directory에서 최종 사용자가 응용 프로그램에 동의 하는 방식 구성을 참조 하세요.](../manage-apps/configure-user-consent.md)

### <a name="group-owner-consent-for-apps-accessing-data"></a>데이터에 액세스 하는 앱에 대 한 그룹 소유자 동의

Azure AD 보안 그룹 또는 M365 그룹의 소유자가 자신이 소유한 그룹의 데이터에 액세스 하는 응용 프로그램에 동의할 수 있는지 확인 합니다. 허용 하지 않거나, 모든 그룹 소유자를 허용 하거나, 그룹 소유자의 하위 집합만 허용할 수 있습니다.

자세한 내용은 [그룹 동의 권한 구성](../manage-apps/configure-user-consent-groups.md)을 참조 하세요.

그런 다음 Azure Portal에서 [사용자 및 그룹 소유자 동의 설정을](https://portal.azure.com/) 구성 합니다.

### <a name="plan-communications"></a>통신 계획

통신은 새 서비스의 성공에 대단히 중요합니다. 사용자에 게 경험을 언제 어떻게 변경 하 고 필요한 경우 지원을 얻는 지를 사전에 알려 줍니다.

앱은 일반적으로 사용자 문제를 만들지 않지만 준비 하는 것이 중요 합니다. 시작 하기 전에 지원 담당자의 모든 리소스 목록 및 가이드를 만듭니다.

#### <a name="communications-templates"></a>통신 템플릿

Microsoft는 [전자 메일에 대 한 사용자 지정 가능한 템플릿과](https://aka.ms/APTemplates) 내 앱에 대 한 기타 통신을 제공 합니다. 회사 문화에 맞게 다른 통신 채널에서 사용 하기 위해 이러한 자산을 조정할 수 있습니다.

 

## <a name="plan-your-sso-configuration"></a>SSO 구성 계획

사용자가 자격 증명을 입력할 필요 없이 원활한 환경을 사용할 수 있도록 내 앱 포털의 모든 앱에 대해 SSO를 사용 하도록 설정 하는 것이 가장 좋습니다.

Azure AD는 여러 SSO 옵션을 지원 합니다. 

* 자세히 알아보려면 [AZURE AD의 Single sign-on 옵션](sso-options.md)을 참조 하세요.

* 앱에 대 한 id 공급자로 Azure AD를 사용 하는 방법에 대 한 자세한 내용은 [응용 프로그램 관리에](../manage-apps/view-applications-portal.md)대 한 빠른 시작 시리즈를 참조 하세요.

### <a name="use-federated-sso-if-possible"></a>가능 하면 페더레이션된 SSO 사용

내 앱 페이지에서 최상의 사용자 환경을 사용 하려면 페더레이션된 SSO (Openid connect Connect 또는 SAML)에 사용할 수 있는 클라우드 응용 프로그램의 통합을 시작 합니다. 페더레이션된 SSO를 사용 하면 사용자가 앱 시작 화면에서 일관적인 단일 클릭 환경을 사용할 수 있으며, 구성 제어에서 더욱 강력해 집니다.

SSO에 대 한 SaaS (software as a service) 응용 프로그램을 구성 하는 방법에 대 한 자세한 내용은 [SaaS SSO 배포 계획]을 참조 하십시오. /Desktop/plan-sso-deployment.md).

### <a name="considerations-for-special-sso-circumstances"></a>특수 SSO 상황에 대 한 고려 사항

> [!TIP]
> 사용자 환경을 개선 하려면 암호 기반 SSO 및 ADFS 대신 응용 프로그램에서 지원할 때 Azure AD에서 페더레이션된 SSO (Openid connect Connect/SAML)를 사용 합니다.

암호 기반 SSO 응용 프로그램 또는 Azure AD 응용 프로그램 프록시에서 액세스 하는 응용 프로그램에 로그인 하려면 사용자가 My Apps 보안 로그인 확장을 설치 하 고 사용 해야 합니다. 사용자가 암호 기반 SSO 또는 응용 프로그램 프록시 응용 프로그램을 처음 시작할 때 확장을 설치 하 라는 메시지가 표시 됩니다. 

![스크린샷](./media/my-apps-deployment-plan/ap-dp-install-myapps.png)

확장에 대 한 자세한 내용은 [My Apps browser 확장 설치](../user-help/my-apps-portal-end-user-access.md)를 참조 하세요.

이러한 응용 프로그램을 통합 해야 하는 경우 [지원 되는 브라우저](../user-help/my-apps-portal-end-user-access.md)를 사용 하 여 확장을 대규모로 배포 하는 메커니즘을 정의 해야 합니다. 표시되는 옵션은 다음과 같습니다.

* [Chrome, Firefox, Microsoft Edge 또는 IE에 대 한 사용자 구동 다운로드 및 구성](../user-help/my-apps-portal-end-user-access.md)

* [Internet Explorer에 대 한 Configuration Manager](/mem/configmgr/core/clients/deploy/deploy-clients-to-windows-computers)

확장을 통해 사용자는 검색 표시줄에서 앱을 시작 하 고 최근에 사용한 응용 프로그램에 대 한 액세스를 찾고 내 앱 페이지에 대 한 링크를 얻을 수 있습니다.

![내 앱 확장 검색의 스크린샷](./media/my-apps-deployment-plan/my-app-extsension.png)

#### <a name="plan-for-mobile-access"></a>모바일 액세스 계획

암호 기반 SSO를 사용 하거나 [Microsoft Azure AD 응용 프로그램 프록시](../manage-apps/application-proxy.md)를 사용 하 여 액세스 하는 응용 프로그램의 경우 Microsoft Edge mobile을 사용 해야 합니다. 다른 응용 프로그램의 경우 모든 모바일 브라우저를 사용할 수 있습니다. 

### <a name="linked-sso"></a>연결된 SSO

연결 된 SSO 옵션을 사용 하 여 응용 프로그램을 추가할 수 있습니다. 기존 웹 응용 프로그램의 URL에 연결 되는 응용 프로그램 타일을 구성할 수 있습니다. 연결 된 SSO를 사용 하면 모든 응용 프로그램을 Azure AD SSO로 마이그레이션하지 않고도 My Apps 포털로 사용자의 연결을 시작할 수 있습니다. 사용자 환경을 방해 하지 않고 Azure AD SSO 구성 응용 프로그램으로 점진적으로 이동할 수 있습니다.

## <a name="plan-the-user-experience"></a>사용자 환경 계획

기본적으로 사용자에 게 액세스 권한이 있는 모든 응용 프로그램 및 셀프 서비스 검색용으로 구성 된 모든 응용 프로그램은 사용자의 내 앱 패널에 표시 됩니다. 많은 조직에서이 목록은 매우 클 수 있으며, 구성 되지 않은 경우에는 작업이 부담이 될 수 있습니다.

### <a name="plan-my-apps-collections"></a>내 앱 컬렉션 계획

사용자가 액세스할 수 있는 모든 Azure AD 응용 프로그램은 모든 앱 컬렉션의 내 앱에 표시 됩니다. 컬렉션을 사용 하 여 관련 응용 프로그램을 그룹화 하 고 별도의 탭에 표시 하 여 쉽게 찾을 수 있습니다. 예를 들어 컬렉션을 사용 하 여 특정 작업 역할, 작업, 프로젝트 등에 대해 응용 프로그램의 논리적 그룹을 만들 수 있습니다. 

최종 사용자는 다음을 통해 환경을 사용자 지정할 수도 있습니다.

* 자신의 앱 컬렉션을 만듭니다.

* [앱 컬렉션을 숨기고 다시 정렬](access-panel-collections.md)합니다.

![셀프 서비스 구성 스크린샷](./media/my-apps-deployment-plan/collections.png)

내 앱 포털에서 앱을 숨기는 동시에 Microsoft 365 포털과 같은 다른 위치에서 계속 액세스할 수 있는 옵션이 있습니다. 자세한 정보: [Azure Active Directory의 사용자 환경에서 응용 프로그램을 숨깁니다](hide-application-from-user-portal.md).

> [!IMPORTANT]
> 내 앱을 통해 사용자에 게 액세스 권한이 있는 950 개 앱에만 액세스할 수 있습니다. 여기에는 사용자 또는 관리자가 숨긴 앱이 포함 됩니다. 

### <a name="plan-self-service-group-management-membership"></a>셀프 서비스 그룹 관리 구성원 자격 계획

사용자가 Azure AD에서 자신의 보안 그룹 또는 Microsoft 365 그룹을 만들고 관리할 수 있도록 설정할 수 있습니다. 그룹 소유자는 멤버 자격 요청을 승인 또는 거부 하 고 그룹 멤버 자격에 대 한 제어를 위임할 수 있습니다. 메일 사용이 가능한 보안 그룹이 나 메일 그룹에는 셀프 서비스 그룹 관리 기능을 사용할 수 없습니다.

셀프 서비스 그룹 멤버 자격에 대 한 계획을 수립 하려면 조직의 모든 사용자가 그룹을 만들고 관리할 수 있도록 허용할지 또는 사용자의 하위 집합만 허용할지 결정 합니다. 사용자의 하위 집합을 허용 하는 경우 해당 사용자가 추가 되는 그룹을 설정 해야 합니다. 

이러한 시나리오를 사용 하는 방법에 대 한 자세한 내용은 [Azure Active Directory의 셀프 서비스 그룹 관리 설정](../enterprise-users/groups-self-service-management.md) 을 참조 하세요.

### <a name="plan-self-service-application-access"></a>셀프 서비스 응용 프로그램 액세스 계획

사용자가 내 앱 패널을 통해 응용 프로그램에 대 한 액세스를 검색 하 고 요청 하도록 설정할 수 있습니다. 이렇게 하려면 먼저 다음을 수행 해야 합니다. 

* 셀프 서비스 그룹 관리 사용

* SSO에 대해 앱 사용

* 응용 프로그램 액세스를 위한 그룹 만들기

![My Apps 셀프 서비스 구성의 스크린샷](./media/my-apps-deployment-plan/my-apps-self-service.png)

사용자가 액세스를 요청 하면 기본 그룹에 대 한 액세스를 요청 하 고 그룹 소유자는 그룹 멤버 자격을 관리 하 고 응용 프로그램 액세스 권한을 위임 받을 수 있습니다. 승인 워크플로를 사용 하 여 응용 프로그램에 대 한 액세스를 명시적으로 승인할 수 있습니다. 승인자 인 사용자는 응용 프로그램에 대 한 액세스 요청을 보류 중인 경우 내 앱 포털 내에서 알림을 받게 됩니다.

## <a name="plan-reporting-and-auditing"></a>보고 및 감사 계획

Azure AD는 [기술 및 비즈니스 정보를 제공 하는 보고서] .를 제공 합니다. /reports-monitoring/overview-reports.md). 비즈니스 및 기술 응용 프로그램 소유자와 협력 하 여 이러한 보고서의 소유권을 가정 하 고 정기적으로 사용 합니다. 다음 표에서는 일반적인 보고 시나리오에 대 한 몇 가지 예를 제공 합니다.

| 예제| 위험 관리| 생산성 향상| 거버넌스 및 규정 준수 |
| - | - | - | -|
| 보고서 유형| 응용 프로그램 사용 권한 및 사용| 계정 프로비전 활동| 응용 프로그램에 액세스 하는 사용자 검토 |
| 잠재적 작업| 감사 액세스 권한 해지| 프로 비전 오류 수정| 액세스 취소 |


Azure AD는 30 일 동안 대부분의 감사 데이터를 유지 합니다. 분석 시스템으로 다운로드 하기 위해 Azure 관리 포털 또는 API를 통해 데이터를 사용할 수 있습니다.

#### <a name="auditing"></a>감사

응용 프로그램 액세스에 대 한 감사 로그는 30 일 동안 사용할 수 있습니다. 조직에서 더 긴 보존을 요구 하는 경우 Splunk 또는 ArcSight와 같은 SIEM (보안 정보 이벤트 및 관리) 도구로 로그를 내보냅니다.

감사, 보고 및 재해 복구 백업의 경우 필요한 다운로드 빈도, 대상 시스템 정의 및 각 백업 관리를 담당 하는 사용자를 문서화 합니다. 별도의 감사 및 보고 백업이 필요 하지 않을 수 있습니다. 재해 복구 백업은 별도의 엔터티입니다.

## <a name="validate-your-deployment"></a>배포 유효성 검사

내 앱 배포가 철저 하 게 테스트 되었고 롤백 계획이 준비 되어 있는지 확인 합니다.

회사 소유의 장치와 개인 장치 모두에서 다음 테스트를 수행 합니다. 이러한 테스트 사례에도 비즈니스 사용 사례가 반영 되어야 합니다. 일반적인 기술 시나리오를 기반으로 하는 몇 가지 사례는 다음과 같습니다. 필요에 따라 다른 사용자를 추가 합니다.

#### <a name="application-sso-access-test-case-examples"></a>응용 프로그램 SSO 액세스 테스트 사례 예:


| 비즈니스 사례| 예상된 결과 |
| - | - |
| 사용자가 내 앱 포털에 로그인| 사용자가 로그인 하 여 응용 프로그램을 볼 수 있음 |
| 사용자가 페더레이션된 SSO 응용 프로그램을 시작 합니다.| 사용자가 응용 프로그램에 자동으로 로그인 됩니다. |
| 사용자가 처음으로 암호 SSO 응용 프로그램을 시작 합니다.| 사용자가 내 앱 확장을 설치 해야 함 |
| 사용자가 다음 번에 암호 SSO 응용 프로그램을 시작 합니다.| 사용자가 응용 프로그램에 자동으로 로그인 됩니다. |
| 사용자가 Microsoft 365 포털에서 앱을 시작 합니다.| 사용자가 응용 프로그램에 자동으로 로그인 됩니다. |
| 사용자가 Managed Browser에서 앱을 시작 합니다.| 사용자가 응용 프로그램에 자동으로 로그인 됩니다. |


#### <a name="application-self-service-capabilities-test-case-examples"></a>응용 프로그램 셀프 서비스 기능 테스트 사례 예제


| 비즈니스 사례| 예상된 결과 |
| - | - |
| 사용자가 응용 프로그램에 대 한 멤버 자격을 관리할 수 있음| 사용자는 앱에 대 한 액세스 권한이 있는 구성원을 추가/제거할 수 있습니다. |
| 사용자가 응용 프로그램을 편집할 수 있습니다.| 사용자는 암호 SSO 응용 프로그램에 대 한 응용 프로그램의 설명 및 자격 증명을 편집할 수 있습니다. |


### <a name="rollback-steps"></a>롤백 단계

배포가 계획대로 진행되지 않는 경우 수행할 작업을 계획하는 것이 중요합니다. 배포 하는 동안 SSO 구성이 실패할 경우 [sso 문제를 해결](../hybrid/tshoot-connect-sso.md) 하 고 사용자에 대 한 영향을 줄이는 방법을 이해 해야 합니다. 극단적인 경우 [SSO를 롤백해야](plan-sso-deployment.md)할 수 있습니다.

## <a name="manage-your-implementation"></a>구현 관리

최소 권한 역할을 사용 하 여 Azure Active Directory 내에서 필요한 작업을 수행 합니다. [사용 가능한 다른 역할을 검토](../roles/permissions-reference.md) 하 고 올바른 역할을 선택 하 여이 응용 프로그램의 각 가상 사용자에 대 한 요구 사항을 해결 합니다. 일부 역할은 배포를 완료 한 후 임시로 적용 해야 할 수도 있습니다.

| 가상 사용자| 역할| Azure AD 역할 |
| - | - | - |
| 기술 지원팀 관리자| 계층 1 지원| 없음 |
| Id 관리| 문제가 Azure AD에 영향을 주는 경우 구성 및 디버그| 글로벌 관리자 |
| 응용 프로그램 관리자| 응용 프로그램의 사용자 증명, 권한이 있는 사용자의 구성| 없음 |
| 인프라 관리자| 인증서 롤오버 소유자| 글로벌 관리자 |
| 비즈니스 소유자/관련자| 응용 프로그램의 사용자 증명, 권한이 있는 사용자의 구성| 없음 |


[Privileged Identity Management](../privileged-identity-management/pim-configure.md) 를 사용 하 여 디렉터리 권한이 있는 사용자에 게 추가 감사, 제어 및 액세스 검토를 제공할 역할을 관리할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[Azure AD Multi-Factor Authentication의 배포 계획](../authentication/howto-mfa-getstarted.md)

[애플리케이션 프록시 배포 계획](application-proxy-deployment-plan.md)

