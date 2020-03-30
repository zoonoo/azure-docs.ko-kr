---
title: Microsoft Azure에 보안 응용 프로그램 배포
description: 이 문서에서는 웹 응용 프로그램 프로젝트의 릴리스 및 응답 단계에서 고려해야 할 모범 사례에 대해 설명합니다.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/12/2019
ms.topic: article
ms.service: security
ms.subservice: security-develop
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: dfe4f09d00a5629249a3041946190f56e83c3480
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "68934879"
---
# <a name="deploy-secure-applications-on-azure"></a>Azure에서 보안 응용 프로그램 배포
이 문서에서는 클라우드에 대 한 응용 프로그램을 배포할 때 고려해야 할 보안 활동 및 컨트롤을 제시 합니다. Microsoft [보안 개발 수명 주기(SDL)의](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) 릴리스 및 응답 단계에서 고려해야 할 보안 질문 및 개념이 다룹니다. 목표는 보다 안전한 응용 프로그램을 배포하는 데 사용할 수 있는 활동 및 Azure 서비스를 정의하는 데 도움을 주는 것입니다.

다음 SDL 단계는 이 문서에서 다룹니다.

- 해제
- 응답

## <a name="release"></a>해제
릴리스 단계의 초점은 공개 릴리스를 위한 프로젝트를 준비하는 것입니다.
여기에는 릴리스 후 서비스 작업을 효과적으로 수행하고 나중에 발생할 수 있는 보안 취약점을 해결하는 방법을 계획하는 것이 포함됩니다.

### <a name="check-your-applications-performance-before-you-launch"></a>시작하기 전에 응용 프로그램의 성능을 확인합니다.

