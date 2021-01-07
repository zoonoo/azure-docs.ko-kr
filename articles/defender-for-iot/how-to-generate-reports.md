---
title: 보고서 생성
description: IoT 보고 도구에 대해 Defender를 사용 하 여 네트워크 활동, 위험, 공격 및 추세에 대 한 통찰력을 확보 하세요.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/17/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: d0c3f531ede0a590a6ba7bb21c6edbd768c08069
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/31/2020
ms.locfileid: "97840343"
---
# <a name="generate-reports"></a>보고서 생성

IoT 보고 도구에 대해 Azure Defender를 사용 하 여 네트워크 활동, 위험, 공격 및 추세에 대 한 통찰력을 얻습니다. 보고서를 생성 하는 데 사용할 수 있는 도구는 다음과 같습니다.

- 개별 센서가 감지한 활동을 기반으로 합니다.
- 모든 센서가 감지한 활동을 기반으로 합니다. 

이러한 보고서는 온-프레미스 관리 콘솔에서 생성 됩니다.

## <a name="reports-for-sensor-risk-assessment"></a>센서 위험 평가에 대 한 보고서

위험 평가 보고를 사용 하면 전체 네트워크 보안 점수 뿐만 아니라 각 네트워크 장치에 대 한 보안 점수를 생성할 수 있습니다. 전체 점수는 100% 보안의 비율을 나타냅니다.

이 점수는 패킷 검사, 동작 모델링 엔진 및 SCADA 특정 상태 시스템 디자인의 결과를 기반으로 합니다. 다음과 같은 다양 한 다른 정보를 사용할 수 있습니다.

- 구성 문제

- 보안 수준별로 우선 순위가 지정 된 장치 취약점

- 네트워크 보안 문제

- 네트워크 작동 문제

- 공격 벡터

- ICS 네트워크에 대 한 연결

- 인터넷 연결

- 산업용 맬웨어 표시기

- 프로토콜 문제

  - 보안 장치: 90% 이상의 보안 점수가 있는 장치입니다.

- 보안 **장치**: 90% 이상의 보안 점수가 있는 장치입니다.

- **취약 한 장치**: 70% 미만의 보안 점수가 있는 장치입니다.

- **개선이 필요한 장치**: 70%에서 89% 사이의 보안 점수가 있는 장치입니다.

보고서를 만들려면

1. 측면 메뉴에서 **위험 평가** 를 선택 합니다.
2. **센서 선택** 드롭다운 목록에서 센서를 선택 합니다.
3. **보고서 생성** 을 선택 합니다.
4. 보관 된 보고서 섹션에서 **다운로드** 를 선택 합니다.

:::image type="content" source="media/how-to-generate-reports/risk-assessment.png" alt-text="위험 평가의 뷰입니다.":::

회사 로고를 가져오려면:

회사 로고를 가져오려면:

- **로고 가져오기** 를 선택 합니다.

## <a name="reports-for-sensor-attack-vector"></a>센서 공격 벡터에 대 한 보고서

공격 벡터 보고서는 악용 가능한 장치의 취약점 체인에 대 한 그래픽 표현을 제공 합니다. 이러한 취약점으로 인해 키 네트워크 장치에 대 한 공격자 액세스 권한이 제공 될 수 있습니다. 공격 벡터 시뮬레이터는 공격 벡터를 실시간으로 계산 하 고 특정 대상에 대 한 모든 공격 벡터를 분석 합니다.

공격 벡터를 사용 하면 공격 순서에서 완화 작업의 영향을 평가할 수 있습니다. 예를 들어, 공격 체인을 중단 하 여 시스템 업그레이드가 공격자의 경로를 중단 하거나 대체 공격 경로가 남아 있는 경우를 결정할 수 있습니다. 이 정보는 재구성 및 완화 작업의 우선 순위를 지정 하는 데 도움이 됩니다.

:::image type="content" source="media/how-to-generate-reports/control-center.png" alt-text="제어 센터에서 경고를 확인 합니다.":::

> [!NOTE]
> 관리자 및 보안 분석가는이 섹션에 설명 된 절차를 수행할 수 있습니다.

공격 벡터 시뮬레이션을 만들려면 다음을 수행 합니다.

