---
title: "Azure Stream Analytics로 이벤트 순서 및 지연 처리 | Microsoft Docs"
description: "Azure Stream Analytics가 데이터 스트림의 순서 비지정 이벤트 또는 늦게 도착한 이벤트에 어떻게 작동하는지 알아봅니다."
keywords: "순서 비지정, 지연, 이벤트"
documentationcenter: 
services: stream-analytics
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/20/2017
ms.author: samacha
ms.translationtype: HT
ms.sourcegitcommit: fda37c1cb0b66a8adb989473f627405ede36ab76
ms.openlocfilehash: cf9a43fbb82a32c92d66f25809916d3ccde1a20d
ms.contentlocale: ko-kr
ms.lasthandoff: 09/14/2017

---
# <a name="azure-stream-analytics-event-order-handling"></a>Azure Stream Analytics 이벤트 순서 처리

이벤트의 임시 데이터 스트림에서는 각 이벤트에 타임스탬프가 할당됩니다. Azure Stream Analytics는 도착 시간이나 응용 프로그램 시간 중 하나를 사용하여 각 이벤트에 타임스탬프를 할당합니다. 

이벤트에 할당된 타임스탬프는 **System.Timestamp** 열에 있습니다. 도착 시간은 이벤트가 소스에 도달하면 입력 소스에서 할당됩니다. 도착 시간은 이벤트 허브 입력의 경우 **EventEnqueuedTime**이고 BLOB 입력의 경우 [BLOB를 마지막으로 수정한 시간](https://docs.microsoft.com/en-us/dotnet/api/microsoft.windowsazure.storage.blob.blobproperties.lastmodified?view=azurestorage-8.1.3)입니다. 응용 프로그램 시간은 이벤트 생성 시에 할당되며 페이로드의 일부분입니다. 

응용 프로그램 시간을 기준으로 이벤트를 처리하려면 select 쿼리에서 TIMESTAMP BY 절을 사용합니다. TIMESTAMP BY 절이 없으면 이벤트는 도착 시간을 기준으로 처리됩니다. 이벤트 허브의 경우 **EventEnqueuedTime** 속성을, BLOB 입력의 경우 **BlobLastModified** 속성을 사용하여 도착 시간에 액세스할 수 있습니다. 

Azure Stream Analytics는 타임스탬프 순서로 출력을 생성하며 순서 비지정 데이터를 처리하기 위한 몇 가지 설정을 제공합니다.

![Stream Analytics 이벤트 처리](media/stream-analytics-event-handling/stream-analytics-event-handling.png)

순서가 올바르지 않은 입력 스트림은 다음 중 하나입니다.
* 정렬되었습니다(따라서 *지연됨*).
* 사용자 지정 정책에 따라 시스템에 의해 조정됩니다.

응용 프로그램 시간을 기준으로 처리할 때 Stream Analytics에서는 늦게 도착한 이벤트와 순서 비지정 이벤트를 허용합니다.

**지연 도착 허용 시간**

* 지연 도착 허용 시간 설정은 응용 프로그램 시간을 기준으로 처리할 때만 적용할 수 있습니다. 그렇지 않은 경우 이 설정은 무시됩니다.
* 지연 도착 허용 시간은 도착 시간과 응용 프로그램 시간 사이의 최대 차이입니다. 응용 프로그램 시간은 *(도착 시간 - 지연 도착 시간)*보다 이전인 경우 *(도착 시간 - 지연 도착 시간)*으로 설정됩니다.
* 같은 입력 스트림이나 여러 입력 스트림의 여러 파티션이 함께 결합될 때의 지연 도착 허용 시간은 모든 파티션이 새 데이터 도착을 대기하는 최대 시간입니다. 

요약하자면, 지연 도착 시간은 이벤트 생성 시간과 입력 소스에서 이벤트를 수신하는 시간 사이의 최대 지연입니다.
먼저 지연 도착 허용 시간을 기준으로 시간을 조정한 후에 순서 비지정을 기준으로 시간을 조정합니다. 이벤트에 할당된 최종 타임스탬프는 **System.Timestamp** 열에 있습니다.

**잘못된 허용 시간**

* 순서가 올바르지 않으나 "잘못된 순서 허용 시간" 이내에 도착하는 이벤트는 *타임스탬프 기준으로 다시 정렬*됩니다. 
* 이 허용 시간보다 늦게 도착하는 이벤트는 *삭제* 또는 *조정*됩니다.
    * **조정**: 최신 허용 가능한 시간에 도착한 것처럼 조정합니다. 
    * **삭제**: 삭제합니다.

"잘못된 순서 허용 시간" 이내에 수신된 이벤트를 다시 정렬하기 위해 쿼리의 출력은 *잘못된 순서 허용 시간만큼 지연*됩니다.

**예제**

지연 도착 허용 시간 = 10분<br/>
잘못된 허용 시간 = 3분<br/>
응용 프로그램 시간을 기준으로 처리<br/>

이벤트:

이벤트 1 _응용 프로그램 시간_ = 00:00:00, _도착 시간_ = 00:10:01, _System.Timestamp_ = 00:00:01 - (_도착 시간_ - _응용 프로그램 시간_)이 지연 도착 허용 시간보다 크므로 조정됨

이벤트 2 _응용 프로그램 시간_ = 00:00:01, _도착 시간_ = 00:10:01, _System.Timestamp_ = 00:00:01 - 응용 프로그램 시간이 지연 도착 허용 시간 이내이므로 조정되지 않음

이벤트 3 _응용 프로그램 시간_ = 00:10:00 _도착 시간_ = 00:10:02, _System.Timestamp_ = 00:10:00 - 응용 프로그램 시간이 지연 도착 허용 시간 이내이므로 조정되지 않음

이벤트 4 _응용 프로그램 시간_ = 00:09:00, _도착 시간_ = 00:10:03, _System.Timestamp_ = 00:09:00 - 응용 프로그램 시간이 잘못된 허용 시간 이내이므로 원본 타임스탬프로 수락됨

이벤트 5 _응용 프로그램 시간_ = 00:06:00, _도착 시간_ = 00:10:04, _System.Timestamp_ = 00:07:00 - 응용 프로그램 시간이 잘못된 허용 시간보다 이전이므로 조정됨



## <a name="get-help"></a>도움말 보기
추가 지원이 필요한 경우 [Azure Stream Analytics 포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)을 참조하세요.

## <a name="next-steps"></a>다음 단계
* [Azure Stream Analytics 소개](stream-analytics-introduction.md)
* [Azure Stream Analytics 시작](stream-analytics-real-time-fraud-detection.md)
* [Azure  Stream Analytics 작업 규모 지정](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics 쿼리 언어 참조](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics 관리 REST API 참조](https://msdn.microsoft.com/library/azure/dn835031.aspx)

