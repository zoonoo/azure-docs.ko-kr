---
title: Microsoft Security Code Analysis 릴리스
description: 이 문서에서는 Microsoft Security Code Analysis 확장의 예정된 릴리스에 대해 설명합니다
author: sukhans
manager: sukhans
ms.author: terrylan
ms.date: 03/22/2021
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 7596df66dbcbe1b7cdefab4811da7174bc83ac65
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104801182"
---
# <a name="microsoft-security-code-analysis-releases-and-roadmap"></a>Microsoft Security Code Analysis 릴리스 및 로드맵

> [!Note]
> 2022년 3월 1일부터 MSCA(Microsoft Security Code Analysis) 확장이 사용 중지됩니다. 기존 MSCA 고객은 2022년 3월 1일까지 MSCA에 대한 액세스가 유지됩니다. Azure DevOps의 대체 옵션은 [OWASP Source Code Analysis 도구](https://owasp.org/www-community/Source_Code_Analysis_Tools)를 참조하세요. GitHub로 마이그레이션을 계획하는 고객의 경우 [Github 고급 보안](https://docs.github.com/github/getting-started-with-github/about-github-advanced-security)을 확인해보세요.

개발자 지원과 파트너 관계인 Microsoft Security Code Analysis 팀은 MSCA 확장에 대한 최근 및 향후 개선 사항을 발표하게 되어 자랑스럽게 생각합니다.


## <a name="credential-scanner-v20-released-in-april-2020"></a>자격 증명 스캐너 v2.0: 2020년 4월 릴리스됨

### <a name="innovations--improvements"></a>혁신 및 개선 사항

- **핵심 엔진**

   - 거의 선형의 런타임으로 평균 성능 25% 업그레이드
   - 향상된 정확도를 위한 컨텍스트/증거 기반 검색 및 우선 순위 지정
   - 명확한 자리 표시자에 대한 일반 암호 검색 및 일치하는 논리의 기능 향상(예: fakePassword)

- **범위** - 다음 최상위 요청을 포함하여 25가지 이상의 비밀 유형 지원.

   - 패브릭 계정 인증서 암호
   - 클라이언트 암호/API 키
   - HTTP 인증 헤더
   - Amazon S3 클라이언트 암호 액세스 키
   - Azure Active Directory 클라이언트 액세스 토큰
   - Azure Function Master/API 키
   - Power BI 액세스 키
   - Azure Resource Manager 템플릿 암호 패턴

- **출력**

   - SARIF 2.1 및 CSV 파일 출력 파일 형식 지원

## <a name="binskim-v160-released-in-april-2020"></a>BinSkim v1.6.0: 2020년 4월 릴리스됨

### <a name="improvements"></a>향상 된 기능

- 기능: 최종 SARIF v2(버전 2.1.16)로 업데이트합니다. 이 업데이트를 통해 명령줄에서 --해시를 전달할 때 결과 캐싱이 가능하고, 검색 대상의 여러 복사본을 사용하여 디렉터리를 반복적으로 분석할 때 성능이 크게 향상됩니다.
- 버그 픽스: BA2021.DoNotMarkWritableSectionsAsExecutable 출력의 오타를 수정했습니다.
- 성능: IL Library(미리 컴파일된) 이진 파일을 비롯하여 관리형 어셈블리의 모든 비 혼합 모드에 대해 PDB 로드를 제거합니다.
- 가음성 픽스: 이진 파일과 함께 배치된 PDB가 실제로 분석 중인 이진 파일과 일치하는지 확인합니다
- 기능: 추가(로컬, 비 기호 서버) PDB 조회 위치를 지정하기 위해 --로컬-기호-디렉터리 인수를 제공합니다
- 가양성 픽스: 생성된 .NET core 네이티브 부트스트랩 exe(사용자 제어 가능 코드가 아님)에 대한 PDB 기반 분석을 건너뜁니다.

## <a name="whats-next-in-q3-cy20"></a>2020년 3분기 다음 일정은 무엇인가요?

- Java 보안 분석 도구
- Python 보안 분석 도구
- TypeScript 및 JavaScript에 대한 TS Lint를 대체하는 ES Lint
- Resource Manager 템플릿 분석 도구

## <a name="tool-deprecation-notification"></a>도구 사용 중단 알림

### <a name="microsoft-security-risk-detection-msrd-is-deprecated-on-june-26-2020"></a>MSRD(Microsoft Security Risk Detection)는 2020년 6월 26일부터 사용이 중지됩니다.

사용되지 않는 MSRD 퍼지 서비스는 Azure용 오픈 소스 자체 호스팅 개발자 퍼지 플랫폼으로 대체됩니다. 이 플랫폼은 현재 여러 Microsoft의 핵심 제품 팀과 협력하여 개발되고 테스트되고 있습니다. 이 퍼지 플랫폼은 새니타이저(sanitizer)를 통합하고, 소프트웨어 프로젝트를 사용하여 시간에 따라 증가하는 CI/CD 파이프라인에 기본 제공되는 적응형 학습 퍼지 테스트를 허용합니다. 이 플랫폼의 오픈 소스 릴리스는 2020년 후반기 예정입니다.

## <a name="next-steps"></a>다음 단계

Microsoft Security Code Analysis를 온보딩하고 설치하는 방법에 대한 지침은 [온보딩 및 설치 가이드](security-code-analysis-onboard.md)를 참조하세요.

확장 및 제공된 도구에 대한 추가 질문이 있는 경우 [FAQ 페이지](security-code-analysis-faq.md)를 확인하세요.
