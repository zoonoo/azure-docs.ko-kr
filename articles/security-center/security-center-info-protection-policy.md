---
title: Azure Security Center의 SQL information protection 정책
description: Azure Security Center에서 정보 보호 정책을 사용자 지정하는 방법을 알아봅니다.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 2ebf2bc7-232a-45c4-a06a-b3d32aaf2500
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2020
ms.author: memildin
ms.openlocfilehash: 0a487f778693e87e680033edd0d80c55d1a85f66
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93348630"
---
# <a name="sql-information-protection-policy-in-azure-security-center"></a>Azure Security Center의 SQL information protection 정책
 
SQL information protection의 [데이터 검색 및 분류 메커니즘](../azure-sql/database/data-discovery-and-classification-overview.md) 은 데이터베이스에서 중요 한 데이터를 검색, 분류, 레이블 지정 및 보고 하기 위한 고급 기능을 제공 합니다. [Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md), [azure SQL Managed Instance](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)및 [azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)에 기본 제공 됩니다.

분류 메커니즘은 다음 두 가지 요소를 기반으로 합니다.

- **레이블** – 열에 저장 된 *데이터의 민감도 수준을* 정의 하는 데 사용 되는 기본 분류 특성입니다. 
- **정보 유형** – 열에 저장 된 *데이터의 유형에 대* 한 추가 세분성을 제공 합니다.

Security Center 내의 information protection 정책 옵션은 분류 엔진의 기본값으로 사용 되는 미리 정의 된 레이블 집합 및 정보 유형을 제공 합니다. 아래에 설명 된 대로 조직의 요구 사항에 따라 정책을 사용자 지정할 수 있습니다.

> [!IMPORTANT]
> Azure 테 넌 트에 대 한 정보 보호 정책을 사용자 지정 하려면 테 넌 트의 루트 관리 그룹에 대 한 관리 권한이 필요 합니다. 자세한 내용은 [Azure Security Center에 대 한 테 넌 트 전체 가시성을 활용](security-center-management-groups.md)하세요.

:::image type="content" source="./media/security-center-info-protection-policy/sql-information-protection-policy-page.png" alt-text="SQL information protection 정책을 표시 하는 페이지":::
 



## <a name="how-do-i-access-the-sql-information-protection-policy"></a>SQL information protection 정책에 액세스 어떻게 할까요??

Information protection 정책에 액세스 하는 방법에는 다음 세 가지가 있습니다.

- **(권장)** Security Center의 가격 책정 및 설정 페이지에서
- 보안 권장 사항에서 "SQL 데이터베이스의 중요 한 데이터를 분류 해야 합니다."
- Azure SQL DB 데이터 검색 페이지에서

각각의 관련 탭에는 아래와 같은 관련 탭이 표시 됩니다.



### <a name="from-security-centers-settings"></a>[**Security Center 설정에서**](#tab/sqlip-tenant)

### <a name="access-the-policy-from-security-centers-pricing-and-settings-page"></a>Security Center의 가격 책정 및 설정 페이지에서 정책에 액세스 합니다. <a name="sqlip-tenant"></a>

Security Center의 **가격 책정 및 설정** 페이지에서 **SQL information protection** 을 선택 합니다.

> [!NOTE]
> 이 옵션은 테 넌 트 수준의 사용 권한이 있는 사용자에 대해서만 표시 됩니다. 

:::image type="content" source="./media/security-center-info-protection-policy/pricing-settings-link-to-information-protection.png" alt-text="Azure Security Center의 가격 책정 및 설정 페이지에서 SQL Information Protection 정책에 액세스":::



### <a name="from-security-centers-recommendation"></a>[**Security Center 권장 사항**](#tab/sqlip-db)

### <a name="access-the-policy-from-the-security-center-recommendation"></a>Security Center 권장 사항에서 정책 액세스 <a name="sqlip-db"></a>

데이터베이스에 대 한 데이터 검색 및 분류 페이지를 보려면 Security Center의 권장 사항 인 "SQL 데이터베이스의 중요 한 데이터를 분류 해야 합니다."를 사용 합니다. 또한 분류 하는 데 권장 되는 정보를 포함 하는 검색 된 열도 표시 됩니다.

1. Security Center의 **권장 사항** 페이지에서 **SQL 데이터베이스의 중요 한 데이터** 를 검색 하 여 분류 해야 합니다.

    :::image type="content" source="./media/security-center-info-protection-policy/sql-sensitive-data-recommendation.png" alt-text="SQL information protection 정책에 대 한 액세스를 제공 하는 권장 사항 찾기":::

1. 권장 사항 세부 정보 페이지의 **정상** 또는 **비정상** 탭에서 데이터베이스를 선택 합니다.

1. **데이터 검색 & 분류** 페이지가 열립니다. **구성** 을 선택합니다.

    :::image type="content" source="./media/security-center-info-protection-policy/access-policy-from-security-center-recommendation.png" alt-text="Azure Security Center의 관련 권장 사항에서 SQL information protection 정책 열기":::



