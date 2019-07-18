---
title: Microsoft Azure에서 보안 응용 프로그램 배포
description: 이 문서에서는 웹 응용 프로그램 프로젝트의 릴리스 및 대응 단계 고려해 야 할 모범 사례를 설명 합니다.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/12/2019
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: e8249113ee65c28414c79f00c53d11596673434b
ms.sourcegitcommit: 22c97298aa0e8bd848ff949f2886c8ad538c1473
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/14/2019
ms.locfileid: "67144452"
---
# <a name="deploy-secure-applications-on-azure"></a>Azure에서 보안 응용 프로그램 배포
이 문서에서는 보안 작업 및 클라우드 용 응용 프로그램을 배포할 때 고려해 야 할 컨트롤 소개 합니다. 보안 질문 및 Microsoft의 릴리스 및 대응 단계 고려해 야 할 개념 [수명 주기 SDL (Security Development)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) 나와 있습니다. 활동 및 보다 안전한 응용 프로그램을 배포 하는 데 사용할 수 있는 Azure 서비스를 정의 하는 데 목표가입니다.

SDL 단계는이 문서에 적용 됩니다.

- 해제
- response

## <a name="release"></a>해제
릴리스 단계 포커스를 공개 릴리스에 대 한 프로젝트를 준비 됩니다.
여기에 효과적으로 릴리스 이후 서비스 작업을 수행 하 고 나중에 발생할 수 있는 보안 취약성을 해결 하는 방법을 계획 합니다.

### <a name="check-your-applications-performance-before-you-launch"></a>시작 하기 전에 응용 프로그램의 성능을 확인합니다

