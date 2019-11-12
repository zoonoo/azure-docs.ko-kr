---
title: 기록 원격 분석 데이터 수집
description: 수집 기록 원격 분석 데이터를 가져오는 방법을 설명 합니다.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: e721a7818c5f2fcea23263b296912edf164036b2
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73927809"
---
# <a name="ingest-historical-telemetry-data"></a>기록 원격 분석 데이터 수집

이 문서에서는 Azure FarmBeats에 기록 센서 데이터를 수집 하는 방법을 설명 합니다.

수집 (IoT) 사물 인터넷에서 장치 및 센서와 같은 리소스에 대 한 기록 데이터를 FarmBeats 하는 것은 일반적인 시나리오입니다. 장치 및 센서에 대 한 메타 데이터를 만든 다음 기록 데이터를 정규 형식으로 FarmBeats에 수집 합니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서를 진행 하기 전에 FarmBeats를 설치 하 고 IoT에서 기록 데이터를 수집 했는지 확인 합니다.

## <a name="enable-partner-access"></a>파트너 액세스 사용

Azure FarmBeats 인스턴스에 파트너 통합을 사용 하도록 설정 해야 합니다. 이 단계에서는 장치 파트너로 Azure FarmBeats에 액세스할 수 있는 클라이언트를 만들고 이후 단계에서 필요한 다음 값을 제공 합니다.

- API 끝점 – https://<datahub>azurewebsites.net와 같은 데이터 허브 URL입니다.
- 테넌트 ID
- 클라이언트 ID
- 클라이언트 암호
- EventHub 연결 문자열

다음 단계를 수행 하 여 이러한 작업을 생성 합니다.

>[!NOTE]
> 다음 단계를 수행 하려면 관리자 여야 합니다.

