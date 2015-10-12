<properties
	pageTitle="Azure SQL 데이터베이스의 BACPAC 만들기 및 내보내기"
	description="Azure SQL 데이터베이스의 BACPAC 만들기 및 Azure 저장소로 내보내기"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="09/05/2015"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# SQL 데이터베이스의 BACPAC 만들기 및 내보내기

**단일 데이터베이스**

> [AZURE.SELECTOR]
- [Azure Preview Portal](sql-database-export.md)
- [PowerShell](sql-database-export-powershell.md)

이 문서에서는 [Azure Preview 포털](https://portal.azure.com)을 사용하여 SQL 데이터베이스의 BACPAC을 수동으로 내보내는 방법을 보여 줍니다.

BACPAC은 데이터베이스 스키마 및 데이터를 포함하는 .bacpac 파일입니다. 자세한 내용은 [데이터 계층 응용 프로그램](https://msdn.microsoft.com/library/ee210546.aspx)에서 백업 패키지(.bacpac)를 참조하세요.

> [AZURE.NOTE]Azure SQL 데이터베이스에서는 모든 사용자 데이터베이스의 백업이 자동으로 생성됩니다. 자세한 내용은 [비즈니스 연속성 개요](sql-database-business-continuity.md)를 참조하세요.


작업이 성공적으로 완료되면 다운로드할 수 있는 BACPAC이 Azure 저장소 Blob 컨테이너로 내보내집니다.

이 문서를 완료하려면 다음이 필요합니다.

- Azure 구독. Azure 구독이 필요할 경우 이 페이지 위쪽에서 **무료 평가판**을 클릭하고 되돌아와 이 문서를 완료합니다.
- Azure SQL 데이터베이스. SQL 데이터베이스가 없는 경우 [첫 Azure SQL 데이터베이스 만들기](sql-database-get-started.md) 문서의 단계에 따라 만듭니다.
- 데이터베이스 백업을 저장할 Blob 컨테이너가 있는 [Azure 저장소 계정](storage-create-storage-account.md). 현재 저장소 계정은 클래식 배포 모델을 사용해야 하므로 저장소 계정을 만들 때 **클래식**을 선택합니다. 


## 데이터베이스 내보내기

.bacpac 파일로 내보낼 데이터베이스에 대한 SQL 데이터베이스 블레이드를 엽니다.

1.	[Azure Preview 포털](https://portal.azure.com)로 이동합니다.
2.	**모두 찾아보기**를 클릭합니다.
3.	**SQL 데이터베이스**를 클릭합니다.
2.	BACPAC으로 내보낼 데이터베이스를 클릭합니다.
3.	SQL 데이터베이스 블레이드에서 **내보내기**를 클릭하여 **데이터베이스 내보내기** 블레이드를 엽니다.

    ![내보내기 단추][1]

1.  **저장소**를 클릭하고 저장소 계정 및 BACPAC을 저장할 Blob 컨테이너를 선택합니다.

    ![데이터베이스 내보내기][2]

1.  백업하는 데이터베이스가 포함된 Azure SQL Server의 **서버 관리자 로그인** 및 **암호**를 입력합니다.
1.  **만들기**를 클릭하여 데이터베이스를 내보냅니다.

**만들기**를 클릭하면 데이터베이스 내보내기 요청이 생성되고 서비스에 제출됩니다. 데이터베이스 크기에 따라 내보내기 작업을 완료하는 데 다소 시간이 걸릴 수 있습니다.

## 내보내기 작업의 진행률 모니터링

2.	**모두 찾아보기**를 클릭합니다.
3.	**SQL Server**를 클릭합니다.
2.	방금 내보낸 원본 데이터베이스를 포함하는 서버를 클릭합니다.
3.	SQL Server 블레이드에서 **가져오기/내보내기 기록**을 클릭합니다.

    ![가져오기 내보내기 기록][3] ![가져오기 내보내기 기록][4]

## BACPAC이 저장소 컨테이너에 있는지 확인

2.	**모두 찾아보기**를 클릭합니다.
3.	**저장소 계정(클래식)**을 클릭합니다.
2.	BACPAC을 저장한 저장소 계정을 클릭합니다.
3.	**컨테이너**를 클릭하고 데이터베이스를 내보낸 컨테이너를 선택하여 백업 세부 정보를 확인합니다(여기서 BACPAC을 다운로드 및 저장할 수 있음).

    ![.bacpac 파일 세부 정보][5]


## 다음 단계

- [Azure SQL 데이터베이스 가져오기](sql-database-import.md)



## 추가 리소스

- [비즈니스 연속성 개요](sql-database-business-continuity.md)
- [재해 복구 연습](sql-database-disaster-recovery-drills.md)
- [SQL 데이터베이스 설명서](https://azure.microsoft.com/documentation/services/sql-database/)


<!--Image references-->
[1]: ./media/sql-database-export/export.png
[2]: ./media/sql-database-export/export-blade.png
[3]: ./media/sql-database-export/export-history.png
[4]: ./media/sql-database-export/export-status.png
[5]: ./media/sql-database-export/bacpac-details.png

<!---HONumber=Oct15_HO1-->