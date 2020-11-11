---
title: Log Analytics 및 Excel 통합
description: Excel로 Log Analytics 쿼리를 가져오고 Excel 내에서 결과를 새로 고칩니다.
ms.subservice: logs
ms.topic: conceptual
author: roygalMS
ms.author: roygal
ms.date: 11/03/2020
ms.openlocfilehash: d903d1bb16ba3576d0092979f1cc6b82fac1c0be
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94507524"
---
# <a name="integrate-log-analytics-and-excel"></a>Log Analytics 및 Excel 통합

M 쿼리와 Log Analytics API를 사용 하 여 Log Analytics 및 Microsoft Excel Azure Monitor를 통합할 수 있습니다.  이러한 통합을 통해 50만 레코드를 Excel로 보낼 수 있습니다.

> [!NOTE]
> Excel은 로컬 클라이언트 응용 프로그램 이므로 로컬 하드웨어 및 소프트웨어 제한 사항으로 인해 성능 및 큰 데이터 집합을 처리 하는 기능이 영향을 받습니다.

## <a name="create-your-m-query-in-log-analytics"></a>Log Analytics에서 M 쿼리 만들기 

1. 일반적으로 Log analytics에서 **쿼리를 만들고 실행** 합니다. 사용자 인터페이스에서 1만 레코드 제한에 도달 하면 걱정 하지 마세요.  ' 이전 ' 함수 또는 UI 시간 선택과 같은 상대 날짜를 사용 하 여 Excel에서 올바른 데이터 집합을 새로 고치는 것이 좋습니다.
  
2. **쿼리 내보내기** -쿼리 및 해당 결과가 만족 스 러 우면 *내보내기* 메뉴에서 **Power BI (m Query) 메뉴를** 사용 하 여 Log Analytics 내보내기를 사용 하 여 쿼리를 M으로 내보냅니다.

:::image type="content" source="media/log-excel/export-query.png" alt-text="데이터 및 내보내기 옵션을 사용 하 여 쿼리 Log Analytics" border="true":::



이 옵션을 선택 하면 Excel에서 사용할 수 있는 M 코드를 포함 하는 .txt 파일이 다운로드 됩니다.

위에 표시 된 쿼리는 다음 M 코드를 내보냅니다. 예제에서 쿼리에 대해 내보낸 M 코드의 예는 다음과 같습니다.

```m
/*
The exported Power Query Formula Language (M Language ) can be used with Power Query in Excel
and Power BI Desktop.
For Power BI Desktop follow the instructions below: 
1) Download Power BI Desktop from https://powerbi.microsoft.com/desktop/
2) In Power BI Desktop select: 'Get Data' -> 'Blank Query'->'Advanced Query Editor'
3) Paste the M Language script into the Advanced Query Editor and select 'Done'
*/

let AnalyticsQuery =
let Source = Json.Document(Web.Contents("https://api.loganalytics.io/v1/workspaces/ddcfc599-cae0-48ee-9026-fffffffffffff/query", 
[Query=[#"query"="

Heartbeat 
| summarize dcount(ComputerIP) by bin(TimeGenerated, 1h)    
| render timechart
",#"x-ms-app"="OmsAnalyticsPBI",#"timespan"="P1D",#"prefer"="ai.response-thinning=true"],Timeout=#duration(0,0,4,0)])),
TypeMap = #table(
{ "AnalyticsTypes", "Type" }, 
{ 
{ "string",   Text.Type },
{ "int",      Int32.Type },
{ "long",     Int64.Type },
{ "real",     Double.Type },
{ "timespan", Duration.Type },
{ "datetime", DateTimeZone.Type },
{ "bool",     Logical.Type },
{ "guid",     Text.Type },
{ "dynamic",  Text.Type }
}),
DataTable = Source[tables]{0},
Columns = Table.FromRecords(DataTable[columns]),
ColumnsWithType = Table.Join(Columns, {"type"}, TypeMap , {"AnalyticsTypes"}),
Rows = Table.FromRows(DataTable[rows], Columns[name]), 
Table = Table.TransformColumnTypes(Rows, Table.ToList(ColumnsWithType, (c) => { c{0}, c{3}}))
in
Table
in AnalyticsQuery
```

## <a name="connect-query-to-excel"></a>Excel에 쿼리 연결 

쿼리를 가져옵니다. 

1. Microsoft Excel을 엽니다. 
1. 리본에서 **데이터** 메뉴로 이동 합니다. **데이터 가져오기** 를 선택 합니다. **다른 원본** 에서 **빈 쿼리** 를 선택 합니다.
 
   :::image type="content" source="media/log-excel/excel-import-blank-query.png" alt-text="Excel에서 공백 가져오기 옵션" border="true":::

1. 파워 쿼리 창에서 **고급 편집기** 를 선택 합니다.

   :::image type="content" source="media/log-excel/advanced-editor.png" alt-text="Excel 고급 쿼리 편집기" border="true":::

 
1. 고급 편집기의 텍스트를 Log Analytics에서 내보낸 쿼리로 바꿉니다.

   :::image type="content" source="media/log-excel/advanced-editor-2.png" alt-text="빈 쿼리 만들기" border="true":::
 
1. **완료** , **로드 및 닫기** 를 차례로 선택 합니다. Excel은 Log analytics API 및 결과 집합을 사용 하 여 쿼리를 실행 합니다.
 

   :::image type="content" source="media/log-excel/excel-query-result.png" alt-text="Excel의 쿼리 결과" border="true":::

##  <a name="refreshing--data"></a>데이터 새로 고침

Excel에서 직접 데이터를 새로 고칠 수 있습니다. Excel 리본의 **데이터** 메뉴 그룹에서 **새로 고침** 단추를 선택 합니다.
 
## <a name="next-steps"></a>다음 단계

Excel의 외부 데이터 원본과의 통합에 대 한 자세한 내용은 [외부 데이터 원본에서 데이터 가져오기 (파워 쿼리)](https://support.office.com/article/import-data-from-external-data-sources-power-query-be4330b3-5356-486c-a168-b68e9e616f5a) 를 참조 하세요.