응용 프로그램을 시작 하거나 프로덕션에 업데이트를 배포 하기 전에 응용 프로그램의 성능을 검사 합니다. 클라우드 기반 실행 [부하 테스트](https://www.visualstudio.com/docs/test/performance-testing/getting-started/getting-started-with-performance-testing) 배포 품질을 향상 시킬 응용 프로그램에서 성능 문제를 찾으려면 Visual Studio를 사용 하 여 응용 프로그램은 최신 또는 사용 가능한 항상 및 응용 프로그램 트래픽을 처리할 수 있도록 해야 합니다 프로그램 출시 합니다.

### <a name="install-a-web-application-firewall"></a>웹 애플리케이션 방화벽 설치

웹 애플리케이션의 널리 알려진 취약점을 악용하는 악의적인 공격이 점점 많아지고 있습니다. 이러한 공격은 SQL 삽입 공격 및 사이트 간 스크립팅 공격입니다. 응용 프로그램 코드에서 이러한 공격을 방지 하는 것은 어려울 수 있습니다. 패치 적용 및 모니터링 응용 프로그램 토폴로지의 여러 레이어에서 엄격한 유지 관리에 필요할 수 있습니다. 중앙 집중식된 WAF는 보안 관리를 간단 하 게 하는 데 도움이 됩니다. 또한 WAF 솔루션은 각 개별 웹 응용 프로그램을 보호 하는 대신 중앙 위치에서 알려진된 취약점을 패치를 적용 하 여 보안 위협에 대응할 수 있습니다.

합니다 [Azure Application Gateway WAF](https://docs.microsoft.com/azure/application-gateway/waf-overview) 일반적인 악용 및 취약점 으로부터 웹 응용 프로그램의 중앙 집중화 된 보호를 제공 합니다. WAF 규칙에 기반 합니다 [OWASP 핵심 규칙 집합](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.0 또는 2.2.9 합니다.

### <a name="create-an-incident-response-plan"></a>인시던트 응답 계획 만들기

인시던트 응답 계획을 준비 하는 것은 시간이 지남에 따라 갖출 수 있는 새로운 위협을 해결 하는 데 중요 합니다. 인시던트 응답 계획을 준비 하 고 적절 한 보안 비상 연락처를 식별 하 고 보안 서비스 사용이 허가 된 타사 코드 및 조직에 있는 다른 그룹에서 상속 되는 코드에 대 한 계획에 포함 되어 있습니다.

### <a name="conduct-a-final-security-review"></a>최종 보안 검토를 수행 합니다.

의도적으로 수행 된 모든 보안 활동을 검토 소프트웨어 릴리스 또는 응용 프로그램에 대 한 준비 상태를 확인 하는 데 도움이 됩니다. 일반적으로 (FSR) 최종 보안 검토는 위협 모델, 도구 출력 및 품질 게이트 및 요구 사항 단계에서 정의 된 버그 막대에 대 한 성능 검사 포함 되어 있습니다.

### <a name="certify-release-and-archive"></a>릴리스 및 보관 인증

릴리스를 사용 하면 보안 및 개인 정보 요구 사항을 충족 하는지 확인 하기 전에 소프트웨어를 인증 합니다. 모든 관련 데이터를 보관 하는 것은 릴리스 후 서비스 작업을 수행 하는 데 필수적입니다. 보관도 사용 하면 낮은 장기 비용을 지속적으로 소프트웨어 엔지니어링 합니다.

## <a name="response"></a>response
응답 릴리스 이후 단계 수 고 새로운 소프트웨어 위협 요소 및 취약성에 대 한 모든 보고서에 적절 하 게 응답할 수 개발 팀에 집중 합니다.

### <a name="execute-the-incident-response-plan"></a>인시던트 응답 계획을 실행 합니다.

릴리스 단계에서 운용 하 고 인시던트 응답 계획을 구현할 수 하는 것이 등장 하는 소프트웨어 보안 또는 개인 정보 취약점 으로부터 고객을 보호 하도록 지 원하는 데 중요 합니다.

### <a name="monitor-application-performance"></a>애플리케이션 성능 모니터링

잠재적으로 배포 된 후 응용 프로그램의 지속적인 모니터링 보안 취약점 뿐만 아니라 성능 문제를 검색 하는 데 도움이 됩니다.
응용 프로그램 모니터링에 도움이 되는 azure 서비스는 같습니다.

  - Azure Application Insights
  - Azure Security Center

#### <a name="application-insights"></a>Application Insights

[Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview)는 여러 플랫폼의 웹 개발자를 위한 확장성 있는 APM(Application Performance Management) 서비스입니다. 이를 사용하여 라이브 웹 애플리케이션을 모니터링합니다. Application Insights는 성능 이상을 자동으로 검색 합니다. 문제를 진단 하 고 사용자가 실제로 수행할 앱을 사용 하 여 이해 하는 데 강력한 분석 도구를 포함 합니다. 성능 및 가용성을 지속적으로 향상시킬 수 있도록 설계되었습니다.

#### <a name="azure-security-center"></a>Azure Security Center

[Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) 예방, 감지 및 향상 된 가시성으로 위협에 대응 (및 제어) 웹 응용 프로그램을 포함 하 여 Azure 리소스의 보안입니다. Azure Security Center는 발견 않을 수 있는 위협을 검색 하는 데 도움이 됩니다. 다양 한 보안 솔루션을 사용 하 여 작동합니다.

Security Center의 무료 계층에서는 Azure 리소스에만 사용 가능한 제한적 보안 기능이 제공됩니다. 합니다 [Security Center 표준 계층과](https://docs.microsoft.com/azure/security-center/security-center-onboarding) 온-프레미스 리소스 및 기타 클라우드에서 이러한 기능을 확장 합니다.
Security Center 표준 계층 수 있습니다.

  - 찾기 및 보안 취약점을 수정 합니다.
  - 액세스 및 애플리케이션 제어를 적용하여 악의적인 활동을 차단합니다.
  - 분석 및 인텔리전스를 사용 하 여 위협을 감지 합니다.
  - 공격을 받을 때 신속 하 게 대응 합니다.

## <a name="next-steps"></a>다음 단계
다음 문서에서는 보안 제어 권장 도움이 될 수 있는 활동 디자인 및 보안 응용 프로그램을 개발 합니다.

- [보안 응용 프로그램 디자인](secure-design.md)
- [안전한 응용 프로그램 개발](secure-develop.md)
