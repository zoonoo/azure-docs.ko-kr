---
title: Azure Active Directory에 애플리케이션 인증 마이그레이션
description: 이 백서에서는 응용 프로그램 인증을 Azure AD로 마이그레이션하는 계획 및 이점에 대해 자세히 설명 합니다.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 02/05/2021
ms.author: kenwith
ms.reviewer: baselden
ms.collection: M365-identity-device-management
ms.openlocfilehash: 629f71c429d6af8583cb15b9ebc96efe6867f78b
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104888892"
---
# <a name="migrate-application-authentication-to-azure-active-directory"></a>Azure Active Directory에 애플리케이션 인증 마이그레이션

## <a name="about-this-paper"></a>이 문서 정보

이 백서에서는 응용 프로그램 인증을 Azure AD로 마이그레이션하는 계획 및 이점에 대해 자세히 설명 합니다. Azure 관리자 및 id 전문가를 위해 설계 되었습니다.

프로세스를 각각 세부적인 계획 및 종료 기준이 있는 4 단계로 나누면 마이그레이션 전략을 계획 하 고 Azure AD 인증이 조직의 목표를 어떻게 지원 하는지 이해 하는 데 도움이 되도록 설계 되었습니다.

## <a name="introduction"></a>소개

오늘날 조직에서는 사용자가 작업을 완료 하는 데 필요한 slew 응용 프로그램 (앱)을 요구 합니다. 매일 앱을 계속 추가, 개발 또는 사용 중지할 가능성이 높습니다. 사용자는 다양 한 회사와 개인 장치 및 위치에서 이러한 응용 프로그램에 액세스 합니다. 다음을 비롯 한 여러 가지 방법으로 앱을 엽니다.

- 회사 홈페이지 또는 포털을 통해

- 브라우저에서 책갈피 설정

- SaaS (software as a service) 앱에 대 한 공급 업체 URL을 통해

- MDM/MAM (모바일 장치/응용 프로그램 관리) 솔루션을 통해 사용자의 데스크톱 또는 모바일 장치로 직접 푸시되는 링크

응용 프로그램은 다음과 같은 유형의 인증을 사용할 가능성이 높습니다.

- 온-프레미스 페더레이션 솔루션 (예: ADFS (Active Directory Federation Services) 및 Ping)

- Active Directory (예: Kerberos 인증 및 Windows-Integrated 인증)

- 기타 클라우드 기반 id 및 액세스 관리 (IAM) 솔루션 (예: Okta 또는 Oracle)

- 온-프레미스 웹 인프라 (예: IIS 및 Apache)

- 클라우드 호스팅 인프라 (예: Azure 및 AWS)

**사용자가 응용 프로그램을 쉽고 안전 하 게 액세스할 수 있도록 하기 위해 온-프레미스 및 클라우드 환경에서 단일 액세스 제어 및 정책 집합을 보유 하는 것이 목표입니다.**

[Azure Active Directory (AZURE AD)](../fundamentals/active-directory-whatis.md) 는 사용자, 파트너 및 고객에 게 원하는 응용 프로그램에 액세스 하 고 모든 플랫폼과 장치에서 공동 작업할 수 있는 단일 id를 제공 하는 범용 id 플랫폼을 제공 합니다.

![Azure Active Directory 연결 다이어그램](media/migrating-application-authentication-to-azure-active-directory-1.jpg)

