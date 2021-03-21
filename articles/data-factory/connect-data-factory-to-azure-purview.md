---
title: Azure Purview에 Data Factory 연결
description: Azure 부서의 범위에 Data Factory를 연결 하는 방법에 대해 알아봅니다.
ms.author: lle
author: lrtoyou1223
ms.service: data-factory
ms.topic: conceptual
ms.custom:
- seo-lt-2019
- references_regions
ms.date: 12/3/2020
ms.openlocfilehash: 44f093f96d0f4653a6fcca94aaa97264c93e3c7d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101727943"
---
# <a name="connect-data-factory-to-azure-purview-preview"></a>Azure 부서의 범위에 Data Factory 연결 (미리 보기)
[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

이 문서에서는 Data Factory를 Azure 부서의 범위에 연결 하는 방법 및 Azure Data Factory 활동의 데이터 계보 데이터 복사, 데이터 흐름 및 실행 SSIS 패키지를 보고 하는 방법을 설명 합니다.


## <a name="connect-data-factory-to-azure-purview"></a>Azure 부서의 범위에 Data Factory 연결
Azure 부서의 범위는 데이터 사용자가 클라우드 및 온-프레미스 환경 전반에 걸친 데이터 관리를 중앙에서 관리 하는 데 사용할 수 있는 새로운 클라우드 서비스입니다. Data Factory를 Azure 부서의 범위에 연결 하 고, 연결을 통해 Azure 부서의 범위를 사용 하 여 복사, 데이터 흐름 및 SSIS 패키지 실행의 계보 데이터를 캡처할 수 있습니다. Azure 부서의 범위에 data factory를 연결 하는 방법에는 두 가지가 있습니다.
### <a name="register-azure-purview-account-to-data-factory"></a>Data Factory에 Azure 부서의 범위 계정 등록
1. ADF 포털에서   ->  **Azure 부서의 범위** 관리로 이동 합니다. **Purview 계정에 연결** 을 선택합니다. 

:::image type="content" source="./media/data-factory-purview/register-purview-account.png" alt-text="부서의 범위 계정을 등록 하기 위한 스크린샷":::
2. **Azure 구독에서 선택** 또는 **수동으로 입력** 을 선택할 수 있습니다. **Azure 구독에서 선택** 을 선택할 경우 액세스 가능한 계정을 선택할 수 있습니다. 
3. 연결 되 면 탭 **부서의 범위 계정** 에서 부서의 범위 계정의 이름을 확인할 수 있어야 합니다. 
4. Azure Data Factory 포털의 위쪽 가운데에 있는 검색 표시줄을 사용 하 여 데이터를 검색할 수 있습니다. 

Azure 부서의 범위 계정을 Data Factory에 등록 한 후 Azure Data Factory 포털에서 경고가 표시 되는 경우 아래 단계에 따라 문제를 해결 합니다.

:::image type="content" source="./media/data-factory-purview/register-purview-account-warning.png" alt-text="부서의 범위 계정 등록 경고에 대 한 스크린샷":::

1. Azure Portal로 이동 하 여 데이터 팩터리를 찾습니다. 섹션 "태그"를 선택 하 고 이름이 **catalogUri** 인 태그가 있는지 확인 합니다. 그렇지 않은 경우 ADF 포털에서 Azure 부서의 범위 계정을 분리 하 고 다시 연결 하세요.

:::image type="content" source="./media/data-factory-purview/register-purview-account-tag.png" alt-text="부서의 범위 계정을 등록 하는 태그의 스크린샷":::

2. Data Factory에 Azure 부서의 범위 계정을 등록할 수 있는 권한이 부여 되었는지 확인 합니다. [Azure Data Factory 및 Azure 부서의 범위를 연결 하는 방법을](../purview/how-to-link-azure-data-factory.md#create-new-data-factory-connection) 참조 하세요.

### <a name="register-data-factory-in-azure-purview"></a>Azure 부서의 범위에서 Data Factory 등록
Azure 부서의 범위에 Data Factory를 등록 하는 방법에 대 한 자세한 내용은 [Azure Data Factory 및 Azure 부서의 범위를 연결 하는 방법](../purview/how-to-link-azure-data-factory.md)을 참조 하세요. 

## <a name="report-lineage-data-to-azure-purview"></a>Azure 부서의 범위에 계보 데이터 보고
고객은 Azure Data Factory에서 복사, 데이터 흐름 또는 SSIS 패키지 실행 작업을 실행할 때 종속성 관계를 얻을 수 있으며 데이터 원본 및 대상 간의 전체 워크플로 프로세스에 대 한 개략적인 개요를 사용할 수 있습니다.
Azure Data Factory에서 계보를 수집 하는 방법은 [데이터 팩터리 계보](../purview/how-to-link-azure-data-factory.md#supported-azure-data-factory-activities)를 참조 하세요.

## <a name="next-steps"></a>다음 단계
[카탈로그 계보 사용자 가이드](../purview/catalog-lineage-user-guide.md)

[자습서: Azure 부서의 범위에 Data Factory 계보 데이터 푸시](turorial-push-lineage-to-purview.md)