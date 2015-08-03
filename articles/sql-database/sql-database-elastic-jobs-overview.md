<properties 
	pageTitle="탄력적 데이터베이스 작업 개요" 
	description="탄력적 데이터베이스 작업 서비스를 설명합니다." 
	services="sql-database" documentationCenter=""  
	manager="jeffreyg" 
	authors="sidneyh"/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/25/2015" 
	ms.author="sidneyh" />

# 탄력적 데이터베이스 작업 개요

**탄력적 데이터베이스 작업**(Preview)을 통해 [탄력적 데이터베이스 풀(Preview)](sql-database-elastic-pool.md)에 있는 모든 데이터베이스에 대해 T-SQL 스크립트(작업)을 실행할 수 있습니다. 예를 들어, 모든 데이터베이스의 스키마에 새 테이블이 포함되도록 간편하게 업데이트할 수 있습니다. 일반적으로는 T-SQL 문을 실행하거나 다른 관리 작업을 수행하려면 각 데이터베이스에 개별적으로 연결해야 합니다. **탄력적 데이터베이스 작업**은 로그인 작업을 처리하고 사용자 대신 스크립트를 안정적으로 실행하는 동시에, 각 데이터베이스의 실행 상태를 기록합니다. 미리 보기 설치에 대한 지침을 보려면 [탄력적 데이터베이스 작업 구성 요소 설치](sql-database-elastic-jobs-service-installation.md)로 이동합니다.

![탄력적 데이터베이스 작업 서비스][1]

## 이점

* 탄력적 데이터베이스 풀 전반에서 실행될 T-SQL 스크립트의 정의, 유지 관리, 보존
* 자동 재시도를 포함하여 안정적으로 규모별 T-SQL 스크립트 실행
* 작업 실행 상태 추적

## 시나리오

* 새 스키마 배포와 같은 관리 작업 수행
* 예를 들어 모든 데이터베이스에서 공통적인 제품 정보와 같은 참조 데이터 업데이트
* 쿼리 성능 향상을 위해 인덱스 다시 작성

## 작업 실행 방식

1.	탄력적 데이터베이스 작업에 사용할 서비스를 설치합니다. [탄력적 데이터베이스 작업 설치](sql-database-elastic-jobs-service-installation.md)를 참조하세요. 설치에 실패하는 경우 [제거 방법](sql-database-elastic-jobs-uninstall.md)을 참조하세요.
2.	[각 데이터베이스에 사용자 추가](sql-database-elastic-jobs-add-logins-to-dbs.md)를 통해 탄력적 데이터베이스를 작업 실행에 적합하도록 구성합니다.
3.	풀 안의 모든 데이터베이스에 대해 실행 할 수 있는 itempotent T-SQL 스크립트를 만듭니다.
4.	스크립트를 실행 하려면 [탄력적 데이터베이스 작업 만들기 및 관리](sql-database-elastic-jobs-create-and-manage.md) 단계를 수행합니다. 

## 구성 요소 및 가격 

다음 구성 요소는 함께 작동하여 관리 작업의 임시 실행을 지원하는 Azure 클라우드 서비스를 구성합니다. 구성 요소는 구독에 포함되어 설치 도중 자동으로 설치 및 구성됩니다. 서비스는 모두 동일하게 자동으로 생성된 이름을 가지므로 식별할 수 있습니다. 각 이름은 고유하며, 접두사 "edj"에 이어 임의로 생성된 문자 21개로 구성됩니다.

* **Azure 클라우드 서비스**: 탄력적 데이터베이스 작업(Preview)은 고객이 호스트하는 Azure 클라우드 서비스 형태로 제공되어 요청된 작업의 실행을 처리합니다. 포털에서 서비스가 배포되며 Microsoft Azure 구독에 포함되어 호스팅됩니다. 기본적으로 배포되는 서비스는 고가용성을 위한 최소 2개의 작업자 역할로 실행됩니다. 각 작업자 역할(ElasticDatabaseJobWorker)의 기본 크기가 A0 인스턴스에서 실행됩니다. 가격에 대한 내용은 [클라우드 서비스 가격](http://azure.microsoft.com/pricing/details/cloud-services/)을 참조하세요. 
* **Azure SQL 데이터베이스**: 이 서비스는 **제어 데이터베이스**로 알려진 Azure SQL 데이터베이스를 사용하여 모든 작업 메타데이터를 저장합니다. 기본 서비스 계층은 S0입니다. 가격에 대한 내용은 [SQL 데이터베이스 가격](http://azure.microsoft.com/pricing/details/sql-database/)을 참조하세요.
* **Azure 서비스 버스**: Azure 서비스 버스는 Azure 클라우드 서비스 내의 작업 조정에 사용됩니다. [서비스 버스 가격](http://azure.microsoft.com/pricing/details/service-bus/)을 참조하세요.
* **Azure 저장소**: Azure 저장소 계정은 추가로 디버그해야 하는 문제가 있을 경우([Azure 진단](../cloud-services-dotnet-diagnostics.md)에서 일반적인 사례) 진단 출력 로그를 저장하는 데 사용됩니다. 가격에 대한 내용은 [Azure 저장소 가격](http://azure.microsoft.com/pricing/details/storage/)을 참조하세요.

## 다음 단계
[구성 요소를 설치하고](sql-database-elastic-jobs-service-installation.md), [풀에 각 데이터베이스를 만들고 로그인을 추가합니다](sql-database-elastic-jobs-add-logins-to-dbs.md). 작업 만들기 및 관리를 더 잘 이해하려면 [탄력적 데이터베이스 작업 만들기 및 관리](sql-database-elastic-jobs-create-and-manage.md)를 참조하세요.

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-overview/elastic-jobs.png
<!--anchors-->

<!---HONumber=July15_HO4-->