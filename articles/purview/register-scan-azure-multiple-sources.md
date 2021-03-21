---
title: Azure에서 여러 소스를 검색 하는 방법
description: Azure 부서의 범위 data catalog에서 전체 Azure 구독 또는 리소스 그룹을 검색 하는 방법을 알아봅니다.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 2/26/2021
ms.openlocfilehash: 098f62365971fd634001706ab99fd414a6b25056
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102123567"
---
# <a name="register-and-scan-azure-multiple-sources"></a>Azure 여러 소스 등록 및 검색

이 문서에서는 부서의 범위에서 Azure 여러 원본 (Azure 구독 또는 리소스 그룹)을 등록 하 고 검색을 설정 하는 방법을 설명 합니다.

## <a name="supported-capabilities"></a>지원되는 기능

Azure 다중 소스는 부서의 범위에서 지 원하는 대부분의 Azure 리소스 유형에 대 한 메타 데이터 및 스키마를 캡처하기 위한 검색을 지원 합니다. 또한 시스템 및 사용자 지정 분류 규칙을 기반으로 데이터를 자동으로 분류 합니다.

## <a name="prerequisites"></a>필수 구성 요소

- 데이터 원본을 등록 하기 전에 Azure 부서의 범위 계정을 만듭니다. 부서의 범위 계정을 만드는 방법에 대 한 자세한 내용은 [빠른 시작: Azure 부서의 범위 계정 만들기](create-catalog-portal.md)를 참조 하세요.
- Azure 부서의 범위 데이터 원본 관리자 여야 합니다.
- 아래 섹션에 설명 된 대로 인증 설정

### <a name="setting-up-authentication-for-enumerating-resources-under-a-subscription-or-resource-group"></a>구독 또는 리소스 그룹에서 리소스를 열거 하기 위한 인증 설정

1. Azure Portal에서 구독 또는 리소스 그룹으로 이동 합니다.  
1. 왼쪽 탐색 메뉴에서 **Access Control (IAM)** 을 선택   합니다. 
1. 구독 또는 리소스 그룹에 역할을 추가 하려면 소유자 또는 사용자 액세스 관리자 여야 합니다. *+ 추가* 단추를 선택 합니다. 
1. **읽기** 상자 역할을 설정 하 고 입력 상자 선택 상자에 Azure 부서의 범위 계정 이름 (해당 MSI를 나타냄)을 입력 합니다. *저장* 을 클릭 하 여 역할 할당을 완료 합니다.

### <a name="setting-up-authentication-to-scan-resources-under-a-subscription-or-resource-group"></a>구독 또는 리소스 그룹에서 리소스를 검색 하는 인증 설정

Azure 다중 소스에 대 한 인증을 설정 하는 방법에는 다음 두 가지가 있습니다.

- 관리 ID
- 서비스 주체

> [!NOTE]
> 등록 하 고 검색 하려는 구독 또는 리소스 그룹 내의 각 리소스에 대해 인증을 설정 해야 합니다. Azure storage 리소스 유형 (Azure blob storage 및 Azure Data Lake Storage Gen2)을 통해 구독/리소스 그룹 수준에서 MSI 또는 서비스 주체를 storage blob 데이터 판독기로 쉽게 추가할 수 있습니다. 그러면 해당 구독 또는 리소스 그룹 내에서 각 저장소 계정으로 trickle 됩니다. 다른 모든 리소스 종류의 경우에는 각 리소스에 MSI 또는 서비스 주체를 적용 하거나,이 작업을 수행 하는 스크립트를 장치에 적용 해야 합니다. 구독 또는 리소스 그룹 내에서 각 리소스 유형에 대 한 사용 권한을 추가 하는 방법은 다음과 같습니다.
    
