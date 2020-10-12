---
title: 데이터 검색 및 분류
description: Azure SQL Database, Azure SQL Managed Instance 및 Azure Synapse Analytics에 대 한 데이터 검색 & 분류
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: sqldbrb=1
titleSuffix: Azure SQL Database, Azure SQL Managed Instance, and Azure Synapse
ms.devlang: ''
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 09/21/2020
tags: azure-synapse
ms.openlocfilehash: 6f324b1b0b5ed1882050684e7ac1c8ec4ea573dc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90886509"
---
# <a name="data-discovery--classification"></a>데이터 검색 및 분류
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

데이터 검색 & 분류는 Azure SQL Database, Azure SQL Managed Instance 및 Azure Synapse Analytics에 기본 제공 됩니다. 데이터베이스에서 중요 한 데이터를 검색, 분류, 레이블 지정 및 보고 하는 고급 기능을 제공 합니다.

가장 중요 한 데이터에는 비즈니스, 금융, 의료 또는 개인 정보가 포함 될 수 있습니다. 이 데이터를 검색 하 고 분류 하면 조직의 정보 보호 방식에서 pivotal 역할을 수행할 수 있습니다. 다음에 대한 인프라를 제공할 수 있습니다.

- 규정 준수를 위한 데이터 개인 정보 및 요구 사항에 대 한 표준을 충족 하는 데 도움이 됩니다.
- 중요한 데이터에 대한 비정상적인 엑세스 모니터링(감사) 및 경고하는 것과 같은 다양한 보안 시나리오.
- 매우 중요 한 데이터를 포함 하는 데이터베이스에 대 한 액세스 제어 및 보안 강화

