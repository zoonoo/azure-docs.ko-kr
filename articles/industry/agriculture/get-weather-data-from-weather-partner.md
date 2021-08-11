---
title: 날씨 파트너로부터 날씨 데이터 가져오기
description: 이 문서에서는 파트너로부터 날씨 데이터를 얻는 방법을 설명합니다.
author: sunasing
ms.topic: article
ms.date: 03/31/2020
ms.author: sunasing
ms.openlocfilehash: bb28c517e353af6b8c1ee0cad788ff41b971918c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96460875"
---
# <a name="get-weather-data-from-weather-partners"></a>날씨 파트너로부터 날씨 데이터 가져오기

Azure FarmBeats는 Docker 기반 커넥터 프레임워크를 사용하여 날씨 데이터 공급자로부터 날씨 데이터를 가져오는 데 도움이 됩니다. 이 프레임워크를 사용하여 날씨 데이터 공급자는 FarmBeats와 통합할 수 있는 Docker를 구현합니다. 현재 다음 날씨 데이터 공급자가 지원됩니다.

  ![FarmBeats 파트너](./media/get-sensor-data-from-sensor-partner/dtn-logo.png)
  
   [DTN](https://www.dtn.com/dtn-content-integration/)

날씨 데이터를 사용하여 FarmBeats에서 실행 가능한 인사이트를 생성하고 AI 또는 ML 모델을 빌드할 수 있습니다.

## <a name="before-you-start"></a>시작하기 전에

날씨 데이터를 가져오려면 [FarmBeats를 설치](./install-azure-farmbeats.md)했는지 확인합니다. 날씨 통합은 1.2.11 이상 버전에서 지원 됩니다. 

## <a name="enable-weather-integration-with-farmbeats"></a>FarmBeats와 날씨 통합 사용

FarmBeats Datahub에 대한 날씨 데이터 가져오기를 시작하려면 다음을 수행합니다.

1. FarmBeats Datahub Swagger `https://farmbeatswebsite-api.azurewebsites.net/swagger`로 이동합니다.

2. /Partner API로 이동한 다음, POST 요청을 수행합니다. 다음 입력 페이로드를 사용합니다.

   ```json
   {  
 
     "dockerDetails": {  
       "credentials": { 
         "username": "<credentials to access private docker - not required for public docker>", 
         "password": "<credentials to access private docker – not required for public docker>"  
       },  
       "imageName" : "<docker image name",
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

   예를 들어 DTN에서 날씨 데이터를 가져오려면 다음 페이로드를 사용합니다. 선호도에 따라 이름 및 설명을 수정할 수 있습니다.

   > [!NOTE]
   > 다음 단계에는 API 키가 필요합니다. DTN 구독에 대한 키를 가져오려면 DTN에 문의하세요.

   ```json
   {
 
     "dockerDetails": {
       "imageName": "dtnweather/dtn-farm-beats",
       "imageTag": "latest",
       "azureBatchVMDetails": {
         "batchVMSKU": "standard_d2_v2",
         "dedicatedComputerNodes": 1,
         "nodeAgentSKUID": "batch.node.ubuntu 18.04"
       }
     },
     "partnerCredentials": {
      "apikey": "<API key from DTN>"
      },
     "partnerType": "Weather",
     "name": "dtn-weather",
     "description": "DTN registered as a Weather Partner in FarmBeats"
   }  
   ```

   > [!NOTE]
   > 파트너 개체에 대한 자세한 내용은 이 문서의 [부록](get-weather-data-from-weather-partner.md#appendix)을 참조하세요.

   이전 단계는 Docker가 고객의 FarmBeats 환경에서 실행될 수 있도록 리소스를 프로비저닝합니다.  

   리소스를 프로비저닝하는 데 약 10~15분이 걸립니다.

3. 이전 단계에서 만든 /Partner 개체의 상태를 확인합니다. 상태를 확인하려면 /Partner API에 대해 GET 요청을 수행하고 파트너 개체의 상태를 확인합니다. FarmBeats가 파트너를 성공적으로 프로비저닝하면 상태가 **활성** 으로 설정됩니다.

4. /JobType API에서 GET 요청을 수행합니다. 파트너 추가 프로세스에서 이전에 만든 날씨 작업을 확인합니다. 날씨 작업에서 **pipelineName** 필드는 **partner-name_partner-type_job-name** 형식입니다.

      이제 FarmBeats 인스턴스에 활성 날씨 데이터 파트너가 있습니다. 작업을 실행하여 특정 위치(위도 및 경도) 및 날짜 범위에 대한 날씨 데이터를 요청할 수 있습니다. 작업 유형에는 날씨 작업을 실행하는 데 필요한 매개 변수에 대한 세부 정보가 있습니다.

      예를 들어, DTN의 경우 다음 작업 유형이 만들어집니다.
   
      - **get_dtn_daily_observations**: 위치 및 기간에 대한 일일 관찰 결과를 가져옵니다.
      - **get_dtn_daily_forecasts**: 위치 및 기간에 대한 일일 예측 결과를 가져옵니다.
      - **get_dtn_hourly_observations**: 위치 및 기간에 대한 시간별 관찰 결과를 가져옵니다.
      - **get_dtn_hourly_forecasts**: 위치 및 기간에 대한 시간별 예측 결과를 가져옵니다.

6. 작업 유형의 ID 및 매개 변수를 기록해 둡니다.

7. /Jobs API로 이동하여 /Jobs에 대해 POST 요청을 수행합니다. 다음 입력 페이로드를 사용합니다.

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

   예를 들어 **get_dtn_daily_observations** 를 실행하려면 다음 페이로드를 사용합니다.

   ```json
   { 
         "typeId": "<id of the JobType>",
         "arguments": {
               "latitude": 47.620422,
               "longitude": -122.349358,
               "days": 5
         },
         "name": "<name of the job>",
         "description": "<description>",
         "properties": {}
   }
   ```

8. 이전 단계에서는 파트너 Docker에 정의된 대로 날씨 작업을 실행하고 FarmBeats에 날씨 데이터를 수집합니다. /Jobs에서 GET 요청을 수행하여 작업의 상태를 확인할 수 있습니다. 응답에서 **currentState** 를 찾습니다. 완료되면 **currentState** 가 **Succeeded** 로 설정됩니다.

## <a name="query-ingested-weather-data"></a>수집된 날씨 데이터 쿼리

날씨 작업이 완료되면 수집한 날씨 데이터를 쿼리하여 FarmBeats Datahub REST API를 통해 모델 또는 실행 가능한 인사이트를 작성할 수 있습니다.

FarmBeats REST API를 사용하여 날씨 데이터를 쿼리하려면 다음을 수행합니다.

1. FarmBeats Datahub [Swagger](https://yourdatahub.azurewebsites.net/swagger)에서 /WeatherDataLocation API로 이동하여 GET 요청을 수행합니다. 응답에는 작업 실행에 지정된 위치(위도 및 경도)에 대해 만들어진/WeatherDataLocation 개체가 포함됩니다. 개체의 **ID** 와 **weatherDataModelId** 를 적어 둡니다.

2. 이전처럼 **weatherDataModelId** 에 대해 /WeatherDataModel API에서 GET/{id} 요청을 수행합니다. 날씨 데이터 모델은 수집된 날씨 데이터에 대한 모든 메타데이터 및 세부 정보를 표시합니다. 예를 들어 날씨 데이터 모델 개체에서 날씨 측정은 지원되는 날씨 정보 및 유형과 단위에 대해 자세히 설명합니다. 예를 들면 다음과 같습니다.

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

   날씨 데이터 모델에 대한 GET/{id} 호출의 응답을 기록해 둡니다.

3. 원격 분석 API로 이동하여 POST 요청을 수행합니다. 다음 입력 페이로드를 사용합니다.

   ```json
   {
       "weatherDataLocationId": "<id from step 1 above>",
     "searchSpan": {
       "from": "2020-XX-XXT07:30:00Z",
       "to": "2020-XX-XXT07:45:00Z"
     }
   }
   ```

    응답은 지정된 시간 범위에 사용할 수 있는 날씨 데이터를 표시합니다.

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

위의 예제에서 응답은 두 타임스탬프에 대한 데이터를 표시합니다. 또한 두 타임스탬프에 보고된 날씨 데이터의 측정 이름(온도) 및 값도 표시합니다. 보고된 값의 유형 및 단위를 해석하려면 연결된 날씨 데이터 모델을 참조하세요.

## <a name="troubleshoot-job-failures"></a>작업 실패 문제 해결

작업 실패 문제를 해결하려면 [작업 로그를 확인](troubleshoot-azure-farmbeats.md#weather-data-job-failures)합니다.


## <a name="appendix"></a>부록

|        파트너   |  세부 정보   |
| ------- | -------             |
|     DockerDetails - imageName         |          Docker 이미지 이름입니다. 예를 들어 docker.io/mydockerimage(hub.docker.com 이미지) 또는 myazureacr.azurecr.io/mydockerimage(Azure Container Registry 이미지) 등이 있습니다. 레지스트리가 제공되지 않으면 기본값은 hub.docker.com입니다.      |
|          DockerDetails - imageTag             |         Docker 이미지의 태그 이름입니다. 기본값은 "latest"입니다.     |
|  DockerDetails - 자격 증명      |  프라이빗 Docker에 액세스하기 위한 자격 증명입니다. 파트너는 자격 증명을 제공합니다.   |
|  DockerDetails - azureBatchVMDetails - batchVMSKU     |    Azure Batch VM SKU. 자세한 내용은 [사용 가능한 모든 Linux 가상 머신](../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)을 참조하세요. <BR> <BR> 유효한 값은 'Small', 'ExtraLarge', 'Large', 'A8', 'A9', 'Medium', 'A5', 'A6', 'A7', 'STANDARD_D1', 'STANDARD_D2', 'STANDARD_D3', 'STANDARD_D4', 'STANDARD_D11', 'STANDARD_D12', 'STANDARD_D13', 'STANDARD_D14', 'A10', 'A11', 'STANDARD_D1_V2', 'STANDARD_D2_V2', 'STANDARD_D3_V2', 'STANDARD_D4_V2', 'STANDARD_D11_V2', 'STANDARD_D12_V2', 'STANDARD_D13_V2', 'STANDARD_D14_V2', 'STANDARD_G1', 'STANDARD_G2', 'STANDARD_G3', 'STANDARD_G4', 'STANDARD_G5', 'STANDARD_D5_V2', 'BASIC_A1', 'BASIC_A2', 'BASIC_A3', 'BASIC_A4', 'STANDARD_A1', 'STANDARD_A2', 'STANDARD_A3', 'STANDARD_A4', 'STANDARD_A5', 'STANDARD_A6', 'STANDARD_A7', 'STANDARD_A8', 'STANDARD_A9', 'STANDARD_A10', 'STANDARD_A11', 'STANDARD_D15_V2', 'STANDARD_F1', 'STANDARD_F2', 'STANDARD_F4', 'STANDARD_F8', 'STANDARD_F16', 'STANDARD_NV6', 'STANDARD_NV12', 'STANDARD_NV24', 'STANDARD_NC6', 'STANDARD_NC12', 'STANDARD_NC24', 'STANDARD_NC24r', 'STANDARD_H8', 'STANDARD_H8m', 'STANDARD_H16', 'STANDARD_H16m', 'STANDARD_H16mr', 'STANDARD_H16r', 'STANDARD_A1_V2', 'STANDARD_A2_V2', 'STANDARD_A4_V2', 'STANDARD_A8_V2', 'STANDARD_A2m_V2', 'STANDARD_A4m_V2', 'STANDARD_A8m_V2', 'STANDARD_M64ms', 'STANDARD_M128s' 및 'STANDARD_D2_V3'입니다. *기본값은 'STANDARD_D2_V2'입니다.*  |
|    DockerDetails - azureBatchVMDetails - dedicatedComputerNodes   |  일괄 처리 풀당 전용 컴퓨터 노드 수입니다. 기본값은 1입니다. |
|    DockerDetails - azureBatchVMDetails - nodeAgentSKUID          |    Azure Batch 노드 에이전트 SKU ID입니다. 현재 "batch. ubuntu 18.04" 일괄 처리 노드 에이전트만 지원됩니다.    |
| DockerDetails - partnerCredentials | Docker에서 파트너 API를 호출하기 위한 자격 증명입니다. 파트너는 지원되는 권한 부여 메커니즘(예: 사용자 이름과 암호 또는 API 키)을 기준으로 이 정보를 제공합니다. |
| partnerType | "Weather". FarmBeats의 다른 파트너 유형은 "Sensor" 및 "Imagery"입니다.  |
|  name   |   FarmBeats 시스템에서 파트너의 원하는 이름입니다.   |
|  description |  설명   |

## <a name="next-steps"></a>다음 단계

지금까지 Azure FarmBeats 인스턴스에서 센서 데이터를 쿼리했으므로 팜에 대한 [맵 생성](generate-maps-in-azure-farmbeats.md#generate-maps) 방법을 알아봅니다.