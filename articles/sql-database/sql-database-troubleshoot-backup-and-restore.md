<properties
	pageTitle="Azure SQL 데이터베이스 백업 및 복원 문제"
	description="Azure SQL 데이터베이스에서 백업 및 복제본을 사용하여 오류 및 가동 중단으로부터 클라우드 데이터베이스를 복원하는 방법에 대해 알아보세요."
	services="sql-database"
	documentationCenter=""
	authors="dalechen"
	manager="felixwu"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/11/2016"
	ms.author="daleche"/>

# 이전 시점으로 데이터베이스 복원, 삭제된 데이터베이스 복원 또는 데이터 센터 가동 중단에서 복구

SQL 데이터베이스는 가동 중단 및 사용자 오류로부터 복구할 수 있도록 데이터베이스의 복제본을 유지합니다. 사용 가능한 옵션은 데이터베이스 서비스 계층 및 선택한 옵션에 따라 달라집니다. 자세한 내용 및 디자인 고려 사항은 [비즈니스 연속성 개요](sql-database-business-continuity.md)를 참조하세요.

##이전 시점으로 데이터베이스를 복원하려면
1.	[Azure 포털](https://azure.microsoft.com/)에서 **SQL 데이터베이스**를 클릭합니다.
2.	목록에서 데이터베이스를 선택한 다음 **복원**을 클릭합니다.
3.	새 데이터베이스 이름을 입력하고 복원할 날짜 및 시간을 선택한 후 **만들기**를 클릭합니다.
4.	새 데이터베이스를 참조하도록 필요에 따라 앱을 조정합니다. [사용자 오류에서 데이터베이스 복구](sql-database-user-error-recovery.md)를 참조하세요.

##실수로 삭제된 데이터베이스를 복원하려면
1.	[Azure 포털](https://azure.microsoft.com/)에서 **SQL Server**를 클릭합니다.
2.	목록에서 데이터베이스를 호스팅하는 서버를 선택합니다.
3.	서버 블레이드에서 아래로 스크롤하여 **삭제된 데이터베이스**를 클릭합니다.
4.	복원할 데이터베이스를 선택한 후 **만들기**를 클릭합니다.
5.	새 데이터베이스를 참조하도록 필요에 따라 앱을 조정합니다. [사용자 오류에서 데이터베이스 복구](sql-database-user-error-recovery.md)를 참조하세요.

##지역별 데이터 센터 가동 중단에서 복구하려면
표준 및 프리미엄 데이터베이스가 있고 지리적 복제 보조를 설정한 경우 이러한 보조를 사용하여 복구할 수 있습니다. 이렇게 하면 데이터 손실 가능성을 줄이고 데이터베이스를 복원할 수 있습니다. 자세한 내용은 [중단 상태에서 Azure SQL 데이터베이스 복구](sql-database-disaster-recovery.md)를 참조하세요.

Azure는 또한 다른 지역(지리적 중복 백업)에 있는 모든 데이터베이스의 백업을 제공합니다. 이러한 백업에서 지리적 복원이라는 새 데이터베이스를 만들 수 있지만 이 방법에만 의존하는 경우 데이터 손실이 발생할 수 있습니다.

**지리적 복원을 사용하여 데이터베이스를 복구하려면:**

- [Azure 포털](https://azure.microsoft.com/)에서 **새로 만들기**, **데이터 및 저장소**, **SQL 데이터베이스**를 차례로 클릭한 다음 데이터베이스 원본으로 **백업**을 선택합니다. 자세한 내용은 [중단 상태에서 Azure SQL 데이터베이스 복구](sql-database-disaster-recovery.md)를 참조하세요.

<!---HONumber=AcomDC_0511_2016-->