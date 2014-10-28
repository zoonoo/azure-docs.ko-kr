<properties title="Get started with SQL database auditing" pageTitle="Get started with SQL database auditing | Azure" description="Get started with SQL database auditing" metaKeywords="" services="sql-database" solutions="data-management" documentationCenter="" authors="jeffreyg" videoId="" scriptId=""  />

<tags ms.service="sql-database" ms.workload="data-management" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jeffreyg"></tags>

# SQL 데이터베이스 감사 시작

Azure SQL 데이터베이스 감사는 데이터베이스 이벤트를 추적하고 Azure 저장소 계정의 감사 로그에 감사된 이벤트를 씁니다. 감사는 Basic, Standard 및 Premium 서비스 계층에서 미리 볼 수 있습니다. 감사를 사용하려면 [미리 보기를 등록][미리 보기를 등록]하세요.

감사는 규정 준수를 유지 관리하고, 데이터베이스 활동을 이해하고, 비즈니스 문제나 의심스러운 보안 위반을 나타낼 수 있는 불일치 및 이상 활동을 파악하는 데 도움이 될 수 있습니다.

감사 도구를 사용하면 규정 준수 표준을 보다 쉽게 준수할 수 있지만 규정을 완전히 준수한다고 보장할 수는 없습니다. 표준 규정 준수를 지원하는 Azure 프로그램에 대한 자세한 내용은 [Azure 보안 센터][Azure 보안 센터]를 참조하세요.

-   [Azure SQL 데이터베이스 감사 기본 사항][Azure SQL 데이터베이스 감사 기본 사항]
-   [데이터베이스에 대한 감사 설정][데이터베이스에 대한 감사 설정]
-   [감사 로그 및 보고서 분석][감사 로그 및 보고서 분석]

## <span id="subheading-1"></span>Azure SQL 데이터베이스 감사 기본 사항</a>

Azure Preview 포털에서 감사를 설정합니다. 이때 Azure SQL 데이터베이스를 Azure 포털에서 만들었는지 또는 Azure Preview 포털에서 만들었는지는 큰 차이가 없습니다. SQL 데이터베이스 감사를 사용하여 다음을 수행할 수 있습니다.

-   선택한 이벤트의 감사 내역을 **유지**합니다. 로깅할 데이터베이스 동작 및 이벤트의 범주를 정의합니다.
-   데이터베이스 활동을 **보고**합니다. 미리 구성된 보고서 및 대시보드를 사용하여 활동 및 이벤트 보고를 빠르게 시작합니다.
-   보고서를 **분석**합니다. 의심스러운 이벤트, 특별한 활동 및 추세를 찾습니다.

다음 활동 및 이벤트를 감사할 수 있습니다.

-   **데이터 액세스**
-   **스키마 변경(DDL)**
-   **데이터 변경(DML)**
-   **계정, 역할 및 사용 권한(DCL)**
-   **보안 예외**

로깅되는 활동 및 이벤트에 대한 자세한 내용은 [감사 로그 형식 참조(doc 파일 다운로드)][감사 로그 형식 참조(doc 파일 다운로드)](영문)를 참조하세요.

감사 로그가 저장될 저장소 계정도 선택합니다.

### 보안 지원 연결 문자열

감사를 설정할 때 Azure는 데이터베이스에 대한 보안 지원 연결 문자열을 제공합니다. 이 연결 문자열을 사용하는 클라이언트 응용 프로그램만 해당 활동 및 이벤트가 로깅되므로 새 문자열 형식을 사용하도록 기존 클라이언트 응용 프로그램을 업데이트해야 합니다.

전형적인 연결 문자열 형식: \<*server name*\>.database.windows.net

보안 지원 연결 문자열: \<*server name*\>.database.**secure**.windows.net

## <span id="subheading-2"></span></a>데이터베이스에 대한 감사 설정

1.  [감사 미리 보기에 등록합니다][미리 보기를 등록].
2.  [새 서비스 계층 미리 보기에 등록][새 서비스 계층 미리 보기에 등록]하고 Basic, Standard 또는 Premium 데이터베이스도 만들어야 합니다.
3.  <https://portal.azure.com>에서 [Azure 미리 보기 포털][Azure 미리 보기 포털]을 시작합니다.
4.  감사할 데이터베이스를 클릭하고 **감사 미리 보기**를 클릭하여 감사 미리 보기를 사용하도록 설정하고 감사 구성 블레이드를 시작합니다.

    ![][]

