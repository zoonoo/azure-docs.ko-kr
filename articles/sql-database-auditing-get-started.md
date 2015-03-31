<properties 
	pageTitle="SQL 데이터베이스 감사 시작 | Azure" 
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
	ms.date="02/23/2015" 
	ms.author="jeffreyg"/>
 
# SQL 데이터베이스 감사 시작 
<p> Azure SQL 데이터베이스 감사는 데이터베이스 이벤트를 추적하고 Azure 저장소 계정의 감사 로그에 감사된 이벤트를 씁니다. 감사 기능은 일반적으로 Basic, Standard 및 Premium 서비스 계층에서 제공됩니다.

감사는 규정 준수를 유지 관리하고, 데이터베이스 활동을 이해하고, 비즈니스 문제나 의심스러운 보안 위반을 나타낼 수 있는 불일치 및 이상 활동을 파악하는 데 도움이 될 수 있습니다. 

감사 도구를 사용하면 규정 준수 표준을 보다 쉽게 준수할 수 있지만 규정을 완전히 준수한다고 보장할 수는 없습니다. 표준 규정 준수를 지원하는 Azure 프로그램에 대한 자세한 내용은 <a href="http://azure.microsoft.com/support/trust-center/compliance/" target="_blank">Azure 보안 센터</a>를 참조하세요.

+ [Azure SQL 데이터베이스 감사 기본 사항] 
+ [데이터베이스에 대한 감사 설정]
+ [감사 로그 및 보고서 분석]

## <a id="subheading-1"></a>Azure SQL 데이터베이스 감사 기본 사항

Azure Preview 포털에서 감사를 설정합니다. 이때 Azure SQL 데이터베이스를 Azure 포털에서 만들었는지 또는 Azure Preview 포털에서 만들었는지는 큰 차이가 없습니다. SQL 데이터베이스 감사를 사용하여 다음을 수행할 수 있습니다.

- 선택한 이벤트의 감사 내역을 **유지**합니다. 로깅할 데이터베이스 동작 및 이벤트의 범주를 정의합니다.
- 데이터베이스 활동을 **보고**합니다. 미리 구성된 보고서 및 대시보드를 사용하여 활동 및 이벤트 보고를 빠르게 시작합니다.
- 보고서를 **분석**합니다. 의심스러운 이벤트, 특별한 활동 및 추세를 찾습니다.

다음 활동 및 이벤트를 감사할 수 있습니다.

- **데이터 액세스**
- **스키마 변경(DDL)**
- **데이터 변경(DML)**
- **계정, 역할 및 사용 권한(DCL)**
- **보안 예외**

기록되는 활동 및 이벤트에 대한 자세한 내용 <a href="http://go.microsoft.com/fwlink/?LinkId=506733" target="_blank">감사 로그 형식 참조(doc 파일 다운로드)</a>를 참조하세요. 

감사 로그가 저장될 저장소 계정도 선택합니다.

### 보안 지원 연결 문자열
감사를 설정할 때 Azure는 데이터베이스에 대한 보안 지원 연결 문자열을 제공합니다. 이 연결 문자열을 사용하는 클라이언트 응용 프로그램만 해당 활동 및 이벤트가 로깅되므로 새 문자열 형식을 사용하도록 기존 클라이언트 응용 프로그램을 업데이트해야 합니다.

전형적인 연결 문자열 형식: <*server name*>.database.windows.net

보안 지원 연결 문자열: <*server name*>.database.**secure**.windows.net


## <a id="subheading-2"></a>데이터베이스에 대한 감사 설정

1. https://portal.azure.com에서 <a href="https://portal.azure.com" target="_blank">Azure Preview 포털</a> 을 시작합니다. 또는 https://manage.windowsazure.com/에서 <a href= "https://manage.windowsazure.com/" target="_bank">기존 Azure 포털</a> 을 시작할 수도 있습니다. 아래에서 자세한 내용을 참조하세요.
2. 감사할 데이터베이스의 구성 블레이드로 이동합니다. **작업** 섹션까지 아래로 스크롤한 후 **감사**를 클릭하여 감사를 사용하도록 설정하고 감사 구성 블레이드를 시작합니다.

	![][1]

