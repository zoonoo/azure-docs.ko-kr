---
title: Microsoft 보안 코드 분석 릴리스
description: 이 문서에서는 Microsoft 보안 코드 분석 확장의 예정 된 릴리스에 대해 설명 합니다.
author: sukhans
manager: sukhans
ms.author: terrylan
ms.date: 04/24/2020
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: d4281d3b6132e551283a71cd1801ef462fbfc68c
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82146134"
---
# <a name="microsoft-security-code-analysis-releases-and-roadmap"></a>Microsoft 보안 코드 분석 릴리스 및 로드맵

개발자 지원와 파트너 관계에 있는 Microsoft 보안 코드 분석 팀은 MSCA 확장에 대 한 최근 및 향후 향상 된 기능을 발표 하 게 되어 기쁘게 생각 합니다. 아래의 로드맵을 참조 하세요.

![릴리스](./media/security-code-analysis-releases/releases.png)

## <a name="credential-scanner-v20-released-in-april-2020"></a>자격 증명 스캐너 v2.0:4 월 2020 릴리스

### <a name="innovations--improvements"></a>혁신 & 개선 사항

- **핵심 엔진**

   - 거의 선형 실행 시간으로 25%의 평균 성능 업그레이드
   - 향상 된 정확도를 위한 컨텍스트/증명 정보 기반 검색 및 순위
   - 명확한 자리 표시자에 대 한 일반 암호 검색 및 일치 논리의 향상 된 기능 (예: fakePassword)

- **검사** -다음 상위 요청을 포함 하 여 25 + 비밀 유형 지원:

   - 패브릭 계정 인증서 암호
   - 클라이언트 암호/a p i 키
   - HTTP 인증 헤더
   - Amazon S3 클라이언트 암호 액세스 키
   - 클라이언트 액세스 토큰 Azure Active Directory
   - Azure Function Master/API 키
   - Power BI 액세스 키
   - Azure Resource Manager 템플릿 암호 패턴

- **outputs**

   - SARIF 2.1 및 CSV 파일 출력 파일 형식에 대 한 지원

## <a name="binskim-v160-released-in-april-2020"></a>BinSkim v 1.6.0:4 월 2020 릴리스

### <a name="improvements"></a>향상 된 기능

- 기능: 최종 SARIF v2 (버전 2.1.16)로 업데이트 합니다. 이를 통해 명령줄에서--해시를 전달할 때 결과 캐싱이 가능 하 고, 검색 대상의 여러 복사본으로 디렉터리를 반복적으로 분석할 때 성능이 크게 향상 됩니다.
- 버그 수정: BA2021의 오타를 수정 합니다. DoNotMarkWritableSectionsAsExecutable 출력입니다.
- 성능: IL Library (미리 컴파일된) 이진 파일을 비롯 하 여 관리 되는 어셈블리의 모든 비 혼합 모드에 대해 PDB 로드를 제거 합니다.
- 거짓 부정 픽스: 이진과 함께 배치 된 PDB가 실제로 분석 중인 이진과 일치 하는지 확인 합니다.
- 기능: 추가 (로컬, 비 기호 서버) PDB 조회 위치를 지정 하기 위해--local 기호 디렉터리 인수를 제공 합니다.
- FALSE 긍정 픽스: 생성 된 .NET core 네이티브 부트스트랩 exe (사용자 제어 코드가 아님)에 대 한 PDB 기반 분석을 건너뜁니다.

## <a name="whats-next-in-fy20"></a>FY20의 다음 단계는 무엇 인가요?

- Java 보안 분석 도구
- Python 보안 분석 도구
- TypeScript 및 JavaScript에 대 한 TS 보푸라기가를 대체 하는 경우

## <a name="next-steps"></a>다음 단계

Microsoft 보안 코드 분석을 등록 하 고 설치 하는 방법에 대 한 지침은 [온 보 딩 및 설치 가이드](security-code-analysis-onboard.md)를 참조 하세요.

확장 및 제공 된 도구에 대 한 추가 질문이 있는 경우 [FAQ 페이지](security-code-analysis-faq.md)를 확인 하세요.
