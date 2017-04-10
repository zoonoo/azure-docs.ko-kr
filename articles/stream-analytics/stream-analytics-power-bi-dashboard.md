---
title: "Azure Stream Analytics의 Power BI 대시보드 | Microsoft Docs"
description: "실시간 스트리밍 Power BI 대시보드를 사용하여 비즈니스 인텔리전스를 수집하고 Stream Analytics 작업에서 대량의 데이터를 분석합니다."
keywords: "분석 대시보드, 실시간 대시보드"
services: stream-analytics
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: fe8db732-4397-4e58-9313-fec9537aa2ad
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 3dbc49d35def6d7b12ade529d1dd1156dee9d75b
ms.openlocfilehash: 09c54f8cce119c1cbe6a08e969236612447d9e17
ms.lasthandoff: 02/27/2017


---
# <a name="stream-analytics-and-power-bi-a-real-time-analytics-dashboard-for-streaming-data"></a>Stream Analytics 및 Power BI: 스트리밍 데이터에 대한 실시간 분석 대시보드
Azure Stream Analytics를 사용하면 최고의 비즈니스 인텔리전스 도구 중 하나인 Microsoft Power BI를 이용할 수 있습니다. Azure Stream Analytics를 사용하여 대용량, 스트리밍 데이터를 분석하고 실시간 Power BI 분석 대시보드로부터 정보를 얻는 방법에 대해 알아봅니다.