- [Azure Blob Storage](register-scan-azure-blob-storage-source.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen1](register-scan-adls-gen1.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen2](register-scan-adls-gen2.md#setting-up-authentication-for-a-scan)
- [Azure SQL Database](register-scan-azure-sql-database.md)
- [Azure SQL Database Managed Instance](register-scan-azure-sql-database-managed-instance.md#setting-up-authentication-for-a-scan)
- [Azure Synapse Analytics](register-scan-azure-synapse-analytics.md#setting-up-authentication-for-a-scan)
 
## <a name="register-an-azure-multiple-source"></a>Azure 여러 소스 등록

데이터 카탈로그에 새 Azure 여러 원본을 등록 하려면 다음을 수행 합니다.

1. Purview 계정으로 이동합니다.
1. 왼쪽 탐색 영역에서 **원본** 을 선택합니다.
1. **등록** 을 선택합니다.
1. **소스 등록** 에서 **Azure (여러 개)** 를 선택 합니다.
1. **계속** 을 선택합니다.

   :::image type="content" source="media/register-scan-azure-multiple-sources/register-azure-multiple.png" alt-text="Azure 여러 소스 등록":::

**원본 등록 (Azure 여러)** 화면에서 다음을 수행 합니다.

1. 카탈로그에서 데이터 원본이 나열 될 **이름을** 입력 합니다. 
1. 선택적으로 필터링 할 **관리 그룹** 을 선택 합니다.
1. 드롭다운에서 지정 된 구독 아래에 있는 **특정 리소스 그룹 또는 구독을 선택** 합니다. 등록 범위는 선택 된 구독 또는 리소스 그룹으로 설정 됩니다.  
1. 컬렉션을 선택 하거나 새 **컬렉션** 을 만듭니다 (선택 사항).
1. 데이터 원본 등록을 **완료** 합니다.

   :::image type="content" source="media/register-scan-azure-multiple-sources/azure-multiple-source-setup.png" alt-text="Azure 다중 원본 설정":::

## <a name="creating-and-running-a-scan"></a>검사 만들기 및 실행

새 검색을 만들고 실행하려면 다음을 수행합니다.

1. **소스** 섹션으로 이동 합니다.

1. 등록 한 데이터 원본 선택

1. 세부 정보 보기를 클릭 하 고 **+ 새 검색** 을 선택 하거나 소스 타일에서 빠른 작업 검색 아이콘을 사용 합니다.

1. *이름을* 입력 하 고이 원본 내에서 검색할 리소스의 모든 형식을 선택 합니다.

    1. *모두* 그대로 둘 수 있습니다. 여기에는 구독 또는 리소스 그룹에 현재 존재 하지 않을 수 있는 향후 리소스 종류가 포함 되어 있습니다.
    1. 검색 하려는 **리소스 종류를 구체적으로 선택할** 수 있습니다. 이 옵션을 선택 하는 경우 나중에 검색을 명시적으로 편집 하지 않는 한이 구독 또는 리소스 그룹 내에서 만들 수 있는 향후 리소스 종류가 검색에 포함 되지 않습니다.
    
    :::image type="content" source="media/register-scan-azure-multiple-sources/multiple-source-scan.png" alt-text="Azure 다중 원본 검색":::

1. 데이터 원본 내의 리소스에 연결할 자격 증명을 선택 합니다. 
    1. **부모 수준** 에서 MSI 또는 특정 서비스 주체 유형 자격 증명을 선택할 수 있습니다 .이 자격 증명은 구독 또는 리소스 그룹의 모든 리소스 유형에 사용 하도록 선택할 수 있습니다.
    1. 특정 리소스 종류를 선택 하 고 해당 리소스 종류에 대해 **다른 자격 증명을 적용할** 수도 있습니다.
    1. 각 자격 증명은 특정 유형 아래의 모든 리소스에 대 한 인증 방법으로 간주 됩니다.
    1. 위의이 [섹션](#setting-up-authentication-to-scan-resources-under-a-subscription-or-resource-group) 에 설명 된 대로 성공적으로 검색 하려면 리소스에 대해 선택한 자격 증명을 설정 해야 합니다.
1. 각 형식 내에서 이름으로 모든 리소스 또는 하위 집합을 검색 하도록 선택할 수 있습니다.
    1. 이 **옵션을 그대로** 두면 이후 검색 실행 에서도 해당 형식의 이후 리소스가 검색 됩니다.
    1. 특정 저장소 계정 또는 SQL 데이터베이스를 선택 하는 경우 나중에 검색이 명시적으로 편집 되지 않는 한이 구독 또는 리소스 그룹 내에서 해당 형식으로 만든 이후 리소스가 검색에 포함 되지 않습니다.
 
1.  계속하려면 **계속** 을 클릭합니다. 구독 또는 리소스 그룹에 대 한 판독기로 부서의 범위 MSI를 적용 했는지 확인 하기 위한 액세스를 테스트 합니다. 오류 메시지가 발생 한 경우 [여기](#setting-up-authentication-for-enumerating-resources-under-a-subscription-or-resource-group) 에 있는 지침을 따르세요.

1.  이전 단계에서 선택한 각 리소스 종류에 대 한 **검사 규칙 집합** 을 선택 합니다. 검색 규칙 집합을 인라인으로 만들 수도 있습니다.
  :::image type="content" source="media/register-scan-azure-multiple-sources/multiple-scan-rule-set.png" alt-text="Azure 다중 검색 규칙 집합 선택":::

1. 검사 트리거를 선택합니다. **주별/월별** 또는 **한 번** 실행 되도록 예약할 수 있습니다.

1. 검색을 검토 하 고 저장을 선택 하 여 설치를 완료 합니다.   

## <a name="viewing-your-scans-and-scan-runs"></a>검사 및 검사 실행 보기

1. 원본 섹션 아래의 타일에서 **세부 정보 보기** 를 클릭 하 여 원본 세부 정보를 봅니다. 

      :::image type="content" source="media/register-scan-azure-multiple-sources/multiple-source-detail.png" alt-text="Azure 다중 원본 세부 정보"::: 

1. 검색 **세부** 정보 페이지로 이동 하 여 검색 실행 세부 정보를 확인 합니다.
    1. *상태 표시줄* 은 자식 리소스의 실행 상태에 대 한 간략 한 요약입니다. 구독 또는 리소스 그룹 수준에 표시 됩니다.
    1. 녹색은 성공 했음을 의미 하 고 빨간색은 실패 했음을 의미 합니다. 회색은 검색을 계속 진행 하 고 있음을 의미 합니다.
    1. 각 검색을 클릭 하 여 보다 세분화 되는 세부 정보를 볼 수 있습니다.

      :::image type="content" source="media/register-scan-azure-multiple-sources/multiple-scan-full-details.png" alt-text="Azure 다중 검색 세부 정보":::

1. 원본 세부 정보 페이지의 아래쪽에서 최근에 실패 한 검색 실행에 대 한 요약 정보를 표시 합니다. 또한 클릭 하 여 이러한 실행과 관련 된 보다 세부적인 세부 정보를 볼 수 있습니다.

## <a name="manage-your-scans---edit-delete-or-cancel"></a>검사 관리 - 편집, 삭제 또는 취소
검사를 관리하거나 삭제하려면 다음을 수행합니다.

- 관리 센터로 이동합니다. 원본 및 검색 섹션에서 데이터 원본을 선택 하 고 원하는 데이터 원본을 선택 합니다.

- 관리하려는 검사를 선택합니다. 편집을 선택 하 여 검색을 편집할 수 있습니다.

- 삭제를 선택 하 여 검색을 삭제할 수 있습니다.
- 검색을 실행 하는 경우에도 취소할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Purview 데이터 카탈로그 찾아보기](how-to-browse-catalog.md)
- [Azure Purview Data Catalog 검색](how-to-search-catalog.md)    
