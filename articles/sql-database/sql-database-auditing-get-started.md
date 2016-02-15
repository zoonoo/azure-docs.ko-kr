<properties
	pageTitle="SQL 데이터베이스 감사 시작 | Microsoft Azure"
	description="SQL 데이터베이스 감사 시작"
	services="sql-database"
	documentationCenter=""
	authors="jeffgoll"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/03/2016"
	ms.author="jeffreyg; ronitr"/>
 
# SQL 데이터베이스 감사 시작
Azure SQL 데이터베이스 감사는 데이터베이스 이벤트를 추적하고 Azure 저장소 계정의 감사 로그에 감사된 이벤트를 씁니다. 감사 기능은 일반적으로 Basic, Standard 및 Premium 서비스 계층에서 제공됩니다.

감사는 규정 준수를 유지 관리하고, 데이터베이스 작업을 이해하고, 비즈니스 문제나 의심스러운 보안 위반을 나타낼 수 있는 불일치 및 이상 활동을 파악하는 데 도움이 될 수 있습니다.

감사 도구를 사용하면 규정 준수 표준을 보다 쉽게 준수할 수 있지만 규정을 완전히 준수한다고 보장할 수는 없습니다. 표준 규정 준수를 지원하는 Azure 프로그램에 대한 자세한 내용은 [Azure 보안 센터](https://azure.microsoft.com/support/trust-center/compliance/)를 참조하세요.

+ [Azure SQL 데이터베이스 감사 기본 사항]
+ [데이터베이스에 대한 감사 설정]
+ [감사 로그 및 보고서 분석]

##<a id="subheading-1"></a>Azure SQL 데이터베이스 감사 기본 사항

다음 섹션에서는 Azure 포털을 사용하여 감사 구성을 설명합니다. [Azure 클래식 포털을 사용하여 데이터베이스에 대한 감사를 설정]할 수도 있습니다.

SQL 데이터베이스 감사를 사용하여 다음을 수행할 수 있습니다.

- 선택한 이벤트의 감사 내역을 **유지**합니다. 감사할 데이터베이스 동작의 범주를 정의할 수 있습니다.
- 데이터베이스 활동을 **보고**합니다. 미리 구성된 보고서 및 대시보드를 사용하여 활동 및 이벤트 보고를 빠르게 시작할 수 있습니다.
- 보고서를 **분석**합니다. 의심스러운 이벤트, 특별한 활동 및 추세를 찾을 수 있습니다.

> [AZURE.NOTE] 이제 미리 보기의 새로운 **위협 감지** 기능을 사용하여 잠재적인 보안 위협을 나타낼 수 있는 비정상적인 데이터베이스 활동에 대해 사전 경고를 받을 수 있습니다. 위협 감지을 켜고 감사 구성 블레이드 내에서 구성할 수 있습니다. 자세한 내용은 [위협 감지 시작](sql-database-threat-detection-get-started.md)을 참조하세요.

다음 이벤트 범주에 대한 감사를 구성할 수 있습니다.

**일반 SQL** 및 수집된 감사 로그를 다음과 같이 분류하는 **매개 변수가 있는 SQL**

- **데이터 액세스**
- **스키마 변경(DDL)**
- **데이터 변경(DML)**
- **계정, 역할 및 사용 권한(DCL)**
- **저장 프로시저**, **로그인** 및 **트랜잭션 관리**.

각 이벤트 범주에 대해 **성공** 및 **실패** 작업의 감사를 따로 구성합니다.

감사된 활동 및 이벤트에 대한 자세한 내용은 [감사 로그 형식 참조(doc 파일 다운로드)](http://go.microsoft.com/fwlink/?LinkId=506733)를 참조하세요.

감사 로그는 Azure 저장소 계정에 저장됩니다. 이전 로그가 삭제되는 감사 로그 보존 기간을 정의할 수 있습니다.

특정 데이터베이스에 대해 또는 기본 서버 정책으로 감사 정책을 정의할 수 있습니다. 기본 서버 감사 정책은 특정 재정의 데이터베이스 감사 정책이 정의되지 않은 서버의 모든 데이터베이스에 적용됩니다.

감사를 설정하기 전에 ["하위 클라이언트"](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md)를 사용 중인지 여부를 확인합니다.


##<a id="subheading-2"></a>데이터베이스에 대한 감사 설정

1. https://portal.azure.com에서 [Azure 포털](https://portal.azure.com)을 시작합니다. 또는 https://manage.windowsazure.com/에서 [Azure 클래식 포털](https://manage.windowsazure.com/)을 시작할 수도 있습니다. 아래에서 자세한 내용을 참조하세요.

2. 감사할 SQL 데이터베이스 / SQL Server의 설정 블레이드로 이동합니다. 설정 블레이드에서 **감사 및 위협 감지**를 선택합니다.

	![탐색 창][1]

3. 감사 구성 블레이드에서 감사를 **켭니다**.

4. **저장소 세부 정보**를 선택하여 감사 로그 저장소 블레이드를 엽니다. 로그를 저장할 Azure 저장소 계정 및 보존 기간을 선택합니다. **팁:** 감사 보고서 서식 파일을 활용하려면 감사되는 모든 데이터베이스에 대해 동일한 저장소 계정을 사용합니다.

	![탐색 창][2]

5. **감사된 이벤트**를 클릭하여 감사할 이벤트를 사용자 지정할 수 있습니다. **이벤트별 로깅** 블레이드에서 **성공** 및 **실패**를 클릭하여 모든 이벤트를 기록하거나 개별 이벤트 범주를 선택합니다.


6. **서버의 감사 설정 상속** 확인란을 선택하여 서버 설정에 따라 이 데이터베이스를 감사하도록 지정할 수 있습니다. 이 옵션을 선택하면 이 컨텍스트의 서버 감사 설정을 보거나 수정할 수 있는 링크가 표시됩니다.

	![탐색 창][3]

7. 감사 설정을 구성하면 위협 감지를 **켜고** 보안 경고를 받을 전자 메일을 구성할 수 있습니다. 자세한 내용은 [위협 감지 시작](sql-database-threat-detection-get-started.md) 페이지를 참조하세요.

8. **Save**를 클릭합니다.



##<a id="subheading-3"></a>감사 로그 및 보고서 분석

감사 로그는 설치 중에 선택한 Azure 저장소 계정의 **SQLDBAuditLogs** 접두사가 포함된 저장소 테이블의 컬렉션에 집계됩니다. [Azure 저장소 탐색기](http://azurestorageexplorer.codeplex.com/)와 같은 도구를 사용하여 로그 파일을 볼 수 있습니다.

미리 구성된 보고서 서식 파일은 로그 데이터를 빠르게 분석하는 데 도움이 되는 [다운로드 가능 Excel 스프레드시트](http://go.microsoft.com/fwlink/?LinkId=403540)로 사용할 수 있습니다. 감사 로그에 템플릿을 사용하려면 Excel 2013 이상과 파워 쿼리가 필요하며 이러한 프로그램은 [여기](http://www.microsoft.com/download/details.aspx?id=39379)에서 다운로드할 수 있습니다.

파워 쿼리를 사용하여 Azure 저장소 계정에서 직접 감사 로그를 Excel 템플릿에 가져올 수 있습니다. 그런 다음 감사 레코드를 탐색하고 로그 데이터 위에 대시보드와 보고서를 만들 수 있습니다.


![탐색 창][4]


##<a id="subheading-4"></a>Azure 클래식 포털을 사용하여 데이터베이스에 대한 감사 설정

1. https://manage.windowsazure.com/에서 [Azure 클래식 포털](https://manage.windowsazure.com/)을 시작합니다.

2. 감사할 SQL 데이터베이스 / SQL 서버를 클릭한 다음 **감사 및 보안** 탭을 클릭합니다.

3. 감사를 **사용**에 설정합니다.

	![탐색 창][5]

4. 필요에 따라 **이벤트별 로깅**을 편집하여 감사할 이벤트를 사용자 지정합니다.

	![탐색 창][6]

5. **저장소 계정**을 선택하고 **보존**을 구성합니다.

	![탐색 창][7]

6. **저장**을 클릭합니다.




##<a id="subheading-5">프로덕션에서 사용하기 위한 연습</a>
이 섹션의 설명에서는 위의 화면 캡처를 참조합니다. [Azure 포털](https://portal.azure.com) 또는 [Azure 클래식 포털](https://manage.windowsazure.com/)을 사용할 수 있습니다.


##<a id="subheading-6"></a>저장소 키 다시 생성

프로덕션에서는 저장소 키를 정기적으로 새로 고칩니다. 키를 새로 고칠 때 감사 정책을 다시 저장해야 합니다. 프로세스는 다음과 같습니다.


1. 감사 구성 블레이드에서 **저장소 액세스 키**를 *기본*에서 *보조*로 전환하고 **저장**을 클릭합니다.

	![][8]

2. 스토리지 구성 블레이드로 이동하고 *기본 액세스 키*를 **다시 생성**합니다.

3. 감사 구성 블레이드로 돌아가서 **저장소 액세스 키**를 *보조*에서 *기본*으로 전환하고 **저장**을 클릭합니다.

4. 저장소 UI로 돌아와서 *보조 액세스 키*를 **다시 생성**합니다(다음 키 새로 고침 주기를 위한 준비).
  
##<a id="subheading-7"></a>자동화
Azure SQL 데이터베이스에서 감사를 구성하는 데 사용할 수 있는 여러 가지 PowerShell cmdlet이 있습니다.

- [Get-AzureRMSqlDatabaseAuditingPolicy](https://msdn.microsoft.com/library/azure/mt603731.aspx)
- [Get-AzureRMSqlServerAuditingPolicy](https://msdn.microsoft.com/library/azure/mt619329.aspx)
- [Remove-AzureRMSqlDatabaseAuditing](https://msdn.microsoft.com/library/azure/mt603796.aspx)
- [Remove-AzureRMSqlServerAuditing](https://msdn.microsoft.com/library/azure/mt603574.aspx)
- [Set-AzureRMSqlDatabaseAuditingPolicy](https://msdn.microsoft.com/library/azure/mt603531.aspx)
- [Set-AzureRMSqlServerAuditingPolicy](https://msdn.microsoft.com/library/azure/mt603794.aspx)
- [Use-AzureRMSqlServerAuditingPolicy](https://msdn.microsoft.com/library/azure/mt619353.aspx)




<!--Anchors-->
[Azure SQL 데이터베이스 감사 기본 사항]: #subheading-1
[데이터베이스에 대한 감사 설정]: #subheading-2
[감사 로그 및 보고서 분석]: #subheading-3
[Azure 클래식 포털을 사용하여 데이터베이스에 대한 감사를 설정]: #subheading-4
[Practices for usage in production]: #subheading-5
[Storage Key Regeneration]: #subheading-6
[Automation]: #subheading-7


<!--Image references-->
[1]: ./media/sql-database-auditing-get-started/1_auditing_get_started_settings.png
[2]: ./media/sql-database-auditing-get-started/2_auditing_get_started_storage_account.png
[3]: ./media/sql-database-auditing-get-started/3_auditing_get_started_inherit_from_server.png
[4]: ./media/sql-database-auditing-get-started/4_auditing_get_started_report_template.png
[5]: ./media/sql-database-auditing-get-started/5_auditing_get_started_classic_portal_enable.png
[6]: ./media/sql-database-auditing-get-started/6_auditing_get_started_classic_portal_events.png
[7]: ./media/sql-database-auditing-get-started/7_auditing_get_started_classic_portal_storage.png
[8]: ./media/sql-database-auditing-get-started/8_auditing_get_started_storage_key_rotation.png


 

<!---HONumber=AcomDC_0204_2016-->