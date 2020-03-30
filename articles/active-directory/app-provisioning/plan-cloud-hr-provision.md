---
title: Azure Active Directory 사용자 프로비저닝에 클라우드 HR 응용 프로그램 계획
description: 이 문서에서는 Azure Active Directory와 Workday 및 SuccessFactors와 같은 클라우드 HR 시스템을 통합하는 배포 프로세스에 대해 설명합니다. Azure AD를 클라우드 HR 시스템과 통합하면 완전한 ID 수명 주기 관리 시스템이 생성됩니다.
services: active-directory
author: martincoetzer
manager: CelesteDG
tags: azuread
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 11/22/2019
ms.author: martinco
ms.reviewer: arvindha
ms.openlocfilehash: 28abe2dfa5a1a13ba09e20202180cb5e47d94072
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522435"
---
# <a name="plan-cloud-hr-application-to-azure-active-directory-user-provisioning"></a>Azure Active Directory 사용자 프로비저닝에 클라우드 HR 응용 프로그램 계획

지금까지 IT 직원은 직원을 만들고 업데이트하고 삭제하는 수동 방법에 의존해 온 적이 있습니다. CSV 파일 또는 사용자 지정 스크립트를 업로드하여 직원 데이터를 동기화하는 등의 방법을 사용했습니다. 이러한 프로비저닝 프로세스는 오류가 발생하기 쉽고 안전하지 않으며 관리하기 가 어렵습니다.

직원, 공급업체 또는 파견 근로자의 ID 수명 주기를 관리하기 위해 [Azure Active Directory(Azure AD) 사용자 프로비저닝 서비스는](../app-provisioning/user-provisioning.md) 클라우드 기반 HR(HR) 응용 프로그램과의 통합을 제공합니다. 응용 프로그램의 예로는 근무일 또는 성공 요인이 있습니다.

Azure AD는 이 통합을 사용하여 다음과 같은 클라우드 HR 응용 프로그램(앱) 워크플로를 활성화합니다.

- **사용자를 Active 디렉터리로 프로비전합니다.** 클라우드 HR 앱에서 선택한 사용자 집합을 하나 이상의 Active Directory 도메인으로 프로비전합니다.
- **Azure AD에 클라우드 전용 사용자를 프로비전합니다.** Active Directory가 사용되지 않는 시나리오에서는 클라우드 HR 앱에서 Azure AD로 직접 사용자를 프로비전합니다.
- **클라우드 HR 앱에 다시 쓰기:** Azure AD의 전자 메일 주소 및 사용자 이름 속성을 클라우드 HR 앱으로 다시 작성합니다.

