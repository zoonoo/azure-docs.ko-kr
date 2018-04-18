---
title: Azure Stream Analytics 작업에 데이터 입력 추가
description: 데이터 원본을 스트리밍 데이터 입력(Event Hubs) 또는 참조 데이터(블로그 저장소)로 Stream Analytics 작업에 연결하는 방법을 알아봅니다.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/28/2017
ms.openlocfilehash: 713b830717cce7b4b2b0fb1171596659c2275b85
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="add-a-streaming-data-input-or-reference-data-to-a-stream-analytics-job"></a>Stream Analytics 작업에 스트리밍 데이터 입력 또는 참조 데이터 추가
Blob 저장소에서 참조 데이터 또는 Event Hubs에서 데이터 입력을 스트리밍하면서 데이터 소스를 Stream Analytics 작업에 연결하는 방법을 배웁니다.

Azure Stream Analytics 작업은 각각의 기존 데이터 원본에 대한 연결을 정의하는 하나 이상의 데이터 입력에 연결할 수 있습니다. 데이터가 해당 데이터 원본에 전송되면 Stream Analytics 작업에서 사용되고 스트리밍 데이터와 같이 실시간으로 처리됩니다. Stream Analytics는 작업 구독 내부 및 외부의 [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) 및 [Azure Blob 저장소](../storage/blobs/storage-dotnet-how-to-use-blobs.md)와 높은 수준으로 통합됩니다.

이 문서는 [Stream Analytics 학습 경로](/documentation/learning-paths/stream-analytics/)의 한 단계입니다.

## <a name="data-input-streaming-data-and-reference-data"></a>데이터 입력: 스트리밍 데이터 및 참조 데이터
Stream Analytics에는 데이터 스트림 및 참조 데이터 이렇게 두 가지 입력 형식이 있습니다.

* **데이터 스트림**: Stream Analytics 작업은 작업에서 사용되고 변환될 하나 이상의 데이터 스트림 입력을 포함해야 합니다. Azure Blob 저장소 및 Azure Event Hubs는 데이터 스트림 입력 소스로 지원됩니다. Azure Event Hubs는 연결된 장치, 서비스 및 응용 프로그램에서 이벤트 스트림을 수집하는 데 사용됩니다. Azure Blob 저장소를 스트림으로 대량 데이터 수집을 위한 입력 소스로 사용할 수 있습니다.  
* **참조 데이터**: Stream Analytics는 참조 데이터라는 두 번째 형식의 보조 입력을 지원합니다.  동작 중인 데이터와 반대로, 이 데이터는 정적이거나 느리게 변경됩니다.  일반적으로 조회 및 데이터 스트림과의 상관 관계를 수행하여 보다 풍부한 데이터 집합을 만드는 데 사용됩니다.  Azure Blob 저장소는 현재 유일하게 지원되는 참조 데이터용 입력 소스입니다.  

Stream Analytics 작업에 입력을 추가하려면

1. Azure Portal에서 **입력**을 클릭한 다음 Stream Analytics 작업에서 **입력 추가**를 클릭합니다.
   
    ![Azure Portal - 입력을 추가합니다.](./media/stream-analytics-add-inputs/1-stream-analytics-add-inputs.png)  
   
    Azure Portal에서 Stream Analytics 작업의 **입력** 타일을 클릭합니다.  
   
    ![Azure 포털 - 데이터 입력을 추가합니다.](./media/stream-analytics-add-inputs/7-stream-analytics-add-inputs.png)  
2. 입력 형식(**데이터 스트림** 또는 **참조 데이터**)을 지정합니다.
   
    ![올바른 데이터 입력, 스트리밍 또는 참조 추가](./media/stream-analytics-add-inputs/2-stream-analytics-add-inputs.png)  
   
    ![올바른 데이터 입력, 스트리밍 또는 참조 추가](./media/stream-analytics-add-inputs/8-stream-analytics-add-inputs.png)  
3. 데이터 스트림 입력을 만드는 경우 입력 소스 유형을 지정합니다.  이때는 Blob 저장소만 지원되므로 참조 데이터를 만드는 동안에는 이 단계를 건너뛸 수 있습니다.
   
    ![데이터 스트림 데이터 입력 추가](./media/stream-analytics-add-inputs/3-stream-analytics-add-inputs.png)  
   
    ![데이터 스트림 데이터 입력 추가](./media/stream-analytics-add-inputs/9-stream-analytics-add-inputs.png)  
4. 입력 별칭 상자에 이 입력의 이름을 입력합니다.  이 이름은 나중에 작업 쿼리에서 입력을 참조하는 데 사용됩니다.
   
    데이터 원본에 연결하는 데 필요한 나머지 연결 속성을 입력합니다. 이러한 필드는 입력 형식 및 소스 형식마다 다르며 [여기](stream-analytics-create-a-job.md)에 자세히 정의되어 있습니다.  
   
    ![이벤트 허브 데이터 입력 추가](./media/stream-analytics-add-inputs/4-stream-analytics-add-inputs.png)  
5. 입력 데이터에 대한 직렬화 설정을 지정합니다.
   
   * 쿼리가 예상대로 작동하게 하려면 들어오는 데이터의 **이벤트 직렬화 형식** 을 지정합니다.  지원되는 직렬화 형식은 JSON, CSV 및 Avro입니다.
   * 데이터의 **Encoding**을 확인합니다.  지금은 지원되는 인코딩 형식이 UTF-8뿐입니다.
     
     ![데이터 입력에 대한 데이터 직렬화 설정](./media/stream-analytics-add-inputs/5-stream-analytics-add-inputs.png)  
     
     ![데이터 입력에 대한 데이터 직렬화 설정](./media/stream-analytics-add-inputs/10-stream-analytics-add-inputs.png)  
6. 입력 만들기를 완료한 후 Stream Analytics에서 입력 소스에 연결할 수 있는지 확인합니다.  알림 허브에서 연결 테스트 작업의 상태를 볼 수 있습니다.
   
    ![스트리밍 데이터 입력의 연결 테스트](./media/stream-analytics-add-inputs/6-stream-analytics-add-inputs.png)  
   
    ![스트리밍 데이터 입력의 연결 테스트](./media/stream-analytics-add-inputs/11-stream-analytics-add-inputs.png)  

## <a name="get-help-with-streaming-data-inputs"></a>스트리밍 데이터 입력에 대한 도움 받기
추가 지원이 필요할 경우 [Azure Stream Analytics 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>다음 단계
* [Azure Stream Analytics 소개](stream-analytics-introduction.md)
* [Azure Stream Analytics 사용 시작](stream-analytics-real-time-fraud-detection.md)
* [Azure  Stream Analytics 작업 규모 지정](stream-analytics-scale-jobs.md)
* [Azure  Stream Analytics 쿼리 언어 참조](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics 관리 REST API 참조](https://msdn.microsoft.com/library/azure/dn835031.aspx)

