---
title: Azure Stream Analytics에서 잘못된 형식의 입력 이벤트 문제 해결
description: 입력 데이터에서 Stream Analytics 작업에 문제를 일으키는 이벤트를 알아내는 방법
services: stream-analytics
author: jasonwhowell
manager: Kfile
ms.author: jasonh
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/05/2018
ms.openlocfilehash: fcbb03b4d9aed797cf99c374661c743d39f81276
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="common-issues-in-stream-analytics-and-steps-to-troubleshoot"></a>Stream Analytics의 일반적인 문제 및 문제를 해결하는 단계

## <a name="troubleshoot-for-malformed-input-events"></a>잘못된 형식의 입력 이벤트 문제 해결

Stream Analytics 작업의 입력 스트림에 잘못된 형식의 메시지가 포함되어 있으면 직렬화 문제가 발생합니다. 잘못된 형식의 메시지는 JSON 개체의 괄호 누락 또는 잘못된 타임스탬프 형식과 같은 잘못된 직렬화를 포함합니다. Stream Analytics 작업이 잘못된 형식의 메시지를 수신하면 해당 메시지를 삭제하고 경고로 알립니다. Stream Analytics 작업의 **입력** 타일에 경고 기호가 표시됩니다(이 경고 기호는 작업이 실행 중인 경우 계속 표시됨).

![입력 타일](media/stream-analytics-malformed-events/inputs_tile.png)

경고의 세부 정보를 보려면 진단 로그를 사용할 수 있습니다. 형식이 잘못된 입력 이벤트의 경우 실행 로그에는 다음과 같은 메시지와 함께 항목이 포함됩니다. "메시지: 리소스에서 입력 이벤트를 <blob URI> JSON으로 deserialize하지 못했습니다." 

### <a name="troubleshooting-steps"></a>문제 해결 단계

1. 입력 타일로 이동한 후 클릭하여 경고를 확인합니다.
2. 입력 세부 정보 타일에는 문제에 대한 세부 정보를 포함하는 경고 집합이 표시됩니다. 다음은 예제 경고 메시지로, 경고 메시지는 잘못된 형식의 JSON 데이터가 있는 파티션, 오프셋 및 시퀀스 번호를 보여 줍니다. 

   ![오프셋을 포함하는 경고 메시지](media/stream-analytics-malformed-events/warning_message_with_offset.png)

3. 잘못된 형식이 포함된 JSON 데이터를 가져오려면 CheckMalformedEvents.cs 코드를 실행합니다. [GitHub 샘플 리포지토리](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/CheckMalformedEventsEH)에서 가져올 수 있습니다. 이 코드는 파티션 ID, 오프셋을 읽고 오프셋에 배치된 데이터를 출력합니다. 

4. 데이터를 읽은 경우 직렬화 형식을 분석하고 수정할 수 있습니다. 

## <a name="handling-duplicate-records-when-using-azure-sql-database-as-output-for-a-stream-analytics-job"></a>Stream Analytics 작업의 출력으로 Azure SQL Database를 사용하는 경우 중복 레코드 처리

Azure SQL Database를 Stream Analytics 작업에 대한 출력으로 구성하면 대상 테이블에 레코드를 대량으로 삽입합니다. 일반적으로 Azure Stream Analytics에서는 출력 싱크에 [적어도 한 번 배달]( https://msdn.microsoft.com/azure/stream-analytics/reference/event-delivery-guarantees-azure-stream-analytics)을 보장합니다. SQL 테이블에 고유한 제약 조건이 정의된 경우에도 사용자는 [정확히 한 번 배달을 설정]( https://blogs.msdn.microsoft.com/streamanalytics/2017/01/13/how-to-achieve-exactly-once-delivery-for-sql-output/)할 수 있습니다. 

고유한 키 제약 조건이 SQL 테이블에 설정되고 중복 레코드가 SQL 테이블에 삽입되면 Azure Stream Analytics는 단일 중복 레코드를 찾을 때까지 데이터를 일괄 처리로 분할하고 재귀적으로 일괄 처리를 삽입하여 중복 레코드를 제거합니다. 파이프라인에 상당히 많은 수의 중복 행이 있으면 중복된 내용을 하나씩 무시하는 데이터를 분할하고 재귀적으로 삽입하는 작업은 시간이 많이 걸리는 프로세스입니다. 최근 한 시간 내에 활동 로그에서 여러 키 위반 경고 메시지가 표시된 경우 SQL 출력이 전체 작업 성능을 저하시킬 가능성이 있습니다. 이 문제를 해결하려면 IGNORE_DUP_KEY 옵션을 사용하여 키 위반이 발생하는 [인덱스를 구성]( https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql)해야 합니다. 이 옵션을 사용하면 중복 값이 대량 삽입하는 동안 SQL에서 무시될 수 있고 SQL Azure에서 오류 대신 경고 메시지를 생성하게 됩니다. Azure Stream Analytics에서는 기본 키 위반 오류를 더 이상 생성하지 않습니다.

몇 가지 유형의 인덱스에서 IGNORE_DUP_KEY를 구성할 때 다음 조사 내용을 적어둡니다.

* 기본 키에서 IGNORE_DUP_KEY 또는 ALTER INDEX를 사용하는 고유한 제약 조건을 설정할 수 없습니다. 인덱스를 삭제하고 다시 만들어야 합니다.  
* 고유 인덱스에 ALTER INDEX를 사용하여 IGNORE_DUP_KEY 옵션을 설정할 수 있습니다. PRIMARY KEY/UNIQUE 제약 조건과 다르게 CREATE INDEX 또는 INDEX 정의를 사용하여 만듭니다.  
* 이러한 인덱스에 고유성을 적용할 수 없기 때문에 IGNORE_DUP_KEY는 열 저장소 인덱스에 적용되지 않습니다.  

## <a name="next-steps"></a>다음 단계

* [Azure  Stream Analytics 쿼리 언어 참조](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics 관리 REST API 참조](https://msdn.microsoft.com/library/azure/dn835031.aspx)

