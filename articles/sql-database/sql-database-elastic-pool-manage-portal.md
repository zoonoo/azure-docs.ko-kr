<properties
	pageTitle="탄력적 데이터베이스 풀 모니터링, 관리 및 크기 조정"
	description="Azure 포털 및 SQL 데이터베이스의 기본 제공 인텔리전스를 사용하여 성능을 최적화하고 비용을 관리하기 위해 확장성 있는 탄력적 데이터베이스 풀을 관리, 모니터링, 적절히 조정하는 방법에 대해 알아봅니다."
	keywords=""
	services="sql-database"
	documentationCenter=""
	authors="jeffgoll"
	manager="jeffreyg"
	editor="cgronlun"/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="04/01/2016"
	ms.author="jeffreyg"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Azure 포털을 사용하여 탄력적 데이터베이스 풀 모니터링, 관리 및 크기 조정

> [AZURE.SELECTOR]
- [Azure 포털](sql-database-elastic-pool-manage-portal.md)
- [C#](sql-database-elastic-pool-manage-csharp.md)
- [PowerShell](sql-database-elastic-pool-manage-powershell.md)
- [T-SQL](sql-database-elastic-pool-manage-tsql.md)


이 문서는 Azure 포털을 사용하여 풀에서 탄력적 데이터베이스 풀 및 데이터베이스를 모니터링, 관리 및 크기 조정하는 방법을 설명합니다. SQL 데이터베이스에는 기록 사용량 원격 분석을 분석하고 가장 비용 효과적인 경우 데이터베이스에 대한 풀을 권장하는 기본 제공 인텔리전스 기능이 있습니다. 또한 변경 내용에 커밋하고 풀 성능 및 저장소에 영향을 주기 전에 데이터베이스를 추가하고 제거할 수 있습니다.

이 문서의 단계를 진행하려면 데이터베이스 및 풀 필요이 필요합니다. 데이터베이스가 이미 있는 경우 [풀 만들기](sql-database-elastic-pool-create-portal.md)를 참조하고 없는 경우 [SQL 데이터베이스 자습서](sql-database-get-started)를 참조하세요.

**작업하려는 풀을 선택하려면**

- [Azure 포털](https://portal.azure.com)에서 **찾아보기**, **SQL 탄력적 풀** 및 목록에서 작업하려는 풀을 차례로 클릭합니다.

##풀의 리소스 사용률 모니터링
작업할 풀을 선택한 후에 **탄력적 풀 모니터링**에서 차트 및 라이브 타일은 풀에 중요한 사용률 정보를 표시합니다.

![탄력적 풀 모니터링](./media/sql-database-elastic-pool-manage-portal/monitor-elastic-pool.png)

**날짜 범위, 차트 종류(막대형 또는 선형) 또는 표시된 리소스를 변경하려면:**

- **편집**을 클릭하고 원하는 설정을 선택한 다음 **저장**을 클릭하여 차트를 업데이트합니다.

**라이브 타일을 변경하려면:**

- **타일 추가**를 클릭한 다음 왼쪽에 표시되는 타일 갤러리에서 원하는 타일을 선택합니다.

##리소스 풀에 경고 추가
리소스가 설정한 사용률 임계값에 도달하면 사람 또는 URL 끝점에 대한 경고 문자열에 전자 메일을 보내는 리소스에 대한 규칙을 추가할 수 있습니다.

**리소스에 경고 추가:**

1. **리소스 사용률** 차트를 클릭하여 **메트릭** 블레이드를 열고 **경고 추가**를 클릭한 다음 **경고 규칙 추가** 블레이드에 정보를 입력합니다(**리소스**는 자동으로 작업 중인 풀로 설정됨).
2. 사용자 및 받는 사람에게 경고를 식별하는 **이름** 및 **설명**을 입력합니다.
3. 목록에서 경고하려는 **메트릭**을 선택합니다.

    차트는 동적으로 임계값을 선택할 수 있도록 해당 메트릭에 대한 리소스 사용률을 보여줍니다.

4. **조건**(크거나 작거나 등) 및 **임계값**을 선택합니다.
5. **확인**을 클릭합니다.

##풀 및 데이터베이스 eDTU당 eDTU 변경
풀의 리소스 사용률이 표시되면 풀이 다른 eDTU 설정을 필요로 하거나 풀의 개별 데이터베이스가 다른 eDTU 설정에 필요하다는 것을 발견할 수 있습니다. 언제든지 풀의 설치 프로그램을 변경하여 적절한 성능 및 비용을 얻을 수 있습니다. 자세한 내용은 [풀의 가격 및 성능 고려 사항](sql-database-elastic-pool-guidance.md)을 참조하세요.

**풀 eDTU 및 데이터베이스당 eDTU를 변경하려면:**

1. **풀 구성**을 클릭하여 **성능 구성** 블레이드를 엽니다.

    **탄력적 데이터베이스 풀 설정**에서 차트는 풀에 대한 용량의 백분율로 eDTU와 저장소 사용률의 최근 추세를 보여줍니다.

    ![탄력적 풀 리소스 사용률](./media/sql-database-elastic-pool-manage-portal/resize-pool.png)

2. 다른 **풀 eDTU**를 클릭하면 변경하려는 예측된 월별 비용 및 선택한 새 최대 eDTU로 예측된 사용률 값을 표시하는 차트 업데이트를 확인할 수 있습니다.

    ![풀 및 새 월별 비용 업데이트](./media/sql-database-elastic-pool-manage-portal/pool-change-edtu.png)

3. **탄력적 데이터베이스 설정**에서 막대 그래프는 풀에 있는 각 데이터베이스의 eDTU 사용률을 보여줍니다.

4. **eDTU 최대**를 클릭하여 최대값을 설정하고 **eDTU 최소**를 클릭하여 풀에서 데이터베이스에 대한 eDTU의 최소값을 설정합니다.

    ![탄력적 데이터베이스 최소 및 최대 eDTU 업데이트](./media/sql-database-elastic-pool-manage-portal/change-db-edtuminmax.png)

##데이터베이스 추가 및 제거

풀을 만든 후에 풀에 데이터베이스를 추가하거나 제거할 수 있습니다. 동일한 SQL server에서 데이터베이스를 추가할 수 있습니다.

**데이터베이스를 추가하려면:**

1. 풀에 대한 블레이드의 **탄력적 데이터베이스**에서 풀의 데이터베이스 수를 보여주는 링크를 클릭합니다.

    ![데이터베이스 나열](./media/sql-database-elastic-pool-manage-portal/db-listing.png)

2. **탄력적 데이터베이스** 블레이드에서 **데이터베이스 추가**, 추가하려는 데이터베이스, **선택** 단추를 차례로 클릭합니다.

    **탄력적 데이터베이스** 블레이드에서는 **보류 중** 상태와 함께 **평균 DTU** 및 저장소 사용을 **크기(GB)**로 사용하여 방금 추가한 데이터베이스를 나열합니다. 변경 내용을 저장하는 경우 풀 사용률 값은 **새** 값을 표시합니다.

    ![권장되는 풀](./media/sql-database-elastic-pool-manage-portal/add-remove-databases.png)

3. 포털에서 요청이 제출되었음을 알리는 경우 **저장**을 클릭하고 **확인**을 클릭합니다. 작업이 완료되면 풀에 있는 데이터베이스 수는 풀에 대한 블레이드에서 표시됩니다.

**데이터베이스를 제거하려면:**

1. 풀에 대한 블레이드의 **탄력적 데이터베이스**에서 풀의 데이터베이스 수를 보여주는 링크를 클릭합니다.

    ![데이터베이스 나열](./media/sql-database-elastic-pool-manage-portal/db-listing.png)

2. **탄력적 데이터베이스** 블레이드의 풀에 있는 데이터베이스의 목록에서 제거하려는 데이터베이스를 클릭한 다음 **데이터베이스 제거**를 클릭합니다.

    변경 내용을 저장하는 경우 풀 사용률 값은 **새로 만들기** 값을 표시합니다.

3. 포털에서 요청이 제출되었음을 알리는 경우 **저장**을 클릭하고 **확인**을 클릭합니다. 작업이 완료되면 풀에 있는 데이터베이스 수는 풀에 대한 블레이드에서 표시됩니다.

## 풀에 새 데이터베이스 만들기

데이터베이스에 대한 블레이드에서 **데이터베이스 만들기**를 클릭합니다. SQL 데이터베이스는 올바른 서버 및 풀에 대해 구성되어 있으므로 계속 클릭하여 기타 설정을 선택한 다음 **확인**을 클릭하여 풀에서 새 데이터베이스를 만듭니다.

   ![탄력적 데이터베이스 만들기](./media/sql-database-elastic-pool-portal/create-database.png)

##탄력적 작업 만들기 및 관리

탄력적인 작업을 통해 개수에 관계없이 풀에 있는 데이터베이스에 대해 Transact-SQL 스크립트를 실행할 수 있습니다. 작업을 사용하기 전에 탄력적 작업 구성 요소를 설치하고 자격 증명을 제공합니다. 자세한 내용은 [탄력적 데이터베이스 작업 개요](sql-database-elastic-jobs-overview.md)를 참조하세요.

## 추가 리소스

- [탄력적 데이터베이스 참조](sql-database-elastic-pool-reference.md)
- [SQL 데이터베이스 탄력적 풀](sql-database-elastic-pool.md)
- [포털을 사용한 탄력적 데이터베이스 풀 만들기](sql-database-elastic-pool-create-csharp.md)
- [PowerShell을 사용한 탄력적 데이터베이스 풀 만들기](sql-database-elastic-pool-create-powershell.md)
- [C#을 사용한 탄력적 데이터베이스 풀 만들기](sql-database-elastic-pool-create-csharp.md)
- [탄력적 데이터베이스 풀에 대한 가격 및 성능 고려 사항](sql-database-elastic-pool-guidance.md)

<!---HONumber=AcomDC_0406_2016-->