### <a name="from-azure-sql"></a>[**Azure SQL에서**](#tab/sqlip-azuresql)

### <a name="access-the-policy-from-azure-sql"></a>Azure SQL에서 정책 액세스 <a name="sqlip-azuresql"></a>

1. Azure Portal에서 Azure SQL을 엽니다.

    :::image type="content" source="./media/security-center-info-protection-policy/open-azure-sql.png" alt-text="Azure Portal에서 Azure SQL 열기":::

1. 데이터베이스를 선택 합니다.

1. 메뉴의 **보안** 영역에서 **데이터 검색 & 분류** 페이지 (1)를 열고 **구성** (2)을 선택 합니다.

    :::image type="content" source="./media/security-center-info-protection-policy/access-policy-from-azure-sql.png" alt-text="Azure SQL에서 SQL information protection 정책 열기":::

--- 


## <a name="customize-your-information-types"></a>정보 형식 사용자 지정

정보 유형을 관리 하 고 사용자 지정 하려면:

1. **정보 유형 관리** 를 선택 합니다.

    :::image type="content" source="./media/security-center-info-protection-policy/manage-types.png" alt-text="Information protection 정책에 대 한 정보 형식 관리":::

1. 새 형식을 추가 하려면 **정보 유형 만들기** 를 선택 합니다. 정보 형식에 대 한 이름, 설명 및 검색 패턴 문자열을 구성할 수 있습니다. 필요에 따라 검색 패턴 문자열은 열의 메타데이터에 따라 와일드 카드 문자('%' 문자 사용)와 함께 키워드를 사용할 수 있습니다. 이 항목은 자동화된 검색 엔진이 데이터베이스에서 중요한 데이터를 식별하는 데 사용합니다.
 
    :::image type="content" source="./media/security-center-info-protection-policy/configure-new-type.png" alt-text="Information protection 정책에 대 한 새 정보 유형 구성":::

1. 추가 검색 패턴 문자열을 추가 하거나, 기존 문자열 중 일부를 사용 하지 않도록 설정 하거나, 설명을 변경 하 여 기본 제공 형식을 수정할 수도 있습니다. 

    > [!TIP]
    > 기본 제공 형식을 삭제 하거나 이름을 변경할 수 없습니다. 

1. **정보 형식** 은 검색 순위 오름차순으로 나열됩니다. 즉, 목록의 위에 있는 형식을 먼저 일치시킵니다. 정보 형식 간의 순위를 변경하려면 형식을 테이블의 오른쪽으로 끌어오거나 **위로 이동** 및 **아래로 이동** 단추를 사용하여 순서를 변경합니다. 

1. 작업이 완료 되 면 **확인을** 선택 합니다.

1. 정보 형식 관리를 완료하면 특정 레이블에 대한 **구성** 을 클릭하고, 적절하게 정보 형식을 추가하거나 삭제하여 적절한 레이블과 관련 형식을 연결하도록 합니다.

1. 변경 내용을 적용 하려면 기본 **레이블** 페이지에서 **저장** 을 선택 합니다.
 

## <a name="exporting-and-importing-a-policy"></a>정책 내보내기 및 가져오기

정의 된 레이블 및 정보 유형을 사용 하 여 JSON 파일을 다운로드 하 고 선택한 편집기에서 파일을 편집한 다음 업데이트 된 파일을 가져올 수 있습니다. 

:::image type="content" source="./media/security-center-info-protection-policy/export-import.png" alt-text="Information protection 정책 내보내기 및 가져오기":::

> [!NOTE]
> 정책 파일을 가져오려면 테 넌 트 수준 권한이 필요 합니다. 


## <a name="manage-sql-information-protection-using-azure-powershell"></a>Azure PowerShell를 사용 하 여 SQL information protection 관리

- [AzSqlInformationProtectionPolicy](/powershell/module/az.security/get-azsqlinformationprotectionpolicy): 유효한 테 넌 트 SQL information protection 정책을 검색 합니다.
- [AzSqlInformationProtectionPolicy](/powershell/module/az.security/set-azsqlinformationprotectionpolicy): 유효한 테 넌 트 SQL information protection 정책을 설정 합니다.
 

## <a name="next-steps"></a>다음 단계
 
이 문서에서는 Azure Security Center에서 정보 보호 정책을 정의 하는 방법을 배웠습니다. SQL Information Protection을 사용하여 SQL 데이터베이스에서 중요한 데이터를 분류하고 보호하는 방법에 대해 자세히 알아보려면 [Azure SQL Database 데이터 검색 및 분류](../azure-sql/database/data-discovery-and-classification-overview.md)를 참조하세요.

Security Center의 보안 정책 및 데이터 보안에 대 한 자세한 내용은 다음 문서를 참조 하세요.
 
- [Azure Security Center에서 보안 정책 설정](tutorial-security-policy.md): Azure 구독 및 리소스 그룹에 대해 보안 정책을 구성하는 방법을 알아봅니다.
- [Azure Security Center 데이터 보안](security-center-data-security.md): Security Center에서 데이터를 관리하고 보호하는 방법을 알아봅니다.
