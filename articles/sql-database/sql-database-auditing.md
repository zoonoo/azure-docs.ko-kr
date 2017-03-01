---
title: "Azure SQL Database - 감사 | Microsoft Docs"
description: "Azure SQL Database 감사는 데이터베이스 이벤트를 추적하고 Azure Storage 계정의 감사 로그에 이벤트를 기록합니다."
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
ms.date: 10/05/2016
ms.author: ronitr; giladm
translationtype: Human Translation
ms.sourcegitcommit: 5d51a5ef3387b4c00079547b0f44ffe1f96bd77c
ms.openlocfilehash: 8c9da10e6ebc67c464bf7b5e569cb2113a082d0c
ms.lasthandoff: 02/17/2017


---
# <a name="sql-database-auditing-concepts"></a>SQL Database 감사 개념
Azure SQL Database 감사는 데이터베이스 이벤트를 추적하고 Azure Storage 계정의 감사 로그에 이벤트를 기록합니다.

감사는 규정 준수를 유지 관리하고, 데이터베이스 작업을 이해하고, 비즈니스 문제나 의심스러운 보안 위반을 나타낼 수 있는 불일치 및 이상 활동을 파악하는 데 도움이 될 수 있습니다.

감사를 사용하면 규정 표준을 보다 쉽게 준수할 수 있지만 규정을 완전히 준수한다고 보장할 수는 없습니다. 표준 규정 준수를 지원하는 Azure 프로그램에 대한 자세한 내용은 [Azure 보안 센터](https://azure.microsoft.com/support/trust-center/compliance/)를 참조하세요.

* [Azure SQL Database 감사 개요]
* [데이터베이스에 대한 감사 설정]
* [감사 로그 및 보고서 분석]

## <a name="a-idsubheading-1aazure-sql-database-auditing-overview"></a><a id="subheading-1"></a>Azure SQL Database 감사 개요
SQL 데이터베이스 감사를 사용하여 다음을 수행할 수 있습니다.

* **유지** 합니다. 감사할 데이터베이스 동작의 범주를 정의할 수 있습니다.
* **보고** 합니다. 미리 구성된 보고서 및 대시보드를 사용하여 활동 및 이벤트 보고를 빠르게 시작할 수 있습니다.
* **분석** 합니다. 의심스러운 이벤트, 특별한 활동 및 추세를 찾을 수 있습니다.

두 가지 **감사 방법**이 있습니다.

* **Blob 감사** - Azure Blob Storage에 로그가 기록됩니다. 이 방법은 고성능을 제공하고, 보다 세밀한 개체 수준 감사를 지원하고, 보다 비용 효율적인 새로운 감사 방법입니다.
* **테이블 감사** - Azure Table Storage에 로그가 기록됩니다.

> [!IMPORTANT]
> 새 Blob 감사의 도입은 데이터베이스에서 상속되는 서버 감사 정책 방식에 주요 변경 사항을 제공합니다. 

다양한 유형의 이벤트 범주에 대한 감사를 구성할 수 있습니다.

* Azure Portal을 사용하여 감사를 구성 및 관리하려면 [Azure Portal을 사용하여 감사](sql-database-auditing-portal.md)를 참조하세요.
* PowerShell을 사용하여 감사를 구성 및 관리하려면 [PowerShell을 사용하여 감사](sql-database-auditing-powershell.md)를 참조하세요.
* REST API를 사용하여 감사를 구성 및 관리하려면 [REST API를 사용하여 감사](sql-database-auditing-rest.md)를 참조하세요.

<!--For each Event Category, auditing of **Success** and **Failure** operations are configured separately.-->

특정 데이터베이스에 대해 또는 기본 서버 정책으로 감사 정책을 정의할 수 있습니다. 서버에 있는 모든 기존 데이터베이스 및 새로 만든 데이터베이스에 기본 서버 감사 정책이 적용됩니다.

## <a name="blob-auditing"></a>Blob 감사

서버 Blob 감사가 설정된 경우 다음에 관계 없이 항상 데이터베이스에 적용됩니다(서버의 모든 데이터베이스는 감사됨).
    - 데이터베이스 감사 설정.
    - 데이터베이스 블레이드에서 "서버에서 상속 설정" 확인란의 선택 여부.

> [!IMPORTANT]
> 서버에서 활성화하는 것 외에도 데이터베이스에 대한 Blob 감사를 활성화하는 것은 서버 Blob 감사 설정을 재정의 또는 변경하지 **않습니다**. 두 감사는 함께 존재합니다. 즉, 데이터베이스는 동시에 두 번 감사됩니다(서버 정책에서 한 번, 데이터베이스 정책에서 한번).

다음과 같은 경우가 아니면 서버 Blob 감사 및 데이터베이스 Blob 감사를 함께 활성화하지 않아야 합니다.
 * 특정 데이터베이스에 대해 다른 *저장소 계정* 또는 *보존 기간*을 사용해야 합니다.
 * 이 서버의 나머지 데이터베이스에 대해 감사되고 있는 특정 데이터베이스에 대해 다른 이벤트 유형 또는 범주를 감사하려고 합니다(예: 테이블 삽입이 특정 데이터베이스에 대해서만 감사되어야 하는 경우).

그렇지 않으면 서버 수준 Blob 감사만 활성화하도록 권장하며 모든 데이터베이스에 대해 데이터베이스 수준 감사를 비활성화로 유지합니다.

## <a name="table-auditing"></a>테이블 감사

서버 수준 테이블 감사가 활성화된 경우 데이터베이스 블레이드의 "서버에서 상속 설정" 확인란이 선택된 경우에만 데이터베이스에 적용됩니다(모든 기존 및 새로 만든 데이터베이스에 대해서는 기본적으로 선택됨).

- 확인란이 *선택*된 경우 데이터베이스의 감사 설정에 대한 변경 내용은 이 데이터베이스에 대한 서버 설정을 **재정의**합니다.

- 확인란이 *선택되지 않고* 데이터베이스 수준 감사가 비활성화된 경우 데이터베이스는 감사되지 않습니다.

## <a name="analyze-audit-logs-and-reports"></a>감사 로그 및 보고서 분석
감사 로그는 설치 중에 선택한 Azure 저장소 계정에 집계됩니다.

[Azure 저장소 탐색기](http://storageexplorer.com/) 등의 도구를 사용하여 감사 로그를 탐색할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* Azure Portal을 사용하여 감사를 구성 및 관리하려면 [Azure Portal에서 감사 구성](sql-database-auditing-portal.md)을 참조하세요.
* PowerShell을 사용하여 감사를 구성 및 관리하려면 [PowerShell에서 감사 구성](sql-database-auditing-powershell.md)을 참조하세요.
* REST API를 사용하여 감사를 구성 및 관리하려면 [REST API에서 감사 구성](sql-database-auditing-rest.md)을 참조하세요.


<!--Anchors-->
[Azure SQL Database 감사 개요]: #subheading-1
[데이터베이스에 대한 감사 설정]: #subheading-2
[감사 로그 및 보고서 분석]: #subheading-3
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

