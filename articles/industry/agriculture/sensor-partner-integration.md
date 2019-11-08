---
title: 센서 파트너 통합
description: 센서 파트너 통합에 대해 설명 합니다.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: e7de815b7254fb071b3094f9ae636b712b38684b
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73797687"
---
# <a name="sensor-partner-integration"></a>센서 파트너 통합
이 문서에서는 Azure FarmBeats **Translator** 구성 요소에 대 한 정보를 제공 합니다.

파트너는이 구성 요소를 사용 하 여 FarmBeats와 통합 된 센서를 개발 하 고, API를 활용 하 고, 고객 장치 데이터 및 원격 분석을 FarmBeats 데이터 허브로 보낼 수 있습니다. FarmBeats Accelerator를 사용 하 여 데이터를 시각화 합니다. 데이터는 데이터 fusion에 사용 되 고 기계 언어/인공 지능 모델을 빌드하는 데 사용 될 수 있습니다.

## <a name="link-farmbeats-account"></a>FarmBeats 계정 연결

고객이 장치/센서를 구입 하 고 배포한 후에는 장치 파트너의 SaaS 포털 (Software as a Service)에서 장치 데이터 및 원격 분석에 액세스할 수 있습니다. 장치 파트너는 고객이 자신의 계정을 Azure의 FarmBeats 인스턴스에 연결할 수 있도록 해야 합니다. Customer/SI를 입력 하려면 다음 자격 증명이 필요 합니다.

   - 표시 이름 (이 통합의 이름을 정의 하는 사용자에 대 한 선택적 필드)
   - API 끝점
   - 테넌트 ID
   - 클라이언트 ID
   - 클라이언트 암호
   - EventHub 연결 문자열
   - 시작 날짜

   > [!NOTE]
   > 시작 날짜를 사용 하 여 기록 데이터 피드 (예: 사용자가 지정한 날짜의 데이터)를 사용할 수 있습니다.

## <a name="unlink-farmbeats"></a>FarmBeats 연결 해제

고객은 기존 FarmBeats 통합의 연결을 끊을 수 있습니다. FarmBeats 연결을 해제 하면 고객의 데이터 허브에 생성 된 장치/센서 메타 데이터를 삭제 하면 안 됩니다. 연결 끊기는 다음을 수행 합니다.

   - 원격 분석 흐름을 중지 합니다.
   - 장치 파트너에 대 한 통합 자격 증명을 삭제 하 고 지웁니다.

## <a name="edit-farmbeats-integration"></a>FarmBeats 통합 편집

고객은 FarmBeats 통합을 편집할 수 있습니다. 편집에 대 한 주요 시나리오는 만료로 인해 클라이언트 암호 또는 연결 문자열이 변경 되는 경우입니다 .이 경우 고객은 다음 필드만 편집할 수 있습니다.

   - 표시 이름 (해당 하는 경우)
   - 클라이언트 암호 ("2x8 * * * * * * * * * * * *" 형식 또는 일반 텍스트가 아닌 표시/숨기기 기능에 표시 되어야 함)
   - 연결 문자열 ("2x8 * * * * * * * * * * * *" 형식 또는 일반 텍스트가 아닌 표시/숨기기 기능에 표시 되어야 함)

   > [!NOTE]
   > Edit는 메타 데이터 개체 만들기를 중단 하지 않아야 합니다.

## <a name="view-last-telemetry-sent"></a>마지막으로 보낸 원격 분석 보기

전송 된 마지막 **원격 분석**의 타임 스탬프를 볼 수 있습니다. 최신 원격 분석이 성공적으로 FarmBeats에 전송 된 시간입니다.

## <a name="translator-development"></a>Translator 개발

**Rest API 기반 통합**

FarmBeats의 센서 데이터 통합 기능은 REST API를 통해 노출 됩니다. 기능에는 메타 데이터 정의, 장치/센서 프로 비전, 장치 및 센서 관리가 포함 됩니다.

**원격 분석 수집**

원격 분석 데이터는 처리를 위해 Azure 이벤트 허브에 게시 되는 정식 메시지에 매핑됩니다. Azure EventHub는 연결 된 장치 및 응용 프로그램에서 실시간 데이터 (원격 분석) 수집을 가능 하 게 하는 서비스입니다.