Azure AD에는 [완전 한 id 관리 기능이](../fundamentals/active-directory-whatis.md#which-features-work-in-azure-ad)포함 되어 있습니다. 앱 인증 및 권한 부여를 Azure AD로 표준화 하면 이러한 기능에서 제공 하는 이점을 얻을 수 있습니다.

에서 더 많은 마이그레이션 리소스를 찾을 수 있습니다. [https://aka.ms/migrateapps](./migration-resources.md)

## <a name="benefits-of-migrating-app-authentication-to-azure-ad"></a>Azure AD로 앱 인증을 마이그레이션하는 이점

앱 인증을 Azure AD로 이동 하면 위험과 비용을 관리 하 고, 생산성을 높이고, 규정 준수 및 거 버 넌 스 요구 사항을 해결 하는 데 도움이 됩니다.

### <a name="manage-risk"></a>위험 관리

앱을 보호 하려면 모든 위험 요인을 완전히 확인 해야 합니다. 앱을 Azure AD로 마이그레이션하면 보안 솔루션이 통합 됩니다. 이를 통해 다음을 수행할 수 있습니다.

- [조건부 액세스 정책](../conditional-access/overview.md), [Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md)및 실시간 위험 기반 [id 보호](../identity-protection/overview-identity-protection.md) 기술을 사용 하 여 응용 프로그램 및 연결 된 회사 데이터에 대 한 보안 사용자 액세스를 향상 합니다.

- Just-in-time 관리 액세스를 사용 하 여 사용자 환경에 대 한 권한 [있는](../../azure-resource-manager/managed-applications/request-just-in-time-access.md) 사용자의 액세스를 보호 합니다.

- 가장 중요 한 비즈니스 요구 사항에 맞게 [AZURE AD의 다중 테 넌 트, 지리적으로 분산 된 고가용성 디자인](https://cloudblogs.microsoft.com/enterprisemobility/2014/09/02/azure-ad-under-the-hood-of-our-geo-redundant-highly-available-distributed-cloud-directory/)을 사용 합니다.

- 이미 배포 했을 수 있는 [보안 하이브리드 액세스 파트너 통합](https://aka.ms/secure-hybrid-access) 중 하나를 사용 하 여 레거시 응용 프로그램을 보호 합니다.

### <a name="manage-cost"></a>비용 관리

조직에서 여러 IAM (Id 액세스 관리) 솔루션을 사용할 수 있습니다. 하나의 Azure AD 인프라로 마이그레이션하는 것은 IAM 라이선스 (온-프레미스 또는 클라우드)에 대 한 종속성 및 인프라 비용을 줄일 수 있는 기회입니다. Microsoft 365 라이선스를 통해 Azure AD에 이미 지불 했을 수 있는 경우 다른 IAM 솔루션의 추가 비용을 지불할 이유가 없습니다.

**Azure AD를 사용 하 여 다음과 같은 방법으로 인프라 비용을 줄일 수 있습니다.**

- [Azure AD 응용 프로그램 프록시](./application-proxy.md)를 사용 하 여 온-프레미스 앱에 대 한 보안 원격 액세스 제공

- [신뢰할 수 있는 유니버설 id 공급자로 AZURE AD를 설정](../hybrid/plan-connect-user-signin.md#choosing-the-user-sign-in-method-for-your-organization)하 여 테 넌 트의 온-프레미스 자격 증명 접근 방식에서 앱을 분리 합니다.

### <a name="increase-productivity"></a>생산성 향상

경제성 및 보안은 조직에서 Azure AD를 도입 하는 이점을 제공 하지만, 전체 채택 및 규정 준수는 사용자가 혜택을 얻을 가능성이 높습니다. Azure AD를 사용 하 여 다음을 수행할 수 있습니다.

- 모든 장치 및 위치에서 모든 응용 프로그램에 대 한 원활한 보안 액세스를 통해 최종 사용자 [SSO (Single Sign-On](./what-is-single-sign-on.md) ) 환경을 개선 합니다.

- 셀프 서비스 [암호 재설정](../authentication/concept-sspr-howitworks.md) 및 [SelfService 그룹 관리](../enterprise-users/groups-self-service-management.md)와 같은 셀프 서비스 IAM 기능을 사용 합니다.

- 클라우드 및 온-프레미스 환경에서 각 사용자에 대 한 단일 id를 관리 하 여 관리 오버 헤드를 줄입니다.

  - Azure AD id를 기반으로 [AZURE Ad 갤러리](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps)에서 사용자 계정 [프로 비전 자동화](../app-provisioning/user-provisioning.md)
  - [Azure Portal](https://portal.azure.com/) 에서 myapps 패널의 모든 앱에 액세스

- 개발자가 MSAL (Microsoft 인증 라이브러리)과 함께 [Microsoft Id 플랫폼](../develop/v2-overview.md) 을 사용 하 여 앱에 대 한 액세스를 보호 하 고 최종 사용자 환경을 개선할 수 있습니다.

- [AZURE AD B2B 공동 작업](../external-identities/what-is-b2b.md)을 사용 하 여 클라우드 리소스에 대 한 액세스 권한을 파트너에 게 부여 합니다. 클라우드 리소스 파트너와 지점 간 페더레이션을 구성 하는 오버 헤드를 제거 합니다.

### <a name="address-compliance-and-governance"></a>주소 규정 준수 및 거 버 넌 스

통합 감사 도구 및 Api를 사용 하 여 회사 액세스 정책을 적용 하 고 응용 프로그램 및 관련 데이터에 대 한 사용자 액세스를 모니터링 하 여 규정 요구 사항을 준수 하는지 확인 합니다. Azure AD를 사용 하면 [SIEM (보안 인시던트 및 이벤트 모니터링) 도구](../reports-monitoring/plan-monitoring-and-reporting.md)를 사용 하는 보고서를 통해 응용 프로그램 로그인을 모니터링할 수 있습니다. 포털 또는 Api에서 보고서에 액세스 하 고, 응용 프로그램에 대 한 액세스 권한이 있는 사용자를 프로그래밍 방식으로 감사 하 고 액세스 검토를 통해 비활성 사용자에 대 한 액세스 권한을 제거할 수 있습니다.

## <a name="plan-your-migration-phases-and-project-strategy"></a>마이그레이션 단계 및 프로젝트 전략 계획

기술 프로젝트에 오류가 발생 하는 경우 예상과 일치 하지 않거나, 올바른 관련자가 관여 하지 않거나, 통신이 부족 하기 때문일 수 있습니다. 프로젝트 자체를 계획 하 여 성공 여부를 확인 합니다.

### <a name="the-phases-of-migration"></a>마이그레이션 단계

도구를 시작 하기 전에 마이그레이션 프로세스를 진행 하는 방법을 이해 해야 합니다. 몇 가지 직접 고객 워크샵을 통해 다음 네 단계를 수행 하는 것이 좋습니다.

![마이그레이션 단계에 대 한 다이어그램](media/migrating-application-authentication-to-azure-active-directory-2.jpg)

### <a name="assemble-the-project-team"></a>프로젝트 팀 조합

응용 프로그램 마이그레이션은 팀에서 수행 하는 작업 이며 모든 중요 한 위치가 채워져 있는지 확인 해야 합니다. 선임 비즈니스 리더의 지원이 중요 합니다. 임원 스폰서, 비즈니스 의사 결정권자 및 실무 전문가의 올바른 집합 (Sme)이 포함 되어 있는지 확인 합니다.

마이그레이션 프로젝트 중 한 사람이 조직의 규모와 구조에 따라 여러 역할을 수행 하거나 여러 사용자가 각 역할을 수행 하는 경우가 있습니다. 또한 보안 환경에서 주요 역할을 수행 하는 다른 팀에 대 한 종속성이 있을 수 있습니다.

다음 표에서는 주요 역할 및 해당 기여를 포함 합니다.

| 역할          | Contributions                                              |
| ------------- | ---------------------------------------------------------- |
| **Project Manager** | Project coach는 다음을 포함 하 여 프로젝트를 안내 합니다.<br /> -경영진 지원 얻기<br /> -관련자에 게 가져오기<br /> -일정, 설명서 및 통신 관리 |
| **Id 설계자/Azure AD 앱 관리자** | 이러한 작업은 다음을 담당 합니다.<br /> -관련자와 협력 하 여 솔루션 디자인<br /> -운영 팀에 전달 하기 위한 솔루션 디자인 및 운영 절차 문서화<br /> -프리 프로덕션 환경과 프로덕션 환경을 관리 합니다. |
| **온-프레미스 AD 운영 팀** | AD 포리스트, LDAP 디렉터리, HR 시스템 등의 다양 한 온-프레미스 id 원본을 관리 하는 조직입니다.<br /> -동기화 하기 전에 필요한 모든 수정 작업을 수행 합니다.<br /> -동기화에 필요한 서비스 계정을 제공 합니다.<br /> -Azure AD에 대 한 페더레이션을 구성 하기 위한 액세스 권한을 제공 합니다. |
| **IT 지원 관리자** | 고객 지원팀 관점에서 이 변경의 지원 가능성에 대한 입력을 제공할 수 있는 IT 지원 조직 출신 담당자입니다. |
| **보안 소유자**  | 계획이 조직의 보안 요구 사항을 충족 하는지 확인할 수 있는 보안 팀의 담당자입니다. |
| **응용 프로그램 기술 소유자** | Azure AD와 통합 되는 앱 및 서비스의 기술 소유자를 포함 합니다. 동기화 프로세스에 포함 해야 하는 응용 프로그램의 id 특성을 제공 합니다. 일반적으로 CSV 담당자와 관계가 있습니다. |
| **응용 프로그램 비즈니스 소유자** | 사용자 환경에 대 한 입력을 제공 하 고 사용자의 관점에서이 변경의 유용성을 제공할 수 있으며 응용 프로그램의 전반적인 비즈니스 측면을 소유 하는 담당자 동료는 액세스 관리를 포함할 수 있습니다. |
| **파일럿 사용자 그룹** | 일상 작업의 일부로 테스트 하 고 파일럿 환경을 제공 하며 나머지 배포를 안내 하는 피드백을 제공 하는 사용자입니다. |

### <a name="plan-communications"></a>통신 계획

효과적인 비즈니스 참여 및 통신은 성공 하는 데 중요 합니다. 관련자와 최종 사용자에 게 정보를 얻고 일정 업데이트에 대 한 정보를 통로 하는 데 필요한 정보를 제공 하는 것이 중요 합니다. 마이그레이션의 가치, 예상 되는 타임 라인의 가치 및 임시 비즈니스 중단을 계획 하는 방법에 대 한 모든 것을 교육 합니다. 브리핑 세션, 전자 메일, 일대일 모임, 배너 및 townhalls와 같은 여러 가지 방법을 사용 합니다.

앱에 대해 선택한 통신 전략에 따라 보류 중인 가동 중지 시간을 사용자에 게 알려 줄 수 있습니다. 또한 배포를 연기 해야 하는 최근 변경 사항이 나 비즈니스 영향을 주지 않는지 확인 해야 합니다.

다음 표에서는 관련자에 게 알려 주는 최소한의 제안 된 통신을 찾습니다.

**계획 단계 및 프로젝트 전략**:

| 통신      | 사용자                                          |
| ------------------ | ------------------------------------------------- |
| Project의 인식 및 비즈니스/기술 가치 | 최종 사용자를 제외한 모든 사용자 |
| 파일럿 앱에 대 한 요청 | -앱 비즈니스 소유자<br />-앱 기술 소유자<br />-설계자 및 Id 팀 |

**1 단계-검색 및 범위**:

| 통신      | 사용자                                          |
| ------------------ | ------------------------------------------------- |
| -응용 프로그램 정보에 대 한 요청<br />-범위 지정 연습의 결과 | -앱 기술 소유자<br />-앱 비즈니스 소유자 |

**2 단계-앱 분류 및 파일럿 계획**:

| 통신      | 사용자                                          |
| ------------------ | ------------------------------------------------- |
| -분류의 결과 및 마이그레이션 일정의 의미<br />-예비 마이그레이션 일정 | -앱 기술 소유자<br /> -앱 비즈니스 소유자 |

**3 단계-마이그레이션 및 테스트 계획**:

| 통신      | 사용자                                          |
| ------------------ | ------------------------------------------------- |
| -응용 프로그램 마이그레이션 테스트의 결과 | -앱 기술 소유자<br />-앱 비즈니스 소유자 |
| -마이그레이션이 제공 되는 알림과 결과 최종 사용자 환경에 대 한 설명입니다.<br />-현재 수행 해야 하는 작업, 사용자 의견 및 도움을 받는 방법 등의 가동 중지 시간 및 전체 통신 | -최종 사용자 (및 기타 모든 항목) |

**4 단계 – 정보 관리 및 얻기**:

| 통신      | 사용자                                          |
| ------------------ | ------------------------------------------------- |
| 사용 가능한 분석 및 액세스 방법 | -앱 기술 소유자<br />-앱 비즈니스 소유자 |

### <a name="migration-states-communication-dashboard"></a>마이그레이션 상태 통신 대시보드

마이그레이션 프로젝트의 전체 상태를 전달 하는 것은 진행 상황을 표시 하는 데 중요 하며, 앱 소유자가 마이그레이션을 준비 하기 위해 앱을 출시 하는 데 도움이 됩니다. Power BI 또는 다른 보고 도구를 사용 하 여 간단한 대시보드를 통합 하 여 마이그레이션하는 동안 응용 프로그램의 상태를 파악할 수 있습니다.

를 사용 하 여 고려할 수 있는 마이그레이션 상태는 다음과 같습니다.

| 마이그레이션 상태       | 작업 계획                                   |
| ---------------------- | --------------------------------------------- |
| **초기 요청** | 앱을 찾고 소유자에 게 자세한 정보를 문의 합니다. |
| **평가 완료** | 앱 소유자는 앱 요구 사항을 평가 하 고 앱 질문을 반환 합니다.</td>
| **구성 진행 중** | Azure AD에 대 한 인증을 관리 하는 데 필요한 변경 사항 개발 |
| **테스트 구성 성공** | 테스트 환경에서 Azure AD 테 넌 트 테스트와 비교 하 여 변경 내용을 평가 하 고 앱을 인증 합니다. |
| **프로덕션 구성 성공** | 프로덕션 AD 테 넌 트에 대해 작동 하도록 구성을 변경 하 고 테스트 환경에서 앱 인증을 평가 합니다. |
| **완료/로그 오프** | 앱에 대 한 변경 내용을 프로덕션 환경에 배포 하 고 프로덕션 Azure AD 테 넌 트에 대해를 실행 합니다. |

이렇게 하면 앱 소유자가 앱 마이그레이션 및 테스트 일정을 파악할 수 있으며, 그 결과는 이미 마이그레이션된 다른 앱의 결과를 확인할 수 있습니다. 또한 마이그레이션되는 앱에 대 한 문제를 해결 하기 위해 소유자를 위한 버그 추적 장치 데이터베이스에 대 한 링크를 제공 하는 것이 좋습니다.

### <a name="best-practices"></a>모범 사례

다음은 고객 및 파트너의 성공 사례와 권장 모범 사례입니다.

- Microsoft 클라우드 솔루션을 안전 하 게 배포 하는 데 중점을 둔 파트너 네트워크의 구성원 인 Patriot 컨설팅에서 [Azure Active Directory 하는 마이그레이션 프로세스를 개선 하는 5 가지 팁](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Five-tips-to-improve-the-migration-process-to-Azure-Active/ba-p/445364) 입니다.

- IAM 및 위험 관리 솔루션을 중심으로 하는 Edgile로 [AZURE AD 응용 프로그램 마이그레이션에 대 한 위험 관리 전략을 개발](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Develop-a-risk-management-strategy-for-your-Azure-AD-application/ba-p/566488) 합니다.

## <a name="phase-1-discover-and-scope-apps"></a>1 단계: 앱 검색 및 범위

**응용 프로그램 검색 및 분석은 좋은 시작을 제공 하기 위한 기본적인 연습입니다.** 알 수 없는 앱을 수용 하기 위해 준비할 모든 항목을 알 수 없습니다.

### <a name="find-your-apps"></a>앱 찾기

응용 프로그램 마이그레이션의 첫 번째 결정 사항은 마이그레이션할 앱 (남아 있는 경우)과 사용 중단 앱입니다. 조직에서 사용 하지 않을 앱을 항상 사용 중단 수 있는 기회가 있습니다. 여러 가지 방법으로 조직에서 앱을 찾을 수 있습니다. **앱을 검색 하는 동안 개발 및 계획 된 앱을 포함 하 고 있는지 확인 하세요. 모든 향후 앱에서 인증에 Azure AD를 사용 합니다.**

**Active Directory Federation Services (AD FS)를 사용 하 여 올바른 앱 인벤토리 수집:**

- **Azure AD Connect Health 사용.** Azure AD Premium 라이선스를 사용 하는 경우 온-프레미스 환경의 앱 사용을 분석 하기 위해 [Azure AD Connect Health](../hybrid/how-to-connect-health-adfs.md) 를 배포 하는 것이 좋습니다. [Adfs 응용 프로그램 보고서](./migrate-adfs-application-activity.md) (미리 보기)를 사용 하 여 마이그레이션할 수 있는 adfs 응용 프로그램을 검색 하 고 마이그레이션할 응용 프로그램의 준비 상태를 평가할 수 있습니다. 마이그레이션을 완료 한 후에는 클라우드 내에서 조직의 섀도 IT를 지속적으로 모니터링할 수 있는 [Cloud Discovery](/cloud-app-security/set-up-cloud-discovery) 를 배포 합니다.

- **로그 구문 분석을 AD FS** 합니다. Azure AD Premium 라이선스가 없는 경우 [PowerShell](https://github.com/AzureAD/Deployment-Plans/tree/master/ADFS%20to%20AzureAD%20App%20Migration)을 기반으로 하는 Azure AD 앱 마이그레이션 도구에 ADFS를 사용 하는 것이 좋습니다. [솔루션 가이드](./migrate-adfs-apps-to-azure.md)를 참조 하세요.

[Active Directory Federation Services (AD FS)에서 Azure AD로 앱을 마이그레이션하는 중입니다.](./migrate-adfs-apps-to-azure.md)

### <a name="using-other-identity-providers-idps"></a>다른 id 공급자 사용 (IdPs)

다른 id 공급자 (예: Okta 또는 Ping)의 경우 해당 도구를 사용 하 여 응용 프로그램 인벤토리를 내보낼 수 있습니다. 조직의 웹 앱에 해당 하는 Active Directory에 등록 된 서비스 원칙을 살펴보는 것이 좋습니다.

### <a name="using-cloud-discovery-tools"></a>Cloud discovery 도구 사용

클라우드 환경에서는 모든 클라우드 서비스에서 사이버 위협을 찾아 공격 하는 데 필요한 다양 한 가시성, 데이터 이동에 대 한 제어 및 정교한 분석이 필요 합니다. 다음 도구를 사용 하 여 클라우드 앱 인벤토리를 수집할 수 있습니다.

- **Casb (Cloud Access Security Broker**) – [casb](/cloud-app-security/) 는 일반적으로 방화벽을 함께 사용 하 여 직원의 클라우드 응용 프로그램 사용에 대 한 가시성을 제공 하 고 사이버 보안 위협 으로부터 회사 데이터를 보호 하는 데 도움이 됩니다. CASB 보고서는 조직에서 가장 많이 사용 되는 앱과 Azure AD로 마이그레이션할 초기 대상을 결정 하는 데 도움이 될 수 있습니다.

- **Cloud Discovery** - [Cloud Discovery](/cloud-app-security/set-up-cloud-discovery)를 구성 하 여 클라우드 앱 사용에 대 한 가시성을 얻고 비 사용 권한 또는 섀도 IT 앱을 검색할 수 있습니다.

- **Api** -클라우드 인프라에 연결 된 앱의 경우 해당 시스템의 api 및 도구를 사용 하 여 호스트 된 앱의 인벤토리를 시작할 수 있습니다. Azure 환경에서:

  - Azure websites에 대 한 정보를 가져오려면 [AzureWebsite](/powershell/module/servicemanagement/azure.service/get-azurewebsite) cmdlet을 사용 합니다.

  - [New-azurermwebapp](/powershell/module/azurerm.websites/get-azurermwebapp) cmdlet을 사용 하 여 Azure Web Apps에 대 한 정보를 가져옵니다.
D
  - [AppCmd.exe](/iis/get-started/getting-started-with-iis/getting-started-with-appcmdexe#working-with-sites-applications-virtual-directories-and-application-pools)를 사용 하 여 Windows 명령줄에서 Microsoft IIS에서 실행 되는 모든 앱을 찾을 수 있습니다.

  - [응용 프로그램](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#application-entity) 및 [서비스 주체](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#serviceprincipal-entity) 를 사용 하 여 Azure AD의 디렉터리에 있는 앱 및 앱 인스턴스에 대 한 정보를 얻습니다.

### <a name="using-manual-processes"></a>수동 프로세스 사용

위에서 설명한 자동화 된 방법을 수행 하면 응용 프로그램에 대 한 좋은 핸들을 갖게 됩니다. 그러나 다음을 수행 하 여 모든 사용자 액세스 영역에서 적절 한 검사가 수행 되도록 할 수 있습니다.

- 조직의 여러 비즈니스 소유자에 게 문의 하 여 조직에서 사용 중인 응용 프로그램을 찾으십시오.

- 프록시 서버에서 HTTP 검사 도구를 실행 하거나 프록시 로그를 분석 하 여 트래픽이 일반적으로 라우팅되는 위치를 확인 합니다.

- 자주 사용 하는 회사 포털 사이트의 웹 로그를 검토 하 여 사용자가 가장 많이 액세스 하는 링크를 확인 합니다.

- 임원 또는 다른 주요 비즈니스 구성원에 게 연락 하 여 업무상 중요 한 앱을 확인 하세요.

### <a name="type-of-apps-to-migrate"></a>마이그레이션할 앱 유형

앱을 찾았으면 조직에서 다음과 같은 유형의 앱을 식별 합니다.

- 최신 인증 프로토콜을 이미 사용 하는 앱

- 현대화 하도록 선택한 레거시 인증 프로토콜을 사용 하는 앱

- 현대화 하지 않도록 선택한 레거시 인증 프로토콜을 사용 하는 앱

- 새 LoB (기간 업무) 앱

### <a name="apps-that-use-modern-authentication-already"></a>최신 인증을 사용 하는 앱

이미 현대화 된 앱은 Azure AD로 이동 될 가능성이 높습니다. 이러한 앱은 이미 최신 인증 프로토콜 (예: SAML 또는 Openid connect Connect)을 사용 하며 Azure AD를 사용 하 여 인증 하도록 다시 구성할 수 있습니다.

[AZURE ad 앱 갤러리](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps) 의 선택 사항 외에도 이러한 앱은 azure ad 갤러리 ([비 갤러리 응용 프로그램)](./add-application-portal.md)의 일부가 아닌 공급 업체의 조직 또는 타사 앱에 이미 있는 앱 일 수 있습니다.

현대화 하도록 선택한 레거시 앱

현대화 하는 레거시 앱의 경우 Azure AD로 이동 하 여 핵심 인증 및 권한 부여는 [Microsoft Graph](https://developer.microsoft.com/graph/gallery/?filterBy=Samples,SDKs) 및 [Intelligent Security Graph](https://www.microsoft.com/security/operations/intelligence?rtc=1) 가 제공 해야 하는 모든 기능과 데이터의 잠금을 해제 합니다.

이러한 응용 프로그램에 대 한 인증 스택 코드를 레거시 프로토콜 (예: Windows-Integrated 인증, Kerberos 제한 된 위임, HTTP 헤더 기반 인증)에서 최신 프로토콜 (예: SAML 또는 Openid connect Connect)로 **업데이트 하는** 것이 좋습니다.

### <a name="legacy-apps-that-you-choose-not-to-modernize"></a>현대화 하지 않도록 선택한 레거시 앱

레거시 인증 프로토콜을 사용 하는 특정 앱의 경우 현대화를 사용 하는 것이 비즈니스 상의 이유로 적합 하지 않은 경우도 있습니다. 여기에는 다음과 같은 유형의 앱이 포함 됩니다.

- 규정 준수 또는 제어 이유 때문에 온-프레미스에 유지 되는 앱입니다.

- 변경 하지 않으려는 온-프레미스 id 또는 페더레이션 공급자에 연결 된 앱입니다.

- 이동할 계획이 없는 온-프레미스 인증 표준을 사용 하 여 개발 된 앱

Azure AD는 최신 Azure AD 보안 및 거 버 넌 스 기능 (예: [Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md), [조건부 액세스](../conditional-access/overview.md), [Id 보호](../identity-protection/index.yml), [위임 된 응용 프로그램 액세스](./access-panel-manage-self-service-access.md), 앱을 건드리지 않고도 이러한 앱에 대 한 [액세스 검토](../governance/manage-user-access-with-access-reviews.md#create-and-perform-an-access-review) )을 사용할 수 있으므로 이러한 레거시 앱에 뛰어난 이점을 누릴 수 있습니다.

Azure AD 응용 프로그램 프록시를 사용 하 여 Azure AD [응용 프로그램 프록시](./application-proxy-configure-single-sign-on-password-vaulting.md) 를 통해 **이러한 앱을 클라우드로 확장** 하 여 사용자가 신속 하 게 마이그레이션할 수 있도록 하거나 이미 배포 했을 수 있는 응용 프로그램 배달 컨트롤러와 [파트너를 통합](https://azure.microsoft.com/services/active-directory/sso/secure-hybrid-access/) 하 여 사용자를 신속 하 게 마이그레이션할 수 있습니다.

### <a name="new-line-of-business-lob-apps"></a>새 LoB (기간 업무) 앱

일반적으로 조직의 사내 사용을 위한 LoB 앱을 개발 합니다. 파이프라인에 새 앱이 있는 경우 [Microsoft Id 플랫폼](../develop/v2-overview.md) 을 사용 하 여 openid connect Connect를 구현 하는 것이 좋습니다.

### <a name="apps-to-deprecate"></a>사용 중단 앱

확실 한 소유자가 없는 앱 및 유지 관리 및 모니터링의 선택을 취소 하면 조직에 보안 위험이 발생 합니다. 다음 경우에 응용 프로그램 사용 중단 고려:

- 해당 **기능은 다른 시스템과 매우 중복 됩니다** . • **비즈니스 소유자가 없습니다** .

- 분명히 **아무런 사용법이 없습니다**.

**높은 영향, 업무상 중요 한 응용 프로그램은 사용 중단 하지** 않는 것이 좋습니다. 이러한 경우 비즈니스 소유자에 게 문의 하 여 올바른 전략을 결정 해야 합니다.

### <a name="exit-criteria"></a>종료 기준

이 단계에서는 다음을 수행 했습니다.

- 마이그레이션의 범위에 있는 시스템에 대해 잘 알고 있어야 합니다 (Azure AD로 이동한 후에는 사용 중지할 수 있음).

- 다음을 포함 하는 앱 목록:

  - 해당 앱이 연결 되는 시스템
  - 사용자가 액세스 하는 위치 및 위치에서
  - 마이그레이션, 사용 되지 않음 또는 [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md)연결 여부

> [!NOTE]
> [응용 프로그램 검색 워크시트](https://download.microsoft.com/download/2/8/3/283F995C-5169-43A0-B81D-B0ED539FB3DD/Application%20Discovery%20worksheet.xlsx) 를 다운로드 하 여 Azure AD 인증으로 마이그레이션하려는 응용 프로그램 및 [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md)를 사용 하 여 관리 하려는 응용 프로그램을 기록할 수 있습니다.

## <a name="phase-2-classify-apps-and-plan-pilot"></a>2 단계: 앱 분류 및 파일럿 계획

앱의 마이그레이션을 분류 하는 것은 중요 한 연습입니다. 모든 앱을 동시에 마이그레이션하고 전환 해야 하는 것은 아닙니다. 각 앱에 대 한 정보를 수집한 후에는 먼저 마이그레이션해야 하는 앱을 합리화 수 있습니다.

### <a name="classify-in-scope-apps"></a>범위 내 앱 분류

이를 고려 하는 한 가지 방법은 각각 여러 요소에 따라 달라 지는 비즈니스의 중요도, 사용량 및 수명의 축에 따라 결정 됩니다.

### <a name="business-criticality"></a>비즈니스 중요성

비즈니스 중요도는 각 비즈니스에 대해 서로 다른 차원을 사용 하지만 고려해 야 할 두 가지 조치는 **기능** 및 **사용자 프로필** 입니다. 중복 되거나 사용 되지 않는 기능을 사용 하는 것 보다 고유한 기능이 있는 앱을 할당 합니다.

![기능 및 사용자 프로필 & 기능에 대 한 spectrums 다이어그램](media/migrating-application-authentication-to-azure-active-directory-3.jpg)

### <a name="usage"></a>사용량

**사용량이 많은** 응용 프로그램은 사용량이 적은 앱 보다 높은 값을 받게 됩니다. 외부, 임원 또는 보안 팀 사용자를 사용 하 여 앱에 더 높은 값을 할당 합니다. 마이그레이션 포트폴리오의 각 앱에 대해 이러한 평가를 완료 합니다.

![사용자 볼륨 및 사용자 범위에 대 한 spectrums 다이어그램](media/migrating-application-authentication-to-azure-active-directory-4.jpg)

비즈니스 중요도 및 사용에 대 한 값을 결정 한 후에는 **응용 프로그램 수명을** 결정 하 고 우선 순위 행렬을 만들 수 있습니다. 아래 행렬을 참조 하세요.

![사용량, 예상 되는 수명 및 비즈니스 중요도 간의 관계를 보여 주는 삼각형 다이어그램](media/migrating-application-authentication-to-azure-active-directory5.jpg)

### <a name="prioritize-apps-for-migration"></a>마이그레이션을 위해 앱 우선 순위 지정

조직의 요구에 따라 우선 순위가 가장 낮은 앱 또는 우선 순위가 가장 높은 앱을 사용 하 여 앱 마이그레이션을 시작 하도록 선택할 수 있습니다.

Azure AD 및 Id 서비스를 사용 하는 경험이 없는 시나리오에서 **우선 순위가 가장 낮은 앱** 을 먼저 azure ad로 이동 하는 것이 좋습니다. 이렇게 하면 비즈니스 영향이 최소화 되 고 모멘텀을 빌드할 수 있습니다. 이러한 앱을 성공적으로 이동 하 고 관련자의 확신을 얻은 후에는 다른 앱을 계속 마이그레이션할 수 있습니다.

명확한 우선 순위가 없는 경우 [AZURE AD 갤러리](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps) 에 있는 앱을 먼저 이동 하는 것이 좋습니다 .이를 쉽게 통합할 수 있기 때문에 여러 id 공급자 (ADFS 또는 okta)를 지원 해야 합니다. 이러한 앱은 조직에서 **우선 순위가 가장 높은 앱** 이 될 수 있습니다. SaaS 응용 프로그램을 Azure AD와 통합 하는 데 도움이 되도록 구성 과정을 안내 하는 [자습서](../saas-apps/tutorial-list.md) 컬렉션을 개발 했습니다.

앱을 마이그레이션할 최종 기한이 있는 경우 우선 순위가 가장 높은 앱 버킷에 주요 작업을 수행 합니다. 최종 기한을 이동한 경우에도 비용을 변경 하지 않으므로 우선 순위가 낮은 앱을 선택할 수 있습니다. 라이선스를 갱신 해야 하는 경우에도 소량의 크기를 사용 합니다.

이 분류 외에도 마이그레이션의 긴급도에 따라 앱 소유자가 앱을 마이그레이션하기 위해 참여 해야 하는 **마이그레이션 일정** 을 설정 하는 것을 고려할 수 있습니다. 이 프로세스가 끝나면 마이그레이션에 대해 우선 순위가 지정 된 버킷으로 모든 응용 프로그램의 목록이 표시 됩니다.

### <a name="document-your-apps"></a>앱 문서화

먼저 응용 프로그램에 대 한 주요 정보를 수집 하 여 시작 합니다. [응용 프로그램 검색 워크시트](https://download.microsoft.com/download/2/8/3/283F995C-5169-43A0-B81D-B0ED539FB3DD/Application%20Discovery%20worksheet.xlsx)는 마이그레이션 결정을 신속 하 게 수행 하 고 비즈니스 그룹에 대 한 권장 사항을 항상 제공 하는 데 도움이 됩니다.

마이그레이션 결정을 내리는 데 중요 한 정보에는 다음이 포함 됩니다.

- **앱 이름** – 비즈니스에 알려진이 앱은 무엇 인가요?

- **앱 유형** – 타사 SaaS 앱 입니까? 사용자 지정 lob (기간 업무) 웹 앱 API?

- **비즈니스 중요도** – 높은 중요도가 있나요? 거의? 무엇 인가?

- **사용자 액세스 볼륨** -모든 사용자가이 앱에 액세스 합니까 아니면 몇 명의 사용자에 게 액세스 하나요?

- **계획 된 수명** –이 앱은 얼마나 걸립니까? 6 개월 이내 입니까? 2 년 이상 인가요?

- **현재 id 공급자** –이 앱에 대 한 기본 IdP? 또는 로컬 저장소를 사용 하나요?

- **인증 방법** – 앱이 오픈 표준을 사용 하 여 인증 합니까?

- **앱 코드를 업데이트할 계획 인지 여부** -앱이 계획 된 개발 또는 활성 개발에 있습니까?

- **앱을 온-프레미스로 유지할지 여부** – 데이터 센터에 응용 프로그램을 유지 하 시겠습니까?

- 앱이 **다른 앱 또는 api에 종속 되는지 여부** -앱이 현재 다른 앱 또는 api를 호출 하나요?

- **앱이 AZURE ad 갤러리에 있는지 여부** – 현재 앱이 [azure ad 갤러리](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps)와 이미 통합 되어 있나요?

나중에도 도움이 되는 기타 데이터는 즉각적인 마이그레이션 결정을 내리는 데 필요 하지 않습니다.

- **앱 URL** -사용자가 앱에 액세스 하려면 어떻게 해야 하나요?

- **앱 설명** – 앱의 용도에 대 한 간단한 설명입니다.

- **앱 소유자** – 비즈니스에서 앱에 대 한 기본 POC 인 사람은 누구 인가요?

- **일반 설명 또는 메모** -앱 또는 비즈니스 소유권에 대 한 기타 일반 정보

응용 프로그램을 분류 하 고 세부 정보를 문서화 한 후에는 계획 된 마이그레이션 전략에 대 한 비즈니스 소유자 구매를 확보 해야 합니다.

### <a name="plan-a-pilot"></a>파일럿 계획

파일럿을 위해 선택 하는 앱은 조직의 주요 id 및 보안 요구 사항을 나타내고 응용 프로그램 소유자에 게 서 구매를 명확 하 게 해야 합니다. 파일럿은 일반적으로 별도의 테스트 환경에서 실행 됩니다. 배포 계획 페이지에서 [파일럿에 대 한 모범 사례](../fundamentals/active-directory-deployment-plans.md#best-practices-for-a-pilot) 를 참조 하세요.

**외부 파트너를 잊지 마세요.** 마이그레이션 일정 및 테스트에 참여 하는지 확인 합니다. 마지막으로, 중단 된 문제가 있는 경우 기술 지원팀에 액세스할 수 있는 방법이 있는지 확인 합니다.

### <a name="plan-for-limitations"></a>제한 사항 계획

일부 앱은 쉽게 마이그레이션할 수 있지만 여러 서버나 인스턴스로 인해 다른 앱이 더 오래 걸릴 수 있습니다. 예를 들어 사용자 지정 로그인 페이지 때문에 SharePoint 마이그레이션이 더 오래 걸릴 수 있습니다.

많은 SaaS 앱 공급 업체는 SSO 연결 변경에 대 한 요금을 청구 합니다. 해당 항목을 확인 하 고이에 대 한 계획을 세워야 합니다.

또한 Azure AD에는 알고 있어야 하는 [서비스 제한 및 제한 사항이](../enterprise-users/directory-service-limits-restrictions.md) 있습니다.

### <a name="app-owner-sign-off"></a>앱 소유자 로그 오프

업무상 중요 하 고 널리 사용 되는 응용 프로그램은 파일럿 단계에서 앱을 테스트 하기 위한 파일럿 사용자 그룹이 필요할 수 있습니다. 사전 프로덕션 또는 파일럿 환경에서 앱을 테스트 한 후에는 앱을 마이그레이션하기 전에 앱 비즈니스 소유자가 성능에 대해 로그 오프 하 고 인증을 위해 Azure AD를 프로덕션으로 사용 하도록 해야 합니다.

### <a name="plan-the-security-posture"></a>보안 상태 계획

마이그레이션 프로세스를 시작 하기 전에 회사 id 시스템에 대해 개발 하려는 보안 환경을 완벽 하 게 고려 하는 것이 좋습니다. 이는 **데이터에 액세스 하는 id, 장치 및 위치와** 같은 중요 한 정보 집합을 수집 하는 것을 기반으로 합니다.

### <a name="identities-and-data"></a>Id 및 데이터

대부분의 조직에는 산업 부문 및 조직 내의 직무 기능에 따라 달라 지는 id 및 데이터 보호에 대 한 특정 요구 사항이 있습니다. 지정 된 [조건부 액세스 정책](../conditional-access/overview.md) 집합 및 관련 기능을 비롯 한 권장 사항은 [id 및 장치 액세스 구성](/microsoft-365/enterprise/microsoft-365-policies-configurations) 을 참조 하세요.

이 정보를 사용 하 여 Azure AD와 통합 된 모든 서비스에 대 한 액세스를 보호할 수 있습니다. 이러한 권장 사항은 [AZURE AD의](../fundamentals/identity-secure-score.md)Microsoft 보안 점수 및 id 점수와 함께 정렬 됩니다. 점수는 다음에 도움이 됩니다.

- ID 보안 상태를 객관적으로 측정

- ID 보안 개선 계획

- 개선 계획의 성공 여부 검토

또한이를 통해 [id 인프라를 보호 하는 5 단계](../../security/fundamentals/steps-secure-identity.md)를 구현할 수 있습니다. 조직의 특정 요구 사항에 맞게 지침을 시작 지점으로 사용 하 고 정책을 조정 합니다.

### <a name="who-is-accessing-your-data"></a>누가 데이터에 액세스 하나요?

Azure AD에서 지 원하는 앱 및 리소스의 두 가지 주요 범주는 다음과 같습니다.

- **내부:** Id 공급자 내에 계정이 있는 직원, 계약자 및 공급 업체. 관리자 또는 리더십과 다른 직원에 대해 다른 규칙을 사용 하 여 추가 피벗이 필요할 수 있습니다.

- **외부:** [AZURE AD B2B 공동 작업](../external-identities/what-is-b2b.md) 을 사용 하 여 일반적인 비즈니스 과정에서 조직과 상호 작용 하는 공급 업체, 공급자, 배포자 또는 기타 비즈니스 파트너입니다.

이러한 사용자에 대 한 그룹을 정의 하 고 이러한 그룹을 다양 한 방식으로 채울 수 있습니다. 관리자가 구성원을 수동으로 그룹에 추가 하도록 선택 하거나 selfservice 그룹 멤버 자격을 사용 하도록 설정할 수 있습니다. [동적 그룹](../enterprise-users/groups-dynamic-membership.md)을 사용 하 여 지정 된 조건에 따라 그룹에 구성원을 자동으로 추가 하는 규칙을 설정할 수 있습니다.

외부 사용자는 고객을 참조할 수도 있습니다. [Azure AD B2C](../../active-directory-b2c/overview.md)별도의 제품은 고객 인증을 지원 합니다. 그러나이 내용은이 문서의 범위를 벗어납니다.

### <a name="devicelocation-used-to-access-data"></a>데이터에 액세스 하는 데 사용 되는 장치/위치

사용자가 앱에 액세스 하는 데 사용 하는 장치 및 위치도 중요 합니다. 회사 네트워크에 물리적으로 연결 된 장치는 더 안전 합니다. VPN을 통해 네트워크 외부에서 연결 하는 경우에는 검사가 필요할 수 있습니다.

![사용자 위치와 데이터 액세스 간의 관계를 보여 주는 다이어그램](media/migrating-application-authentication-to-azure-active-directory-6.jpg)

이러한 측면에서 리소스, 사용자 및 장치를 염두에 두면 [AZURE AD 조건부 액세스](../conditional-access/overview.md) 기능을 사용 하도록 선택할 수 있습니다. 조건부 액세스는 사용자 권한을 초과 합니다. 즉, 사용자 또는 그룹의 id, 사용자가 연결 된 네트워크, 사용 중인 장치 및 응용 프로그램, 액세스 하려는 데이터 형식 등의 요소 조합을 기반으로 합니다. 사용자에 게 부여 된 액세스는 보다 광범위 한 조건 집합에 적응 합니다.

### <a name="exit-criteria"></a>종료 기준

이 단계에서는 다음과 같은 작업을 수행 합니다.

- 앱 파악
  - 마이그레이션하려는 앱을 완벽 하 게 문서화
  - 비즈니스 중요도, 사용 볼륨 및 수명에 따라 우선 순위가 지정 된 앱이 있어야 합니다.

- 파일럿에 대 한 요구 사항을 나타내는 앱을 선택 합니다.

- 우선 순위 및 전략에 대 한 비즈니스 소유자 구매

- 보안 상태에 대 한 요구 사항 및 구현 방법 이해

## <a name="phase-3-plan-migration-and-testing"></a>3 단계: 마이그레이션 및 테스트 계획

비즈니스를 구매한 후 다음 단계는 이러한 앱을 Azure AD 인증으로 마이그레이션하기 시작 하는 것입니다.

### <a name="migration-tools-and-guidance"></a>마이그레이션 도구 및 지침

아래 도구와 지침을 사용 하 여 응용 프로그램을 Azure AD로 마이그레이션하는 데 필요한 정확한 단계를 따르세요.

- **일반 마이그레이션 지침** – [Azure AD apps 마이그레이션 도구 키트](./migration-resources.md) 의 백서, 도구, 전자 메일 템플릿 및 응용 프로그램 질문을 사용 하 여 앱을 검색, 분류 및 마이그레이션합니다.

- **Saas 응용 프로그램** – 종단 간 프로세스를 안내 하는 [수백 개의 saas 앱 자습서](../saas-apps/tutorial-list.md) 및 전체 [Azure AD SSO 배포 계획](https://aka.ms/ssodeploymentplan) 목록을 참조 하세요.

- **온-프레미스에서 실행 되는 응용 프로그램** - [azure AD 응용 프로그램 프록시에](./application-proxy.md) 대해 알아보고 전체 [azure AD 응용 프로그램 프록시 배포 계획](https://aka.ms/AppProxyDPDownload) 을 사용 하 여 빠르게 진행 하세요.

- **개발 중인 앱** – 단계별 [통합](../develop/quickstart-register-app.md) 및 [등록](../develop/quickstart-register-app.md) 지침을 읽어 보세요.

마이그레이션 후 성공적인 배포를 사용자에 게 알리는 통신을 보내도록 선택 하 고 수행 해야 하는 새로운 단계를 알릴 수 있습니다.

### <a name="plan-testing"></a>테스트 계획

마이그레이션을 진행 하는 동안 앱은 정기 배포 중에 이미 테스트 환경을 사용 하 고 있을 수 있습니다. 마이그레이션 테스트에이 환경을 계속 사용할 수 있습니다. 테스트 환경을 현재 사용할 수 없는 경우 응용 프로그램의 아키텍처에 따라 Azure App Service 또는 Azure Virtual Machines를 사용 하 여 설정할 수 있습니다. 앱 구성을 개발할 때 사용할 개별 테스트 Azure AD 테 넌 트를 설정 하도록 선택할 수 있습니다. 이 테 넌 트는 정리 된 상태로 시작 되며 어떤 시스템과도 동기화 하도록 구성 되지 않습니다.

테스트 사용자로 로그인 하 여 각 응용 프로그램을 테스트 하 고 모든 기능이 마이그레이션 이전과 동일한 지 확인 합니다. 테스트 중에 사용자가 [MFA](/azure/active-directory/authentication/howto-mfa-userstates) 또는 [SSPR](../authentication/tutorial-enable-sspr.md)설정을 업데이트 해야 하는지 또는 마이그레이션 중에이 기능을 추가 해야 하는지 확인 하는 경우 최종 사용자 통신 계획에이 기능을 추가 해야 합니다. [MFA](https://aka.ms/mfatemplates) 및 [SSPR](https://aka.ms/ssprtemplates) 최종 사용자 통신 템플릿을 참조 하세요.

앱을 마이그레이션한 후에는 [Azure Portal](https://aad.portal.azure.com/) 로 이동 하 여 마이그레이션이 성공 했는지 테스트 합니다. 아래의 지침을 따르세요.

- **엔터프라이즈 응용 프로그램 &gt; 모든 응용** 프로그램을 선택 하 고 목록에서 앱을 찾습니다.

- **&gt; 사용자 및 그룹 관리** 를 선택 하 여 하나 이상의 사용자 또는 그룹을 앱에 할당 합니다.

- **&gt; 조건부 액세스 관리** 를 선택 합니다. 정책 목록을 검토 하 고 [조건부 액세스 정책을](../conditional-access/overview.md)사용 하 여 응용 프로그램에 대 한 액세스를 차단 하지 않는지 확인 합니다.

앱을 구성 하는 방법에 따라 SSO가 제대로 작동 하는지 확인 합니다.

| 인증 유형      | 테스트                                             |
| ------------------------ | --------------------------------------------------- |
| **OAuth/Openid connect Connect** | **엔터프라이즈 응용 프로그램 &gt; 사용 권한** 을 선택 하 고 앱에 대 한 사용자 설정에서 조직에 사용할 응용 프로그램에 동의한 확인 합니다. |
| **SAML 기반 SSO** | Single Sign-on 아래에 있는 [SAML 설정 테스트](./debug-saml-sso-issues.md) 단추를 사용 **합니다.** |
| **암호 기반 SSO** | [Myapps 보안 로그인 확장](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension)을 다운로드 하 여 설치 합니다. 이 확장은 SSO 프로세스를 사용 해야 하는 조직의 클라우드 앱을 시작 하는 데 도움이 됩니다. |

| **[응용 프로그램 프록시](./application-proxy.md)** | 커넥터가 실행 중이 고 응용 프로그램에 할당 되었는지 확인 합니다. 자세한 내용은 [응용 프로그램 프록시 문제 해결 가이드](./application-proxy-troubleshoot.md) 를 참조 하세요. |

### <a name="troubleshoot"></a>문제 해결

문제가 발생 하면 [앱 문제 해결 가이드](../app-provisioning/isv-automatic-provisioning-multi-tenant-apps.md) 를 확인 하 여 도움을 받으세요. 또한 문제 해결 문서를 확인 하 고 [SAML 기반 Single Sign-On 구성 된 앱에 로그인 하는 문제](/troubleshoot/azure/active-directory/troubleshoot-sign-in-saml-based-apps)를 확인할 수 있습니다.

### <a name="plan-rollback"></a>롤백 계획

마이그레이션이 실패 하는 경우 가장 좋은 전략은 롤백하고 테스트 하는 것입니다. 마이그레이션 문제를 완화 하기 위해 수행할 수 있는 단계는 다음과 같습니다.

- 앱의 기존 구성에 대 한 **스크린샷을 찍습니다** . 앱을 다시 구성 해야 하는 경우 다시 확인할 수 있습니다.

- 또한 클라우드 인증에 문제가 있는 경우 **레거시 인증에 대 한 링크를 제공 하는** 것을 고려할 수 있습니다.

- 마이그레이션을 완료 하기 전에 이전 id 공급자를 사용 하 여 **기존 구성을 변경 하지 마십시오** .

- 먼저 **여러 IdPs를 지 원하는 앱** 을 마이그레이션합니다. 문제가 발생 하는 경우 항상 기본 IdP의 구성으로 변경할 수 있습니다.

- 앱 환경에 **피드백 단추** 또는 **기술 지원팀** 문제에 대 한 포인터가 있는지 확인 합니다.

### <a name="exit-criteria"></a>종료 기준

이 단계에서는 다음과 같은 작업을 수행 했습니다.

- 각 앱을 마이그레이션하는 방법 결정

- 마이그레이션 도구 검토

- 테스트 환경 및 그룹을 포함 하 여 테스트 계획

- 계획 된 롤백

## <a name="phase-4-plan-management-and-insights"></a>4 단계: 관리 및 통찰력 계획

앱이 마이그레이션된 후 다음을 확인 해야 합니다.

- 사용자가 안전 하 게 액세스 하 고 관리할 수 있음

- 사용량 및 앱 상태에 대 한 적절 한 정보를 얻을 수 있습니다.

조직에 적절 한 다음 작업을 수행 하는 것이 좋습니다.

### <a name="manage-your-users-app-access"></a>사용자의 앱 액세스 관리

앱을 마이그레이션한 후에는 다양 한 방식으로 사용자의 경험을 보강할 수 있습니다.

**앱 검색 가능**

**사용자에** 게 [myapps](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension)포털 환경을 지정 합니다. 여기에서 모든 클라우드 기반 앱, [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md)를 사용 하 여 사용 가능 하도록 설정한 앱 및 [응용 프로그램 프록시](./application-proxy.md) 를 사용 하는 앱에 액세스할 수 있는 권한이 제공 된 앱에 액세스할 수 있습니다.


앱을 검색 하는 방법에 대 한 사용자를 안내할 수 있습니다.

- [기존 Single sign-on](./view-applications-portal.md) 기능을 사용 하 여 **사용자를 앱에 연결**


- 앱에 대 한 [셀프 서비스 응용 프로그램 액세스](./manage-self-service-access.md)를 사용 하도록 설정 하 고 **사용자가 원하는 앱을 추가 하도록 허용**

- 최종 사용자 (기본 Microsoft 앱 또는 다른 앱) [에서 응용 프로그램](./hide-application-from-user-portal.md) 을 숨겨 **앱을 더 검색 하기 위해 필요한 앱 만들기**

### <a name="make-apps-accessible"></a>앱에 액세스할 수 있도록 설정

**사용자가 모바일 장치에서 앱에 액세스할 수 있도록** 합니다. 사용자는 [iOS](./hide-application-from-user-portal.md) 7.0 이상 또는 [Android](./hide-application-from-user-portal.md) 장치에서 Intune 관리 브라우저를 사용 하 여 myapps 포털에 액세스할 수 있습니다.

사용자는 **Intune에서 관리 되는 브라우저** 를 다운로드할 수 있습니다.

- **Android 장치의** 경우 [Google play 스토어](/azure/active-directory/authentication/howto-mfa-userstates) 에서

- Apple **장치의** 경우 [apple 앱 스토어](https://itunes.apple.com/us/app/microsoft-intune-managed-browser/id943264951?mt=8) 에서 또는 [iOS 용 My Apps 모바일 앱](https://apps.apple.com/us/app/my-apps-azure-active-directory/id824048653) 을 다운로드할 수 있습니다.

**사용자가 브라우저 확장에서 앱을 열 수 있도록 합니다.**

사용자는 [Chrome,](https://chrome.google.com/webstore/detail/my-apps-secure-sign-in-ex/ggjhpefgjjfobnfoldnjipclpcfbgbhl) [FireFox](https://addons.mozilla.org/firefox/addon/access-panel-extension/) 또는 [Microsoft Edge](https://www.microsoft.com/p/my-apps-secure-sign-in-extension/9pc9sckkzk84?rtc=1&activetab=pivot%3Aoverviewtab) 에서 [myapps 보안 로그인 확장을 다운로드할](https://www.microsoft.com/p/my-apps-secure-sign-in-extension/9pc9sckkzk84?rtc=1&activetab=pivot%3Aoverviewtab) 수 있으며 브라우저 표시줄에서 바로 앱을 시작할 수 있습니다.

- **앱을 검색 하 고 가장 최근에 사용 된 앱을 표시 합니다.**

- [응용 프로그램 프록시에서](./application-proxy.md) 구성한 **내부 Url을 자동으로** 적절 한 외부 url로 변환 합니다. 이제 사용자는 어디에 있든 관계 없이 친숙 한 링크를 사용할 수 있습니다.

**사용자가 Office.com에서 앱을 열 수 있도록 합니다.**

사용자는 [Office.com](https://www.office.com/) 으로 이동 하 여 **앱을 검색 하 고 가장 최근에 사용한 앱** 이 작동 하는 위치에서 바로 표시 되도록 할 수 있습니다.

### <a name="secure-app-access"></a>보안 앱 액세스

Azure AD는 마이그레이션된 앱을 관리 하는 중앙 액세스 위치를 제공 합니다. [Azure Portal](https://portal.azure.com/) 로 이동 하 고 다음 기능을 사용 하도록 설정 합니다.

- **앱에 대 한 사용자 액세스를 보호 합니다.** 장치 상태, 위치 등을 기준으로 응용 프로그램에 대 한 사용자 액세스를 보호 하기 위해 [조건부 액세스 정책](../conditional-access/overview.md)또는 [id 보호](../identity-protection/overview-identity-protection.md)를 사용 하도록 설정 합니다.

- **자동 프로 비전.** 사용자가 액세스 해야 하는 다양 한 타사 SaaS 앱을 사용 하 여 [사용자의 자동 프로 비전](../app-provisioning/user-provisioning.md) 을 설정 합니다. 사용자 id를 만드는 것 외에도 상태 또는 역할이 변경 되는 사용자 id를 유지 관리 및 제거 하는 작업이 포함 됩니다.

- **사용자 액세스** **관리** 를 위임 합니다. 앱에 대 한 셀프 서비스 응용 프로그램 액세스를 사용 하도록 설정 하 고 *비즈니스 승인자를 할당 하 여 해당 앱에* 대 한 액세스를 승인 합니다. 앱 컬렉션에 할당 된 그룹에 대해 [셀프 서비스 그룹 관리](../enterprise-users/groups-self-service-management.md)를 사용 합니다.

- **관리 액세스를 위임 합니다.** **디렉터리 역할** 을 사용 하 여 관리자 역할 (예: 응용 프로그램 관리자, 클라우드 응용 프로그램 관리자 또는 응용 프로그램 개발자)을 사용자에 게 할당 합니다.

### <a name="audit-and-gain-insights-of-your-apps"></a>앱 감사 및 통찰력 얻기

또한 [Azure Portal](https://portal.azure.com/) 를 사용 하 여 중앙 위치에서 모든 앱을 감사할 수 있습니다.

- * * 엔터프라이즈 응용 프로그램을 사용 하 여 **앱을 감사** 하 고, 감사 하거나, [AZURE AD Reporting API](../reports-monitoring/concept-reporting-api.md) 의 동일한 정보에 액세스 하 여 즐겨 사용 하는 도구에 통합 하세요.

- **엔터프라이즈 응용 프로그램,** OAuth/openid connect Connect를 사용 하는 앱에 대 한 사용 권한을 사용 하 여 **앱에 대 한 사용 권한을 봅니다** .

- **엔터프라이즈 응용 프로그램, 로그인을** 사용 하 여 **로그인 정보를 가져옵니다** . [AZURE AD REPORTING API](../reports-monitoring/concept-reporting-api.md) 에서 동일한 정보에 액세스 합니다.

- [AZURE AD Power BI 콘텐츠 팩](../reports-monitoring/howto-use-azure-monitor-workbooks.md) 에서 **앱 사용을 시각화** 합니다.

### <a name="exit-criteria"></a>종료 기준

이 단계에서는 다음과 같은 작업을 수행 합니다.

- 사용자에 게 보안 앱 액세스 제공

- 마이그레이션된 앱을 감사 하 고 정보를 얻는 관리

### <a name="do-even-more-with-deployment-plans"></a>배포 계획을 사용 하 여 더 많은 작업 수행

배포 계획은 앱 마이그레이션 시나리오를 비롯 하 여 Azure AD 솔루션의 비즈니스 가치, 계획, 구현 단계 및 관리를 안내 합니다. Azure AD 기능에서 값을 배포 하 고 가져오는 데 필요한 모든 것을 함께 제공 합니다. 배포 가이드에는 Microsoft 권장 모범 사례, 최종 사용자 통신, 계획 가이드, 구현 단계, 테스트 사례 등의 콘텐츠가 포함 됩니다.

다양 한 [배포 계획](../fundamentals/active-directory-deployment-plans.md) 을 사용할 수 있으며,이는 항상 더 많은 것입니다.

### <a name="contact-support"></a>기술 지원 서비스에 문의하십시오.

지원 티켓을 만들거나 추적 하 고 상태를 모니터링 하려면 다음 지원 링크를 방문 하세요.

- **Azure 지원:** [Microsoft 지원](https://azure.microsoft.com/support) 를 호출 하 고 Azure에 대 한 티켓을 열 수 있습니다.

Microsoft의 기업계약에 따라 id 배포 문제입니다.

- **Fasttrack**: EMS (Enterprise Mobility and Security) 또는 Azure AD Premium 라이선스를 구매한 경우 [fasttrack 프로그램](/enterprise-mobility-security/solutions/enterprise-mobility-fasttrack-program) 에서 배포 지원을 받을 수 있습니다.

- **제품 엔지니어링 팀 참여:** 수백만 명의 사용자가 있는 주요 고객 배포에서 작업 하는 경우 Microsoft 계정 팀 또는 클라우드 솔루션 설계자의 지원을 받을 수 있습니다. 프로젝트의 배포 복잡도에 따라 [Azure Id 제품 엔지니어링 팀](https://aad.portal.azure.com/#blade/Microsoft_Azure_Marketplace/MarketplaceOffersBlade/selectedMenuItemId/solutionProviders) 과 직접 작업할 수 있습니다.

- **AZURE AD id 블로그:** [AZURE AD id 블로그](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/bg-p/Identity) 를 구독 하 여 최신 제품 공지 사항, 심층 다이브 및 id 엔지니어링 팀에서 직접 제공 하는 로드맵 정보를 최신 상태로 유지 하세요.