5.  감사 구성 브레이드에서 로그가 저장될 Azure 저장소 계정을 선택합니다. **팁:** 감사되는 모든 데이터베이스에 대해 동일한 저장소 계정을 사용하여 미리 구성된 보고서 템플릿을 활용합니다.

    ![][1]

6.  **감사 옵션**에서 **모두**를 클릭하여 모든 이벤트를 로깅하거나 개별 이벤트 유형을 선택합니다.

    ![][2]

7.  이러한 설정을 서버의 모든 향후 데이터베이스와 감사를 아직 설정하지 않은 모든 데이터베이스에 적용하려면 **Save this Configuration as Default(이 구성을 기본값으로 저장)**를 선택합니다. 나중에 다음과 동일한 단계를 수행하여 각 데이터베이스에 대한 설정을 재정의할 수 있습니다.

    ![][3]

8.  **데이터베이스 연결 문자열 표시**를 클릭하고 응용 프로그램에 대한 해당 보안 지원 연결 문자열을 복사하거나 적어둡니다. 해당 활동을 감사하려는 클라이언트 응용 프로그램에 대해 이 문자열을 사용합니다.

    ![][4]

9.  **확인**을 클릭합니다.

## <span id="subheading-3"></span>감사 로그 및 보고서 분석</a>

감사 로그는 설치 중에 선택한 Azure 저장소 계정의 **AuditLogs**라는 단일 Azure 저장 테이블에 집계됩니다. [Azure 저장소 탐색기][Azure 저장소 탐색기]와 같은 도구를 사용하여 로그 파일을 볼 수 있습니다.

미리 구성된 대시보드 보고서 템플릿은 로그 데이터를 빠르게 분석하도록 도와주는 [다운로드 가능 Excel 스프레드시트][다운로드 가능 Excel 스프레드시트]로 사용할 수 있습니다. 감사 로그에 템플릿을 사용하려면 Excel 2013 이상과 파워 쿼리가 필요하며 이러한 프로그램은 [여기][여기]에서 다운로드할 수 있습니다.

템플릿에는 가상의 샘플 데이터가 포함되어 있으며 Azure 저장소 계정에서 직접 감사 로그를 가져오도록 파워 쿼리를 설정할 수 있습니다.

보고서 템플릿 사용에 대한 자세한 지침은 [방법(doc 다운로드)][방법(doc 다운로드)](영문)을 참조하세요.

![][5]

<!--Anchors--> 
<!--Image references--> 
<!--Link references-->

  [미리 보기를 등록]: http://go.microsoft.com/fwlink/?LinkId=404163
  [Azure 보안 센터]: http://azure.microsoft.com/ko--kr/support/trust-center/compliance/
  [Azure SQL 데이터베이스 감사 기본 사항]: #subheading-1
  [데이터베이스에 대한 감사 설정]: #subheading-2
  [감사 로그 및 보고서 분석]: #subheading-3
  [감사 로그 형식 참조(doc 파일 다운로드)]: http://go.microsoft.com/fwlink/?LinkId=506733
  [새 서비스 계층 미리 보기에 등록]: https://account.windowsazure.com/PreviewFeatures?fid=premiumdb
  [Azure 미리 보기 포털]: https://portal.azure.com
  []: ./media/sql-database-auditing-get-started/sql-database-get-started-auditingpreview.png
  [1]: ./media/sql-database-auditing-get-started/sql-database-get-started-storageaccount.png
  [2]: ./media/sql-database-auditing-get-started/sql-database-auditing-eventtype.png
  [3]: ./media/sql-database-auditing-get-started/sql-database-get-started-saveconfigasdefault.png
  [4]: ./media/sql-database-auditing-get-started/sql-database-get-started-connectionstring.png
  [Azure 저장소 탐색기]: http://azurestorageexplorer.codeplex.com/
  [다운로드 가능 Excel 스프레드시트]: http://go.microsoft.com/fwlink/?LinkId=403540
  [여기]: http://www.microsoft.com/ko--kr/download/details.aspx?id=39379
  [방법(doc 다운로드)]: http://go.microsoft.com/fwlink/?LinkId=506731
  [5]: ./media/sql-database-auditing-get-started/sql-database-auditing-dashboard.png