1. 측면 메뉴에서 :::image type="content" source="media/how-to-generate-reports/plus.png" alt-text="더하기 기호":::를 선택 하 여 시뮬레이션을 추가 합니다.

 :::image type="content" source="media/how-to-generate-reports/vector.png" alt-text="공격 벡터 시뮬레이션입니다.":::

2. 시뮬레이션 속성을 입력 합니다.

   - **이름**: 시뮬레이션 이름입니다.

   - **최대 벡터**: 단일 시뮬레이션의 최대 벡터 수입니다.

   - **장치 맵에 표시**: 공격 벡터를 장치 맵에 필터로 표시 합니다.

   - **모든 원본 장치**: 공격 벡터는 모든 장치를 공격 원본으로 간주 합니다.

   - **공격 원본**: 공격 벡터는 지정 된 장치만 공격 원본으로 간주 합니다.

   - **모든 대상 장치**: 공격 벡터는 모든 장치를 공격 대상으로 간주 합니다.

   - **공격 대상**: 공격 벡터는 지정 된 장치만 공격 대상으로 간주 합니다.

   - **장치 제외**: 지정 된 장치가 공격 벡터 시뮬레이션에서 제외 됩니다.

   - **서브넷 제외**: 지정 된 서브넷은 공격 벡터 시뮬레이션에서 제외 됩니다.

3. **시뮬레이션 추가** 를 선택 합니다. 시뮬레이션이 시뮬레이션 목록에 추가 됩니다.

   :::image type="content" source="media/how-to-generate-reports/new-simulation.png" alt-text="새 시뮬레이션을 추가 합니다.":::

4. :::image type="icon" source="media/how-to-generate-reports/edit-a-simulation-icon.png" border="false":::시뮬레이션을 편집 하려면 선택 합니다.

   :::image type="icon" source="media/how-to-generate-reports/delete-simulation-icon.png" border="false":::시뮬레이션을 삭제 하려면 선택 합니다.

   :::image type="icon" source="media/how-to-generate-reports/make-a-favorite-icon.png" border="false":::시뮬레이션을 즐겨찾기로 표시 하려면 선택 합니다.

5. 공격 벡터 목록이 나타나고 벡터 점수 (100), 공격 원본 장치 및 공격 대상 장치가 포함 됩니다. 공격 벡터의 그래픽 표현에 대 한 특정 공격을 선택 합니다.

   :::image type="content" source="media/how-to-generate-reports/sample-attack-vectors.png" alt-text="공격 벡터.":::

## <a name="sensor-data-mining-queries"></a>센서 데이터-마이닝 쿼리

데이터 마이닝 도구를 사용 하면 다양 한 계층에서 네트워크 장치에 대 한 포괄적이 고 세부적인 정보를 생성할 수 있습니다. 예를 들어 다음을 기반으로 쿼리를 만들 수 있습니다.

- 기간

- 인터넷 연결

- 포트

- 프로토콜

- 펌웨어 버전

- 프로그래밍 명령

- 장치 비활성화

필터를 기반으로 보고서를 세부적으로 조정할 수 있습니다. 예를 들어 펌웨어가 업데이트 된 특정 서브넷을 쿼리할 수 있습니다.

:::image type="content" source="media/how-to-generate-reports/active-device-list-v2.png" alt-text="활성 장치 목록입니다.":::

다양 한 도구를 사용 하 여 쿼리를 관리할 수 있습니다. 예를 들어를 내보내고 저장할 수 있습니다.

> [!NOTE]
> 관리자 및 보안 분석가는 데이터 마이닝 옵션에 액세스할 수 있습니다.

### <a name="dynamic-updates"></a>동적 업데이트

사용자가 만든 데이터 마이닝 쿼리는 열 때마다 동적으로 업데이트 됩니다. 예를 들면 다음과 같습니다.

- 6 월 1 일에 장치에서 펌웨어 버전에 대 한 보고서를 만들고 6 월 10 일에 다시 보고서를 열면이 보고서는 6 월 10 일에 정확한 정보로 업데이트 됩니다.

- 6 월 1 일에 지난 30 일 동안 검색 된 새 장치를 검색 하는 보고서를 만들고 6 월 30 일에 보고서를 열면 지난 30 일 동안 결과가 표시 됩니다.