응용 프로그램을 시작하거나 프로덕션에 업데이트를 배포하기 전에 응용 프로그램의 성능을 확인합니다. Visual Studio를 사용하여 클라우드 기반 [부하 테스트를](https://www.visualstudio.com/docs/test/performance-testing/getting-started/getting-started-with-performance-testing) 실행하여 응용 프로그램의 성능 문제를 찾고, 배포 품질을 개선하고, 응용 프로그램이 항상 가동 중이거나 사용 가능한지 확인하고, 응용 프로그램이 시작에 대한 트래픽을 처리할 수 있는지 확인합니다.

### <a name="install-a-web-application-firewall"></a>웹 애플리케이션 방화벽 설치

웹 애플리케이션의 널리 알려진 취약점을 악용하는 악의적인 공격이 점점 많아지고 있습니다. 이러한 악용 중에는 SQL 주입 공격 및 사이트 간 스크립팅 공격이 일반적입니다. 응용 프로그램 코드에서 이러한 공격을 방지하는 것은 어려울 수 있습니다. 응용 프로그램 토폴로지의 여러 계층에서 엄격한 유지 관리, 패치 및 모니터링이 필요할 수 있습니다. 중앙 집중식 WAF를 사용하면 보안 관리가 더 간단해지게 됩니다. WAF 솔루션은 중앙 위치에서 알려진 취약점을 패치하는 것과 각 개별 웹 응용 프로그램을 보호하여 보안 위협에 대응할 수도 있습니다.

[Azure 응용 프로그램 게이트웨이 WAF는](../../application-gateway/waf-overview.md) 일반적인 악용 및 취약점으로부터 웹 응용 프로그램을 중앙 집중식으로 보호합니다. WAF는 [OWASP 코어 규칙 세트](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.0 또는 2.2.9의 규칙을 기반으로 합니다.

### <a name="create-an-incident-response-plan"></a>인시던트 대응 계획 만들기

사고 대응 계획을 준비하는 것은 시간이 지남에 따라 발생할 수 있는 새로운 위협을 해결하는 데 매우 중요합니다. 사고 대응 계획을 준비하는 것은 적절한 보안 비상 연락처를 식별하고 조직의 다른 그룹및 라이선스가 부여된 타사 코드에서 상속된 코드에 대한 보안 서비스 계획을 수립하는 것입니다.

### <a name="conduct-a-final-security-review"></a>최종 보안 검토 수행

수행된 모든 보안 활동을 의도적으로 검토하면 소프트웨어 릴리스 또는 응용 프로그램에 대한 준비 상태를 보장하는 데 도움이 됩니다. 최종 보안 검토(FSR)에는 일반적으로 요구 사항 단계에서 정의된 품질 게이트 및 버그 막대에 대한 위협 모델, 도구 출력 및 성능 검토가 포함됩니다.

### <a name="certify-release-and-archive"></a>릴리스 및 보관 인증

릴리스 전에 소프트웨어를 인증하면 보안 및 개인 정보 보호 요구 사항을 충족하는 데 도움이 됩니다. 모든 관련 데이터를 보관하는 것은 릴리스 후 서비스 작업을 수행하는 데 필수적입니다. 또한 아카이빙을 통해 지속적인 소프트웨어 엔지니어링과 관련된 장기적인 비용을 절감할 수 있습니다.

## <a name="response"></a>응답
릴리스 후 대응 단계는 개발 팀이 새로운 소프트웨어 위협 및 취약점에 대한 보고에 적절하게 대응할 수 있는 데 중점을 둡니다.

### <a name="execute-the-incident-response-plan"></a>사고 대응 계획 실행

릴리스 단계에서 수립된 인시던트 대응 계획을 구현할 수 있는 것은 등장하는 소프트웨어 보안 또는 개인 정보 보호 취약점으로부터 고객을 보호하는 데 필수적입니다.

### <a name="monitor-application-performance"></a>애플리케이션 성능 모니터링

응용 프로그램이 배포된 후 지속적으로 모니터링하면 성능 문제와 보안 취약점을 감지할 수 있습니다.
응용 프로그램 모니터링을 지원하는 Azure 서비스는 다음과 같습니다.

  - Azure Application Insights
  - Azure Security Center

#### <a name="application-insights"></a>애플리케이션 정보

[응용 프로그램 인사이트는](../../azure-monitor/app/app-insights-overview.md) 여러 플랫폼의 웹 개발자를 위한 확장 가능한 APM(응용 프로그램 성능 관리) 서비스입니다. 이를 사용하여 라이브 웹 애플리케이션을 모니터링합니다. Application Insights는 자동으로 성능 이상을 검색합니다. 여기에는 문제를 진단하고 사용자가 실제로 앱에서 수행하는 작업을 이해하는 데 도움이 되는 강력한 분석 도구가 포함되어 있습니다. 성능 및 가용성을 지속적으로 향상시킬 수 있도록 설계되었습니다.

#### <a name="azure-security-center"></a>Azure Security Center

[Azure Security Center를](../../security-center/security-center-intro.md) 사용하면 웹 응용 프로그램을 포함하여 Azure 리소스의 보안에 대한 가시성을 높이고 제어하여 위협을 방지, 탐지 및 대응할 수 있습니다. Azure 보안 센터는 눈에 띄지 않을 수 있는 위협을 감지하는 데 도움이 됩니다. 다양한 보안 솔루션과 함께 작동합니다.

Security Center의 무료 계층에서는 Azure 리소스에만 사용 가능한 제한적 보안 기능이 제공됩니다. [보안 센터 표준 계층은](../../security-center/security-center-onboarding.md) 이러한 기능을 온-프레미스 리소스 및 기타 클라우드로 확장합니다.
보안 센터 표준은 다음을 지원합니다.

  - 보안 취약점을 찾아 수정합니다.
  - 액세스 및 응용 프로그램 컨트롤을 적용하여 악의적인 활동을 차단합니다.
  - 분석 및 인텔리전스를 사용하여 위협을 탐지합니다.
  - 공격을 받을 때 신속하게 대응합니다.

## <a name="next-steps"></a>다음 단계
다음 문서에서는 보안 응용 프로그램을 디자인하고 개발하는 데 도움이 되는 보안 제어 및 활동을 권장합니다.

- [안전한 애플리케이션 설계](secure-design.md)
- [보안 애플리케이션 개발](secure-develop.md)
