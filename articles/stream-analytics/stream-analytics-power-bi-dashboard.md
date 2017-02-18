---
title: "Stream Analytics의 Power BI 대시보드 | Microsoft Docs"
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
ms.date: 02/01/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: c60d620d9fc91cfef2340b645e15ed19d3d1f4e1
ms.openlocfilehash: 39b2852c39e8d3eb4687cbcfd9116e1d66fc9d9d


---
# <a name="stream-analytics--power-bi-a-real-time-analytics-dashboard-for-streaming-data"></a>Stream Analytics 및 Power BI: 스트리밍 데이터에 대한 실시간 분석 대시보드
Azure Stream Analytics을 사용하면 최고의 비즈니스 인텔리전스 도구 중 하나인 Microsoft Power BI를 이용할 수 있습니다. Azure Stream Analytics을 사용하여 대용량, 스트리밍 데이터를 분석하고 실시간 Power BI 분석 대시보드에서 이해를 넓히는 방법에 대해 알아봅니다.

[Microsoft Power BI](https://powerbi.com/) 를 사용하여 라이브 대시보드를 빠르게 만들 수 있습니다. [시나리오를 설명하는 동영상을 시청합니다](https://www.youtube.com/watch?v=SGUpT-a99MA).

이 문서에서는 Azure Stream Analytics 작업에 대한 출력으로 Power BI를 사용하여 사용자 고유의 사용자 지정 비즈니스 인텔리전스 도구를 만들고 실시간 대시보드를 활용하는 방법에 대해 알아봅니다.

## <a name="prerequisites"></a>필수 조건
* Microsoft Azure 계정
* Power BI에 대한 회사 또는 학교 계정
* [실시간 사기 감지](stream-analytics-real-time-fraud-detection.md) 시작 시나리오를 완료합니다. 이 문서는 해당 워크플로를 기반으로 하며 Power BI 스트리밍 데이터 집합 출력이 추가되었습니다.

## <a name="add-power-bi-output"></a>Power BI 출력 추가
이제 작업에 대한 입력이 존재하며 Power BI에 대한 출력을 정의할 수 있습니다. 작업 대시보드 **출력** 중간에 있는 상자를 선택합니다. 그런 다음 친숙한 **+ 추가** 단추를 클릭하고 출력을 만듭니다.

![출력 추가](./media/stream-analytics-power-bi-dashboard/create-pbi-output.png)

**출력 별칭** 입력 – 쉽게 참조할 수 있는 모든 출력 별칭을 입력할 수 있습니다. 이 출력 별칭은 작업에 대한 여러 출력을 포함하려는 경우에 특히 유용합니다. 이 경우 쿼리에서 이 출력을 참조해야 합니다. 예를 들어, 출력 별칭 값 = "StreamAnalyticsRealTimeFraudPBI"를 사용해 보겠습니다.

그런 다음 **권한 부여** 단추를 클릭합니다.

![권한 부여 추가](./media/stream-analytics-power-bi-dashboard/pbi-authorize.png)

Azure 자격 증명(회사 또는 학교 계정)을 입력하라는 창이 표시되면 Power BI 영역에 대한 Azure 작업 액세스를 제공합니다.

![권한 부여 필드](./media/stream-analytics-power-bi-dashboard/authorize-area.png)

조건이 충족되면 권한 부여 창이 사라지고 새 출력 영역에 데이터 집합 이름 및 테이블 이름에 대한 필드가 포함됩니다.

![pbi 작업 영역](./media/stream-analytics-power-bi-dashboard/pbi-workspace.png)

다음과 같이 정의합니다.
* **그룹 영역** – 데이터 집합을 만들려고 하는 Power BI 테넌트에서 작업 영역을 선택합니다.
* **데이터 집합 이름** - Power BI 출력에 포함할 데이터 집합 이름을 입력합니다. 예를 들어, "StreamAnalyticsRealTimeFraudPBI"를 사용해 보겠습니다.
* **테이블 이름** -Power BI 출력의 데이터 집합 아래 테이블 이름을 입력합니다. 여기서는 "StreamAnalyticsRealTimeFraudPBI"라고 하겠습니다. 현재, Stream Analytics 작업의 Power BI 출력에는 하나의 데이터 집합에 하나의 테이블만 있을 수 있습니다.

**만들기**를 클릭하면 이제 출력 구성이 완료됩니다.

> [!WARNING]
> 이 Stream Analytics 작업에서 제공한 이름과 동일한 이름의 데이터 집합과 테이블이 Power BI에 이미 있는 경우에는 기존 데이터를 덮어씁니다.
> 또한 이 데이터 집합 및 테이블을 Power BI 계정에서 명시적으로 만들어서는 안 됩니다. 그것들은 Stream Analytics 작업을 시작해 Power BI로 출력을 쏟아붓기 시작할 때 자동으로 생성됩니다. 작업 쿼리가 결과 생성하지 않으면 데이터 집합 및 테이블은 생성되지 않은 것입니다.
> 
> 

데이터 집합이 다음과 같은 설정 집합으로 만들어집니다.
* defaultRetentionPolicy: BasicFIFO - 데이터는 FIFO이고, 최대 행은 200k임
* defaultMode: pushStreaming: 스트리밍 타일 및 기존의 보고서 기반 시각적 개체(즉, 푸시)를 모두 지원
* 다른 플래그를 이용한 데이터 집합 생성은 현재 지원되지 않음

Power BI 데이터 집합에 대한 자세한 내용은 [Power BI REST API](https://msdn.microsoft.com/library/mt203562.aspx) 참조를 살펴보세요.


## <a name="write-query"></a>쿼리 작성
작업의 **쿼리** 탭으로 이동합니다. Power BI에서 원하는 쿼리, 출력을 작성합니다. 예를 들어 통신 업계에서 SIM 사기를 catch하는 다음 SQL 쿼리 등을 작성할 수 있습니다.


```
/* Our criteria for fraud:
 Calls made from the same caller to two phone switches in different locations (e.g. Australia and Europe) within 5 seconds) */

 SELECT System.Timestamp AS WindowEnd, COUNT(*) AS FraudulentCalls
 INTO "StreamAnalyticsRealTimeFraudPBI"
 FROM "StreamAnalyticsRealTimeFraudInput" CS1 TIMESTAMP BY CallRecTime
 JOIN "StreamAnalyticsRealTimeFraudInput" CS2 TIMESTAMP BY CallRecTime
   
/* Where the caller is the same, as indicated by IMSI (International Mobile Subscriber Identity) */
 ON CS1.CallingIMSI = CS2.CallingIMSI

/* ...and date between CS1 and CS2 is between 1 and 5 seconds */
 AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5

/* Where the switch location is different */
 WHERE CS1.SwitchNum != CS2.SwitchNum
 GROUP BY TumblingWindow(Duration(second, 1))
```

## <a name="create-the-dashboard-in-power-bi"></a>Power BI에서 대시보드 만들기

[Powerbi.com](https://powerbi.com) 으로 이동하고 회사 또는 학교 계정으로 로그인합니다. Stream Analytics 작업 쿼리가 결과를 출력하면, 사용자 데이터 집합이 이미 생성되어 표시됩니다.

![스트리밍 데이터 집합](./media/stream-analytics-power-bi-dashboard/streaming-dataset.png)

타일 추가를 클릭하고 사용자 지정 스트리밍 데이터를 선택합니다.

![사용자 지정 스트리밍 데이터 집합](./media/stream-analytics-power-bi-dashboard/custom-streaming-data.png)

그런 다음 목록에서 데이터 집합을 선택합니다.

![스트리밍 데이터 집합](./media/stream-analytics-power-bi-dashboard/your-streaming-dataset.png)

이제 시각화 카드를 만들고 fraudulentcalls 필드를 선택합니다.

![사기 추가](./media/stream-analytics-power-bi-dashboard/add-fraud.png)

이제 사기 카운터가 나타납니다.

![사기 카운터](./media/stream-analytics-power-bi-dashboard/fraud-counter.png)

타일을 추가하는 연습을 다시 살펴보지만 여기서는 꺾은선형 차트를 선택합니다. 값은 fraudulentcalls를, 축은 windowend를 추가합니다. 지난 10분을 선택했습니다.

![사기 호출](./media/stream-analytics-power-bi-dashboard/fraud-calls.png)


이 자습서에서는 데이터 집합에 대한 한 종류의 차트를 만드는 방법에 대해 설명합니다. Power BI는 조직에 대한 다른 고객 비즈니스 인텔리전스 도구를 만드는 데 도움이 됩니다. Power BI 대시보드의 다른 예제는 [Power BI 시작](https://youtu.be/L-Z_6P56aas?t=1m58s) 비디오를 보세요.

Power BI 출력 구성 및 Power BI 그룹 사용 방법에 대한 자세한 내용은 [Power BI 섹션](stream-analytics-define-outputs.md#power-bi)의 [Stream Analytics 출력 이해]와 (stream-analytics-define-outputs.md "Stream Analytics 출력 이해")를 검토하세요. Power BI를 사용한 대시보드 만들기에 대해 알 수 있는 다른 유용한 리소스는 [Power BI의 대시보드](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/)입니다.

## <a name="limitations-and-best-practices"></a>제한 사항 및 모범 사례
Power BI는 [https://powerbi.microsoft.com/pricing](https://powerbi.microsoft.com/pricing "Power BI 가격 책정")

현재는, 대략&1;초당 한 번 Power BI를 호출할 수 있습니다. 스트리밍 시각적 개체는 15kb 크기의 패킷을 지원합니다. 이 범위를 벗어나는 스트리밍 시각적 개체는 실패합니다(하지만 푸시는 계속 작동).

때문에 Power BI는 Azure Stream Analytics으로 상당한 데이터 부하 감소가 이루어 지는 경우에 가장 적합합니다.
TumblingWindow 또는 HoppingWindow를 사용하여 데이터 푸시가 최대 1초마다 푸시되도록 하고 쿼리가 처리량 요구 사항 이내에 들도록 하는 것이 좋습니다. 다음 수식을 사용하여 수 초 내에 창에 공급할 값을 계산할 수 있습니다.

![equation1](./media/stream-analytics-power-bi-dashboard/equation1.png)  

예를 들어 매초마다 데이터를 보내는 1,000개의 장치가 있다면 시간당 1,000,000행을 지원하는 Power BI Pro SKU를 사용하면서 Power BI에서 장치별 평균 데이터를 확인하고 아래 표시된 것과 같이 장치별로 최대 4초마다 푸시를 수행할 수 있습니다.  

![equation2](./media/stream-analytics-power-bi-dashboard/equation2.png)  

이를 위해 원래 쿼리가 아래와 같이 변경됩니다.

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
작업을 만들거나 마지막으로 인증한 후에 암호가 변경된 경우에 Power BI 계정을 다시 인증해야 합니다. MFA(Multi-Factor Authentication)가 AAD(Azure Active Directory) 테넌트에 구성된 경우 2주마다 Power BI 권한 부여도 갱신해야 합니다. 이 문제의 증상은 작업 출력이 없으며 작업 로그에 "사용자 인증 오류"가 표시됩니다.

마찬가지로 토큰이 만료된 동안 작업이 시작되려고 하는 경우 오류가 발생하고 작업이 실패합니다. 이 문제를 해결하려면 실행 중인 작업을 중지하고 Power BI 출력으로 이동합니다.  "권한 부여 갱신" 링크를 클릭하고 마지막 중지 시간부터 작업을 다시 시작하여 데이터 손실을 방지합니다. Power BI를 사용하여 권한 부여가 새로 고쳐지면 권한 부여 영역에서 문제가 해결되었음을 나타내는 녹색 경고가 표시됩니다.

## <a name="get-help"></a>도움말 보기
추가 지원이 필요할 경우 [Azure 스트림 분석 포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>다음 단계
* [Azure Stream Analytics 소개](stream-analytics-introduction.md)
* [Azure Stream Analytics 사용 시작](stream-analytics-get-started.md)
* [Azure 스트림 분석 작업 규모 지정](stream-analytics-scale-jobs.md)
* [Azure 스트림 분석 쿼리 언어 참조](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics 관리 REST API 참조](https://msdn.microsoft.com/library/azure/dn835031.aspx)




<!--HONumber=Feb17_HO1-->


