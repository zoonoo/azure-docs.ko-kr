---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 01/08/2020
ms.author: orspodek
ms.openlocfilehash: f9788e4623ce60ad55d79558d1d77a17eb2a9f26
ms.sourcegitcommit: 5b073caafebaf80dc1774b66483136ac342f7808
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75779956"
---
### <a name="event-system-properties-mapping"></a>이벤트 시스템 속성 매핑

위의 테이블의 **데이터 원본** 섹션에서 **이벤트 시스템 속성** 을 선택한 경우 [웹 UI](https://dataexplorer.azure.com/) 로 이동 하 여 적절 한 매핑 생성을 위해 관련 KQL 명령을 실행 합니다.

   **Csv 매핑:**

    ```kusto
    .create table MyTable ingestion csv mapping "CsvMapping1"
    '['
    '   { "column" : "messageid", "DataType":"string", "Properties":{"Ordinal":"0"}},'
    '   { "column" : "userid", "DataType":"string", "Properties":{"Ordinal":"1"}},'
    '   { "column" : "other", "DataType":"int", "Properties":{"Ordinal":"2"}}'
    ']'
    ```
 
   **For json 매핑:**

    ```kusto
    .create table MyTable ingestion json mapping "JsonMapping1"
    '['
    '    { "column" : "messageid", "datatype" : "string", "Properties":{"Path":"$.message-id"}},'
    '    { "column" : "userid", "Properties":{"Path":"$.user-id"}},'
    '    { "column" : "other", "Properties":{"Path":"$.other"}}'
    ']'
    ```

   > [!TIP]
   > * 선택한 모든 속성을 매핑에 포함 해야 합니다. 
   > * Csv 매핑에서 속성 순서는 중요 합니다. 시스템 속성은 다른 모든 속성과 **이벤트 시스템 속성** 드롭다운에서 표시 되는 순서와 동일한 순서로 나열 되어야 합니다.