### <a name="data-mining-use-cases"></a>데이터 마이닝 사용 사례

쿼리를 사용 하 여 다양 한 보안 팀의 다양 한 보안 요구 사항을 처리할 수 있습니다.

- **SOC 인시던트 응답**: 즉각적인 인시던트 대응을 처리할 수 있도록 실시간으로 보고서를 생성 합니다. 예를 들어 패치를 요구 하는 장치 목록에 대 한 보고서를 생성 합니다.

- **법적** 고가: 조사 보고서에 대 한 기록 데이터를 기반으로 보고서를 생성 합니다.

- **IT 네트워크 무결성**: 전체적인 네트워크 보안을 개선 하는 데 도움이 되는 보고서를 생성 합니다. 예를 들어 약한 인증 자격 증명이 있는 장치를 나열 하는 보고서를 생성 합니다.

- **표시 유형**: 네트워크의 모든 기본 매개 변수를 볼 수 있는 모든 쿼리 항목을 포함 하는 보고서를 생성 합니다.

### <a name="data-mining-storage"></a>데이터 마이닝 저장소

장치를 삭제 하는 경우를 제외 하 고 데이터 마이닝 정보는 계속 저장 되 고 저장 됩니다. 데이터 마이닝 결과를 안전한 서버에 외부에서 내보내고 저장할 수 있습니다. 또한 센서는 시스템 연속성 및 데이터 유지를 위해 자동 매일 백업을 수행 합니다.

### <a name="data-mining-and-reports"></a>데이터 마이닝 및 보고서 

**보고서** 옵션에서 액세스할 수 있는 일반 보고서는 미리 정의 된 데이터 마이닝 보고서입니다. 이러한 쿼리는 데이터 마이닝에서 사용할 수 있는 동적 쿼리가 아니라 데이터 마이닝 쿼리 결과의 정적 표현입니다.

데이터 마이닝 쿼리 결과는 RO 사용자가 사용할 수 없습니다. RO 사용자가 데이터 마이닝 쿼리에 의해 생성 된 정보에 액세스할 수 있도록 하려는 관리자 및 보안 분석가는 정보를 보고서로 저장 해야 합니다.

### <a name="predefined-queries"></a>미리 정의 된 쿼리

다음 미리 정의 된 쿼리를 사용할 수 있습니다. 이러한 쿼리는 실시간으로 생성 됩니다.

- **Cves**: 지난 24 시간 이내에 알려진 취약점으로 검색 된 장치 목록입니다.

- **제외 된 cves**: 수동으로 제외 된 모든 cves의 목록입니다. VA 보고서 및 공격 벡터에서 더 정확한 결과를 얻으려면 CVEs를 포함 하거나 제외 하 여 수동으로 CVE 목록을 사용자 지정할 수 있습니다.

- **인터넷 작업**: 인터넷에 연결 된 장치입니다.

- **비활성 장치**: 지난 7 일 동안 통신 하지 않은 장치입니다.

- **활성 장치**: 지난 24 시간 이내에 활성화 된 네트워크 장치입니다.

- **원격 액세스**: 원격 세션 프로토콜을 통해 통신 하는 장치입니다.

- **프로그래밍 명령**: 산업용 프로그래밍을 보내는 장치입니다.

이러한 보고서는 **보고서** 화면에서 자동으로 액세스할 수 있으며, 여기서는 RO 사용자 및 다른 사용자가 이러한 보고서를 볼 수 있습니다. RO 사용자는 데이터 마이닝 보고서에 액세스할 수 없습니다.

:::image type="content" source="media/how-to-generate-reports/data-mining-screeshot-v2.png" alt-text="데이터 마이닝 화면입니다.":::

### <a name="create-a-data-mining-report"></a>데이터 마이닝 보고서 만들기

데이터 마이닝 보고서를 만들려면 다음을 수행 합니다.

1. 측면 메뉴에서 **데이터 마이닝** 을 선택 합니다. 미리 정의 된 제안 보고서는 자동으로 표시 됩니다.

 :::image type="content" source="media/how-to-generate-reports/data-mining-screeshot-v2.png" alt-text="왼쪽 창에서 데이터 마이닝을 선택 합니다.":::

