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
ms.date: 02/05/2020
tags: azure-synapse
ms.openlocfilehash: eb4e7907c3dcffed035307c2084160ce6051be13
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409952"
---
# <a name="data-discovery--classification-for-azure-sql-database-and-azure-synapse-analytics"></a>Azure SQL 데이터베이스 및 Azure 시냅스 분석을 위한 데이터 검색 & 분류

데이터 검색 & 분류는 데이터베이스의 중요한 데이터를**보고하는** **검색,** **분류,** **레이블 지정을** & 위해 Azure SQL Database에 기본 제공된 고급 기능을 제공합니다.

가장 중요한 데이터(비즈니스, 재무, 의료, PII(개인 식별 데이터) 등)를 검색하고 분류하는 기능은 조직 정보 보호 평판에 중추적인 역할을 할 수 있습니다. 다음에 대한 인프라를 제공할 수 있습니다.

- 데이터 프라이버시 표준 및 규정 준수 요구 사항을 충족하도록 지원.
- 중요한 데이터에 대한 비정상적인 엑세스 모니터링(감사) 및 경고하는 것과 같은 다양한 보안 시나리오.
- 매우 중요한 데이터가 들어 있는 데이터베이스에 대한 엑세스 제어 및 보안 강화.

데이터 검색 & 분류는 고급 SQL 보안 기능을 위한 통합 패키지인 [ADS(고급 데이터 보안)](sql-database-advanced-data-security.md) 서비스의 일부입니다. 중앙 SQL ADS 포털을 통해 데이터 검색 및 분류에 액세스하고 데이터 검색 및 분류를 관리할 수 있습니다.

