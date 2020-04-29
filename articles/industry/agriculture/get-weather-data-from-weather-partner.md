---
title: 날씨 파트너의 날씨 데이터 가져오기
description: 이 문서에서는 파트너에서 날씨 데이터를 가져오는 방법을 설명 합니다.
author: sunasing
ms.topic: article
ms.date: 03/31/2020
ms.author: sunasing
ms.openlocfilehash: 66fa4e7d3edf839ab1e497e86362bcfc979dc279
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81266163"
---
# <a name="get-weather-data-from-weather-partners"></a>날씨 파트너의 날씨 데이터 가져오기

Azure FarmBeats는 docker 기반 커넥터 프레임 워크를 사용 하 여 날씨 데이터 공급자의 날씨 데이터를 가져오는 데 도움이 됩니다. 이 프레임 워크를 사용 하 여 날씨 데이터 공급자는 FarmBeats와 통합할 수 있는 docker를 구현 합니다. 현재 지원 되는 날씨 데이터 공급자는 다음과 같습니다.

[Azure Open 데이터 집합의 NOAA 데이터](https://azure.microsoft.com/services/open-datasets/)

날씨 데이터를 사용 하 여 FarmBeats에서 실행 가능한 정보를 생성 하 고 AI/ML 모델을 빌드할 수 있습니다.

## <a name="before-you-start"></a>시작하기 전에

날씨 데이터를 얻으려면 FarmBeats를 설치 했는지 확인 합니다. **날씨 통합은 버전 1.2.11 이상에서 지원 됩니다**. Azure FarmBeats를 설치 하려면 [FarmBeats 설치](https://aka.ms/farmbeatsinstalldocumentation)를 참조 하세요.

## <a name="enable-weather-integration-with-farmbeats"></a>FarmBeats와 날씨 통합 사용

FarmBeats 데이터 허브에서 날씨 데이터 가져오기를 시작 하려면 다음 단계를 수행 합니다.

1. FarmBeats Data hub swagger로 이동 합니다 (https://yourdatahub.azurewebsites.net/swagger)

2. /파트너 API로 이동 하 고 다음 입력 페이로드를 사용 하 여 POST 요청을 수행 합니다.

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

   예를 들어 Azure Open 데이터 집합에서 NOAA의 날씨 데이터를 가져오려면 아래 페이로드를 사용 합니다. 기본 설정에 따라 이름 및 설명을 수정할 수 있습니다.

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
   > 파트너 개체에 대 한 자세한 내용은 [부록](get-weather-data-from-weather-partner.md#appendix) 을 참조 하십시오.

   이전 단계는 docker가 고객의 FarmBeats 환경에서 실행 될 수 있도록 리소스를 프로 비전 합니다.  

   위의 리소스를 프로 비전 하는 데 약 10-15 분이 소요 됩니다.

3. 2 단계에서 만든/Cpartner 개체의 상태를 확인 합니다. 이렇게 하려면/Spartner API에 대 한 GET 요청을 수행 하 고 파트너 개체의 **상태** 를 확인 합니다. FarmBeats가 파트너를 성공적으로 프로 비전 하면 상태가 **활성**으로 설정 됩니다.

4. /JobType API로 이동 하 고 동일한에 대 한 GET 요청을 수행 합니다. 2 단계에서 파트너 추가 프로세스의 일부로 생성 된 날씨 작업을 확인 합니다. 날씨 작업의 **pipelineName** 필드는 "partner-name_partner-type_job-name" 형식입니다.

5. 이제 FarmBeats 인스턴스에 활성 날씨 데이터 파트너가 있고, 작업을 실행 하 여 특정 위치 (위도/경도) 및 날짜 범위에 대 한 날씨 데이터를 요청할 수 있습니다. JobType는 날씨 작업을 실행 하는 데 필요한 매개 변수에 대 한 세부 정보를 포함 합니다.

   예를 들어 Azure Open 데이터 집합의 NOAA 데이터의 경우 다음 JobType이 만들어집니다.

   - get_weather_data (ISD/과거 날씨 데이터 가져오기)
   - get_weather_forecast_data (GFS/예측 날씨 데이터 가져오기)

6. JobType의 **ID** 와 매개 변수를 적어 둡니다.

7. /작업 API로 이동 하 고 다음 입력 페이로드를 사용 하 여/작업에 대 한 POST 요청을 만듭니다.

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

   예를 들어 **get_weather_data**를 실행 하려면 다음 페이로드를 사용 합니다.

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

8. 이전 단계에서는 파트너 docker에 정의 된 날씨 작업을 실행 하 고 날씨 데이터를 FarmBeats에 수집 합니다. /작업에 대 한 GET 요청을 수행 하 고 응답에서 **currentState** 을 찾아 작업 상태를 확인할 수 있습니다. 완료 되 면 currentState가 **Succeeded**로 설정 됩니다.

## <a name="query-ingested-weather-data"></a>수집 날씨 데이터 쿼리

날씨 작업이 완료 되 면 수집 날씨 데이터를 쿼리하여 모델 또는 실행 가능한 정보를 작성할 수 있습니다. FarmBeats에서 날씨 데이터에 액세스 하 고 쿼리 하는 방법에는 두 가지가 있습니다.

- API 및
- TSI (Time Series Insights).

### <a name="query-using-rest-api"></a>REST API를 사용 하 여 쿼리

FarmBeats REST API를 사용 하 여 날씨 데이터를 쿼리하려면 다음 단계를 수행 합니다.

1. FarmBeats Data hub swagger (https://yourdatahub.azurewebsites.net/swagger)에서/WeatherDataLocation API로 이동 하 여 GET 요청을 수행 합니다. 응답에는 작업 실행의 일부로 지정 된 위치 (위도/경도)에 대해 생성 된/WeatherDataLocation 개체가 포함 됩니다. 개체의 **ID** 와 **weatherDataModelId** 를 적어 둡니다.

2. 1 단계에서 설명한 대로 **weatherDataModelId** 에 대 한 GET/{id}를/WeatherDataModel API로 만듭니다. "날씨 데이터 모델"은 수집 날씨 데이터에 대 한 모든 메타 데이터 및 세부 정보를 포함 합니다. 예를 들어 **날씨 데이터 모델** 개체 내의 **날씨 측정값** 에는 지원 되는 날씨 정보 및 종류와 단위에 대 한 세부 정보가 있습니다. 예를 들면 다음과 같습니다.

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

   날씨 데이터 모델에 대 한 GET/{id} 호출의 응답을 기록해 둡니다.

3. **원격 분석** API로 이동 하 고 다음 입력 페이로드를 사용 하 여 POST 요청을 만듭니다.

   ```json
   {
       "weatherDataLocationId": "<id from step 1 above>",
     "searchSpan": {
       "from": "2020-XX-XXT07:30:00Z",
       "to": "2020-XX-XXT07:45:00Z"
     }
   }
   ```

4. 지정 된 시간 범위에 사용할 수 있는 날씨 데이터를 포함 하는 응답은 다음과 같습니다.

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

앞의 예제에서 응답에는 두 타임 스탬프의 두 타임 스탬프에 대 한 데이터 ("온도")와 보고 된 날씨 데이터의 값이 있습니다. 위의 2 단계에서 설명한 대로 연결 된 날씨 데이터 모델을 참조 하 여 보고 된 값의 형식 및 단위를 해석 해야 합니다.

### <a name="query-using-azure-time-series-insights-tsi"></a>Azure Time Series Insights를 사용 하 여 쿼리 (TSI)

FarmBeats는 [TSI (Azure Time Series Insights)](https://azure.microsoft.com/services/time-series-insights/) 를 사용 하 여 IoT scale에서 데이터를 수집, 저장, 쿼리 및 시각화 합니다. 즉, 시계열에 대해 매우 까닭 되 고 최적화 된 데이터입니다.

날씨 데이터는 EventHub에서 수신 된 다음 FarmBeats 리소스 그룹 내의 TSI 환경으로 푸시됩니다. 그런 다음 TSI에서 직접 데이터를 쿼리할 수 있습니다. 자세한 내용은 [Tsi 설명서](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-explorer)를 참조 하세요.

TSI에서 데이터를 시각화 하는 단계를 수행 합니다.

1. **Azure Portal** > **FarmBeats datahub 리소스 그룹** 으로 이동 하 > **Time Series Insights** 환경 (tsi-xxxx) > **데이터 액세스 정책**을 선택 합니다. 판독기 또는 참가자 액세스 권한이 있는 사용자를 추가 합니다.

2. **Time Series Insights** 환경 (tsi-xxxx)의 **개요** 페이지로 이동 하 여 **Time Series Insights 탐색기 URL**을 선택 합니다. 이제 수집 날씨 데이터를 시각화할 수 있습니다.

TSI는 날씨 데이터의 저장, 쿼리 및 시각화 외에도 Power BI 대시보드를 통합할 수 있도록 합니다. 자세한 내용은 [Power BI의 Time Series Insights에서 데이터 시각화](https://docs.microsoft.com/azure/time-series-insights/how-to-connect-power-bi)를 참조 하세요.

## <a name="appendix"></a>부록

|        파트너   |  세부 정보   |
| ------- | -------             |
|     DockerDetails-imageName         |          Docker 이미지 이름입니다. 예를 들어 docker.io/azurefarmbeats/farmbeats-noaa (hub.docker.com의 이미지) 또는 myazureacr.azurecr.io/mydockerimage (Azure Container Registry 이미지) 등이 있습니다. 레지스트리를 제공 하지 않으면 기본값은 hub.docker.com입니다.      |
|          DockerDetails-imageTag             |         Docker 이미지의 태그 이름입니다. 기본값은 "최신"입니다.     |
|  DockerDetails-자격 증명      |  개인 docker에 액세스 하기 위한 자격 증명입니다. 파트너는 고객에 게 제공 됩니다.   |
|  DockerDetails-azureBatchVMDetails-batchVMSKU     |    VM SKU를 Azure Batch 합니다. 사용 가능한 모든 Linux virtual machines는 [여기](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 를 참조 하세요. 유효한 값: "Small ', ' ExtraLarge ', ' Large ', ' A8 ', ' A9 ', ' Medium ', ' A5 ', ' A6 ', ' A7 ', ' STANDARD_D1 ', ' STANDARD_D2 ', ' STANDARD_D3 ', ' STANDARD_D4 ', ' STANDARD_D11 ', ' STANDARD_D12 ', ' STANDARD_D13 ', ' STANDARD_D14 ', ' A10 ', ' A11 ', ' STANDARD_D1_V2 ', ' STANDARD_D2_V2 ', ' STANDARD_D3_V2 ', ' STANDARD_D4_V2 ', ' STANDARD_D11_V2 ', ' STANDARD_D12_V2 ', ' STANDARD_D13_V2 ', ' STANDARD_D14_V2 ', ' STANDARD_G1 ', ' STANDARD_G2 ', ' STANDARD_G3 ', ' STANDARD_G4 ', ' ' , ' STANDARD_G5 ', ' STANDARD_D5_V2 ', ' BASIC_A1 ', ' BASIC_A2 ', ' BASIC_A3 ', ' BASIC_A4 ', ' STANDARD_A1 ', ' STANDARD_A2 ', ' STANDARD_A3 ', ' STANDARD_A4 ', ' STANDARD_A5 ', ' STANDARD_A6 ', ' STANDARD_A7 ', ' STANDARD_A8 ', ' STANDARD_A9 ', ' STANDARD_A10 ', ' STANDARD_A11 ', ' STANDARD_D15_V2 ', ' STANDARD_F1 ', ' STANDARD_F2 ', ' STANDARD_F4 ', ' STANDARD_F8 ', ' STANDARD_F16 ', ' STANDARD_NV6 ', ' STANDARD_NV12 ', ' STANDARD_NV24 ' , ' STANDARD_H8 ', ' STANDARD_H8m ', ' STANDARD_H16 ', ' STANDARD_H16m ', ' STANDARD_H16mr ', ' STANDARD_H16r ', ' STANDARD_A1_V2 ', ' STANDARD_A2_V2 ', ' STANDARD_A4_V2 ', ' STANDARD_A8_V2 ', ' STANDARD_A2m_V2 ', ' STANDARD_A4m_V2 ', ' STANDARD_A8m_V2 ', ' STANDARD_M64ms ', ' STANDARD_M128s ', ' STANDARD_D2_V3 '. **기본값은 "standard_d2_v2"입니다.**  |
|    DockerDetails-azureBatchVMDetails-dedicatedComputerNodes   |  아니요. batch 풀에 대 한 전용 컴퓨터 노드. 기본값은 1입니다. |
|    DockerDetails-azureBatchVMDetails-nodeAgentSKUID          |    노드 에이전트 SKU ID를 Azure Batch 합니다. 현재 "batch. ubuntu 18.04" 일괄 처리 노드 에이전트도 지원 됩니다.    |
| DockerDetails-파트너 자격 증명 | docker에서 파트너 API를 호출 하기 위한 자격 증명입니다. 파트너는 예를 들어 지원 되는 인증 메커니즘을 기반으로 고객에 게이 정보를 제공 해야 합니다. 사용자 이름/암호 또는 API 키. |
| partnerType | "날씨" (FarmBeats의 다른 파트너 유형은 "센서" 및 "이미지")  |
|  name   |   FarmBeats 시스템에서 필요한 파트너의 이름입니다.   |
|  description |  Description   |

## <a name="next-steps"></a>다음 단계

이제 Azure FarmBeats 인스턴스에서 센서 데이터를 쿼리 했습니다. 이제 팜에 대 한 [맵을 생성](generate-maps-in-azure-farmbeats.md#generate-maps) 하는 방법에 대해 알아봅니다.
