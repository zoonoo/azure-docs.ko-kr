---
title: "Stream Analytics에서 스트리밍 작업을 시작 하는 방법 | Microsoft Docs"
description: "Azure Stream Analytics에서 스트리밍 작업을 실행 하는 방법 | 학습 경로 세그먼트"
keywords: "스트리밍 작업"
documentationcenter: 
services: stream-analytics
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 9d46950f-2b69-49ce-a567-df558c5dd820
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: samacha
ms.openlocfilehash: bd252d7df2fc15aaa24d1a1ed7aaf6e00d301410
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-run-a-streaming-job-in-azure-stream-analytics"></a>Azure Stream Analytics에서 스트리밍 작업을 실행 하는 방법
작업 입력, 쿼리 및 출력을 모두 지정했으면 Stream Analytics 작업을 시작할 수 있습니다.

작업을 시작하려면

1. Azure 클래식 포털의 작업 대시보드의 페이지 아래쪽에서 **시작** 을 클릭합니다.
   
   ![작업 시작 단추](./media/stream-analytics-run-a-job/1-stream-analytics-run-a-job.png)  
   
   Azure Portal의 작업 페이지 맨 위에서 **시작** 을 클릭합니다.
   
   ![Azure Portal 작업 시작 단추](./media/stream-analytics-run-a-job/4-stream-analytics-run-a-job.png)  
2. **출력 시작** 값을 지정하여 이 작업이 출력 생성을 시작하는 시기를 결정합니다. 이전에 시작되지 않은 작업에 대한 기본 설정은 **작업 시작 시간**으로 이는 작업이 즉시 데이터 처리를 시작함을 의미합니다. 과거(기록 데이터 사용을 위해) 또는 미래(미래 시간까지 처리 지연을 위해)의 **사용자 지정** 시간을 지정할 수도 있습니다. 작업이 이전에 시작 및 중지된 경우 마지막 출력 시간부터 작업을 다시 시작하고 데이터 손실을 방지하기 위해 **마지막 중지 시간** 옵션을 사용할 수 있습니다.  
참고: 파티션을 사용할 때 마지막 중지 시간은 모든 파티션 전체에서 마지막 출력 시간의 최소값을 나타냅니다.
   
   ![스트리밍 작업 시작 시간](./media/stream-analytics-run-a-job/2-stream-analytics-run-a-job.png)  
   
   ![Azure Portal 스트리밍 작업 시작 시간](./media/stream-analytics-run-a-job/5-stream-analytics-run-a-job.png)  
3. 선택을 확인합니다. 작업 상태가 *시작 중*으로 변경되었다가 작업이 시작되면 곧 *실행 중*으로 변경됩니다. **알림 허브**에서 **시작** 작업의 진행률을 모니터링할 수 있습니다.
   
   ![스트림 작업 진행률](./media/stream-analytics-run-a-job/3-stream-analytics-run-a-job.png)  
   
   ![Azure Portal 스트리밍 작업 진행률](./media/stream-analytics-run-a-job/6-stream-analytics-run-a-job.png)  

## <a name="get-help"></a>도움말 보기
추가 지원이 필요할 경우 [Azure 스트림 분석 포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>다음 단계
* [Azure Stream Analytics 소개](stream-analytics-introduction.md)
* [Azure Stream Analytics 사용 시작](stream-analytics-real-time-fraud-detection.md)
* [Azure  Stream Analytics 작업 규모 지정](stream-analytics-scale-jobs.md)
* [Azure  Stream Analytics 쿼리 언어 참조](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics 관리 REST API 참조](https://msdn.microsoft.com/library/azure/dn835031.aspx)