3. 감사 구성 브레이드에서 로그가 저장될 Azure 저장소 계정을 선택합니다. **팁:** 감사되는 모든 데이터베이스에 대해 동일한 저장소 계정을 사용하여 미리 구성된 보고서 템플릿을 활용합니다.

	![][2]

4. **감사 옵션**에서 **모두**를 클릭하여 모든 이벤트를 로깅하거나 개별 이벤트 유형을 선택합니다.

	![][3]

5. 이러한 설정을 서버의 모든 향후 데이터베이스와 감사를 아직 설정하지 않은 모든 데이터베이스에 적용하려면 **이 설정을 서버 기본값으로 저장**을 선택합니다. 나중에 다음과 동일한 단계를 수행하여 각 데이터베이스에 대한 설정을 재정의할 수 있습니다. 

6. **데이터베이스 연결 문자열 표시**를 클릭하고 응용 프로그램에 대한 해당 보안 지원 연결 문자열을 복사하거나 적어 둡니다. 해당 활동을 감사하려는 클라이언트 응용 프로그램에 대해 이 문자열을 사용합니다.

	![][5]

7. **확인**을 클릭합니다.



## <a id="subheading-3">감사 로그 및 보고서 분석</a>

감사 로그는 설치 중에 선택한 Azure 저장소 계정의 **AuditLogs**라는 단일 Azure 저장 테이블에 집계됩니다. 로그 파일을 보려면 <a href="http://azurestorageexplorer.codeplex.com/" target="_blank">Azure 저장소 탐색기</a>를 참조하세요.

미리 구성된 대시보드 보고서 템플릿은 로그 데이터를 빠르게 분석하도록 도와주는 <a href="http://go.microsoft.com/fwlink/?LinkId=403540" target="_blank">다운로드 가능한 Excel 스프레드시트</a> 로 사용할 수 있습니다. 감사 로그에 템플릿을 사용하려면 Excel 2013 이상과 파워 쿼리가 필요하며 이러한 프로그램은 <a href="http://www.microsoft.com/download/details.aspx?id=39379">여기</a>를 참조하세요. 

템플릿에는 가상의 샘플 데이터가 포함되어 있으며 Azure 저장소 계정에서 직접 감사 로그를 가져오도록 파워 쿼리를 설정할 수 있습니다. 

보고서 템플릿 사용에 대한 자세한 지침은 <a href="http://go.microsoft.com/fwlink/?LinkId=506731">방법(doc 다운로드)</a>을 참조하세요.

![][6]


## <a id="subheading-4"></a>기존 Azure 포털을 사용하여 데이터베이스에 대한 감사 설정

1. https://manage.windowsazure.com/에서 <a href= "https://manage.windowsazure.com/" target="_bank">기존 Azure 포털</a> 을 시작할 수도 있습니다. 
2. 감사할 데이터베이스를 클릭한 후 **감사 및 보안 미리 보기** 탭을 클릭합니다.
3. 감사 섹션에서 "사용"을 클릭합니다.

![][7]

4. 필요한 경우 **이벤트 유형**을 편집합니다.

![][8]

5. **저장소 계정**을 선택합니다.
6. **저장**을 클릭합니다.
7. 연결 문자열에 대해 **보안 연결 문자열 표시**를 클릭합니다.


## <a id="subheading-3">프로덕션에서 사용하기 위한 연습</a>
이 섹션의 설명에서는 위의 화면 캡처를 참조합니다. 여기서는 <a href="https://portal.azure.com" target="_blank">Azure 미리 보기 포털</a> 또는 <a href= "https://manage.windowsazure.com/" target="_bank">기존 Azure 포털</a> 을 사용할 수 있습니다.
 

## <a id="subheading-4"></a>보안 지원 액세스

