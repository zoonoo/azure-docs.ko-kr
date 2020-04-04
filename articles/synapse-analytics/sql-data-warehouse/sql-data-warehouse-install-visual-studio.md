---
title: Visual Studio 2019 설치
description: 시냅스 SQL용 비주얼 스튜디오 및 SQL 서버 개발 도구(SSDT) 설치
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
ms.openlocfilehash: b8e37a15f473e5777fe5412b785a0916bd78f095
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655908"
---
# <a name="getting-started-with-visual-studio-2019"></a>비주얼 스튜디오 2019 시작

Visual Studio **2019** SQL 서버 데이터 도구(SSDT)는 다음을 수행할 수 있는 단일 도구입니다.

- 응용 프로그램 연결, 쿼리 및 개발
- 개체 탐색기를 활용하여 테이블, 뷰, 저장 프로시저 등을 포함하여 데이터 모델의 모든 개체를 시각적으로 탐색할 수 있습니다.
- 개체에 대한 T-SQL 데이터 정의 언어(DDL) 스크립트 생성
- SSDT 데이터베이스 프로젝트를 사용하여 상태 기반 접근 방식을 사용하여 데이터 웨어하우스 개발
- 데이터베이스 프로젝트를 Git과 같은 소스 제어 시스템과 Azure Repos에 통합
- Azure DevOps와 같은 자동화 서버로 지속적인 통합 및 배포 파이프라인 설정

## <a name="install-visual-studio-2019"></a>Visual Studio 2019 설치

[Visual Studio 2019 다운로드를][] 참조하여 Visual Studio **16.3 이상을**다운로드하여 설치하십시오. 설치 하는 동안 데이터 저장소 및 처리 워크로드를 선택 합니다. Visual Studio 2019에서는 독립 실행형 SSDT 설치가 더 이상 필요하지 않습니다.

## <a name="unsupported-features-in-ssdt"></a>SSDT에서 지원되지 않는 기능

Synapse SQL에 대한 기능 릴리스에 SSDT에 대한 지원이 포함되지 않을 수 있습니다. 다음 기능은 현재 지원되지 않습니다.

- [구체화된 뷰](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [정렬된 클러스터된 열 저장소 인덱스](/sql/t-sql/statements/create-columnstore-index-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest#examples--and-)
- [복사 문](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [워크로드 관리](sql-data-warehouse-workload-management.md) - 워크로드 그룹 및 분류자
- [행 수준 보안](/sql/relational-databases/security/row-level-security?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
  - 지원 티켓을 제출하거나 [여기에](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040057-ssdt-row-level-security) 투표하여 지원 기능을 받으십시오.
- [동적 데이터 마스킹](/sql/relational-databases/security/dynamic-data-masking?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest#defining-a-dynamic-data-mask)
  - 지원 티켓을 제출하거나 [여기에](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040048-ssdt-support-dynamic-data-masking) 투표하여 지원 기능을 받으십시오.
- [제약 조건이 있는 테이블은](sql-data-warehouse-table-constraints.md#table-constraints) 지원되지 않습니다. 이러한 테이블 개체의 경우 빌드 작업을 "없음"으로 설정합니다.

## <a name="next-steps"></a>다음 단계

이제 최신 버전의 SSDT를 사용하므로 SQL 풀에 [연결할](sql-data-warehouse-query-visual-studio.md) 준비가 되었습니다.

<!--Other-->

[Visual Studio 2019 다운로드]: https://visualstudio.microsoft.com/downloads/
[Installing Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[SSDT Download]: https://msdn.microsoft.com/library/mt204009.aspx
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
