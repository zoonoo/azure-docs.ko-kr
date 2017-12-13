---
title: "Azure Stream Analytics로 이벤트 순서 및 지연 처리 | Microsoft Docs"
description: "Stream Analytics가 데이터 스트림의 잘못된 순서 또는 지연 이벤트에 어떻게 작동하는지 알아봅니다."
keywords: "순서 비지정, 지연, 이벤트"
documentationcenter: 
services: stream-analytics
author: jseb225
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/20/2017
ms.author: jeanb
ms.openlocfilehash: 208dfa14d5d18e106d654539cd80bafdeb90cdf8
ms.sourcegitcommit: 80eb8523913fc7c5f876ab9afde506f39d17b5a1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2017
---
# <a name="azure-stream-analytics-event-order-consideration"></a>Azure Stream Analytics 이벤트 순서 고려 사항

## <a name="understand-arrival-time-and-application-time"></a>도착 시간과 응용 프로그램 시간을 이해합니다.

이벤트의 임시 데이터 스트림에서는 각 이벤트에 타임스탬프가 할당됩니다. Azure Stream Analytics는 도착 시간이나 응용 프로그램 시간 중 하나를 사용하여 각 이벤트에 타임스탬프를 할당합니다. 이벤트에 할당된 타임스탬프는 "System.Timestamp" 열에 있습니다. 도착 시간은 이벤트가 소스에 도달하면 입력 소스에서 할당됩니다. 도착 시간은 이벤트 허브 입력의 경우 EventEnqueuedTime이고 BLOB 입력의 경우 [BLOB를 마지막으로 수정한 시간](https://docs.microsoft.com/en-us/dotnet/api/microsoft.windowsazure.storage.blob.blobproperties.lastmodified?view=azurestorage-8.1.3)입니다. 응용 프로그램 시간은 이벤트 생성 시에 할당되며 페이로드의 일부분입니다. 응용 프로그램 시간을 기준으로 이벤트를 처리하려면 select 쿼리에서 "Timestamp by" 절을 사용합니다. "Timestamp by" 절이 없으면 이벤트는 도착 시간을 기준으로 처리됩니다. 이벤트 허브의 경우 EventEnqueuedTime 속성을, BLOB 입력의 경우 BlobLastModified 속성을 사용하여 도착 시간에 액세스할 수 있습니다. Azure Stream Analytics는 타임스탬프 순서로 출력을 생성하며 순서 비지정 데이터를 처리하기 위한 몇 가지 설정을 제공합니다.


## <a name="azure-stream-analytics-handling-of-multiple-streams"></a>Azure Stream Analytics의 여러 스트림 처리

Azure Stream Analytics 작업은 다음을 포함하는 일부 경우에 여러 타임라인의 이벤트를 결합합니다.

* 여러 파티션에서 출력 생성. 명시적 "Partition by PartitionId"가 없는 쿼리는 모든 파티션의 이벤트를 결합해야 합니다.
* 서로 다른 두 개 이상의 입력 원본의 합집합
* 입력 원본 조인

여러 타임라인이 결합되는 시나리오에서 Azure Stream Analytics는 결합된 모든 원본이 적어도 *t1* 시간이 된 후에만 타임스탬프 *t1*에 대한 출력을 생성합니다.
예를 들어, 쿼리가 두 개의 파티션을 포함하고 한 파티션 *P1*에는 *t1* 시간까지의 이벤트가 있고 다른 파티션 *P2*에는 *t1 + x* 시간까지의 이벤트가 있는 *이벤트 허브* 파티션에서 읽을 경우 *t1* 시간이 될 때까지 출력이 생성됩니다.
그러나 명시적 *"Partition by PartitionId"* 절이 있으면 두 파티션은 독립적으로 진행합니다.
일부 파티션에서는 데이터가 없는 문제를 처리하는 데 지연 도착 허용 시간 설정이 사용됩니다.

## <a name="configuring-late-arrival-tolerance-and-out-of-order-tolerance"></a>지연 도착 허용 시간 및 잘못된 허용 시간 구성
순서가 올바르지 않은 입력 스트림은 다음 중 하나입니다.
* 정렬되었습니다(따라서 **지연됨**).
* 사용자 지정 정책에 따라 시스템에 의해 조정됩니다.

**응용 프로그램 시간**을 기준으로 처리할 때 Stream Analytics에서는 늦게 도착한 이벤트와 순서 비지정 이벤트를 허용합니다. 다음 설정은 Azure Portal의 **이벤트 순서 지정** 옵션에서 사용할 수 있습니다. 

![Stream Analytics 이벤트 처리](media/stream-analytics-event-handling/stream-analytics-event-handling.png)

**지연 도착 허용 시간**
* 이 설정은 응용 프로그램 시간을 기준으로 처리할 때만 적용할 수 있고 그 밖의 경우 무시됩니다.
* 이 값은 도착 시간과 응용 프로그램 시간 사이의 최대 차이입니다. 응용 프로그램 시간은 (도착 시간 - 지연 도착 시간)보다 이전인 경우 (도착 시간 - 지연 도착 시간)으로 설정됩니다.
* 같은 입력 스트림이나 여러 입력 스트림의 여러 파티션이 함께 결합될 때의 지연 도착 허용 시간은 모든 파티션이 새 데이터 도착을 대기하는 최대 시간입니다. 

요약하자면, 지연 도착 시간은 이벤트 생성 시간과 입력 소스에서 이벤트를 수신하는 시간 사이의 최대 지연입니다.
먼저 지연 도착 허용 시간을 기준으로 시간을 조정한 후에 다음에 순서 비지정을 기준으로 시간을 조정합니다. 이벤트에 할당된 최종 타임스탬프는 **System.Timestamp** 열에 있습니다.

**잘못된 허용 시간**
* 순서가 올바르지 않으나 "잘못된 순서 허용 시간" 이내에 도착하는 이벤트는 **타임스탬프 기준으로 다시 정렬**됩니다. 
* 이 허용 시간보다 늦게 도착하는 이벤트는 **삭제 또는 조정**됩니다.
    * **조정**: 최신 허용 가능한 시간에 도착한 것처럼 조정합니다. 
    * **삭제**: 삭제합니다.

"잘못된 순서 허용 시간" 이내에 수신된 이벤트를 다시 정렬하기 위해 쿼리의 출력은 **잘못된 순서 허용 시간만큼 지연**됩니다.

**예제**

* 지연 도착 허용 시간 = 10분<br/>
* 잘못된 허용 시간 = 3분<br/>
* 응용 프로그램 시간을 기준으로 처리<br/>
* 이벤트:
   * 이벤트 1 _응용 프로그램 시간_ = 00:00:00, _도착 시간_ = 00:10:01, _System.Timestamp_ = 00:00:01 - (_도착 시간_ - _응용 프로그램 시간_)이 지연 도착 허용 시간보다 크므로 조정됨
   * 이벤트 2 _응용 프로그램 시간_ = 00:00:01, _도착 시간_ = 00:10:01, _System.Timestamp_ = 00:00:01 - 응용 프로그램 시간이 지연 도착 허용 시간 이내이므로 조정되지 않음
   * 이벤트 3 _응용 프로그램 시간_ = 00:10:00, _도착 시간_ = 00:10:02, _System.Timestamp_ = 00:10:00 - 응용 프로그램 시간이 지연 도착 허용 시간 이내이므로 조정되지 않음
   * 이벤트 4 _응용 프로그램 시간_ = 00:09:00, _도착 시간_ = 00:10:03, _System.Timestamp_ = 00:09:00 - 응용 프로그램 시간이 잘못된 허용 시간 이내이므로 원본 타임스탬프로 수락됨
   * 이벤트 5 _응용 프로그램 시간_ = 00:06:00, _도착 시간_ = 00:10:04, _System.Timestamp_ = 00:07:00 - 응용 프로그램 시간이 잘못된 허용 시간보다 이전이므로 조정됨

## <a name="practical-considerations"></a>실제 고려 사항
앞서 설명한 것처럼 *지연 도착 허용 시간*은 응용 프로그램 시간과 도착 시간 사이의 최대 차이입니다.
또한 응용 프로그램 시간에 따라 처리할 경우, 구성된 *지연 도착 허용 시간*보다 늦은 이벤트는 *잘못된 허용 시간* 설정이 적용되기 전에 조정됩니다. 따라서 유효 잘못된 시간은 지연 도착 허용 시간 및 잘못된 허용 시간의 최소값입니다.

스트림 내의 잘못된 이벤트는 다음을 비롯한 여러 이유로 인해 발생합니다.
* 보낸 사람 간의 클럭 오차
* 보낸 사람과 입력 이벤트 원본 간의 가변 대기 시간

지연 도착은 다음을 비롯한 여러 이유로 인해 발생합니다.
* 보낸 사람이 특정 시간 간격 동안의 이벤트를 해당 간격 이후에 일괄로 처리하고 전송
* 보낸 사람이 이벤트를 보내시는 간과 입력 원본에서 이벤트를 수신하는 시간 사이에 대기 시간 발생

특정 작업에 대한 *지연 도착 허용 시간* 및 *잘못된 허용 시간*을 구성할 때는 정확성, 지연 시간 요구 사항 및 위 요인도 고려해야 합니다.

다음은 몇 가지 예제입니다.

### <a name="example-1"></a>예제 1: 
쿼리에 "Partition by PartitionId" 절이 있고 단일 파티션 내에서 이벤트가 동기 전송 방법을 사용하여 전송됩니다. 동기 전송 방법은 이벤트가 전송될 때까지 차단됩니다.
이 경우 이벤트가 명시적으로 확인되면서 순서대로 전송된 후 다음 이벤트가 전송되므로 잘못된 이벤트 수는 0입니다. 지연 도착은 이벤트가 생성되는 시점과 이벤트를 전송하는 시점 사이의 최대 지연 시간에 보낸 사람과 입력 원본 사이의 최대 지연 시간을 더한 값이 됩니다.

### <a name="example-2"></a>예 2:
쿼리에 "Partition by PartitionId" 절이 있고 단일 파티션 내에서 이벤트가 비동기 전송 방법을 사용하여 전송됩니다. 비동기 전송 방법은 동시에 여러 전송을 시작할 수 있으므로 잘못된 이벤트가 발생할 수 있습니다.
이 경우 잘못된 이벤트 및 지연 도착의 최대 크기는 적어도 이벤트가 생성되는 시점과 이벤트를 전송하는 시점 사이의 최대 지연 시간에 보낸 사람과 입력 원본 사이의 최대 지연 시간을 더한 값이 됩니다.

### <a name="example-3"></a>예 3:
쿼리에 "Partition by PartitionId"가 없으며 2개 이상의 파티션이 있습니다.
구성은 예제 2와 동일합니다. 그러나 파티션 중 하나에서 데이터가 부족할 경우 추가 *지연 도착 허용” 시간만큼 출력이 지연될 수 있습니다.

## <a name="to-summarize"></a>요약하면
* 지연 도착 허용 시간과 잘못된 시간은 정확성, 지연 시간 요구 사항을 기준으로구 성해야 하며, 이벤트가 전송되는 방식도 고려해야 합니다.
* 잘못된 허용 시간은 지연 도착 허용 시간보다 작게 유지하는 것이 좋습니다.
* 여러 타임라인을 결합하는 경우 원본 또는 파티션 중 하나에서 데이터가 부족할 경우 추가 지연 도착 허용 시간만큼 출력이 지연될 수 있습니다.

## <a name="get-help"></a>도움말 보기
추가 지원이 필요한 경우 [Azure Stream Analytics 포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)을 참조하세요.

## <a name="next-steps"></a>다음 단계
* [Stream Analytics 소개](stream-analytics-introduction.md)
* [Stream Analytics 시작](stream-analytics-real-time-fraud-detection.md)
* [Stream Analytics 작업 크기 조정](stream-analytics-scale-jobs.md)
* [Stream Analytics 쿼리 언어 참조](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Stream Analytics 관리 REST API 참조](https://msdn.microsoft.com/library/azure/dn835031.aspx)
