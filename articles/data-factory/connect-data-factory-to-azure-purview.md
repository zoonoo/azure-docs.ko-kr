---
title: Azure Purview에 Data Factory 연결
description: Azure Purview에 Data Factory를 연결하는 방법 알아보기
ms.author: lle
author: lrtoyou1223
ms.service: data-factory
ms.topic: conceptual
ms.custom:
- seo-lt-2019
- references_regions
ms.date: 12/3/2020
ms.openlocfilehash: 44f093f96d0f4653a6fcca94aaa97264c93e3c7d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101727943"
---
# <a name="connect-data-factory-to-azure-purview-preview"></a>Azure Purview(미리 보기)에 Data Factory 연결
[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

이 문서에서는 Data Factory를 Azure Purview에 연결하는 방법 및 Azure Data Factory 작업, 복사 데이터, 데이터 흐름, SSIS 패키지 실행의 데이터 계보를 보고하는 방법을 설명합니다.


## <a name="connect-data-factory-to-azure-purview"></a>Azure Purview에 Data Factory 연결
Azure Purview는 데이터 사용자가 클라우드 및 온-프레미스 환경 전반에 걸친 데이터 거버넌스를 중앙에서 관리하는 데 사용할 수 있는 새로운 클라우드 서비스입니다. Data Factory를 Azure Purview에 연결하면 연결을 통해 Azure Purview를 사용하여 복사, 데이터 흐름, SSIS 패키지 실행의 계보 데이터를 캡처할 수 있습니다. Azure Purview에 Data Factory를 연결하는 방법에는 두 가지가 있습니다.
### <a name="register-azure-purview-account-to-data-factory"></a>Data Factory에 Azure Purview 계정 등록
1. ADF 포털에서 **관리** -> **Azure Purview** 로 이동합니다. **Purview 계정에 연결** 을 선택합니다. 

:::image type="content" source="./media/data-factory-purview/register-purview-account.png" alt-text="Purview 계정 등록 스크린샷":::
2. **Azure 구독에서 선택** 또는 **수동으로 입력** 을 선택할 수 있습니다. **Azure 구독에서 선택** 을 선택할 경우 액세스 가능한 계정을 선택할 수 있습니다. 
3. 연결되면 **Azure Purview 계정** 탭에 Purview 계정 이름이 표시됩니다. 
4. Azure Data Factory 포털의 상단 중앙에 있는 검색 창을 사용하여 데이터를 검색할 수 있습니다. 

Azure Purview 계정을 Data Factory에 등록한 후 Azure Data Factory 포털에 경고가 나타나는 경우에는 아래 단계에 따라 문제를 해결합니다.

:::image type="content" source="./media/data-factory-purview/register-purview-account-warning.png" alt-text="Purview 계정 등록 경고 스크린샷":::

1. Azure Portal로 이동하여 데이터 팩터리를 찾습니다. ‘태그’ 섹션을 선택하고 이름이 **catalogUri** 인 태그가 있는지 확인합니다. 없는 경우 ADF 포털에서 Azure Purview 계정의 연결을 해제하고 다시 연결합니다.

:::image type="content" source="./media/data-factory-purview/register-purview-account-tag.png" alt-text="Purview 계정 등록 태그 스크린샷":::

2. Data Factory에 Azure Purview 계정을 등록할 수 있는 권한이 부여되었는지 확인합니다. [Azure Data Factory와 Azure Purview를 연결하는 방법](../purview/how-to-link-azure-data-factory.md#create-new-data-factory-connection)을 참조하세요.

### <a name="register-data-factory-in-azure-purview"></a>Azure Purview에서 Data Factory 등록
Azure Purview에서 Data Factory를 등록하는 방법에 대한 자세한 내용은 [Azure Data Factory와 Azure Purview를 연결하는 방법](../purview/how-to-link-azure-data-factory.md)을 참조하세요. 

## <a name="report-lineage-data-to-azure-purview"></a>Azure Purview에 계보 데이터 보고
고객은 Azure Data Factory에서 복사, 데이터 흐름 또는 SSIS 패키지 실행 작업을 실행할 때 종속성 관계를 얻을 수 있으며 데이터 원본과 대상 간의 전체 워크플로 프로세스에 대한 개략적인 개요를 얻을 수 있습니다.
Azure Data Factory에서 계보를 수집하는 방법은 [데이터 팩터리 계보](../purview/how-to-link-azure-data-factory.md#supported-azure-data-factory-activities)를 참조하세요.

## <a name="next-steps"></a>다음 단계
[카탈로그 계보 사용자 가이드](../purview/catalog-lineage-user-guide.md)

[자습서: Data Factory 계보 데이터를 Azure Purview에 푸시](turorial-push-lineage-to-purview.md)