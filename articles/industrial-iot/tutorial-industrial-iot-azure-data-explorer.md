---
title: Azure 산업용 IoT 데이터를 ADX로 끌어오기
description: 이 자습서에서는 IIoT 데이터를 ADX로 끌어오는 방법을 알아봅니다.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: 4c344dc09ad6c8aa4b2aa431952fc271d946b60d
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104787313"
---
# <a name="tutorial-pull-azure-industrial-iot-data-into-adx"></a>자습서: Azure 산업용 IoT 데이터를 ADX로 끌어오기

Azure IIoT(산업용 IoT) 플랫폼은 에지 모듈 및 클라우드 마이크로서비스를 다양한 Azure PaaS 서비스와 결합하여 산업 자산 검색 기능을 제공하고 OPC UA를 사용하여 이러한 자산에서 데이터를 수집합니다. [ADX(Azure Data Explorer)](https://docs.microsoft.com/azure/data-explorer)는 OPC 게시자를 통해 IoT Hub에 연결된 OPC UA 서버에서 수집된 데이터에 대해 유연한 쿼리를 실행할 수 있는 데이터 분석 기능을 갖춘 IIoT 데이터의 자연스러운 대상입니다. ADX 클러스터는 IoT Hub에서 직접 데이터를 수집할 수 있지만, IIoT 플랫폼은 마이크로서비스의 전체 배포를 사용할 때 제공되는 Event Hub에 데이터를 배치하기 전에 해당 데이터를 추가로 처리하여 더 유용하게 만듭니다(IIoT 플랫폼 아키텍처 참조).

이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.

> [!div class="checklist"]
> * ADX에서 테이블 만들기
> * Event Hub를 ADX 클러스터에 연결
> * ADX에서 데이터 분석

## <a name="how-to-make-the-data-available-in-the-adx-cluster-to-query-it-effectively"></a>ADX 클러스터에서 사용할 수 있도록 하여 데이터를 효과적으로 쿼리하는 방법 

Event Hub에서 메시지 형식(Microsoft.Azure.IIoT.OpcUa.Subscriber.Models.MonitoredItemMessageModel 클래스에서 정의됨)을 살펴보면 ADX 테이블 스키마에 필요한 구조에 대한 힌트를 확인할 수 있습니다.

![구조](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-1.png)

ADX 클러스터에서 데이터를 사용할 수 있도록 하고 데이터를 효과적으로 쿼리하는 데 필요한 단계는 다음과 같습니다.  
1. ADX 클러스터를 만듭니다. 이미 IIoT 플랫폼으로 프로비저닝된 ADX 클러스터가 없거나 다른 클러스터를 사용하려면 [여기](https://docs.microsoft.com/azure/data-explorer/create-cluster-database-portal#create-a-cluster)의 단계를 따르세요. 
2. [여기](https://docs.microsoft.com/azure/data-explorer/ingest-data-streaming#enable-streaming-ingestion-on-your-cluster)서 설명한 대로 ADX 클러스터에서 스트리밍 수집을 사용하도록 설정합니다. 
3. [여기](https://docs.microsoft.com/azure/data-explorer/create-cluster-database-portal#create-a-database)의 단계에 따라 ADX 데이터베이스를 만듭니다.

다음 단계에서는 [ADX 웹 인터페이스](https://docs.microsoft.com/azure/data-explorer/web-query-data)를 사용하여 필요한 쿼리를 실행합니다. 링크에서 설명한 대로 클러스터를 웹 인터페이스에 추가해야 합니다.  
 
4. ADX에서 수집된 데이터를 저장할 테이블을 만듭니다.  MonitoredItemMessageModel 클래스를 사용하여 ADX 테이블의 스키마를 정의할 수 있지만, 먼저 [Dynamic](https://docs.microsoft.com/azure/data-explorer/kusto/query/scalar-data-types/dynamic)(동적) 형식인 하나의 열이 있는 준비 테이블에 데이터를 수집하는 것이 좋습니다. 이를 통해 데이터를 처리하고 여러 사용 사례의 요구 사항을 충족하는 다른 테이블(잠재적으로 다른 데이터 원본과 결합)로 처리하는 데 더 많은 유연성을 제공할 수 있습니다. 다음 ADX 쿼리에서는 하나의 'payload' 열이 있는 'iiot_stage' 준비 테이블을 만듭니다.

```
.create table ['iiot_stage']  (['payload']:dynamic)
```

또한 json 수집 매핑을 추가하여 Event Hub의 전체 Json 메시지를 준비 테이블에 배치하도록 클러스터에 지시해야 합니다.

```
.create table ['iiot_stage'] ingestion json mapping 'iiot_stage_mapping' '[{"column":"payload","path":"$","datatype":"dynamic"}]'
```

5. 이제 테이블에서 Event Hub로부터 데이터를 받을 준비가 되었습니다. 
6. [여기](https://docs.microsoft.com/azure/data-explorer/ingest-data-event-hub#connect-to-the-event-hub)의 지침을 사용하여 Event Hub를 ADX 클러스터에 연결하고 준비 테이블로의 데이터 수집을 시작합니다. IIoT 플랫폼에서 프로비저닝한 Event Hub가 이미 있으므로 연결만 만들면 됩니다.  
7. 연결이 확인되면 데이터가 테이블에 전달되기 시작하고 짧은 지연 후에 테이블의 데이터 검사를 시작할 수 있습니다. ADX 웹 인터페이스에서 다음 쿼리를 사용하여 10개 행의 데이터 샘플을 확인합니다. 여기서 페이로드의 데이터가 앞에서 설명한 MonitoredItemMessageModel 클래스와 비슷한 정도를 확인할 수 있습니다.

![쿼리](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-2.png)

8. 이제 'payload' 열의 Dynamic 데이터를 직접 구문 분석하여 이 데이터에 대한 몇 가지 분석을 실행해 보겠습니다. 다음 예제에서는 특정 시점(min_t 변수로 정의됨) 이후 수집된 모든 레코드에 대해 10분 기간 동안 "DisplayName": "PositiveTrendData"로 식별되는 원격 분석의 평균을 계산합니다. let min_t = datetime(2020-10-23); iiot_stage | where todatetime(payload.Timestamp) > min_t | where tostring(payload.DisplayName)== 'PositiveTrendData' | summarize event_avg = avg(todouble(payload.Value)) by bin(todatetime(payload.Timestamp), 10 m)
 
'payload' 열에는 Dynamic 데이터 형식이 포함되어 있으므로 바른 데이터 형식에 대한 계산이 수행되도록 쿼리 시 데이터 변환을 수행해야 합니다.

![페이로드 타임스탬프](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-3.png)

앞에서 설명한 대로 OPC UA 데이터를 하나의 'Dynamic' 열이 있는 준비 테이블로 수집하면 유연성이 제공됩니다. 그러나 쿼리 시간에 데이터 형식 변환을 실행해야 하는 경우 특히 데이터 볼륨이 크고 동시 쿼리가 많은 경우 쿼리 실행이 지연될 수 있습니다. 이 단계에서는 데이터 형식이 이미 결정된 다른 테이블을 만들어 쿼리 시간 데이터 형식 변환을 방지할 수 있습니다.
 
9. 준비 테이블의 동적 'payload' 콘텐츠에서 제한된 선택 항목으로 구성된 구문 분석된 데이터에 대한 새 테이블을 만듭니다. 원격 분석에서 예상되는 각 예상 데이터 형식에 대한 값 열을 만들었습니다.

```
.create table ['iiot_parsed']  
    (['Tag_Timestamp']: datetime ,  
    ['Tag_PublisherId']:string ,  
    ['Tag']:string ,
    ['Tag_Datatype']:string ,  
    ['Tag_NodeId']:string,  
    ['Tag_value_long']:long ,  
    ['Tag_value_double']:double,  
    ['Tag_value_boolean']:bool)
```

10. 준비 테이블에서 필요한 데이터를 프로젝션하는 함수(데이터베이스 수준)를 만듭니다. 여기서는 'payload' 열에서 'Timestamp', 'PublisherId', 'DisplayName', 'Datatype' 및 'NodeId' 항목을 선택하고, 이러한 항목을 'Tag_Timestamp', 'Tag_PublisherId', 'Tag', 'Tag_Datatype', 'Tag_NodeId'으로 프로젝션합니다. 'Value' 항목은 'DataType'을 기준으로 세 개의 다른 부분으로 프로젝션됩니다.

```
.create-or-alter function fn_InflightParseIIoTEvent()
{
iiot_stage
| extend Tag_Timestamp =  todatetime(payload.Timestamp)
| extend Tag_PublisherId = tostring(payload.PublisherId)
| extend Tag = tostring(payload.DisplayName)
| extend Tag_Datatype = tostring(payload.DataType)
| extend Tag_NodeId = tostring(payload.NodeId)
| extend Tag_value_long = case(Tag_Datatype == "Int64", tolong(payload.Value), long(null))
| extend Tag_value_double = case(Tag_Datatype == "Double", todouble(payload.Value), double(null))
| extend Tag_value_boolean = case(Tag_Datatype == "Boolean", tobool(payload.Value), bool(null))
| project Tag_Timestamp, Tag_PublisherId, Tag, Tag_Datatype, Tag_NodeId, Tag_value_long, Tag_value_double, Tag_value_boolean
}
```

ADX의 데이터 형식 매핑에 대한 자세한 내용은 [여기](https://docs.microsoft.com/azure/data-explorer/kusto/query/scalar-data-types/dynamic)를 참조하고, ADX의 함수는 [여기](https://docs.microsoft.com/azure/data-explorer/kusto/query/schema-entities/stored-functions)서 시작할 수 있습니다.
 
11. 업데이트 정책을 사용하여 이전 단계의 함수를 구문 분석된 테이블에 적용합니다. 업데이트 [정책](https://docs.microsoft.com/azure/data-explorer/kusto/management/updatepolicy)은 원본 테이블에 삽입된 데이터에서 실행되는 변환 쿼리에 따라 새 데이터가 원본 테이블에 삽입될 때마다 데이터를 대상 테이블에 자동으로 추가하도록 ADX에 지시합니다. 다음 쿼리를 사용하여 구문 분석된 테이블을 대상으로 지정하고, 준비 테이블을 이전 단계에서 만든 함수로 정의된 업데이트 정책의 원본으로 할당할 수 있습니다.

```
.alter table iiot_parsed policy update
@'[{"IsEnabled": true, "Source": "iiot_stage", "Query": "fn_InflightParseIIoTEvent()", "IsTransactional": true, "PropagateIngestionProperties": true}]'
```

위의 쿼리가 실행되는 즉시 데이터 흐름이 시작되고 'iiot_parsed’ 대상 테이블이 채워집니다. 다음과 같이 'iiot_passed'의 데이터를 살펴볼 수 있습니다.

![구문 분석된 테이블](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-4.png)

12. 이제 이전 단계에서 수행한 분석을 반복하는 방법을 살펴보겠습니다. 특정 시점(min_t 변수로 정의됨) 이후 수집된 모든 레코드에 대해 10분 기간 동안 "DisplayName": "PositiveTrendData"로 식별되는 원격 분석의 평균을 계산합니다. 이제 double 데이터 형식의 열에 저장된 'PositveTrendData' 태그의 값이 있으므로 쿼리 성능이 향상될 것입니다.

![분석 반복](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-5.png)

13. 마지막으로 두 경우 모두의 쿼리 성능을 비교해 보겠습니다. ADX UI의 '통계'(쿼리 결과 위에 있을 수 있음)를 사용하여 쿼리를 실행하는 데 걸린 시간을 확인할 수 있습니다.  

![쿼리 성능 1](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-6.png)

![쿼리 성능 2](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-7.png)

구문 분석된 테이블을 사용하는 쿼리가 준비 테이블에 대한 쿼리보다 대략 두 배 정도 빠르다는 것을 알 수 있습니다. 이 예제에서는 작은 데이터 세트가 있고 동시 쿼리가 실행되지 않으므로 쿼리 실행 시간에 미치는 영향은 크지 않지만, 실제 워크로드의 경우 성능에 큰 영향을 미칠 수 있습니다. 따라서 다른 데이터 형식을 다른 열로 분리하는 것이 중요합니다.

> [!NOTE] 
> 업데이트 정책은 정책이 설정된 후 준비 테이블로 수집된 데이터에 대해서만 작동하며, 기존 데이터에는 적용되지 않습니다. 예를 들어 업데이트 정책을 변경해야 하는 경우 이를 고려해야 합니다. 자세한 내용은 ADX 설명서에서 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계
이제 구성의 기본값을 변경하는 방법을 알아보았으므로 다음을 수행할 수 있습니다. 

> [!div class="nextstepaction"]
> [산업용 IoT 구성 요소 구성](tutorial-configure-industrial-iot-components.md)

> [!div class="nextstepaction"]
> [Time Series Insights를 사용하여 데이터 시각화 및 분석](tutorial-visualize-data-time-series-insights.md)