---
title: Azure SQL Data Warehouse 감사 | Microsoft Docs
description: 감사 및 Azure SQL Data Warehouse에서 감사를 설정하는 방법에 대해 알아봅니다.
services: sql-data-warehouse
author: kavithaj
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/11/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: d35ac7cac5c14a7bd57913046e8f4c09a22f177a
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/14/2019
ms.locfileid: "54267439"
---
# <a name="auditing-in-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse 감사

감사 및 Azure SQL Data Warehouse에서 감사를 설정하는 방법에 대해 알아봅니다.

## <a name="what-is-auditing"></a>감사란?
SQL Data Warehouse 감사를 사용하면 Azure Storage 계정의 감사 로그에 데이터베이스의 이벤트를 기록할 수 있습니다. 감사는 규정 준수를 유지 관리하고, 데이터베이스 작업을 이해하고, 비즈니스 문제나 의심스러운 보안 위반을 나타낼 수 있는 불일치 및 이상 활동을 파악하는 데 도움이 될 수 있습니다.

감사 도구를 사용하면 규정 준수 표준을 보다 쉽게 준수할 수 있지만 규정을 완전히 준수한다고 보장할 수는 없습니다. 표준 규정 준수를 지원하는 Azure 프로그램에 대한 자세한 내용은 [Azure 보안 센터](https://azure.microsoft.com/support/trust-center/compliance/)를 참조하세요.

## <a id="subheading-1"></a>감사 기본 사항
SQL Data Warehouse 데이터베이스 감사를 사용하여 다음을 수행할 수 있습니다.

* **유지** 합니다. 감사할 데이터베이스 동작의 범주를 정의할 수 있습니다.
* **보고** 합니다. 미리 구성된 보고서 및 대시보드를 사용하여 활동 및 이벤트 보고를 빠르게 시작할 수 있습니다.
* **분석** 합니다. 의심스러운 이벤트, 특별한 활동 및 추세를 찾을 수 있습니다.

감사 로그는 Azure 저장소 계정에 저장됩니다. 감사 로그의 보존 기간을 정의할 수 있습니다.


## <a id="subheading-4"></a>서버 수준 및 데이터베이스 수준 감사 정책 정의

특정 데이터베이스에 대해 또는 기본 서버 정책으로 감사 정책을 정의할 수 있습니다.

* 서버에 있는 **모든 기존 데이터베이스 및 새로 만든 데이터베이스에** 서버 정책이 적용됩니다.

* *서버 BLOB 감사를 사용하는 경우* *항상 데이터베이스에 적용됩니다*. 데이터베이스 감사 설정에 관계없이 데이터베이스를 감사합니다.

* 서버에서 활성화하는 것 외에도 데이터베이스에서 감사를 활성화하는 것은 서버 Blob 감사 설정을 재정의 또는 변경하지 *않습니다*. 두 감사는 함께 존재합니다. 즉, 데이터베이스는 동시에 두 번 감사됩니다(서버 정책에서 한 번, 데이터베이스 정책에서 한번).

> [!NOTE]
> **서버 수준 Blob 감사만** 활성화하고 모든 데이터베이스에 대해 데이터베이스 수준 감사를 비활성화로 유지하는 것이 좋습니다.
> 다음과 같은 경우가 아니면 서버 감사 및 데이터베이스 감사를 함께 활성화하지 않아야 합니다.
> * 특정 데이터베이스에 대해 다른 *저장소 계정* 또는 *보존 기간*을 사용할 수 있습니다.
> * 서버의 나머지 데이터베이스와는 다른 특정 데이터베이스에 대해 이벤트 형식이나 범주를 감사하려 합니다. 예를 들어 특정 데이터베이스에 대해서만 감사해야 하는 테이블 삽입이 있을 수 있습니다.
> * 현재 데이터베이스 수준 감사로만 지원되는 위협 감지를 사용하려고 합니다.

> [!IMPORTANT]
>Azure SQL Data Warehouse 또는 Azure SQL Data Warehouse가 있는 서버에서 감사를 사용하도록 설정하면 이전에 일시 중지된 경우에도 **Data Warehouse가 다시 시작됩니다**. **감사를 사용하도록 설정한 후에 Data Warehouse를 일시 중지해야 합니다**.

## <a id="subheading-5"></a>모든 데이터베이스에 대한 서버 수준 감사 설정

서버에 있는 **모든 기존 데이터베이스 및 새로 만든 데이터베이스에** 서버 감사 정책이 적용됩니다.

다음 섹션에서는 Azure Portal을 사용하여 감사 구성을 설명합니다.

1. [Azure 포털](https://portal.azure.com)로 이동합니다.
2. 감사하려는 **SQL 서버**로 이동합니다(특정 데이터베이스/DW가 아닌 SQL 서버를 보고 있는지 확인). **보안** 메뉴에서 **감사 및 위협 감지**를 선택합니다.

    ![탐색 창][6]
4. *감사 및 위협 감지* 블레이드의 **감사**에 대해 **켜기**를 선택합니다. 이 감사 정책은 이 서버의 모든 기존 및 새로 만든 데이터베이스에 적용됩니다.

    ![탐색 창][7]
5. **감사 로그 저장소** 블레이드를 열려면 **저장소 세부 정보**를 선택합니다. 로그가 저장되는 Azure 저장소 계정을 선택하거나 만든 다음, 보존 기간을 선택합니다(오래된 로그는 삭제됨). 그런 후 **OK**를 클릭합니다.

    ![탐색 창][8]

    > [!IMPORTANT]
    > 서버 수준 감사 로그는 Azure 구독의 Azure Blob Storage에 있는 **Blob 추가**에 기록됩니다.
    >
    > * **Premium Storage**는 현재 Blob 추가에서 **지원되지 않습니다**.
    > * **VNet의 저장소**는 현재 **지원되지 않습니다**.

8. **저장**을 클릭합니다.



## <a id="subheading-2"></a>단일 데이터베이스에 대한 데이터베이스 수준 감사 설정

특정 데이터베이스에 대해 또는 기본 서버 정책으로 감사 정책을 정의할 수 있습니다.

[서버 수준 및 데이터베이스 수준 감사 정책 정의](#subheading-4)에 설명된 대로 데이터베이스 수준 감사가 아닌 서버 수준 감사를 사용하는 것이 가장 좋습니다.

감사를 설정하기 전에 ["하위 클라이언트"](sql-data-warehouse-auditing-downlevel-clients.md)를 사용 중인지 여부를 점검합니다.


1. [Azure Portal](https://portal.azure.com)을 시작합니다.
2. 감사하려는 SQL Data Warehouse의 **설정**으로 이동합니다. **감사 및 위협 감지**를 선택합니다.

    ![][1]
3. 다음으로 **켜기** 단추를 클릭하여 감사를 사용합니다.

    ![][3]
4. 감사 구성 패널에서 **저장소 정보**를 선택하여 감사 로그 저장소 패널을 엽니다. 로그에 대한 Azure Storage 계정 및 보존 기간을 선택합니다.
    >[!TIP]
    >미리 구성된 보고서 템플릿을 활용하려면 감사되는 모든 데이터베이스에 대해 동일한 저장소 계정을 사용합니다.

    ![][4]

5. **확인** 단추를 클릭하여 저장소 세부 정보 구성을 저장합니다.
6. 아래에서 **이벤트별 로깅** 아래에서 **성공** 및 **실패**를 클릭하여 모든 이벤트를 기록하거나 개별 이벤트 범주를 선택합니다.
7. 데이터베이스에 대한 감사를 구성하는 경우 클라이언트의 연결 문자열을 변경하여 데이터 감사가 바르게 캡처되는지 확인해야 합니다. [연결 문자열에서 서버 FQDN 수정](sql-data-warehouse-auditing-downlevel-clients.md) 항목에서 하위 클라이언트 연결을 확인합니다.
8. **확인**을 클릭합니다.

## <a id="subheading-3"></a>감사 로그 및 보고서 분석

### <a name="server-level-policy-audit-logs"></a>서버 수준 정책 감사 로그
서버 수준 감사 로그는 Azure 구독의 Azure Blob Storage에 있는 **Blob 추가**에 기록됩니다. **sqldbauditlogs**라는 컨테이너 내부에 Blob 파일 컬렉션으로 저장됩니다.

저장소 폴더의 계층 구조, 명명 규칙 및 로그 형식에 대한 자세한 내용은 [BLOB 감사 로그 형식 참조](https://go.microsoft.com/fwlink/?linkid=829599)를 참조하세요.

Blob 감사 로그를 볼 수 있는 여러 가지 방법이 있습니다.

* SQL Server Management Studio에서 **감사 파일 병합** 사용(SSMS 17부터 지원):
    1. SSMS 메뉴에서 **파일** > **열기** > **감사 파일 병합**을 선택합니다.

    2. **감사 파일 추가** 대화 상자가 열립니다. **추가** 옵션 중 하나를 선택하여 로컬 디스크에서 감사 파일을 병합할지 또는 Azure Storage에서 감사 파일을 가져올지 선택합니다 Azure Storage 세부 정보 및 계정 키를 제공해야 합니다.

    3. 병합할 모든 파일을 추가했으면 **확인**을 클릭하여 병합 작업을 완료합니다.

    4. 병합된 파일이 SSMS에서 열립니다. 여기에서 파일을 보고 분석할 수 있을 뿐만 아니라 XEL 또는 CSV 파일이나 테이블로 내보낼 수 있습니다.

* Microsoft에서 만든 [동기화 애플리케이션](https://github.com/Microsoft/Azure-SQL-DB-auditing-OMS-integration)을 사용합니다. 이 응용 프로그램은 Azure에서 실행되며 Log Analytics 공용 API를 활용하여 SQL 감사 로그를 Log Analytics에 푸시합니다. 동기화 애플리케이션은 Log Analytics 대시보드를 통해 사용할 수 있도록 Log Analytics에 SQL 감사 로그를 푸시합니다.

* Power BI를 사용합니다. Power BI에서 감사 로그 데이터를 보고 분석할 수 있습니다. 자세한 내용은 [Power BI 및 다운로드 가능한 템플릿 액세스](https://blogs.msdn.microsoft.com/azuresqldbsupport/2017/05/26/sql-azure-blob-auditing-basic-power-bi-dashboard/)를 참조하세요.

* 포털을 통해 또는 [Azure Storage 탐색기](http://storageexplorer.com/) 같은 도구를 사용하여 Azure Storage Blob 컨테이너에서 로그 파일을 다운로드합니다.
    * 로그 파일을 로컬에 다운로드한 후에는 해당 파일을 두 번 클릭하여 열고, SSMS에서 로그를 살펴보고 분석할 수 있습니다.
    * 또한 Azure Storage 탐색기를 통해 동시에 여러 파일을 다운로드할 수 있습니다. 특정 하위 폴더를 마우스 오른쪽 단추로 클릭하고 **다른 이름으로 저장**을 선택하여 로컬 폴더에 저장합니다.

* 추가 방법:
   * 여러 파일 또는 로그 파일이 포함된 하위 폴더를 다운로드한 후 SSMS 감사 파일 병합 지침에 설명된 대로 파일을 로컬로 병합할 수 있습니다.

   * 프로그래밍 방식으로 Blob 감사 로그를 확인합니다.

     * [확장 이벤트 판독기](https://blogs.msdn.microsoft.com/extended_events/2011/07/20/introducing-the-extended-events-reader/) C# 라이브러리를 사용합니다.
     * PowerShell을 사용하여 [확장 이벤트 파일을 쿼리](https://sqlscope.wordpress.com/2014/11/15/reading-extended-event-files-using-client-side-tools-only/)합니다.



<br>
### <a name="database-level-policy-audit-logs"></a>데이터베이스 수준 정책 감사 로그
데이터베이스 수준 감사 로그는 설치 중에 선택한 Azure 저장소 계정의 **SQLDBAuditLogs** 접두사가 포함된 저장소 테이블의 컬렉션에 집계됩니다. [Azure Storage 탐색기](http://azurestorageexplorer.codeplex.com)와 같은 도구를 사용하여 로그 파일을 볼 수 있습니다.

미리 구성된 대시보드 보고서 템플릿은 로그 데이터를 빠르게 분석하는 데 도움이 되는 [다운로드 가능 Excel 스프레드시트](https://go.microsoft.com/fwlink/?LinkId=403540)로 사용할 수 있습니다. 감사 로그의 템플릿을 사용하려면 Excel 2013 이상과 파워 쿼리가 필요하며 이러한 프로그램은 [여기](https://www.microsoft.com/download/details.aspx?id=39379)에서 다운로드할 수 있습니다.

템플릿에는 가상의 샘플 데이터가 포함되어 있으며 Azure 저장소 계정에서 직접 감사 로그를 가져오도록 파워 쿼리를 설정할 수 있습니다.

## <a id="subheading-4"></a>Storage 키 다시 생성
프로덕션에서는 저장소 키를 주기적으로 새로 고치게 됩니다. 키를 새로 고칠 때 정책을 저장해야 합니다. 프로세스는 다음과 같습니다.

1. 감사 구성 패널(이전 감사 설정 섹션의 설명 참조)에서 **저장소 액세스 키**를 *기본*에서 *보조*로 변경하고 **저장**을 클릭합니다.

   ![][4]
2. 저장소 구성 패널로 이동하고 *기본 액세스 키*를 **다시 생성**합니다.
3. 감사 구성 패널으로 돌아간 후
4. **저장소 액세스 키**를 *보조*에서 *기본*으로 전환하고 **저장**을 누릅니다.
4. 저장소 UI로 돌아와서 **보조 선택키** 를 *다시 생성* 합니다(다음 키 새로 고침 주기를 위한 준비).

## <a id="subheading-5"></a>Automation(PowerShell/REST API)
또한 다음 자동화 도구를 사용하여 Azure SQL Data Warehouse에서 감사를 구성할 수도 있습니다.

* **PowerShell cmdlet**:

   * [Get-AzureRMSqlDatabaseAuditingPolicy](/powershell/module/azurerm.sql/get-azurermsqldatabaseauditingpolicy)
   * [Get-AzureRMSqlServerAuditingPolicy](/powershell/module/azurerm.sql/Get-AzureRMSqlServerAuditingPolicy)
   * [Remove-AzureRMSqlDatabaseAuditing](/powershell/module/azurerm.sql/Remove-AzureRMSqlDatabaseAuditing)
   * [Remove-AzureRMSqlServerAuditing](/powershell/module/azurerm.sql/Remove-AzureRMSqlServerAuditing)
   * [Set-AzureRMSqlDatabaseAuditingPolicy](/powershell/module/azurerm.sql/Set-AzureRMSqlDatabaseAuditingPolicy)
   * [Set-AzureRMSqlServerAuditingPolicy](/powershell/module/azurerm.sql/Set-AzureRMSqlServerAuditingPolicy)
   * [Use-AzureRMSqlServerAuditingPolicy](/powershell/module/azurerm.sql/Use-AzureRMSqlServerAuditingPolicy)


## <a name="downlevel-clients-support-for-auditing-and-dynamic-data-masking"></a>감사 및 동적 데이터 마스킹에 대한 하위 수준 클라이언트 지원
감사는 TDS 리디렉션을 지원하는 SQL 클라이언트와 함께 작동합니다.

TDS 7.4를 구현하는 모든 클라이언트는 리디렉션도 지원해야 합니다. 이에 대한 예외에는 리디렉션 기능이 완전히 지원되지 않는 JDBC 4.0 및 리디렉션이 구현되지 않은 Node.JS용 Tedious가 포함됩니다.

TDS 버전 7.3 이하를 지원하는 "하위 클라이언트"의 경우, 연결 문자열에서 서버 FQDN을 다음과 같이 수정합니다.

- 연결 문자열에서 원래 서버 FQDN: <*서버 이름*>.database.windows.net
- 연결 문자열에서 수정된 서버 FQDN: <*서버 이름*>.database.**secure**.windows.net

"하위 클라이언트"의 일부 목록에는 다음이 포함됩니다.

* .NET 4.0 이하
* ODBC 10.0 이하
* JDBC(JDBC는 TDS 7.4를 지원하지만 TDS 리디렉션 기능은 완전히 지원되지 않음)
* Tedious(Node.JS용)

**주석:** 위의 서버 FQDN 수정은 각 데이터베이스에서 구성 단계에 대한 요구 없이 SQL 서버 수준 감사 정책의 적용에도 유용할 수 있습니다.     




<!--Anchors-->
[Database Auditing basics]: #subheading-1
[Set up auditing for your database]: #subheading-2
[Analyze audit logs and reports]: #subheading-3
[Define server-level vs. database-level auditing policy]: #subheading-4
[Set up server-level auditing for all databases]: #subheading-5

<!--Image references-->
[1]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing.png
[2]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-inherit.png
[3]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-enable.png
[4]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-storage-account.png
[5]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-dashboard.png
[6]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-server_1_overview.png
[7]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-server_2_enable.png
[8]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-server_3_storage.png