2. :::image type="icon" source="media/how-to-generate-reports/plus-icon.png" border="false":::를 선택합니다.

3. **새 보고서** 를 선택 하 고 보고서를 정의 합니다.

   :::image type="content" source="media/how-to-generate-reports/create-new-report-screen.png" alt-text="이 화면을 작성 하 여 새 보고서를 만듭니다.":::

   사용할 수 있는 매개 변수는 다음과 같습니다.

   - 보고서 이름 및 설명을 제공 합니다.

   - 범주에서 다음 중 하나를 선택 합니다.

      - **범주 (모두)** -네트워크의 모든 장치에 대 한 모든 보고서 결과를 표시 합니다.

      - 표준 범주를 선택 하는 **일반** 입니다.

   - 관심 있는 특정 보고서 매개 변수를 선택 합니다.

   - 정렬 순서 (**order by**)를 선택 합니다. 활동 또는 범주를 기준으로 결과를 정렬 합니다.

   - **보고서 페이지에서** 액세스할 수 있는 보고서로 보고서 결과를 저장 하려면 **보고서 페이지에 저장** 을 선택 합니다. 이렇게 하면 RO 사용자가 만든 보고서를 실행할 수 있습니다.

   - 필터 **(추가)** 를 선택 하 여 더 많은 필터를 추가 합니다. 와일드 카드 요청이 지원 됩니다.

   - 장치 그룹 (장치 맵에 정의 됨)을 지정 합니다.

   - IP 주소를 지정 하십시오.

   - 포트를 지정 하십시오.

   - MAC 주소를 지정 하십시오.

4. **저장** 을 선택합니다. **데이터 마이닝** 페이지에 보고서 결과가 열립니다.

:::image type="content" source="media/how-to-generate-reports/data-mining-page.png" alt-text="데이터 마이닝 페이지에 표시 되는 결과를 보고 합니다.":::

### <a name="manage-data-mining-reports"></a>데이터 마이닝 보고서 관리

다음 표에서는 데이터 마이닝에 대 한 관리 옵션을 설명 합니다.

| 아이콘 이미지 | 설명 |
|--|--|
| :::image type="icon" source="media/how-to-generate-reports/edit-a-simulation-icon.png" border="false"::: | 보고서 매개 변수를 편집 합니다. |
| :::image type="icon" source="media/how-to-generate-reports/export-as-pdf-icon.png" border="false"::: | PDF로 내보냅니다. |
| :::image type="icon" source="media/how-to-generate-reports/csv-export-icon.png" border="false"::: |CSV로 내보내기 |
| :::image type="icon" source="media/how-to-generate-reports/information-icon.png" border="false"::: | 마지막으로 수정한 날짜와 같은 추가 정보를 표시 합니다. 이 기능을 사용 하 여 쿼리 결과 스냅숏을 만듭니다. 예를 들어 팀 리더 또는 SOC 분석가의 추가 조사를 위해이 작업을 수행 해야 할 수도 있습니다. |
| :::image type="icon" source="media/how-to-generate-reports/pin-icon.png" border="false"::: | 보고서 **페이지에** 표시 하거나 **보고서** 페이지를 숨깁니다. :::image type="content" source="media/how-to-generate-reports/hide-reports-page.png" alt-text="보고서를 숨기 거 나 표시 합니다."::: |
| :::image type="icon" source="media/how-to-generate-reports/delete-simulation-icon.png" border="false"::: | 보고서를 삭제합니다. |

#### <a name="create-customized-directories"></a>사용자 지정 디렉터리 만들기 

범주에 대 한 디렉터리를 만들어 데이터 마이닝 쿼리에 대 한 광범위 한 정보를 구성할 수 있습니다. 예를 들어 프로토콜 또는 위치에 대 한 디렉터리를 만들 수 있습니다.

새 디렉터리를 만들려면 다음을 수행 합니다.

1. :::image type="icon" source="media/how-to-generate-reports/plus-icon.png" border="false":::새 디렉터리를 추가 하려면 선택 합니다.

2. 새 디렉터리 **를 선택 하 여 새** 디렉터리 양식을 표시 합니다.

3. 새 디렉터리의 이름을로 합니다.

4. 필요한 보고서를 새 디렉터리로 끌어 옵니다. 언제 든 지 보고서를 주 보기로 다시 끌 수 있습니다.

