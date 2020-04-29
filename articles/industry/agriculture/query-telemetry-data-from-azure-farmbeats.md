---
title: 수집된 원격 분석 데이터 쿼리
description: 이 문서에서는 수집 원격 분석 데이터를 쿼리 하는 방법을 설명 합니다.
author: sunasing
ms.topic: article
ms.date: 03/11/2020
ms.author: sunasing
ms.openlocfilehash: f717903b3f953e04c793092c86802f2006de7e82
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80349814"
---
# <a name="query-ingested-telemetry-data"></a>수집된 원격 분석 데이터 쿼리

이 문서에서는 Azure FarmBeats에서 수집 센서 데이터를 쿼리 하는 방법을 설명 합니다.

수집 (IoT) 리소스 사물 인터넷의 데이터 (예: 장치 및 센서)는 FarmBeats에서 일반적인 시나리오입니다. 장치 및 센서에 대 한 메타 데이터를 만든 다음 기록 데이터를 정규 형식으로 FarmBeats에 수집 합니다. FarmBeats Datahub에서 센서 데이터를 사용할 수 있게 되 면 동일한 쿼리를 실행 하 여 실행 가능한 정보를 생성 하거나 모델을 만들 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서를 진행 하기 전에 IoT 장치에서 FarmBeats에 FarmBeats 및 수집 센서 원격 분석 데이터를 설치 했는지 확인 합니다.

센서 원격 분석 데이터를 수집 하려면 [기록 원격 분석 데이터 수집](ingest-historical-telemetry-data-in-azure-farmbeats.md) 을 참조 하세요.

계속 하기 전에 Api를 사용 하 여 수집 원격 분석을 쿼리 하는 것 처럼 FarmBeats REST Api에 대해 잘 알고 있어야 합니다. FarmBeats Api에 대 한 자세한 내용은 [FARMBEATS REST api](rest-api-in-azure-farmbeats.md)를 참조 하세요. **FarmBeats Datahub 끝점에 대 한 API 요청을 수행할 수 있는지 확인**합니다.

## <a name="query-ingested-sensor-telemetry-data"></a>수집 센서 원격 분석 데이터 쿼리

FarmBeats에서 원격 분석 데이터에 액세스 하 고 쿼리 하는 방법에는 두 가지가 있습니다.

- API 및
- TSI (Time Series Insights).

### <a name="query-using-rest-api"></a>REST API를 사용 하 여 쿼리

FarmBeats REST Api를 사용 하 여 수집 센서 원격 분석 데이터를 쿼리 하는 단계를 수행 합니다.

1. 관심이 있는 센서를 확인 합니다. 이 작업을 수행 하려면/Smsa API에 대 한 GET 요청을 수행 합니다.

> [!NOTE]
> 관심 있는 센서 개체의 **id** 및 **sensorModelId** 입니다.

2. 1 단계에서 설명한 대로 **sensorModelId** 에 대 한 GET/{id}를/SensorModel API로 만듭니다. "센서 모델"에는 센서의 수집 원격 분석에 대 한 모든 메타 데이터 및 세부 정보가 포함 되어 있습니다. 예를 들어 센서 **모델** 개체 내의 **센서 측정값** 에는 센서에서 전송 하는 측정값과 해당 유형 및 단위에 대 한 세부 정보가 있습니다. 예를 들면 다음과 같습니다.

  ```json
  {
      "name": "moist_soil_last <name of the sensor measure - this is what we will receive as part of the queried telemetry data>",
      "dataType": "Double <Data Type - eg. Double>",
      "type": "SoilMoisture <Type of measure eg. temperature, soil moisture etc.>",
      "unit": "Percentage <Unit of measure eg. Celsius, Percentage etc.>",
      "aggregationType": "None <either of None, Average, Maximum, Minimum, StandardDeviation>",
      "description": "<Description of the measure>"
  }
  ```
센서 모델에 대 한 GET/{id} 호출의 응답을 기록해 둡니다.

3. 다음 입력 페이로드를 사용 하 여/원격 분석 API에 대 한 POST 호출을 수행 합니다.

  ```json
  {
    "sensorId": "<id of the sensor as noted in step 1>",
    "searchSpan": {
      "from": "<desired start timestamp in ISO 8601 format; default is UTC>",
      "to": "<desired end timestamp in ISO 8601 format; default is UTC>"
    },
    "filter": {
      "tsx": "string"
    },
    "projectedProperties": [
      {
        "additionalProp1": "string",
        "additionalProp2": "string",
        "additionalProp3": "string"
      }
    ]
  }
  ```
4. /원격 분석 API의 응답은 다음과 같습니다.

  ```json
  {
    "timestamps": [
      "2020-XX-XXT07:30:00Z",
      "2020-XX-XXT07:45:00Z"
    ],
    "properties": [
      {
        "values": [
          "<id of the sensor>",
          "<id of the sensor>"
        ],
        "name": "Id",
        "type": "String"
      },
      {
        "values": [
          2.1,
          2.2
        ],
        "name": "moist_soil_last <name of the SensorMeasure as defined in the SensorModel object>",
        "type": "Double <Data Type of the value - eg. Double>"
      }
    ]
  }
  ```
위의 예제 응답에서 쿼리 된 센서 원격 분석은 두 타임 스탬프에 대 한 데이터를 측정값 이름 ("moist_soil_last") 및 보고 된 원격 분석 값과 함께 두 타임 스탬프에 대 한 데이터를 제공 합니다. 2 단계에서 설명한 대로 연결 된 센서 모델을 참조 하 여 보고 된 값의 형식 및 단위를 해석 해야 합니다.

### <a name="query-using-azure-time-series-insights-tsi"></a>Azure Time Series Insights를 사용 하 여 쿼리 (TSI)

FarmBeats는 [TSI (Azure Time Series Insights)](https://azure.microsoft.com/services/time-series-insights/) 를 활용 하 여 데이터를 수집, 저장, 쿼리 및 시각화할 수 있습니다. 즉, 시계열에 대해 매우 까닭 되 고 최적화 된 데이터입니다.

원격 분석 데이터는 EventHub에서 수신 된 다음 FarmBeats 리소스 그룹 내의 TSI 환경으로 처리 되 고 푸시됩니다. 그런 다음 TSI에서 직접 데이터를 쿼리할 수 있습니다. 자세한 내용은 [Tsi 설명서](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-explorer) 를 참조 하세요.

TSI에서 데이터를 시각화 하는 단계를 수행 합니다.

1. **Azure Portal** > **FarmBeats datahub 리소스 그룹** > 선택 하 **Time Series Insights** 환경 (tsi-xxxx) > **데이터 액세스 정책**을 선택 합니다. 판독기 또는 참가자 액세스 권한이 있는 사용자를 추가 합니다.
2. **Time Series Insights** 환경 (tsi-xxxx)의 **개요** 페이지로 이동 하 여 **Time Series Insights 탐색기 URL**을 선택 합니다. 이제 수집 원격 분석을 시각화할 수 있습니다.

TSI는 원격 분석을 저장, 쿼리 및 시각화 하는 것 외에도 Power BI 대시보드를 통합할 수 있도록 합니다. 자세한 내용은 [여기]( https://docs.microsoft.com/azure/time-series-insights/how-to-connect-power-bi) 를 참조 하세요.

## <a name="next-steps"></a>다음 단계

이제 Azure FarmBeats 인스턴스에서 센서 데이터를 쿼리 했습니다. 이제 팜에 대 한 [맵을 생성](generate-maps-in-azure-farmbeats.md#generate-maps) 하는 방법에 대해 알아봅니다.