[Microsoft Power BI](https://powerbi.com/) 를 사용하여 라이브 대시보드를 빠르게 만들 수 있습니다. [이 시나리오를 보여주는 비디오를 보세요](https://www.youtube.com/watch?v=SGUpT-a99MA).

이 문서에서는 Azure Stream Analytics 작업에 대한 출력으로 Power BI를 사용하여 사용자 고유의 사용자 지정 비즈니스 인텔리전스 도구를 만드는 방법에 대해 알아봅니다. 실시간 대시보드를 활용하는 방법도 학습합니다.

## <a name="prerequisites"></a>필수 조건
* Microsoft Azure 계정.
* Power BI에 대한 회사 또는 학교 계정.
* [실시간 사기 감지](stream-analytics-real-time-fraud-detection.md) 시나리오의 완료. 읽고 있는 문서는 이제 [실시간 사기 감지](stream-analytics-real-time-fraud-detection.md)에 설명된 워크플로에서 빌드되고 Power BI 스트리밍 데이터 집합 출력을 추가합니다.

## <a name="add-power-bi-output"></a>Power BI 출력 추가
이제 작업에 대한 입력이 존재하며 Power BI에 대한 출력을 정의할 수 있습니다.

1. 작업 대시보드 중간에 있는 **출력** 상자를 선택합니다. 그런 다음 **+ 추가**를 선택하여 출력을 만듭니다.

    ![출력 추가](./media/stream-analytics-power-bi-dashboard/create-pbi-output.png)

2. **출력 별칭**을 제공합니다. 쉽게 참조할 수 있는 모든 출력 별칭을 사용할 수 있습니다. 이 출력 별칭은 작업에 대한 여러 출력을 포함하려는 경우 유용합니다. 이 경우 쿼리에서 이 출력을 참조합니다. 예를 들어, 출력 별칭 값 "StreamAnalyticsRealTimeFraudPBI"를 사용해 보겠습니다.

3. **권한 부여**를 선택합니다.

    ![권한 부여 추가](./media/stream-analytics-power-bi-dashboard/pbi-authorize.png)

4. Azure 자격 증명(회사 또는 학교 계정)을 제공할 수 있는 창이 열립니다. 또한 Azure 작업에 Power BI 영역에 대한 액세스 권한을 제공합니다.

    ![권한 부여 필드](./media/stream-analytics-power-bi-dashboard/authorize-area.png)

5. 권한 부여는 필요한 정보를 제공한 후 사라집니다. **새 출력** 영역에는 **데이터 집합 이름** 및 **테이블 이름**을 위한 필드가 있습니다.

    ![PBI 작업 영역](./media/stream-analytics-power-bi-dashboard/pbi-workspace.png)

6. 다음과 같이 정의합니다.
    * **그룹 영역**: 데이터 집합을 만들려는 Power BI 테넌트에서 작업 영역을 선택합니다.
    * **데이터 집합 이름**: Power BI 출력에 포함할 데이터 집합 이름을 입력합니다. 예를 들어, "StreamAnalyticsRealTimeFraudPBI"를 사용해 보겠습니다.
    * **테이블 이름**: Power BI 출력의 데이터 집합 아래 테이블 이름을 입력합니다. 예를 들어, "StreamAnalyticsRealTimeFraudPBI"를 사용해 보겠습니다. 현재, Stream Analytics 작업의 Power BI 출력에는 하나의 데이터 집합에 하나의 테이블만 있을 수 있습니다.

7. **만들기**를 선택합니다. 이제 출력 구성이 완료됩니다.

> [!WARNING]
> 이 Stream Analytics 작업에서 사용한 것과 동일한 이름의 데이터 집합과 테이블이 Power BI에 있는 경우에는 기존 데이터를 덮어씁니다.
> 또한 이 데이터 집합 및 테이블을 Power BI 계정에 명시적으로 만들지 않는 것이 좋습니다. 이들은 Stream Analytics 작업을 시작하고 작업이 Power BI로 출력을 시작할 때 자동으로 생성됩니다. 작업 쿼리가 결과 생성하지 않으면 데이터 집합 및 테이블은 생성되지 않습니다.
>
>

데이터 집합은 다음과 같은 설정으로 만들어집니다.
* **defaultRetentionPolicy: BasicFIFO**:데이터는 FIFO이고, 최대 200,000개의 행이 있습니다.
* **defaultMode: pushStreaming**: 스트리밍 타일 및 기존의 보고서 기반 시각적 개체(즉, 푸시)를 모두 지원합니다.

지금은 다른 플래그로 데이터 집합을 만들 수 없습니다.

Power BI 데이터 집합에 대한 자세한 내용은 [Power BI REST API](https://msdn.microsoft.com/library/mt203562.aspx)를 참조하세요.


## <a name="write-query"></a>쿼리 작성
작업의 **쿼리** 탭으로 이동합니다. Power BI에서 원하는 쿼리, 출력을 작성합니다. 예를 들어 이는 통신 업계에서 SIM 사기를 적발하는 다음 SQL 쿼리와 유사할 수 있습니다.


```
/* Our criteria for fraud:
 Calls made from the same caller to two phone switches in different locations (for example, Australia and Europe) within five seconds */

 SELECT System.Timestamp AS WindowEnd, COUNT(*) AS FraudulentCalls
 INTO "StreamAnalyticsRealTimeFraudPBI"
 FROM "StreamAnalyticsRealTimeFraudInput" CS1 TIMESTAMP BY CallRecTime
 JOIN "StreamAnalyticsRealTimeFraudInput" CS2 TIMESTAMP BY CallRecTime

/* Where the caller is the same, as indicated by IMSI (International Mobile Subscriber Identity) */
 ON CS1.CallingIMSI = CS2.CallingIMSI

/* ...and date between CS1 and CS2 is between one and five seconds */
 AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5

/* Where the switch location is different */
 WHERE CS1.SwitchNum != CS2.SwitchNum
 GROUP BY TumblingWindow(Duration(second, 1))
```

## <a name="create-the-dashboard-in-power-bi"></a>Power BI에서 대시보드 만들기

1. [Powerbi.com](https://powerbi.com)으로 이동한 다음 회사 또는 학교 계정으로 로그인합니다. Stream Analytics 작업 쿼리가 결과를 출력하면, 다음 그림처럼 데이터 집합이 이미 생성되어 표시됩니다.

    ![스트리밍 데이터 집합](./media/stream-analytics-power-bi-dashboard/streaming-dataset.png)

2. **타일 추가**를 클릭한 다음, 사용자 지정 스트리밍 데이터를 선택합니다.

    ![사용자 지정 스트리밍 데이터 집합](./media/stream-analytics-power-bi-dashboard/custom-streaming-data.png)

3. 목록에서 데이터 집합을 선택합니다.

    ![스트리밍 데이터 집합](./media/stream-analytics-power-bi-dashboard/your-streaming-dataset.png)

4. 시각화 카드를 만듭니다. 그런 다음 **fraudulentcalls** 필드를 선택합니다.

    ![사기 추가](./media/stream-analytics-power-bi-dashboard/add-fraud.png)

    이제 사기 카운터가 나타납니다.

    ![사기 카운터](./media/stream-analytics-power-bi-dashboard/fraud-counter.png)

5. 타일을 추가하는 연습을 다시 살펴보세요. 그러나 이번에는 꺾은선형 차트를 선택합니다. 값으로 **fraudulentcalls**를, 축으로 **windowend**를 추가합니다. 다음 스크린 샷에 표시된 것처럼 지난 10분을 선택합니다.

![사기 호출](./media/stream-analytics-power-bi-dashboard/fraud-calls.png)


이 자습서에서는 데이터 집합에 대해 한 종류의 차트를 만드는 방법을 설명합니다. Power BI는 조직에 대한 다른 고객 비즈니스 인텔리전스 도구를 만드는 데 도움이 됩니다. Power BI 대시보드의 다른 예제는 [Power BI 시작](https://youtu.be/L-Z_6P56aas?t=1m58s) 비디오를 보세요.

Power BI 출력 구성 및 Power BI 그룹 사용 방법에 대한 자세한 내용은 [Power BI 섹션](stream-analytics-define-outputs.md#power-bi)의 [Stream Analytics 출력 이해](stream-analytics-define-outputs.md "Stream Analytics 출력 이해")를 검토하세요. [Power BI의 대시보드](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/)는 또 하나의 유용한 리소스입니다.

## <a name="learn-about-limitations-and-best-practices"></a>제한 사항 및 모범 사례에 대해 알아보기
Power BI는 [Power BI](https://powerbi.microsoft.com/pricing "Power BI 가격 책정")에 설명된 바와 같이 동시성과 처리량 제약 조건을 모두 사용합니다.

현재는 대략 1초당 한 번 Power BI를 호출할 수 있습니다. 스트리밍 시각적 개체는 15KB의 패킷을 지원합니다. 이보다 크면 스트리밍 시각적 개체가 실패합니다(푸시는 계속 작동).

이러한 제한 사항 때문에 Power BI는 Azure Stream Analytics가 데이터 부하를 상당히 줄이는 경우에 가장 적합합니다.
연속 창 또는 도약 창을 사용하여 데이터 푸시가 최대 초당 한번의 푸시를 수행하고 쿼리가 처리량 요구 사항 범위 내에 있도록 하는 것이 좋습니다.

다음 수식을 사용하여 값을 초 단위로 계산하여 창에 제공할 수 있습니다.

![수식 1](./media/stream-analytics-power-bi-dashboard/equation1.png)  

예:
- 1,000대의 장치가 1초 간격으로 데이터를 보내고 있습니다.
- 시간당 1,000,000개의 행을 지원하는 Power BI Pro SKU를 사용하고 있습니다.
- 장치당 평균 데이터 양을 Power BI에 게시하려 합니다.

그 결과, 수식은 다음과 같습니다.

![수식 2](./media/stream-analytics-power-bi-dashboard/equation2.png)  

이는 원래 쿼리를 다음과 같이 변경할 수 있다는 의미합니다.

    SELECT
        MAX(hmdt) AS hmdt,
        MAX(temp) AS temp,
        System.TimeStamp AS time,
        dspl
    INTO
        OutPBI
    FROM
        Input TIMESTAMP BY time
    GROUP BY
        TUMBLINGWINDOW(ss,4),
        dspl


### <a name="renew-authorization"></a>권한 부여 갱신
작업을 만들거나 마지막으로 인증한 후에 암호가 변경된 경우 Power BI 계정을 다시 인증해야 합니다. Azure Multi-Factor Authentication가 Azure Active Directory(Azure AD) 테넌트에서 구성된 경우 2주마다 Power BI 권한 부여도 갱신해야 합니다. 갱신하지 않으면 작업 출력 부족 또는 작업 로그에 "인증 사용자 오류"와 같은 증상을 볼 수 있습니다.

마찬가지로 토큰이 만료된 후 작업이 시작되려고 하는 경우 오류가 발생하고 작업이 실패합니다. 이 문제를 해결하려면 실행 중인 작업을 중지하고 Power BI 출력으로 이동합니다. 데이터 손실을 방지하기 위해 **권한 부여 갱신** 링크를 클릭한 다음 **마지막 중지 시간**부터 작업을 다시 시작합니다.

Power BI를 사용하여 권한 부여가 새로 고쳐지면 권한 부여 영역에 문제가 해결되었음을 나타내는 녹색 알림이 표시됩니다.

## <a name="get-help"></a>도움말 보기
추가 지원이 필요한 경우 [Azure Stream Analytics 포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)을 참조하세요.

## <a name="next-steps"></a>다음 단계
* [Azure Stream Analytics 소개](stream-analytics-introduction.md)
* [Azure Stream Analytics 사용 시작](stream-analytics-get-started.md)
* [Azure 스트림 분석 작업 규모 지정](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics 쿼리 언어 참조](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics 관리 REST API 참조](https://msdn.microsoft.com/library/azure/dn835031.aspx)