5. 새 디렉터리를 마우스 오른쪽 단추로 클릭 하 여 열거나, 편집 하거나, 삭제 합니다.

#### <a name="create-snapshots-of-report-results"></a>보고서 결과의 스냅숏 만들기

추가 조사를 위해 특정 쿼리 결과를 저장 해야 할 수도 있습니다. 예를 들어 다양 한 보안 팀과 결과를 공유 해야 할 수 있습니다.

스냅숏은 보고서 결과 내에 저장 되며 동적 쿼리를 생성 하지 않습니다.

:::image type="content" source="media/how-to-generate-reports/report-results-report.png" alt-text="스냅숏을.":::

스냅숏을 만들려면 다음을 수행 합니다.

1. 필요한 보고서를 엽니다.

2. 정보 아이콘을 선택 :::image type="icon" source="media/how-to-generate-reports/information-icon.png" border="false"::: 합니다.

3. **새로 만들기** 를 선택 합니다.

4. 스냅숏의 이름을 입력 하 고 **저장** 을 선택 합니다.

:::image type="content" source="media/how-to-generate-reports/take-a-snapshot.png" alt-text="스냅숏을 만듭니다.":::

#### <a name="customize-the-cve-list"></a>CVE 목록 사용자 지정

다음과 같이 수동으로 CVE 목록을 사용자 지정할 수 있습니다.

  - CVEs 포함/제외

  - CVE 점수 변경

CVE 보고서에서 수동 변경을 수행 하려면:

1.  측면 메뉴에서 **데이터 마이닝** 을 선택 합니다.

2. :::image type="icon" source="media/how-to-generate-reports/plus-icon.png" border="false"::: **데이터 마이닝** 창의 왼쪽 위 모서리에서를 선택 합니다. 그런 다음 **새 보고서** 를 선택 합니다.

   :::image type="content" source="media/how-to-generate-reports/create-a-new-report-screen.png" alt-text="새 보고서를 만듭니다.":::

3. 왼쪽 창에서 다음 옵션 중 하나를 선택 합니다.

   - **알려진 취약점**: 두 옵션을 선택 하 고 보고서의 두 테이블에 cves와 제외 된 cves를 포함 하는 결과를 표시 합니다.

   - **Cves**: 모든 cves의 목록을 표시 하려면이 옵션을 선택 합니다.

   - **제외 된 cves**: 제외 된 모든 cves의 목록을 표시 하려면이 옵션을 선택 합니다.

4. **이름** 및 **설명** 정보를 입력 하 고 **저장** 을 선택 합니다. 새 보고서가 **데이터 마이닝** 창에 표시 됩니다.

5. CVEs를 제외 하려면 CVEs에 대 한 데이터 마이닝 보고서를 엽니다. 모든 CVEs의 목록이 표시 됩니다.

   :::image type="content" source="media/how-to-generate-reports/cves.png" alt-text="C V E 보고서.":::

6. 목록에서 항목을 선택할 수 있도록 설정 하려면 :::image type="icon" source="media/how-to-generate-reports/enable-selecting-icon.png" border="false"::: 사용자 지정 하려는 CVEs를 선택 하 고 선택 합니다. **작업** 막대가 아래쪽에 표시 됩니다.

   :::image type="content" source="media/how-to-generate-reports/operations-bar-appears.png" alt-text="데이터 마이닝 작업 표시줄의 스크린샷":::

7. 제외 하려는 CVEs를 선택한 다음 **레코드 삭제** 를 선택 합니다. 선택한 CVEs는 CVEs 목록에 표시 되지 않으며 제외 된 CVEs 목록에 표시 됩니다.

8. CVEs 목록에 제외 된 CVEs를 포함 하려면 제외 된 CVEs에 대 한 보고서를 생성 하 고 해당 목록에서 CVEs 목록에 다시 포함 하려는 항목을 삭제 합니다.

9. 점수를 변경 하려는 CVEs를 선택한 후 **CVE 점수 업데이트** 를 선택 합니다.

   :::image type="content" source="media/how-to-generate-reports/set-new-score-screen.png" alt-text="CVE 점수를 업데이트 합니다.":::

