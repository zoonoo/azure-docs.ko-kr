---
title: 수집된 원격 분석 데이터 쿼리
description: 이 문서에서는 수집된 원격 분석 데이터를 쿼리하는 방법을 설명합니다.
author: sunasing
ms.topic: article
ms.date: 03/11/2020
ms.author: sunasing
ms.openlocfilehash: f717903b3f953e04c793092c86802f2006de7e82
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349814"
---
# <a name="query-ingested-telemetry-data"></a>수집된 원격 분석 데이터 쿼리

이 문서에서는 Azure FarmBeats에서 수집된 센서 데이터를 쿼리하는 방법을 설명합니다.

장치 및 센서와 같은 사물 인터넷(IoT) 리소스에서 데이터를 수집하는 것은 FarmBeats의 일반적인 시나리오입니다. 장치 및 센서에 대한 메타데이터를 만든 다음 기록 데이터를 표준 형식으로 FarmBeats에 수집합니다. FarmBeats Datahub에서 센서 데이터를 사용할 수 있게 되면 동일한 쿼리를 수행하여 실행 가능한 통찰력을 생성하거나 모델을 빌드할 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서를 진행하기 전에 FarmBeats를 설치하고 IoT 장치에서 FarmBeats로 센서 원격 분석 데이터를 수집했는지 확인합니다.

센서 원격 분석 데이터를 수집하려면 [기록 원격 분석 데이터 수집을](ingest-historical-telemetry-data-in-azure-farmbeats.md) 방문하십시오.

계속하기 전에 API를 사용하여 인비트 된 원격 분석을 쿼리할 때 FarmBeats REST API에 대해 잘 알고 있어야 합니다. FarmBeats API에 대한 자세한 내용은 [FarmBeats REST API를](rest-api-in-azure-farmbeats.md)참조하십시오. **FarmBeats Datahub 끝점에 API 요청을 할 수 있는지 확인합니다.**

## <a name="query-ingested-sensor-telemetry-data"></a>수집된 센서 원격 분석 데이터 쿼리

FarmBeats에서 원격 분석 데이터에 액세스하고 쿼리하는 방법에는 두 가지가 있습니다.

- API 및
- TSI(타임시리즈 인사이트)

### <a name="query-using-rest-api"></a>REST API를 사용한 쿼리

이 단계에 따라 FarmBeats REST API를 사용하여 수집된 센서 원격 분석 데이터를 쿼리합니다.

1. 관심 있는 센서를 식별합니다. /Sensor API에서 GET 요청을 수행하여 이 작업을 수행할 수 있습니다.

> [!NOTE]
> 관심 있는 센서 객체의 **ID** 및 **센서모델Id.**

2. 1단계에서 설명한 대로 **sensorModelId에** 대한 /SensorModel API에서 GET/{id}를 만듭니다. "센서 모델"에는 센서에서 수집된 원격 측정에 대한 모든 메타데이터와 세부 정보가 있습니다. 예를 들어 **센서 모델** 개체 내의 **센서 측정값에는** 센서 전송 및 유형 및 단위에 대한 세부 정보가 있습니다. 예를 들면 다음과 같습니다.

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
센서 모델에 대한 GET/{id} 호출의 응답을 기록합니다.

3. 다음 입력 페이로드를 사용 하 고 /Telemetry API에 POST 호출을 수행 합니다.

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
4. /Telemetry API의 응답은 다음과 같습니다.

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
위의 예제 응답에서 쿼리된 센서 원격 분석은 측정값 이름("moist_soil_last") 및 두 타임스탬프에서 보고된 원격 분석값과 함께 두 개의 타임스탬프에 대한 데이터를 제공합니다. 보고된 값의 유형과 단위를 해석하려면 관련 센서 모델(2단계에서 설명된 대로)을 참조해야 합니다.

### <a name="query-using-azure-time-series-insights-tsi"></a>Azure 열열 인사이트(TSI)를 사용한 쿼리

FarmBeats는 [Azure Time Series Insights(TSI)를](https://azure.microsoft.com/services/time-series-insights/) 활용하여 타임시리즈에 대해 컨텍스트화되고 최적화된 IoT 스케일 데이터에서 데이터를 수집, 저장, 쿼리 및 시각화합니다.

원격 분석 데이터는 EventHub에서 수신된 다음 FarmBeats 리소스 그룹 내에서 TSI 환경으로 처리되고 푸시됩니다. 그런 다음 TSI에서 데이터를 직접 쿼리할 수 있습니다. 자세한 내용은 [TSI 설명서를](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-explorer) 참조하십시오.

TSI에서 데이터를 시각화하는 단계를 따릅니다.

1. Azure **Portal** > **FarmBeats DataHub 리소스 그룹으로** 이동 > 데이터 액세스 **정책**> **타임시리즈 인사이트** 환경(tsi-xxxx)을 선택합니다. 독자 또는 기여자 액세스 권한이 있는 사용자를 추가합니다.
2. **타임시리즈 인사이트** 환경(tsi-xxxx)의 **개요** 페이지로 이동하여 **타임시리즈 인사이트 탐색기 URL을**선택합니다. 이제 인비스트 된 원격 분석을 시각화할 수 있습니다.

원격 분석의 저장, 쿼리 및 시각화 외에도 TSI를 사용하면 Power BI 대시보드에 통합할 수 있습니다. 자세한 내용은 [여기를]( https://docs.microsoft.com/azure/time-series-insights/how-to-connect-power-bi) 참조하십시오.

## <a name="next-steps"></a>다음 단계

이제 Azure FarmBeats 인스턴스에서 센서 데이터를 쿼리했습니다. 이제 팜에 대한 [맵을 생성하는](generate-maps-in-azure-farmbeats.md#generate-maps) 방법을 알아봅니다.
