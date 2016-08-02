<properties
	pageTitle="Azure 포털을 사용하여 Azure SQL 데이터베이스를 BACPAC 파일에 보관"
	description="Azure 포털을 사용하여 Azure SQL 데이터베이스를 BACPAC 파일에 보관"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="07/19/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Azure 포털을 사용하여 Azure SQL 데이터베이스를 BACPAC 파일에 보관

> [AZURE.SELECTOR]
- [Azure 포털](sql-database-export.md)
- [PowerShell](sql-database-export-powershell.md)

이 문서에서는 [Azure 포털](https://portal.azure.com)을 사용하여 Azure Blob 저장소에 저장된 BACPAC 파일로 Azure SQL 데이터베이스를 보관하기 위한 지침을 제공합니다.

Azure SQL 데이터베이스의 보관 파일을 만들어야 하는 경우 데이터베이스 스키마 및 데이터를 BACPAC 파일로 내보낼 수 있습니다. BACPAC 파일은 BACPAC의 확장명을 가진 단순한 ZIP 파일입니다. BACPAC 파일은 나중에 Azure Blob 저장소 또는 온-프레미스 저장소의 로컬 저장소에 저장할 수 있으며 나중에 Azure SQL 데이터베이스 또는 SQL Server 온-프레미스 설치로 다시 가져올 수 있습니다.

***고려 사항***

- 보관 파일이 트랜잭션 일치하도록 내보내기 중에나 Azure SQL 데이터베이스의 [트랜잭션 일치 복사본](sql-database-copy.md)에서 내보내기 중에는 쓰기 활동이 발생하지 않도록 해야 합니다.
- Azure Blob 저장소에 보관된 BACPAC 파일의 최대 크기는 200GB입니다. 이보다 큰 BACPAC 파일을 로컬 저장소에 보관하려면 [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) 명령 프롬프트 유틸리티를 사용합니다. 이 유틸리티는 Visual Studio 및 SQL Server에 기본적으로 제공됩니다. 최신 버전의 SQL Server Data Tools를 [다운로드](https://msdn.microsoft.com/library/mt204009.aspx)하여 이 유틸리티를 가져올 수도 있습니다.
- BACPAC 파일을 사용하여 Azure 프리미엄 저장소에 보관하는 것은 지원되지 않습니다.
- 내보내기 작업 20시간 초과되면 취소될 수 있습니다. 내보내는 중에 성능을 향상시키기 위해 다음을 수행할 수 있습니다.
 - 서비스 수준 임시로 향상
 - 내보내기 중에 모든 읽기 및 쓰기 작업 중단
 - 모든 대형 테이블에 클러스터형 인덱스 사용 클러스터형 인덱스가 없는 경우 6~12시간 이상 소요되면 내보내기에 실패할 수 있습니다. 전체 테이블 내보내기를 시도하려면 내보내기 서비스에서 테이블 스캔을 완료해야 하기 때문입니다.

> [AZURE.NOTE] BACPAC는 백업에 사용되는 목적이 아니며 작업을 복원합니다. Azure SQL 데이터베이스에서는 모든 사용자 데이터베이스의 백업이 자동으로 생성됩니다. 자세한 내용은 [비즈니스 연속성 개요](sql-database-business-continuity.md)를 참조하세요.

이 문서를 완료하려면 다음이 필요합니다.

- Azure 구독.
- Azure SQL 데이터베이스.
- 표준 저장소에 BACPAC를 저장할 Blob 컨테이너가 있는 [Azure 표준 저장소 계정](../storage/storage-create-storage-account.md).

## 데이터베이스 내보내기

내보낼 데이터베이스에 대한 SQL 데이터베이스 블레이드를 엽니다.

> [AZURE.IMPORTANT] BACPAC 파일의 트랜잭션 일관성을 위해 먼저 [데이터베이스의 사본을 만든 다음](sql-database-copy.md) 데이터베이스 사본을 내보냅니다.

1.	[Azure 포털](https://portal.azure.com)로 이동합니다.
2.	**SQL 데이터베이스**를 클릭합니다.
3.	보관할 데이터베이스를 클릭합니다.
4.	SQL 데이터베이스 블레이드에서 **내보내기**를 클릭하여 **데이터베이스 내보내기** 블레이드를 엽니다.

    ![내보내기 단추][1]

5.  **저장소**를 클릭하고 저장소 계정 및 BACPAC을 저장할 Blob 컨테이너를 선택합니다.

    ![데이터베이스 내보내기][2]

6. 인증 유형을 선택합니다.
7.  내보내려는 데이터베이스가 포함된 Azure SQL Server의 적절한 인증 자격 증명을 입력합니다.
8.  **확인**을 클릭하여 데이터베이스를 보관합니다. **확인**을 클릭하면 데이터베이스 내보내기 요청이 생성되고 서비스에 제출됩니다. 내보내기에 걸리는 시간은 데이터베이스의 크기와 복잡성 및 서비스 수준에 따라 달라집니다. 알림이 제공됩니다.

    ![내보내기 알림][3]

## 내보내기 작업의 진행률 모니터링

1.	**SQL Server**를 클릭합니다.
2.	방금 보관한 원본 데이터베이스를 포함하는 서버를 클릭합니다.
3.  작업까지 아래로 스크롤합니다.
4.	SQL Server 블레이드에서 **가져오기/내보내기 기록**을 클릭합니다.

    ![가져오기 내보내기 기록][4]

## BACPAC이 저장소 컨테이너에 있는지 확인

1.	**저장소 계정**을 클릭합니다.
2.	BACPAC 보관 파일을 저장한 저장소 계정을 클릭합니다.
3.	**컨테이너**를 클릭하고 데이터베이스를 내보낸 컨테이너를 선택하여 세부 정보를 확인합니다(여기서 BACPAC을 다운로드 및 저장할 수 있음).

    ![.bacpac 파일 세부 정보][5]

## 다음 단계

- [Azure SQL 데이터베이스로 BACPCAC 가져오기](sql-database-import.md)
- [SQL Server 데이터베이스에 BACPCAC 가져오기](https://msdn.microsoft.com/library/hh710052.aspx)

## 추가 리소스

- [비즈니스 연속성 개요](sql-database-business-continuity.md)
- [재해 복구 연습](sql-database-disaster-recovery-drills.md)
- [SQL 데이터베이스 설명서](https://azure.microsoft.com/documentation/services/sql-database/)


<!--Image references-->
[1]: ./media/sql-database-export/export.png
[2]: ./media/sql-database-export/export-blade.png
[3]: ./media/sql-database-export/export-notification.png
[4]: ./media/sql-database-export/export-history.png
[5]: ./media/sql-database-export/bacpac-archive.png

<!---HONumber=AcomDC_0720_2016-->