---
title: Visual Studio 2019 설치
description: Synapse SQL 용 Visual Studio 및 SSDT (SQL Server Development Tools) 설치
services: synapse-analytics
ms.custom: vs-azure, azure-synapse
ms.workload: azure-vs
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: f83ae9c8290a52381c8087b46da959d4723d7f4e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80745157"
---
# <a name="getting-started-with-visual-studio-2019"></a>Visual Studio 2019 시작

Visual Studio **2019** SQL SERVER DATA TOOLS (SSDT)는 다음을 수행 하는 데 사용할 수 있는 단일 도구입니다.

- 응용 프로그램 연결, 쿼리 및 개발
- 개체 탐색기를 활용 하 여 테이블, 뷰, 저장 프로시저 등을 비롯 한 데이터 모델의 모든 개체를 시각적으로 탐색할 수 있습니다.
- 개체에 대 한 T-sql DDL (데이터 정의 언어) 스크립트를 생성 합니다.
- SSDT Database 프로젝트를 사용 하 여 상태 기반 방법을 사용 하 여 데이터 웨어하우스 개발
- Azure Repos를 사용 하 여 데이터베이스 프로젝트를 Git와 같은 원본 제어 시스템과 통합
- Azure DevOps와 같은 자동화 서버를 사용 하 여 연속 통합 및 배포 파이프라인 설정

## <a name="install-visual-studio-2019"></a>Visual Studio 2019 설치

Visual studio **16.3**를 다운로드 하 여 설치 하려면 visual [studio 2019 다운로드](https://visualstudio.microsoft.com/downloads/) 를 참조 하세요. 설치 하는 동안 데이터 저장소 및 처리 워크 로드를 선택 합니다. Visual Studio 2019에는 독립 실행형 SSDT 설치가 더 이상 필요 하지 않습니다.

## <a name="unsupported-features-in-ssdt"></a>SSDT에서 지원 되지 않는 기능

Synapse SQL의 기능 릴리스에 SSDT에 대 한 지원이 포함 되지 않는 경우가 있습니다. 현재 지원 되지 않는 기능은 다음과 같습니다.

- [구체화된 보기](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [순서가 지정 된 클러스터형 Columnstore 인덱스](/sql/t-sql/statements/create-columnstore-index-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest#examples--and-)
- [COPY 문](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [워크 로드 관리](sql-data-warehouse-workload-management.md) -작업 그룹 및 분류자
- [행 수준 보안](/sql/relational-databases/security/row-level-security?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
  - 지원 티켓을 제출 [하거나 투표](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040057-ssdt-row-level-security) 하 여 지원 되는 기능을 받으세요.
- [동적 데이터 마스킹](/sql/relational-databases/security/dynamic-data-masking?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest#defining-a-dynamic-data-mask)
   - 지원 티켓을 제출 [하거나 투표](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040048-ssdt-support-dynamic-data-masking) 하 여 지원 되는 기능을 받으세요.
- [제약 조건이 있는 테이블](sql-data-warehouse-table-constraints.md#table-constraints) 은 지원 되지 않습니다. 이러한 테이블 개체에 대해 빌드 작업을 "없음"으로 설정 합니다.

## <a name="next-steps"></a>다음 단계

최신 버전의 SSDT를 만들었으므로 SQL 풀에 [연결할](sql-data-warehouse-query-visual-studio.md) 준비가 되었습니다.
