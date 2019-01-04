---
title: Azure SQL Elastic Database 작업 | Microsoft Docs
description: Elastic Database 작업을 사용하여 하나 이상의 Azure SQL 데이터베이스의 집합에서 T-SQL(Transact-SQL) 스크립트 실행
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: overview
author: srinia
ms.author: srinia
ms.reviewer: sstein
manager: craigg
ms.date: 07/26/2018
ms.openlocfilehash: f91632bfe16ea145a087656ffc946e4a76e07466
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/19/2018
ms.locfileid: "53608674"
---
# <a name="manage-groups-of-databases-with-elastic-database-jobs"></a>Elastic Database 작업을 사용하여 데이터베이스 그룹 관리

**Elastic Database 작업**은 스케줄에 따라 또는 요청 시 많은 데이터베이스에서 병렬로 하나 이상의 T-SQL 스크립트를 실행하는 기능을 제공합니다.

**데이터베이스의 모든 조합에 대해 작업 실행**: 특정 데이터베이스를 포함하거나 제외할 유연성이 추가된 하나 이상의 개별 데이터베이스, 서버의 모든 데이터베이스, 탄력적 풀에서 모든 데이터베이스 또는 shardmap입니다. **작업은 여러 서버 및 여러 사람에서 실행될 수 있으며 심지어 다른 구독의 데이터베이스에 대해서도 실행될 수 있습니다.** 서버 및 풀은 런타임 시 동적으로 열거되므로 작업은 실행 시 대상 그룹에 존재하는 모든 데이터베이스에 대해 실행됩니다.

다음 이미지에서는 다른 유형의 대상 그룹에 대해 작업을 실행하는 작업 에이전트를 보여줍니다.

![탄력적 작업 에이전트 개념적 모델](media/elastic-jobs-overview/conceptual-diagram.png)


## <a name="why-use-elastic-jobs"></a>탄력적 잡을 사용하는 이유?

### <a name="manage-many-databases"></a>많은 데이터베이스 관리

- 매주, 몇 시간 후 등에 실행하려면 관리 작업을 예약합니다.
- 스키마 변경, 자격 증명 관리, 성능 데이터 수집 또는 테넌트(고객) 원격 분석 수집을 배포합니다. 참조 데이터(모든 데이터베이스에서 공통적인 정보) 를 업데이트합니다.
- 인덱스를 다시 작성하여 쿼리 성능 향상 사용량이 적은 시간 중과 같이 데이터베이스 컬렉션에 대해 되풀이해서 실행하려면 작업을 구성합니다.
- 지속적으로 데이터베이스 집합에서 중앙 테이블로 쿼리 결과 수집 지속적으로 성능 쿼리를 실행하고 실행할 추가 작업을 트리거하도록 구성할 수 있습니다.

### <a name="collect-data-for-reporting"></a>보고에 대한 데이터 수집

- Azure SQL Database의 컬렉션에서 단일 대상 테이블로 데이터를 집계합니다.
- 큰 데이터베이스 집합에 대해 더 오래 실행되는 데이터 처리 쿼리(예: 고객 원격 분석 수집) 실행. 추가 분석을 위해 결과가 단일 대상 테이블에 수집됩니다.

### <a name="reduce-overhead"></a>오버헤드 줄이기

- 일반적으로는 Transact-SQL 문을 실행하거나 다른 관리 작업을 수행하려면 각 데이터베이스에 개별적으로 연결해야 합니다. 작업은 대상 그룹의 각 데이터베이스에 로그인하는 태스크를 처리합니다. Azure SQL Database 그룹에서 실행할 Transact-SQL 스크립트를 정의, 유지 관리 및 보존합니다.

### <a name="accounting"></a>회계

- 작업은 각 데이터베이스에 대한 실행 상태를 기록합니다. 또한 오류가 발생할 때 자동으로 재시도하도록 합니다.

### <a name="flexibility"></a>유연성

- Azure SQL Database의 사용자 지정 그룹을 정의하고 작업 실행을 위한 일정을 정의합니다.


## <a name="elastic-job-components"></a>탄력적 작업 구성 요소