프로덕션에서는 모든 응용 프로그램 및 도구에서 데이터베이스에 액세스하는 모든 트래픽을 감사해야 합니다. 이렇게 하려면 **보안 지원 액세스**를 *Optional*에서 *Required*로 수정하고 정책을 저장합니다. 여기서 *Required*를 구성한 경우 데이터베이스에 원래 연결 문자열을 통해 액세스할 수는 없으며 보안 지원 연결 문자열을 통해서만 액세스할 수 있습니다.


![][9]


## <a id="subheading-4"></a>저장소 키 다시 생성

프로덕션에서는 저장소 키를 정기적으로 새로 고칩니다. 감사 서비스에서는 사용자의 저장소 계정 키를 저장하지 않습니다. 저장하면 감사 테이블에 대해 SAS(공유 액세스 서명) 키가 생성됩니다(고객만 감사 로그를 읽을 수 있음). 이런 목적으로 키를 새로 고칠 때 정책을 다시 저장해야 합니다. 프로세스는 다음과 같습니다.


1. 감사 구성 블레이드(위에 나온 감사 설정 섹션의 설명 참조)에서**저장소 액세스 키**를 *Primary*에서 *Secondary*로 전환하고 **저장**을 클릭합니다.
![][10]
2. 스토리지 구성 블레이드로 이동하여 *Primary Access Key*를 **다시 생성**합니다.

3. 감사 구성 블레이드로 돌아가서 **저장소 액세스 키**를 *Secondary*에서 *Primary*으로 전환하고 **저장**을 누릅니다.

4. 저장소 UI로 돌아와서 *Secondary Access Key*를 **다시 생성**합니다(다음 키 새로 고침 주기를 위한 준비).
  
## <a id="subheading-4"></a>자동화
Azure SQL 데이터베이스에서 감사를 구성하는 데 사용할 수 있는 여러 가지 PowerShell cmdlet이 있습니다. 감사 cmdlet에 액세스하려면 Azure 리소스 관리자 모드에서 PowerShell을 실행해야 합니다.

> [AZURE.NOTE] AzureResourceManager 모듈은 현재 사전 검토 단계입니다. 이 모듈은 Azure 모듈과 동일한 관리 기능을 제공하지 않을 수도 있습니다.

 [Azure 리소스 관리자](https://msdn.microsoft.com/library/dn654592.aspx) 모드에 액세스하려면 Switch-AzureMode cmdlet(`Switch-AzureMode AzureResourceManager`)을 실행합니다. Azure 리소스 관리자 모드에서 `Get-Command *AzureSql*`을 실행하여 사용 가능한 cmdlet 목록을 확인할 수 있습니다.







<!--Anchors-->
[Azure SQL 데이터베이스 감사 기본 사항]: #subheading-1
[데이터베이스에 대한 감사 설정]: #subheading-2
[감사 로그 및 보고서 분석]: #subheading-3
[기존 Azure 포털을 사용하여 데이터베이스에 대한 감사 설정]: #subheading-4


<!--Image references-->
[1]: ./media/sql-database-auditing-get-started/sql-database-get-started-auditingpreview.png
[2]: ./media/sql-database-auditing-get-started/sql-database-get-started-storageaccount.png
[3]: ./media/sql-database-auditing-get-started/sql-database-auditing-eventtype.png
[5]: ./media/sql-database-auditing-get-started/sql-database-get-started-connectionstring.png
[6]: ./media/sql-database-auditing-get-started/sql-database-auditing-dashboard.png
[7]: ./media/sql-database-auditing-get-started/sql-database-auditing-classic-portal-enable.png
[8]: ./media/sql-database-auditing-get-started/sql-database-auditing-classic-portal-configure.png
[9]: ./media/sql-database-auditing-get-started/sql-database-auditing-security-enabled-access.png
[10]: ./media/sql-database-auditing-get-started/sql-database-auditing-storage-account.png






<!--Link references-->
[다른 azure.microsoft.com 설명서 항목의 링크 1]: ../virtual-machines-windows-tutorial/
[다른 azure.microsoft.com 설명서 항목의 링크 2]: ../web-sites-custom-domain-name/
[다른 azure.microsoft.com 설명서 항목의 링크 3]: ../storage-whatis-account/


<!--HONumber=47-->
