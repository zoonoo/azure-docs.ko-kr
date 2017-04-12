---
title: "Azure Portal: SQL Database 감사 관리 | Microsoft Docs"
description: "데이터베이스 이벤트를 추적하여 Azure Storage 계정의 감사 로그에 기록하도록 Azure Portal에서 Azure SQL Database 감사를 구성합니다."
services: sql-database
documentationcenter: 
author: ronitr
manager: jhubbard
editor: giladm
ms.assetid: 89c2a155-c2fb-4b67-bc19-9b4e03c6d3bc
ms.service: sql-database
ms.custom: secure and protect
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 2/25/2017
ms.author: ronitr;giladm
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: e20a30a565198c01f213895a87fe8807a37272cb
ms.lasthandoff: 03/30/2017


---

# <a name="configure-and-manage-sql-database-auditing-in-the-azure-portal"></a>Azure Portal에서 SQL Database 감사 구성 및 관리

다음 섹션에서는 Azure Portal을 사용하여 감사를 구성 및 관리하는 방법을 설명합니다. PowerShell을 사용하여 감사를 구성 및 관리하려면 [PowerShell에서 감사 구성](scripts/sql-database-create-and-configure-database-powershell.md)을 참조하세요. 

감사의 개요는 [SQL Database 감사](sql-database-auditing.md)를 참조하세요.

## <a name="configure-blob-auditing"></a>Blob 감사 구성

1. https://portal.azure.com에서 [Azure Portal](https://portal.azure.com)을 실행합니다.
2. 감사할 SQL Database / SQL Server의 설정 블레이드로 이동합니다. 설정 블레이드에서 **감사 및 위협 감지**를 선택합니다.

    ![탐색 창](./media/sql-database-auditing-get-started/1_auditing_get_started_settings.png)
3. 데이터베이스 감사 구성 블레이드에서 **서버의 설정 상속** 확인란을 선택하여 서버 설정에 따라 이 데이터베이스를 감사하도록 지정할 수 있습니다. 이 옵션을 선택하면 이 컨텍스트의 서버 감사 설정을 보거나 수정할 수 있는 **서버 감사 설정 보기** 링크가 표시됩니다.

    ![탐색 창][2]
4. 서버 수준 감사에 추가로 또는 서버 수준 감사 대신 데이터베이스 수준에서 Blob 감사를 사용하려면 **서버의 감사 설정 상속** 옵션을 **선택 취소**하고, 감사를 **켜고**, **Blob** 감사 유형을 선택합니다.

    ![탐색 창][3]
5. **저장소 세부 정보**를 선택하여 감사 로그 저장소 블레이드를 엽니다. 로그가 저장되는 Azure Storage 계정을 선택하고, 이 기간 후 오래된 로그가 삭제되는 보존 기간을 선택한 다음 하단에서 **확인**을 클릭합니다. **팁:** 감사 보고서 서식 파일을 활용하려면 감사되는 모든 데이터베이스에 대해 동일한 저장소 계정을 사용합니다.

    <a id="storage-screenshot"></a>
    ![탐색 창][4]
6. 감사 이벤트를 사용자 지정하려면 PowerShell 또는 REST API를 통해 다음 작업을 수행합니다.
7. 감사 설정을 구성했으면 새로운 위협 감지(미리 보기) 기능을 켜고, 보안 경고를 받을 전자 메일을 구성할 수 있습니다. 위협 감지 기능을 사용하여 잠재적인 보안 위협을 나타낼 수 있는 비정상적인 데이터베이스 활동에 대해 사전 경고를 받을 수 있습니다. 자세한 내용은 [위협 감지](sql-database-threat-detection.md)를 참조하세요.
8. **Save**를 클릭합니다.


## <a name="table-auditing"></a>테이블 감사

> [!IMPORTANT]
> **테이블 감사**를 설정하기 전에 ["하위 클라이언트"](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md)를 사용 중인지 확인합니다. 또한 엄격한 방화벽 설정이 있으면 테이블 감사를 사용하도록 설정할 때 [데이터베이스의 IP 끝점이 변경됩니다](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md).
>

