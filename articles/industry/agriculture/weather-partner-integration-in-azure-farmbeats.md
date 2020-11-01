---
title: 날씨 파트너 통합
description: 날씨 데이터 공급자가 FarmBeats와 통합 될 수 있는 방법에 대해 알아봅니다.
author: sunasing
ms.topic: article
ms.date: 07/09/2020
ms.author: sunasing
ms.openlocfilehash: f0fbd93e2a5f4e92089e10e75dc17e304ff80bf6
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/01/2020
ms.locfileid: "93147082"
---
# <a name="weather-partner-integration-with-farmbeats"></a>FarmBeats와 날씨 파트너 통합

이 문서에서는 Azure FarmBeats 커넥터 Docker 구성 요소에 대 한 정보를 제공 합니다. 날씨 데이터 공급자는 커넥터 Docker를 사용 하 여 FarmBeats와 통합할 수 있습니다. 해당 Api를 사용 하 여 날씨 데이터를 FarmBeats로 보냅니다. FarmBeats에서 데이터를 사용 하 여 데이터를 fusion 하 고 기계 학습 모델 또는 인공 지능 모델을 빌드할 수 있습니다.

 > [!NOTE]
 > 이 문서에서는 Azure Open 데이터 집합 및 NOAA (국가별 Oceanic and 대기 Administration)의 날씨 데이터를 사용 하 여 빌드된 [참조 구현을](https://github.com/azurefarmbeats/noaa_docker) 사용 합니다. 또한 해당 [Docker 이미지](https://hub.docker.com/r/azurefarmbeats/farmbeats-noaa)를 사용 합니다.

[적절 한 docker 이미지 또는 프로그램](#docker-specifications) 을 제공 하 고 고객이 액세스할 수 있는 컨테이너 레지스트리에서 docker 이미지를 호스트 해야 합니다. 고객에 게 다음 정보를 제공 합니다.

- Docker 이미지 URL
- Docker 이미지 태그
- Docker 이미지에 액세스 하기 위한 키 또는 자격 증명
- 시스템의 데이터에 액세스 하기 위한 고객 관련 API 키 또는 자격 증명
- VM SKU 세부 정보 (Docker 이미지에 특정 VM 요구 사항이 있는 경우 이러한 세부 정보를 제공 합니다. 그렇지 않은 경우 고객은 Azure에서 지원 되는 VM Sku를 선택할 수 있습니다.

고객은이 Docker 정보를 사용 하 여 FarmBeats 인스턴스에 날씨 파트너를 등록 합니다. 고객이 Docker를 사용 하 여 FarmBeats에서 날씨 데이터를 수집 하는 방법에 대 한 자세한 내용은 [날씨 파트너의 데이터 가져오기](./get-weather-data-from-weather-partner.md)를 참조 하세요.

## <a name="connector-docker-development"></a>커넥터 Docker 개발

**REST API 기반 통합**

FarmBeats Api에는 Swagger 기술 설명서가 포함 되어 있습니다. Api 및 해당 요청 또는 응답에 대 한 자세한 내용은 [FarmBeats Swagger](https://aka.ms/farmbeatsswagger)를 참조 하세요. 

FarmBeats를 이미 설치한 경우 FarmBeats Swagger at에 액세스 합니다. `https://yourfarmbeatswebsitename-api.azurewebsites.net/swagger`

*-Api* 가 FarmBeats 웹 사이트 이름에 추가 됩니다. API 끝점은 `https://yourfarmbeatswebsitename-api.azurewebsites.net`

### <a name="datahub-lib"></a>Datahub lib

FarmBeats는 사용할 수 있는 lib를 제공 합니다. Lib는 현재 [참조 구현의 일부로](https://github.com/azurefarmbeats/noaa_docker/tree/master/datahub_lib)사용할 수 있습니다. 이후에는 여러 언어에 대 한 SDK로 사용할 수 있습니다.

### <a name="authentication"></a>인증

**FarmBeats Api를 사용 하 여 인증**

FarmBeats는 전달자 인증을 사용 합니다. 요청의 헤더 섹션에 액세스 토큰을 제공 하 여 Api에 액세스할 수 있습니다. 예를 들면 다음과 같습니다.

```
headers = *{"Authorization": "Bearer " + access_token, …}*
```

고객의 FarmBeats 인스턴스에서 실행 되는 Azure Functions 앱에서 액세스 토큰을 요청할 수 있습니다. Azure Functions URL은 Docker 프로그램에 인수로 제공 됩니다. URL에 대 한 요청을 수행 하 여 액세스 토큰을 가져올 수 있습니다 `GET` . URL의 응답에는 액세스 토큰이 포함 되어 있습니다. 

Datahub lib의 도우미 함수를 사용 하 여 액세스 토큰을 가져옵니다. 자세한 내용은 [NOAA Docker 이미지에 대 한 GitHub 페이지](https://github.com/azurefarmbeats/noaa_docker/blob/master/datahub_lib/auth/partner_auth_helper.py)를 참조 하세요.

액세스 토큰은 몇 시간 동안만 유효 합니다. 만료 되 면 다시 요청 해야 합니다.

**파트너 쪽 Api를 사용 하 여 인증**

Docker 작업이 실행 되는 동안 파트너 측 Api를 사용 하 여 인증 하려면 파트너를 등록 하는 동안 자격 증명을 제공 해야 합니다. 예를 들면 다음과 같습니다.

```json
{
 "partnerCredentials": {
   "key1": "value1",
   "key2": "value2"
   }
}
```
API 서비스는이 dict를 직렬화 하 고 [키 자격 증명 모음](../../key-vault/general/basic-concepts.md)에 저장 합니다.

[Azure Data Factory](../../data-factory/introduction.md) 는 날씨 작업을 오케스트레이션 하는 데 사용 됩니다. 리소스를 회전 하 여 Docker 코드를 실행 합니다. 또한 Data Factory는 Docker 작업이 실행 되는 VM에 데이터를 안전 하 게 푸시하는 메커니즘도 제공 합니다. 그러면 API 자격 증명이 키 자격 증명 모음에 안전 하 게 저장 됩니다. 

자격 증명은 키 자격 증명 모음에서 보안 문자열로 읽습니다. Docker 컨테이너의 작업 디렉터리에 확장 속성으로 제공 됩니다. 파일 경로는 */mnt/working_dir/activity.js* 입니다. 

Docker 코드는 런타임에 *activity.js* 에서 자격 증명을 읽어 고객의 파트너 측 api에 액세스할 수 있습니다. JSON 파일에서 자격 증명은 다음 코드 예제 처럼 보입니다.

```json
{ 
   "typeProperties" : {
      "extendedProperties" : { 
         "partnerCredentials": "" } 
   } 
}
```
`partnerCredentials`자격 증명은 고객이 파트너 등록 중에 제공 하는 방식으로 사용할 수 있습니다.

FarmBeats lib는 도우미 함수를 제공 합니다. 이러한 함수를 사용 하 여 작업 속성에서 자격 증명을 읽습니다. 자세한 내용은 [NOAA Docker 이미지에 대 한 GitHub 페이지](https://github.com/azurefarmbeats/noaa_docker/blob/master/datahub_lib/auth/partner_adf_helper.py)를 참조 하세요.

이 파일은 Docker 코드가 실행 되는 동안에만 사용 됩니다. 코드가 완료 되 면 파일이 삭제 됩니다.

Data Factory 파이프라인과 작업의 작동 방식에 대 한 자세한 내용은 [스키마 및 데이터 형식 매핑](../../data-factory/copy-activity-schema-and-type-mapping.md)을 참조 하세요.

**HTTP 요청 헤더**

다음 표에서는 FarmBeats에 대 한 API 호출을 수행할 때 지정 해야 하는 가장 일반적인 요청 헤더를 보여 줍니다.

헤더 | 설명 및 예
--- | ---
콘텐츠 형식 | 요청 형식입니다. 예: `Content-Type: application/<format>` <br/>FarmBeats Datahub API의 경우 형식은 JSON입니다. 예: ` Content-Type: application/json`
권한 부여 | API 호출을 수행 하는 데 필요한 액세스 토큰입니다. 예: `Authorization: Bearer <Access-Token>`
수락 | 응답 형식입니다. FarmBeats Datahub API의 경우 형식은 JSON입니다. 예: `Accept: application/json`

## <a name="data-format"></a>데이터 형식

JSON은 임의 데이터 구조의 단순한 텍스트 표현을 제공하는 공용 언어 독립적 데이터 형식입니다. 자세한 내용은 [JSON.org](https://json.org)를 참조 하세요.

## <a name="docker-specifications"></a>Docker 사양

Docker 프로그램에는 부트스트랩 및 작업의 두 구성 요소가 필요 합니다. 프로그램에는 둘 이상의 작업이 있을 수 있습니다.

### <a name="bootstrap"></a>부트스트랩

고객이 FarmBeats에서 Docker 등록을 시작할 때 부트스트랩 구성 요소가 실행 됩니다. `arg1` `arg2` 프로그램에 전달 되는 인수는 다음과 같습니다.

- **FARMBEATS api 끝점** : api 요청에 대 한 FarmBeats api 끝점입니다. 이 끝점은 FarmBeats 배포에 대 한 API 호출을 수행 합니다.
- **AZURE FUNCTIONS URL** : 고유한 끝점입니다. 이 URL은 FarmBeats Api에 대 한 액세스 토큰을 제공 합니다. `GET`이 URL에 대해를 호출 하 여 액세스 토큰을 가져올 수 있습니다.

부트스트랩은 날씨 데이터를 얻기 위해 사용자가 작업을 실행 하는 데 필요한 메타 데이터를 만듭니다. 자세한 내용은 [참조 구현](https://github.com/azurefarmbeats/noaa_docker)을 참조 하세요. 

파일 *에bootstrap_manifest.js* 를 사용자 지정 하는 경우 참조 부트스트랩 프로그램은 사용자에 게 필요한 메타 데이터를 만듭니다. 부트스트랩 프로그램은 다음 메타 데이터를 만듭니다. 

 > [!NOTE]
 > [참조 구현](https://github.com/azurefarmbeats/noaa_docker) 에서 설명한 대로 파일 *에bootstrap_manifest.js* 를 업데이트 하는 경우에는 다음 메타 데이터를 만들 필요가 없습니다. 부트스트랩 프로그램은 매니페스트 파일을 사용 하 여 필요한 메타 데이터를 만듭니다.

- /**WeatherDataModel** : WeatherDataModel 메타 데이터는 날씨 데이터를 나타냅니다. 원본에서 제공 하는 데이터 집합에 해당 합니다. 예를 들어 DailyForecastSimpleModel은 하루에 한 번 평균 온도, 습도 및 강 정보를 제공할 수 있습니다. 이와 대조적으로 DailyForecastAdvancedModel는 시간별 세분성에서 훨씬 더 많은 정보를 제공할 수 있습니다. 원하는 수의 날씨 데이터 모델을 만들 수 있습니다.
- /**JobType** : FarmBeats에는 확장 가능한 작업 관리 시스템이 있습니다. 날씨 데이터 공급자는 다양 한 데이터 집합 및 Api (예: GetDailyForecasts)를 사용할 수 있습니다. JobType를 사용 하 여 FarmBeats에서 이러한 데이터 집합 및 Api를 사용 하도록 설정할 수 있습니다. 작업 유형을 만든 후에는 고객이 해당 유형의 작업을 트리거하여 해당 위치 또는 관심 있는 팜에 대 한 날씨 데이터를 가져올 수 있습니다. 자세한 내용은 JobType and Job Api in the [FarmBeats Swagger](https://aka.ms/farmbeatsswagger)항목을 참조 하세요.

### <a name="jobs"></a>작업

FarmBeats 사용자가 부트스트랩 프로세스의 일부로 만든/JobType 작업을 실행할 때마다 Jobs 구성 요소가 호출 됩니다. 작업에 대 한 Docker run 명령은 사용자가 만든/JobType의 일부로 정의 됩니다.

작업은 원본에서 데이터를 인출 하 고 FarmBeats로 푸시합니다. 부트스트랩 프로세스 중에 데이터를 가져오는 데 필요한 매개 변수를/JobType.의 일부로 정의 해야 합니다.

작업의 일환으로 프로그램은 부트스트랩 프로세스 중에 생성 된/WeatherDataModel를 기반으로/WeatherDataLocation를 만들어야 합니다. /WeatherDataLocation은 사용자가 작업에 대 한 매개 변수로 제공한 위치 (위도 및 경도 좌표)에 해당 합니다.

### <a name="object-details"></a>개체 정보

WeatherDataModel | 설명 |
--- | ---
Name  | 날씨 데이터 모델의 이름입니다. |
설명  | 모델에 대 한 의미 있는 설명입니다. |
속성  | 데이터 공급자가 정의한 추가 속성입니다. |
weatherMeasures > 이름  | 날씨 측정의 이름입니다. 예를 들어 humidity_max 합니다. |
weatherMeasures > 데이터 형식  | Double 또는 Enum 중 하나입니다. Enum 인 경우 measureEnumDefinition가 필요 합니다. |
weatherMeasures > measureEnumDefinition  | DataType이 Enum 인 경우에만 필요 합니다. 예를 들어 `{ "NoRain": 0, "Snow": 1, "Drizzle": 2, "Rain": 3 }` |
weatherMeasures > 형식  | 날씨 원격 분석 데이터의 유형입니다. 예를 들면 RelativeHumidity입니다. 시스템 정의 형식은 AmbientTemperature입니다. NoUnit, CO2, Depth, ElectricalConductivity, LeafWetness, Length, LiquidLevel, Nounit, O2, PH, Phosphate, PointInTime, Potassium, 압력, Raingge, RelativeHumidity, Salinity, SoilMoisture, SoilTemperature, SolarRadiation, State, TimeDuration, UVRadiation, Uvradiation, Volume, WindDirection, WindRun, WindSpeed, Evapotranspiration 및 or. 더 많은 형식을 추가 하려면이 문서에서 [ExtendedType 추가](#add-extendedtype) 섹션을 참조 하세요.
weatherMeasures > Unit | 날씨 원격 분석 데이터의 단위입니다. 시스템 정의 단위는 NoUnit, 섭씨, 화씨, 온도, Rankine, 파스칼, 수, 수 PSI, 밀리미터, 센티미터, 미터, 인치, 피트, 마일, KiloMeter, MilesPerHour, MilesPerSecond, KMPerHour, KMPerSecond, MetersPerHour, MetersPerSecond, 학위, WattsPerSquareMeter, KiloWattsPerSquareMeter, MilliWattsPerSquareCentiMeter, MilliJoulesPerSquareCentiMeter, VolumetricWaterContent, 백분율, PartsPerMillion, 마이크로 Mole, MicroMolesPerLiter, SiemensPerSquareMeterPerMole, MilliSiemensPerCentiMeter, Centibar, DeciSiemensPerMeter, KiloPascal, VolumetricIonContent, 리터, MilliLiter, Seconds,, MicroMolePerMeterSquaredPerSecond, InchesPerHour 및. 단위를 더 추가 하려면이 문서에서 [ExtendedType 추가](#add-extendedtype) 섹션을 참조 하세요.
weatherMeasures > AggregationType  | 집계의 유형입니다. 가능한 값은 없음, 평균, 최대값, 최소값, StandardDeviation, 합계 및 합계입니다.
weatherMeasures > 깊이  | 센서의 깊이(센티미터)입니다. 예를 들어 지하 10cm에서 수분을 측정합니다.
weatherMeasures > 설명  | 측정에 대 한 의미 있는 설명입니다. 

JobType | 설명 |
--- | ---
Name  | 작업의 이름입니다. 예를 들어 Get_Daily_Forecast 합니다. 고객은이 작업을 실행 하 여 날씨 데이터를 가져옵니다.|
pipelineDetails > 매개 변수 > 이름  | 매개 변수의 이름입니다. |
pipelineDetails > 매개 변수 > 형식 | 매개 변수 유형입니다. 가능한 값에는 String, Int, Float, Bool 및 Array가 있습니다. |
pipelineDetails > 매개 변수 > isRequired | 매개 변수의 부울 값입니다. 매개 변수가 필요한 경우 값은 true입니다. 그렇지 않은 경우 값은 false입니다. 기본값은 true입니다. |
pipelineDetails > 매개 변수 > defaultValue | 매개 변수의 기본값입니다. |
pipelineDetails > 매개 변수 > 설명 | 매개 변수에 대 한 설명입니다. |
속성  | 제조업체로부터의 추가 속성입니다.
속성 > 프로그래밍 Runcommand | Docker run 명령입니다. 이 명령은 고객이 날씨 작업을 실행할 때 실행 됩니다. |

WeatherDataLocation | 설명 |
--- | ---
weatherDataModelId  | 부트스트랩 프로세스 중에 만들어진 해당 WeatherDataModel의 ID입니다.|
위치  | 위도, 경도 및 권한 상승입니다. |
Name | 개체 이름입니다. |
설명 | 날씨 데이터 위치에 대 한 설명입니다. |
farmId | 필드 팜의 ID입니다. 고객은이 ID를 작업 매개 변수의 일부로 제공 합니다. |
속성  | 제조업체로부터의 추가 속성입니다.

개체 및 해당 속성에 대 한 자세한 내용은 [FarmBeats Swagger](https://aka.ms/FarmBeatsSwagger)를 참조 하십시오.

> [!NOTE]
> Api는 생성 된 각 인스턴스에 대 한 고유 Id를 반환 합니다. 장치 관리 및 메타 데이터 동기화에 대 한 번역기는이 ID를 유지 해야 합니다.

**메타데이터 동기화**

커넥터 Docker 구성 요소는 메타 데이터에 대 한 업데이트를 보낼 수 있어야 합니다. 예를 들어 날씨 공급자가 데이터 집합에 새 매개 변수를 추가 하거나 새로운 30 일 예측과 같은 새로운 기능이 추가 되 면 업데이트를 전송 해야 합니다.

> [!NOTE]
> 삭제는 날씨 데이터 모델의 메타 데이터에 대해 지원 되지 않습니다.
>
> 메타 데이터를 업데이트 하려면 `/Get/{ID}` 날씨 데이터 모델에서를 호출 해야 합니다. 변경 된 속성을 업데이트 한 다음를 수행 `/Put/{ID}` 하 여 사용자가 설정 하는 속성을 유지 합니다.

## <a name="weather-data-telemetry-specifications"></a>날씨 데이터 (원격 분석) 사양

날씨 데이터는 처리를 위해 Azure 이벤트 허브로 푸시되는 정식 메시지에 매핑됩니다. Azure Event Hubs는 연결된 디바이스 및 애플리케이션으로부터 실시간 데이터(원격 분석) 수집을 가능하게 하는 서비스입니다. 

FarmBeats에 날씨 데이터를 보내려면 FarmBeats에서 이벤트 허브로 메시지를 보내는 클라이언트를 만듭니다. 자세한 내용은 [이벤트 허브로 원격 분석 전송](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)을 참조 하세요.

다음 샘플 Python 코드는 원격 분석을 지정 된 이벤트 허브에 클라이언트로 보냅니다.

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

원격 분석 메시지의 예는 다음과 같습니다.

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

파트너 작업은 기존 작업 프레임 워크에서 실행 됩니다. 따라서 오류는 다른 기존 FarmBeats 작업 (예: GetFarmData 및 SensorPlacement)에 대 한 오류와 동일한 방식으로 기록 됩니다. Data Factory 파이프라인 내에서 실행 되는 Data Factory 활동은 및를 모두 기록 합니다 `STDERR` `STDOUT` . 두 파일은 `datahublogs-xxx` FarmBeats 리소스 그룹 내의 저장소 계정에서 사용할 수 있습니다.

Datahub lib는 전체 Datahub 로그의 일부로 로깅을 사용 하도록 설정 하는 도우미 함수를 제공 합니다. 자세한 내용은 [NOAA Docker 이미지에 대 한 GitHub 페이지](https://github.com/azurefarmbeats/noaa_docker/blob/master/datahub_lib/framework/logger.py)를 참조 하세요.

### <a name="troubleshooting-and-support"></a>문제 해결 및 지원

고객이 FarmBeats 인스턴스에서 날씨 데이터를 받을 수 없는 경우 지원 및 문제 해결 메커니즘을 제공 합니다.

## <a name="add-extendedtype"></a>ExtendedType 추가

FarmBeats는 새 센서 측정 유형 및 단위를 추가하도록 지원합니다. [참조 구현](https://github.com/azurefarmbeats/noaa_docker)에서 파일 *의bootstrap_manifest.js* 를 업데이트 하 여 새 단위나 유형을 추가할 수 있습니다.

새 WeatherMeasure 형식 (예: PrecipitationDepth)을 추가 하려면 다음 단계를 수행 합니다.

1. `GET`쿼리를 사용 하 여/ExtendedType에서 요청을 만듭니다 `filter - key = WeatherMeasureType` .
2. 반환 된 개체의 ID를 확인 합니다.
3. 반환 된 개체의 목록에 새 형식을 추가 합니다. `PUT`다음 새 목록을 사용 하 여/ExtendedType{ID}에 대 한 요청을 만듭니다. 입력 페이로드는 앞에서 받은 응답과 동일 해야 합니다. 값 목록의 끝에 새 단위를 추가 해야 합니다.

/ExtendedType API에 대 한 자세한 내용은 [FarmBeats Swagger](https://aka.ms/FarmBeatsSwagger)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

이제 FarmBeats와 통합 된 커넥터 Docker 구성 요소가 있습니다. 다음으로, FarmBeats에서 Docker 이미지를 사용 하 여 [날씨 데이터를 가져오는](get-weather-data-from-weather-partner.md) 방법을 알아봅니다. 
