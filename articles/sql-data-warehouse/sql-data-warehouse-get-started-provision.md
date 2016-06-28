<properties
   pageTitle="Azure 포털에서 SQL 데이터 웨어하우스 만들기 | Microsoft Azure"
   description="Azure 포털에서 SQL 데이터 웨어하우스를 만드는 방법을 알아봅니다."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="jhubbard"
   editor=""
   tags="azure-sql-data-warehouse"/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/20/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# Azure SQL 데이터 웨어하우스 만들기

> [AZURE.SELECTOR]
- [Azure 포털](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

이 자습서에서는 Azure 포털을 사용하여 AdventureWorksDW 샘플 데이터베이스를 포함하는 SQL 데이터 웨어하우스를 만듭니다.


[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]


1. [Azure 포털](https://portal.azure.com)에 로그인합니다.

2. **+ 새로 만들기** > **데이터 + 저장소** > **SQL 데이터 웨어하우스**를 클릭합니다.

    ![생성](./media/sql-data-warehouse-get-started-provision/create-sample.gif)

3. **SQL 데이터 웨어하우스** 블레이드에, 필요한 정보를 입력한 다음, ‘만들기’를 눌러서 만듭니다.

    ![데이터베이스 만들기](./media/sql-data-warehouse-get-started-provision/create-database.png)

	- **서버**: 먼저 서버를 선택하는 것이 좋습니다. 기존 서버를 선택하거나 [새로 만들 수](./sql-data-warehouse-get-started-new-server.md) 있습니다. 

	- **데이터베이스 이름**: SQL 데이터 웨어하우스를 참조하는 데 사용할 이름입니다. 서버에 대해 고유해야 합니다.
	
    - **성능**: 400 DWU로 시작하는 것이 좋습니다. 슬라이더를 왼쪽이나 오른쪽으로 이동하여 데이터 웨어하우스의 성능을 조정하거나, 만든 후에 확장 또는 축소할 수 있습니다. DWU에 대한 자세한 내용은 설명서의 [확장](./sql-data-warehouse-manage-compute-overview.md) 또는 [가격 페이지](https://azure.microsoft.com/ko-KR/pricing/details/sql-data-warehouse/)를 참조하세요.

    - **구독**: SQL 데이터 웨어하우스가 청구될 구독을 선택합니다.

    - **리소스 그룹**: 리소스 그룹은 컨테이너로, Azure 리소스의 컬렉션을 관리할 수 있도록 디자인되었습니다. [리소스 그룹](../azure-portal/resource-group-portal.md)에 대해 알아봅니다.

    - **원본 선택**: **원본 선택** > **샘플**을 클릭합니다. 현재는 한 개의 예제 데이터베이스만 사용 가능하므로 샘플을 선택하면 Azure에서 **샘플 선택** 옵션이 AdventureWorksDW로 자동으로 채워집니다.

4. **만들기**를 클릭하여 SQL 데이터 웨어하우스를 만듭니다.

5. 몇 분 정도 지나면 SQL 데이터 웨어하우스가 준비됩니다. 완료하면 [Azure 포털](https://portal.azure.com)로 돌아가야 합니다. 생성 시 사용한 리소스 그룹 또는 SQL 데이터베이스 아래 나열된, 대시보드에서 SQL 데이터 웨어하우스를 찾을 수 있습니다.

    ![포털 보기](./media/sql-data-warehouse-get-started-provision/database-portal-view.png)

[AZURE.INCLUDE [SQL 데이터베이스 서버 만들기](../../includes/sql-database-create-new-server-firewall-portal.md)]

## 다음 단계

SQL 데이터 웨어하우스를 만들었으니, [연결](./sql-data-warehouse-connect-overview.md)하여 쿼리할 준비가 되었습니다.

SQL 데이터 웨어하우스로 데이터를 로드하는 경우 [로드 개요](./sql-data-warehouse-overview-load.md)를 참조하세요.

기존 데이터베이스를 SQL 데이터 웨어하우스에 마이그레이션하려는 경우 [마이그레이션 개요](./sql-data-warehouse-overview-migrate.md)를 참조하거나 [마이그레이션 유틸리티](./sql-data-warehouse-migrate-migration-utility.md)를 사용하세요.

<!---HONumber=AcomDC_0622_2016-->