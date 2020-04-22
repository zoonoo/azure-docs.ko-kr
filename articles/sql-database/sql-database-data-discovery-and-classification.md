---
title: 데이터 검색 및 분류
description: Azure SQL 데이터베이스 및 Azure 시냅스 분석을 위한 데이터 검색 & 분류
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
titleSuffix: Azure SQL Database and Azure Synapse
ms.devlang: ''
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 04/21/2020
tags: azure-synapse
ms.openlocfilehash: f05b4d4fec99aaa2fb79da46e2167d883d1f15ec
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81766953"
---
# <a name="data-discovery--classification-for-azure-sql-database-and-azure-synapse-analytics"></a>Azure SQL 데이터베이스 및 Azure 시냅스 분석을 위한 데이터 검색 & 분류

데이터 검색 & 분류는 Azure SQL 데이터베이스에 기본제공됩니다. 데이터베이스의 중요한 데이터를 검색, 분류, 레이블 지정 및 보고하기 위한 고급 기능을 제공합니다.

가장 중요한 데이터에는 비즈니스, 금융, 의료 또는 개인 정보가 포함될 수 있습니다. 이 데이터를 검색하고 분류하는 것은 조직의 정보 보호 접근 방식에서 중추적인 역할을 할 수 있습니다. 다음에 대한 인프라를 제공할 수 있습니다.

- 데이터 프라이버시 에 대한 표준 및 규정 준수 요구 사항을 충족하는 데 도움을 줍니다.
- 중요한 데이터에 대한 비정상적인 엑세스 모니터링(감사) 및 경고하는 것과 같은 다양한 보안 시나리오.
- 매우 중요한 데이터가 포함된 데이터베이스의 액세스 제어 및 보안 강화

데이터 검색 & 분류는 고급 SQL 보안 기능을 위한 통합 패키지인 [고급 데이터 보안](sql-database-advanced-data-security.md) 오퍼링의 일부입니다. Azure 포털의 중앙 **SQL 고급 데이터 보안** 섹션을 통해 데이터 검색 & 분류에 액세스하고 관리할 수 있습니다.

