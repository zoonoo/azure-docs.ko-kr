<properties
	pageTitle="Azure 포털로 새 탄력적 풀 만들기 | Microsoft Azure"
	description="여러 데이터베이스 간에 공유되는 관리 및 리소스에 대한 SQL 데이터베이스 구성에 확장성 있는 탄력적 데이터베이스 풀을 추가하는 방법입니다."
	keywords="확장성 있는 데이터베이스, 데이터베이스 구성"
	services="sql-database"
	documentationCenter=""
	authors="ninarn"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="04/28/2016"
	ms.author="ninarn"
	ms.workload="data-management"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="NA"/>


# Azure 포털로 새 탄력적 데이터베이스 풀 만들기

> [AZURE.SELECTOR]
- [Azure 포털](sql-database-elastic-pool-create-portal.md)
- [PowerShell](sql-database-elastic-pool-create-powershell.md)
- [C#](sql-database-elastic-pool-create-csharp.md)

[풀](sql-database-elastic-pool.md)을 만드는 방법은 두 가지입니다.

1. 특히 원하는 풀 설치를 알고 있는 경우 도움말 없이 만듭니다.
2. SQL 데이터베이스 서비스의 권장 사항을 시작합니다. 서비스는 원격 분석을 사용하여 지난 사용량을 검사합니다. 그런 다음 데이터베이스에 대한 비용 효율적인 구성을 권장합니다.

서버에 풀을 여러 개 추가할 수 있지만 다른 서버에 속하는 데이터베이스를 동일한 풀에 추가할 수 없습니다. 풀을 만들려면 V12 서버에서 하나 이상의 데이터베이스가 필요합니다. v12 서버가 없는 경우 [첫 Azure SQL 데이터베이스 만들기](sql-database-get-started.md)를 참조하세요. 하나의 데이터베이스로 그룹을 만들 수 있지만 여러 데이터베이스가 있는 경우 풀이 비용 효율적입니다. [탄력적 데이터베이스 풀의 가격 및 성능 고려 사항](sql-database-elastic-pool-guidance.md)을 참조하세요.

풀은 SQL 데이터베이스 V12 서버에서만 사용할 수 있습니다. V11 서버에 데이터베이스가 있으면 V12 서버에서 [PowerShell 스크립트를 사용하여 풀에 대한 후보로 식별](sql-database-elastic-pool-database-assessment-powershell.md)한 후 [PowerShell을 사용하여 V12로 업그레이드하고 풀을 한 번에 만들 수 있습니다](sql-database-upgrade-server-powershell.md).

## 새 풀 만들기

1. [Azure 포털](http://portal.azure.com/)에서 **SQL Server**를 클릭한 다음 풀에 추가할 데이터베이스를 포함하는 서버를 클릭합니다.
2. **새 풀**을 클릭합니다.

    ![서버에 풀 추가](./media/sql-database-elastic-pool-create-portal/new-pool.png)

    **또는**

    메시지를 클릭하여 기록 데이터베이스 사용량 원격 분석에 따라 권장된 풀을 확인한 다음 계층을 클릭하여 자세한 내용을 보고 풀을 사용자 지정합니다. 권장 사항을 만드는 방법은 이 토픽 뒷부분에서 [풀 권장 사항 이해](#understand-pool-recommendations)를 참조하세요.

    ![권장되는 풀](./media/sql-database-elastic-pool-create-portal/recommended-pool.png)

    **탄력적 데이터베이스 풀** 블레이드가 나타나면 거기서 풀을 설정할 수 있습니다. 이전 단계에서 **새 풀**을 클릭한 경우 포털은 **가격 책정 계층**에서 **표준 풀**, 풀에 대한 고유한 **이름** 및 풀에 대한 기본 구성을 선택합니다. 권장되는 풀을 선택한 경우 권장된 계층 및 풀의 구성은 이미 선택되었지만 변경할 수 있습니다.

    ![탄력적 풀 구성](./media/sql-database-elastic-pool-create-portal/configure-elastic-pool.png)

3. 풀에 대한 가격 책정 계층을 변경하려면 **가격 책정 계층**, 원하는 가격 책정 계층, **선택**을 차례로 클릭합니다.

    > [AZURE.IMPORTANT] 가격 책정 계층을 선택하고 마지막 단계에서 **확인**을 클릭하여 변경 내용을 적용한 후에는 풀의 가격 책정 계층을 변경할 수 없습니다.

4. **풀 구성**을 클릭하여 데이터베이스를 추가하고 풀에 대한 리소스 설정을 선택할 수 있습니다.
5. 데이터베이스를 추가하려면 **데이터베이스 추가**, 추가하려는 데이터베이스, **선택** 단추를 차례로 클릭합니다.

    ![데이터베이스 추가](./media/sql-database-elastic-pool-create-portal/add-databases.png)

    사용하고 있는 데이터베이스에 충분한 기록 사용량 원격 분석이 있는 경우 **예상되는 eDTU 및 GB 사용량** 그래프 및 **실제 eDTU 사용량** 막대형 차트는 구성을 결정할 수 있도록 업데이트됩니다.

    ![동적 권장 사항](./media/sql-database-elastic-pool-create-portal/dynamic-recommendation.png)

6. **풀 구성** 페이지에서 제어를 사용하여 설정을 탐색하고 풀을 구성합니다. 각 서비스 계층에 대한 제한의 자세한 내용은 [탄력적 풀 제한](sql-database-elastic-pool.md#edtu-and-storage-limits-for-elastic-pools-and-elastic-databases)을 참조하고 적정한 풀의 크기에 대한 자세한 지침은 [탄력적 데이터베이스 풀에 대한 가격 책정 및 성능 고려 사항](sql-database-elastic-pool-guidance.md)을 참조하세요.
7. 완료하면 **선택**을 클릭한 다음 **확인**을 클릭하여 풀을 만듭니다.

## 풀 권장 사항 이해

SQL 데이터베이스 서비스는 사용 기록을 평가하고 단일 데이터베이스를 사용하는 경우보다 비용 효율적인 경우 하나 이상의 풀을 권장합니다. 각 권장 사항은 풀에 가장 적합한 서버 데이터베이스의 고유한 하위 집합으로 구성됩니다. 풀 권장 사항은 다음으로 구성됩니다.

- 풀에 대한 가격 책정 계층(Basic, Standard 또는 Premium)
- 적절한 **풀 eDTU**(풀당 최대 eDTU라고도 함)
- 데이터베이스당 **eDTU 최대** 및 **eDTU 최소**
- 풀에 대한 권장 데이터베이스 목록

서비스는 풀을 권장할 때 최근 30일간의 원격 분석을 고려합니다. 데이터베이스가 탄력적 데이터베이스 풀의 후보로 간주되려면 7일 이상 존재해야 합니다. 이미 탄력적 데이터베이스 풀에 있는 데이터베이스는 탄력적 데이터베이스 풀 권장 사항에 대한 후보로 간주되지 않습니다.

서비스는 리소스 요구와 각 서비스 계층에 있는 단일 데이터베이스를 동일한 계층의 풀로 이동하는 경우 비용 효율성을 평가합니다. 예를 들어 서버의 모든 Standard 데이터베이스는 표준 탄력적 풀에 적합한지 평가됩니다. 즉, 서비스는 Standard 데이터베이스를 Premium 풀로 이동하는 경우와 같은 계층 간 권장 사항을 제공하지 않습니다.

## 추가 리소스

- [포털을 사용하여 SQL 데이터베이스 탄력적 풀 관리](sql-database-elastic-pool-manage-portal.md)
- [PowerShell을 통해 SQL 데이터베이스 탄력적 풀 관리](sql-database-elastic-pool-manage-powershell.md)
- [C#을 통해 SQL 데이터베이스 탄력적 풀 관리](sql-database-elastic-pool-manage-csharp.md)
- [Azure SQL 데이터베이스를 사용하여 확장](sql-database-elastic-scale-introduction.md) 

<!---HONumber=AcomDC_0504_2016-->