10. 새 점수를 입력 하 고 **확인을** 선택 합니다. 업데이트 된 점수가 선택한 CVEs에 표시 됩니다.

### <a name="reports-based-on-data-mining"></a>데이터 마이닝을 기반으로 하는 보고서

보고서는 데이터 마이닝 쿼리 결과에 의해 생성 된 정보를 반영 합니다. 여기에는 보고서 보기에서 사용할 수 있는 기본 데이터 마이닝 보고서가 포함 됩니다. 또한 관리자 및 보안 분석가는 사용자 지정 데이터 마이닝 쿼리를 생성 하 여 보고서로 저장할 수 있습니다. 이러한 보고서는 RO 사용자도 사용할 수 있습니다.

보고서를 생성 하려면:

1. 측면 메뉴에서 **보고서** 를 선택 합니다.

2. 표시할 필수 보고서를 선택 합니다. **사용자 지정** 또는 **자동 생성 된** 보고서 (예: **프로그래밍 명령** 및 **원격 액세스**)를 선택할 수 있습니다.

3. 화면 오른쪽 위에 있는 아이콘 중 하나를 선택 하 여 보고서를 내보낼 수 있습니다.

   :::image type="icon" source="media/how-to-generate-reports/export-to-pdf-icon.png" border="false"::: PDF 파일로 내보냅니다.

   :::image type="icon" source="media/how-to-generate-reports/export-to-csv-icon.png" border="false"::: CSV 파일로 내보내기

> [!NOTE] 
> RO 사용자는 자신이 만든 보고서만 볼 수 있습니다.

:::image type="content" source="media/how-to-generate-reports/select-a-report-screen.png" alt-text="생성할 보고서를 선택 합니다.":::

:::image type="content" source="media/how-to-generate-reports/remote-access-report.png" alt-text="원격 액세스 보고서가 생성 되었습니다.":::

## <a name="reports-for-sensor-trends-and-statistics"></a>센서 추세 및 통계에 대 한 보고서

위젯 그래프와 원형 차트를 만들어 네트워크 추세와 통계에 대 한 통찰력을 얻을 수 있습니다. 위젯은 사용자 정의 대시보드 아래에서 그룹화 할 수 있습니다.

> [!NOTE]
> 관리자와 보안 분석가만이 섹션의 절차를 수행할 수 있습니다.

대시보드 및 위젯을 보려면 측면 메뉴에서 **추세 & 통계** 를 선택 합니다.

:::image type="content" source="media/how-to-generate-reports/investigation-screenshot.png" alt-text="조사의 스크린샷입니다.":::

대시보드는 다음 유형의 정보를 그래픽으로 설명 하는 위젯로 구성 됩니다.

- 포트당 트래픽
- 채널 대역폭
- 총 대역폭
- 활성 TCP 연결
- 디바이스
  - 새 디바이스
  - 사용 중인 장치
  - 공급 업체별 장치
  - OS 별 장치
  - 연결 되지 않은 장치
- 시간별 연결 삭제
- 유형별 인시던트에 대 한 경고
- 데이터베이스 테이블 액세스
- 프로토콜 분석 위젯
- 이더넷 및 IP 주소:
  - CIP 서비스의 이더넷 및 IP 주소 트래픽
  - CIP 클래스의 이더넷 및 IP 주소 트래픽
  - 명령에의 한 이더넷 및 IP 주소 트래픽
- OPC
  - OPC 상위 관리 작업
  - OPC 상위 i/o 작업
- Siemens S7:
  - Control 함수 별 S7 트래픽
  - Subfunction의 S7 트래픽
- SRTP:
  - 서비스 코드로 트래픽 SRTP
  - 일별 SRTP 오류
- SuiteLink:
  - SuiteLink top 쿼리 태그
  - SuiteLink numeric 태그 동작
- IEC-60870 트래픽 (ASDU 별)
- 함수 별 DNP3 트래픽
- 서비스별 MMS 트래픽
- 함수 별 modbus 트래픽
- OPC-서비스 별 UA 트래픽

> [!NOTE]
>  위젯의 시간은 센서 시간에 따라 설정 됩니다.

## <a name="reports-for-the-on-premises-management-console"></a>온-프레미스 관리 콘솔에 대 한 보고서

