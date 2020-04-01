---
title: 센서 파트너 통합
description: 이 문서에서는 센서 파트너 통합에 대해 설명합니다.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 3431576acbb01a0cc3a5f372460b28be05bf7ce7
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437472"
---
# <a name="sensor-partner-integration"></a>센서 파트너 통합

이 문서에서는 센서 파트너 통합을 가능하게 하는 Azure FarmBeats **번역기** 구성 요소에 대한 정보를 제공합니다.

이 구성 요소를 사용하여 파트너는 FarmBeats Datahub API를 사용하여 FarmBeats와 통합하고 고객 장치 데이터 및 원격 분석을 FarmBeats Datahub로 보낼 수 있습니다. FarmBeats에서 데이터를 사용할 수 있게 되면 FarmBeats 가속기를 사용하여 시각화되며 데이터 융합 및 기계 학습/인공 지능 모델을 구축하는 데 사용할 수 있습니다.

## <a name="before-you-start"></a>시작하기 전에

번역기 구성 요소를 개발하려면 FarmBeats API에 액세스할 수 있는 다음 자격 증명이 필요합니다.

- API 엔드포인트
- 테넌트 ID
- 클라이언트 ID
- 클라이언트 암호
- EventHub 연결 문자열

위의 자격 증명을 보려면 이 섹션을 [참조하십시오.](get-sensor-data-from-sensor-partner.md#enable-device-integration-with-farmbeats)

## <a name="translator-development"></a>번역기 개발

**REST API 기반 통합**

FarmBeats의 센서 데이터 통합 기능은 REST API를 통해 노출됩니다. 기능에는 메타데이터 정의, 장치 및 센서 프로비저닝, 장치 및 센서 관리가 포함됩니다.

**원격 분석 사용**

원격 분석 데이터는 처리를 위해 Azure Event Hubs에 게시된 표준 메시지에 매핑됩니다. Azure Event Hubs는 연결된 장치 및 응용 프로그램에서 실시간 데이터(원격 분석) 수집을 가능하게 하는 서비스입니다.

**API 개발**

API에는 Swagger 기술 문서가 포함되어 있습니다. API 및 해당 요청 또는 응답에 대한 자세한 내용은 [Swagger](https://aka.ms/FarmBeatsSwagger)을 참조하십시오.

**인증**

FarmBeats는 Microsoft Azure Active Directory 인증을 사용합니다.Azure App Service는 기본 제공 인증 및 권한 부여 지원을 제공합니다.

자세한 내용은 [Azure Active Directory](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization)를 참조하세요.

FarmBeats Datahub는 다음과 같은 자격 증명이 필요한 베어러 인증을 사용합니다.
   - 클라이언트 ID
   - 클라이언트 암호
   - 테넌트 ID

이러한 자격 증명을 사용하여 호출자는 액세스 토큰을 요청할 수 있습니다. 토큰은 다음과 같이 헤더 섹션에서 후속 API 요청에서 보내야 합니다.

```
headers = {"Authorization": "Bearer " + access_token, …} 
```

다음 샘플 파이썬 코드는 FarmBeats에 대한 후속 API 호출에 사용할 수 있는 액세스 토큰을 제공합니다.

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

다음은 FarmBeats Datahub에 대한 API 호출을 수행할 때 지정해야 하는 가장 일반적인 요청 헤더입니다.


**헤더** | **설명 및 예**
--- | ---
콘텐츠 형식 | 요청 형식(콘텐츠 유형: 응용<format>프로그램/ ). 팜비트 데이터허브 API의 경우 형식은 JSON입니다. Content-Type: application/json
권한 부여 | API 호출을 만드는 데 필요한 액세스 토큰을 지정합니다. 권한 부여: 액세스 토큰 <보유자>
수락 | 응답 형식입니다. 팜비트 데이터허브 API의 경우 형식은 JSON입니다. 수락: 신청서/json

**API 요청**

REST API 요청을 하려면 HTTP(GET, POST 또는 PUT) 메서드, API 서비스에 대한 URL, URI(통일 리소스 식별자)를 리소스에 결합하여 쿼리, 데이터 제출, 업데이트 또는 삭제, 하나 이상의 HTTP 요청 헤더를 만듭니다. API 서비스에 대한 URL은 제공하는 API 끝점입니다. 다음은 샘플입니다: https://\<datahub-웹 사이트 이름>.azurewebsite.net

선택적으로 GET 호출에 쿼리 매개 변수를 포함하여 필터링하고 크기를 제한하며 응답에서 데이터를 정렬할 수 있습니다.

다음 샘플 요청은 장치 목록을 얻는 것입니다.

```bash
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>"
```
대부분의 GET, POST 및 PUT 호출에는 JSON 요청 본문이 필요합니다.

다음 샘플 요청은 장치를 만드는 것입니다. (이 샘플에는 요청 본문이 있는 입력 JSON이 있습니다.

```bash
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  "accept: application/json" -H  "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  \"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

## <a name="data-format"></a>데이터 형식

JSON은 임의의 데이터 구조의 간단한 텍스트 표현을 제공하는 공통 언어 독립적 인 데이터 형식입니다. 자세한 내용은 [json.org](http://json.org)를 참조하십시오.

## <a name="metadata-specifications"></a>메타데이터 사양

FarmBeats Datahub에는 장치 파트너가 장치 또는 센서 메타데이터를 만들고 관리할 수 있는 다음 API가 있습니다.

- /**DeviceModel**: DeviceModel은 게이트웨이 또는 노드인 제조업체 및 장치 유형과 같은 장치의 메타데이터에 해당합니다.
- /**장치**: 장치는 팜에 있는 물리적 장치에 해당합니다.
- /**센서 모델**: SensorModel은 제조업체, 아날로그 또는 디지털 센서 유형, 주변 온도 및 압력과 같은 센서 측정과 같은 센서의 메타데이터에 해당합니다.
- /**센서**: 센서는 값을 기록하는 물리적 센서에 해당합니다. 센서는 일반적으로 장치 ID가 있는 장치에 연결됩니다.

  **장치 모델** |  |
  --- | ---
  유형(노드, 게이트웨이)  | 장치 유형 - 노드 또는 게이트웨이 |
  제조업체  | 제조업체 이름 |
  Productcode  | 장치 제품 코드 또는 모델 이름 또는 번호. 예를 들어, 엔비로모니터#6800. |
  포트  | 디지털 또는 아날로그 포트 이름 및 유형입니다.  |
  속성  | 리소스를 식별할 이름입니다. 예를 들어 모델 이름 또는 제품 이름입니다. |
  Description  | 모델에 대한 의미 있는 설명을 제공합니다. |
  속성  | 제조업체의 추가 속성입니다. |
  **디바이스** |  |
  장치 모델 ID  |연결된 장치 모델의 ID입니다. |
  하드웨어 ID   |MAC 주소와 같은 장치의 고유 ID입니다.  |
  ReportingInterval |보고 간격을 초 단위로 보고합니다. |
  위치    |장치 위도(-90 ~ +90), 경도(-180 ~ 180) 및 고도(미터). |
  부모장치 ID | 이 장치가 연결된 상위 장치의 ID입니다. 예를 들어 노드가 게이트웨이에 연결되어 있는 경우 노드에는 parentDeviceID가 게이트웨이로 있습니다. |
  속성  | 리소스를 식별하는 이름입니다. 장치 파트너는 장치 파트너 측의 장치 이름과 일치하는 이름을 보내야 합니다. 장치 이름이 장치 파트너 측에서 사용자 정의된 경우 동일한 사용자 정의 이름을 FarmBeats에 전파해야 합니다.  |
  Description  | 의미 있는 설명을 제공합니다.  |
  속성  |제조업체의 추가 속성입니다.  |
  **센서 모델** |  |
  유형(아날로그, 디지털)  |아날로그 또는 디지털 센서를 언급합니다.|
  제조업체  | 제조업체 이름입니다. |
  Productcode  | 제품 코드 또는 모델 이름 또는 번호. 예를 들어, RS-CO2-N01.  |
  센서> 이름 측정  | 센서 측정값의 이름입니다. 소문자만 지원됩니다. 다른 깊이에서 측정할 때 깊이를 지정합니다. 예를 들어, soil_moisture_15cm. 이 이름은 원격 분석 데이터와 일치해야 합니다. |
  센서데이터유형에> 측정  | 원격 분석 데이터 형식입니다. 현재 이중이 지원됩니다. |
  센서측정 > 유형  | 센서 원격 분석 데이터의 측정 유형입니다. 다음은 시스템 정의 유형입니다: 주변 온도, CO2, 깊이, 전기 전도성, 잎습지, 길이, 액체 레벨, 질산염, O2, PH, 인산염, PointInTime, 칼륨, 압력, RainGauge, 상대 습도, 염도, 토양 보습, 토양 온도, 태양 복사, 상태, 시간 지속 시간, UVRadiation, UVIndex, 볼륨, 풍풍, 풍풍, 풍도, 풍도, 풍도, 풍도, 풍사. 더 추가하려면 /ExtendedType API를 참조하십시오.
  센서측정 > 유닛 | 센서 원격 분석 데이터의 단위입니다. 다음은 시스템 정의 단위입니다: NoUnit, 섭씨, 화씨, 켈빈, 랭킨, 파스칼, 수은, PSI, 밀리미터, 센티미터, 미터, 인치, 피트, 마일, 킬로미터, 마일스퍼라, 마일퍼초, KMPerHour, KMPer초, 미터퍼초, 미터퍼초, 도, 와츠퍼스퀘어미터, 킬로와트퍼스퀘어미터, 밀리미터 MilliJoulesPerSquareCentIMeter, volumetricWaterContent, 백분율, partsPermillion, 마이크로몰, 마이크로몰퍼리터, 지멘스퍼스퀘어미터퍼몰, 밀리지멘스퍼센티미터, 센티바, 데시지멘스퍼미터, 킬로스칼, 체피트리에이온콘텐츠, 리터, 밀리리터, 초, 유니스타임스탬프, 마이크로펄퍼마켓퍼퍼퍼퍼퍼. 더 추가하려면 /ExtendedType API를 참조하십시오.
  센서측정> 집계유형  | 없음, 평균, 최대, 최소 또는 StandardDeviation 중 하나.
  센서> 깊이 측정  | 센티미터 의 센서 깊이. 예를 들어, 지하 10cm 의 수분 측정.
  센서측정 > 설명  | 측정에 대한 의미 있는 설명을 제공합니다.
  속성  | 리소스를 식별할 이름입니다. 예를 들어 모델 이름 또는 제품 이름입니다.
  Description  | 모델에 대한 의미 있는 설명을 제공합니다.
  속성  | 제조업체의 추가 속성입니다.
  **센서**  |  |
  하드웨어 ID  | 제조업체에서 설정한 센서의 고유 ID입니다.
  센서모델Id  | 관련 센서 모델의 ID입니다.
  위치  | 센서 위도(-90 ~ +90), 경도(-180 ~ 180) 및 고도(미터).
  포트 > 이름  |센서가 장치에 연결된 포트의 이름 및 유형입니다. 이 이름은 장치 모델에 정의된 것과 같아야 합니다.
  deviceId  | 센서가 연결된 장치의 ID입니다.
  속성  | 리소스를 식별하는 이름입니다. 예를 들어 센서 이름 또는 제품 이름, 모델 번호 또는 제품 코드입니다.
  Description  | 의미 있는 설명을 제공합니다.
  속성  | 제조업체의 추가 속성입니다.

 각 개체 및 해당 속성에 대한 자세한 내용은 [Swagger](https://aka.ms/FarmBeatsDatahubSwagger)를 참조하십시오.

 > [!NOTE]
 > API는 생성된 각 인스턴스에 대해 고유한 아이디를 반환합니다. 이 ID는 장치 관리 및 메타데이터 동기화를 위해 번역기에 의해 유지되어야 합니다.


**메타데이터 동기화**

번역기는 메타데이터에 대한 업데이트를 보내야 합니다. 예를 들어 업데이트 시나리오는 장치 또는 센서 이름 변경 및 장치 또는 센서 위치 변경입니다.

번역기는 FarmBeats의 사용자 게시물 링크에 의해 설치된 새 장치 또는 센서를 추가할 수 있어야 합니다. 마찬가지로 사용자가 장치 또는 센서를 업데이트한 경우 해당 장치 또는 센서에 대해 FarmBeats에서 동일한 장치 또는 센서를 업데이트해야 합니다. 장치 또는 센서를 업데이트해야 하는 일반적인 시나리오는 장치 위치의 변경 또는 노드에 센서를 추가하는 것입니다.


> [!NOTE]
> 기기 또는 센서 메타데이터에 는 삭제가 지원되지 않습니다.
>
> 메타데이터를 업데이트하려면 장치 또는 센서에서 /Get/{id}를 호출하고 변경된 속성을 업데이트한 다음 사용자가 설정한 속성이 손실되지 않도록 /Put/{id}를 수행하는 것이 필수입니다.

### <a name="add-new-types-and-units"></a>새 유형 및 단위 추가

FarmBeats는 새로운 센서 측정 유형 및 단위 추가를 지원합니다. /ExtendedType API에 대한 자세한 내용은 [Swagger](https://aka.ms/FarmBeatsSwagger)를 참조하십시오.

## <a name="telemetry-specifications"></a>원격 분석 사양

원격 분석 데이터는 처리를 위해 Azure Event Hubs에 게시된 표준 메시지에 매핑됩니다. Azure Event Hubs는 연결된 장치 및 응용 프로그램에서 실시간 데이터(원격 분석) 수집을 가능하게 하는 서비스입니다.

## <a name="send-telemetry-data-to-farmbeats"></a>팜비츠에 원격 분석 데이터 보내기

원격 분석 데이터를 FarmBeats에 보내려면 FarmBeats의 이벤트 허브에 메시지를 보내는 클라이언트를 만듭니다. 원격 분석 데이터에 대한 자세한 내용은 [이벤트 허브로 원격 분석 보내기](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send)를 참조하십시오.

다음은 클라이언트로 원격 분석을 지정된 이벤트 허브로 보내는 샘플 파이썬 코드입니다.

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

표준 메시지 형식은 다음과 같습니다.

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
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        }
      ]
    }
 ]
}
```
원격 분석 JSON의 모든 키 이름은 소문자여야 합니다. 예를 들어 장치 및 센서 데이터입니다.

예를 들어 원격 분석 메시지는 다음과 같습니다.


```json
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

> [!NOTE]
> 다음 섹션은 다른 변경 사항(예: UI, 오류 관리 등) 센서 파트너가 번역기 구성 요소를 개발할 때 참조할 수 있습니다.


## <a name="link-a-farmbeats-account"></a>팜비츠 계정 연결

고객이 장치 또는 센서를 구입하고 배포한 후에는 SaaS(서비스) 포털로서 장치 파트너의 소프트웨어에서 장치 데이터 및 원격 분석에 액세스할 수 있습니다. 장치 파트너는 고객이 다음 자격 증명을 입력하는 방법을 제공하여 Azure의 FarmBeats 인스턴스에 계정을 연결할 수 있도록 합니다.

   - 표시 이름(사용자가 이 통합의 이름을 정의할 수 있는 선택적 필드)
   - API 엔드포인트
   - 테넌트 ID
   - 클라이언트 ID
   - 클라이언트 암호
   - EventHub 연결 문자열
   - 시작 날짜

   > [!NOTE]
   > 시작 날짜는 기록 데이터 피드, 즉 사용자가 지정한 날짜의 데이터를 활성화합니다.

## <a name="unlink-farmbeats"></a>링크 해제 팜비트

장치 파트너를 사용하면 고객이 기존 FarmBeats 통합의 연결을 해제할 수 있습니다. FarmBeats를 연결 해제하면 FarmBeats Datahub에서 만든 장치 또는 센서 메타데이터를 삭제해서는 안 됩니다. 연결 해제는 다음을 수행합니다.

   - 원격 분석 흐름을 중지합니다.
   - 장치 파트너의 통합 자격 증명을 삭제하고 지웁습니다.

## <a name="edit-farmbeats-integration"></a>팜비츠 통합 편집

장치 파트너는 클라이언트 비밀 또는 연결 문자열이 변경되는 경우 고객이 FarmBeats 통합 설정을 편집할 수 있도록 할 수 있습니다. 이 경우 다음 필드만 편집할 수 있습니다.

   - 표시 이름(해당하는 경우)
   - 클라이언트 보안 정보(일반 텍스트가 아닌 "2x8*******" 형식 또는 표시/숨기기 기능으로 표시되어야 합니다.)
   - 연결 문자열(일반 텍스트가 아닌 "2x8*******" 형식 또는 표시/숨기기 기능으로 표시되어야 합니다.

## <a name="view-the-last-telemetry-sent"></a>보낸 마지막 원격 분석 보기

장치 파트너를 사용하면 고객이 전송된 마지막 원격 분석의 타임스탬프를 볼 수 있으며, 이 타임스탬프는 **원격 분석 전송**에서 찾을 수 있습니다. 최신 원격 분석이 FarmBeats로 성공적으로 전송된 시간입니다.

## <a name="troubleshooting-and-error-management"></a>문제 해결 및 오류 관리

**옵션 또는 지원 문제 해결**

고객이 지정된 FarmBeats 인스턴스에서 장치 데이터 또는 원격 분석을 수신할 수 없는 경우 장치 파트너는 문제 해결을 위한 지원 및 메커니즘을 제공해야 합니다.

**원격 분석 데이터 보존**

오류 나 데이터 손실이 발생할 경우 원격 분석을 디버깅하거나 다시 보내는 데 유용할 수 있도록 미리 정의된 기간 동안 원격 분석 데이터를 유지해야 합니다.

**오류 관리 또는 오류 알림**

오류가 장치 또는 센서 메타데이터 또는 장치 파트너 시스템의 데이터 통합 또는 원격 분석 데이터 흐름에 영향을 미치는 경우 고객에게 알림을 받아야 합니다. 오류를 해결하는 메커니즘도 설계및 구현해야 합니다.

**연결 체크리스트**

장치 제조업체 또는 파트너는 다음 검사 목록을 사용하여 고객이 제공한 자격 증명이 정확한지 확인할 수 있습니다.

   - 제공된 자격 증명을 사용하여 액세스 토큰이 수신되는지 확인합니다.
   - 수신된 액세스 토큰으로 API 호출이 성공하는지 확인합니다.
   - EventHub 클라이언트 연결이 설정되었는지 확인합니다.

## <a name="next-steps"></a>다음 단계

REST API에 대한 자세한 내용은 [REST API를](rest-api-in-azure-farmbeats.md)참조하십시오.
