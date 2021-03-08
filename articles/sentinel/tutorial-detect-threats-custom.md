---
title: Azure 센티널을 사용 하 여 위협을 검색 하는 사용자 지정 분석 규칙 만들기 | Microsoft Docs
description: 이 자습서를 사용 하 여 Azure 센티널에서 보안 위협을 검색 하는 사용자 지정 분석 규칙을 만드는 방법을 알아봅니다. 이벤트 그룹화, 경고 그룹화 및 경고 보강을 활용 하 고 자동 사용 안 함을 이해 합니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/10/2021
ms.author: yelevin
ms.openlocfilehash: 6f0a94daef8c5db820a17fe8cb50eda616bcf260
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/08/2021
ms.locfileid: "102453938"
---
# <a name="tutorial-create-custom-analytics-rules-to-detect-threats"></a>자습서: 위협 검색을 위한 사용자 지정 분석 규칙 만들기

[데이터 원본을](quickstart-onboard.md) Azure 센티널에 연결 했으므로 사용자 환경에 있는 위협 및 비정상 동작을 검색 하는 데 도움이 되는 사용자 지정 분석 규칙을 만들 수 있습니다. 이러한 규칙은 사용자 환경에서 특정 이벤트 또는 이벤트 집합을 검색 하 고, 특정 이벤트 임계값 또는 조건에 도달 하면 경고를 생성 하 고, SOC에서 심사 및 조사를 위해 인시던트를 생성 하 고, 자동화 된 추적 및 재구성 프로세스를 통해 위협에 대응 합니다. 

이 자습서에서는 사용자 지정 규칙을 만들어 Azure 센티널에서 위협을 검색 하는 방법을 안내 합니다.

이 자습서를 완료 하면 다음을 수행할 수 있습니다.
> [!div class="checklist"]
> * 분석 규칙 만들기
> * 이벤트 및 경고가 처리 되는 방법 정의
> * 경고 및 인시던트를 생성 하는 방법 정의
> * 규칙에 대 한 자동화 된 위협 응답을 선택 합니다.

## <a name="create-a-custom-analytics-rule-with-a-scheduled-query"></a>예약 된 쿼리를 사용 하 여 사용자 지정 분석 규칙 만들기

1. Azure 센티널 탐색 메뉴에서 **분석** 을 선택 합니다.

1. 위쪽의 작업 모음에서 **+ 만들기** 를 선택 하 고 **예약 된 쿼리 규칙** 을 선택 합니다. 그러면 **분석 규칙 마법사** 가 열립니다.

    :::image type="content" source="media/tutorial-detect-threats-custom/create-scheduled-query-small.png" alt-text="예약 된 쿼리 만들기" lightbox="media/tutorial-detect-threats-custom/create-scheduled-query-full.png":::

### <a name="analytics-rule-wizard---general-tab"></a>분석 규칙 마법사-일반 탭

- 고유한 **이름** 및 **설명을** 제공 합니다. 

