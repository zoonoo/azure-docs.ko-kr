<properties
	pageTitle="자습서: Azure Data Factory 파이프라인에서 복사 작업 사용"
	description="이 자습서에서는 Azure Data Factory 파이프라인에 Azure Blob에서 Azure SQL 데이터베이스로 데이터를 복사하는 복사 작업을 사용하는 방법을 보여 줍니다."
	services="data-factory"
	documentationCenter=""
	authors="spelluru"
	manager="jhubbard"
	editor="monicar"/>

<tags
	ms.service="data-factory"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article" 
	ms.date="11/02/2015"
	ms.author="spelluru"/>

# 자습서: Azure Blob에서 Azure SQL로 데이터 복사
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-get-started.md)
- [Using Data Factory Editor](data-factory-get-started-using-editor.md)
- [Using PowerShell](data-factory-monitor-manage-using-powershell.md)
- [Using Visual Studio](data-factory-get-started-using-vs.md)

이 문서의 자습서는 Azure Data Factory 서비스 사용을 빠르게 시작하는 데 도움이 됩니다. 이 자습서에서는 Azure Data Factory를 만들고 Azure Blob 저장소에서 Azure SQL 데이터베이스로 데이터를 복사하는 파이프라인을 데이터 팩터리에 만듭니다.

> [AZURE.NOTE]데이터 팩터리 서비스에 대한 자세한 개요는 [Azure Data Factory 소개][data-factory-introduction] 문서를 참조하세요.

##자습서의 필수 조건
이 자습서를 시작하기 전에 다음이 있어야 합니다.

- **Azure 구독**. 구독이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [무료 평가판][azure-free-trial] 문서를 참조하세요.
- **Azure 저장소 계정**. 이 자습서에서는 Blob 저장소를 **원본** 데이터 저장소로 사용합니다. Azure 저장소 계정이 없는 경우 새로 만드는 단계는 [저장소 계정 만들기][data-factory-create-storage] 문서를 참조하세요.
- **Azure SQL 데이터베이스**. 이 자습서에서는 Azure SQL 데이터베이스를 **대상** 데이터 저장소로 사용합니다. 자습서에서 사용할 수 있는 Azure SQL 데이터베이스가 없는 경우 [Azure SQL 데이터베이스를 만들고 구성하는 방법][data-factory-create-sql-database]을 참조하여 새로 만드세요.
- **SQL Server 2012/2014 또는 Visual Studio 2013**. SQL Server Management Studio 또는 Visual Studio를 사용하여 샘플 데이터베이스를 만들고 데이터베이스에서 결과 데이터를 확인합니다.  

### Azure 저장소 계정의 계정 이름과 계정 키를 수집합니다.
이 자습서를 수행하려면 Azure 저장소 계정의 계정 이름과 계정 키가 필요합니다. 아래 지침에 따라 Azure 저장소 계정의 **계정 이름**과 **계정 키**를 적어둡니다.

1. [Azure 포털][azure-portal]에 로그인합니다.
2. 왼쪽의 **찾아보기** 허브를 클릭하고 **저장소 계정**을 선택합니다.
3. **저장소 계정** 블레이드에서, 이 자습서에서 사용하려는 **Azure 저장소 계정**을 선택합니다.
4. **저장소** 블레이드에서 **키** 타일을 클릭합니다.
5. **키 관리** 블레이드에서 **저장소 계정 이름** 텍스트 상자 옆에 있는 **복사**(이미지) 단추를 클릭하고 텍스트 파일 등에 저장/붙여넣습니다.  
6. 이전 단계를 반복하여 **기본 액세스 키**를 복사하거나 적어둡니다.
7. **X**를 클릭하여 모든 블레이드를 닫습니다.

### Azure SQL 데이터베이스의 서버 이름, 데이터베이스 이름 및 사용자 계정을 수집합니다.
이 자습서를 수행하려면 Azure SQL Server, 데이터베이스 및 사용자의 이름이 필요합니다. 아래 지침에 따라 Azure SQL 데이터베이스의 **서버**, **데이터베이스** 및 **사용자**의 이름을 적어둡니다.

1. **Azure 포털**에서 왼쪽의 **찾아보기**를 클릭하고 **SQL 데이터베이스**를 선택합니다.
2. **SQL 데이터베이스 블레이드**에서, 이 자습서에서 사용하려는 **데이터베이스**를 선택합니다. **데이터베이스 이름**을 적어둡니다.  
3. **SQL 데이터베이스** 블레이드에서 **속성** 타일을 클릭합니다.
4. **서버 이름** 및 **서버 관리자 로그인**의 값을 적어둡니다.
5. **X**를 클릭하여 모든 블레이드를 닫습니다.

