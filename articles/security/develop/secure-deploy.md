---
title: Microsoft Azure에서 보안 응용 프로그램 배포
description: 이 문서에서는 웹 응용 프로그램 프로젝트의 릴리스 및 응답 단계 중에 고려할 모범 사례를 설명 합니다.
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
ms.openlocfilehash: dd86e6bf571dd67da00aee63eadff031a1040ff7
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68728732"
---
# <a name="deploy-secure-applications-on-azure"></a>Azure에서 보안 응용 프로그램 배포
이 문서에서는 클라우드의 응용 프로그램을 배포할 때 고려해 야 할 보안 작업 및 제어를 제공 합니다. Microsoft [SDL (보안 개발 수명 주기)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) 의 릴리스 및 응답 단계 중에 고려해 야 하는 보안 질문 및 개념을 다룹니다. 목표는 보다 안전한 응용 프로그램을 배포 하는 데 사용할 수 있는 활동 및 Azure 서비스를 정의 하는 데 도움을 주는 것입니다.

이 문서에서 다루는 SDL 단계는 다음과 같습니다.

- 릴리스
- 응답

## <a name="release"></a>릴리스
릴리스 단계의 초점은 공용 릴리스에 대 한 프로젝트를 패키징하여는 것입니다.
여기에는 릴리스 후 서비스 작업을 효과적으로 수행 하 고 나중에 발생할 수 있는 보안 취약점을 해결 하는 계획 방법이 포함 됩니다.

### <a name="check-your-applications-performance-before-you-launch"></a>시작 하기 전에 응용 프로그램의 성능을 확인 합니다.

