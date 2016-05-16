<properties
	pageTitle="Azure 포털을 사용하여 탄력적 데이터베이스 풀 모니터링 및 관리 | Microsoft Azure"
	description="Azure 포털 및 SQL 데이터베이스의 기본 제공 인텔리전스를 사용하여 성능을 최적화하고 비용을 관리하기 위해 확장성 있는 탄력적 데이터베이스 풀을 관리, 모니터링, 적절히 조정하는 방법에 대해 알아봅니다."
	keywords=""
	services="sql-database"
	documentationCenter=""
	authors="ninarn"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="05/02/2016"
	ms.author="ninarn"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Azure 포털을 사용하여 탄력적 데이터베이스 풀 모니터링 및 관리

> [AZURE.SELECTOR]
- [Azure 포털](sql-database-elastic-pool-manage-portal.md)
- [PowerShell](sql-database-elastic-pool-manage-powershell.md)
- [C#](sql-database-elastic-pool-manage-csharp.md)
- [T-SQL](sql-database-elastic-pool-manage-tsql.md)


Azure 포털을 사용하여 풀에서 탄력적 데이터베이스 풀 및 데이터베이스를 모니터링, 관리 및 구성할 수 있습니다. SQL 데이터베이스에는 서버에 있는 모든 데이터베이스의 기록 사용량을 풀에 있는지 여부에 관계없이 분석하고 가장 비용 효과적인 경우 데이터베이스에 대한 풀을 권장하는 기본 제공 인텔리전스 기능이 있습니다.

포털을 사용하면 풀 및 데이터베이스 설정을 변경하고 변경 내용을 미리 보며 모든 변경 내용을 동시에 커밋할 수 있습니다. 데이터베이스 추가 및 제거와 같은 변경 내용을 미리 볼 수 있습니다. 성능 및 가격 책정에 대한 잠재적인 효과도 표시됩니다.

이 문서의 단계를 진행하려면 몇 가지 데이터베이스 및 풀이 필요합니다. 기존 데이터베이스가 있으면 [풀 만들기](sql-database-elastic-pool-create-portal.md)를 참조하고 데이터베이스가 없으면 [SQL 데이터베이스 자습서](sql-database-get-started.md)를 참조하세요.

## 작업하려는 풀 선택

1. [Azure 포털](https://portal.azure.com)에서 **찾아보기**를 클릭합니다.
2. **SQL 탄력적 풀**을 클릭합니다.
3. 목록에서 작업하려는 풀을 클릭합니다.

## 탄력적 풀로 데이터베이스 이동

기존 풀에서 데이터베이스를 제거하거나 추가할 수 있습니다. 데이터베이스가 다른 풀에 있을 수 있습니다. 그러나 동일한 논리 서버에 있는 데이터베이스만 추가할 수 있습니다.

1. 풀에 대한 블레이드에서 **탄력적 데이터베이스** 아래 **풀 구성**을 클릭합니다.

    ![풀 구성 클릭][1]

2. **풀 구성** 블레이드에서 **풀에 추가**를 클릭합니다.

	![풀에 추가 클릭](./media/sql-database-elastic-pool-manage-portal/add-to-pool.png)

	
3. **데이터베이스 추가** 블레이드에서 풀에 추가할 데이터베이스를 하나 이상 선택합니다. 그런 다음 **선택**을 클릭합니다.

	![추가할 데이터베이스 선택](./media/sql-database-elastic-pool-manage-portal/add-databases-pool.png)

    이제 **풀 구성** 블레이드에 방금 추가한 데이터베이스가 **보류 중**으로 상태가 설정되어 나열됩니다.

    ![보류 중인 풀 추가](./media/sql-database-elastic-pool-manage-portal/pending-additions.png)

3. "풀 구성 블레이드"에서 **저장**을 클릭합니다.

    ![저장을 클릭합니다.](./media/sql-database-elastic-pool-manage-portal/click-save.png)

## 탄력적 풀 외부로 데이터베이스 이동

1. **풀 구성** 블레이드에서 제거할 데이터베이스를 하나 이상 선택합니다.

    ![데이터베이스 나열](./media/sql-database-elastic-pool-manage-portal/select-pools-removal.png)

2. **풀에서 제거**를 클릭합니다.

    ![데이터베이스 나열](./media/sql-database-elastic-pool-manage-portal/remove-from-pool.png)

	선택한 데이터베이스가 "제거하도록 선택한 데이터베이스"로 UI에 표시됩니다.


## 풀의 리소스 사용률 모니터링


1. 작업하려는 풀을 선택합니다.
2. **탄력적 풀 모니터링**에서 차트 및 라이브 타일은 풀에 중요한 사용률 정보를 표시합니다.

![탄력적 풀 모니터링](./media/sql-database-elastic-pool-manage-portal/monitor-elastic-pool.png)

**차트 및 디스플레이를 변경하려면:**

- **편집**을 클릭합니다.

	![편집 클릭](./media/sql-database-elastic-pool-manage-portal/edit-resource-utlization.png)

- **차트 편집** 블레이드에서 새 시간 범위(지난 시간, 오늘 또는 지난 주)를 선택하거나 **사용자 지정**을 클릭하여 다른 시간 범위를 설정합니다. 차트 종류(가로 막대형 또는 꺾은선형)를 선택하고 모니터링할 리소스를 선택합니다.

	![편집 클릭](./media/sql-database-elastic-pool-manage-portal/edit-chart.png)

- 그런 후 **OK**를 클릭합니다.


## 리소스 풀에 경고 추가

리소스가 설정한 사용률 임계값에 도달하면 사람 또는 URL 끝점에 대한 경고 문자열에 전자 메일을 보내는 리소스에 대한 규칙을 추가할 수 있습니다.

**리소스에 경고 추가:**

1. **리소스 사용률** 차트를 클릭하여 **메트릭** 블레이드를 열고 **경고 추가**를 클릭한 다음 **경고 규칙 추가** 블레이드에 정보를 입력합니다(**리소스**는 자동으로 작업 중인 풀로 설정됨).
2. 사용자 및 받는 사람에 대한 경고를 식별하는 **이름** 및 **설명**을 입력합니다.
3. 목록에서 경고하려는 **메트릭**을 선택합니다.

    차트는 동적으로 임계값을 선택할 수 있도록 해당 메트릭에 대한 리소스 사용률을 보여줍니다.

4. **조건**(크거나 작거나 등) 및 **임계값**을 선택합니다.
5. **확인**을 클릭합니다.

## 풀의 성능 설정 변경

풀의 리소스 사용률을 모니터링하면서 풀에 더 많은 eDTU가 필요함을 알 수 있습니다. 또는 풀에 있는 개별 데이터베이스에 다른 eDTU 설정이 필요합니다. 언제든지 풀의 설치 프로그램을 변경하여 적절한 성능 및 비용을 얻을 수 있습니다. 자세한 내용은 [탄력적 데이터베이스 풀을 사용해야 하는 경우](sql-database-elastic-pool-guidance.md)를 참조하세요.

**풀당 eDTU 및 데이터베이스당 eDTU를 변경하려면:**

1. **풀 구성** 블레이드를 엽니다.

    **탄력적 데이터베이스 풀 설정** 아래에서 슬라이더를 사용하여 풀 설정을 변경합니다.

    ![탄력적 풀 리소스 사용률](./media/sql-database-elastic-pool-manage-portal/resize-pool.png)

2. 설정이 변경되면 디스플레이에 변경의 예상된 월별 비용이 표시됩니다.

    ![풀 및 새 월별 비용 업데이트](./media/sql-database-elastic-pool-manage-portal/pool-change-edtu.png)

## 데이터베이스 작업 미리 보기

**풀 구성** 블레이드에서 작업을 커밋하기 전에 데이터베이스의 추가 및 제거를 미리 볼 수 있습니다.

![데이터베이스 추가 및 제거 미리 보기](./media/sql-database-elastic-pool-manage-portal/pools-tab.png).


## 탄력적 작업 만들기 및 관리

탄력적인 작업을 통해 개수에 관계없이 풀에 있는 데이터베이스에 대해 Transact-SQL 스크립트를 실행할 수 있습니다. 작업을 사용하기 전에 탄력적 작업 구성 요소를 설치하고 자격 증명을 제공합니다. 자세한 내용은 [탄력적 데이터베이스 작업 개요](sql-database-elastic-jobs-overview.md)를 참조하세요.

[Azure SQL 데이터베이스를 사용하여 규모 확장](sql-database-elastic-scale-introduction.md) 참조: 탄력적 데이터베이스 도구를 사용하여 규모를 확장하거나 데이터를 이동하거나 쿼리 또는 트랜잭션을 만듭니다.


## 추가 리소스

- [SQL 데이터베이스 탄력적 풀](sql-database-elastic-pool.md)
- [포털을 사용한 탄력적 데이터베이스 풀 만들기](sql-database-elastic-pool-create-csharp.md)
- [PowerShell을 사용한 탄력적 데이터베이스 풀 만들기](sql-database-elastic-pool-create-powershell.md)
- [C#을 사용한 탄력적 데이터베이스 풀 만들기](sql-database-elastic-pool-create-csharp.md)
- [탄력적 데이터베이스 풀에 대한 가격 및 성능 고려 사항](sql-database-elastic-pool-guidance.md)


<!--Image references-->
[1]: ./media/sql-database-elastic-pool-manage-portal/configure-pool.png

<!---HONumber=AcomDC_0504_2016-->