- **전술** 필드에서 규칙을 분류 하기 위한 공격 범주 중에서 선택할 수 있습니다. [MITRE at&t&접시](https://attack.mitre.org/) 프레임 워크의 전략을 기반으로 합니다.

- 경고 **심각도** 를 적절 하 게 설정 합니다. 

- 규칙을 만들 때 **상태** 는 기본적으로 **사용 하도록 설정** 되어 있습니다. 즉, 규칙 만들기를 완료 한 후 즉시 실행 됩니다. 즉시 실행 하지 않으려면 **사용 안 함** 을 선택 합니다. 규칙은 **활성 규칙** 탭에 추가 되 고 필요할 때 해당 규칙에서 사용 하도록 설정할 수 있습니다.

   :::image type="content" source="media/tutorial-detect-threats-custom/general-tab.png" alt-text="사용자 지정 분석 규칙 만들기 시작":::

## <a name="define-the-rule-query-logic-and-configure-settings"></a>규칙 쿼리 논리 정의 및 설정 구성

**규칙 논리 설정** 탭에서 **규칙 쿼리** 필드에 직접 쿼리를 작성 하거나 Log Analytics에서 쿼리를 만든 다음 여기에 복사 하 여 붙여 넣을 수 있습니다.

- 쿼리는 Kusto Query Language(KQL)로 작성됩니다. KQL [개념](/azure/data-explorer/kusto/concepts/) 및 [쿼리에](/azure/data-explorer/kusto/query/)대해 자세히 알아보고 편리한 [빠른 참조 가이드](/azure/data-explorer/kql-quick-reference)를 참조 하세요.

- 이 스크린샷에서 표시 된 예제는 *Securityevent* 테이블을 쿼리하여 [실패 한 Windows 로그온 이벤트](/windows/security/threat-protection/auditing/event-4625)의 유형을 표시 합니다.

   :::image type="content" source="media/tutorial-detect-threats-custom/set-rule-logic-tab-1-new.png" alt-text="쿼리 규칙 논리 및 설정 구성" lightbox="media/tutorial-detect-threats-custom/set-rule-logic-tab-all-1-new.png":::

- 다음은 [Azure 활동](../azure-monitor/platform/activity-log.md)에서 비정상 리소스를 만들 때 경고 하는 또 다른 샘플 쿼리입니다.

    ```kusto
    AzureActivity
    | where OperationName == "Create or Update Virtual Machine" or OperationName =="Create Deployment"
    | where ActivityStatus == "Succeeded"
    | make-series dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(ago(7d), now(), 1d) by Caller
    ```

    > [!NOTE]
    > #### <a name="rule-query-best-practices"></a>규칙 쿼리 모범 사례
    > - 쿼리 길이는 1 ~ 007e; 1만 자 여야 하며 " `search *` " 또는 ""를 포함할 수 없습니다 `union *` .
    >
    > - ADX 함수를 사용 하 여 Log Analytics 쿼리 창 안에 Azure 데이터 탐색기 쿼리를 만들 수 **는 없습니다**.
    >
    > - 쿼리에서 함수를 사용 하는 경우 **`bag_unpack`** ""를 사용 하 여 열을 필드로 프로젝션 하 `project field1` 고 해당 열이 존재 하지 않으면 쿼리가 실패 합니다. 이러한 상황을 방지 하려면 열을 다음과 같이 프로젝션 해야 합니다.
    >   - `project field1 = column_ifexists("field1","")`

### <a name="alert-enrichment"></a>경고 보강

> [!IMPORTANT]
> Alert 보강 기능은 현재 **미리 보기로** 제공 됩니다. 베타, 미리 보기 또는 아직 일반 공급으로 출시 되지 않은 Azure 기능에 적용 되는 추가 약관은 [Microsoft Azure 미리 보기에](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 대 한 추가 사용 약관을 참조 하세요.
    
- **엔터티 매핑** 구성 섹션을 사용 하 여 쿼리 결과의 매개 변수를 Azure 센티널에서 인식할 수 있는 엔터티로 매핑할 수 있습니다. 엔터티는 다음에 나오는 조사 프로세스 및 수정 작업의 빌딩 블록으로 사용 되는 필수 정보를 사용 하 여 규칙의 출력 (경고 및 인시던트)을 보강 합니다. **인시던트 설정** 탭에서 경고를 인시던트에 그룹화 할 수 있는 조건 이기도 합니다.

    [Azure 센티널의 엔터티에](entities-in-azure-sentinel.md)대해 자세히 알아보세요.

    [이전 버전과의 호환성](map-data-fields-to-entities.md#notes-on-the-new-version)에 대 한 중요 한 정보와 함께 전체 엔터티 매핑 지침은 [Azure 센티널의 엔터티에 데이터 필드 매핑](map-data-fields-to-entities.md) 을 참조 하세요.

- **사용자 지정 세부 정보** 구성 섹션을 사용 하 여 쿼리에서 이벤트 데이터 항목을 추출 하 고이 규칙에 의해 생성 된 경고에 표시 하 여 경고 및 인시던트에 즉각적인 이벤트 콘텐츠를 표시 합니다.

    경고에 표시 되는 사용자 지정 세부 정보에 대해 자세히 알아보고 [전체 지침](surface-custom-details-in-alerts.md)을 확인 하세요.

### <a name="query-scheduling-and-alert-threshold"></a>쿼리 일정 및 경고 임계값

- **쿼리 일정** 섹션에서 다음 매개 변수를 설정 합니다.

   :::image type="content" source="media/tutorial-detect-threats-custom/set-rule-logic-tab-2.png" alt-text="쿼리 일정 및 이벤트 그룹화 설정" lightbox="media/tutorial-detect-threats-custom/set-rule-logic-tab-all-2-new.png":::

    - 쿼리가 실행 되는 빈도 (5 분 마다 또는 14 일 마다 한 번)를 제어 하려면 **매번 쿼리 실행** 을 설정 합니다.

    - **마지막에서 조회 데이터** 를 설정 하 여 쿼리가 적용 되는 데이터의 기간을 결정 합니다. 예를 들어 지난 10 분의 데이터 또는 지난 6 시간의 데이터를 쿼리할 수 있습니다. 최대값은 14 일입니다.

        > [!NOTE]
        > **쿼리 간격 및 lookback 기간**
        >
        >  이러한 두 설정은 한 지점까지 서로 독립적입니다. 간격 보다 긴 시간 동안 쿼리를 실행할 수 있지만 (겹치는 쿼리를 포함 하는 경우), 검사 기간을 초과 하는 간격으로 쿼리를 실행할 수 없습니다. 그렇지 않으면 전체 쿼리 범위에서 간격이 발생할 수 있습니다.
        >
        > **수집 지연**
        >
        > 원본에서 이벤트를 생성 하 고 Azure 센티널에 수집 하는 동안 발생할 수 있는 **대기 시간** 을 고려 하 여 데이터 중복 없이 전체 검사를 보장 하기 위해 azure 센티널은 예약 된 시간부터 **5 분 지연** 시간에 예약 된 분석 규칙을 실행 합니다.
        >
        > 이러한 지연이 필요 하 고이 문제를 해결 하는 방법에 대 한 자세한 기술 설명은 Ron Marsiano 's 's 's 's 's 's 's 's 's 's 's 's 's the[Azure 센티널의 수집 지연 처리 경고 규칙](https://techcommunity.microsoft.com/t5/azure-sentinel/handling-ingestion-delay-in-azure-sentinel-scheduled-alert-rules/ba-p/2052851)을 참조 하세요.

- **경고 임계값** 섹션을 사용 하 여 규칙의 민감도 수준을 정의 합니다. 예를 들어 쿼리가 실행 될 때마다 반환 되는 결과 수를 1000 반환 하 **는 경우에** 만 경고를 생성 하는 경우 1000에만 경고를 생성 하는 경우에 **경고 생성** 을 설정 합니다. 이 필드는 필수 필드 이므로 임계값을 설정 하지 않으려는 경우 (즉, 경고를 통해 모든 이벤트를 등록 하려면 number 필드에 0을 입력 합니다.
    
### <a name="results-simulation"></a>결과 시뮬레이션

**결과 시뮬레이션** 영역에서 마법사의 오른쪽에 있는 **현재 데이터를 사용 하 여 테스트** 를 선택 하면 Azure 센티널에는 현재 정의 된 일정에 따라 실행 된 최근 50 시간 동안 쿼리가 생성 된 결과 (로그 이벤트) 그래프가 표시 됩니다. 쿼리를 수정 하는 경우 **현재 데이터로 테스트** 를 다시 선택 하 여 그래프를 업데이트 합니다. 그래프는 **쿼리 일정** 섹션의 설정에 따라 결정 되는 정의 된 기간 동안의 결과 수를 보여 줍니다.
  
위의 스크린샷에 나온 쿼리 결과는 다음과 같습니다. 왼쪽은 기본 뷰입니다. 오른쪽은 그래프의 특정 시점을 마우스로 가리킬 때 표시 되는 것입니다.

:::image type="content" source="media/tutorial-detect-threats-custom/results-simulation.png" alt-text="결과 시뮬레이션 스크린샷":::

쿼리에서 너무 많거나 너무 자주 발생 하는 경고를 트리거하는 경우 **쿼리 일정** 및 **경고 임계값** [섹션](#query-scheduling-and-alert-threshold) 의 설정을 시험해 보고 **현재 데이터로 테스트** 를 다시 선택할 수 있습니다.

### <a name="event-grouping-and-rule-suppression"></a>이벤트 그룹화 및 규칙 억제

> [!IMPORTANT]
> 이벤트 그룹화는 현재 **미리 보기** 상태입니다. 베타, 미리 보기 또는 아직 일반 공급으로 출시 되지 않은 Azure 기능에 적용 되는 추가 약관은 [Microsoft Azure 미리 보기에](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 대 한 추가 사용 약관을 참조 하세요.
    
- **이벤트 그룹화** 에서 다음 두 가지 방법 중 하나를 선택 하 여 **이벤트** 그룹화를 **경고** 로 처리 합니다. 

    - **모든 이벤트를 단일 경고** (기본 설정)로 그룹화 합니다. 이 규칙은 실행 될 때마다 단일 경고를 생성 하 고, 쿼리가 지정 된 **경고 임계값** 보다 많은 결과를 반환 하는 경우에 한 합니다. 경고에는 결과에 반환 된 모든 이벤트에 대 한 요약이 포함 됩니다. 

    - **각 이벤트에 대해 경고 트리거**. 규칙은 쿼리에서 반환 된 각 이벤트에 대해 고유한 경고를 생성 합니다. 이 방법은 이벤트를 개별적으로 표시 하거나 사용자, 호스트 이름 또는 다른 특정 매개 변수를 기준으로 이벤트를 그룹화 하려는 경우에 유용 합니다. 쿼리에서 이러한 매개 변수를 정의할 수 있습니다.
    
        현재 규칙에서 생성할 수 있는 경고 수는 20개입니다. 특정 규칙에서 **이벤트 그룹화** 가 **각 이벤트에 대 한 경고를 트리거하기** 로 설정 되 고, 규칙의 쿼리에서 20 개 이상의 이벤트를 반환 하는 경우, 처음 19 개의 이벤트는 각각 고유한 경고를 생성 하 고 20 경고는 전체 반환 이벤트 집합을 요약 합니다. 즉, 20 경고는 **모든 이벤트를 단일 경고로 그룹화** 옵션에서 생성 된 것입니다.

    > [!NOTE]
    > **이벤트** 와 **경고** 의 차이점은 무엇 인가요?
    >
    > - **이벤트** 는 단일 작업 발생에 대 한 설명입니다. 예를 들어 로그 파일의 단일 항목은 이벤트로 계산 될 수 있습니다. 이 컨텍스트에서 이벤트는 분석 규칙의 쿼리에서 반환 되는 단일 결과를 나타냅니다.
    >
    > - **경고** 는 함께 수행 되는 이벤트 모음으로, 보안 관점에서 중요 합니다. 예를 들어, 이벤트의 보안 영향이 중요 한 경우 (예: 사무실 시간 외의 외부 국가에서 관리 로그인) 단일 이벤트가 경고에 포함 될 수 있습니다.
    >
    > - 이 방법으로 **인시던트** 는 무엇 인가요? Azure 센티널의 내부 논리는 **경고 또는 경고** 그룹에서 **인시던트** 를 만듭니다. 인시던트 큐는 SOC 분석가의 작업 심사, 조사 및 수정의 초점입니다.
    > 
    > Azure 센티널은 일부 데이터 원본에서 원시 이벤트를 수집 다른 사람의 경고를 이미 처리 했습니다. 언제 든 지 처리 하는 항목을 확인 하는 것이 중요 합니다.

- 경고를 받은 후 쿼리 간격을 초과 하는 기간 동안이 규칙의 작업을 일시 중단 하려는 **경우에는** **억제** 섹션에서 **경고 생성 후 쿼리 실행 중지** 설정을 켤 수 있습니다. 이 옵션을 설정 하는 경우 실행 중지 **쿼리** 를 쿼리 실행을 중지 해야 하는 시간 (최대 24 시간)으로 설정 해야 합니다.

## <a name="configure-the-incident-creation-settings"></a>인시던트 만들기 설정 구성

**인시던트 설정** 탭에서 Azure 센티널이 경고를 조치 가능한 인시던트에 설정 하는지 여부와 방법을 선택할 수 있습니다. 이 탭만 있으면 Azure 센티널은 각 및 경고에서 별도의 단일 인시던트를 만듭니다. 이 탭의 설정을 변경 하 여 인시던트를 만들지 않거나 여러 경고를 단일 인시던트에 그룹화 하도록 선택할 수 있습니다.

> [!IMPORTANT]
> 인시던트 설정 탭은 현재 **미리 보기** 상태입니다. 베타, 미리 보기 또는 아직 일반 공급으로 출시 되지 않은 Azure 기능에 적용 되는 추가 약관은 [Microsoft Azure 미리 보기에](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 대 한 추가 사용 약관을 참조 하세요.

:::image type="content" source="media/tutorial-detect-threats-custom/incident-settings-tab.png" alt-text="인시던트 만들기 및 경고 그룹화 설정 정의":::

### <a name="incident-settings"></a>인시던트 설정

**인시던트 설정** 섹션에서 **이 분석 규칙에 의해 트리거된 경고에서 인시던트 만들기** 가 기본적으로 **사용** 으로 설정 됩니다. 즉, Azure 센티널은 규칙에 의해 트리거되는 각 경고와 개별 인시던트 하나를 만듭니다.
    
- 이 규칙을 사용 하 여 인시던트가 생성 되는 것을 원하지 않는 경우 (예:이 규칙이 후속 분석에 대 한 정보를 수집 하는 경우) **사용 안 함** 으로 설정 합니다.

- 단일 경고에 대 한 하나의 인시던트 대신 경고 그룹에서 단일 인시던트를 만들려는 경우 다음 섹션을 참조 하세요.

### <a name="alert-grouping"></a>경고 그룹화

**경고 그룹화** 섹션에서 유사 하거나 반복 되는 경고 150의 그룹에서 단일 인시던트를 생성 하려는 경우 (참고 참조), **이 분석 규칙에 의해 트리거된 그룹 관련 경고** 를 설정 된 인시던트에 **설정 하** 고 다음 매개 변수를 설정 합니다.

- **선택한 시간 프레임 내에 생성 된 경고로 그룹 제한**: 비슷하거나 반복 되는 경고를 그룹화 할 시간 프레임을 결정 합니다. 이 시간 프레임 내에 해당 하는 모든 경고는 아래 그룹화 설정에 따라 인시던트 또는 인시던트 집합을 전체적으로 생성 합니다. 이 시간 프레임 이외의 경고는 별도의 인시던트 또는 인시던트 집합을 생성 합니다.

- **이 분석 규칙에 의해 트리거되는 경고를 단일 인시던트** 로 그룹화 합니다. 경고를 그룹화 할 기준을 선택 합니다.

    - **모든 엔터티가 일치 하는 경우 경고를 단일 인시던트에 그룹화** 합니다. 위의 규칙 논리 설정 탭에 정의 된 매핑된 각 엔터티에 대해 동일한 값을 공유 하는 경우 경고를 그룹화 합니다. 권장 설정입니다.

    - **이 규칙에 의해 트리거되는 모든 경고를 단일 인시던트에 그룹화** 합니다 .이 규칙에 의해 생성 된 모든 경고는 동일한 값을 공유 하지 않더라도 함께 그룹화 됩니다.

    - **선택한 엔터티가 일치 하는 경우 경고를 단일 인시던트에 그룹화** 합니다. 매핑된 엔터티 중 일부에 대해 동일한 값을 공유 하는 경우 (드롭다운 목록에서 선택할 수 있음) 경고를 하나로 그룹화 합니다. 예를 들어 원본 또는 대상 IP 주소를 기반으로 별도의 인시던트를 만들려는 경우이 설정을 사용 하는 것이 좋습니다.

- **닫힌 일치 인시던트 다시 열기**: 인시던트가 해결 되 고 종결 된 후 나중에 해당 인시던트에 속해야 하는 다른 경고가 생성 되 면이 설정을 **사용** 으로 설정 하 고, 종료 된 인시던트를 다시 열고, 경고를 통해 새 인시던트를 만들도록 설정 **된 상태를** 유지 합니다.
    
    > [!NOTE]
    > **최대 150 경고** 는 단일 인시던트로 그룹화 할 수 있습니다. 단일 인시던트로 그룹화 하는 규칙에 의해 150 개 이상의 경고가 생성 되 면 원래 인시던트와 동일한 인시던트 세부 정보를 사용 하 여 새 인시던트가 생성 되 고 초과 경고가 새 인시던트에 그룹화 됩니다.

## <a name="set-automated-responses-and-create-the-rule"></a>자동화 된 응답 설정 및 규칙 만들기

1. 자동화 된 **응답** 탭에서 사용자 지정 규칙에 의해 경고가 생성 될 때 자동으로 실행 하려는 플레이 북를 선택 합니다. 플레이 북을 만들고 자동화 하는 방법에 대 한 자세한 내용은 [위협에 대응](tutorial-respond-threats-playbook.md)을 참조 하세요.

    :::image type="content" source="media/tutorial-detect-threats-custom/automated-response-tab.png" alt-text="자동화 된 응답 설정 정의":::

1. **검토 및 만들기** 를 선택 하 여 새 경고 규칙에 대 한 모든 설정을 검토 합니다. "유효성 검사를 통과 했습니다." 라는 메시지가 표시 되 면 **만들기** 를 선택 하 여 경고 규칙을 초기화 합니다.

    :::image type="content" source="media/tutorial-detect-threats-custom/review-and-create-tab.png" alt-text="모든 설정 검토 및 규칙 만들기":::

## <a name="view-the-rule-and-its-output"></a>규칙 및 해당 출력 보기
  
- 새로 만든 사용자 지정 규칙 ("예약 된" 형식)은 기본 **분석** 화면의 **활성 규칙** 탭에 있는 테이블에서 찾을 수 있습니다. 이 목록에서 각 규칙을 사용 하거나 사용 하지 않도록 설정 하거나 삭제할 수 있습니다.

- 만든 경고 규칙의 결과를 보려면 **인시던트 페이지로 이동 하 여** 인시던트를 조사 하 고, [인시던트를 조사](tutorial-investigate-cases.md)하 고, 위협을 수정할 수 있습니다.

> [!NOTE]
> Azure 센티널에서 생성 된 경고는 [Microsoft Graph 보안](/graph/security-concept-overview)을 통해 사용할 수 있습니다. 자세한 내용은 [Microsoft Graph 보안 경고 설명서](/graph/api/resources/security-api-overview)를 참조 하세요.

## <a name="troubleshooting"></a>문제 해결

### <a name="issue-no-events-appear-in-query-results"></a>문제: 쿼리 결과에 이벤트가 표시 되지 않습니다.

**이벤트 그룹화** 가 **각 이벤트에 대 한 경고를 트리거하기** 로 설정 된 경우 특정 시나리오에서 나중에 쿼리 결과를 볼 때 (예: 인시던트의 경고를 다시 피벗 하는 경우) 쿼리 결과가 나타나지 않을 수 있습니다. 경고에 대 한 이벤트의 연결은 특정 이벤트 정보를 해시 하 고 쿼리에 해시를 포함 하 여 수행 되기 때문입니다. 경고가 생성 된 후 쿼리 결과가 변경 된 경우 해시가 더 이상 유효 하지 않으며 결과가 표시 되지 않습니다. 

이벤트를 보려면 규칙의 쿼리에서 해시가 포함 된 줄을 수동으로 제거 하 고 쿼리를 실행 합니다.

### <a name="issue-a-scheduled-rule-failed-to-execute-or-appears-with-auto-disabled-added-to-the-name"></a>문제: 예약 된 규칙을 실행 하지 못했거나 이름에 자동 사용 안 함이 추가 된 상태로 표시 됩니다.

예약 된 쿼리 규칙이 실행 되지 않지만 발생할 수 있는 드문 경우입니다. Azure 센티널은 실패의 특정 유형과 그 원인이 되는 상황을 기반으로 하 여 일시적 또는 영구적으로 오류를 분류 합니다.

#### <a name="transient-failure"></a>일시적인 오류

일시적인 오류는 일시적인 상황으로 인해 발생 합니다 .이는 곧 정상적으로 돌아가서 규칙 실행이 성공 합니다. Azure 센티널에서 임시로 분류 하는 오류에 대 한 몇 가지 예는 다음과 같습니다.

- 규칙 쿼리를 실행 하는 데 시간이 너무 오래 걸리고 시간이 초과 되었습니다.
- 데이터 원본 및 Log Analytics 간의 연결 문제 또는 Log Analytics와 Azure 센티널 사이
- 다른 모든 신규 및 알 수 없는 오류는 일시적인 것으로 간주 됩니다.

일시적인 오류가 발생 하는 경우 Azure 센티널은 미리 결정 된 후 계속 해 서 규칙을 다시 실행 하려고 합니다. 그런 다음이 규칙은 다음에 예약 된 시간에만 다시 실행 됩니다. 일시적인 오류로 인해 규칙이 자동으로 사용 하지 않도록 설정 되지 않습니다.

#### <a name="permanent-failure---rule-auto-disabled"></a>영구 오류-규칙 자동 사용 안 함

규칙 실행을 허용 하는 조건의 변경으로 인해 영구적인 오류가 발생 하며,이는 사용자의 개입 없이 이전 상태로 돌아오지 않습니다. 영구 분류 된 오류의 몇 가지 예는 다음과 같습니다.

- 규칙 쿼리가 작동 하는 대상 작업 영역이 삭제 되었습니다.
- 규칙 쿼리가 작동 하는 대상 테이블은 삭제 되었습니다.
- Azure 센티널이 대상 작업 영역에서 제거 되었습니다.
- 규칙 쿼리에서 사용 되는 함수가 더 이상 유효 하지 않습니다. 수정 되었거나 제거 되었습니다.
- 규칙 쿼리의 데이터 원본 중 하나에 대 한 권한이 변경 되었습니다.
- 규칙 쿼리의 데이터 원본 중 하나가 삭제 되거나 연결 해제 되었습니다.

동일한 **유형 및 동일한 규칙의 연속 영구 오류 수를 미리 결정** 한 경우 Azure 센티널은 규칙의 실행을 중지 하 고 다음 단계를 수행 합니다.

- 규칙을 사용 하지 않도록 설정 합니다.
- 규칙 이름의 시작 부분에 **"자동 사용 안 함"** 이라는 단어를 추가 합니다.
- 오류의 원인 (및 비활성화)을 규칙의 설명에 추가 합니다.

규칙 목록을 이름별로 정렬 하 여 자동 사용 하지 않도록 설정 된 규칙이 있는지 쉽게 확인할 수 있습니다. 자동 사용 안 함 규칙은 목록의 맨 위 또는 맨 위에 표시 됩니다.

SOC 관리자는 자동 사용 하지 않도록 설정 된 규칙이 있는지 정기적으로 규칙 목록을 확인 해야 합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure 센티널을 사용 하 여 위협 감지를 시작 하는 방법을 배웠습니다.

- [Azure 센티널에서 인시던트를 조사](tutorial-investigate-cases.md)하는 방법에 대해 알아봅니다.
- [Azure 센티널의 엔터티에](entities-in-azure-sentinel.md)대해 알아봅니다.
- [Azure 센티널에서 자동화 된 위협 응답을 설정](tutorial-respond-threats-playbook.md)하는 방법에 대해 알아봅니다.