응용 프로그램을 시작 하기 전에 응용 프로그램의 성능을 확인 하거나 프로덕션 환경에 업데이트를 배포 합니다. Visual Studio를 사용 하 여 클라우드 기반 [부하 테스트](https://www.visualstudio.com/docs/test/performance-testing/getting-started/getting-started-with-performance-testing) 를 실행 하 여 응용 프로그램에서 성능 문제를 찾고, 배포 품질을 향상 하 고, 응용 프로그램이 항상 사용 가능 하며 응용 프로그램에서 시작에 대 한 트래픽을 처리할 수 있는지 확인 합니다.

### <a name="install-a-web-application-firewall"></a>웹 애플리케이션 방화벽 설치

웹 애플리케이션의 널리 알려진 취약점을 악용하는 악의적인 공격이 점점 많아지고 있습니다. 이러한 악용 중에는 SQL 삽입 공격과 사이트 간 스크립팅 공격이 있습니다. 응용 프로그램 코드에서 이러한 공격을 방지 하는 것은 어려울 수 있습니다. 응용 프로그램 토폴로지의 여러 계층에서 엄격한 유지 관리, 패치 적용 및 모니터링이 필요할 수 있습니다. 중앙 집중식 WAF를 사용 하면 보안을 보다 간단 하 게 관리할 수 있습니다. WAF 솔루션은 각각의 개별 웹 응용 프로그램에 대 한 보안을 유지 하 고 중앙 위치에서 알려진 취약성을 패치 하 여 보안 위협에 대응할 수도 있습니다.

[Azure 애플리케이션 Gateway WAF](https://docs.microsoft.com/azure/application-gateway/waf-overview) 는 일반적인 악용 및 취약점 으로부터 웹 응용 프로그램에 대 한 중앙 집중식 보호를 제공 합니다. WAF는 [OWASP core 규칙 집합](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.0 또는 2.2.9의 규칙을 기반으로 합니다.

### <a name="create-an-incident-response-plan"></a>인시던트 대응 계획 만들기

사고 대응 계획을 준비 하는 것은 시간이 지남에 따라 발생할 수 있는 새로운 위협을 해결 하는 데 도움이 됩니다. 인시던트 대응 계획을 준비 하는 경우에는 적절 한 보안 비상 연락처를 식별 하 고 조직의 다른 그룹에서 상속 되는 코드에 대 한 보안 서비스 계획을 설정 하 고 사용이 허가 된 타사 코드를 사용 합니다.

### <a name="conduct-a-final-security-review"></a>최종 보안 검토 수행

수행 된 모든 보안 활동을 의도적으로 검토 하면 소프트웨어 릴리스 또는 응용 프로그램에 대 한 준비 상태를 보장할 수 있습니다. 최종 보안 검토 (FSR)에는 일반적으로 요구 사항 단계에서 정의한 품질 게이트 및 버그 바에 대 한 위협 모델, 도구 출력 및 성능 검사가 포함 되어 있습니다.

### <a name="certify-release-and-archive"></a>릴리스 및 보관 인증

릴리스 전에 소프트웨어를 인증 하면 보안 및 개인 정보 요구 사항이 충족 됩니다. 모든 관련 데이터를 보관 하는 것은 릴리스 후 서비스 작업을 수행 하는 데 필수적입니다. 또한 보관을 통해 지속적인 소프트웨어 엔지니어링과 관련 된 장기 비용을 낮출 수 있습니다.

## <a name="response"></a>응답
개발 팀의 응답 후 단계 센터는 새로운 소프트웨어 위협 및 취약성에 대 한 모든 보고서에 적절 하 게 대응 하 고 사용할 수 있습니다.

### <a name="execute-the-incident-response-plan"></a>인시던트 응답 계획 실행

릴리스 단계에서 인시던트 대응 계획 instituted를 구현할 수 있는 것은 고객이 소프트웨어 보안 또는 개인 정보 보호 취약점 으로부터 고객을 보호 하는 데 필요 합니다.

### <a name="monitor-application-performance"></a>애플리케이션 성능 모니터링

응용 프로그램이 배포 된 후 지속적으로 모니터링 하면 보안 취약성 뿐만 아니라 성능 문제를 감지 하는 데 도움이 됩니다.
응용 프로그램 모니터링을 지 원하는 Azure 서비스는 다음과 같습니다.

  - Azure Application Insights
  - Azure Security Center

#### <a name="application-insights"></a>Application Insights

[Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview)는 여러 플랫폼의 웹 개발자를 위한 확장성 있는 APM(Application Performance Management) 서비스입니다. 이를 사용하여 라이브 웹 애플리케이션을 모니터링합니다. Application Insights에서 성능 변칙을 자동으로 검색 합니다. 문제를 진단 하 고 사용자가 앱에서 실제로 수행 하는 작업을 파악 하는 데 도움이 되는 강력한 분석 도구를 포함 합니다. 성능 및 가용성을 지속적으로 향상시킬 수 있도록 설계되었습니다.

#### <a name="azure-security-center"></a>Azure Security Center

[Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) 를 통해 웹 응용 프로그램을 포함 하 여 Azure 리소스의 보안을 향상 하 고 제어 하 여 위협을 예방, 감지 및 대응 하는 데 도움이 됩니다. Azure Security Center는 다른 방법으로는 알지 못할 수 있는 위협을 검색 하는 데 도움이 됩니다. 다양 한 보안 솔루션에서 작동 합니다.

Security Center의 무료 계층에서는 Azure 리소스에만 사용 가능한 제한적 보안 기능이 제공됩니다. [Security Center 표준 계층](https://docs.microsoft.com/azure/security-center/security-center-onboarding) 은 이러한 기능을 온-프레미스 리소스 및 기타 클라우드로 확장 합니다.
Security Center 표준은 다음을 지원 합니다.

  - 보안 취약성을 찾아 해결 합니다.
  - 액세스 및 애플리케이션 제어를 적용하여 악의적인 활동을 차단합니다.
  - 분석 및 인텔리전스를 사용 하 여 위협을 감지 합니다.
  - 공격을 받을 때 신속 하 게 응답 합니다.

## <a name="next-steps"></a>다음 단계
다음 문서에서는 보안 응용 프로그램을 디자인 하 고 개발 하는 데 사용할 수 있는 보안 제어 및 작업을 권장 합니다.

- [보안 응용 프로그램 디자인](secure-design.md)
- [보안 응용 프로그램 개발](secure-develop.md)
