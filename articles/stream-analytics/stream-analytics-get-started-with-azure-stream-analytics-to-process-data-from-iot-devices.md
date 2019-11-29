---
title: Azure Stream Analytics를 사용 하 여 실시간 IoT 데이터 스트림 처리
description: 스트림 분석 및 실시간 데이터 처리와 IoT 센서 태그 및 데이터 스트림
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 11/26/2019
ms.openlocfilehash: 1cc9c6dbb700664e732a67245563e9a211456767
ms.sourcegitcommit: c31dbf646682c0f9d731f8df8cfd43d36a041f85
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/27/2019
ms.locfileid: "74559821"
---
# <a name="process-real-time-iot-data-streams-with-azure-stream-analytics"></a>Azure Stream Analytics를 사용 하 여 실시간 IoT 데이터 스트림 처리

이 문서에서는 IoT (사물 인터넷) 장치에서 데이터를 수집 하는 스트림 처리 논리를 만드는 방법에 대해 알아봅니다. IoT (실제 사물 인터넷) 사용 사례를 사용 하 여 신속 하 고 경제적으로 솔루션을 구축 하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>전제 조건

* 무료 [Azure 구독](https://azure.microsoft.com/pricing/free-trial/)을 만듭니다.
* [GitHub](https://aka.ms/azure-stream-analytics-get-started-iot)에서 샘플 쿼리 및 데이터 파일을 다운로드 합니다.

## <a name="scenario"></a>시나리오

Contoso는 산업용 자동화 공간의 회사로, 제조 프로세스를 완전히 자동화했습니다. 이 공장의 기계에는 실시간으로 데이터 스트림을 내보낼 수 있는 센서가 있습니다. 이 시나리오에서 생산 작업장 관리자는 센서 데이터로부터 실시간으로 정보를 얻어 패턴을 파악하고 조치를 취하고 싶어합니다. 센서 데이터에 대해 대해 SAQL (Stream Analytics Query Language)를 사용 하 여 들어오는 데이터 스트림에서 흥미로운 패턴을 찾을 수 있습니다.

이 예제에서 데이터는 텍사스 악기 센서 태그 장치에서 생성 됩니다. 데이터의 페이로드는 JSON 형식이며 다음과 같습니다.

```json
{
    "time": "2016-01-26T20:47:53.0000000",  
    "dspl": "sensorE",  
    "temp": 123,  
    "hmdt": 34  
}  
```

실제 시나리오에서는 이러한 센서가 수백 개 있으며 이벤트를 스트림으로 생성할 수 있습니다. 이상적으로 게이트웨이 디바이스는 이러한 이벤트를 [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) 또는 [Azure IoT Hubs](https://azure.microsoft.com/services/iot-hub/)로 푸시하는 코드를 실행합니다. Stream Analytics 작업은 Event Hubs에서 이러한 이벤트를 수집하고 스트림에 대해 실시간 분석 쿼리를 실행합니다. [지원되는 출력](stream-analytics-define-outputs.md) 중 하나에 결과를 보낼 수 있습니다.

사용 편의성을 위해 이 시작 가이드는 실제 센서 태그 디바이스에서 캡처된 샘플 데이터 파일을 제공합니다. 샘플 데이터에서 쿼리를 실행하고 결과를 볼 수 있습니다. 이후 자습서에서는 작업을 입력 및 출력에 연결하고 이를 Azure 서비스에 배포하는 방법을 알아봅니다.

## <a name="create-a-stream-analytics-job"></a>Stream Analytics 작업 만들기

1. [Azure Portal](https://portal.azure.com)의 왼쪽 탐색 메뉴에서 **+ 리소스 만들기** 를 선택 합니다. 그런 다음 **Analytics**에서 **Stream Analytics 작업** 을 선택 합니다.
   
    ![새 Stream Analytics 작업 만들기](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-02.png)

1. 고유한 작업 이름을 입력하고 해당 작업에 대한 구독이 유효한지 확인합니다. 새 리소스 그룹을 만들거나 구독에서 기존 리소스 그룹을 선택 합니다.

1. 작업의 위치를 선택 합니다. 리소스 그룹 및 모든 리소스에 대해 동일한 위치를 사용 하 여 처리 속도를 높이고 비용을 줄였습니다. 구성을 만든 후 **만들기**를 선택 합니다.
   
    ![새 Stream Analytics 작업 만들기 세부 정보](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-03.png)

## <a name="create-an-azure-stream-analytics-query"></a>Azure Stream Analytics 쿼리 만들기
작업을 만든 후 다음 단계는 쿼리를 작성 하는 것입니다. 입력 또는 출력을 작업에 연결 하지 않고 샘플 데이터에 대해 쿼리를 테스트할 수 있습니다.

GitHub에서 [helloworldasa-inputstream.json-InputStream](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/GettingStarted/HelloWorldASA-InputStream.json
) 를 다운로드 합니다. 그런 다음 Azure Portal에서 Azure Stream Analytics 작업으로 이동 합니다.

왼쪽 메뉴의 **작업 토폴로지** 아래에서 **쿼리** 를 선택 합니다. 그런 다음 **샘플 입력 업로드**를 선택 합니다. `HelloWorldASA-InputStream.json` 파일을 업로드 하 고 **확인**을 선택 합니다.

![Stream Analytics 대시보드 쿼리 타일](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-05.png)

데이터의 미리 보기가 **입력 미리 보기** 테이블에 자동으로 채워집니다.

![샘플 입력 데이터 미리 보기](./media/stream-analytics-get-started-with-iot-devices/input-preview.png)

### <a name="query-archive-your-raw-data"></a>쿼리: 원시 데이터 보관

가장 간단한 형태의 쿼리는 모든 입력 데이터를 지정된 출력에 보관하는 통과 쿼리입니다. 이 쿼리는 새 Azure Stream Analytics 작업에서 채워지는 기본 쿼리입니다.

```sql
SELECT 
    *
INTO
    Output
FROM
    InputStream
```

**쿼리 테스트** 를 선택 하 고 **테스트 결과** 테이블에서 결과를 확인 합니다.

![Stream Analytics 쿼리에 대한 테스트 결과](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-07.png)

### <a name="query-filter-the-data-based-on-a-condition"></a>쿼리: 조건에 따라 데이터 필터링

조건에 따라 결과를 필터링 해 보겠습니다. “sensorA”에서 가져온 해당 이벤트에 대한 결과만 표시하려고 합니다.

```sql
SELECT 
    time,
    dspl AS SensorName,
    temp AS Temperature,
    hmdt AS Humidity
INTO
   Output
FROM
    InputStream
WHERE dspl='sensorA'
```

편집기에 쿼리를 붙여넣고 **쿼리 테스트** 를 선택 하 여 결과를 검토 합니다.

![데이터 스트림 필터링](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-08.png)

### <a name="query-alert-to-trigger-a-business-workflow"></a>쿼리: 비즈니스 워크플로를 트리거하는 경고

쿼리를 더 자세히 만들겠습니다. 모든 유형의 센서에 대해 30초 기간당 평균 온도를 모니터링하고 평균 온도가 100도를 초과하는 경우만 결과를 표시하려고 합니다.

```sql
SELECT 
    System.Timestamp AS OutputTime,
    dspl AS SensorName,
    Avg(temp) AS AvgTemperature
INTO
   Output
FROM
    InputStream TIMESTAMP BY time
GROUP BY TumblingWindow(second,30),dspl
HAVING Avg(temp)>100
```

![30초 필터 쿼리](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-10.png)

평균 온도 100 보다 큰 245 행 및 센서 이름만 포함 된 결과가 표시 됩니다. 이 쿼리는 이벤트의 스트림을 30초 동안의 **연속 창**에서 센서 이름인 **dspl**로 그룹화합니다. 임시 쿼리는 시간을 진행 하는 방법을 명시 해야 합니다. **TIMESTAMP by** 절을 사용 하 여 모든 임시 계산과 시간을 연결 하는 **outputtime** 열을 지정 했습니다. 자세한 내용은 [시간 관리](https://docs.microsoft.com/stream-analytics-query/time-management-azure-stream-analytics) 및 [창 기능 함수](https://docs.microsoft.com/stream-analytics-query/windowing-azure-stream-analytics)를 참조 하세요.

### <a name="query-detect-absence-of-events"></a>쿼리: 이벤트 부재 감지

이벤트의 부족을 찾기 위해 어떻게 쿼리를 작성할 수 있나요? 센서가 데이터를 전송한 후 다음 5 초 동안 이벤트를 보내지 않은 마지막 시간을 살펴보겠습니다.

```sql
SELECT 
    t1.time,
    t1.dspl AS SensorName
INTO
   Output
FROM
    InputStream t1 TIMESTAMP BY time
LEFT OUTER JOIN InputStream t2 TIMESTAMP BY time
ON
    t1.dspl=t2.dspl AND
    DATEDIFF(second,t1,t2) BETWEEN 1 and 5
WHERE t2.dspl IS NULL
```

![이벤트의 부재 감지](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-11.png)

여기서는 동일한 데이터 스트림에 대해 **왼쪽 외부** 조인을 사용합니다(자체 조인). **내부** 조인의 경우 결과는 일치 항목이 있는 경우에만 반환됩니다.  하지만 **왼쪽 외부** 조인의 경우 조인 왼쪽의 이벤트가 일치하지 않는 경우 오른쪽 행의 모든 열에 대해 NULL이 있는 행이 반환됩니다. 이 방법은 이벤트의 부재를 찾는 데 매우 유용합니다. 자세한 내용은 [조인](https://docs.microsoft.com/stream-analytics-query/join-azure-stream-analytics)을 참조 하세요.

## <a name="conclusion"></a>결론

이 문서의 목적은 다른 Stream Analytics 쿼리 언어 쿼리를 작성 하 고 브라우저에서 결과를 확인 하는 방법을 보여 주는 것입니다. 그러나이 작업은 시작 하기 위한 것입니다. Stream Analytics은 다양한 입력 및 출력을 지원하고 Azure Machine Learning에서 함수를 사용하여 데이터 스트림을 분석하는 강력한 도구로 만들 수 있습니다. 쿼리를 작성하는 방법에 대한 자세한 내용은 [일반적인 쿼리 패턴](stream-analytics-stream-analytics-query-patterns.md)에 대한 문서를 참조하세요.