**API 개발**

Api에는 Swagger 기술 설명서가 포함 되어 있습니다. Api 및 해당 요청/응답에 대 한 자세한 내용은 [Swagger](https://aka.ms/FarmBeatsDatahubSwagger) 를 참조 하세요.

**인증**

FarmBeats는 Microsoft Azure Active Directory 인증을 활용 합니다. Azure App Service는 기본 제공 인증 및 권한 부여를 지원 합니다.

자세한 내용은 [Azure Active Directory](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization)를 참조하세요.

FarmBeats Data hub는 다음 자격 증명이 필요한 전달자 인증을 사용 합니다.
   - 클라이언트 ID
   - 클라이언트 암호
   - 테넌트 ID

호출자는 위의 자격 증명을 사용 하 여 다음과 같이 헤더 섹션의 후속 API 요청에서 전송 해야 하는 액세스 토큰을 요청할 수 있습니다.

```
headers = {"Authorization": "Bearer " + access_token, …} 
```

다음은 FarmBeats에 대 한 후속 API 호출에 사용할 수 있는 액세스 토큰을 제공 하는 샘플 Python 코드입니다. 

```python
import azure 

from azure.common.credentials import ServicePrincipalCredentials 
import adal 
#FarmBeats API Endpoint 
ENDPOINT = "https://<yourdatahub>.azurewebsites.net" [Azure website](https://<yourdatahub>.azurewebsites.net)
CLIENT_ID = "<Your Client ID>"   
CLIENT_SECRET = "<Your Client Secret>"   
TENANT_ID = "<Your Tenant ID>" 
AUTHORITY_HOST = 'https://login.microsoftonline.com' 
AUTHORITY = AUTHORITY_HOST + '/' + TENANT_ID 
#Authenticating with the credentials 
context = adal.AuthenticationContext(AUTHORITY) 
token_response = context.acquire_token_with_client_credentials(ENDPOINT, CLIENT_ID, CLIENT_SECRET) 
#Should get an access token here 
access_token = token_response.get('accessToken') 
```


**HTTP 요청 헤더**

FarmBeats Data hub에 대 한 API 호출을 만들 때 지정 해야 하는 가장 일반적인 요청 헤더는 다음과 같습니다.


**머리글** | **설명 및 예제**
--- | ---
콘텐츠 형식 | FarmBeats 데이터 허브 Api 형식의 요청 형식 (Content-type: application/<format>)은 json입니다. Content-type: application/json
권한 부여 | API 호출 권한 부여를 수행 하는 데 필요한 액세스 토큰을 지정 합니다. 전달자 < 액세스-토큰 >
수락 | 응답 형식입니다. FarmBeats Data hub Api의 경우 json Accept 형식은 application/json입니다.

**API 요청**

REST (Representational State Transfer) API 요청을 만들려면 HTTP (GET, POST 또는 PUT) 메서드, API 서비스에 대 한 URL, URI (Uniform Resource Identifier)를 쿼리, 데이터 전송, 업데이트 또는 삭제 및 하나 이상의 HTTP 요청에 결합 합니다. 머리글과. API 서비스에 대 한 URL은 고객이 제공한 API 끝점입니다. 다음은 샘플입니다. https://\<해당 하는 datahub-웹 사이트-이름 >

필요에 따라 필터에 대 한 GET 호출에 쿼리 매개 변수를 포함 하 고, 크기를 제한 하 고, 응답의 데이터를 정렬할 수 있습니다.

아래 샘플 요청은 장치 목록을 가져오는 것입니다.

```
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>”
```
대부분 GET, POST 및 PUT 호출에는 JSON 요청 본문이 필요 합니다.

아래 샘플 요청은 장치를 만드는 것입니다 .이 샘플에는 요청 본문을 사용 하는 입력 json이 있습니다.

```
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  "accept: application/json" -H  "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  \"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

## <a name="data-format"></a>데이터 형식

JSON (JavaScript Object Notation)은 임의의 데이터 구조에 대 한 간단한 텍스트 표현을 제공 하는 일반적인 언어 독립적 데이터 형식입니다. 자세한 내용은 [json.org](http://json.org)를 참조 하세요.

## <a name="metadata-specifications"></a>메타 데이터 사양

FarmBeats Data hub에는 장치 파트너가 장치/센서 메타 데이터를 만들고 관리할 수 있도록 하는 다음과 같은 Api가 있습니다.  

- /**DeviceModel** 모델은 장치에 대 한 메타 데이터 (예: 장치 유형 또는 게이트웨이 또는 노드)에 해당 합니다.  
- /**장치** -장치는 팜에 있는 물리적 장치에 해당 합니다.
- /**SensorModel** -센서 모델은 제조업체, 센서 유형 (예: 아날로그 또는 디지털, 주변 온도, 압력 등의 센서 측정)과 같은 센서의 메타 데이터에 해당 합니다.
- **센서** 센서 센서 센서는 값을 기록 하는 실제 센서에 해당 합니다. / 센서는 일반적으로 장치 ID를 사용 하 여 장치에 연결 됩니다.

  장치 모델| DeviceModel은 제조업체, 장치 유형, 게이트웨이 또는 노드 등 장치의 메타 데이터에 해당 합니다.
  --- | ---
  유형 (노드, 게이트웨이)  | 별 1개 |
  제조업체  | 별 2개 |
  ProductCode  | 장치 제품 코드 또는 모델 이름/번호입니다. 예: EnviroMonitor # 6800 |
  포트  | 포트 이름 및 유형 (디지털/아날로그)  |
  Name  | 리소스를 식별 하는 이름입니다. 예: 모델 이름/제품 이름 |
  설명  | 모델에 대 한 의미 있는 설명 제공 |
  속성  | 제조업체의 추가 속성 |
  **디바이스** | **장치는 팜에 있는 물리적 장치에 해당 합니다. 각 장치에는 고유한 장치 ID가 있습니다.** |
DeviceModelId  |연결 된 장치 모델의 ID입니다. |
hardwareId   |MAC 주소 등의 장치에 대 한 고유 ID  |
reportingInterval |보고 간격 (초) |
위치    |장치 위도 (-90 ~ + 90)/경도 (-180 ~ 180)/상승 (미터) |
ParentDeviceId | 이 장치가 연결 된 부모 장치의 ID입니다. 예제: 게이트웨이에 연결 된 노드입니다. 노드가 parentDeviceID를 게이트웨이로 포함 합니다. |
  Name  | 리소스를 식별 하는 이름입니다.  장치 파트너는 장치 파트너 측의 장치 이름과 일치 하는 이름을 보내야 합니다. 장치 이름이 장치 파트너 쪽에서 사용자 정의 이면 동일한 사용자 정의 이름을 FarmBeats에 전파 해야 합니다.  |
  설명  | 의미 있는 설명 제공  |
  속성  |제조업체의 추가 속성  |
  **센서 모델** | SensorModel은 제조업체, 센서 유형 (예: 아날로그 또는 디지털, 주변 온도, 압력 등의 센서 측정)과 같은 센서의 메타 데이터에 해당 합니다. |
  유형 (아날로그, 디지털)  |아날로그 또는 디지털 센서를 언급 합니다.|
  manufacturer  | 제조업체 이름 |
  ProductCode  | 제품 코드 또는 모델 이름/번호입니다. 예: RS-CO2-N01  |
  SensorMeasures > 이름  | 센서 측정값의 이름입니다. 소문자만 지원 됩니다. 다른 깊이에서 측정값을 지정 하려면 깊이를 지정 합니다. 예를 들어 soil_moisture_15cm이 이름은 원격 분석 데이터와 일치 해야 합니다. |
  SensorMeasures > 데이터 형식  | 원격 분석 데이터 형식입니다. 현재 double이 지원 됩니다.  |
  sensorMeasures > 형식  | 센서 원격 분석 데이터의 측정 유형입니다. AmbientTemperature, CO2, Depth, ElectricalConductivity, LeafWetness, Length, LiquidLevel, Nitrate, O2, PH, Phosphate, PointInTime, Potassium, 압력, Raingge, RelativeHumidity, Salinity, SoilMoisture 등의 시스템 정의 형식은 다음과 같습니다. SoilTemperature, SolarRadiation, State, TimeDuration, UVRadiation, Uvradiation, Volume, WindDirection, WindRun, WindSpeed, Evapotranspiration,와 동일 합니다. 추가 정보를 추가 하려면/ExtendedType API를 참조 하세요.
  SensorMeasures > Unit | 센서 원격 분석 데이터의 단위입니다. 다음은 시스템 정의 단위입니다. NoUnit, 섭씨로, 화씨, 켈빈, Rankine, 파스칼식, 이란, PSI, 밀리미터, 센티미터, 미터, 인치, 피트, 마일, KiloMeter, MilesPerHour, MilesPerSecond, KMPerHour, KMPerSecond, MetersPerHour, MetersPerSecond, 학위, WattsPerSquareMeter, KiloWattsPerSquareMeter, MilliWattsPerSquareCentiMeter, MilliJoulesPerSquareCentiMeter, VolumetricWaterContent, 백분율, PartsPerMillion, MicroMol, MicroMolesPerLiter, SiemensPerSquareMeterPerMole, MilliSiemensPerCentiMeter, Centibar, DeciSiemensPerMeter, KiloPascal, VolumetricIonContent, 리터, MilliLiter, Seconds,, MicroMolPerMeterSquaredPerSecond, InchesPerHour,를 추가 하려면 다음을 참조 하세요. ExtendedType API
  SensorMeasures > aggregationType  | 없음, 평균, 최대값, 최소값, StandardDeviation 중 하나
  SensorMeasures > 깊이  | 센서의 깊이 (예: 땅에 습기 10 cm의 측정)
  sensorMeasures > 설명  | 측정값에 대 한 의미 있는 설명을 제공 합니다.
  name  | 리소스를 식별 하는 이름입니다. 예: 모델 이름/제품 이름
  description  | 모델에 대 한 의미 있는 설명 제공
  properties  | 제조업체의 추가 속성
  **수신**  |
  hardwareId  | 제조업체에서 설정한 센서의 고유 ID
  sensorModelId  | 연결 된 센서 모델의 ID입니다.
  location  | 센서 위도 (-90 ~ + 90)/경도 (-180 ~ 180)/상승 (미터)
  포트 > 이름  |장치에서 센서가 연결 된 포트의 이름 및 유형입니다. 장치 모델에 정의 된 이름과 동일 해야 합니다.
  deviceId  | 센서가 연결 된 장치의 ID
  name  | 리소스를 식별 하는 이름입니다. 예를 들어 센서 이름/제품 이름 및 모델 번호/제품 코드를 사용할 수 있습니다.
  description  | 의미 있는 설명 제공
  properties  | 제조업체의 추가 속성

 각 개체 및 해당 속성에 대 한 자세한 내용은 [swagger](httpa://aka.ms/FarmBeatsDatahubSwagger)를 참조 하십시오.

 > [!NOTE]
 > Api는 생성 된 각 인스턴스에 대 한 고유 Id를 반환 합니다. 장치 관리 및 메타 데이터 동기화를 위해 변환기에서이 ID를 유지 해야 합니다.


**메타 데이터 동기화**

변환기는 메타 데이터에 대 한 업데이트를 전송 해야 합니다. 예를 들어 업데이트 시나리오의 경우 장치/센서 이름 변경, 장치/센서 위치 변경 등이 있습니다.

변환기에는 FarmBeats의 사용자 게시 링크를 통해 설치 된 새 장치 및/또는 센서를 추가할 수 있는 기능이 있어야 합니다. 마찬가지로, 사용자가 장치/센서를 업데이트 한 경우 해당 장치/센서에 대해 FarmBeats에서 동일 하 게 업데이트 해야 합니다. 장치/센서 업데이트에 대 한 일반적인 시나리오는 장치 위치 변경, 노드에 센서 추가 등이 될 수 있습니다.


> [!NOTE]
> 장치/센서 메타 데이터에 대해서는 삭제가 지원 되지 않습니다.
>
> 메타 데이터를 업데이트 하려면 장치/센서에 대해/Get/{id}를 호출 하 고, 변경 된 속성을 업데이트 한 후/Put/{id}를 수행 하 여 사용자가 설정한 모든 속성이 손실 되지 않도록 해야 합니다.

### <a name="adding-new-typesunit"></a>새 형식/단위 추가

FarmBeats는 새 센서 측정 유형 및 단위를 추가 하는 것을 지원 합니다. /ExtendedType API에 대 한 자세한 내용은 [Swagger](https://aka.ms/FarmBeatsDatahubSwagger)를 추가 합니다.

## <a name="telemetry-specifications"></a>원격 분석 사양

원격 분석 데이터는 처리를 위해 Azure 이벤트 허브에 게시 되는 정식 메시지에 매핑됩니다. Azure EventHub는 연결 된 장치 및 응용 프로그램에서 실시간 데이터 (원격 분석) 수집을 가능 하 게 하는 서비스입니다.

## <a name="send-telemetry-data-to-farmbeats"></a>FarmBeats로 원격 분석 데이터 보내기

FarmBeats에 원격 분석 데이터를 보내려면 FarmBeats에서 이벤트 허브로 메시지를 보내는 클라이언트를 만들어야 합니다. 원격 분석 데이터에 대 한 자세한 내용은 [이벤트 허브로 원격 분석 전송](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send)을 참조 하세요.

다음은 원격 분석을 지정 된 이벤트 허브에 클라이언트로 보내는 샘플 Python 코드입니다.

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

```
{
“deviceid”: “<id of the Device created>”,
 "timestamp": "<timestamp in ISO 8601 format>",
"version" : "1",
"sensors": [
    {
      "id": "<id of the sensor created>”
      "sensordata": [
        {
          "timestamp": "< timestamp in ISO 8601 format >",
          "<sensor measure name (as defined in the Sensor Model)>": value
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": value
        }
      ]
    }
}

```

원격 분석 json의 모든 키 이름은 소문자 (예: deviceid, sensordata 등) 여야 합니다.

예: 원격 분석 메시지:


```
{
  "deviceid": "7f9b4b92-ba45-4a1d-a6ae-c6eda3a5bd12",
  "timestamp": "2019-06-22T06:55:02.7279559Z",
  "version" : "1",
  "sensors": [
    {
      "id": "d8e7beb4-72de-4eed-9e00-45e09043a0b3",
      "sensordata": [
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_max": 15
        },
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_min": 42
        }
      ]
    },
    {
      "id": "d8e7beb4-72de-4eed-9e00-45e09043a0b3",
      "sensordata": [
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_max": 20
        },
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_min": 89
        }
      ]
    }
  ]
}