|구성 요소  | 설명(추가 세부 정보는 표 아래에 있음) |
|---------|---------|
|[**탄력적 작업 에이전트**](#elastic-job-agent) |  작업을 실행하고 관리하기 위해 만든 Azure 리소스입니다.   |
|[**작업 데이터베이스**](#job-database)    |    작업 에이전트가 작업 관련 데이터, 작업 정의 등을 저장하는 데 사용하는 Azure SQL 데이터베이스입니다.      |
|[**대상 그룹**](#target-group)      |  서버, 풀, 데이터베이스 및 분할된 데이터베이스의 집합이 작업을 실행하기 위해 매핑됩니다.       |
|[**작업**](#job)  |  작업은 하나 이상의 [작업 단계](#job-step)로 구성된 작업 단위입니다. 작업 단계는 실행할 T-SQL 스크립트 뿐만 아니라 스크립트를 실행하는 데 필요한 기타 세부 정보를 지정합니다.  |


### <a name="elastic-job-agent"></a>탄력적 작업 에이전트

탄력적 작업 에이전트는 작업을 생성하고 실행하고 관리하기 위한 Azure 리소스입니다. 탄력적 작업 에이전트는 포털에서 만드는 Azure 리소스입니다([PowerShell](elastic-jobs-powershell.md) 및 REST도 지원됩니다). 

**탄력적 작업 에이전트**를 만드는 데는 기존 SQL 데이터베이스가 필요합니다. 에이전트는 [*작업 데이터베이스*](#job-database) 같은 기존 데이터베이스를 구성합니다.

탄력적 작업 에이전트는 무료입니다. 작업 데이터베이스는 모든 SQL 데이터베이스와 동일한 비용이 청구됩니다.

### <a name="job-database"></a>작업 데이터베이스

*작업 데이터베이스*는 작업을 정의하고 작업 실행의 기록 및 상태를 추적하는 데 사용됩니다. *작업 데이터베이스*는 또한 에이전트 메타데이터, 로그, 결과, 작업 정의를 저장하는 데 사용되며, T-SQL을 사용하여 작업을 생성, 실행 및 관리하기 위해 많은 유용한 저장 프로시저 및 기타 데이터베이스 개체도 포함합니다.

현재 미리 보기의 경우 탄력적 작업 에이전트를 만드는 데 기존 Azure SQL 데이터베이스(S0 이상)가 필요합니다.

*작업 데이터베이스*는 문자 그대로 새 것일 필요는 없지만 깨끗하고 비어있고 S0 이상의 서비스 계층이어야 합니다. 권장된 서비스 계층의 *작업 데이터베이스*는 S1 이상이지만 작업 단계 수, 작업 실행 횟수 및 작업 실행 빈도 같은 사용자 작업의 성능 요구에 따라 달라집니다. 예를 들어 S0 데이터베이스는 한 시간에 거의 작업을 실행하지 않는 작업 에이전트에는 충분할 수 있지만 1분마다 작업을 실행하면 성능이 못 미칠 수 있어 서비스 계층이 더 높을 수록 효과는 더 좋을 수 있습니다.


#### <a name="job-database-permissions"></a>작업 데이터베이스 사용 권한

작업 에이전트 생성 동안 스키마, 테이블 및 *jobs_reader*라는 역할이 *작업 데이터베이스*에서 만들어집니다. 역할은 다음과 같은 사용 권한으로 생성되며, 작업 모니터링을 위해 관리자에게 보다 정교한 액세스 제어를 제공하도록 설계되었습니다.


|역할 이름  |'작업' 스키마 사용 권한  |'jobs_internal' 스키마 사용 권한  |
|---------|---------|---------|
|**jobs_reader**     |    SELECT     |    없음     |

> [!IMPORTANT]
> 데이터베이스 관리자로 *작업 데이터베이스*에 대해 액세스 권한을 부여하기 전에 보안 관련 문제를 고려합니다. 작업을 만들거나 편집할 사용 권한이 있는 악의적인 사용자는 해당 악의적인 사용자의 제어 아래 저장된 자격 증명을 사용하는 작업을 만들거나 편집할 수 있으며, 이는 악의적인 사용자가 자격 증명의 암호를 확인하도록 허용할 수 있습니다.



### <a name="target-group"></a>대상 그룹

*대상 그룹*은 작업 단계에서 실행될 데이터베이스의 집합을 정의합니다. 대상 그룹은 다음의 모든 번호와 조합을 포함할 수 있습니다.

- **Azure SQL 서버** - 서버가 지정되면 작업 실행 시 서버에 존재하는 모든 데이터베이스 그룹의 일부가 됩니다. 작업 실행에 앞서 그룹을 열거하고 업데이트할 수 있도록 master 데이터베이스 자격 증명이 제공되어야 합니다.
- **탄력적 풀** - 탄력적 풀을 지정하는 경우 작업 실행 시 탄력적인 풀에 있는 모든 데이터베이스는 그룹의 일부가 됩니다. 서버의 경우 작업 실행에 앞서 그룹을 업데이트할 수 있도록 master 데이터베이스 자격 증명이 제공되어야 합니다.
- **단일 데이터베이스** - 하나 이상의 개별 데이터베이스가 그룹의 일부가 되도록 지정합니다.
- **Shardmap** - Shardmap의 데이터베이스입니다.

> [!TIP]
> 작업 실행 시 *동적 열거형*은 서버 또는 풀을 포함하는 대상 그룹에서 데이터베이스의 집합을 다시 평가합니다. 동적 열거형은 **작업 실행 시 서버나 풀에 존재하는 모든 데이터베이스에서 작업이 실행되는지** 확인합니다. 런타임 시 데이터베이스 목록의 재평가는 풀 또는 서버 멤버 자격이 자주 변경되는 시나리오에 특히 유용합니다.

풀 및 단일 데이터베이스는 그룹에서 제외되거나 포함되는 것으로 지정할 수 있습니다. 이렇게 하면 데이터베이스의 모든 조합을 사용하여 대상 그룹을 만들 수 있습니다. 예를 들어 대상 그룹에 서버를 추가할 수 있지만 탄력적 풀에서 특정 데이터베이스를 제외합니다(또는 전체 풀을 제외).

대상 그룹은 여러 지역 및 여러 구독에서 데이터베이스를 포함할 수 있습니다. 지역 간 실행은 동일한 지역 내 실행보다 더 많은 대기 시간이 필요합니다.

다음 예제에서는 작업 실행 시 여러 대상 그룹 정의를 동적으로 열거하여 작업에서 실행할 데이터베이스를 결정하는 원리를 보여줍니다.

![대상 그룹 예제](media/elastic-jobs-overview/targetgroup-examples1.png)

**예제 1**은 개별 데이터베이스 목록으로 구성되는 대상 그룹을 보여줍니다. 이 대상 그룹을 사용하여 작업 단계가 실행되면 작업 단계의 동작은 각 데이터베이스에서 실행됩니다.<br>
**예제 2**는 Azure SQL Server를 대상으로 포함하는 대상 그룹을 보여줍니다. 이 대상 그룹을 사용하여 작업 단계가 실행되면 현재 서버에 있는 데이터베이스 목록을 확인하기 위해 서버가 동적으로 열거됩니다. 작업 단계의 동작은 각 데이터베이스에서 실행됩니다.<br>
**예제 3**은 *예제 2*와 비슷한 대상 그룹을 보여주지만, 개별 데이터베이스가 제외됩니다. 제외된 데이터베이스에서 작업 단계의 동작이 실행되지 *않습니다*.<br>
**예제 4**는 탄력적 풀을 대상으로 포함하는 대상 그룹을 보여줍니다. *예제 2*와 비슷하게, 풀에 있는 데이터베이스 목록을 확인하기 위해 런타임에 풀이 동적으로 열거됩니다.
<br><br>


![대상 그룹 예제](media/elastic-jobs-overview/targetgroup-examples2.png)

**예제 5** 및 **예제 6**은 포함 및 제외 규칙을 사용하여 Azure SQL Server, 탄력적 풀, 데이터베이스를 결합할 수 있는 고급 시나리오를 보여줍니다.<br>
**예제 7**은 분할된 데이터베이스 맵의 분할된 데이터베이스를 런타임에도 평가할 수 있음을 보여줍니다.

### <a name="job"></a>작업

*작업*은 일정에 따라 또는 일회성 작업으로 실행되는 작업의 단위입니다. 작업은 하나 이상의 *작업 단계*로 구성됩니다.

#### <a name="job-step"></a>작업 단계

각 작업 단계는 실행할 T-SQL 스크립트, T-SQL 스크립트를 실행하는 하나 이상의 대상 그룹 및 작업 에이전트가 대상 데이터베이스에 연결해야 하는 자격 증명을 지정합니다. 각 작업 단계에는 사용자 지정 가능한 시간 제한 및 재시도 정책이 있으며, 출력 매개 변수를 선택적으로 지정할 수 있습니다.

#### <a name="job-output"></a>작업 출력

각 대상 데이터베이스에서 작업 단계의 결과는 자세히 레코드되며, 스크립트 출력은 지정된 테이블로 캡처될 수 있습니다. 작업에서 반환된 모든 데이터를 저장하려면 데이터베이스를 지정할 수 있습니다.

#### <a name="job-history"></a>작업 기록

작업 실행 기록은 *작업 데이터베이스*에 저장됩니다. 시스템 정리 작업은 45일 이상된 실행 기록을 제거합니다. 45일 이하 기록을 제거하려면 *작업 데이터베이스*에서 **sp_purge_history** 저장 프로시저를 호출합니다.

## <a name="workflow-to-create-configure-and-manage-jobs"></a>작업을 만들고 구성하고 관리하는 워크플로

### <a name="create-and-configure-the-agent"></a>에이전트 만들기 및 구성

1. 빈 S0 이상의 SQL 데이터베이스를 만들거나 식별합니다. 탄력적 작업 에이전트를 만드는 동안 *작업 데이터베이스*로 사용됩니다.
2. [포털](https://portal.azure.com/#create/Microsoft.SQLElasticJobAgent)에서 또는 [PowerShell](elastic-jobs-powershell.md#create-the-elastic-job-agent)로 탄력적 작업 에이전트 만듭니다.

   ![탄력적 작업 에이전트 만들기](media/elastic-jobs-overview/create-elastic-job-agent.png)

### <a name="create-run-and-manage-jobs"></a>작업 만들기, 실행 및 관리

1. [PowerShell](elastic-jobs-powershell.md#create-job-credentials-so-that-jobs-can-execute-scripts-on-its-targets) 또는 [T-SQL](elastic-jobs-tsql.md#create-a-credential-for-job-execution)을 사용하여 *작업 데이터베이스*에서 작업 실행에 대한 자격 증명을 만듭니다.
2. [PowerShell](elastic-jobs-powershell.md#define-the-target-databases-you-want-to-run-the-job-against) 또는 [T-SQL](elastic-jobs-tsql.md#create-a-target-group-servers)을 사용하여 대상 그룹(작업을 실행하려는 데이터베이스)을 정의합니다.
3. 작업을 실행할 각 데이터베이스에서 작업 에이전트 자격 증명을 만듭니다[(그룹의 각 데이터베이스에서 사용자(또는 역할)를 추가합니다)](https://docs.microsoft.com/azure/sql-database/sql-database-control-access). 예를 들어 [PowerShell 자습서](elastic-jobs-powershell.md#create-job-credentials-so-that-jobs-can-execute-scripts-on-its-targets)를 참조합니다.
4. [PowerShell](elastic-jobs-powershell.md#create-a-job) 또는 [T-SQL](elastic-jobs-tsql.md#deploy-new-schema-to-many-databases)을 사용하여 작업을 만듭니다.
5. [PowerShell](elastic-jobs-powershell.md#create-a-job-step) 또는 [T-SQL](elastic-jobs-tsql.md#deploy-new-schema-to-many-databases)을 사용하여 작업 단계를 추가합니다.
6. [PowerShell](elastic-jobs-powershell.md#run-the-job) 또는 [T-SQL](elastic-jobs-tsql.md#begin-ad-hoc-execution-of-a-job)을 사용하여 작업을 실행합니다.
7. 포털, [PowerShell](elastic-jobs-powershell.md#monitor-status-of-job-executions) 또는 [T-SQL](elastic-jobs-tsql.md#monitor-job-execution-status)을 사용하여 작업 실행 상태를 모니터링합니다.

   ![포털](media/elastic-jobs-overview/elastic-job-executions-overview.png)

## <a name="credentials-for-running-jobs"></a>실행 중인 작업에 대한 자격 증명

실행 시 대상 그룹에서 지정한 데이터베이스에 연결하려면 작업은 [데이터베이스 범위 자격 증명](/sql/t-sql/statements/create-database-scoped-credential-transact-sql)을 사용합니다. 대상 그룹에 서버 또는 풀이 포함되어 있는 경우 이러한 데이터베이스 범위 자격 증명은 사용 가능한 데이터베이스를 열거하는 master 데이터베이스에 연결하는 데 사용됩니다.

작업을 실행하기 위해 적절한 자격 증명을 설정하면 다소 혼동을 줄 수 있으므로 다음 사항을 염두에 두어야 합니다.

- 데이터베이스 범위 자격 증명은 *작업 데이터베이스*에서 만들어져야 합니다.
- **모든 대상 데이터베이스는 작업이 성공적으로 완료되게 하려면 [충분한 사용 권한](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine)으로 로그인해야 합니다**(아래 다이어그램의 jobuser).
- 자격 증명은 작업 간에 다시 사용할 것으로 예상됩니다. 자격 증명 암호는 암호화되어 작업 개체에 읽기 전용 액세스 권한이 있는 사용자로부터 보호됩니다.

다음 이미지는 적절한 작업 자격 증명을 이해하고 설정하는 데 도움이 되도록 설계되었습니다. **모든 데이터베이스에서 사용자를 만들려면(모든 *대상 사용자 dbs*) 작업이 실행돼야 합니다**.

![탄력적 작업 자격 증명](media/elastic-jobs-overview/job-credentials.png)

## <a name="security-best-practices"></a>보안 모범 사례

탄력적 작업으로 작업하는 것에 대한 몇 가지 모범 사례 고려 사항입니다.

- API 사용을 신뢰할 수 있는 개인으로 제한합니다.
- 자격 증명은 작업 단계를 수행하는 데 필요한 최소한의 권한만 있어야 합니다. 추가 정보는 [SQL Server 권한 부여 및 사용 권한](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/authorization-and-permissions-in-sql-server)을 참조합니다.
- 서버 및/또는 풀 대상 그룹 구성원을 사용할 경우 작업 실행 전에 서버 및/또는 풀의 데이터베이스 목록을 확장하는 데 사용되는 데이터베이스를 보려면/나열하려면 master 데이터베이스에 대한 권한이 있는 별도 자격 증명을 만들 것을 강력히 제안합니다.



## <a name="agent-performance-capacity-and-limitations"></a>에이전트 성능, 용량 및 제한 사항

탄력적 작업은 장기 실행 작업이 완료되기를 기다리는 동안 최소한의 계산 리소스를 사용합니다.

작업(동시 작업자 수)에 대한 원하는 실행 시간 및 데이터베이스의 대상 그룹 크기에 따라 에이전트는 *작업 데이터베이스*의 다른 양의 성능 및 계산을 요구합니다(대상 및 작업 수가 많아질수록 필요한 계산 양도 더 많아집니다).

현재 미리 보기의 동시 작업은 100개로 제한됩니다.

### <a name="prevent-jobs-from-reducing-target-database-performance"></a>작업이 대상 데이터베이스 성능을 감소시키는 것을 방지

SQL 탄력적인 풀에서 데이터베이스에 대해 작업을 실행할 때 리소스에 작업 부하를 주지 않도록 하려면 작업이 동시에 실행될 수 있는 데이터베이스의 수를 제한하도록 작업을 구성할 수 있습니다.

##  <a name="differences-between-elastic-jobs-and-sql-server-agent"></a>탄력적 작업 및 SQL Server 에이전트 간의 차이점

SQL Server 에이전트(사용할 수 있는 온-프레미스 및 SQL Database Managed Instance의 일부로) 및 Azure SQL Database 탄력적 작업 에이전트(SQL Database 및 SQL Data Warehouse에 대해 현재 사용할 수 있는) 간의 몇몇 차이점을 주목할 필요가 있습니다.


|  |탄력적 작업  |SQL Server 에이전트 |
|---------|---------|---------|
|범위     |  작업 에이전트와 동일한 Azure 클라우드에서 많은 수의 Azure SQL 데이터베이스 및/또는 데이터 웨어하우스입니다. 대상은 다른 논리 서버, 구독 및/또는 지역에 있을 수 있습니다. <br><br>대상 그룹은 개별 데이터베이스나 데이터 웨어하우스 또는 서버, 풀 또는 shardmap(작업 런타임 시 동적으로 열거되는)의 모든 데이터베이스로 구성됩니다. | SQL 에이전트와 동일한 SQL Server 인스턴스의 모든 단일 데이터베이스입니다. |
|지원되는 API 및 도구     |  포털, PowerShell, T-SQL, Azure Resource Manager      |   T-SQL 및 SSMS(SQL Server Management Studio)     |





## <a name="best-practices-for-creating-jobs"></a>작업을 만들기 위한 모범 사례

### <a name="idempotent-scripts"></a>Idempotent 스크립트
작업의 T-SQL 스크립트는 [idempotent](https://en.wikipedia.org/wiki/Idempotence)여야 합니다. **Idempotent**는 스크립트가 성공한 경우를 나타내고 다시 실행하면 동일한 결과가 발생합니다. 일시적인 네트워크 문제로 인해 스크립트가 실패할 수 있습니다. 이 경우 작업에서 중지하기 전에 스크립트 실행을 미리 설정된 횟수 만큼 자동으로 다시 시도합니다. idempotent 스크립트는 성공적으로 두 번(이상) 실행된 경우에도 동일한 결과를 포함합니다.

간단한 방법은 만들기 전에 개체의 존재 여부를 테스트하는 것입니다.


```sql
IF NOT EXIST (some_object)
    -- Create the object
    -- If it exists, drop the object before recreating it.
```

마찬가지로 스크립트는 논리적으로 테스트하고 발견된 모든 조건을 고려하여 성공적으로 실행할 수 있어야 합니다.



## <a name="next-steps"></a>다음 단계

- [PowerShell을 사용하여 탄력적 작업 만들기 및 관리](elastic-jobs-powershell.md)
- [T-SQL(Transact-SQL)을 사용하여 탄력적 작업 만들기 및 관리](elastic-jobs-tsql.md)
