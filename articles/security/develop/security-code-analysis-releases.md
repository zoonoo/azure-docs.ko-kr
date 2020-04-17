---
title: 마이크로소프트 보안 코드 분석 릴리스
description: 이 문서에서는 Microsoft 보안 코드 분석 확장에 대 한 예정된 릴리스에 대해 설명 합니다.
author: sukhans
manager: sukhans
ms.author: terrylan
ms.date: 04/14/2020
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: a07ce7437d664baca0cfdc310dbc2631f41fdbcc
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81462036"
---
# <a name="microsoft-security-code-analysis-releases-and-roadmap"></a>Microsoft 보안 코드 분석 릴리스 및 로드맵

개발자 지원과 협력하여 Microsoft 보안 코드 분석 팀은 MSCA 확장에 대한 최근 및 향후 개선 사항들을 발표하게 된 것을 자랑스럽게 생각합니다. 아래 로드맵을 참조하십시오.

![릴리스](./media/security-code-analysis-releases/releases.png)

## <a name="credential-scanner-v20-released-on-april-1-2020"></a>자격 증명 스캐너 v2.0: 2020년 4월 1일 출시

### <a name="innovations--improvements"></a>혁신 & 개선 사항

- **코어 엔진**

   - 선형 실행 시간이 거의 25%인 평균 성능 업그레이드
   - 정확도 향상을 위한 컨텍스트/증거 기반 검색 및 순위
   - 일반 암호 검색 및 명백한 자리 표시자에 대한 일치 논리 개선(예: fakePassword)

- **적용 범위** - 다음 상단 요청 을 포함하여 25 개 이상의 비밀 유형에 대한 지원 :

   - 패브릭 계정 인증서 암호
   - 클라이언트 시크릿/API 키
   - HTTP 권한 부여 헤더
   - 아마존 S3 클라이언트 비밀 액세스 키
   - Azure Active 디렉터리 클라이언트 액세스 토큰
   - Azure 함수 마스터/API 키
   - 전원 BI 액세스 키
   - Azure 리소스 관리자 템플릿 암호 패턴

- **outputs**

   - SARIF 2.1 및 CSV 파일 출력 파일 형식 지원

## <a name="binskim-v160-to-be-released-on-april-2020"></a>빈스킴 v1.6.0: 2020년 4월 출시 예정

### <a name="improvements"></a>개선 사항

- 기능: 최종 SARIF v2(버전 2.1.16)로 업데이트합니다. 이렇게 하면 명령줄에서 해시를 전달할 때 결과를 캐싱할 수 있으며, 스캔 대상의 여러 복사본을 사용하여 디렉터리를 재귀적으로 분석할 때 성능이 크게 향상됩니다.
- 버그 수정 : BA2021에서 오타를 수정합니다. DoNotMarkWritable섹션실행 가능 출력.
- 성능: IL 라이브러리(컴파일된 시간 보다) 바이너리를 포함하여 관리되는 어셈블리의 모든 비혼합 모드에 대한 PDB 로드를 제거합니다.
- 거짓 네거티브 수정: 이진과 함께 배치된 PDB가 실제로 분석 중인 바이너리와 일치하는지 확인합니다.
- 기능: --local-symbol-directories 인수를 제공하여 추가(로컬, 비심볼 서버) PDB 조회 위치를 지정합니다.
- 거짓 긍정 수정: 생성된 .NET 코어 네이티브 부트스트랩 exe(사용자가 제어할 수 없는 코드)에 대한 PDB 기반 분석을 건너뜁니다.

## <a name="whats-next-in-fy20"></a>20년도의 다음 단계는 무엇입니까?

- 자바 보안 분석 도구
- 파이썬 보안 분석 도구
- ES 린트 타이프 스크립트 및 자바 스크립트에 대 한 TS 보풀을 대체 하려면

## <a name="next-steps"></a>다음 단계

Microsoft 보안 코드 분석을 온보딩하고 설치하는 방법에 대한 지침은 [온보딩 및 설치 가이드를](security-code-analysis-onboard.md)참조하십시오.

확장 프로그램 및 제공되는 도구에 대해 더 궁금한 점이 있으면 [FAQ 페이지를](security-code-analysis-faq.md)확인하십시오.
