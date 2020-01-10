---
title: Visual Studio 2019 설치
description: Azure SQL Data Warehouse용 Visual Studio 및 SSDT(SQL Server 개발 도구) 설치
services: sql-data-warehouse
ms.custom: seo-lt-2019
ms.workload: azure-vs
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 01/07/2020
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: b2b7b2413a187c446aace4eb1e416dea1b4258d3
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/08/2020
ms.locfileid: "75750648"
---
# <a name="getting-started-with-visual-studio-2019-for-sql-data-warehouse"></a>SQL Data Warehouse 용 Visual Studio 2019 시작
Visual Studio **2019** SQL SERVER DATA TOOLS (SSDT)는 다음을 수행 하는 데 사용할 수 있는 단일 도구입니다.

- SQL Data Warehouse에 대 한 응용 프로그램 연결, 쿼리 및 개발 
- 개체 탐색기를 활용 하 여 테이블, 뷰, 저장 프로시저 등을 비롯 한 데이터 모델의 모든 개체를 시각적으로 탐색할 수 있습니다.
- 개체에 대 한 T-sql DDL (데이터 정의 언어) 스크립트를 생성 합니다.
- SSDT Database 프로젝트를 사용 하 여 상태 기반 방법을 사용 하 여 데이터 웨어하우스 개발
- Azure Repos를 사용 하 여 데이터베이스 프로젝트를 Git와 같은 원본 제어 시스템과 통합
- Azure DevOps와 같은 자동화 서버를 사용 하 여 연속 통합 및 배포 파이프라인 설정

## <a name="install-visual-studio-2019"></a>Visual Studio 2019 설치
Visual studio **16.3**를 다운로드 하 여 설치 하려면 visual [Visual Studio 2019 다운로드][] 를 참조 하세요. 설치 하는 동안 데이터 저장소 및 처리 워크 로드를 선택 합니다. Visual Studio 2019에는 독립 실행형 SSDT 설치가 더 이상 필요 하지 않습니다.

## <a name="unsupported-features-in-ssdt"></a>SSDT에서 지원 되지 않는 기능

SQL Data Warehouse에 대 한 기능 릴리스에 SSDT에 대 한 지원이 포함 되지 않을 수 있습니다. 현재 지원 되지 않는 기능은 다음과 같습니다.

- [구체화 된 뷰](https://docs.microsoft.com/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest) (진행 중)
- [순서가 지정 된 클러스터형 Columnstore 인덱스](https://docs.microsoft.com/sql/t-sql/statements/create-columnstore-index-transact-sql?view=azure-sqldw-latest#examples--and-) (진행 중)
- [COPY 문](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (진행 중)
- [워크 로드 관리](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-workload-management) -작업 그룹 및 분류자 (진행 중)
- [행 수준 보안](https://docs.microsoft.com/sql/relational-databases/security/row-level-security?view=sql-server-ver15)
  - 지원 티켓을 제출 하거나 [여기](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040057-ssdt-row-level-security) 에 투표 하 여 지원 되는 기능을 받으세요.
- [동적 데이터 마스킹](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking?toc=%2Fazure%2Fsql-data-warehouse%2Ftoc.json&view=sql-server-2017#defining-a-dynamic-data-mask)
  - 지원 티켓을 제출 하거나 [여기](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040048-ssdt-support-dynamic-data-masking) 에 투표 하 여 지원 되는 기능을 받으세요. 
- [PREDICT](https://docs.microsoft.com/sql/t-sql/queries/predict-transact-sql?view=sql-server-ver15&viewFallbackFrom=azure-sqldw-latest) 함수 
- [제약 조건이 있는 테이블](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-table-constraints#table-constraints) 은 지원 되지 않습니다. 이러한 테이블 개체에 대해 빌드 작업을 "없음"으로 설정 합니다.

## <a name="next-steps"></a>다음 단계

최신 버전의 SSDT를 만들었으므로 SQL Data Warehouse에 [연결할][connect] 준비가 되었습니다.

<!--Anchors-->

<!--Image references-->

<!--Articles-->
[connect]: ./sql-data-warehouse-query-visual-studio.md

<!--Other-->
[Visual Studio 2019 다운로드]: https://visualstudio.microsoft.com/downloads/
[Installing Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[SSDT Download]: https://msdn.microsoft.com/library/mt204009.aspx
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
