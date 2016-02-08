<properties
	pageTitle="탄력적 데이터베이스 작업 개요 | Microsoft Azure" 
	description="탄력적 데이터베이스 작업 서비스를 설명합니다." 
	metaKeywords="azure sql database elastic databases" 
	services="sql-database" documentationCenter=""  
	manager="jeffreyg" 
	authors="ddove"/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/04/2015" 
	ms.author="ddove; sidneyh" />

# 탄력적 데이터베이스 작업 개요

**탄력적 데이터베이스 작업** 기능(미리 보기)을 사용하면 Transact-SQL(T-SQL) 스크립트를 안정적으로 실행하거나 다음을 포함한 데이터베이스 그룹에 대해 DACPAC([데이터 계층 응용 프로그램](https://msdn.microsoft.com/library/ee210546.aspx))를 적용할 수 있습니다.

* 데이터베이스의 사용자 정의 컬렉션(아래 설명)
* [탄력적 데이터베이스 풀](sql-database-elastic-pool.md)의 모든 데이터베이스
* 분할된 데이터베이스 집합([탄력적 데이터베이스 클라이언트 라이브러리](sql-database-elastic-database-client-library.md)를 사용하여 생성) 
 
설치 지침을 보려면 [탄력적 데이터베이스 작업 구성 요소 설치](sql-database-elastic-jobs-service-installation.md)로 이동합니다.

**탄력적 데이터베이스 작업**은 현재 **작업**이라는 임시 및 예약된 관리 태스크를 실행할 수 있도록 하는 고객 호스트된 Azure 클라우드 서비스입니다. 작업을 통해 관리 작업을 수행하는 Transact-SQL 스크립트를 실행하여 대규모 그룹의 Azure SQL 데이터베이스를 쉽고 안정적으로 관리할 수 있습니다.

![탄력적 데이터베이스 작업 서비스][1]

## 이점
* 스키마 변경, 자격 증명 관리, 참조 데이터 업데이트, 성능 데이터 수집 또는 테넌트(고객) 원격 분석 수집 등을 쉽게 관리할 수 있습니다.
* 오버헤드 감소: 일반적으로는 Transact-SQL 문을 실행하거나 다른 관리 태스크를 수행하려면 각 데이터베이스에 개별적으로 연결해야 합니다. 작업은 대상 그룹의 각 데이터베이스에 로그인하는 태스크를 처리합니다.
* 계정: 작업은 스크립트를 실행하고 각 데이터베이스에 대한 실행 상태를 기록합니다. 
* 유연성: Azure SQL 데이터베이스의 사용자 지정 그룹 정의
* Azure SQL 데이터베이스 그룹에서 실행할 TRANSACT-SQL 스크립트 정의, 유지 관리 및 보존 
* DACPAC(데이터 계층 응용 프로그램) 배포
* 스크립트를 실행할 때 자동 다시 시도
* 실행 일정 정의
* Azure SQL 데이터베이스의 컬렉션에서 단일 대상 테이블로 데이터 집계

> [AZURE.NOTE] Azure 포털에서는 SQL Azure 탄력적 풀로 제한된 감소된 기능 집합만 사용할 수 있습니다. PowerShell API를 사용하여 현재 기능의 전체 집합에 액세스할 수 있습니다.

## 시나리오

* 새 스키마 배포와 같은 관리 작업 수행
* 일정을 사용하여 평일 근무 외 시간에 업데이트 자동화를 포함하여 모든 데이터베이스에서 공통적인 제품 정보와 같은 참조 데이터 업데이트
* 인덱스를 다시 작성하여 쿼리 성능 향상 사용량이 적은 시간 중과 같이 데이터베이스 컬렉션에 대해 되풀이해서 실행되도록 다시 작성 구성
* 지속적으로 데이터베이스 집합에서 중앙 테이블로 쿼리 결과 수집 지속적으로 성능 쿼리를 실행하고 실행할 추가 작업을 트리거하도록 구성할 수 있습니다.
* 큰 데이터베이스 집합에 대해 더 오래 실행되는 데이터 처리 쿼리(예: 고객 원격 분석 수집) 실행. 추가 분석을 위해 결과가 단일 대상 테이블에 수집됩니다.

## 탄력적 데이터베이스 작업: 종단 간 
1.	**탄력적 데이터베이스 작업** 구성 요소를 설치합니다. 자세한 내용은 [탄력적 데이터베이스 작업 설치](sql-database-elastic-jobs-service-installation.md)를 참조하세요. 설치에 실패하는 경우 [제거 방법](sql-database-elastic-jobs-uninstall.md)을 참조하세요.
2.	PowerShell API를 사용하여 사용자 지정 데이터베이스 컬렉션 만들기, 일정 추가 및/또는 결과 집합 수집 등 더 많은 기능에 액세스할 수 있습니다. **탄력적 데이터베이스 풀**에 대한 실행으로 제한되는 작업의 생성/모니터링 및 간단한 설치에는 포털을 사용합니다. 
3.	작업 실행을 위한 암호화된 자격 증명을 만들고 [그룹의 각 데이터베이스에 사용자(또는 역할)를 추가](sql-database-security.md)합니다.
4.	그룹의 모든 데이터베이스에 대해 실행할 수 있는 idempotent T-SQL 스크립트를 만듭니다. 
5.	Azure 포털을 사용하여 [탄력적 데이터베이스 작업 만들기 및 관리](sql-database-elastic-jobs-create-and-manage.md) 단계에 따라 작업을 만듭니다. 
6.	또는 PowerShell 스크립트를 사용하여 [PowerShell로 SQL 데이터베이스 탄력적 데이터베이스 작업을 만들고 관리(미리 보기)](sql-database-elastic-jobs-powershell.md)합니다.

## idempotent 스크립트의 중요성
스크립트는 [idempotent](https://en.wikipedia.org/wiki/Idempotence)여야 합니다. 간단히 말해 "idempotent"는 스크립트가 성공한 경우를 나타내고 다시 실행하면 동일한 결과가 발생합니다. 일시적인 네트워크 문제로 인해 스크립트가 실패할 수 있습니다. 이 경우 작업에서 중지하기 전에 스크립트 실행을 미리 설정된 횟수 만큼 자동으로 다시 시도합니다. idempotent 스크립트는 성공적으로 두 번 실행된 경우에도 동일한 결과를 포함합니다.

간단한 방법은 만들기 전에 개체의 존재 여부를 테스트하는 것입니다.

	IF NOT EXIST (some_object)
	-- Create the object 
	-- If it exists, drop the object before recreating it.

마찬가지로 스크립트는 논리적으로 테스트하고 발견된 모든 조건을 고려하여 성공적으로 실행할 수 있어야 합니다.

## 오류 및 로그

스크립트를 여러 번 시도했지만 실패하면 작업에서 오류를 기록하고 계속합니다. 작업이 종료된 후(그룹의 모든 데이터베이스에 대해 실행되는 것을 의미) 실패한 시도 목록을 확인할 수 있습니다. 로그는 결함이 있는 스크립트를 디버깅하는 세부 정보를 제공합니다.

## 그룹 유형 및 만들기

두 종류의 그룹이 있습니다.

1. 분할된 데이터베이스 집합
2. 사용자 지정 그룹

분할된 데이터베이스 집합 그룹은 [탄력적 데이터베이스 도구](sql-database-elastic-scale-introduction.md)를 사용하여 만듭니다. 분할된 데이터베이스 집합 그룹을 만들면 그룹에서 데이터베이스가 자동으로 추가 또는 제거됩니다. 예를 들어 새 분할된 데이터베이스가 자동으로 그룹이 됩니다. 조정 없이 그룹에 대해 작업이 실행됩니다.

반면에 사용자 지정 그룹은 엄격하게 정의됩니다. 사용자 지정 그룹에서 데이터베이스를 명시적으로 추가하거나 제거해야 합니다. 그룹에서 데이터베이스가 삭제되면 작업은 데이터베이스에 대해 스크립트 실행을 시도하며 결국에는 오류가 발생합니다. 현재 Azure 포털을 사용하여 만든 그룹은 사용자 지정 그룹입니다.


## 구성 요소 및 가격 
다음 구성 요소는 함께 작동하여 관리 작업의 임시 실행을 지원하는 Azure 클라우드 서비스를 구성합니다. 구성 요소는 구독에 포함되어 설치 도중 자동으로 설치 및 구성됩니다. 서비스는 모두 동일하게 자동으로 생성된 이름을 가지므로 식별할 수 있습니다. 각 이름은 고유하며, 접두사 "edj"에 이어 임의로 생성된 문자 21개로 구성됩니다.

* **Azure 클라우드 서비스**: 탄력적 데이터베이스 작업(Preview)은 고객이 호스트하는 Azure 클라우드 서비스 형태로 제공되어 요청된 작업의 실행을 처리합니다. 포털에서 서비스가 배포되며 Microsoft Azure 구독에 포함되어 호스팅됩니다. 기본적으로 배포되는 서비스는 고가용성을 위한 최소 2개의 작업자 역할로 실행됩니다. 각 작업자 역할(ElasticDatabaseJobWorker)의 기본 크기가 A0 인스턴스에서 실행됩니다. 가격에 대한 내용은 [클라우드 서비스 가격](https://azure.microsoft.com/pricing/details/cloud-services/)을 참조하세요. 
* **Azure SQL 데이터베이스**: 이 서비스는 **제어 데이터베이스**로 알려진 Azure SQL 데이터베이스를 사용하여 모든 작업 메타데이터를 저장합니다. 기본 서비스 계층은 S0입니다. 가격에 대한 내용은 [SQL 데이터베이스 가격](https://azure.microsoft.com/pricing/details/sql-database/)을 참조하세요.
* **Azure 서비스 버스**: Azure 서비스 버스는 Azure 클라우드 서비스 내의 작업 조정에 사용됩니다. [서비스 버스 가격](https://azure.microsoft.com/pricing/details/service-bus/)을 참조하세요.
* **Azure 저장소**: Azure 저장소 계정은 추가로 디버그해야 하는 문제가 있을 경우([Azure 진단](cloud-services-dotnet-diagnostics.md)에서 일반적인 사례) 진단 출력 로그를 저장하는 데 사용됩니다. 가격에 대한 내용은 [Azure 저장소 가격](https://azure.microsoft.com/pricing/details/storage/)을 참조하세요.

## 탄력적 데이터베이스 작업의 작동 방식
1.	Azure SQL 데이터베이스는 모든 메타데이터 및 상태 데이터를 저장하는 제어 데이터베이스로 지정됩니다.
2.	**탄력적 데이터베이스 작업**은 제어 데이터베이스에 액세스하여 실행할 작업을 시작 및 추적합니다.
3.	다음 두 가지 역할이 제어 데이터베이스와 통신합니다. 
	* 컨트롤러: 요청된 작업을 수행할 태스크가 필요한 작업을 확인하고 새 작업 태스크를 만들어 실패한 작업을 다시 시도합니다.
	* 작업 태스크 실행: 작업 태스크를 수행합니다.

### 작업 태스크 유형
작업을 실행하는 여러 유형의 작업 태스크가 있습니다.

* ShardMapRefresh: 분할된 데이터베이스 맵을 쿼리하여 분할된 데이터베이스로 사용되는 모든 데이터베이스를 확인합니다.
* ScriptSplit: 'GO' 문에 걸친 스크립트를 일괄 처리로 분할합니다.
* ExpandJob: 데이터베이스 그룹을 대상으로 하는 작업에서 각 데이터베이스에 대한 자식 작업을 만듭니다.
* ScriptExecution: 정의된 자격 증명을 사용하여 특정 데이터베이스에 대해 스크립트를 실행합니다.
* Dacpac: 특정 자격 증명을 사용하여 특정 데이터베이스에 DACPAC를 적용합니다.

## 종단 간 작업 실행 워크플로
1.	포털이나 PowerShell API를 사용하여 **제어 데이터베이스**에 작업이 삽입됩니다. 작업이 특정 자격 증명을 사용하여 데이터베이스 그룹에 대해 TRANSACT-SQL 스크립트를 실행하도록 요청합니다.
2.	컨트롤러가 새 작업을 식별합니다. 작업 태스크가 생성 및 실행되어 스크립트를 분할하고 그룹의 데이터베이스를 새로 고칩니다. 마지막으로, 새 작업이 생성 및 실행되어 작업을 확장하고 새 자식 작업을 만듭니다. 여기서 각 자식 작업은 그룹의 개별 데이터베이스에 대해 TRANSACT-SQL 스크립트를 실행하도록 지정됩니다.
3.	컨트롤러가 생성된 자식 작업을 식별합니다. 각 작업에서 컨트롤러가 데이터베이스에 대해 스크립트를 실행할 작업 태스크를 만들고 트리거합니다. 
4.	모든 작업 태스크가 완료된 후 컨트롤러가 작업을 완료 상태로 업데이트합니다. 작업 실행 중 언제든지 PowerShell API를 사용하여 작업 실행의 현재 상태를 볼 수 있습니다. PowerShell API에서 반환되는 모든 시간은 UTC로 표시됩니다. 원하는 경우 취소 요청을 시작하여 작업을 중지할 수 있습니다. 

## 다음 단계
[구성 요소를 설치](sql-database-elastic-jobs-service-installation.md)하고 [데이터베이스 그룹의 각 데이터베이스를 만들고 로그인을 추가](sql-database-security.md)합니다. 작업 만들기 및 관리를 더 잘 이해하려면 [탄력적 데이터베이스 작업 만들기 및 관리](sql-database-elastic-jobs-create-and-manage.md)를 참조하세요.

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-overview/elastic-jobs.png
<!--anchors-->

<!---HONumber=AcomDC_0128_2016-->