> [!NOTE]
> 이 배포 계획은 Azure AD 사용자 프로비저닝을 사용하여 클라우드 HR 앱 워크플로를 배포하는 방법을 보여 줍니다. 소프트웨어에 자동 사용자 프로비저닝을 서비스로 배포하는 방법에 대한 자세한 내용은 [자동 사용자 프로비저닝 배포 계획을](https://aka.ms/deploymentplans/provisioning)참조하십시오.

## <a name="enabled-hr-scenarios"></a>사용 가능한 HR 시나리오

Azure AD 사용자 프로비저닝 서비스를 사용하면 다음 HR 기반 ID 수명 주기 관리 시나리오를 자동화할 수 있습니다.

- **신입 사원 채용:** 새 직원이 클라우드 HR 앱에 추가되면 사용자 계정이 Active Directory 및 Azure AD에 자동으로 만들어지며 클라우드 HR 앱에 전자 메일 주소 및 사용자 이름 속성을 다시 쓸 수 있습니다.
- **직원 속성 및 프로필 업데이트:** 클라우드 HR 앱에서 이름, 제목 또는 관리자와 같은 직원 레코드가 업데이트되면 사용자 계정이 Active Directory 및 Azure AD에서 자동으로 업데이트됩니다.
- **직원 해고:** 직원이 클라우드 HR 앱에서 종료되면 Active Directory 및 Azure AD에서 사용자 계정이 자동으로 비활성화됩니다.
- **직원 재채용:** 직원이 클라우드 HR 앱에서 다시 고용되면 이전 계정을 자동으로 다시 활성화하거나 Active Directory 및 Azure AD로 다시 프로비전할 수 있습니다.

## <a name="who-is-this-integration-best-suited-for"></a>이 통합은 누구를 위해 가장 적합합니까?

Azure AD 사용자 프로비저닝과의 클라우드 HR 앱 통합은 다음과 같은 조직에 이상적입니다.

- 클라우드 HR 사용자 프로비저닝을 위한 미리 빌드된 클라우드 기반 솔루션을 원합니다.
- 클라우드 HR 앱에서 Active Directory 또는 Azure AD로 직접 사용자 프로비전이 필요합니다.
- 클라우드 HR 앱에서 얻은 데이터를 사용하여 사용자를 프로비전해야 합니다.
- 클라우드 HR 앱에서 검색된 변경 정보만을 기반으로 사용자를 하나 이상의 Active Directory 포리스트, 도메인 및 OUs에 동기화하도록 조인, 이동 및 떠나도록 요구합니다.
- 전자 메일의 경우 Office 365를 사용합니다.

## <a name="learn"></a>배우기

사용자 프로비저닝은 지속적인 ID 거버넌스를 위한 토대를 마련합니다. 신뢰할 수 있는 ID 데이터에 의존하는 비즈니스 프로세스의 품질을 향상시킵니다.

### <a name="terms"></a>용어

이 문서에서는 다음 용어를 사용합니다.

- **원본 시스템**: Azure AD에서 프로비전하는 사용자의 리포지토리입니다. 예를 들어 근무일 또는 성공 요소와 같은 클라우드 HR 앱이 있습니다.
- **대상 시스템:** Azure AD가 프로비전하는 사용자의 리포지토리입니다. 예를 들어 Active Directory, Azure AD, Office 365 또는 기타 SaaS 앱이 있습니다.
- **Joiners-Movers-Leavers 프로세스**: 클라우드 HR 앱을 기록 시스템으로 사용하여 신규 채용, 이전 및 해지에 사용되는 용어입니다. 서비스가 대상 시스템에 필요한 특성을 성공적으로 프로비전하면 프로세스가 완료됩니다.

### <a name="key-benefits"></a>주요 이점

HR 기반 IT 프로비저닝의 이 기능은 다음과 같은 중요한 비즈니스 이점을 제공합니다.

- **생산성 향상:** 이제 사용자 계정 및 Office 365 라이선스 할당을 자동화하고 키 그룹에 대한 액세스를 제공할 수 있습니다. 과제를 자동화하면 신입 사원이 작업 도구에 즉시 액세스할 수 있으며 생산성이 향상됩니다.
- **위험 관리:** 클라우드 HR 앱에서 데이터로 들어오는 직원 상태 또는 그룹 구성원 자격에 따라 변경을 자동화하여 보안을 강화할 수 있습니다. 변경 사항을 자동화하면 사용자가 조직을 전환하거나 나갈 때 사용자 ID 및 주요 앱에 대한 액세스가 자동으로 업데이트됩니다.
- **규정 준수 및 거버넌스 해결:** Azure AD는 원본 및 대상 시스템의 앱에서 수행하는 사용자 프로비저닝 요청에 대한 기본 감사 로그를 지원합니다. 감사를 사용하면 단일 화면에서 앱에 액세스할 수 있는 사람을 추적할 수 있습니다.
- **비용 관리:** 자동 프로비저닝은 수동 프로비저닝과 관련된 비효율성과 인적 오류를 방지하여 비용을 절감합니다. 레거시 및 오래된 플랫폼을 사용하여 시간이 지남에 따라 구축된 사용자 프로비저닝 솔루션의 필요성을 줄입니다.

### <a name="licensing"></a>라이선스

Azure AD 사용자 프로비저닝 통합에 클라우드 HR 앱을 구성하려면 유효한 [Azure AD 프리미엄 라이선스와](https://azure.microsoft.com/pricing/details/active-directory/) Workday 또는 SuccessFactors와 같은 클라우드 HR 앱에 대한 라이선스가 필요합니다.

또한 클라우드 HR 앱에서 공급되고 Active Directory 또는 Azure AD로 프로비전되는 모든 사용자에 대해 유효한 Azure AD Premium P1 또는 더 높은 구독 라이선스가 필요합니다. 클라우드 HR 앱에 있는 라이선스수가 부적절하게 발생하면 사용자 프로비저닝 중에 오류가 발생할 수 있습니다.

### <a name="prerequisites"></a>사전 요구 사항

- Azure AD 글로벌 관리자 액세스하여 Azure AD Connect 프로비전 에이전트를 구성합니다.
- 클라우드 HR 앱의 테스트 및 프로덕션 인스턴스입니다.
- 클라우드 HR 앱의 관리자 권한은 시스템 통합 사용자를 만들고 테스트 목적으로 직원 데이터를 테스트하기 위해 변경합니다.
- Active Directory에 사용자 프로비전하려면 .NET 4.7.1+ 런타임을 통해 Windows Server 2012 이상을 실행하는 서버가 [Azure AD Connect 프로비전 에이전트를](https://go.microsoft.com/fwlink/?linkid=847801)호스트해야 합니다.
- Active Directory와 Azure AD 간에 사용자를 동기화하기 위한 [Azure AD 연결입니다.](../hybrid/whatis-azure-ad-connect.md)

### <a name="training-resources"></a>교육 리소스

| **리소스** | **링크 및 설명** |
|:-|:-|
| 비디오 | [활성 Azure 디렉터리에서 사용자 프로비전이란 무엇입니까?](https://youtu.be/_ZjARPpI6NI) |
| | [활성 Azure 디렉터리에서 사용자 프로비저닝을 배포하는 방법](https://youtu.be/pKzyts6kfrw) |
| 자습서 | [Azure AD와 SaaS 앱을 통합하는 방법에 대한 자습서 목록](../saas-apps/tutorial-list.md) |
| | [자습서: 자동 사용자 프로비전을 위한 Workday 구성](../saas-apps/workday-inbound-tutorial.md#frequently-asked-questions-faq) |
| FAQ | [자동화된 사용자 프로비전](../app-provisioning/user-provisioning.md#what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning) |
| | [근무일에서 Azure AD로 프로비저닝](../saas-apps/workday-inbound-tutorial.md#frequently-asked-questions-faq) |

### <a name="solution-architecture"></a>솔루션 아키텍처

다음 예제는 일반적인 하이브리드 환경에 대한 종단 간 사용자 프로비저닝 솔루션 아키텍처에 대해 설명하며 다음을 포함합니다.

- **신뢰할 수 있는 HR 데이터가 클라우드 HR 앱에서 Active Directory로 흐르게 됩니다.** 이 흐름에서 HR 이벤트(Joiners-Movers-Leavers 프로세스)는 클라우드 HR 앱 테넌트에서 시작됩니다. Azure AD 프로비전 서비스 및 Azure AD Connect 프로비전 에이전트는 클라우드 HR 앱 테넌트의 사용자 데이터를 Active Directory로 프로비전합니다. 이벤트에 따라 Active Directory에서 작업을 생성, 업데이트, 활성화 및 사용하지 않도록 설정할 수 있습니다.
- **Azure AD와 동기화하고 온-프레미스 Active Directory에서 클라우드 HR 앱으로 이메일 및 사용자 이름을 다시 작성합니다.** 활성 디렉터리에서 계정을 업데이트하면 Azure AD Connect를 통해 Azure AD와 동기화됩니다. 전자 메일 주소 및 사용자 이름 특성은 클라우드 HR 앱 테넌트에 다시 쓸 수 있습니다.

![워크플로 다이어그램](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img1.png)

#### <a name="description-of-workflow"></a>워크플로에 대한 설명

다음 주요 단계는 다이어그램에 표시됩니다.  

1. **HR 팀은** 클라우드 HR 앱 테넌트에서 트랜잭션을 수행합니다.
2. **Azure AD 프로비저닝 서비스는** 클라우드 HR 앱 테넌트에서 예약된 주기를 실행하고 Active Directory와 동기화하기 위해 처리해야 하는 변경 내용을 식별합니다.
3. **Azure AD 프로비저닝 서비스는** Active Directory 계정 생성, 업데이트, 사용 및 비활성화 작업을 포함하는 요청 페이로드를 사용하여 Azure AD Connect 프로비전 에이전트를 호출합니다.
4. **Azure AD Connect 프로비전 에이전트는** 서비스 계정을 사용하여 Active Directory 계정 데이터를 관리합니다.
5. **Azure AD Connect는** 델타 [동기화를](../hybrid/how-to-connect-sync-whatis.md) 실행하여 Active Directory에서 업데이트를 가져옵니다.
6. **Active Directory** 업데이트는 Azure AD와 동기화됩니다.
7. **Azure AD 프로비저닝 서비스는** Azure AD에서 클라우드 HR 앱 테넌트로 전자 메일 특성 및 사용자 이름을 다시 작성합니다.

## <a name="plan-the-deployment-project"></a>구축 프로젝트 계획

사용자 환경에서 이 배포에 대한 전략을 결정하는 동안 조직의 요구 사항을 고려합니다.

### <a name="engage-the-right-stakeholders"></a>올바른 이해 관계자 참여

기술 프로젝트가 실패하면 일반적으로 영향, 결과 및 책임에 대한 불일치기대로 인해 그렇게 합니다. 이러한 함정을 방지하려면 [올바른 이해 관계자를 참여시키는지 확인하십시오.](https://aka.ms/deploymentplans) 또한 프로젝트의 이해 관계자 역할을 잘 이해해야 합니다. 이해 관계자와 프로젝트 입력 및 책임 도문서화합니다.

기존 HR 비즈니스 프로세스 및 작업자 ID 및 작업 데이터 처리 요구 사항에 대한 입력을 제공할 수 있는 HR 조직의 담당자를 포함합니다.

### <a name="plan-communications"></a>통신 계획

통신은 새로운 서비스의 성공에 매우 중요합니다. 사용자 환경이 변경될 시기와 방법에 대해 사전에 사용자와 통신합니다. 문제가 발생하면 지원을 받는 방법을 알려주십시오.

### <a name="plan-a-pilot"></a>파일럿 계획

HR 비즈니스 프로세스와 ID 워크플로를 클라우드 HR 앱에서 대상 시스템에 통합하려면 솔루션을 프로덕션에 배포하기 전에 상당한 양의 데이터 유효성 검사, 데이터 변환, 데이터 정리 및 종단 간 테스트가 필요합니다.

프로덕션 환경에서 모든 사용자에게 확장하기 전에 [파일럿 환경에서](../fundamentals/active-directory-deployment-plans.md#best-practices-for-a-pilot) 초기 구성을 실행합니다.

## <a name="select-cloud-hr-provisioning-connector-apps"></a>클라우드 HR 프로비저닝 커넥터 앱 선택

클라우드 HR 앱과 Active Directory 간의 Azure AD 프로비저닝 워크플로를 용이하게 하기 위해 Azure AD 앱 갤러리에서 여러 프로비저닝 커넥터 앱을 추가할 수 있습니다.

- **Active Directory 사용자 프로비저닝에 클라우드 HR 앱**: 이 프로비저닝 커넥터 앱은 클라우드 HR 앱에서 단일 Active Directory 도메인으로 사용자 계정 프로비전을 용이하게 합니다. 도메인이 여러 개 있는 경우 프로비전해야 하는 각 Active Directory 도메인에 대해 Azure AD 앱 갤러리에서 이 앱의 하나의 인스턴스를 추가할 수 있습니다.
- **Azure AD 사용자 프로비저닝에 대한 클라우드 HR 앱:** Azure AD Connect는 Active Directory 사용자를 Azure AD로 동기화하는 데 사용해야 하는 도구이지만 이 프로비저닝 커넥터 앱은 클라우드 HR 앱에서 단일 Azure AD 테넌트로 클라우드 전용 사용자를 프로비저닝하는 데 사용할 수 있습니다.
- **클라우드 HR 앱 쓰기 백**: 이 프로비저닝 커넥터 앱은 Azure AD에서 클라우드 HR 앱으로 사용자의 전자 메일 주소를 쉽게 다시 쓸 수 있도록 합니다.

예를 들어 다음 이미지에는 Azure AD 앱 갤러리에서 사용할 수 있는 Workday 커넥터 앱이 나열됩니다.

![Azure Active Directory 포털 앱 갤러리](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img2.png)

### <a name="decision-flow-chart"></a>의사 결정 흐름 차트

다음 의사 결정 순서도를 사용하여 시나리오와 관련된 클라우드 HR 프로비저닝 앱을 식별합니다.

![의사 결정 흐름 차트](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img3.png)

## <a name="design-the-azure-ad-connect-provisioning-agent-deployment-topology"></a>Azure AD Connect 프로비저닝 에이전트 배포 토폴로지 설계

클라우드 HR 앱과 Active Directory 간의 프로비저닝 통합에는 다음 네 가지 구성 요소가 필요합니다.

- 클라우드 HR 앱 테넌트
- 커넥터 앱 프로비저닝
- Azure AD 연결 프로비전 에이전트
- Active Directory 도메인

Azure AD Connect 프로비저닝 에이전트 배포 토폴로지에서는 통합하려는 클라우드 HR 앱 테넌트 및 Active Directory 자식 도메인 의 수에 따라 달라집니다. Active Directory 도메인이 여러 개인 경우 Active Directory 도메인이 연속 도메인인지 또는 [분리되는지에](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/disjoint-namespace)따라 달라집니다.

결정에 따라 배포 시나리오 중 하나를 선택합니다.

- 단일 클라우드 HR 앱 테넌트 -> 신뢰할 수 있는 포리스트에서 단일 또는 다중 Active Directory 자식 도메인을 대상으로 합니다.
- 단일 클라우드 HR 앱 테넌트 -> 분리된 Active Directory 포리스트에서 여러 자식 도메인을 대상으로 합니다.

### <a name="single-cloud-hr-app-tenant---target-single-or-multiple-active-directory-child-domains-in-a-trusted-forest"></a>단일 클라우드 HR 앱 테넌트 -> 신뢰할 수 있는 포리스트에서 단일 또는 다중 Active Directory 자식 도메인을 대상으로 합니다.

다음 프로덕션 구성을 권장합니다.

|요구 사항|권장|
|:-|:-|
|배포할 Azure AD Connect 프로비저닝 에이전트 수|2개(고가용성 및 장애 조치)
|구성할 커넥터 앱 프로비저닝 수|하위 도메인당 하나의 앱|
|Azure AD Connect 프로비전 에이전트용 서버 호스트|Windows 2012 R2+ 지리적으로 배치된 Active Directory 도메인 컨트롤러에 가시선</br>Azure AD Connect 서비스와 공존할 수 있습니다.|

![온-프레미스 에이전트로의 흐름](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img4.png)

### <a name="single-cloud-hr-app-tenant---target-multiple-child-domains-in-a-disjoint-active-directory-forest"></a>단일 클라우드 HR 앱 테넌트 -> 분리된 Active Directory 포리스트에서 여러 자식 도메인을 대상으로 합니다.

이 시나리오에는 클라우드 HR 앱에서 분리된 Active Directory 포리스트의 도메인으로 사용자를 프로비전하는 작업이 포함됩니다.

다음 프로덕션 구성을 권장합니다.

|요구 사항|권장|
|:-|:-|
|온-프레미스를 배포할 Azure AD Connect 프로비저닝 에이전트 수|분리된 Active Directory 포리스트당 2개|
|구성할 커넥터 앱 프로비저닝 수|하위 도메인당 하나의 앱|
|Azure AD Connect 프로비전 에이전트용 서버 호스트|Windows 2012 R2+ 지리적으로 배치된 Active Directory 도메인 컨트롤러에 가시선</br>Azure AD Connect 서비스와 공존할 수 있습니다.|

![단일 클라우드 HR 앱 테넌트 분리 활성 디렉터리 포리스트](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img5.png)

### <a name="azure-ad-connect-provisioning-agent-requirements"></a>Azure AD Connect 프로비전 에이전트 요구 사항

Active Directory 사용자 프로비전 솔루션에 대한 클라우드 HR 앱에서는 Windows 2012 R2 이상을 실행하는 서버에 하나 이상의 Azure AD Connect 프로비전 에이전트를 배포해야 합니다. 서버에는 최소 4GB RAM과 .NET 4.7.1 이상의 런타임이 있어야 합니다. 호스트 서버가 대상 Active Directory 도메인에 대한 네트워크 액세스 권한을 가지고 있는지 확인합니다.

온-프레미스 환경을 준비하기 위해 Azure AD Connect 프로비저닝 에이전트 구성 마법사는 에이전트를 Azure AD 테넌트에 등록하고, [포트를 열고,](../manage-apps/application-proxy-add-on-premises-application.md#open-ports) [URL에 대한 액세스를 허용하며,](../manage-apps/application-proxy-add-on-premises-application.md#allow-access-to-urls) [아웃바운드 HTTPS 프록시 구성을](../saas-apps/workday-inbound-tutorial.md#how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication)지원합니다.

프로비저닝 에이전트는 서비스 계정을 사용하여 Active Directory 도메인과 통신합니다. 에이전트를 설치하기 전에 Active Directory 사용자 및 컴퓨터에서 다음 요구 사항을 충족하는 서비스 계정을 만듭니다.

- 만료되지 않는 암호
- 사용자 계정을 읽고, 만들고, 삭제하고, 관리할 수 있는 위임된 제어 권한

프로비저닝 요청을 처리해야 하는 도메인 컨트롤러를 선택할 수 있습니다. 지리적으로 분산된 도메인 컨트롤러가 여러 개 있는 경우 원하는 도메인 컨트롤러와 동일한 사이트에 프로비저닝 에이전트를 설치합니다. 이 포지셔닝은 엔드 투 엔드 솔루션의 신뢰성과 성능을 향상시킵니다.

고가용성의 경우 두 개 이상의 Azure AD Connect 프로비전 에이전트를 배포할 수 있습니다. 에이전트를 등록하여 동일한 온-프레미스 Active Directory 도메인 집합을 처리합니다.

## <a name="plan-scoping-filters-and-attribute-mapping"></a>범위 지정 필터 및 특성 매핑 계획

클라우드 HR 앱에서 Active Directory 또는 Azure AD로 프로비전을 사용하도록 설정하면 Azure 포털은 특성 매핑을 통해 특성 값을 제어합니다.

### <a name="define-scoping-filters"></a>범위 지정 필터 정의

[범위 지정 필터를](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) 사용하여 클라우드 HR 앱에서 Active Directory 또는 Azure AD로 프로비전해야 하는 사용자를 결정하는 특성 기반 규칙을 정의합니다.

Joiners 프로세스를 시작할 때 다음 요구 사항을 수집합니다.

- 클라우드 HR 앱이 직원과 파견 근로자 모두를 데려오는 데 사용되나요?
- Azure AD 사용자 프로비저닝에 클라우드 HR 앱을 사용하여 직원과 파견 작업자를 모두 관리할 계획입니까?
- 클라우드 HR 앱 사용자의 하위 집합에 대해서만 Azure AD 사용자 프로비저닝에 클라우드 HR 앱을 배포할 계획입니까? 예를 들어 직원만 있을 수 있습니다.

요구 사항에 따라 특성 매핑을 구성할 때 **소스 개체 범위** 필드를 설정하여 클라우드 HR 앱의 사용자 집합을 Active Directory에 프로비전할 범위에 있어야 합니다. 자세한 내용은 일반적으로 사용되는 범위 지정 필터에 대한 클라우드 HR 앱 자습서를 참조하십시오.

### <a name="determine-matching-attributes"></a>일치하는 특성 결정

프로비저닝을 사용하면 원본 시스템과 대상 시스템 간에 기존 계정을 일치시키는 기능을 얻을 수 있습니다. 클라우드 HR 앱을 Azure AD 프로비저닝 서비스와 통합할 때 [특성 매핑을 구성하여](../app-provisioning/configure-automatic-user-provisioning-portal.md#mappings) 클라우드 HR 앱에서 Active Directory 또는 Azure AD로 어떤 사용자 데이터가 흐를지 결정하도록 할 수 있습니다.

Joiners 프로세스를 시작할 때 다음 요구 사항을 수집합니다.

- 각 사용자를 식별하는 데 사용되는 이 클라우드 HR 앱의 고유 ID는 무엇입니까?
- ID 수명 주기 관점에서 재고용을 어떻게 처리합니까? 재고용은 이전 직원 의 아이디를 유지합니까?
- 미래의 고용을 처리하고 사전에 Active Directory 계정을 만들수 있습니까?
- ID 수명 주기의 관점에서 직원을 우발적 인 작업자 변환 또는 기타 처리방법은 무엇입니까?
- 전환된 사용자가 이전 Active Directory 계정을 유지합니까 아니면 새 계정을 얻습니까?

요구 사항에 따라 Azure AD는 속성 매핑에 대해 일정한 값을 제공하거나 [식을 작성하여](../app-provisioning/functions-for-customizing-application-data.md)직접 특성-속성 매핑을 지원합니다. 이러한 유연성을 통해 대상 앱 속성에 채워진 내용을 궁극적으로 제어할 수 있습니다. Microsoft 그래프 [API](../app-provisioning/export-import-provisioning-configuration.md) 및 그래프 탐색기를 사용하여 사용자 프로비저닝 특성 매핑 및 스키마를 JSON 파일로 내보내고 Azure AD로 다시 가져올 수 있습니다.

기본적으로 고유한 직원 ID를 나타내는 클라우드 HR 앱의 특성은 *Active Directory의 고유 특성에 매핑된* 일치 특성으로 사용됩니다. 예를 들어 Workday 앱 시나리오에서 **Workday** **WorkerID** 특성은 Active Directory **employeeID** 특성에 매핑됩니다.

여러 일치 특성을 설정하고 일치 우선 순위를 할당할 수 있습니다. 일치 우선 순위에 따라 평가됩니다. 일치 항목이 발견되는 즉시 더 이상 일치 특성을 평가하지 않습니다.

기존 특성 매핑을 변경하거나 삭제하는 등 기본 특성 매핑을 [사용자 지정할](../app-provisioning/customize-application-attributes.md#understanding-attribute-mapping-types)수도 있습니다. 비즈니스 요구 사항에 따라 새 특성 매핑을 만들 수도 있습니다. 자세한 내용은 매핑할 사용자 지정 특성 목록은 클라우드 HR 앱 자습서(예: [Workday)를](../saas-apps/workday-inbound-tutorial.md#planning-workday-to-active-directory-user-attribute-mapping-and-transformations)참조하십시오.

### <a name="determine-user-account-status"></a>사용자 계정 상태 확인

기본적으로 프로비저닝 커넥터 앱은 HR 사용자 프로필 상태를 Active Directory 또는 Azure AD의 사용자 계정 상태에 매핑하여 사용자 계정을 활성화 또는 비활성화할지 여부를 결정합니다.

Joiners-Leavers 프로세스를 시작하면 다음 요구 사항을 수집합니다.

| Process | 요구 사항 |
| - | - |
| **조이너** | ID 수명 주기 관점에서 재고용을 어떻게 처리합니까? 재고용은 이전 직원 의 아이디를 유지합니까? |
| | 미래의 고용을 처리하고 사전에 Active Directory 계정을 만들수 있습니까? 이러한 계정이 사용 설정되거나 비활성화된 상태로 만들어졌습니까? |
| | ID 수명 주기의 관점에서 직원을 우발적 인 작업자 변환 또는 기타 처리방법은 무엇입니까? |
| | 변환된 사용자가 이전 Active Directory 계정을 유지합니까, 아니면 새 계정을 얻습니까? |
| **이탈자** | Active Directory의 직원 및 파견 근로자에 대해 해고가 다르게 처리됩니까? |
| | 사용자 종료 처리를 위해 어떤 유효 날짜가 고려됩니까? |
| | 직원 및 파견 작업자 전환은 기존 Active Directory 계정에 어떤 영향을 미칩니까? |
| | Active Directory에서 Rescind 작업을 어떻게 처리합니까? 향후 일자 고용이 Joiner 프로세스의 일부로 Active Directory에서 생성되는 경우 철회 작업을 처리해야 합니다. |

요구 사항에 따라 [Azure AD 식을](../app-provisioning/functions-for-customizing-application-data.md) 사용하여 매핑 논리를 사용자 지정하여 데이터 포인트 조합에 따라 Active Directory 계정을 사용하거나 사용하지 않도록 설정할 수 있습니다.

### <a name="map-cloud-hr-app-to-active-directory-user-attributes"></a>클라우드 HR 앱을 Active Directory 사용자 속성에 매핑

각 클라우드 HR 앱은 기본 클라우드 HR 앱과 함께 Active Directory 매핑으로 제공됩니다.

Joiners-Movers-Leavers 프로세스를 시작하면 다음 요구 사항을 수집합니다.

| Process | 요구 사항 |
| - | - |
| **조이너** | Active Directory 계정 생성 프로세스 매뉴얼, 자동화 또는 부분적으로 자동화되어 있습니까? |
| | 클라우드 HR 앱에서 Active Directory로 사용자 지정 특성을 전파할 계획입니까? |
| **발동기** | 클라우드 HR 앱에서 이동 작업이 수행될 때마다 어떤 속성을 처리하시겠습니까? |
| | 사용자 업데이트 시 특정 특성 유효성 검사를 수행합니까? 그렇다면 세부 정보를 제공합니다. |
| **이탈자** | Active Directory의 직원 및 파견 근로자에 대해 해고가 다르게 처리됩니까? |
| | 사용자 종료 처리를 위해 어떤 유효 날짜가 고려됩니까? |
| | 직원 및 파견 작업자 전환은 기존 Active Directory 계정에 어떤 영향을 미합니까? |

요구 사항에 따라 통합 목표에 맞게 매핑을 수정할 수 있습니다. 자세한 내용은 매핑할 사용자 지정 특성 목록은 특정 클라우드 HR 앱 자습서(예: [Workday)를](../saas-apps/workday-inbound-tutorial.md#planning-workday-to-active-directory-user-attribute-mapping-and-transformations)참조하십시오.

### <a name="generate-a-unique-attribute-value"></a>고유한 특성 값 생성

Joiners 프로세스를 시작할 때 CN, samAccountName 및 고유한 제약 조건이 있는 UPN과 같은 특성을 설정할 때 고유한 특성 값을 생성해야 할 수 있습니다.

Azure AD 함수 [SelectUniqueValues는](../app-provisioning/functions-for-customizing-application-data.md#selectuniquevalue) 각 규칙을 평가한 다음 대상 시스템에서 고유성에 대해 생성된 값을 확인합니다. 예를 [들어, 사용자에 대한 고유 값 생성을 참조하십시오UPN(사용자이름) 특성](../app-provisioning/functions-for-customizing-application-data.md#generate-unique-value-for-userprincipalname-upn-attribute).

> [!NOTE]
> 이 함수는 현재 Workday에서 Active Directory 사용자 프로비저닝에만 지원됩니다. 다른 프로비저닝 앱과 함께 사용할 수 없습니다.

### <a name="configure-active-directory-ou-container-assignment"></a>Active Directory OU 컨테이너 할당 구성

Active Directory 사용자 계정을 비즈니스 단위, 위치 및 부서를 기반으로 컨테이너에 배치하는 것이 일반적입니다. 이동 프로세스를 시작하고 감독 조직이 변경되는 경우 Active Directory에서 사용자를 한 OU에서 다른 OU로 이동해야 할 수 있습니다.

[Switch()](../app-provisioning/functions-for-customizing-application-data.md#switch) 함수를 사용하여 OU 할당에 대한 비즈니스 논리를 구성하고 Active Directory 특성 **parentDistinguishedName에**매핑합니다.

예를 들어 HR 특성 **지자체를**기반으로 OU에서 사용자를 만들려면 다음 식을 사용할 수 있습니다.

`
Switch([Municipality], "OU=Default,OU=Users,DC=contoso,DC=com", "Dallas", "OU=Dallas,OU=Users,DC=contoso,DC=com", "Austin", "OU=Austin,OU=Users,DC=contoso,DC=com", "Seattle", "OU=Seattle,OU=Users,DC=contoso,DC=com", "London", "OU=London,OU=Users,DC=contoso,DC=com")
`

이 식을 사용하면 시정촌 값이 댈러스, 오스틴, 시애틀 또는 런던인 경우 해당 OU에 사용자 계정이 만들어집니다. 일치하는 항목이 없는 경우 기본 OU에서 계정이 만들어집니다.

## <a name="plan-for-password-delivery-of-new-user-accounts"></a>새 사용자 계정의 암호 배달 계획

Joiners 프로세스를 시작할 때 새 사용자 계정의 임시 암호를 설정하고 제공해야 합니다. Azure AD 사용자 프로비저닝에 클라우드 HR을 사용하면 첫날 사용자에 대한 Azure AD 셀프 서비스 암호 재설정(SSPR) 기능을 롤아웃할 수 있습니다. [self-service password reset](../authentication/quickstart-sspr.md)

SSPR은 IT 관리자가 사용자가 암호를 재설정하거나 계정 잠금을 해제할 수 있도록 하는 간단한 수단입니다. 클라우드 HR 앱에서 Active Directory로 **휴대폰 번호** 특성을 프로비전하고 Azure AD와 동기화할 수 있습니다. 휴대폰 **번호** 특성이 Azure AD에 있으면 사용자 계정에 대해 SSPR을 사용하도록 설정할 수 있습니다. 그런 다음 첫 날에 새 사용자는 등록되고 확인된 휴대폰 번호를 사용하여 인증을 사용할 수 있습니다.

## <a name="plan-for-initial-cycle"></a>초기 주기 계획

Azure AD 프로비저닝 서비스가 처음으로 실행되면 클라우드 HR 앱에 대한 [초기 주기를](../app-provisioning/how-provisioning-works.md#initial-cycle) 수행하여 클라우드 HR 앱의 모든 사용자 개체의 스냅숏을 만듭니다. 초기 주기에 걸리는 시간은 원본 시스템에 있는 사용자 수에 따라 직접 달라집니다. 사용자가 100,000명 이상인 일부 클라우드 HR 앱 테넌자의 초기 주기는 시간이 오래 걸릴 수 있습니다.

**대규모 클라우드 HR 앱 테넌션(>30,000명의 사용자)의 경우** 점진적 단계에서 초기 주기를 실행합니다. 다른 사용자 프로비저닝 시나리오에 대해 Active Directory에 올바른 특성이 설정되어 있는지 확인한 후에만 증분 업데이트를 시작합니다. 여기에 순서를 따르십시오.

1. [범위 지정 필터를](#plan-scoping-filters-and-attribute-mapping)설정하여 제한된 사용자 집합에 대해서만 초기 주기를 실행합니다.
2. Active Directory 계정 프로비저닝 및 첫 번째 실행에 대해 선택한 사용자에 대해 설정된 특성 값을 확인합니다. 결과가 기대에 부합하는 경우 범위 지정 필터를 확장하여 더 많은 사용자를 점진적으로 포함하고 두 번째 실행의 결과를 확인합니다.

테스트 사용자에 대한 초기 주기의 결과에 만족하면 [증분 업데이트를](../app-provisioning/how-provisioning-works.md#incremental-cycles)시작합니다.

## <a name="plan-testing-and-security"></a>테스트 및 보안 계획

초기 파일럿부터 사용자 프로비저닝 활성화까지 배포의 각 단계에서 결과가 예상대로 테스트되고 프로비저닝 주기를 감사해야 합니다.

### <a name="plan-testing"></a>계획 테스트

Azure AD 사용자 프로비저닝에 클라우드 HR 앱을 구성한 후 테스트 사례를 실행하여 이 솔루션이 조직의 요구 사항을 충족하는지 확인합니다.

|시나리오|예상 결과|
|:-|:-|
|클라우드 HR 앱에서 신입사원이 채용됩니다.| - 사용자 계정은 Active Directory에 프로비전됩니다.</br>- 사용자는 Active Directory-도메인 앱에 로그인하여 원하는 작업을 수행할 수 있습니다.</br>- Azure AD Connect 동기화가 구성된 경우 사용자 계정도 Azure AD에서 만들어집니다.
|사용자는 클라우드 HR 앱에서 종료됩니다.|- Active Directory에서 사용자 계정이 비활성화됩니다.</br>- 사용자는 Active Directory로 보호되는 엔터프라이즈 앱에 로그인할 수 없습니다.
|사용자 감독 조직은 클라우드 HR 앱에서 업데이트됩니다.|특성 매핑에 따라 사용자 계정은 Active Directory에서 한 OU에서 다른 OU로 이동합니다.|
|HR은 클라우드 HR 앱에서 사용자의 관리자를 업데이트합니다.|Active Directory의 관리자 필드가 새 관리자의 이름을 반영하도록 업데이트됩니다.|
|HR은 직원을 새로운 역할로 재채용합니다.|동작은 클라우드 HR 앱이 직원 아이디를 생성하도록 구성되는 방식에 따라 달라집니다.</br>- 이전 직원 ID를 재고용에 재사용하는 경우 커넥터를 사용하여 사용자의 기존 Active Directory 계정을 사용할 수 있습니다.</br>- 재고용이 새 직원 ID를 받으면 커넥터는 사용자에 대한 새 Active Directory 계정을 만듭니다.|
|HR은 직원을 계약 근로자로 전환하거나 그 반대로 변환합니다.|새 페르소나에 대해 새 Active Directory 계정이 만들어지고 이전 계정은 전환 발효일에 비활성화됩니다.|

이전 결과를 사용하여 기존 일정에 따라 자동 사용자 프로비저닝 구현을 프로덕션으로 전환하는 방법을 결정합니다.

> [!TIP]
> 프로덕션 데이터로 테스트 환경을 새로 고칠 때 데이터 감소 및 데이터 스크러빙과 같은 기술을 사용하여 중요한 개인 데이터를 제거하거나 마스킹하여 개인 정보 보호 및 보안 표준을 준수합니다. 

### <a name="plan-security"></a>보안 계획

새 서비스 배포의 일부로 보안 검토가 필요한 것이 일반적입니다. 보안 검토가 필요하거나 수행되지 않은 경우 서비스로 ID에 대한 개요를 제공하는 많은 Azure AD [백서를](https://www.microsoft.com/download/details.aspx?id=36391) 참조하십시오.

### <a name="plan-rollback"></a>롤백 계획

클라우드 HR 사용자 프로비저닝 구현은 프로덕션 환경에서 원하는 대로 작동하지 않을 수 있습니다. 이 경우 다음 롤백 단계를 통해 이전에 알려진 양호 상태로 되돌릴 수 있습니다.

1. [프로비저닝 요약 보고서](../app-provisioning/check-status-user-account-provisioning.md#getting-provisioning-reports-from-the-azure-portal) 및 [프로비저닝 로그를](../app-provisioning/check-status-user-account-provisioning.md#provisioning-logs-preview) 검토하여 영향을 받는 사용자 또는 그룹에서 수행된 잘못된 작업을 확인합니다. 프로비저닝 요약 보고서 및 로그에 대한 자세한 내용은 [클라우드 HR 앱 사용자 프로비저닝 관리를](#manage-your-configuration)참조하십시오.
2. 영향을 받는 사용자 또는 그룹의 마지막으로 알려진 된 상태는 프로비저닝 감사 로그를 통해 또는 대상 시스템(Azure AD 또는 Active Directory)을 검토하여 확인할 수 있습니다.
3. 앱 소유자와 협력하여 마지막으로 알려진 정상 상태 값을 사용하여 앱에서 직접 영향을 받는 사용자 또는 그룹을 업데이트합니다.

## <a name="deploy-the-cloud-hr-app"></a>클라우드 HR 앱 배포

솔루션 요구 사항에 맞는 클라우드 HR 앱을 선택합니다.

**근무일**: Workday에서 활성 디렉터리 및 Azure AD로 작업자 프로필을 가져오려면 [자습서: 자동 사용자 프로비저닝을 위한 작업일 구성](../saas-apps/workday-inbound-tutorial.md#planning-your-deployment)을 참조하십시오. 선택적으로 전자 메일 주소와 사용자 이름을 Workday에 다시 쓸 수 있습니다.

## <a name="manage-your-configuration"></a>구성 관리

Azure AD는 감사 로그 및 보고서를 통해 조직의 사용자 프로비저닝 사용 및 운영 상태와에 대한 추가 통찰력을 제공할 수 있습니다.

### <a name="gain-insights-from-reports-and-logs"></a>보고서 및 로그에서 인사이트 확보

[성공적인 초기 주기](../app-provisioning/how-provisioning-works.md#initial-cycle)후 Azure AD 프로비저닝 서비스는 다음 이벤트 중 하나가 발생할 때까지 각 앱에 특정한 자습서에 정의된 간격으로 백투백 증분 업데이트를 무기한 계속 실행합니다.

- 서비스가 수동으로 중지됩니다. [Azure 포털](https://portal.azure.com/) 또는 적절한 Microsoft [그래프 API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview) 명령을 사용하여 새 초기 주기가 트리거됩니다.
- 특성 매핑 또는 범위 지정 필터의 변경으로 인해 새 초기 주기가 트리거됩니다.
- 프로비저닝 프로세스는 오류율이 높기 때문에 격리됩니다. 4주 이상 격리 된 상태로 유지되며, 이 때 자동으로 비활성화됩니다.

프로비저닝 서비스에서 수행하는 이러한 이벤트 및 기타 모든 활동을 검토하려면 [로그를 검토하고 프로비저닝 활동에 대한 보고서를 받는 방법을 알아봅니다.](../app-provisioning/check-status-user-account-provisioning.md)

#### <a name="azure-monitor-logs"></a>Azure Monitor 로그

프로비저닝 서비스에서 수행하는 모든 활동은 Azure AD 감사 로그에 기록됩니다. 추가 분석을 위해 Azure AD 감사 로그를 Azure Monitor 로그로 라우팅할 수 있습니다. Azure Monitor 로그(로그 분석 작업 영역이라고도 함)를 사용하면 데이터를 쿼리하여 이벤트를 찾고 추세를 분석하며 다양한 데이터 원본간에 상관 관계를 수행할 수 있습니다. 이 [비디오를](https://youtu.be/MP5IaCTwkQg) 시청하여 실제 사용자 시나리오에서 Azure AD 로그에 Azure 모니터 로그를 사용할 때의 이점을 알아봅니다.

Azure [AD 활동 로그에 대한 로그 분석 보기를](../reports-monitoring/howto-install-use-log-analytics-views.md) 설치하여 사용자 환경의 이벤트 프로비저닝과 관련된 [미리 빌드된 보고서에](https://github.com/AzureAD/Deployment-Plans/tree/master/Log%20Analytics%20Views) 액세스할 수 있습니다.

자세한 내용은 [Azure 모니터 로그를 사용하여 Azure AD 활동 로그를 분석하는 방법을 참조하세요.](../reports-monitoring/howto-analyze-activity-logs-log-analytics.md)

### <a name="manage-personal-data"></a>개인 데이터 관리

Windows 서버에 설치된 Azure AD Connect 프로비저닝 에이전트는 클라우드 HR 앱에 따라 Active Directory 특성 매핑에 따라 개인 데이터를 포함할 수 있는 Windows 이벤트 로그에 로그를 만듭니다. 사용자 개인 정보 보호 의무를 준수하려면 Windows 예약 작업을 설정하여 이벤트 로그를 지우고 48시간 이내로 데이터가 유지되지 않도록 합니다.

Azure AD 프로비저닝 서비스는 서비스가 30일을 초과하는 데이터를 저장, 처리 또는 보관하지 않기 때문에 보고서를 생성하거나 분석을 수행하거나 30일을 초과하는 정보를 제공하지 않습니다.

### <a name="troubleshoot"></a>문제 해결

프로비저닝 중에 발생할 수 있는 문제를 해결하려면 다음 문서를 참조하십시오.

- [Azure AD 갤러리 애플리케이션에 대해 사용자 프로비전 구성 문제](application-provisioning-config-problem.md)
- [온-프레미스 Active Directory에서 응용 프로그램에 프로비전하기 위해 Azure AD에 특성 동기화](user-provisioning-sync-attributes-for-mapping.md)
- [Azure AD 갤러리 애플리케이션에 대한 사용자 프로비저닝이 오래 걸림](application-provisioning-when-will-provisioning-finish.md)
- [사용자가 Azure Active Directory 갤러리 애플리케이션에 프로비전을 구성하는 동안 관리자 자격 증명을 저장하는 문제](application-provisioning-config-problem-storage-limit.md)
- [사용자가 Azure AD 갤러리 애플리케이션에 프로비전되지 않음](application-provisioning-config-problem-no-users-provisioned.md)
- [Azure AD 갤러리 애플리케이션에 잘못된 사용자 집합이 프로비전됨](application-provisioning-config-problem-wrong-users-provisioned.md)
- [에이전트 문제 해결을 위한 Windows 이벤트 뷰어 설정](../saas-apps/workday-inbound-tutorial.md#setting-up-windows-event-viewer-for-agent-troubleshooting)
- [서비스 문제 해결을 위한 Azure Portal 감사 로그 설정](../saas-apps/workday-inbound-tutorial.md#setting-up-azure-portal-audit-logs-for-service-troubleshooting)
- [AD 사용자 계정 생성 작업에 대한 로그 이해](../saas-apps/workday-inbound-tutorial.md#understanding-logs-for-ad-user-account-create-operations)
- [관리자 업데이트 작업에 대한 로그 이해](../saas-apps/workday-inbound-tutorial.md#understanding-logs-for-manager-update-operations)
- [일반적으로 발생하는 오류 해결](../saas-apps/workday-inbound-tutorial.md#resolving-commonly-encountered-errors)

### <a name="next-steps"></a>다음 단계

- [특성 매핑에 대한 표현식 작성](functions-for-customizing-application-data.md)
- [Azure AD 동기화 API 개요](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)
- [범위를 벗어난 사용자 계정의 삭제 건너뛰기](skip-out-of-scope-deletions.md)
- [Azure AD 연결 프로비전 에이전트: 버전 릴리스 기록](provisioning-agent-release-version-history.md)