온-프레미스 관리 콘솔을 사용 하 여 연결 된 각 센서에 대 한 보고서를 생성할 수 있습니다. 보고서는 수행 되는 센서 데이터 마이닝 쿼리를 기반으로 합니다.

다음 보고서를 생성할 수 있습니다.

- **활성 장치 (지난 24 시간)**: 24 시간 이내에 네트워크 활동을 표시 하는 장치 목록을 표시 합니다.

- **비활성 장치 (최근 7 일)**: 지난 7 일 동안 네트워크 활동을 표시 하지 않는 장치 목록을 표시 합니다.

- **프로그래밍 명령**: 지난 24 시간 내에 프로그래밍 명령을 보낸 장치의 목록을 표시 합니다.

- **원격 액세스**: 지난 24 시간 이내에 원격 소스에 액세스 한 장치 목록을 표시 합니다.

:::image type="content" source="media/how-to-generate-reports/reports-view.png" alt-text="보고서 뷰의 스크린샷":::

온-프레미스 관리 콘솔에서 센서를 선택 하면 해당 센서에 구성 된 모든 사용자 지정 보고서가 보고서 목록에 표시 됩니다. 각 센서에 대해 기본 보고서 또는 해당 센서에 구성 된 사용자 지정 보고서를 생성할 수 있습니다.

보고서를 생성 하려면:

1. 왼쪽 창에서 **보고서** 를 선택 합니다. **보고서** 창이 표시 됩니다.

2. **센서** 드롭다운 목록에서 보고서를 생성할 센서를 선택 합니다.

   :::image type="content" source="media/how-to-generate-reports/sensor-drop-down-list.png" alt-text="센서 보기의 스크린샷":::

3. 오른쪽 드롭다운 목록에서 생성 하려는 보고서를 선택 합니다.

4. 보고서 결과의 PDF를 만들려면를 선택 :::image type="icon" source="media/how-to-generate-reports/pdf-report-icon.png" border="false"::: 합니다.

## <a name="risk-assessment-reports-for-the-on-premises-management-console"></a>온-프레미스 관리 콘솔에 대 한 위험 평가 보고서

온-프레미스 관리 콘솔에 연결 된 각 센서에 대해 위험 평가 보고서를 생성 합니다. 이 보고서는 센서가 모니터링 하는 각 네트워크에 대 한 통찰력을 제공 합니다.

위험 평가 보고서를 사용 하면 전체 네트워크 보안 점수 뿐만 아니라 각 네트워크 장치에 대 한 보안 점수를 생성할 수 있습니다. 전체 점수는 심층 패킷 검사, 동작 모델링 엔진 및 SCADA 특정 상태 시스템 디자인을 기반으로 합니다. 다양 한 범위의 다른 정보를 사용할 수 있습니다. 예를 들면 다음과 같습니다.

- 구성 문제

- 보안 수준별로 우선 순위가 지정 된 장치 취약점

- 네트워크 보안 문제

- 네트워크 작동 문제

- 공격 벡터

- ICS 네트워크에 대 한 연결

- 인터넷 연결

- 산업용 맬웨어 표시기

- 프로토콜 문제

이 보고서는 보안 점수를 개선 하는 데 도움이 되는 완화 권장 사항을 제공 합니다.

- 보안 장치: 90% 이상의 보안 점수가 있는 장치입니다.

- **취약 한 장치**: 70% 미만의 보안 점수가 있는 장치입니다.

- **개선이 필요한 장치**: 70%에서 89% 사이의 보안 점수가 있는 장치입니다.

보고서를 만들려면

1. 측면 메뉴에서 **위험 평가** 를 선택 합니다.

2. **센서 선택** 드롭다운 목록에서 센서를 선택 합니다.

3. **보고서 생성** 을 선택 합니다.

4. **보관 된 보고서** 섹션에서 **다운로드** 를 선택 합니다.

회사 로고를 가져오려면:

- **로고 가져오기** 를 선택 합니다.

:::image type="content" source="media/how-to-generate-reports/import-logo-screenshot.png" alt-text="위험 평가 보기를 통해 로고를 가져옵니다.":::

## <a name="see-also"></a>참조
[사이트 맵 보기 작업](how-to-gain-insight-into-global-regional-and-local-threats.md#work-with-site-map-views)
