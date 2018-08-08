---
title: SQL Data Warehouse 권장 사항 - 개념 | Microsoft Docs
description: SQL Data Warehouse 권장 사항 및 생성 방식에 대해 설명
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 07/27/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 2cbd691c29039c65b98d8b0191e9e278d2440f09
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/30/2018
ms.locfileid: "39348161"
---
# <a name="sql-data-warehouse-recommendations"></a>SQL Data Warehouse 권장 사항

이 문서에서는 SQL Data Warehouse가 Azure Advisor를 통해 제공하는 권장 사항을 설명합니다.  

SQL Data Warehouse는 데이터 웨어하우스가 지속적으로 성능에 최적화되도록 권장 사항을 제공합니다. 데이터 웨어하우스 권장 사항은 [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-performance-recommendations)와 긴밀하게 통합되어 [Azure Portal](https://aka.ms/Azureadvisor) 내에서 직접 모범 사례를 제공합니다. SQL Data Warehouse는 날마다 데이터 웨어하우스의 현재 상태를 분석하고, 원격 분석 데이터를 수집하고, 활성 워크로드에 대한 권장 사항을 표시합니다. 아래에는 지원되는 데이터 웨어하우스 권장 시나리오 및 권장 조치를 적용하는 방법이 설명되어 있습니다.

SQL Data Warehouse Advisor에 대한 피드백이 있거나 문제가 있는 경우 [sqldwadvisor@service.microsoft.com](mailto:sqldwadvisor@service.microsoft.com)으로 알려주세요.   

[여기](https://aka.ms/Azureadvisor)를 클릭하여 현재 권장 사항을 확인하세요. 현재 이 기능은 Gen2 데이터 웨어하우스에만 적용됩니다. 

## <a name="data-skew"></a>데이터 기울이기

데이터 기울이기는 워크로드를 실행할 때 추가 데이터 이동 또는 리소스 병목 현상을 일으킬 수 있습니다. 다음 문서에서는 데이터 기울이기를 발견하고 최적의 배포 키를 선택하여 데이터 기울이기를 방지하는 방법을 설명합니다.

- [기울이기 식별 및 제거](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute#how-to-tell-if-your-distribution-column-is-a-good-choice) 

## <a name="no-or-outdated-statistics"></a>통계가 없거나 오래된 통계

최적 통계가 아니면 SQL Data Warehouse 쿼리 최적화 프로그램이 최적이 아닌 쿼리 계획을 생성할 수 있으므로 쿼리 성능에 심각한 악영향을 미칠 수 있습니다. 다음 문서에서는 통계 생성 및 업데이트와 관련된 모범 사례를 설명합니다.

- [테이블 통계 생성 및 업데이트](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistic)

이러한 두 권장 사항과 관련하여, 관리자는 지속적으로 다음 [T-SQL 스크립트](https://github.com/Microsoft/sql-data-warehouse-samples/blob/master/samples/sqlops/MonitoringScripts/ImpactedTables)를 실행하여 기울이기 및 통계 권장 사항의 영향을 받는 테이블을 식별해야 합니다.
