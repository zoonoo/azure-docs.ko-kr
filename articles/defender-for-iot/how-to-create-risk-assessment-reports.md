---
title: 위험 평가 보고서 만들기
description: 개별 센서가 검색 한 네트워크 위험 또는 모든 센서가 감지한 위험에 대 한 집계 보기를 파악 합니다.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/17/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 367974e214892d4dfefeb138ae5bfa516f49882a
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98811673"
---
# <a name="risk-assessment-reporting"></a>위험 평가 보고

## <a name="about-risk-assessment-reports"></a>위험 평가 보고서 정보

위험 평가 보고서는 다음을 제공 합니다.

- 조직 센서가 검색 하는 장치에 대 한 전반적인 보안 점수입니다.

- 개별 센서가 검색 한 각 네트워크 장치에 대 한 보안 점수입니다.

- 취약 한 장치 수, 향상 된 장치 및 보안 장치를 필요로 하는 장치에 대 한 분석입니다.

-  보안 및 운영 문제에 대 한 통찰력:

    - 구성 문제

    - 보안 수준별로 우선 순위가 지정 된 장치 취약점

    - 네트워크 보안 문제

    - 네트워크 작동 문제

    - ICS 네트워크에 대 한 연결

    - 인터넷 연결

    - 산업용 맬웨어 표시기

    - 프로토콜 문제

    - 공격 벡터

### <a name="risk-mitigation"></a>위험 완화

보고서는 보안 점수를 개선 하는 데 도움이 되는 권장 사항을 제공 합니다. 예를 들어 최신 보안 업데이트를 설치 하 고 펌웨어를 최신 버전으로 업그레이드 하거나 경고에 대해 후속 작업을 수행 합니다.

## <a name="about-security-scores"></a>보안 점수 정보

각 보고서에서 전체 네트워크 보안 점수가 생성 됩니다. 점수는 100% 보안의 백분율을 나타냅니다. 예를 들어, 30%의 점수는 네트워크가 30% 안전 하다는 것을 의미 합니다.

위험 평가 점수는 패킷 검사, 동작 모델링 엔진 및 SCADA 특정 상태 시스템 디자인에서 얻은 정보를 기반으로 합니다.

보안 **장치** 는 90% 이상의 보안 점수가 있는 장치입니다.

**개선이 필요한 장치**: 70%에서 89% 사이의 보안 점수가 있는 장치입니다.

**취약 한 장치** 는 70% 미만의 보안 점수를 가진 장치입니다.

## <a name="create-risk-assessment-reports"></a>위험 평가 보고서 만들기

PDF 위험 평가 보고서를 만듭니다. 보고서 이름은 risk-assessment-report-1.pdf으로 자동 생성 됩니다. 새로 만든 보고서 마다 번호가 업데이트 됩니다.  만든 날짜와 시간을 표시 합니다.

### <a name="create-a-sensor-risk-assessment-report"></a>센서 위험 평가 보고서 만들기

로그인 한 센서에의 한 검색을 기반으로 위험 평가 보고서를 만듭니다.

보고서를 만들려면

1. 센서 콘솔에 로그인 합니다.
1. 측면 메뉴에서 **위험 평가** 를 선택 합니다.
1. **보고서 생성** 을 선택 합니다. 보관 된 보고서 섹션에 보고서가 나타납니다.
1. 보관 된 보고서 섹션에서 보고서를 선택 하 여 다운로드 합니다.

:::image type="content" source="media/how-to-generate-reports/risk-assessment.png" alt-text="위험 평가의 뷰입니다.":::

회사 로고를 가져오려면:

- **로고 가져오기** 를 선택 합니다.

### <a name="create-an-on-premises-management-console-risk-assessment-report"></a>온-프레미스 관리 콘솔 위험 평가 보고서 만들기

온-프레미스 관리 콘솔에서 관리 하는 센서에의 한 검색을 기반으로 위험 평가 보고서를 만듭니다. 

보고서를 만들려면

1. 측면 메뉴에서 **위험 평가** 를 선택 합니다.

2. **센서 선택** 드롭다운 목록에서 센서를 선택 합니다.

3. **보고서 생성** 을 선택 합니다.

4. **보관 된 보고서** 섹션에서 **다운로드** 를 선택 합니다.

회사 로고를 가져오려면:

- **로고 가져오기** 를 선택 합니다.

:::image type="content" source="media/how-to-generate-reports/import-logo-screenshot.png" alt-text="위험 평가 보기를 통해 로고를 가져옵니다.":::

## <a name="see-also"></a>참고 항목

[공격 벡터 보고](how-to-create-attack-vector-reports.md)