> [!NOTE]
> 이 문서에서는 Azure SQL 데이터베이스 및 Azure 시냅스 분석과 관련이 있습니다. 간단히 하기 위해 SQL 데이터베이스는 여기에서 *SQL 데이터베이스와* Azure Synapse를 모두 참조합니다. SQL Server(온-프레미스)에 대한 자세한 내용은 [SQL 데이터 검색 및 분류](https://go.microsoft.com/fwlink/?linkid=866999)를 참조하십시오.

## <a name="what-is-data-discovery--classification"></a><a id="what-is-dc"></a>데이터 검색 & 분류란 무엇입니까?

데이터 검색 & 분류에서는 고급 서비스 집합과 새로운 SQL Database 기능을 소개합니다. 데이터베이스뿐만 아니라 데이터를 보호하기 위한 SQL Database의 새로운 정보 보호 패러다임을 형성합니다. 패러다임에는 다음이 포함됩니다.

- **검색 및 권장 사항:** 분류 엔진은 데이터베이스를 검사하고 잠재적으로 중요한 데이터를 포함하는 열을 식별합니다. 그런 다음 Azure 포털을 통해 권장 분류를 쉽게 검토하고 적용할 수 있는 방법을 제공합니다.

- **라벨링:** SQL 데이터베이스 엔진에 추가된 새 메타데이터 특성을 사용하여 민감도 분류 레이블을 열에 지속적으로 적용할 수 있습니다. 그런 다음 이 메타데이터를 고급 민감도 기반 감사 및 보호 시나리오에 사용할 수 있습니다.

- **쿼리 결과 집합 민감도:** 쿼리 결과 집합의 민감도는 감사를 위해 실시간으로 계산됩니다.

- **가시성:** Azure 포털의 자세한 대시보드에서 데이터베이스 분류 상태를 볼 수 있습니다. 또한 보고서를 Excel 형식으로 다운로드하여 규정 준수 및 감사 목적 및 기타 요구 사항에 사용할 수 있습니다.

## <a name="discover-classify-and-label-sensitive-columns"></a><a id="discover-classify-columns"></a>중요한 열 검색, 분류 및 레이블 지정

이 섹션에서는 다음 단계에 대해 설명합니다.

- 데이터베이스에 중요한 데이터가 포함된 열을 검색, 분류 및 레이블 지정합니다.
- 데이터베이스의 현재 분류 상태를 보고 보고서를 내보냅니다.

분류에는 두 개의 메타데이터 특성이 포함됩니다.

- **레이블**: 열에 저장된 데이터의 민감도 수준을 정의하는 데 사용되는 주요 분류 특성입니다.  
- **정보 유형**: 열에 저장된 데이터 유형에 대한 보다 세부적인 정보를 제공하는 특성입니다.

### <a name="define-and-customize-your-classification-taxonomy"></a>분류 체계 정의 및 사용자 지정

데이터 검색 & 분류에는 감도 레이블의 기본 제공 집합과 정보 유형 및 검색 논리가 내장되어 있습니다. 이제 이 분류법을 사용자 지정하고, 사용자 환경에 맞게 분류 구문 집합과 순위를 정의할 수 있습니다.

전체 Azure 조직에 대해 하나의 중앙 위치에 분류 분류를 정의하고 사용자 지정합니다. 해당 위치는 [보안](https://docs.microsoft.com/azure/security-center/security-center-intro)정책의 일부로 Azure 보안 센터에 있습니다. 조직의 루트 관리 그룹에 대한 관리 권한이 있는 사람만 이 작업을 수행할 수 있습니다.

SQL 정보 보호를 위한 정책 관리의 일환으로 사용자 지정 레이블을 정의하고 순위를 지정하고 선택한 정보 유형 집합과 연결할 수 있습니다. 사용자 지정 정보 형식을 추가하고 문자열 패턴으로 구성할 수도 있습니다. 패턴은 데이터베이스에서 이러한 유형의 데이터를 식별하기 위한 검색 논리에 추가됩니다.

[SQL 정보 보호 방법 가이드에서](https://go.microsoft.com/fwlink/?linkid=2009845&clcid=0x409)정책을 사용자 지정하고 관리하는 방법에 대해 자세히 알아봅니다.

조직 전체 정책이 정의된 후에는 사용자 지정된 정책을 사용하여 개별 데이터베이스를 계속 분류할 수 있습니다.

### <a name="classify-your-sql-database"></a>SQL 데이터베이스 분류

1. [Azure 포털](https://portal.azure.com)로 이동합니다.

2. Azure SQL Database 창의 **보안** 제목 아래 **고급 데이터 보안으로** 이동합니다. **고급 데이터 보안을**선택한 다음 데이터 검색 & **분류** 카드를 선택합니다.

   ![Azure 포털의 고급 데이터 보안 창](./media/sql-data-discovery-and-classification/data_classification.png)

3. 데이터 **검색 & 분류** 페이지에서 **개요** 탭에는 데이터베이스의 현재 분류 상태에 대한 요약이 포함됩니다. 요약에는 모든 분류된 열의 자세한 목록이 포함되어 있으며, 필터링하여 특정 스키마 부분, 정보 유형 및 레이블만 표시할 수도 있습니다. 아직 열을 분류하지 않은 경우 [5단계로 건너뜁니다.](#step-5)

   ![현재 분류 상태 요약](./media/sql-data-discovery-and-classification/2_data_classification_overview_dashboard.png)

4. 보고서를 Excel 형식으로 다운로드하려면 창의 상단 메뉴에서 **내보내기를** 선택합니다.

5. <a id="step-5"></a>데이터 분류를 시작하려면 **데이터 검색 & 분류** 페이지에서 **분류** 탭을 선택합니다.

    분류 엔진은 데이터베이스를 검색하여 잠재적으로 중요한 데이터가 포함된 열을 검색하고 권장열 분류 목록을 제공합니다.

6. 분류 권장 사항 보기 및 적용:

   - 권장 열 분류 목록을 보려면 창 하단의 권장 패널을 선택합니다.

   - 특정 열에 대한 권장 사항을 수락하려면 관련 행의 왼쪽 열에 있는 확인란을 선택합니다. 모든 권장 사항을 허용된 것으로 표시하려면 권장 사항 테이블 헤더에서 가장 왼쪽의 확인란을 선택합니다.

       ![분류 권장 사항 목록에서 검토 및 선택](./media/sql-data-discovery-and-classification/6_data_classification_recommendations_list.png)

   - 선택한 권장 사항을 적용하려면 **선택한 권장 사항 수락을**선택합니다.

7. 또한 열을 수동으로 분류하거나 권장 사항 기반 분류 외에 다른 항목으로 분류할 수도 있습니다.

   1. 창의 상단 메뉴에서 **분류 추가를** 선택합니다.

   1. 여는 컨텍스트 창에서 분류할 스키마, 테이블 및 열과 정보 유형 및 민감도 레이블을 선택합니다.

   1. 컨텍스트 창 하단에 **분류 추가를** 선택합니다.

      ![분류할 열을 선택합니다.](./media/sql-data-discovery-and-classification/9_data_classification_manual_classification.png)

8. 분류를 완료하고 새 분류 메타데이터가 있는 데이터베이스 열에 지속적으로 레이블(태그)하려면 창의 상단 메뉴에 **저장을** 선택합니다.

## <a name="audit-access-to-sensitive-data"></a><a id="audit-sensitive-data"></a>중요한 데이터에 대한 액세스 감사

정보 보호 패러다임의 중요한 측면은 중요한 데이터에 대한 액세스를 모니터링하는 기능입니다. [Azure SQL 데이터베이스 감사가](sql-database-auditing.md) 확장되어 감사 로그에 새 `data_sensitivity_information`필드를 포함하도록 합니다. 이 필드는 쿼리에서 반환된 데이터의 민감도 분류(레이블)를 기록합니다. 예를 들면 다음과 같습니다.

![감사 로그](./media/sql-data-discovery-and-classification/11_data_classification_audit_log.png)

## <a name="permissions"></a><a id="permissions"></a>사용 권한

이러한 기본 제공 역할은 Azure SQL 데이터베이스의 데이터 분류를 읽을 수 있습니다.

- 소유자
- 판독기
- 참가자
- SQL 보안 관리자
- 사용자 액세스 관리자

이러한 기본 제공 역할은 Azure SQL 데이터베이스의 데이터 분류를 수정할 수 있습니다.

- 소유자
- 참가자
- SQL 보안 관리자

[Azure 리소스에 대한 RBAC의](https://docs.microsoft.com/azure/role-based-access-control/overview)역할 기반 권한에 대해 자세히 알아봅니다.

## <a name="manage-classifications"></a><a id="manage-classification"></a>분류 관리

T-SQL, REST API 또는 PowerShell을 사용하여 분류를 관리할 수 있습니다.

### <a name="use-t-sql"></a>T-SQL 사용

T-SQL을 사용하여 열 분류를 추가 또는 제거하고 전체 데이터베이스에 대한 모든 분류를 검색할 수 있습니다.

> [!NOTE]
> T-SQL을 사용하여 레이블을 관리하는 경우 조직의 정보 보호 정책(포털 권장 사항에 나타나는 레이블 집합)에 열에 추가하는 레이블이 있는지 유효성 검사가 없습니다. 따라서 이 것의 유효성을 검사하는 것은 당신에게 달려 있습니다.

분류에 T-SQL을 사용하는 것에 대한 자세한 내용은 다음 참조를 참조하십시오.

- 하나 이상의 열의 분류를 추가하거나 업데이트하려면 [감도 분류 추가](https://docs.microsoft.com/sql/t-sql/statements/add-sensitivity-classification-transact-sql)
- 하나 이상의 열에서 분류를 제거하려면 [삭제 감도 분류](https://docs.microsoft.com/sql/t-sql/statements/drop-sensitivity-classification-transact-sql)
- 데이터베이스의 모든 분류를 보려면 [sys.sensitivity_classifications](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-sensitivity-classifications-transact-sql)

### <a name="use-the-rest-api"></a>나머지 API 사용

REST API를 사용하여 분류 및 권장 사항을 프로그래밍 방식으로 관리할 수 있습니다. 게시된 REST API는 다음 작업을 지원합니다.

- [만들기 또는 업데이트](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/createorupdate): 지정된 열의 민감도 레이블을 만들거나 업데이트합니다.
- [삭제](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/delete): 지정된 열의 민감도 레이블을 삭제합니다.
- [권장 사항 사용 안 함](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/disablerecommendation): 지정된 열에서 민감도 권장 사항을 사용하지 않도록 설정합니다.
- [권장 사항 사용](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/enablerecommendation): 지정된 열에서 민감도 권장 사항을 활성화합니다. (권장 사항은 기본적으로 모든 열에서 활성화됩니다.)
- [Get](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/get): 지정된 열의 민감도 레이블을 가져옵니다.
- [데이터베이스별 현재 목록](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listcurrentbydatabase): 지정된 데이터베이스의 현재 민감도 레이블을 가져옵니다.
- [데이터베이스에서 권장하는 목록](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listrecommendedbydatabase): 지정된 데이터베이스의 권장 민감도 레이블을 가져옵니다.

### <a name="use-powershell-cmdlets"></a>PowerShell cmdlet 사용
PowerShell을 사용하여 Azure SQL Database 및 관리되는 인스턴스에 대한 분류 및 권장 사항을 관리할 수 있습니다.

#### <a name="powershell-cmdlets-for-sql-database"></a>SQL 데이터베이스를 위한 PowerShell cmdlet

- [Get-AzSqlDatabase감도분류](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityclassification)
- [세트-아즈Sql데이터베이스감도분류](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasesensitivityclassification)
- [제거-아즈Sql데이터베이스감도분류](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabasesensitivityclassification)
- [받기-아즈Sql데이터베이스감수도추천](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityrecommendation)
- [인에이블 아즈SqlDatabaSesensitivity추천](https://docs.microsoft.com/powershell/module/az.sql/enable-azsqldatabasesensitivityrecommendation)
- [비활성화-아즈Sql데이터베이스감도추천](https://docs.microsoft.com/powershell/module/az.sql/disable-azsqldatabasesensitivityrecommendation)

#### <a name="powershell-cmdlets-for-managed-instances"></a>관리되는 인스턴스용 PowerShell cmdlet

- [받기-아즈Sql인스턴스데이터베이스감도분류](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityclassification)
- [설정-아즈Sql인스턴스데이터베이스감도분류](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasesensitivityclassification)
- [제거-아즈Sql인스턴스데이터베이스감도분류](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstancedatabasesensitivityclassification)
- [받기-아즈Sql인스턴스데이터베이스감도추천](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityrecommendation)
- [인에이블-아즈Sql인스턴스데이터베이스민감권고](https://docs.microsoft.com/powershell/module/az.sql/enable-azsqlinstancedatabasesensitivityrecommendation)
- [사용 안 함-아즈Sql인스턴스데이터베이스감도추천](https://docs.microsoft.com/powershell/module/az.sql/disable-azsqlinstancedatabasesensitivityrecommendation)


## <a name="next-steps"></a><a id="next-steps"></a>다음 단계

- [고급 데이터 보안에](sql-database-advanced-data-security.md)대해 자세히 알아보십시오.
- 분류된 중요한 데이터에 대한 액세스를 모니터링 및 감사하기 위해 [Azure SQL Database 감사](sql-database-auditing.md)를 구성하는 것이 좋습니다.
- 데이터 검색 및 분류가 포함된 프레젠테이션의 경우 [SQL 데이터 보호 & 검색, 분류, 레이블 지정 을 참조하십시오 | 데이터 노출](https://www.youtube.com/watch?v=itVi9bkJUNc).
