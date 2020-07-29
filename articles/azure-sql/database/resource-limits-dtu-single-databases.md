---
title: DTU 리소스 제한 단일 데이터베이스
description: 이 페이지에서는 Azure SQL Database의 단일 데이터베이스에 대 한 몇 가지 일반적인 DTU 리소스 제한을 설명 합니다.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: references_regions
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/20/2019
ms.openlocfilehash: fa8901f2a219b0693cee17bc4d0ab78da615c415
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87325103"
---
# <a name="resource-limits-for-single-databases-using-the-dtu-purchasing-model---azure-sql-database"></a>DTU 구매 모델을 사용 하 여 단일 데이터베이스에 대 한 리소스 제한-Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

이 문서에서는 DTU 구매 모델을 사용 하 여 Azure SQL Database 단일 데이터베이스에 대 한 자세한 리소스 제한을 제공 합니다.

탄력적 풀에 대 한 DTU 구매 모델 리소스 제한은 [dtu 리소스 제한-탄력적 풀](resource-limits-dtu-elastic-pools.md)을 참조 하세요. VCore 리소스 제한의 경우 [Vcore 리소스 제한-단일 데이터베이스](resource-limits-vcore-single-databases.md) 및 [vcore 리소스 제한-탄력적 풀](resource-limits-vcore-elastic-pools.md)을 참조 하세요. 다양한 구매 모델에 관한 자세한 내용은 [구매 모델 및 서버 계층](purchasing-models.md)을 참조하세요.

## <a name="single-database-storage-sizes-and-compute-sizes"></a>단일 데이터베이스: 스토리지 크기 및 컴퓨팅 크기