1. 이 [스크립트](https://aka.ms/farmbeatspartnerscript) 를 다운로드 하 고 로컬 드라이브의에서 압축을 풉니다. ZIP 파일 내에서 두 개의 파일을 찾을 수 있습니다.
2. [Azure Portal](https://portal.azure.com/) 에 로그인 하 여 Cloud Shell를 엽니다 (이 옵션은 포털의 오른쪽 위 막대에서 사용할 수 있음).  

    ![프로젝트 팜 비트](./media/for-tutorials/navigation-bar-1.png)

3. 환경이 **PowerShell**로 설정 되었는지 확인 합니다.

    ![프로젝트 팜 비트](./media/for-tutorials/power-shell-new-1.png)

4. Cloud Shell에서 다운로드 한 두 파일 (위의 1 단계)을 업로드 합니다.  

    ![프로젝트 팜 비트](./media/for-tutorials/power-shell-two-1.png)

5. 파일이 업로드 된 디렉터리로 이동 합니다.

   >[!NOTE]
   > 기본적으로 파일은 홈 디렉터리/home/username/. 업로드 됩니다.
6. 명령을 사용 하 여 스크립트를 실행 합니다.  

    ```azurepowershell-interactive
    ./generateCredentials.ps1
    ```

7. 화면의 지시에 따라 절차를 완료 합니다.

## <a name="create-devicesensor-metadata"></a>장치/센서 메타 데이터 만들기

 이제 필수 자격 증명이 있으므로 FarmBeats Api를 사용 하 여 메타 데이터를 만들어 장치 및 센서를 정의할 수 있습니다.

 FarmBeats Data hub에는 장치/센서 메타 데이터의 생성 및 관리를 지 원하는 다음과 같은 Api가 있습니다.   

- /**DeviceModel** 모델은 장치에 대 한 메타 데이터 (예: 장치 유형 또는 게이트웨이 또는 노드)에 해당 합니다.  
- /**장치** -장치는 팜에 있는 물리적 장치에 해당 합니다.  
- /**SensorModel** 센서 모델은 제조업체, 센서 유형 (예: 아날로그 또는 디지털, 주변 온도와 같은 센서 측정 또는 압력)과 같은 센서의 메타 데이터에 해당 합니다.
- **센서** 센서 센서 센서는 값을 기록 하는 실제 센서에 해당 합니다. / 센서는 일반적으로 장치 ID를 사용 하 여 장치에 연결 됩니다.  


|        장치 모드   |  제안   |
| ------- | -------             |
|     유형 (노드, 게이트웨이)        |          별 1개      |
|          제조업체            |         별 2개     |
|  ProductCode                    |  장치 제품 코드 또는 모델 이름/번호입니다. 예: EnviroMonitor # 6800.  |
|            포트          |     포트 이름 및 유형 (디지털/아날로그)
|     이름                 |  리소스를 식별 하는 이름입니다. 예를 들어 모델 이름/제품 이름입니다.
      설명     | 모델에 대 한 의미 있는 설명 제공
|    속성          |    제조업체의 추가 속성   |
|    **디바이스**             |                      |
|   DeviceModelId     |     연결 된 장치 모델의 ID  |
|  hardwareId          | MAC 주소 등의 장치에 대 한 고유 ID
|  reportingInterval        |   보고 간격 (초)
|  Location            |  장치 위도 (-90 ~ + 90)/경도 (-180 ~ 180)/상승 (미터)   
|ParentDeviceId       |    이 장치가 연결 된 부모 장치의 ID입니다. 예를 들어 게이트웨이에 연결 된 노드입니다. 노드는 게이트웨이로 parentDeviceId를 포함 합니다.  |
|    이름            | 리소스를 식별 하는 이름입니다. 장치 파트너는 파트너 측의 장치 이름과 일치 하는 이름을 보내야 합니다. 파트너 장치 이름이 사용자 정의 이면 동일한 사용자 정의 이름을 FarmBeats에 전파 해야 합니다.|
|     설명       |      의미 있는 설명 제공  |
|     속성    |  제조업체의 추가 속성
|     **센서 모델**        |          |
|       유형 (아날로그, 디지털)          |      아날로그 또는 디지털 인지 여부를 나타내는 센서 유형       |
|          제조업체            |       센서의 제조업체입니다.     |
|     ProductCode| 제품 코드 또는 모델 이름/번호입니다. 예를 들어 RS-CO2-N01입니다. |
|       SensorMeasures > 이름       | 센서 측정값의 이름입니다. 소문자만 지원 됩니다. 다른 깊이에서 측정값을 지정 하려면 깊이를 지정 합니다. 예를 들어 soil_moisture_15cm 합니다. 이 이름은 원격 분석 데이터와 일치 해야 합니다.  |
|          SensorMeasures > 데이터 형식       |원격 분석 데이터 형식입니다. 현재 double이 지원 됩니다.|
|    sensorMeasures > 형식    |센서 원격 분석 데이터의 측정 유형입니다. AmbientTemperature, CO2, Depth, ElectricalConductivity, LeafWetness, Length, LiquidLevel, Nitrate, O2, PH, Phosphate, PointInTime, Potassium, 압력, Raingge, RelativeHumidity, Salinity, SoilMoisture 등의 시스템 정의 형식은 다음과 같습니다. SoilTemperature, SolarRadiation, State, TimeDuration, UVRadiation, Uvradiation, Volume, WindDirection, WindRun, WindSpeed, Evapotranspiration,와 동일 합니다. 추가 정보를 추가 하려면/ExtendedType API를 참조 하세요.|
|        SensorMeasures > Unit              | 센서 원격 분석 데이터의 단위입니다. 다음은 시스템 정의 단위입니다. NoUnit, 섭씨로, 화씨, 켈빈, Rankine, 파스칼식, 이란, PSI, 밀리미터, 센티미터, 미터, 인치, 피트, 마일, KiloMeter, MilesPerHour, MilesPerSecond, KMPerHour, KMPerSecond, MetersPerHour, MetersPerSecond, 학위, WattsPerSquareMeter, KiloWattsPerSquareMeter, MilliWattsPerSquareCentiMeter, MilliJoulesPerSquareCentiMeter, VolumetricWaterContent, 백분율, PartsPerMillion, MicroMol, MicroMolesPerLiter, SiemensPerSquareMeterPerMole, MilliSiemensPerCentiMeter, Centibar, DeciSiemensPerMeter, KiloPascal, VolumetricIonContent, 리터, MilliLiter, Seconds,, MicroMolPerMeterSquaredPerSecond, InchesPerHour,를 추가 하려면 다음을 참조 하세요. ExtendedType API.|
|    SensorMeasures > aggregationType    |  값은 없음, 평균, 최대값, 최소값 또는 StandardDeviation 일 수 있습니다.  |
|          이름            | 리소스를 식별 하는 이름입니다. 예를 들어 모델 이름/제품 이름입니다.  |
|    설명        | 모델에 대 한 의미 있는 설명 제공  |
|   속성       |  제조업체의 추가 속성  |
|    **수신**      |          |
| hardwareId          |   제조업체에서 설정한 센서의 고유 ID |
|  sensorModelId     |    연결 된 센서 모델의 ID   |
| location          |  센서 위도 (-90 ~ + 90)/경도 (-180 ~ 180)/상승 (미터)|
|   포트 > 이름        |  장치에서 센서가 연결 된 포트의 이름 및 유형입니다. 이 이름은 장치 모델에 정의 된 이름과 동일 해야 합니다. |
|    DeviceID  |    센서가 연결 된 장치의 ID     |
| 이름            |   리소스를 식별 하는 이름입니다. 예를 들어 센서 이름/제품 이름 및 모델 번호/제품 코드를 사용할 수 있습니다.|
|    설명      | 의미 있는 설명 제공 |
|    속성        |제조업체의 추가 속성 |

개체에 대 한 자세한 내용은 [Swagger](https://aka.ms/FarmBeatsDatahubSwagger)를 참조 하십시오.

**메타 데이터를 만들기 위한 API 요청**

API 요청을 만들려면 HTTP (POST) 메서드와 API 서비스에 대 한 URL, 쿼리할 리소스에 대 한 URI, 요청을 만들거나 삭제 하기 위해 데이터를 전송 하 고 하나 이상의 HTTP 요청 헤더를 추가 합니다. API 서비스에 대 한 URL은 API 끝점 (예: 데이터 허브 URL (https://<yourdatahub>. azurewebsites.net))입니다.  

**인증**:

FarmBeats Data hub는 위의 섹션에서 생성 한 다음 자격 증명을 필요로 하는 전달자 인증을 사용 합니다.

- 클라이언트 ID
- 클라이언트 암호
- 테넌트 ID  

호출자는 위의 자격 증명을 사용 하 여 다음과 같이 헤더 섹션의 후속 API 요청에서 전송 해야 하는 액세스 토큰을 요청할 수 있습니다.

```
headers = *{"Authorization": "Bearer " + access_token, …}*
```

**HTTP 요청 헤더**:

FarmBeats Data hub에 대 한 API 호출을 만들 때 지정 해야 하는 가장 일반적인 요청 헤더는 다음과 같습니다.

- Content-type: application/json
- 권한 부여: 전달자 < 액세스-토큰 >
- 수락: application/json

**메타 데이터를 만들기 위한 입력 페이로드**:

**DeviceModel**


```json
{
  "type": "Node",
  "manufacturer": "string",
  "productCode": "string",
  "ports": [
    {
      "name": "string",
      "type": "Analog"
    }
  ],
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```

디바이스

```json
{
  "deviceModelId": "string",
  "hardwareId": "string",
  "farmId": "string",
  "reportingInterval": 0,
  "location": {
    "latitude": 0,
    "longitude": 0,
    "elevation": 0
  },
  "parentDeviceId": "string",
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```

SensorModel

```json
{
  "type": "Analog",
  "manufacturer": "string",
  "productCode": "string",
  "sensorMeasures": [
    {
      "name": "string",
      "dataType": "Double",
      "type": "string",
      "unit": "string",
      "aggregationType": "None",
      "depth": 0,
      "description": "string"
    }
  ],
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}

```
센서

```json
{
  "hardwareId": "string",
  "sensorModelId": "string",
  "location": {
    "latitude": 0,
    "longitude": 0,
    "elevation": 0
  },
  "depth": 0,
  "port": {
    "name": "string",
    "type": "Analog"
  },
  "deviceId": "string",
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```
아래 샘플 요청은 장치를 만들기 위한 것입니다. 여기에는 입력 json이 요청 본문과 함께 페이로드로 포함 됩니다.  

```bash
curl -X POST "https://<datahub>.azurewebsites.net/Device" -H  
"accept: application/json" -H  "Content-Type: application/json" -H
"Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  
\"reportingInterval\": 900,  \"name\": \"Device123\",  
\"description\": \"Test Device 123\"}" *
```

> [!NOTE]
> Api는 생성 된 각 인스턴스에 대 한 고유 Id를 반환 합니다. 해당 원격 분석 메시지를 보내기 위한 Id를 유지 해야 합니다.

**원격 분석 보내기**

이제 FarmBeats에서 장치 및 센서를 만들었으므로 연결 된 원격 분석 메시지를 보낼 수 있습니다.  

**원격 분석 클라이언트 만들기**

처리를 위해 Azure 이벤트 허브로 원격 분석을 전송 해야 합니다. Azure EventHub는 연결 된 장치 및 응용 프로그램에서 실시간 데이터 (원격 분석) 수집을 가능 하 게 하는 서비스입니다. FarmBeats에 원격 분석 데이터를 보내려면 FarmBeats에서 이벤트 허브로 메시지를 보내는 클라이언트를 만들어야 합니다. 원격 분석을 보내는 방법에 대 한 자세한 내용은 [Azure event hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send)를 참조 하세요.

**원격 분석 메시지를 클라이언트로 보내기**

EventHub 클라이언트로 설정 된 연결이 있으면 EventHub로 메시지를 json으로 보낼 수 있습니다.  
기록 센서 데이터 형식을 Azure FarmBeats에서 인식 하는 정식 형식으로 변환 합니다. 정식 메시지 형식은 다음과 같습니다.  

```json
{
"deviceid": "<id of the Device created>",
"timestamp": "<timestamp in ISO 8601 format>",
"version" : "1",
"sensors": [
    {
      "id": "<id of the sensor created>",
      "sensordata": [
        {
          "timestamp": "< timestamp in ISO 8601 format >",
          "<sensor measure name (as defined in the Sensor Model)>": "<value>"
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": "<value>"
        }
      ]
    }
 ]
}
```

해당 장치 및 센서를 추가한 후 이전 섹션에서 설명한 대로 원격 분석 메시지에서 deviceid 및 sensorid를 가져옵니다.

원격 분석 메시지 예:


 ```json
{
  "deviceid": "7f9b4b92-ba45-4a1d-a6ae-c6eda3a5bd12",
  "timestamp": "2019-06-22T06:55:02.7279559Z",
  "version": "1",
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


## <a name="next-steps"></a>다음 단계

Rest API 기반 통합 정보에 대 한 자세한 내용은 [REST API](references-for-farmbeats.md#rest-api)를 참조 하세요.
