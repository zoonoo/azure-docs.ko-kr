---
title: Azure SQL Database DTU 리소스는 단일 데이터베이스를 제한 합니다. | Microsoft Docs
description: 이 페이지에서는 Azure SQL Database의 단일 데이터베이스에 대 한 몇 가지 일반적인 DTU 리소스 제한을 설명 합니다.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/20/2019
ms.openlocfilehash: 37cda6dec3f98a195e704808244656f3182b4c2a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73520983"
---
# <a name="resource-limits-for-single-databases-using-the-dtu-purchasing-model"></a>DTU 구매 모델을 사용 하 여 단일 데이터베이스에 대 한 리소스 제한

이 문서에서는 DTU 구매 모델을 사용 하 여 Azure SQL Database 단일 데이터베이스에 대 한 자세한 리소스 제한을 제공 합니다.

탄력적 풀에 대 한 DTU 구매 모델 리소스 제한은 [dtu 리소스 제한-탄력적 풀](sql-database-dtu-resource-limits-elastic-pools.md)을 참조 하세요. VCore 리소스 제한의 경우 [Vcore 리소스 제한-단일 데이터베이스](sql-database-vcore-resource-limits-single-databases.md) 및 [vcore 리소스 제한-탄력적 풀](sql-database-vcore-resource-limits-elastic-pools.md)을 참조 하세요. 다양한 구매 모델에 관한 자세한 내용은 [구매 모델 및 서버 계층](sql-database-purchase-models.md)을 참조하세요.

## <a name="single-database-storage-sizes-and-compute-sizes"></a>단일 데이터베이스: 스토리지 크기 및 컴퓨팅 크기

아래 표에는 각 서비스 계층과 컴퓨팅 크기에서 단일 데이터베이스에 사용할 수 있는 리소스가 나와 있습니다. [Azure Portal](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server), [Transact-SQL](sql-database-single-databases-manage.md#transact-sql-manage-sql-database-servers-and-single-databases), [PowerShell](sql-database-single-databases-manage.md#powershell-manage-sql-database-servers-and-single-databases), [Azure CLI](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases) 또는 [REST API](sql-database-single-databases-manage.md#rest-api-manage-sql-database-servers-and-single-databases)를 사용하여 단일 데이터베이스에 대한 서비스 계층, 컴퓨팅 크기 및 스토리지 용량을 설정할 수 있습니다.

> [!IMPORTANT]
> 크기 조정 지침 및 고려 사항은 [단일 데이터베이스 크기 조정](sql-database-single-database-scale.md) 을 참조 하세요.

### <a name="basic-service-tier"></a>기본 서비스 계층

| **컴퓨팅 크기** | **Basic** |
| :--- | --: |
| 최대 DTU | 5 |
| 포함된 스토리지(GB) | 2 |
| 최대 스토리지(GB) 옵션 | 2 |
| 최대 메모리 내 OLTP 스토리지(GB) |해당 없음 |
| 최대 동시 작업자(요청) | 30 |
| 최대 동시 세션 | 300 |
|||

### <a name="standard-service-tier"></a>표준 서비스 계층

| **컴퓨팅 크기** | **S0** | **S1** | **S2** | **S3** |
| :--- |---:| ---:|---:|---:|
| 최대 DTU | 10 | 20 | 50 | 100 |
| 포함된 스토리지(GB) | 250 | 250 | 250 | 250 |
| 최대 스토리지(GB) 옵션 | 250 | 250 | 250 | 250, 500, 750, 1024 |
| 최대 메모리 내 OLTP 스토리지(GB) | 해당 없음 | 해당 없음 | 해당 없음 | 해당 없음 |
| 최대 동시 작업자(요청)| 60 | 90 | 120 | 200 |
| 최대 동시 세션 |600 | 900 | 1200 | 2400 |
||||||

### <a name="standard-service-tier-continued"></a>표준 서비스 계층(계속)

| **컴퓨팅 크기** | **S4** | **S6** | **S7** | **S9** | **S12** |
| :--- |---:| ---:|---:|---:|---:|
| 최대 DTU | 200 | 400 | 800 | 1600 | 3000 |
| 포함된 스토리지(GB) | 250 | 250 | 250 | 250 | 250 |
| 최대 스토리지(GB) 옵션 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 |
| 최대 메모리 내 OLTP 스토리지(GB) | 해당 없음 | 해당 없음 | 해당 없음 | 해당 없음 |해당 없음 |
| 최대 동시 작업자(요청)| 400 | 800 | 1600 | 3200 |6000 |
| 최대 동시 세션 |4800 | 9600 | 19200 | 30000 |30000 |
|||||||

### <a name="premium-service-tier"></a>프리미엄 서비스 계층

| **컴퓨팅 크기** | **P1** | **P2** | **P4** | **P6** | **P11** | **P15** |
| :--- |---:|---:|---:|---:|---:|---:|
| 최대 DTU | 125 | 250 | 500 | 1000 | 1750 | 4000 |
| 포함된 스토리지(GB) | 500 | 500 | 500 | 500 | 4096 * | 4096 * |
| 최대 스토리지(GB) 옵션 | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 4096 * | 4096 * |
| 최대 메모리 내 OLTP 스토리지(GB) | 1 | 2 | 4 | 8 | 14 | 32 |
| 최대 동시 작업자(요청)| 200 | 400 | 800 | 1600 | 2400 | 6400 |
| 최대 동시 세션 | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
|||||||

1024 GB에서 최대 4096 GB까지 \* 256 GB 단위로

> [!IMPORTANT]
> 프리미엄 계층에 1TB 이상 저장소는 중국 동부, 중국 북부, 독일 중부, 독일 북동쪽, 미국 서 부, US DoD 지역 및 미국 정부 중부를 제외한 모든 지역에서 사용할 수 있습니다. 이러한 지역에서 프리미엄 계층 스토리지 최대 크기는 1TB로 제한됩니다.  자세한 내용은 [P11-P15 현재 제한 사항](sql-database-single-database-scale.md#p11-and-p15-constraints-when-max-size-greater-than-1-tb)을 참조하세요.  
> [!NOTE]
> `tempdb` 제한은 [tempdb 제한](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database?view=sql-server-2017#tempdb-database-in-sql-database)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

- 단일 데이터베이스에 대 한 vCore 리소스 제한은 [vcore 구매 모델을 사용 하 여 단일 데이터베이스에 대 한 리소스 제한](sql-database-vcore-resource-limits-single-databases.md) 을 참조 하세요.
- 탄력적 풀에 대 한 vCore 리소스 제한은 [vcore 구매 모델을 사용 하 여 탄력적 풀에 대 한 리소스 제한](sql-database-vcore-resource-limits-elastic-pools.md) 을 참조 하세요.
- 탄력적 풀에 대 한 DTU 리소스 제한의 경우 [dtu 구매 모델을 사용 하 여 탄력적 풀에 대 한 리소스 제한](sql-database-dtu-resource-limits-elastic-pools.md) 을 참조 하세요.
- 관리되는 인스턴스에 대한 리소스 제한의 경우 [관리되는 인스턴스 리소스 제한](sql-database-managed-instance-resource-limits.md)을 참조합니다.
- 일반 Azure 제한에 대한 자세한 내용은 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-subscription-service-limits.md)을 참조하세요.
- 데이터베이스 서버의 리소스 제한에 대한 자세한 내용은 서버 및 구독 수준의 한도에 관한 정보인 경우 [SQL Database 서버의 리소스 제한 개요](sql-database-resource-limits-database-server.md)를 참조하세요.
