---
title: 타사 응용 프로그램 및 Azure Synapse Analytics와의 호환성 문제
description: 타사 응용 프로그램에서 Azure Synapse를 사용 하 여 찾을 수 있는 알려진 문제에 대해 설명 합니다.
services: synapse-analytics
author: periclesrocha
ms.service: synapse-analytics
ms.topic: troubleshooting
ms.subservice: ''
ms.date: 11/18/2020
ms.author: procha
ms.reviewer: jrasnick
ms.openlocfilehash: 861b963922d64926266b42a23f85e9dc30fda7a3
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2020
ms.locfileid: "95903987"
---
# <a name="compatibility-issues-with-third-party-applications-and-azure-synapse-analytics"></a>타사 응용 프로그램 및 Azure Synapse Analytics와의 호환성 문제

SQL Server 용으로 빌드된 응용 프로그램은 Azure Synapse 전용 SQL 풀에서 원활 하 게 작동 합니다. 그러나 경우에 따라 SQL Server에서 일반적으로 사용 되는 기능 및 언어 요소를 Azure Synapse에서 사용 하지 못할 수도 있고 다르게 동작할 수도 있습니다.

이 문서에서는 Azure Synapse Analytics에서 타사 응용 프로그램을 사용 하는 경우 발생할 수 있는 알려진 문제에 대해 설명 합니다. 

## <a name="tableau-error-an-attempt-to-complete-a-transaction-has-failed-no-corresponding-transaction-found"></a>Tableau error: "트랜잭션을 완료 하지 못했습니다. 해당 하는 트랜잭션을 찾을 수 없습니다. "

Azure Synapse 전용 SQL 풀 버전 10.0.11038.0에서 시작 하 여 저장 프로시저를 호출 하는 일부 Tableau 쿼리는 다음 오류 메시지와 함께 실패할 수 있습니다. "**[Microsoft] [ODBC Driver 17 for SQL Server] [SQL Server] 111214; 트랜잭션을 완료 하지 못했습니다. 해당 하는 트랜잭션을 찾을 수 없습니다.**"

### <a name="cause"></a>원인

이는 ODBC 및 JDBC 드라이버에서 자동으로 호출 되는 새 시스템 저장 프로시저의 도입으로 인해 발생 하는 Azure Synapse 전용 SQL 풀의 문제입니다. 이러한 시스템 저장 프로시저 중 하나를 실행 하면 열려 있는 트랜잭션이 중단 될 수 있습니다. 이 문제는 클라이언트 응용 프로그램 논리에 따라 발생할 수 있습니다.

### <a name="solution"></a>해결 방법
Azure Synapse 전용 SQL 풀에 연결 된 Tableau를 사용 하는 경우이 특정 문제를 보고 하는 고객은 SQL 연결에서 FMTONLY를 예로 설정 해야 합니다. Tableau Desktop 및 Tableau Server의 경우 tableau가이 매개 변수를 드라이버로 전달 하는지 확인 하려면 Tableau 데이터 원본 사용자 지정 (TDC) 파일을 사용 해야 합니다.  

> [!NOTE] 
> Microsoft는 타사 도구에 대 한 지원을 제공 하지 않습니다. 이 솔루션이 Tableau Desktop 2020.3.2에서 작동 하는지 테스트 한 후에는이 해결 방법을 사용자의 용량에 맞게 사용 해야 합니다.
>

* [Tableau Desktop에서 TDC 파일을 사용 하 여 전역 사용자 지정을 수행 하는 방법에 대 한 자세한 내용은 Tableau Desktop 설명서를 참조 하세요.](https://help.tableau.com/current/pro/desktop/en-us/odbc_customize.htm)
* [Tableau Server에서 TDC 파일을 사용 하 여 전역 사용자 지정을 수행 하는 방법에 대 한 자세한 내용은을 참조 하세요. Tableau Server를 사용 하는 TDC 파일.](https://kb.tableau.com/articles/howto/using-a-tdc-file-with-tableau-server)

아래 예에서는 SQL 연결 문자열에 FMTONLY = YES 매개 변수를 전달 하는 Tableau TDC 파일을 보여 줍니다.

```json
<connection-customization class='azure_sql_dw' enabled='true' version='18.1'>
    <vendor name='azure_sql_dw' />
    <driver name='azure_sql_dw' />
    <customizations>        
        <customization name='odbc-connect-string-extras' value='UseFMTONLY=yes' />
    </customizations>
</connection-customization>
```
TDC 파일 사용에 대 한 자세한 내용은 Tableau 지원에 문의 하세요. 

## <a name="see-also"></a>참조

* [Azure Synapse Analytics의 전용 SQL 풀에 대 한 t-sql 언어 요소입니다.](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-reference-tsql-language-elements?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
* [Azure Synapse Analytics에서 전용 SQL 풀에 대해 지원 되는 t-sql 문입니다.](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-reference-tsql-statements)