### Azure 서비스가 Azure SQL Server에 액세스하도록 허용
데이터 팩터리 서비스가 Azure SQL Server에 액세스할 수 있도록 Azure SQL Server에 대해 **Azure 서비스에 대한 액세스 허용** 설정이 **ON**인지 확인합니다. 이 설정을 확인하고 켜려면 다음을 수행합니다.

1. 왼쪽의 **찾아보기** 허브를 클릭하고 **SQL Server**를 클릭합니다.
2. **서버**를 선택하고 **SQL SERVER** 블레이드에서 **설정**을 클릭합니다.
3. **설정** 블레이드에서 **방화벽**을 클릭합니다.
4. **방화벽 설정** 블레이드에서 **Azure 서비스에 대한 액세스 허용**에 대해 **ON**을 클릭합니다.
5. **X**를 클릭하여 모든 블레이드를 닫습니다.

### 자습서에서 사용할 Azure Blob 저장소 및 Azure SQL 데이터베이스 준비
이제 다음 단계를 수행하여 자습서에서 사용할 Azure Blob 저장소 및 Azure SQL 데이터베이스를 준비합니다.

1. 메모장을 시작하고 다음 텍스트를 붙여 넣은 다음 **emp.txt**로 하드 드라이브의 **C:\\ADFGetStarted** 폴더에 저장합니다.

        John, Doe
		Jane, Doe

2. [Azure 저장소 탐색기](https://azurestorageexplorer.codeplex.com/)와 같은 도구를 사용하여 **adftutorial** 컨테이너를 만들고 **emp.txt** 파일을 이 컨테이너에 업로드합니다.

    ![Azure 저장소 탐색기](./media/data-factory-get-started/getstarted-storage-explorer.png)
3. 다음 SQL 스크립트를 사용하여 **emp** 테이블을 Azure SQL 데이터베이스에 만듭니다.  


        CREATE TABLE dbo.emp
		(
			ID int IDENTITY(1,1) NOT NULL,
			FirstName varchar(50),
			LastName varchar(50),
		)
		GO

		CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);

	**SQL Server 2012/2014가 컴퓨터에 설치된 경우:** [2단계: SQL Server Management Studio를 사용하여 Azure SQL 데이터베이스 관리의 SQL 데이터베이스에 연결][sql-management-studio] 문서의 지침에 따라 Azure SQL Server에 연결하고 SQL 스크립트를 실행합니다. 이 문서에서는 [Azure 포털](http://portal.azure.com)이 아닌 [Azure 클래식 포털](http://manage.windowsazure.com)을 사용하여 Azure SQL Server의 방화벽을 구성합니다.

	**Visual Studio 2013이 컴퓨터에 설치된 경우:** [Azure 포털](http://portal.azure.com)에서 왼쪽의 **찾아보기** 허브를 클릭하고 **SQL Server**를 클릭한 다음 데이터베이스를 선택하고 도구 모음의 **Visual Studio에서 열기** 단추를 클릭하여 Azure SQL Server에 연결한 다음 스크립트를 실행합니다. 클라이언트가 Azure SQL Server에 액세스할 수 없는 경우 컴퓨터(IP 주소)의 액세스를 허용하도록 Azure SQL Server의 방화벽을 구성해야 합니다. Azure SQL Server의 방화벽을 구성하는 단계는 위의 문서를 참조하세요.


다음을 수행합니다.

- 맨 위의 [데이터 팩터리 편집기 사용](data-factory-get-started-using-editor.md) 링크를 클릭하여 Azure 클래식 포털의 일부인 데이터 팩터리 편집기를 통해 자습서를 수행합니다.
- 맨 위의 [PowerShell 사용](data-factory-monitor-manage-using-powershell.md) 링크를 클릭하여 Azure PowerShell을 통해 자습서를 수행합니다.
- Visual Studio 2013을 사용해서 자습서를 수행하려면 맨 위의 [Visual Studio 사용](data-factory-get-started-using-vs.md) 링크를 클릭합니다.
 


<!--Link references-->
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-portal]: https://portal.azure.com/
[sql-management-studio]: http://azure.microsoft.com/documentation/articles/sql-database-manage-azure-ssms/#Step2

[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[data-factory-introduction]: data-factory-introduction.md
[data-factory-create-storage]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/#create-a-storage-account
[data-factory-create-sql-database]: ../sql-database-get-started.md

<!---HONumber=AcomDC_1203_2015-->