> [!NOTE]
> 온-프레미스 SQL Server에 대 한 자세한 내용은 [SQL 데이터 검색 & 분류](https://go.microsoft.com/fwlink/?linkid=866999)를 참조 하세요.

## <a name="what-is-data-discovery--classification"></a><a id="what-is-dc"></a>데이터 검색 & 분류 란?

데이터 검색 & 분류에는 Azure의 일련의 고급 서비스 및 새로운 기능이 도입 되었습니다. 데이터베이스 뿐만 아니라 데이터를 보호 하는 것을 목표로 하는 SQL Database, SQL Managed Instance 및 Azure Synapse에 대 한 새로운 정보 보호 패러다임을 형성 합니다. 패러다임에는 다음이 포함 됩니다.

- **검색 및 권장 사항:** 분류 엔진은 데이터베이스를 검색 하 고 잠재적으로 중요 한 데이터가 포함 된 열을 식별 합니다. 그런 다음 Azure Portal를 통해 권장 분류를 쉽게 검토 하 고 적용할 수 있는 방법을 제공 합니다.

- **레이블 지정:** SQL Server 데이터베이스 엔진에 추가 된 새 메타 데이터 특성을 사용 하 여 민감도 분류 레이블을 열에 영구적으로 적용할 수 있습니다. 그런 다음이 메타 데이터를 고급, 민감도 기반 감사 및 보호 시나리오에 사용할 수 있습니다.

- **쿼리 결과-민감도를 설정 합니다.** 쿼리 결과 집합의 민감도는 감사를 위해 실시간으로 계산 됩니다.

- **표시 유형:** Azure Portal의 상세 대시보드에서 데이터베이스 분류 상태를 볼 수 있습니다. 또한 호환성 및 감사 목적 및 기타 요구 사항에 사용할 수 있도록 보고서를 Excel 형식으로 다운로드할 수 있습니다.

## <a name="discover-classify-and-label-sensitive-columns"></a><a id="discover-classify-columns"></a>중요 한 열 검색, 분류 및 레이블

이 섹션에서는 다음에 대 한 단계를 설명 합니다.

- 데이터베이스의 중요 한 데이터를 포함 하는 열을 검색, 분류 및 레이블 지정
- 데이터베이스의 현재 분류 상태를 보고 보고서를 내보내는 중입니다.

분류에는 두 개의 메타데이터 특성이 포함됩니다.

- **레이블**: 열에 저장 된 데이터의 민감도 수준을 정의 하는 데 사용 되는 기본 분류 특성입니다.  
- **정보 형식**: 열에 저장 된 데이터 형식에 대 한 보다 세부적인 정보를 제공 하는 특성입니다.

### <a name="define-and-customize-your-classification-taxonomy"></a>분류 체계 정의 및 사용자 지정

데이터 검색 & 분류는 기본 제공 된 민감도 레이블 집합과 기본 제공 정보 형식 및 검색 논리 집합을 제공 합니다. 이제 이 분류법을 사용자 지정하고, 사용자 환경에 맞게 분류 구문 집합과 순위를 정의할 수 있습니다.

전체 Azure 조직에 대해 하나의 중앙에서 분류 분류를 정의 하 고 사용자 지정 합니다. 해당 위치는 보안 정책의 일부로 [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)에 있습니다. 조직의 루트 관리 그룹에 대 한 관리 권한이 있는 사용자만이 작업을 수행할 수 있습니다.

Information protection에 대 한 정책 관리의 일부로 사용자 지정 레이블을 정의 하 고 순위를 지정 하 고 선택한 정보 유형 집합에 연결할 수 있습니다. 사용자 고유의 사용자 지정 정보 형식을 추가 하 고 문자열 패턴을 사용 하 여 구성할 수도 있습니다. 패턴은 데이터베이스에서 이러한 유형의 데이터를 식별 하기 위한 검색 논리에 추가 됩니다.

자세한 내용은 [Azure Security Center에서 SQL information protection 정책 사용자 지정 (미리 보기)](https://go.microsoft.com/fwlink/?linkid=2009845&clcid=0x409)을 참조 하세요.

조직 차원의 정책을 정의한 후에는 사용자 지정 된 정책을 사용 하 여 개별 데이터베이스를 계속 분류할 수 있습니다.

### <a name="classify-your-database"></a>데이터베이스 분류

> [!NOTE]
> 아래 예제에서는 Azure SQL Database를 사용 하지만 데이터 검색 & 분류를 구성 하려는 적절 한 제품을 선택 해야 합니다.

1. [Azure 포털](https://portal.azure.com)로 이동합니다.

1. Azure SQL Database 창의 보안 제목 아래에서 **데이터 검색 & 분류** 로 이동 합니다. 개요 탭에는 데이터베이스의 현재 분류 상태 요약이 포함 되어 있습니다. 요약에는 특정 스키마 파트, 정보 유형 및 레이블만 표시 하도록 필터링 할 수 있는 모든 분류 된 열의 상세 목록이 포함 되어 있습니다. 아직 열을 분류 하지 않은 경우 [4 단계로 건너뜁니다](#step-4).

1. Excel 형식으로 보고서를 다운로드 하려면 창의 최상위 메뉴에서 **내보내기** 를 선택 합니다.

1. <a id="step-4"></a>데이터 분류를 시작 하려면 **데이터 검색 & 분류** 페이지에서 **분류** 탭을 선택 합니다.

    분류 엔진은 데이터베이스에서 잠재적으로 중요 한 데이터가 포함 된 열을 검색 하 고 권장 되는 열 분류 목록을 제공 합니다.

1. 분류 권장 사항을 보고 적용 합니다.

   - 권장 열 분류 목록을 보려면 창 맨 아래에 있는 권장 사항 패널을 선택 합니다.

   - 특정 열에 대 한 권장 사항을 적용 하려면 관련 행의 왼쪽 열에 있는 확인란을 선택 합니다. 모든 권장 사항을 허용 됨으로 표시 하려면 권장 구성 테이블 헤더에서 가장 왼쪽에 있는 확인란을 선택 합니다.

   - 선택한 권장 사항을 적용 하려면 **선택한 권장 사항 적용**을 선택 합니다.

1. 또한 다른 방법으로 또는 권장 사항 기반 분류를 비롯 하 여 열을 수동으로 분류할 수도 있습니다.

   1. 창의 위쪽 메뉴에서 **분류 추가** 를 선택 합니다.

   1. 열리는 컨텍스트 창에서 분류 하려는 스키마, 테이블 및 열과 정보 유형 및 민감도 레이블을 선택 합니다.

   1. 컨텍스트 창의 아래쪽에서 **분류 추가** 를 선택 합니다.

1. 분류를 완료 하 고 새 분류 메타 데이터를 사용 하 여 데이터베이스 열을 영구적으로 레이블 (태그) 하려면 창의 상단 메뉴에서 **저장** 을 선택 합니다.

## <a name="audit-access-to-sensitive-data"></a><a id="audit-sensitive-data"></a>중요 한 데이터에 대 한 액세스 감사

정보 보호 패러다임의 중요 한 측면은 중요 한 데이터에 대 한 액세스를 모니터링 하는 기능입니다. [AZURE SQL 감사](../../azure-sql/database/auditing-overview.md) 는 이라는 감사 로그에 새 필드를 포함 하도록 향상 되었습니다 `data_sensitivity_information` . 이 필드는 쿼리에 의해 반환 된 데이터의 민감도 분류 (레이블)를 기록 합니다. 예를 들면 다음과 같습니다.

![감사 로그](./media/data-discovery-and-classification-overview/11_data_classification_audit_log.png)

## <a name="permissions"></a><a id="permissions"></a>권한

이러한 기본 제공 역할은 데이터베이스의 데이터 분류를 읽을 수 있습니다.

- 소유자
- 판독기
- 참가자
- SQL 보안 관리자
- 사용자 액세스 관리자

이러한 기본 제공 역할은 데이터베이스의 데이터 분류를 수정할 수 있습니다.

- 소유자
- 참가자
- SQL 보안 관리자

[AZURE RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)의 역할 기반 권한에 대해 자세히 알아보세요.

## <a name="manage-classifications"></a><a id="manage-classification"></a>분류 관리

T-sql, REST API 또는 PowerShell을 사용 하 여 분류를 관리할 수 있습니다.

### <a name="use-t-sql"></a>T-SQL 사용

T-sql을 사용 하 여 열 분류를 추가 또는 제거 하 고 전체 데이터베이스에 대 한 모든 분류를 검색할 수 있습니다.

> [!NOTE]
> T-sql을 사용 하 여 레이블을 관리할 때는 열에 추가 하는 레이블이 조직의 정보 보호 정책 (포털 권장 사항에 표시 되는 레이블 집합)에 존재 한다는 유효성 검사가 수행 되지 않습니다. 따라서 유효성을 검사할 수 있습니다.

분류에 T-sql을 사용 하는 방법에 대 한 자세한 내용은 다음 참조를 참조 하세요.

- 하나 이상의 열에 대 한 분류를 추가 하거나 업데이트 하려면: [민감도 분류를 추가](https://docs.microsoft.com/sql/t-sql/statements/add-sensitivity-classification-transact-sql) 합니다.
- 하나 이상의 열에서 분류를 제거 하려면: [DROP 민감도 분류](https://docs.microsoft.com/sql/t-sql/statements/drop-sensitivity-classification-transact-sql)
- 데이터베이스의 모든 분류를 보려면 다음을 수행 하십시오. [sys.sensitivity_classifications](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-sensitivity-classifications-transact-sql)

### <a name="use-powershell-cmdlets"></a>PowerShell cmdlet 사용
PowerShell을 사용 하 여 Azure SQL Database 및 Azure SQL Managed Instance에 대 한 분류 및 권장 사항을 관리 합니다.

#### <a name="powershell-cmdlets-for-azure-sql-database"></a>Azure SQL Database에 대 한 PowerShell cmdlet

- [AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityclassification)
- [AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasesensitivityclassification)
- [AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabasesensitivityclassification)
- [AzSqlDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityrecommendation)
- [AzSqlDatabaSesensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/enable-azsqldatabasesensitivityrecommendation)
- [Disable-AzSqlDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/disable-azsqldatabasesensitivityrecommendation)

#### <a name="powershell-cmdlets-for-azure-sql-managed-instance"></a>Azure SQL Managed Instance에 대 한 PowerShell cmdlet

- [AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityclassification)
- [AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasesensitivityclassification)
- [AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstancedatabasesensitivityclassification)
- [AzSqlInstanceDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityrecommendation)
- [AzSqlInstanceDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/enable-azsqlinstancedatabasesensitivityrecommendation)
- [Disable-AzSqlInstanceDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/disable-azsqlinstancedatabasesensitivityrecommendation)

### <a name="use-the-rest-api"></a>Rest API 사용

REST API를 사용 하 여 분류 및 권장 사항을 프로그래밍 방식으로 관리할 수 있습니다. 게시된 REST API는 다음과 같은 작업을 지원합니다.

- [만들기 또는 업데이트](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/createorupdate): 지정된 열의 민감도 레이블을 만들거나 업데이트합니다.
- [삭제](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/delete): 지정 된 열의 민감도 레이블을 삭제 합니다.
- [권장 하지 않음](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/disablerecommendation): 지정 된 열에 대 한 민감도 권장 사항을 사용 하지 않도록 설정 합니다.
- [권장 구성 사용](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/enablerecommendation): 지정 된 열에서 민감도 권장 사항을 사용 하도록 설정 합니다. 권장 사항은 모든 열에 대해 기본적으로 사용 하도록 설정 되어 있습니다.
- [Get](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/get): 지정 된 열의 민감도 레이블을 가져옵니다.
- [데이터베이스당 현재 데이터베이스 나열](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listcurrentbydatabase): 지정 된 데이터베이스의 현재 민감도 레이블을 가져옵니다.
- [데이터베이스에서 권장 하는 목록](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listrecommendedbydatabase): 지정 된 데이터베이스의 권장 민감도 레이블을 가져옵니다.

## <a name="next-steps"></a><a id="next-steps"></a>다음 단계

- 분류 된 중요 한 데이터에 대 한 액세스 모니터링 및 감사를 위해 [AZURE SQL 감사](../../azure-sql/database/auditing-overview.md) 를 구성 하는 것이 좋습니다.
- 데이터 검색 & 분류를 포함 하는 프레젠테이션은 [SQL 데이터 검색, 분류, 레이블 지정 & 보호를 참조 하세요. 데이터가 노출](https://www.youtube.com/watch?v=itVi9bkJUNc)됩니다.