```

## <a name="troubleshooterror-management"></a>문제 해결/오류 관리

**옵션/지원 문제 해결**

고객이 지정 된 FarmBeats 인스턴스에서 장치 데이터 및/또는 원격 분석을 받을 수 없는 경우 장치 파트너는 동일한 문제를 해결 하기 위한 지원 및 메커니즘을 제공 해야 합니다.

**원격 분석 데이터 보존**

또한 미리 정의 된 기간 동안에는 원격 분석 데이터를 유지 하 여 오류 또는 데이터 손실이 발생할 경우 원격 분석을 디버깅 하거나 다시 전송 하는 데 유용 하 게 사용할 수 있습니다.

**오류 관리/오류 알림**

장치 파트너 시스템의 장치/센서 메타 데이터/데이터 통합 또는 원격 분석 데이터 흐름에 영향을 주는 오류가 발생 하는 경우 고객에 게 동일한 알림이 제공 되어야 합니다. 오류를 해결 하는 메커니즘을 설계 하 고 구현 해야 합니다.

**연결 검사 목록**

장치 제조업체/파트너는 고객이 제공한 자격 증명이 정확한 지 확인 하기 위해 다음과 같은 온전성 테스트/검사 목록을 사용할 수 있습니다.

   - 제공 된 자격 증명을 사용 하 여 액세스 토큰을 받았는지 확인 합니다.
   - 받은 액세스 토큰을 사용 하 여 API 호출이 성공 하는지 확인 합니다.
   - EventHub 클라이언트 연결이 설정 되었는지 확인 합니다.

## <a name="next-steps"></a>다음 단계

REST API에 대 한 자세한 내용은 [REST API](references-for-farmbeats.md#rest-api)을 참조 하세요.