1. https://portal.azure.com에서 [Azure Portal](https://portal.azure.com)을 실행합니다.
2. 감사할 SQL Database / SQL Server의 설정 블레이드로 이동합니다. 설정 블레이드에서 **감사 및 위협 감지**를 선택합니다(*[Blob 감사 섹션의 스크린샷 참조](#auditing-screenshot)*).
3. 데이터베이스 감사 구성 블레이드에서 **서버의 설정 상속** 확인란을 선택하여 서버 설정에 따라 이 데이터베이스를 감사하도록 지정할 수 있습니다. 이 옵션을 선택하면 이 컨텍스트의 서버 감사 설정을 보거나 수정할 수 있는 **서버 감사 설정 보기** 링크가 표시됩니다.

    ![탐색 창][2]
4. 서버의 감사 설정을 상속하지 않으려면 **서버의 감사 설정 상속** 옵션을 **선택 취소**하고, 감사를 **켜고**, **테이블** 감사 유형을 선택합니다.

    ![탐색 창][3-tbl]
5. **저장소 세부 정보**를 선택하여 감사 로그 저장소 블레이드를 엽니다. 로그가 저장되는 Azure Storage 계정을 선택하고, 이 기간 후 오래된 로그가 삭제되는 보존 기간을 선택합니다. **팁:** 감사 보고서 템플릿을 활용하려면 감사되는 모든 데이터베이스에 대해 동일한 저장소 계정을 사용합니다(*[Blob 감사 섹션의 스크린샷 참조](#storage-screenshot)*).
6. **감사된 이벤트** 를 클릭하여 감사할 이벤트를 사용자 지정할 수 있습니다. 이벤트별 로깅 블레이드에서 **성공** 및 **실패**를 클릭하여 모든 이벤트를 기록하거나 개별 이벤트 범주를 선택합니다.

    ![탐색 창][5]
7. 감사 설정을 구성했으면 새로운 위협 감지(미리 보기) 기능을 켜고, 보안 경고를 받을 전자 메일을 구성할 수 있습니다. 위협 감지 기능을 사용하여 잠재적인 보안 위협을 나타낼 수 있는 비정상적인 데이터베이스 활동에 대해 사전 경고를 받을 수 있습니다. 자세한 내용은 [위협 감지](sql-database-threat-detection.md)를 참조하세요.
8. **Save**를 클릭합니다.

## <a name="auditing-geo-replicated-databases"></a>지역에서 복제된 데이터베이스 감사

지역에서 복제된 데이터베이스를 사용하는 경우 감사 유형에 따라 주 데이터베이스, 보조 데이터베이스 또는 둘 모두에 감사를 설정할 수 있습니다.

**테이블 감사:** 두 데이터베이스(주 및 보조) 각각에 대해 데이터베이스 또는 서버 수준에서 별도의 정책을 구성할 수 있습니다.

**Blob 감사:** 다음 지침을 따릅니다.

1. **주 데이터베이스:** 서버 또는 데이터베이스 자체에서 Blob 감사를 켭니다.
2. **보조 데이터베이스:** Blob 감사는 주 데이터베이스 감사 설정에서만 켜고 끌 수 있습니다.

   * 주 데이터베이스에서 Blob 감사 켜기 Blob 감사는 서버가 아니라 *주 데이터베이스 자체*에서 활성화해야 합니다.
   * 주 데이터베이스에서 Blob 감사가 활성화되면 보조 데이터베이스에서도 활성화됩니다.

    > [!IMPORTANT]
    > 기본적으로는 보조 데이터베이스의 저장소 설정은 주 데이터베이스와 동일하기 때문에 지역 간 트래픽이 발생합니다. 보조 서버에서 Blob 감사를 활성화하고 보조 서버 저장소 설정에서 로컬 저장소를 구성하면(이렇게 하면 보조 데이터베이스의 저장소 위치가 재정의되어 각 데이터베이스가 감사 로그를 로컬 저장소에 저장함) 이 문제를 피할 수 있습니다.  

## <a name="viewing-blob-auditing-logs"></a>Blob 감사 로그 보기

Blob 감사 로그는 "**sqldbauditlogs**"라는 컨테이너 내부에 Blob 파일 컬렉션으로 저장됩니다.

Blob 감사 로그 저장소 폴더 계층 구조, Blob 명명 규칙 및 로그 형식에 대한 자세한 내용은 [감사 로그 형식 참조(doc 파일 다운로드)](https://go.microsoft.com/fwlink/?linkid=829599)를 참조하세요.

Blob 감사 로그를 보는 여러 가지 방법이 있습니다.

* [Azure Portal](https://portal.azure.com)을 통해 - 관련 데이터베이스를 엽니다. 데이터베이스의 감사 및 위협 감지 블레이드 맨 위에서 **감사 로그 보기**를 클릭합니다.

    ![탐색 창][10]

    감사 레코드 블레이드가 열리고, 여기서 로그를 볼 수 있습니다.

    - 감사 레코드 블레이드의 맨 위쪽 영역에서 **필터**를 클릭하여 특정 날짜를 선택할 수 있습니다.
    - 서버 정책 또는 데이터베이스 정책 감사에서 생성된 감사 레코드 사이를 전환할 수 있습니다.

    ![탐색 창][11]
* 포털을 통해 또는 [Azure Storage Explorer](http://storageexplorer.com/) 같은 도구를 사용하여 Azure Storage Blob 컨테이너에서 로그 파일을 다운로드합니다.

    로그 파일을 로컬에 다운로드한 후에는 해당 파일을 두 번 클릭하여 열고, SSMS에서 로그를 살펴보고 분석할 수 있습니다.

* 추가 방법:

   * Azure Storage Explorer를 통해 동시에 여러 파일을 다운로드할 수 있습니다. 특정 하위 폴더(특정 날짜의 모든 로그 파일이 포함된 하위 폴더)를 마우스 오른쪽 단추로 클릭하고 **다른 이름으로 저장**을 선택하여 로컬 폴더에 저장합니다.

       여러 파일(또는 위에서 설명한 대로 하루 전체)을 다운로드한 후에는 다음과 같이 여러 파일을 로컬에 병합할 수 있습니다.

       **SSMS 열기 -> 파일 -> 열기 -> 확장 이벤트 병합 -> 병합할 모든 파일 선택**
   * 프로그래밍 방식:

     * [확장 이벤트 판독기 C# 라이브러리](https://blogs.msdn.microsoft.com/extended_events/2011/07/20/introducing-the-extended-events-reader/)
     * [PowerShell을 사용하여 확장 이벤트 파일 쿼리](https://sqlscope.wordpress.com/2014/11/15/reading-extended-event-files-using-client-side-tools-only/)

   * Azure에서 실행되고 OMS 공용 API를 활용하여 OMS 대시보드를 통해 소비에 대한 OMS로 SQL 감사 로그를 밀어 넣는 [응용 프로그램 예제](https://github.com/Microsoft/Azure-SQL-DB-auditing-OMS-integration)를 만들었습니다.

## <a name="viewing-table-audit-logs"></a>테이블 감사 로그 보기

테이블 감사 로그는 **SQLDBAuditLogs** 접두사가 붙은 Azure Storage 테이블 컬렉션으로 저장됩니다.

테이블 감사 로그 형식에 대한 자세한 내용은 [테이블 감사 로그 형식 참조(doc 파일 다운로드)](http://go.microsoft.com/fwlink/?LinkId=506733)를 참조하세요.

테이블 감사 로그를 보는 여러 가지 방법이 있습니다.

* [Azure Portal](https://portal.azure.com)을 통해 - 관련 데이터베이스를 엽니다. 데이터베이스의 감사 및 위협 감지 블레이드 맨 위에서 **감사 로그 보기**를 클릭합니다.

    ![탐색 창][10]

    감사 레코드 블레이드가 열리고, 여기서 로그를 볼 수 있습니다.

    * 감사 레코드 블레이드의 맨 위쪽 영역에서 **필터**를 클릭하여 특정 날짜를 선택할 수 있습니다.
    * 감사 레코드 블레이드의 맨 위쪽 영역에서 **Excel에서 열기**를 클릭하면 감사 로그를 Excel 형식으로 다운로드하여 볼 수 있습니다.

    ![탐색 창][12]

* 또는 미리 구성된 보고서 템플릿이 로그 데이터를 빠르게 분석하는 데 도움이 되는 [다운로드 가능 Excel 스프레드시트](http://go.microsoft.com/fwlink/?LinkId=403540)로 제공됩니다. 감사 로그에 템플릿을 사용하려면 Excel 2013 이상과 [파워 쿼리](http://www.microsoft.com/download/details.aspx?id=39379)가 필요합니다.

* 또한 파워 쿼리를 사용하여 Azure 저장소 계정에서 직접 감사 로그를 Excel 템플릿에 가져올 수 있습니다. 그런 다음 감사 레코드를 탐색하고 로그 데이터 위에 대시보드와 보고서를 만들 수 있습니다.

    ![탐색 창][9]

## <a name="storage-key-regeneration"></a>저장소 키 다시 생성
프로덕션에서는 저장소 키를 주기적으로 새로 고치게 됩니다. 키를 새로 고칠 때 감사 정책을 다시 저장해야 합니다. 프로세스는 다음과 같습니다.

1. 저장소 세부 정보 블레이드에서 **저장소 액세스 키**를 *기본*에서 *보조*로 전환하고 맨 아래에서 **확인**을 클릭합니다. 그런 다음 감사 구성 블레이드의 맨 위에서 **저장**을 클릭합니다.

    ![탐색 창][6]
2. 스토리지 구성 블레이드로 이동하고 *기본 액세스 키*를 **다시 생성**합니다.

    ![탐색 창][8]
3. 감사 구성 블레이드로 돌아가서 **저장소 액세스 키**를 *보조*에서 *기본*으로 전환하고 맨 아래에서 **확인**을 클릭합니다. 그런 다음 감사 구성 블레이드의 맨 위에서 **저장**을 클릭합니다.
4. 저장소 구성 블레이드로 돌아와서 **보조 액세스 키**를 *다시 생성*합니다(다음 키 새로 고침 주기를 위한 준비).


<!--Anchors-->
[Azure SQL Database Auditing overview]: #subheading-1
[Set up auditing for your database]: #subheading-2
[Analyze audit logs and reports]: #subheading-3
[Practices for usage in production]: #subheading-5
[Storage Key Regeneration]: #subheading-6
[Automation (PowerShell / REST API)]: #subheading-7
[Blob/Table differences in Server auditing policy inheritance]: (#subheading-8)  

<!--Image references-->
[1]: ./media/sql-database-auditing-get-started/1_auditing_get_started_settings.png
[2]: ./media/sql-database-auditing-get-started/2_auditing_get_started_server_inherit.png
[3]: ./media/sql-database-auditing-get-started/3_auditing_get_started_turn_on.png
[3-tbl]: ./media/sql-database-auditing-get-started/3_auditing_get_started_turn_on_table.png
[4]: ./media/sql-database-auditing-get-started/4_auditing_get_started_storage_details.png
[5]: ./media/sql-database-auditing-get-started/5_auditing_get_started_audited_events.png
[6]: ./media/sql-database-auditing-get-started/6_auditing_get_started_storage_key_regeneration.png
[7]: ./media/sql-database-auditing-get-started/7_auditing_get_started_activity_log.png
[8]: ./media/sql-database-auditing-get-started/8_auditing_get_started_regenerate_key.png
[9]: ./media/sql-database-auditing-get-started/9_auditing_get_started_report_template.png
[10]: ./media/sql-database-auditing-get-started/10_auditing_get_started_blob_view_audit_logs.png
[11]: ./media/sql-database-auditing-get-started/11_auditing_get_started_blob_audit_records.png
[12]: ./media/sql-database-auditing-get-started/12_auditing_get_started_table_audit_records.png

## <a name="next-steps"></a>다음 단계

* PowerShell을 사용하여 감사를 구성 및 관리하려면 [PowerShell을 사용하여 데이터베이스 감사 구성](scripts/sql-database-create-and-configure-database-powershell.md)을 참조하세요.
* 감사의 개요는 [데이터베이스 감사](sql-database-auditing.md)를 참조하세요.