> [!NOTE]
> 이 문서는 Azure SQL 데이터베이스 및 Azure 시냅스와 관련이 있습니다. 간단히 하기 위해 SQL 데이터베이스는 SQL 데이터베이스와 Azure Synapse를 모두 참조할 때 사용됩니다. SQL Server(온프레미스)의 경우 [SQL 데이터 검색 및 분류](https://go.microsoft.com/fwlink/?linkid=866999)를 참조하십시오.

## <a name="what-is-data-discovery--classification"></a><a id="subheading-1"></a>데이터 검색 및 분류란?

데이터 검색 및 분류는 고급 서비스 및 새로운 SQL 기능 집합을 도입하여 데이터베이스 뿐 아니라 데이터를 보호 대상으로 하는 새로운 SQL Information Protection 패러다임을 형성합니다.

- **검색 및 권장 사항**

  분류 엔진은 데이터베이스를 스캔하여 잠재적으로 중요한 데이터가 포함된 열을 식별합니다. 그런 다음 Azure Portal을 통해 적절한 분류 권장 사항을 쉽게 검토하고 적용하는 방법을 제공합니다.

- **레이블 지정**

  민감도 분류 레이블은 SQL Engine에 도입된 새로운 분류 메타데이터 특성을 사용하여 열에 영구적으로 태그할 수 있습니다. 그런 후에 이 메타데이터는 고급 민감도 기반 감사 및 보호 시나리오에 사용될 수 있습니다.

- **쿼리 결과 집합 민감도**

  쿼리 결과 집합의 민감도는 감사 목적으로 실시간 계산됩니다.

- **표시 유형**

  데이터베이스 분류 상태는 포털의 자세한 대시보드에서 볼 수 있습니다. 또한 규정 준수 및 감사 목적은 물론 다른 요구 사항에도 사용하도록 보고서(Excel 형식)를 다운로드할 수 있습니다.

## <a name="discover-classify--label-sensitive-columns"></a><a id="subheading-2"></a>중요한 열 검색, 분류 및 레이블 지정

다음 섹션에서는 데이터베이스에서 중요한 데이터를 포함하는 열을 검색, 분류, 및 레이블을 지정할 뿐만 아니라 데이터베이스 및 내보내는 보고서의 현재 분류 상태를 보는 단계를 설명합니다.

분류에는 두 개의 메타데이터 특성이 포함됩니다.

- 레이블 - 주 분류 속성은 열에 저장된 데이터의 민감도 수준을 정의하는 데 사용합니다.  
- 정보 형식 – 열에 저장된 데이터의 형식에 대한 추가 세분성을 제공합니다.

## <a name="define-and-customize-your-classification-taxonomy"></a>분류 체계 정의 및 사용자 지정

데이터 검색 & 분류에는 감도 레이블의 기본 제공 집합과 정보 유형 및 검색 논리가 내장되어 있습니다. 이제 환경에 맞게 이 분류 체계를 사용자 지정하고 분류 구조의 집합 및 순위 지정 방법을 정의해야 합니다.

분류 체계를 정의하고 사용자 지정하는 작업은 전체 Azure 테넌트에 대한 중앙의 한 위치에서 수행됩니다. 이 위치는 보안 정책에 따라 [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) 내에 있습니다. 테넌트 루트 관리 그룹에 대한 관리 권한이 있는 사람만이 이 작업을 수행할 수 있습니다.

Information Protection 정책 관리의 일환으로, 사용자 지정 레이블을 정의하고, 순위를 지정하고, 선택한 정보 유형 집합과 연결할 수 있습니다. 또한 문자열 패턴을 사용하여 사용자 고유의 사용자 지정 정보 유형을 추가하고 구성할 수 있으며, 이러한 정보 유형은 데이터베이스에서 이러한 유형의 데이터를 식별하기 위한 검색 논리에 추가됩니다.
정책을 사용자 지정하고 관리하는 방법에 대한 자세한 내용은 [Information Protection 정책 방법 가이드](https://go.microsoft.com/fwlink/?linkid=2009845&clcid=0x409)를 참조하세요.

테넌트 수준 정책을 정의한 후에는 사용자 지정된 정책을 사용하여 개별 데이터베이스 분류를 계속할 수 있습니다.

## <a name="classify-your-sql-database"></a>SQL Database 분류

1. [Azure 포털로](https://portal.azure.com)이동합니다.

2. Azure SQL Database 창의 보안 머리글 아래에 있는 **Advanced Data Security**로 이동합니다. 고급 데이터 보안을 활성화하려면 클릭한 다음 **데이터 검색 & 분류** 카드를 클릭합니다.

   ![데이터베이스 검색](./media/sql-data-discovery-and-classification/data_classification.png)

3. **개요** 탭은 특정 스키마 부분, 정보 유형 및 레이블만을 보도록 또한 필터링할 수 있는 모든 분류된 열의 상세 목록을 포함하여, 데이터베이스의 현재 분류 상태 요약을 포함합니다. 아직 어떠한 열도 분류하지 않았다면 [5 단계로 건너뜁니다](#step-5).

   ![현재 분류 상태 요약](./media/sql-data-discovery-and-classification/2_data_classification_overview_dashboard.png)

4. 보고서를 Excel 형식으로 다운로드하려면 창의 위쪽 메뉴에서 **내보내기** 옵션을 클릭합니다.

   ![Excel로 내보내기](./media/sql-data-discovery-and-classification/3_data_classification_export_report.png)

5. <a id="step-5"></a>데이터 분류를 시작하려면 창의 위쪽에 있는 **분류 탭**을 클릭합니다.

    ![데이터 분류](./media/sql-data-discovery-and-classification/4_data_classification_classification_tab_click.png)

6. 분류 엔진은 데이터베이스를 검색하여 잠재적으로 중요한 데이터가 포함된 열을 검색하고 **권장열 분류**목록을 제공합니다. 분류 권장 사항 보기 및 적용하기

   - 권장된 열 분류 목록을 보려면 창의 맨 아래에서 권장 사항 패널을 클릭합니다.

      ![데이터 분류](./media/sql-data-discovery-and-classification/5_data_classification_recommendations_panel.png)

   - 권장 사항 목록을 검토합니다. 특정 열에 대한 권장 사항을 받아 들이려면 관련 행의 왼쪽 열에 있는 확인란을 선택합니다. 권장 사항 테이블 헤더에서 확인란을 선택하여 *모든 권장 사항*을 수락됨으로 표시할 수도 있습니다.

       ![권장 사항 목록 검토](./media/sql-data-discovery-and-classification/6_data_classification_recommendations_list.png)

   - 선택한 권장 사항을 적용하려면 파란색 **선택한 권장 사항 허용** 단추를 클릭합니다.

      ![권장 사항 적용](./media/sql-data-discovery-and-classification/7_data_classification_accept_selected_recommendations.png)

7. 대안으로 열을 **수동으로 분류**하거나 권장 사항 기반 분류로 지정할 수도 있습니다.

   - 창의 위쪽 메뉴에서 **분류 추가**를 클릭합니다.

      ![수동으로 분류 추가](./media/sql-data-discovery-and-classification/8_data_classification_add_classification_button.png)

   - 열려 있는 컨텍스트 창에서 분류하려는 스키마 > 테이블 > 열을 선택하고 정보 형식 및 민감도 레이블을 선택합니다. 컨텍스트 창의 아래쪽에 있는 파란색 **분류 추가** 단추를 클릭합니다.

      ![분류할 열 선택](./media/sql-data-discovery-and-classification/9_data_classification_manual_classification.png)

8. 새 분류 메타데이터를 사용하여 분류를 완료하고 영구적으로 데이터베이스 열의 레이블(태그)을 지정하려면 창의 최상위 메뉴에서 **저장**을 클릭합니다.

   ![저장](./media/sql-data-discovery-and-classification/10_data_classification_save.png)

## <a name="auditing-access-to-sensitive-data"></a><a id="subheading-3"></a>중요한 데이터에 대한 액세스 감사

정보 보호 패러다임의 중요한 측면은 중요한 데이터에 대한 액세스를 모니터링하는 기능입니다. [Azure SQL Database 감사](sql-database-auditing.md)는 *data_sensitivity_information*이라는 감사 로그에 새 필드를 포함하도록 개선되어, 쿼리에서 반환된 실제 데이터의 민감도 분류(레이블)를 기록합니다.

![감사 로그](./media/sql-data-discovery-and-classification/11_data_classification_audit_log.png)

## <a name="permissions"></a><a id="subheading-4"></a>권한을

다음 기본 제공 역할은 Azure SQL 데이터베이스의 데이터 분류를 `Contributor` `SQL Security Manager` 읽을 `User Access Administrator`수 `Owner`있습니다. `Reader`

다음 기본 제공 역할은 Azure SQL 데이터베이스의 데이터 `Owner`분류를 `SQL Security Manager`수정할 수 있습니다. `Contributor`

[Azure 리소스에 대한 RBAC에 대해](https://docs.microsoft.com/azure/role-based-access-control/overview) 자세히 알아보기

## <a name="manage-classifications"></a><a id="subheading-5"></a>분류 관리

# <a name="t-sql"></a>[T-SQL](#tab/azure-t-sql)
T-SQL을 사용하여 열 분류를 추가/제거하고 전체 데이터베이스에 대한 모든 분류를 검색할 수 있습니다.

> [!NOTE]
> T-SQL을 사용하여 레이블을 관리하는 경우, 열에 추가된 레이블이 조직 정보 보호 정책(포털 권장 사항에 표시되는 레이블 집합)에 있는지 확인되지 않습니다. 따라서 이 확인 작업은 사용자가 수행해야 합니다.

- 하나 이상의 열 분류 추가/업데이트: [ADD SENSITIVITY CLASSIFICATION](https://docs.microsoft.com/sql/t-sql/statements/add-sensitivity-classification-transact-sql)
- 하나 이상의 열에서 분류 제거: [DROP SENSITIVITY CLASSIFICATION](https://docs.microsoft.com/sql/t-sql/statements/drop-sensitivity-classification-transact-sql)
- 데이터베이스에 대한 모든 분류 보기: [sys.sensitivity_classifications](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-sensitivity-classifications-transact-sql)

# <a name="rest-apis"></a>[나머지 API](#tab/azure-rest-api)
REST API를 사용하여 분류 및 권장 사항을 프로그래밍 방식으로 관리할 수 있습니다. 게시된 REST API는 다음과 같은 작업을 지원합니다.

- [만들기 또는 업데이트](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/createorupdate) - 지정된 열의 민감도 레이블을 만들거나 업데이트합니다.
- [삭제](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/delete) - 지정된 열의 민감도 레이블을 삭제합니다.
- [권장 사항 사용 안 함](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/disablerecommendation) - 지정된 열에서 민감도 권장 사항을 사용하지 않도록 설정합니다.
- [권장 사항 사용](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/enablerecommendation) - 지정된 열에 대한 민감도 권장 사항 사용(모든 열에서 권장 사항이 기본적으로 활성화).
- [가져오기](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/get) - 지정된 열의 민감도 레이블을 가져옵니다.
- [데이터베이스별 최신 목록](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listcurrentbydatabase) - 지정된 데이터베이스의 최신 민감도 레이블을 가져옵니다.
- [데이터베이스에서 권장하는 목록](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listrecommendedbydatabase) - 지정된 데이터베이스의 권장 감도 레이블을 가져옵니다.

# <a name="powershell-cmdlet"></a>[PowerShell Cmdlet](#tab/azure-powelshell)
PowerShell을 사용하여 Azure SQL 데이터베이스 및 관리되는 인스턴스에 대한 분류 및 권장 사항을 관리할 수 있습니다.

### <a name="powershell-cmdlet-for-azure-sql-database"></a>Azure SQL 데이터베이스에 대한 PowerShell Cmdlet
- [Get-AzSqlDatabase감도분류](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityclassification)
- [세트-아즈Sql데이터베이스감도분류](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasesensitivityclassification)
- [제거-아즈Sql데이터베이스감도분류](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabasesensitivityclassification)
- [받기-아즈Sql데이터베이스감수도추천](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityrecommendation)
- [인에이블 아즈SqlDatabaSesensitivity추천](https://docs.microsoft.com/powershell/module/az.sql/enable-azsqldatabasesensitivityrecommendation)
- [비활성화-아즈Sql데이터베이스감도추천](https://docs.microsoft.com/powershell/module/az.sql/disable-azsqldatabasesensitivityrecommendation)

### <a name="powershell-cmdlets-for-managed-instance"></a>관리되는 인스턴스용 PowerShell Cmdlet
- [받기-아즈Sql인스턴스데이터베이스감도분류](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityclassification)
- [설정-아즈Sql인스턴스데이터베이스감도분류](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasesensitivityclassification)
- [제거-아즈Sql인스턴스데이터베이스감도분류](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstancedatabasesensitivityclassification)
- [받기-아즈Sql인스턴스데이터베이스감도추천](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityrecommendation)
- [인에이블-아즈Sql인스턴스데이터베이스민감권고](https://docs.microsoft.com/powershell/module/az.sql/enable-azsqlinstancedatabasesensitivityrecommendation)
- [사용 안 함-아즈Sql인스턴스데이터베이스감도추천](https://docs.microsoft.com/powershell/module/az.sql/disable-azsqlinstancedatabasesensitivityrecommendation)

---

## <a name="next-steps"></a><a id="subheading-6"></a>다음 단계

- [고급 데이터 보안](sql-database-advanced-data-security.md)에 대해 자세히 알아봅니다.
- 분류된 중요한 데이터에 대한 액세스를 모니터링 및 감사하기 위해 [Azure SQL Database 감사](sql-database-auditing.md)를 구성하는 것이 좋습니다.
- 데이터 검색 & 분류가 포함된 YouTube 프레젠테이션의 경우 [SQL 데이터 보호 & 검색, 분류, 레이블 지정 을 참조하십시오 | 데이터 노출](https://www.youtube.com/watch?v=itVi9bkJUNc).

<!--Anchors-->
[What is data discovery & classification]: #subheading-1
[Discovering, classifying & labeling sensitive columns]: #subheading-2
[Auditing access to sensitive data]: #subheading-3
[Permissions]: #subheading-4
[Manage classifications]: #subheading-5
[Next Steps]: #subheading-6
