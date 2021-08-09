---
title: 날씨 파트너 통합
description: 날씨 데이터 공급자가 FarmBeats와 통합할 수 있는 방법에 대해 알아봅니다.
author: sunasing
ms.topic: article
ms.date: 07/09/2020
ms.author: sunasing
ms.openlocfilehash: f0fbd93e2a5f4e92089e10e75dc17e304ff80bf6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93147082"
---
# <a name="weather-partner-integration-with-farmbeats"></a>FarmBeats와 날씨 파트너 통합

이 문서에서는 Azure FarmBeats 커넥터 Docker 구성 요소에 대한 정보를 제공합니다. 날씨 데이터 공급자는 커넥터 Docker를 사용하여 FarmBeats와 통합할 수 있습니다. API를 사용하여 FarmBeats에 날씨 데이터를 보냅니다. FarmBeats에서 데이터는 데이터 융합 및 기계 학습 모델 또는 인공 지능 모델 구축에 사용할 수 있습니다.

 > [!NOTE]
 > 이 문서에서는 Azure Open Datasets 및 NOAA(National Oceanic and Atmospheric Administration)의 날씨 데이터를 사용하여 구축된 [참조 구현](https://github.com/azurefarmbeats/noaa_docker)을 사용합니다. 해당하는 [Docker 이미지](https://hub.docker.com/r/azurefarmbeats/farmbeats-noaa)도 사용합니다.

[적절한 Docker 이미지 또는 프로그램](#docker-specifications)을 제공하고 고객이 액세스할 수 있는 컨테이너 레지스트리에 Docker 이미지를 호스트해야 합니다. 고객에게 다음 정보를 제공합니다.

- Docker 이미지 URL
- Docker 이미지 태그
- Docker 이미지에 액세스하기 위한 키 또는 자격 증명
- 시스템의 데이터에 액세스하기 위한 고객별 API 키 또는 자격 증명
- VM SKU 세부 정보(Docker 이미지에 특정 VM 요구 사항이 있는 경우 이러한 세부 정보를 제공합니다. 그렇지 않은 경우 고객은 Azure에서 지원되는 VM SKU 중에서 선택할 수 있습니다.)

고객은 이 Docker 정보를 사용하여 FarmBeats 인스턴스에 날씨 파트너를 등록합니다. 고객이 Docker를 사용하여 FarmBeats에서 날씨 데이터를 수집하는 방법에 대한 자세한 내용은 [날씨 파트너로부터 데이터 가져오기](./get-weather-data-from-weather-partner.md)를 참조하세요.

## <a name="connector-docker-development"></a>커넥터 Docker 개발

**REST API 기반 통합**

FarmBeats API에는 Swagger 기술 문서가 포함되어 있습니다. API 및 해당 요청 또는 응답에 대한 자세한 내용은 [FarmBeats Swagger](https://aka.ms/farmbeatsswagger)를 참조하세요. 

이미 FarmBeats를 설치했다면 `https://yourfarmbeatswebsitename-api.azurewebsites.net/swagger`에서 FarmBeats Swagger에 액세스합니다.

FarmBeats 웹사이트 이름에 *-api* 가 추가됩니다. API 엔드포인트는 `https://yourfarmbeatswebsitename-api.azurewebsites.net`입니다.

### <a name="datahub-lib"></a>Datahub 라이브러리

FarmBeats는 사용할 수 있는 라이브러리를 제공합니다. 라이브러리는 현재 [참조 구현의 일부](https://github.com/azurefarmbeats/noaa_docker/tree/master/datahub_lib)로 제공됩니다. 나중에 다국어용 SDK로 제공될 예정입니다.

### <a name="authentication"></a>인증

**FarmBeats API를 사용한 인증**

FarmBeats는 전달자 인증을 사용합니다. 요청의 헤더 섹션에 액세스 토큰을 제공하여 API에 액세스할 수 있습니다. 예를 들면 다음과 같습니다.

```
headers = *{"Authorization": "Bearer " + access_token, …}*
```

고객의 FarmBeats 인스턴스에서 실행 중인 Azure Functions 앱에서 액세스 토큰을 요청할 수 있습니다. Azure Functions URL은 Docker 프로그램에 인수로 제공됩니다. URL에 `GET` 요청을 하면 액세스 토큰을 얻을 수 있습니다. URL의 응답에는 액세스 토큰이 포함되어 있습니다. 

Datahub 라이브러리의 도우미 기능을 사용하여 액세스 토큰을 가져옵니다. 자세한 내용은 [NOAA Docker 이미지에 대한 GitHub 페이지](https://github.com/azurefarmbeats/noaa_docker/blob/master/datahub_lib/auth/partner_auth_helper.py)를 참조하세요.

액세스 토큰은 몇 시간 동안만 유효합니다. 만료되면 다시 요청해야 합니다.

**파트너 쪽 API를 사용한 인증**

Docker 작업이 실행되는 동안 파트너 쪽 API로 인증하려면 고객이 파트너 등록 중에 자격 증명을 제공해야 합니다. 예를 들면 다음과 같습니다.

```json
{
 "partnerCredentials": {
   "key1": "value1",
   "key2": "value2"
   }
}
```
API 서비스는 이 사전을 직렬화하여 [키 자격 증명 모음](../../key-vault/general/basic-concepts.md)에 저장합니다.

[Azure Data Factory](../../data-factory/introduction.md)는 날씨 작업을 조정하는 데 사용됩니다. Docker 코드를 실행하기 위해 리소스를 가동합니다. Data Factory는 Docker 작업이 실행되는 VM에 데이터를 안전하게 푸시하는 메커니즘도 제공합니다. 그러면 API 자격 증명이 키 자격 증명 모음에 안전하게 저장됩니다. 

자격 증명은 키 자격 증명 모음에서 보안 문자열로 읽힙니다. Docker 컨테이너의 작업 디렉터리에서 확장 속성으로 제공됩니다. 파일 경로는 */mnt/working_dir/activity.json* 입니다. 

Docker 코드는 런타임 중에 *activity.json* 에서 자격 증명을 읽어 고객의 파트너 쪽 API에 액세스할 수 있습니다. JSON 파일에서 자격 증명은 다음 코드 예와 같이 보입니다.

```json
{ 
   "typeProperties" : {
      "extendedProperties" : { 
         "partnerCredentials": "" } 
   } 
}
```
`partnerCredentials` 자격 증명은 고객이 파트너 등록 중에 제공한 방식으로 제공됩니다.

FarmBeats 라이브러리는 도우미 기능을 제공합니다. 이 함수를 사용하여 작업 속성에서 자격 증명을 읽습니다. 자세한 내용은 [NOAA Docker 이미지에 대한 GitHub 페이지](https://github.com/azurefarmbeats/noaa_docker/blob/master/datahub_lib/auth/partner_adf_helper.py)를 참조하세요.

파일은 Docker 코드가 실행되는 동안에만 사용됩니다. 코드가 완료되면 파일이 삭제됩니다.

Data Factory 파이프라인 및 작업의 작동 방식에 대한 자세한 내용은 [스키마 및 데이터 유형 매핑](../../data-factory/copy-activity-schema-and-type-mapping.md)을 참조하세요.

**HTTP 요청 헤더**

다음 표는 FarmBeats에 API를 호출할 때 지정해야 하는 가장 일반적인 요청 헤더를 보여 줍니다.

헤더 | 설명 및 예
--- | ---
콘텐츠 형식 | 요청 형식입니다. 예: `Content-Type: application/<format>` <br/>FarmBeats Datahub API의 경우 형식은 JSON입니다. 예: ` Content-Type: application/json`
권한 부여 | API 호출에 필요한 액세스 토큰입니다. 예: `Authorization: Bearer <Access-Token>`
수락 | 응답 형식입니다. FarmBeats Datahub API의 경우 형식은 JSON입니다. 예: `Accept: application/json`

## <a name="data-format"></a>데이터 형식

JSON은 임의 데이터 구조의 단순한 텍스트 표현을 제공하는 공용 언어 독립적 데이터 형식입니다. 자세한 내용은 [JSON.org](https://json.org)를 참조하세요.

## <a name="docker-specifications"></a>Docker 사양

Docker 프로그램에는 부트스트랩과 작업이라는 두 가지 구성 요소가 필요합니다. 프로그램은 둘 이상의 작업을 가질 수 있습니다.

### <a name="bootstrap"></a>부트스트랩

부트스트랩 구성 요소는 고객이 FarmBeats에서 Docker 등록을 시작할 때 실행되어야 합니다. 다음 인수(`arg1` 및 `arg2`)가 프로그램에 전달됩니다.

- **FarmBeats API 엔드포인트**: API 요청을 위한 FarmBeats API 엔드포인트입니다. 이 엔드포인트는 FarmBeats 배포에 대한 API 호출을 수행합니다.
- **Azure Functions URL**: 고유한 엔드포인트입니다. 이 URL은 FarmBeats API에 대한 액세스 토큰을 제공합니다. 이 URL에서 `GET`을 호출하여 액세스 토큰을 가져올 수 있습니다.

부트스트랩은 사용자가 날씨 데이터를 얻기 위해 작업을 실행하는 데 필요한 메타데이터를 만듭니다. 자세한 내용은 [참조 구현](https://github.com/azurefarmbeats/noaa_docker)을 참조하세요. 

*bootstrap_manifest.json* 파일을 사용자 지정하면 참조 부트스트랩 프로그램이 필요한 메타데이터를 만듭니다. 부트스트랩 프로그램은 다음 메타데이터를 만듭니다. 

 > [!NOTE]
 > [참조 구현](https://github.com/azurefarmbeats/noaa_docker)에 설명된 대로 *bootstrap_manifest.json* 파일을 업데이트하면 다음 메타데이터를 만들 필요가 없습니다. 부트스트랩 프로그램은 매니페스트 파일을 사용하여 필요한 메타데이터를 만듭니다.

- /**WeatherDataModel**: WeatherDataModel 메타데이터는 날씨 데이터를 나타냅니다. 원본이 제공하는 데이터 세트에 해당합니다. 예를 들어 DailyForecastSimpleModel은 하루에 한 번 평균 온도, 습도 및 강수량 정보를 제공할 수 있습니다. 대조적으로 DailyForecastAdvancedModel은 시간 단위로 훨씬 더 많은 정보를 제공할 수 있습니다. 원하는 수의 날씨 데이터 모델을 만들 수 있습니다.
- /**JobType**: FarmBeats에는 확장 가능한 작업 관리 시스템이 있습니다. 날씨 데이터 공급자는 다양한 데이터 세트와 API(예: GetDailyForecasts)를 갖게 됩니다. JobType을 사용하여 FarmBeats에서 이러한 데이터 세트 및 API를 사용하도록 설정할 수 있습니다. 작업 유형이 만들어진 후 고객은 해당 유형의 작업을 트리거하여 해당 위치 또는 관심 팜에 대한 날씨 데이터를 얻을 수 있습니다. 자세한 내용은 [FarmBeats Swagger](https://aka.ms/farmbeatsswagger)의 JobType 및 작업 API를 참조하세요.

### <a name="jobs"></a>작업

작업 구성 요소는 FarmBeats 사용자가 부트스트랩 프로세스의 일부로 만든 /JobType의 작업을 실행할 때마다 호출됩니다. 작업에 대한 Docker 실행 명령은 만든 /JobType의 일부로 정의됩니다.

작업은 원본에서 데이터를 가져와 FarmBeats로 푸시합니다. 부트스트랩 프로세스 동안 데이터를 가져오는 데 필요한 매개 변수는 /JobType의 일부로 정의되어야 합니다.

작업의 일부로, 프로그램은 부트스트랩 프로세스 중에 만들어진 /WeatherDataModel을 기반으로 /WeatherDataLocation을 만들어야 합니다. /WeatherDataLocation은 사용자가 작업에 대한 매개 변수로 제공한 위치(위도 및 경도 좌표)에 해당합니다.

### <a name="object-details"></a>개체 세부 정보

WeatherDataModel | 설명 |
--- | ---
이름  | 날씨 데이터 모델의 이름입니다. |
설명  | 모델에 대한 의미 있는 설명입니다. |
속성  | 데이터 공급자가 정의한 추가 속성입니다. |
weatherMeasures > Name  | 날씨 측정값의 이름입니다. 예를 들어 humidity_max. |
weatherMeasures > DataType  | Double이거나 Enum입니다. Enum인 경우 measureEnumDefinition이 필요합니다. |
weatherMeasures > measureEnumDefinition  | DataType이 Enum인 경우에만 필요합니다. 예를 들어 `{ "NoRain": 0, "Snow": 1, "Drizzle": 2, "Rain": 3 }` |
weatherMeasures > Type  | 날씨 원격 측정 데이터의 유형입니다. 예를 들어 RelativeHumidity. 시스템 정의 유형은 AmbientTemperature, NoUnit, CO2, Depth, ElectricalConductivity, LeafWetness, Length, LiquidLevel, Nitrate, O2, PH, Phosphate, PointInTime, Potassium, Pressure, RainGauge, RelativeHumidity, Salinity, SoilMoisture, SoilTemperature, SolarRadiation, State, TimeDuration, UVRadiation, UVIndex, Volume, WindDirection, WindRun, WindSpeed, Evapotranspiration 및 PAR입니다. 더 많은 유형을 추가하려면 이 문서의 [ExtendedType 추가](#add-extendedtype) 섹션을 참조하세요.
weatherMeasures > Unit | 날씨 원격 측정 데이터의 단위입니다. 시스템 정의 단위는 NoUnit, Celsius, Fahrenheit, Kelvin, Rankine, Pascal, Mercury, PSI, MilliMeter, CentiMeter, Meter, Inch, Feet, Mile, KiloMeter, MilesPerHour, MilesPerSecond, KMPerHour, KMPerSecond, MetersPerHour, MetersPerSecond, Degree, WattsPerSquareMeter, KiloWattsPerSquareMeter, MilliWattsPerSquareCentiMeter, MilliJoulesPerSquareCentiMeter, VolumetricWaterContent, Percentage, PartsPerMillion, MicroMole, MicroMolesPerLiter, SiemensPerSquareMeterPerMole, MilliSiemensPerCentiMeter, Centibar, DeciSiemensPerMeter, KiloPascal, VolumetricIonContent, Liter, MilliLiter, Seconds, UnixTimestamp, MicroMolePerMeterSquaredPerSecond 및 InchesPerHour입니다. 단위를 더 추가하려면 이 문서의 [ExtendedType 추가](#add-extendedtype) 섹션을 참조하세요.
weatherMeasures > AggregationType  | 집계 유형입니다. 가능한 값은 None, Average, Maximum, Minimum, StandardDeviation, Sum 및 Total입니다.
weatherMeasures > Depth  | 센서의 깊이(센티미터)입니다. 예를 들어 지하 10cm에서 수분을 측정합니다.
weatherMeasures > Description  | 측정에 대한 의미 있는 설명입니다. 

JobType | 설명 |
--- | ---
이름  | 작업의 이름입니다. 예를 들어 Get_Daily_Forecast. 고객은 이 작업을 실행하여 날씨 데이터를 가져옵니다.|
pipelineDetails > parameters > name  | 매개 변수의 이름입니다. |
pipelineDetails > parameters > type | 매개 변수 유형입니다. 가능한 값에는 String, Int, Float, Bool 및 Array가 있습니다. |
pipelineDetails > parameters > isRequired | 매개 변수의 부울 값입니다. 매개 변수가 필요한 경우 값은 true입니다. 그렇지 않으면 값은 false입니다. 기본값은 true입니다. |
pipelineDetails > parameters > defaultValue | 매개 변수의 기본값입니다. |
pipelineDetails > parameters > description | 매개 변수에 대한 설명입니다. |
속성  | 제조업체로부터의 추가 속성입니다.
Properties > programRunCommand | Docker 실행 명령입니다. 이 명령은 고객이 날씨 작업을 실행할 때 실행됩니다. |

WeatherDataLocation | 설명 |
--- | ---
weatherDataModelId  | 부트스트랩 프로세스 중에 만들어진 해당 WeatherDataModel의 ID입니다.|
위치  | 위도, 경도 및 고도로 표현됩니다. |
이름 | 개체 이름입니다. |
설명 | 날씨 데이터의 위치에 대한 설명입니다. |
farmId | (선택 사항) 팜의 ID입니다. 고객은 이 ID를 작업 매개 변수의 일부로 제공합니다. |
속성  | 제조업체로부터의 추가 속성입니다.

개체 및 해당 속성에 대한 자세한 내용은 [FarmBeats Swagger](https://aka.ms/FarmBeatsSwagger)를 참조하세요.

> [!NOTE]
> API는 만들어진 각 인스턴스의 고유 ID를 반환합니다. 디바이스 관리 및 메타데이터 동기화를 위한 변환기는 이 ID를 유지해야 합니다.

**메타데이터 동기화**

커넥터 Docker 구성 요소는 메타데이터에 대한 업데이트를 보낼 수 있어야 합니다. 예를 들어 날씨 공급자가 데이터 세트에 새 매개 변수를 추가하거나 새로운 30일 예보와 같은 새로운 기능이 추가되면 업데이트를 보내야 합니다.

> [!NOTE]
> 날씨 데이터 모델의 메타데이터에는 삭제가 지원되지 않습니다.
>
> 메타데이터를 업데이트하려면 날씨 데이터 모델에서 `/Get/{ID}`를 호출해야 합니다. 변경된 속성을 업데이트한 다음 `/Put/{ID}`를 수행하여 사용자가 설정한 속성을 유지합니다.

## <a name="weather-data-telemetry-specifications"></a>날씨 데이터(원격 측정) 사양

날씨 데이터는 처리를 위해 Azure 이벤트 허브로 푸시되는 정식 메시지에 매핑됩니다. Azure Event Hubs는 연결된 디바이스 및 애플리케이션으로부터 실시간 데이터(원격 분석) 수집을 가능하게 하는 서비스입니다. 

FarmBeats에 날씨 데이터를 보내려면 FarmBeats에서 이벤트 허브로 메시지를 보내는 클라이언트를 만듭니다. 자세한 내용은 [이벤트 허브에 원격 분석 보내기](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)를 참조하세요.

다음 샘플 Python 코드는 클라이언트로서 원격 분석을 지정된 이벤트 허브에 보냅니다.

```python
import azure
from azure.eventhub import EventHubClient, Sender, EventData, Receiver, Offset
EVENTHUBCONNECTIONSTRING = "<EventHub connection string provided by customer>"
EVENTHUBNAME = "<EventHub name provided by customer>"

write_client = EventHubClient.from_connection_string(EVENTHUBCONNECTIONSTRING, eventhub=EVENTHUBNAME, debug=False)
sender = write_client.add_sender(partition="0")
write_client.run()
for i in range(5):
    telemetry = "<Canonical telemetry message>"
    print("Sending telemetry: " + telemetry)
    sender.send(EventData(telemetry))
write_client.stop()

```

다음은 정식 메시지 형식입니다.

```json
{
   "weatherstations": [
   {
   "id": "ID of the WeatherDataLocation.",
   "weatherdata": [
   {
     "timestamp": "Timestamp of the data. For historical purposes, this is the time for which the observations are sent. For forecast, this is the time for which data is forecasted. Format is ISO 8601. Default time zone is UTC.",
     "predictiontimestamp": "Timestamp on which the forecast data is predicted. I.e., the time of prediction. Required only for forecast data. Format is ISO 8601. Default time zone is UTC. ",
     "weathermeasurename1": <value>,
     "weathermeasurename2": <value>
     }
     ]
    }
   ]
}
```

다음은 원격 분석 메시지의 예입니다.

```json
{
   "weatherstations": [
   {
     "id": "5e4b34e7-bf9e-4f39-xxxx-f3c06d0366ea",
     "weatherdata": [
     {
      "timestamp": "2019-07-10T00:00:00Z",
      "predictiontimestamp": "2019-07-05T00:00:00Z",
      "PrecipitationTotalLiquidEquivalent": 0,
      "AvgPressure": 0,
      "AvgRelativeHumidity": 72
     }
    ] 
  }
 ]
}

```

## <a name="troubleshooting-and-error-management"></a>문제 해결 및 오류 관리

### <a name="error-logging"></a>오류 로깅

파트너 작업은 기존 작업 프레임워크에서 실행됩니다. 따라서 오류는 다른 기존 FarmBeats 작업(예: GetFarmData 및 SensorPlacement)에 대한 오류와 동일한 방식으로 기록됩니다. Data Factory 파이프라인 내에서 실행되는 Data Factory 작업은 `STDERR` 및 `STDOUT`를 모두 기록합니다. 두 파일 모두 FarmBeats 리소스 그룹 내의 `datahublogs-xxx` 스토리지 계정에서 사용할 수 있습니다.

Datahub 라이브러리는 전체 Datahub 로그의 일부로 로깅을 사용하도록 설정하는 도우미 기능을 제공합니다. 자세한 내용은 [NOAA Docker 이미지에 대한 GitHub 페이지](https://github.com/azurefarmbeats/noaa_docker/blob/master/datahub_lib/framework/logger.py)를 참조하세요.

### <a name="troubleshooting-and-support"></a>문제 해결 및 지원

고객이 FarmBeats 인스턴스에서 날씨 데이터를 수신할 수 없는 경우 문제를 해결할 수 있는 지원 및 메커니즘을 제공합니다.

## <a name="add-extendedtype"></a>ExtendedType 추가

FarmBeats는 새 센서 측정 유형 및 단위를 추가하도록 지원합니다. [참조 구현](https://github.com/azurefarmbeats/noaa_docker)에서 *bootstrap_manifest.json* 파일을 업데이트하여 새 단위 또는 유형을 추가할 수 있습니다.

다음 단계에 따라 새 WeatherMeasure 유형(예: PrecipitationDepth)을 추가합니다.

1. 쿼리 `filter - key = WeatherMeasureType`을 사용하여 /ExtendedType에서 `GET` 요청을 수행합니다.
2. 반환된 개체의 ID를 기록해 둡니다.
3. 반환된 개체의 목록에 새 유형을 추가합니다. 다음 새 목록을 사용하여 /ExtendedType{ID}에 대해 `PUT` 요청을 수행합니다. 입력 페이로드는 이전에 받은 응답과 동일해야 합니다. 값 목록 끝에 새 단위를 추가해야 합니다.

/ExtendedType API에 대한 자세한 내용은 [FarmBeats Swagger](https://aka.ms/FarmBeatsSwagger)를 참조하세요.

## <a name="next-steps"></a>다음 단계

이제 FarmBeats와 통합되는 커넥터 Docker 구성 요소가 있습니다. 다음으로 FarmBeats에서 Docker 이미지를 사용하여 [날씨 데이터를 가져오는 방법](get-weather-data-from-weather-partner.md)을 알아봅니다. 
