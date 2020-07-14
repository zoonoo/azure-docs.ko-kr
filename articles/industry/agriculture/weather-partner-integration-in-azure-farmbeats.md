---
title: 날씨 파트너 통합
description: 이 문서에서는 날씨 데이터 공급자가 FarmBeats와 통합 될 수 있는 방법을 설명 합니다.
author: sunasing
ms.topic: article
ms.date: 07/09/2020
ms.author: sunasing
ms.openlocfilehash: d4ceb25b9b81c831ed1b285a875742ebfaa6d24f
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/10/2020
ms.locfileid: "86232647"
---
# <a name="weather-partner-integration"></a>날씨 파트너 통합

이 문서에서는 Azure FarmBeats **커넥터** docker 구성 요소에 대 한 정보를 제공 합니다 .이 구성 요소는 해당 api를 활용 하 고 날씨 데이터를 FarmBeats로 보내서 FarmBeats와 통합 하기 위해 개발할 수 있습니다. FarmBeats에서 데이터를 사용할 수 있게 되 면 데이터 fusion 및 기계 학습/인공 지능 모델 빌드에 사용할 수 있습니다.

 > [!NOTE]
 > 이 설명서에서는 Azure Open 데이터 집합에서 NOAA를 사용 하 여 빌드된 참조 구현을 사용 하 고에서 사용할 수 있습니다 [https://github.com/azurefarmbeats/noaa_docker](https://github.com/azurefarmbeats/noaa_docker) .
 > 해당 docker 이미지는에서 사용할 수 있습니다.[https://hub.docker.com/r/azurefarmbeats/farmbeats-noaa](https://hub.docker.com/r/azurefarmbeats/farmbeats-noaa)

날씨 파트너는 docker 이미지/프로그램 (아래 설명 참조)을 제공 하 고 고객이 액세스할 수 있는 컨테이너 레지스트리에서 docker 이미지를 호스트 해야 합니다. 날씨 파트너는 고객에 게 다음 정보를 제공 해야 합니다.

- Docker 이미지 URL
- Docker 이미지 태그
- Docker 이미지에 액세스 하기 위한 키/자격 증명
- 날씨 파트너 시스템의 데이터에 액세스 하기 위한 고객 관련 API 키/자격 증명
- VM SKU 세부 정보 (파트너는 docker에 특정 VM 요구 사항이 있는 경우이를 제공할 수 있습니다. 그렇지 않은 경우 고객은 Azure에서 지원 되는 VM Sku에서 선택할 수 있음)

위의 docker 정보를 사용 하 여 고객은 FarmBeats 인스턴스에 날씨 파트너를 등록 합니다. 고객이 docker를 사용 하 여 FarmBeats에서 날씨 데이터를 수집 하는 방법에 대 한 자세한 내용은 [날씨 데이터를 가져오는](https://docs.microsoft.com/azure/industry/agriculture/get-weather-data-from-weather-partner) 가이드를 참조 하세요.

## <a name="connector-docker-development"></a>커넥터 docker 개발

**REST API 기반 통합**

FarmBeats Api에는 Swagger 기술 설명서가 포함 되어 있습니다. 모든 Api 및 해당 요청 또는 응답에 대 한 자세한 내용은 [FarmBeats Swagger](https://aka.ms/farmbeatsswagger)를 참조 하세요. 

FarmBeats를 설치한 경우 FarmBeats swagger at에 액세스할 수 있습니다.[https://yourfarmbeatswebsitename-api.azurewebsites.net](https://yourfarmbeatswebsitename-api.azurewebsites.net/swagger)

FarmBeats 웹 사이트 이름에 "-api"가 추가 됩니다.
API 끝점은 다음과 같습니다.[https://yourfarmbeatswebsitename-api.azurewebsites.net](https://yourfarmbeatswebsitename-api.azurewebsites.net)

### <a name="datahub-lib"></a>Datahub lib

FarmBeats는 날씨 파트너에서 사용할 수 있는 lib를 제공 합니다. Lib는 현재 [여기](https://github.com/azurefarmbeats/noaa_docker/tree/master/datahub_lib)에서 참조 구현의 일부로 사용할 수 있습니다. 향후에는 여러 언어에 대 한 SDK로 동일한를 사용할 수 있습니다.

### <a name="authentication"></a>인증

**FarmBeats Api를 사용 하 여 인증**

FarmBeats는 전달자 인증을 사용 하 고 아래와 같이 요청의 헤더 섹션에 액세스 토큰을 제공 하 여 Api에 액세스할 수 있습니다.

```
headers = *{"Authorization": "Bearer " + access_token, …}*
```

고객의 FarmBeats 인스턴스에서 실행 되는 Azure 함수에서 액세스 토큰을 요청할 수 있습니다. Azure 함수 URL은 인수로 docker 프로그램에 제공 되며, URL에 GET 요청을 수행 하 여 액세스 토큰을 가져올 수 있습니다. URL의 응답에는 액세스 토큰이 포함 됩니다. Datahub lib는 파트너에서 액세스 토큰을 가져올 수 있도록 하는 도우미 함수를 제공 합니다. 자세한 내용은 [여기](https://github.com/azurefarmbeats/noaa_docker/blob/master/datahub_lib/auth/partner_auth_helper.py)에 있습니다.

액세스 토큰은 몇 시간 동안만 유효 하며 만료 되 면 다시 요청 해야 합니다.

**파트너 쪽 Api를 사용 하 여 인증**

고객이 docker를 실행 하는 동안 파트너 측 Api를 사용 하 여 인증할 수 있도록 하려면 다음과 같이 파트너를 등록 하는 동안 자격 증명을 제공 해야 합니다.

```json
{
 "partnerCredentials": {
   "key1": "value1",
   "key2": "value2"
   }
}
```
API 서비스는이 dict를 직렬화 하 여 [Keyvault](https://docs.microsoft.com/azure/key-vault/basic-concepts)에 저장 합니다.

[Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction) 는 날씨 작업을 오케스트레이션 하 고 docker 코드를 실행 하기 위해 리소스를 회전 하는 데 사용 됩니다. 또한 docker 작업이 실행 되는 VM에 데이터를 안전 하 게 푸시하는 메커니즘도 제공 합니다. API 자격 증명, 이제 키 자격 증명 모음에 안전 하 게 저장 된는 KeyVault의 보안 문자열로 읽고, activity.js(파일 경로는 "/mnt/working_dir/activity.json"으로) docker 컨테이너의 작업 디렉터리에서 확장 속성으로 사용할 수 있게 됩니다 .이는 런타임에 고객을 대신 하 여 파트너 측 Api에 액세스 하는 동안 docker 코드에서이 파일의 자격 증명을 읽을 수 있습니다. 자격 증명은 다음과 같이 파일에서 사용할 수 있습니다.

```json
{ 
   "typeProperties" : {
      "extendedProperties" : { 
         "partnerCredentials": "" } 
   } 
}
```
파트너 등록 중에 고객이 제공한 정확한 방법으로 "파트너 자격 증명"을 사용할 수 있습니다.

FarmBeats lib는 파트너가 활동 속성에서 자격 증명을 읽을 수 있도록 하는 도우미 함수를 제공 합니다. 자세한 내용은 [여기](https://github.com/azurefarmbeats/noaa_docker/blob/master/datahub_lib/auth/partner_adf_helper.py)에 있습니다.

파일의 수명은 docker 코드를 실행 하는 동안에만 발생 하며 docker 실행이 종료 된 후 삭제 됩니다.

ADF 파이프라인과 활동의 작동 방식에 대 한 자세한 내용은을 참조 [https://docs.microsoft.com/azure/data-factory/copy-activity-schema-and-type-mapping](https://docs.microsoft.com/azure/data-factory/copy-activity-schema-and-type-mapping) 하세요.

**HTTP 요청 헤더**

FarmBeats에 대 한 API 호출을 수행할 때 지정 해야 하는 가장 일반적인 요청 헤더는 다음과 같습니다.

**머리글** | **설명 및 예**
--- | ---
콘텐츠 형식 | 요청 형식입니다(Content-Type: application/<format>). FarmBeats Datahub API의 경우 형식은 JSON입니다. Content-Type: application/json
권한 부여 | API 호출을 수행하는 데 필요한 액세스 토큰을 지정합니다. 권한 부여: Bearer <Access-Token>
수락 | 응답 형식입니다. FarmBeats Datahub API의 경우 형식은 JSON입니다. Accept: application/json

## <a name="data-format"></a>데이터 형식

JSON은 임의 데이터 구조의 단순한 텍스트 표현을 제공하는 공용 언어 독립적 데이터 형식입니다. 자세한 내용은 [json.org](http://json.org)를 참조하세요.

## <a name="docker-specifications"></a>Docker 사양

Docker 프로그램에는 **부트스트랩** 및 **작업**의 두 구성 요소가 있어야 합니다. 둘 이상의 작업이 있을 수 있습니다.

### <a name="bootstrap"></a>부트스트랩

이 구성 요소는 고객이 FarmBeats에서 docker 등록을 시작할 때 실행 됩니다. 이 프로그램에 전달 되는 인수 (arg1, arg2)는 다음과 같습니다.

- FarmBeats API 끝점: API 요청에 대 한 FarmBeats API 끝점: FarmBeats 배포에 대 한 API 호출을 수행 하기 위한 끝점입니다.
- Azure 함수 URL: FarmBeats Api에 대 한 액세스 토큰을 제공 하는 고유한 개인 끝점입니다. 이 url에 대해 GET을 호출 하기만 하면에서 해당 응답으로 액세스 토큰을 가져옵니다.

부트스트랩의 책임은 사용자가 날씨 데이터를 얻기 위해 작업을 실행할 수 있도록 필수 메타 데이터를 만드는 것입니다. 참조 구현은 [여기](https://github.com/azurefarmbeats/noaa_docker)에서 참조 하세요. 필요에 따라 파일의 bootstrap_manifest.js를 업데이트할 수 있으며, 참조 부트스트랩 프로그램은 필요한 메타 데이터를 만듭니다.

다음 메타 데이터는이 프로세스의 일부로 생성 됩니다. 

 > [!NOTE]
 > [참조 구현](https://github.com/azurefarmbeats/noaa_docker)에서 설명한 대로 파일의 bootstrap_manifest.js를 업데이트 하 **는 경우 부트스트랩** 이 매니페스트 파일에 따라 동일한를 만들기 때문에 아래 메타 데이터를 만들 필요가 없습니다.

- /**WeatherDataModel**: WeatherDataModel는 날씨 데이터를 나타내는 모델 이며 원본에서 제공 하는 다양 한 데이터 집합에 해당 합니다. 예를 들어 DailyForecastSimpleModel은 하루에 한 번 평균 온도, 습도 및 강 정보를 제공할 수 있는 반면, DailyForecastAdvancedModel는 시간별 세분성에서 훨씬 더 많은 정보를 제공할 수 있습니다. WeatherDataModels를 원하는 수 만큼 만들 수 있습니다.
- /**JobType**: FarmBeats에는 확장 가능한 작업 관리 시스템이 있습니다. 날씨 데이터 공급자는 다른 데이터 집합/a p i를 갖게 됩니다 (예: GetDailyForecasts). FarmBeats에서 JobType로 사용할 수 있습니다. JobType 만들어지면 고객이 해당 유형의 작업을 트리거하여 관심 있는 위치/팜에 대 한 날씨 데이터를 가져올 수 있습니다 ( [FarmBeats Swagger](https://aka.ms/farmbeatsswagger)의 JobType 및 Job api 참조).

### <a name="jobs"></a>작업

이 구성 요소는 FarmBeats 사용자가 부트스트랩 프로세스의 일부로 만든/JobType 작업을 실행할 때마다 호출 됩니다. 작업에 대 한 docker run 명령은 사용자가 만든 **/JobType** 의 일부로 정의 됩니다.
- 작업의 책임은 원본에서 데이터를 인출 하 고 FarmBeats로 푸시하는 것입니다. 데이터를 가져오는 데 필요한 매개 변수는 부트스트랩 프로세스에서/JobType의 일부로 정의 되어야 합니다.
- 작업의 일환으로 프로그램은 부트스트랩 프로세스의 일부로 생성 된/WeatherDataModel를 기반으로 **/WeatherDataLocation** 를 만들어야 합니다. **/WeatherDataLocation** 은 사용자가 작업에 대 한 매개 변수로 제공 하는 위치 (lat/long)에 해당 합니다.

### <a name="details-of-the-objects"></a>개체의 세부 정보

  **WeatherDataModel** |  |
  --- | ---
  이름  | 날씨 데이터 모델의 이름입니다. |
  Description  | 모델에 대한 의미 있는 설명을 제공합니다. |
  속성  | 데이터 공급자가 정의한 추가 속성입니다. |
  weatherMeasures > 이름  | 날씨 측정의 이름입니다. 예 humidity_max |
  weatherMeasures > 데이터 형식  | Double 또는 Enum 중 하나입니다. Enum 인 경우 measureEnumDefinition가 필요 합니다. |
  weatherMeasures > measureEnumDefinition  | DataType이 Enum 인 경우에만 필요 합니다. 예: {"Drizzle": 0, "눈 덮인": 1, "": 2, "비": 3} |
  weatherMeasures > 형식  | 날씨 원격 분석 데이터의 유형입니다. 예를 들면 "RelativeHumidity"입니다. 시스템 정의 형식은 다음과 같습니다. AmbientTemperature, NoUnit, CO2, Depth, ElectricalConductivity, LeafWetness, Length, LiquidLevel, Nounit, O2, PH, Phosphate, PointInTime, Potassium, 압력, Raingge, RelativeHumidity, Salinity, SoilMoisture, SoilTemperature, SolarRadiation, State, TimeDuration, UVRadiation, Uvradiation, Volume, WindDirection, WindRun, WindSpeed, Evapotranspiration,와 동일 합니다. 더 추가 하려면/ExtendedType API를 참조 하거나 아래에서 [형식 및 단위 추가 섹션](weather-partner-integration-in-azure-farmbeats.md#add-extendedtype) 을 참조 하세요.
  weatherMeasures > Unit | 날씨 원격 분석 데이터의 단위입니다. 시스템 정의 단위는 다음과 같습니다. NoUnit, Celsius, Fahrenheit, Kelvin, Rankine, Pascal, Mercury, PSI, MilliMeter, CentiMeter, Meter, Inch, Feet, Mile, KiloMeter, MilesPerHour, MilesPerSecond, KMPerHour, KMPerSecond, MetersPerHour, MetersPerSecond, Degree, WattsPerSquareMeter, KiloWattsPerSquareMeter, MilliWattsPerSquareCentiMeter, MilliJoulesPerSquareCentiMeter, VolumetricWaterContent, Percentage, PartsPerMillion, MicroMol, MicroMolesPerLiter, SiemensPerSquareMeterPerMole, MilliSiemensPerCentiMeter, Centibar, DeciSiemensPerMeter, KiloPascal, VolumetricIonContent, Liter, MilliLiter, Seconds, UnixTimestamp, MicroMolPerMeterSquaredPerSecond, and InchesPerHour. 더 추가 하려면/ExtendedType API 또는 아래의 [형식 및 단위 추가 섹션](weather-partner-integration-in-azure-farmbeats.md#add-extendedtype) 을 참조 하세요.
  weatherMeasures > AggregationType  | 없음, 평균, 최대값, 최소값, StandardDeviation, 합계, 합계
  weatherMeasures > 깊이  | 센서의 깊이(센티미터)입니다. 예를 들어 지하 10cm에서 수분을 측정합니다.
  weatherMeasures > 설명  | 측정에 대한 의미 있는 설명을 제공합니다. |
  **JobType** |  |
  이름  | 작업 이름-예: Get_Daily_Forecast; 날씨 데이터를 얻기 위해 고객이 실행 하는 작업입니다.|
  pipelineDetails > 매개 변수 > 이름  | 매개 변수의 이름입니다. |
  pipelineDetails > 매개 변수 > 형식 | String, Int, Float, Bool, Array 중 하나입니다. |
  pipelineDetails > 매개 변수 > isRequired | 부울 필수 매개 변수 이면 true이 고, 그렇지 않으면 false입니다. 기본값은 true입니다. |
  pipelineDetails > 매개 변수 > defaultValue | 매개 변수의 기본값입니다. |
  pipelineDetails > 매개 변수 > 설명 | 매개 변수에 대한 설명 |
  속성  | 제조업체로부터의 추가 속성입니다.
  속성 > 프로그래밍 **runcommand** | docker run 명령-이 명령은 고객이 날씨 작업을 실행할 때 실행 됩니다. |
  **WeatherDataLocation** |  |
  weatherDataModelId  | 부트스트랩 중에 생성 된 해당 WeatherDataModel의 ID|
  위치  | 위도, 경도 및 상승을 나타냅니다. |
  이름 | 개체의 이름입니다. |
  설명 | 설명 |
  farmId | **선택 사항** 작업 매개 변수의 일부로 고객이 제공한 팜 ID |
  속성  | 제조업체로부터의 추가 속성입니다.

 각 개체 및 해당 속성에 대한 자세한 내용은 [Swagger](https://aka.ms/FarmBeatsSwagger)를 참조하세요.

 > [!NOTE]
 > API는 생성된 각 인스턴스의 고유 ID를 반환합니다. 이 ID는 변환기가 디바이스 관리 및 메타데이터 동기화를 위해 유지해야 합니다.

**메타데이터 동기화**

커넥터 docker에는 메타 데이터에 대 한 업데이트를 보낼 수 있는 기능이 있어야 합니다. 업데이트 시나리오의 예는 날씨 공급자의 데이터 집합에 새 날씨 매개 변수를 추가 하 고 기능을 추가 하는 것입니다 (예: 30 일 예보 추가

> [!NOTE]
> 메타 데이터에 대 한 삭제는 지원 되지 않습니다. 예를 들어 날씨 데이터 모델.
>
> 메타 데이터를 업데이트 하려면 날씨 데이터 모델에서/Get/{ID}를 호출 하 고, 변경 된 속성을 업데이트 한 다음, 사용자가 설정한 모든 속성이 손실 되지 않도록/Put/{ID}를 수행 해야 합니다.

## <a name="weather-data-telemetry-specifications"></a>날씨 데이터 (원격 분석) 사양

날씨 데이터는 처리를 위해 Azure 이벤트 허브로 푸시되는 정식 메시지에 매핑됩니다. Azure EventHub는 연결 된 장치 및 응용 프로그램에서 실시간 데이터 (원격 분석) 수집을 가능 하 게 하는 서비스입니다. FarmBeats에 날씨 데이터를 보내려면 FarmBeats에서 이벤트 허브로 메시지를 보내는 클라이언트를 만들어야 합니다. 원격 분석을 보내는 방법에 대 한 자세한 내용은 [이벤트 허브로 원격 분석 전송](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send) 을 참조 하세요.

다음은 클라이언트로서 원격 분석을 지정된 이벤트 허브에 보내는 샘플 Python 코드입니다.

```python
import azure
from azure.eventhub import EventHubClient, Sender, EventData, Receiver, Offset
EVENTHUBCONNECTIONSTRING = "<EventHub Connection String provided by customer>"
EVENTHUBNAME = "<EventHub Name provided by customer>"

write_client = EventHubClient.from_connection_string(EVENTHUBCONNECTIONSTRING, eventhub=EVENTHUBNAME, debug=False)
sender = write_client.add_sender(partition="0")
write_client.run()
for i in range(5):
    telemetry = "<Canonical Telemetry message>"
    print("Sending telemetry: " + telemetry)
    sender.send(EventData(telemetry))
write_client.stop()

```

정식 메시지 형식은 다음과 같습니다.

```json
{
   "weatherstations": [
   {
   "id": "id of the WeatherDataLocation",
   "weatherdata": [
   {
     "timestamp": "timestamp of the data. For historical, this is the time for which the observations are sent. For forecast this is the time for which data is forecasted. Format is ISO 8601. Default time-zone is UTC",
     "predictiontimestamp": "timestamp on which the forecast data is predicted i.e time of prediction. Required only for forecast data. Format is ISO 8601. Default timezone is UTC ",
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

**오류 로깅**

파트너 작업은 기존 작업 프레임 워크에서 실행 되므로 FarmBeats의 다른 기존 작업 (예: GetFarmData, SensorPlacement 등)에 대 한 오류와 동일한 방식으로 오류가 기록 됩니다. ADF 파이프라인 내에서 실행 되는 ADF 작업은 STDERR 및 STDOUT을 모두 기록 합니다. 두 파일은 FarmBeats 리소스 그룹 내의 "datahublogs-xxx" 저장소 계정에서 사용할 수 있습니다.

Datahub lib는 전체 Datahub 로그의 일부로 로깅을 사용 하도록 설정 하는 도우미 함수를 제공 합니다. 자세한 내용은 [여기](https://github.com/azurefarmbeats/noaa_docker/blob/master/datahub_lib/framework/logger.py)에 있습니다.

**문제 해결 옵션 또는 지원**

고객이 지정 된 FarmBeats 인스턴스에서 날씨 데이터를 받을 수 없는 경우 날씨 파트너는 동일한 문제를 해결 하는 데 지원 및 메커니즘을 제공 해야 합니다.

## <a name="add-extendedtype"></a>ExtendedType 추가

FarmBeats는 새 센서 측정 유형 및 단위를 추가하도록 지원합니다. 날씨 파트너는 [참조 구현에서](https://github.com/azurefarmbeats/noaa_docker) 파일의 bootstrap_manifest.js를 업데이트 하 여 새 단위/유형을 추가할 수 있습니다.

새 WeatherMeasure 형식 (예: "PrecipitationDepth")을 추가 하려면 다음 단계를 수행 합니다.

1. 쿼리 필터를 사용 하 여/ExtendedType에 GET 요청-key = WeatherMeasureType
2. 반환 된 개체의 ID를 확인 합니다.
3. 반환 된 개체의 목록에 새 형식을 추가 하 고 다음과 같은 새 목록을 사용 하 여/ExtendedType{ID}에 PUT 요청을 만듭니다. 입력 페이로드는 위에서 받은 응답과 동일 해야 하며 값 목록의 끝에 새 단위를 추가 해야 합니다.

/ExtendedType API에 대한 자세한 내용은 [Swagger](https://aka.ms/FarmBeatsSwagger)를 참조하세요.

## <a name="next-steps"></a>다음 단계

이제 FarmBeats와 통합 된 커넥터 docker가 있습니다. 다음으로 docker를 사용 하 여 FarmBeats으로 날씨 데이터를 가져오는 방법을 볼 수 있습니다. [날씨 데이터 가져오기](get-weather-data-from-weather-partner.md)를 참조 하세요.
