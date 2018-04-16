---
title: "SQL Data Warehouse와 함께 Azure Data Factory 사용 | Microsoft Docs"
description: "솔루션 개발을 위한 Azure SQL 데이터 웨어하우스의 Azure 데이터 팩터리(ADF) 사용을 위한 팁"
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: jhubbard
editor: 
ms.assetid: 492de762-c7a2-4cdb-943f-3135230e94f1
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: integrate
ms.date: 10/31/2016
ms.author: elbutter;barbkess
ms.openlocfilehash: 6adfa1264c9d196d6c6e57f1d108710b9ee73265
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-data-factory-with-sql-data-warehouse"></a>SQL 데이터 웨어하우스와 함께 Azure 데이터 팩터리 사용
Azure 데이터 팩터리는 데이터를 전송 및 SQL 데이터 웨어하우스에 대한 저장 프로시저의 실행 조율을 위한 완전한 관리 메서드를 제공합니다.  이렇게 하면 복잡한 ETL(Extract Transform and Load) 프로시저를 SQL 데이터 웨어하우스와 함께 보다 쉽게 설정하고 예약할 수 있습니다. Azure Data Factory의 전체 개요는 [Azure Data Factory 설명서][Azure Data Factory documentation]를 참조하세요.

## <a name="data-movement"></a>데이터 이동
Azure 데이터 팩터리는 온-프레미스 원본 및 다른 Azure 서비스 간의 데이터 이동을 설정할 수 있습니다.  Azure 데이터 팩터리와의 전반적인 현재 통합은 다음 위치에서의/으로의 데이터 이동을 지원합니다.

* Azure Blob 저장소
* Azure SQL 데이터베이스
* 온-프레미스 SQL Server
* IaaS의 SQL Server

데이터 복사 활동을 설정하는 방법에 대한 정보는 [Azure Data Factory를 사용하여 데이터 복사][Copy data with Azure Data Factory]를 참조하세요.

## <a name="stored-procedures"></a>저장 프로시저
 데이터 전송을 예약하는 데 사용할 수 있는 동일한 방법으로 Azure 데이터 팩터리는 저장 프로시저의 실행을 오케스트레이션하는 데도 사용될 수 있습니다.  이렇게 하면 더 복잡한 파이프라인을 만들 수 있으며 Azure 데이터 팩터리 기능을 확장하여 SQL 데이터 웨어하우스의 컴퓨팅 기능을 활용할 수 있습니다.

## <a name="next-steps"></a>다음 단계
통합 개요는 [SQL Data Warehouse 통합 개요][SQL Data Warehouse integration overview]를 참조하세요.
더 많은 개발 팁은 [SQL Data Warehouse 개발 개요][SQL Data Warehouse development overview]를 참조하세요.

<!--Image references-->

<!--Article references-->

[Copy data with Azure Data Factory]: ../data-factory/copy-activity-overview.md
[SQL Data Warehouse development overview]: ./sql-data-warehouse-overview-develop.md
[SQL Data Warehouse integration overview]: ./sql-data-warehouse-overview-integrate.md

<!--MSDN references-->

<!--Other Web references-->
[Azure Data Factory documentation]:https://azure.microsoft.com/documentation/services/data-factory/