아래 표에는 각 서비스 계층과 컴퓨팅 크기에서 단일 데이터베이스에 사용할 수 있는 리소스가 나와 있습니다. [Azure Portal](single-database-manage.md#the-azure-portal), [Transact-SQL](single-database-manage.md#transact-sql-t-sql), [PowerShell](single-database-manage.md#powershell), [Azure CLI](single-database-manage.md#the-azure-cli) 또는 [REST API](single-database-manage.md#rest-api)를 사용하여 단일 데이터베이스에 대한 서비스 계층, 컴퓨팅 크기 및 스토리지 용량을 설정할 수 있습니다.

> [!IMPORTANT]
> 크기 조정 지침 및 고려 사항은 [단일 데이터베이스 크기 조정](single-database-scale.md) 을 참조 하세요.

### <a name="basic-service-tier"></a>기본 서비스 계층

| **컴퓨팅 크기** | **기본** |
| :--- | --: |
| 최대 DTU | 5 |
| 포함된 스토리지(GB) | 2 |
| 최대 저장소 (GB) | 2 |
| 최대 메모리 내 OLTP 스토리지(GB) |N/A |
| 최대 동시 작업자(요청) | 30 |
| 최대 동시 세션 | 300 |
|||

> [!IMPORTANT]
> 기본 서비스 계층은 두 개 미만의 vCore (CPU)를 제공 합니다.  CPU를 많이 사용 하는 워크 로드의 경우에는 S3 이상의 서비스 계층을 사용 하는 것이 좋습니다.
>
>데이터 저장소와 관련 하 여 기본 서비스 계층은 표준 페이지 Blob에 배치 됩니다. 표준 페이지 Blob은 HDD (하드 디스크 드라이브) 기반 저장소 미디어를 사용 하며 성능 변동에 덜 민감한 개발, 테스트 및 자주 액세스 하지 않는 작업에 가장 적합 합니다.
>

### <a name="standard-service-tier"></a>표준 서비스 계층

| **컴퓨팅 크기** | **S0** | **S1** | **S2** | **S3** |
| :--- |---:| ---:|---:|---:|
| 최대 DTU | 10 | 20 | 50 | 100 |
| 포함 된 저장소 (GB) <sup>1</sup> | 250 | 250 | 250 | 250 |
| 최대 저장소 (GB) | 250 | 250 | 250 | 1024 |
| 최대 메모리 내 OLTP 스토리지(GB) | N/A | N/A | N/A | N/A |
| 최대 동시 작업자(요청)| 60 | 90 | 120 | 200 |
| 최대 동시 세션 |600 | 900 | 1200 | 2400 |
||||||

<sup>1</sup> 추가 저장소로 프로 비전 될 때 발생 하는 추가 비용에 대 한 자세한 내용은 [SQL Database 가격 책정 옵션](https://azure.microsoft.com/pricing/details/sql-database/single/) 을 참조 하세요.

> [!IMPORTANT]
> 표준 S0, S1 및 S2 계층은 두 개 미만의 vCore (CPU)를 제공 합니다.  CPU를 많이 사용 하는 워크 로드의 경우에는 S3 이상의 서비스 계층을 사용 하는 것이 좋습니다.
>
>데이터 저장소와 관련 하 여 표준 S0 및 S1 서비스 계층은 표준 페이지 Blob에 배치 됩니다. 표준 페이지 Blob은 HDD (하드 디스크 드라이브) 기반 저장소 미디어를 사용 하며 성능 변동에 덜 민감한 개발, 테스트 및 자주 액세스 하지 않는 작업에 가장 적합 합니다.
>

### <a name="standard-service-tier-continued"></a>표준 서비스 계층(계속)

| **컴퓨팅 크기** | **S4** | **S6** | **S7** | **S9** | **S12** |
| :--- |---:| ---:|---:|---:|---:|
| 최대 DTU | 200 | 400 | 800 | 1600 | 3000 |
| 포함 된 저장소 (GB) <sup>1</sup> | 250 | 250 | 250 | 250 | 250 |
| 최대 저장소 (GB) | 1024 | 1024 | 1024 | 1024 | 1024 |
| 최대 메모리 내 OLTP 스토리지(GB) | N/A | N/A | N/A | N/A |N/A |
| 최대 동시 작업자(요청)| 400 | 800 | 1600 | 3200 |6000 |
| 최대 동시 세션 |4800 | 9600 | 19200 | 30000 |30000 |
|||||||

<sup>1</sup> 추가 저장소로 프로 비전 될 때 발생 하는 추가 비용에 대 한 자세한 내용은 [SQL Database 가격 책정 옵션](https://azure.microsoft.com/pricing/details/sql-database/single/) 을 참조 하세요.

### <a name="premium-service-tier"></a>프리미엄 서비스 계층

| **컴퓨팅 크기** | **P1** | **P2** | **P4** | **P6** | **P11** | **P15** |
| :--- |---:|---:|---:|---:|---:|---:|
| 최대 DTU | 125 | 250 | 500 | 1000 | 1750 | 4000 |
| 포함 된 저장소 (GB) <sup>1</sup> | 500 | 500 | 500 | 500 | 4096 <sup>2</sup> | 4096 <sup>2</sup> |
| 최대 저장소 (GB) | 1024 | 1024 | 1024 | 1024 | 4096 <sup>2</sup> | 4096 <sup>2</sup> |
| 최대 메모리 내 OLTP 스토리지(GB) | 1 | 2 | 4 | 8 | 14 | 32 |
| 최대 동시 작업자(요청)| 200 | 400 | 800 | 1600 | 2800 | 6400 |
| 최대 동시 세션 | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
|||||||

<sup>1</sup> 추가 저장소로 프로 비전 될 때 발생 하는 추가 비용에 대 한 자세한 내용은 [SQL Database 가격 책정 옵션](https://azure.microsoft.com/pricing/details/sql-database/single/) 을 참조 하세요.

<sup>2</sup> 1024,-4096 gb 256의 증분

> [!IMPORTANT]
> 프리미엄 계층에 1TB 이상 저장소는 중국 동부, 중국 북부, 독일 중부, 독일 북동쪽, 미국 서 부, US DoD 지역 및 미국 정부 중부를 제외한 모든 지역에서 사용할 수 있습니다. 이러한 지역에서 프리미엄 계층 스토리지 최대 크기는 1TB로 제한됩니다.  자세한 내용은 [P11-P15 현재 제한 사항](single-database-scale.md#p11-and-p15-constraints-when-max-size-greater-than-1-tb)을 참조하세요.
> [!NOTE]
> `tempdb`제한 사항은 [tempdb 제한](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database?view=sql-server-2017#tempdb-database-in-sql-database)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

- 단일 데이터베이스에 대 한 vCore 리소스 제한은 [vcore 구매 모델을 사용 하 여 단일 데이터베이스에 대 한 리소스 제한](resource-limits-vcore-single-databases.md) 을 참조 하세요.
- 탄력적 풀에 대 한 vCore 리소스 제한은 [vcore 구매 모델을 사용 하 여 탄력적 풀에 대 한 리소스 제한](resource-limits-vcore-elastic-pools.md) 을 참조 하세요.
- 탄력적 풀에 대 한 DTU 리소스 제한의 경우 [dtu 구매 모델을 사용 하 여 탄력적 풀에 대 한 리소스 제한](resource-limits-dtu-elastic-pools.md) 을 참조 하세요.
- Azure SQL Managed Instance 관리 되는 인스턴스의 리소스 제한에 대해서는 [sql Managed Instance 리소스 제한](../managed-instance/resource-limits.md)을 참조 하세요.
- 일반 Azure 제한에 대한 자세한 내용은 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../../azure-resource-manager/management/azure-subscription-service-limits.md)을 참조하세요.
- 논리 SQL server의 리소스 제한에 대 한 자세한 내용은 서버 및 구독 수준 제한에 대 한 자세한 내용은 [논리 sql server의 리소스 제한 개요](resource-limits-logical-server.md) 를 참조 하세요.
