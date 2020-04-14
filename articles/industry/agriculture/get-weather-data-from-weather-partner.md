---
title: 날씨 파트너로부터 날씨 데이터 얻기
description: 이 문서에서는 파트너로부터 날씨 데이터를 얻는 방법에 대해 설명합니다.
author: sunasing
ms.topic: article
ms.date: 03/31/2020
ms.author: sunasing
ms.openlocfilehash: 66fa4e7d3edf839ab1e497e86362bcfc979dc279
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81266163"
---
# <a name="get-weather-data-from-weather-partners"></a>날씨 파트너로부터 날씨 데이터 얻기

Azure FarmBeats를 사용하면 도커 기반 커넥터 프레임워크를 사용하여 날씨 데이터 공급자로부터 날씨 데이터를 가져올 수 있습니다. 날씨 데이터 공급자는 이 프레임워크를 사용하여 FarmBeats와 통합할 수 있는 도커를 구현합니다. 현재 다음과 같은 날씨 데이터 공급자가 지원됩니다.

[Azure 오픈 데이터 집합의 NOAA 데이터](https://azure.microsoft.com/services/open-datasets/)

날씨 데이터를 사용하여 실행 가능한 통찰력을 생성하고 FarmBeats에서 AI/ML 모델을 빌드할 수 있습니다.

## <a name="before-you-start"></a>시작하기 전에

날씨 데이터를 얻으려면 FarmBeats를 설치했습니다. **날씨 통합은 버전 1.2.11 이상에서 지원됩니다.** Azure FarmBeats를 설치하려면 [FarmBeats 설치를](https://aka.ms/farmbeatsinstalldocumentation)참조하십시오.

## <a name="enable-weather-integration-with-farmbeats"></a>FarmBeats와 날씨 통합 활성화

FarmBeats 데이터 허브에서 날씨 데이터를 가져오는 방법을 보려면 다음 단계를 따르세요.

1. FarmBeats 데이터 허브 스웨거(https://yourdatahub.azurewebsites.net/swagger)

2. /Partner API로 이동하여 다음 입력 페이로드를 사용하여 POST 요청을 합니다.

   ```json
   {  
 
     "dockerDetails": {  
       "credentials": { 
         "username": "<credentials to access private docker - not required for public docker>", 
         "password": "<credentials to access private docker – not required for public docker>"  
       },  
       "imageName" : "<docker image name. Default is azurefarmbeats/fambeats-noaa>",
       "imageTag" : "<docker image tag, default:latest>",
       "azureBatchVMDetails": {  
         "batchVMSKU" : "<VM SKU. Default is standard_d2_v2>",  
         "dedicatedComputerNodes" : 1,
         "nodeAgentSKUID": "<Node SKU. Default is batch.node.ubuntu 18.04>"  
       }
     },
     "partnerCredentials": {  
       "key1": "value1",  
       "key2": "value2"  
     },  
     "partnerType": "Weather",  
     "name": "<Name of the partner>",  
     "description": "<Description>",
     "properties": {  }  
   }  
   ```

   예를 들어 Azure Open 데이터 집합별로 NOAA의 날씨 데이터를 얻으려면 아래 페이로드를 사용합니다. 기본 설정에 따라 이름과 설명을 수정할 수 있습니다.

   ```json
   {
 
     "dockerDetails": {
       "imageName": "azurefarmbeats/farmbeats-noaa",
       "imageTag": "latest",
       "azureBatchVMDetails": {
         "batchVMSKU": "standard_d2_v2",
         "dedicatedComputerNodes": 1,
         "nodeAgentSKUID": "batch.node.ubuntu 18.04"
       }
     },
     "partnerType": "Weather",
     "name": "ods-noaa",
     "description": "NOAA data from Azure Open Datasets registered as a Weather Partner"
   }  
   ```

   > [!NOTE]
   > 파트너 개체에 대한 자세한 내용은 [부록을](get-weather-data-from-weather-partner.md#appendix) 참조하십시오.

   앞의 단계에서는 고객의 FarmBeats 환경에서 docker를 실행할 수 있도록 리소스를 프로비전합니다.  

   위의 리소스를 프로비전하는 데 약 10-15분이 걸립니다.

3. 2단계에서 만든 /Partner 개체의 상태를 확인합니다. 이렇게 하려면 /파트너 API에서 GET 요청을 하고 파트너 개체의 **상태를** 확인합니다. FarmBeats가 파트너를 성공적으로 프로비전하면 상태가 **활성으로**설정됩니다.

4. /JobType API로 이동하여 동일한 GET 요청을 합니다. 2단계에서 파트너 추가 프로세스의 일부로 생성된 날씨 작업을 확인합니다. 날씨 작업의 **파이프라인Name** 필드는 "파트너 name_partner-type_job 이름"입니다.

5. 이제 FarmBeats 인스턴스에는 활성 날씨 데이터 파트너가 있으며 작업을 실행하여 특정 위치(위도/경도) 및 날짜 범위에 대한 날씨 데이터를 요청할 수 있습니다. JobType(들)에는 날씨 작업을 실행하는 데 필요한 매개 변수에 대한 세부 정보가 있습니다.

   예를 들어 Azure Open 데이터 집합의 NOAA 데이터의 경우 JobType(들)에 따라 다음이 만들어집니다.

   - get_weather_data(ISD/과거 기상 데이터 얻기)
   - get_weather_forecast_data (GFS / 일기 예보 데이터 얻기)

6. **ID와** JobType(들)의 매개 변수를 기록합니다.

7. /Jobs API로 이동하여 다음 입력 페이로드를 사용하여 /Jobs에서 POST 요청을 수행합니다.

   ```json
    {
         "typeId": "<id of the JobType>",
         "arguments": {
           "additionalProp1": {},
           "additionalProp2": {},
           "additionalProp3": {}
         },
         "name": "<name of the job>",
         "description": "<description>",
         "properties": {}
       }
   ```

   예를 들어 **get_weather_data**실행하려면 다음 페이로드를 사용합니다.

   ```json
   {
 
         "typeId": "<id of the JobType>",
         "arguments": {
               "latitude": 47.620422,
               "longitude": -122.349358,
               "start_date": "yyyy-mm-dd",
               "end_date": "yyyy-mm-dd"
         },
         "name": "<name of the job>",
         "description": "<description>",
         "properties": {}
   }
   ```

8. 이전 단계에서는 파트너 도커에 정의된 대로 날씨 작업을 실행하고 FarmBeats에 날씨 데이터를 수집합니다. /Jobs에서 GET 요청을 수행하여 작업의 상태를 확인하고 응답에서 **현재 상태를** 찾을 수 있습니다. 완료되면 현재상태가 **성공으로**설정됩니다.

## <a name="query-ingested-weather-data"></a>수집된 날씨 데이터 쿼리

날씨 작업이 완료되면 수집된 날씨 데이터를 쿼리하여 모델 또는 실행 가능한 통찰력을 구축할 수 있습니다. FarmBeats에서 날씨 데이터에 액세스하고 쿼리하는 방법에는 두 가지가 있습니다.

- API 및
- TSI(타임시리즈 인사이트)

### <a name="query-using-rest-api"></a>REST API를 사용한 쿼리

FarmBeats REST API를 사용하여 날씨 데이터를 쿼리하려면 다음 단계를 따르십시오.

1. FarmBeats 데이터 허브 swagger에서 (,https://yourdatahub.azurewebsites.net/swagger)/WeatherDataLocation API로 이동 하 고 GET 요청을 합니다. 응답에는 작업 실행의 일부로 지정된 위치(위도/경도)에 대해 /WeatherDataLocation 개체가 만들어집니다. **개체의 ID와** **날씨데이터모델Id를** 기록합니다.

2. 1단계에서 설명한 대로 **weatherDataModelID에** 대한 /WeatherDataModel API에서 GET/{id}를 만듭니다. "날씨 데이터 모델"에는 수집된 날씨 데이터에 대한 모든 메타데이터와 세부 정보가 있습니다. 예를 들어 **날씨 데이터 모델** 개체 내의 날씨 **측정값에는** 지원되는 날씨 정보와 어떤 유형 및 단위에 대한 세부 정보가 있습니다. 예를 들면 다음과 같습니다.

   ```json
   {
       "name": "Temperature <name of the weather measure - this is what we will receive as part of the queried weather data>",
       "dataType": "Double <Data Type - eg. Double, Enum>",
       "type": "AmbientTemperature <Type of measure eg. AmbientTemperature, Wind speed etc.>",
       "unit": "Celsius <Unit of measure eg. Celsius, Percentage etc.>",
       "aggregationType": "None <either of None, Average, Maximum, Minimum, StandardDeviation, Sum, Total>",
       "description": "<Description of the measure>"
   }
   ```

   날씨 데이터 모델에 대한 GET/{id} 호출의 응답을 기록합니다.

3. 원격 **분석** API로 이동하여 다음 입력 페이로드를 사용하여 POST 요청을 합니다.

   ```json
   {
       "weatherDataLocationId": "<id from step 1 above>",
     "searchSpan": {
       "from": "2020-XX-XXT07:30:00Z",
       "to": "2020-XX-XXT07:45:00Z"
     }
   }
   ```

4. 지정된 시간 범위에 사용할 수 있는 날씨 데이터가 포함된 응답은 다음과 같습니다.

   ```json
   {
     "timestamps": [
       "2020-XX-XXT07:30:00Z",
       "2020-XX-XXT07:45:00Z"
     ],
     "properties": [
       {
         "values": [
           "<id of the weatherDataLocation>",
           "<id of the weatherDataLocation>"
         ],
         "name": "Id",
         "type": "String"
       },
       {
         "values": [
           29.1,
           30.2
         ],
         "name": "Temperature <name of the WeatherMeasure as defined in the WeatherDataModel object>",
         "type": "Double <Data Type of the value - eg. Double>"
       }
     ]
   }
   ```

앞의 예제에서 응답에는 측정값 이름("온도")과 두 타임스탬프에 보고된 날씨 데이터의 값과 함께 두 개의 타임스탬프에 대한 데이터가 있습니다. 보고된 값의 유형과 단위를 해석하려면 관련 날씨 데이터 모델(위의 2단계에서 설명한 대로)을 참조해야 합니다.

### <a name="query-using-azure-time-series-insights-tsi"></a>Azure 열열 인사이트(TSI)를 사용한 쿼리

FarmBeats는 [Azure Time Series Insights(TSI)를](https://azure.microsoft.com/services/time-series-insights/) 사용하여 시간계에 대해 컨텍스트화되고 최적화된 IoT 규모 데이터에서 데이터를 수집, 저장, 쿼리 및 시각화합니다.

날씨 데이터는 EventHub에서 수신된 다음 FarmBeats 리소스 그룹 내의 TSI 환경으로 푸시됩니다. 그런 다음 TSI에서 데이터를 직접 쿼리할 수 있습니다. 자세한 내용은 [TSI 문서를](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-explorer)참조하십시오.

TSI에서 데이터를 시각화하는 단계를 따릅니다.

1. Azure **포털** > **FarmBeats DataHub 리소스 그룹** > 데이터 액세스 **정책을**> 타임 **시리즈 인사이트** 환경(tsi-xxxx)을 선택합니다. 독자 또는 기여자 액세스 권한이 있는 사용자를 추가합니다.

2. **타임시리즈 인사이트** 환경(tsi-xxxx)의 **개요** 페이지로 이동하여 **타임시리즈 인사이트 탐색기 URL을**선택합니다. 이제 수집된 날씨 데이터를 시각화할 수 있습니다.

TSI는 날씨 데이터의 저장, 쿼리 및 시각화 외에도 Power BI 대시보드에 통합할 수 있습니다. 자세한 내용은 [Power BI의 타임시리즈 인사이트에서 데이터 시각화를](https://docs.microsoft.com/azure/time-series-insights/how-to-connect-power-bi)참조하세요.

## <a name="appendix"></a>부록

|        파트너   |  세부 정보   |
| ------- | -------             |
|     도커 세부 정보 - 이미지 이름         |          도커 이미지 이름입니다. 예를 들어 docker.io/azurefarmbeats/farmbeats-noaa(hub.docker.com 이미지) 또는 myazureacr.azurecr.io/mydockerimage(Azure 컨테이너 레지스트리의 이미지) 등등입니다. 레지스트리가 제공되지 않으면 기본값은 hub.docker.com      |
|          도커 세부 정보 - 이미지 태그             |         도커 이미지의 태그 이름입니다. 기본값은 "최신"     |
|  Docker세부 사항 - 자격 증명      |  개인 도커에 액세스할 수 있는 자격 증명입니다. 이는 파트너가 고객에게 제공합니다.   |
|  도커 세부 정보 - azureBatchVM 세부 정보 - 배치VMSKU     |    Azure 일괄 처리 VM SKU. 사용 가능한 모든 Linux 가상 머신은 [여기를](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 참조하십시오. 유효한 값은 '작음', '엑스트라라지', '큰', 'A8', 'A9', '보통', 'A5', 'A6', 'A6', 'STANDARD_D1', 'STANDARD_D2', 'STANDARD_D3', 'STANDARD_D4', 'STANDARD_D11 STANDARD_D4', 'STANDARD_D12', 'STANDARD_D13', 'STANDARD_D14', 'A10', 'A10', 'STANDARD_D1_V2 STANDARD_D2_V2', 'STANDARD_D2_V2', 'STANDARD_D3_V2', 'STANDARD_D4_V2', 'STANDARD_D11_V2', 'STANDARD_D12_V2', 'STANDARD_G4 STANDARD_G3 STANDARD_G2 STANDARD_G1 STANDARD_D14_V2 STANDARD_D13_V2' 'STANDARD_D13_V2' 'STANDARD_D13_V2' ' STANDARD_G5', 'STANDARD_D5_V2', 'BASIC_A1', 'BASIC_A2', 'BASIC_A3', 'BASIC_A4', 'STANDARD_A1', 'STANDARD_A2 STANDARD_A3', 'STANDARD_A4', 'STANDARD_A5 STANDARD_A4', 'STANDARD_A6', 'STANDARD_A7', 'STANDARD_A8', 'STANDARD_A9 STANDARD_A8', 'STANDARD_A10', 'STANDARD_A11', 'STANDARD_D15_V2 STANDARD_A11', 'STANDARD_F1', 'STANDARD_F2', 'STANDARD_F2', 'STANDARD_F4 STANDARD_F16 STANDARD_F8', 'STANDARD_NC24r STANDARD_NC24 STANDARD_NC12 STANDARD_NC6 STANDARD_NV12 STANDARD_NV6', 'STANDARD_NV6', 'STANDARD_NV6', 'STANDARD_NV24'BASIC_A3', 'STANDARD_NV24', 'STANDARD_NV24', 'STANDARD_NV24', 'STANDARD_NV24', 'STANDARD_NV24', 'STANDARD_NV24', 'STANDARD_NV24', 'STANDARD_NV24', 'STANDARD_NV24', 'STANDARD_NV24', 'STANDARD_NV24', 'STANDARD_A8', 'STANDARD_A8', 'STANDARD_A10', 'STANDARD_A11', 'STANDARD_NV24', 'STANDARD_NV24', 'STANDARD_NV24', 'STANDARD_NV24', 'STANDARD_NV24', 'STANDARD_NV24', 'STANDARD_NV24', 'STANDARD_NV24', 'STANDARD_NV24', 'STANDARD_NV24', 'STANDARD_NV6', 'BASIC_A3', 'STANDARD_A1', 'STANDARD_A3', 'STANDARD_A4', 'STANDARD_A6', 'STANDARD_A7', 'STANDARD_A8', 'STANDARD_A10', 'STANDARD_A11', 'STANDARD_F1', 'STANDARD_A1', 'STANDARD_NV24', 'STANDARD_NV24', 'STANDARD_NV24', 'STANDARD_NV24', 'STANDARD_NV24', 'STANDARD_NV24', 'STANDARD_NV24', 'STANDARD_NV24', 'STANDARD_NV24', 'STANDARD_NV24', 'STANDARD_NV24', 'STANDARD_NV24', 'STANDARD_NV24', 'STANDARD_NV24', 'STANDARD_NV24', 'STANDARD_NV24', 'STANDARD_NV24', 'STANDARD_NV24', 'STANDARD_NV24', 'STANDARD_NV24', 'STANDARD_NV24', ' 'STANDARD_H8', 'STANDARD_H8m', 'STANDARD_H16', 'STANDARD_H16m', 'STANDARD_H16mr', 'STANDARD_H16r', 'STANDARD_A1_V2', 'STANDARD_A2_V2', 'STANDARD_A4_V2', 'STANDARD_A8_V2', 'STANDARD_A2m_V2', 'STANDARD_A4m_V2', 'STANDARD_A8m_V2', 'STANDARD_M64ms', 'STANDARD_M128s', 'STANDARD_D2_V3'. **기본값은 "standard_d2_v2"입니다.**  |
|    도커 세부 정보 - azureBatchVM세부 사항 - 전용컴퓨터 노드   |  아니요. 배치 풀전용 컴퓨터 노드의 기본값은 1입니다. |
|    도커 세부 정보 - azureBatchVM 세부 정보 - 노드 에이전트SKUID          |    Azure 일괄 처리 노드 에이전트 SKU ID입니다. 현재 "batch.node.ubuntu 18.04" 배치 노드 에이전트만 지원됩니다.    |
| Docker세부 사항 - 파트너 자격 증명 | docker에서 파트너 API호출을 위한 자격 증명을 사용할 수 있습니다. 파트너는 예를 들어 지원되는 인증 메커니즘을 기반으로 고객에게 이 정보를 제공해야 합니다. 사용자 이름/암호 또는 API 키. |
| 파트너 유형 | "날씨"(FarmBeats의 다른 파트너 유형은 "센서" 및 "이미지")  |
|  name   |   FarmBeats 시스템에서 파트너의 원하는 이름   |
|  description |  Description   |

## <a name="next-steps"></a>다음 단계

이제 Azure FarmBeats 인스턴스에서 센서 데이터를 쿼리했습니다. 이제 팜에 대한 [맵을 생성하는](generate-maps-in-azure-farmbeats.md#generate-maps) 방법을 알아봅니다.
