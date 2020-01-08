---
title: Azure Security Center에서 데이터 서비스에 대 한 위협 감지 | Microsoft Docs
description: 이 문서에서는 Azure Security Center에서 사용할 수 있는 데이터 서비스 경고를 제공 합니다.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: da960861-0b6c-4d80-932d-898cdebb4f83
ms.service: security-center
ms.topic: conceptual
ms.date: 01/05/2020
ms.author: memildin
ms.openlocfilehash: 6d6e48c84f558840b3266193c4cbb9c3576f1a58
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/06/2020
ms.locfileid: "75665737"
---
# <a name="threat-detection-for-data-services-in-azure-security-center"></a>Azure Security Center에서 데이터 서비스에 대 한 위협 감지

 Azure Security Center는 데이터 저장소 서비스의 로그를 분석 하 고 데이터 리소스에 대 한 위협이 감지 되 면 경고를 트리거합니다. 이 문서에서는 다음 서비스에 대해 Security Center에서 생성 하는 경고를 나열 합니다.

* [Azure SQL Database 및 Azure SQL Data Warehouse](#data-sql)
* [Azure Storage](#azure-storage)
* [Azure Cosmos DB](#cosmos-db)

## SQL Database 및 SQL Data Warehouse<a name="data-sql"></a>

Azure SQL Database에 대 한 Advanced Threat Protection은 비정상적인 활동을 감지 하 여 데이터베이스에 액세스 하거나 악용 하려는 비정상적인 시도를 감지 합니다.

의심 스러운 데이터베이스 활동, 잠재적 취약성 또는 SQL 삽입 공격 뿐만 아니라 비정상적인 데이터베이스 액세스 및 쿼리 패턴을 사용 하는 경우 경고가 표시 됩니다.

Azure SQL Database 및 SQL에 대 한 advanced Threat Protection은 azure Virtual Machines의 Azure SQL Database Azure SQL Database, 관리 되는 인스턴스, Azure SQL Data Warehouse 데이터베이스 및 SQL server에 대 한 고급 SQL 보안 기능을 제공 하는 [ad (Advanced Data Security)](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security) 통합 패키지의 일부입니다.

자세한 내용은 다음을 참조하세요.

* [Azure SQL Database에 대해 Advanced Threat Protection을 사용 하도록 설정 하는 방법](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)
* [Azure Virtual Machines에서 SQL server에 대 한 Advanced Threat Protection을 사용 하도록 설정 하는 방법](security-center-iaas-advanced-data.md)
* [SQL Database 및 SQL Data Warehouse에 대 한 위협 방지 경고 목록](alerts-reference.md#alerts-sql-db-and-warehouse)

## Azure Storage<a name="azure-storage"></a>

저장소에 대 한 고급 위협 방지 (현재 Blob 저장소에만 사용 가능)는 저장소 계정에 액세스 하거나 악용 하려는 비정상적이 고 잠재적으로 유해한 시도를 검색 합니다. 이 보호 계층을 사용 하면 보안 전문가가 아니어도 위협을 해결 하 고 보안 모니터링 시스템을 관리할 수 있습니다.

>[!NOTE]
>저장소에 대 한 Advanced Threat Protection은 현재 Azure 정부 및 소 버린 클라우드 지역에서 사용할 수 없습니다.

자세한 내용은 다음을 참조하세요.

* [Azure Storage에 대해 Advanced Threat Protection을 사용 하도록 설정 하는 방법](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection)
* [Azure Storage에 대 한 위협 방지 경고 목록](alerts-reference.md#alerts-azurestorage)

## Azure Cosmos DB<a name="cosmos-db"></a>

Azure Cosmos DB 경고는 비정상적이 고 잠재적으로 유해한 시도로 인해 Azure Cosmos DB 계정에 액세스 하거나 악용할 때 생성 됩니다.

자세한 내용은 다음을 참조하세요.

* [Azure Cosmos DB에 대 한 Advanced Threat Protection (미리 보기)](../cosmos-db/cosmos-db-advanced-threat-protection.md)
* [Azure Cosmos DB에 대 한 위협 방지 경고 목록 (미리 보기)](alerts-reference.md#alerts-azurecosmos)
