---
title: Purview를 사용하여 ADF의 데이터 검색 및 살펴보기
description: 부서의 범위를 사용 하 여 Azure Data Factory에서 데이터를 검색 하 고 탐색 하는 방법을 알아봅니다.
ms.service: data-factory
ms.topic: conceptual
author: lrtoyou1223
ms.author: lle
ms.custom: seo-lt-2019
ms.date: 01/15/2021
ms.openlocfilehash: 031e7ceee739ffd666ad367bd6778865a297a25d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100386274"
---
# <a name="discover-and-explore-data-in-adf-using-purview"></a>Purview를 사용하여 ADF의 데이터 검색 및 살펴보기

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

이 문서에서는 Azure 부서의 범위 계정을 Data Factory에 등록 합니다. 이 연결을 통해 Azure 부서의 범위 자산을 검색 하 고 ADF 기능을 통해 상호 작용할 수 있습니다. 

ADF에서 다음 작업을 수행할 수 있습니다. 
- 위쪽의 검색 상자에서 키워드를 사용하여 Purview 자산 찾기 
- 메타데이터, 계보, 주석을 기반으로 데이터 이해 
- 연결 된 서비스 또는 데이터 집합을 사용 하 여 이러한 데이터를 데이터 팩터리에 연결 

## <a name="prerequisites"></a>사전 요구 사항 
- [Azure  Purview 계정](../purview/create-catalog-portal.md) 
- [Data Factory](./quickstart-create-data-factory-portal.md) 
- [Azure 부서의 범위 계정을 Data Factory에 연결](./connect-data-factory-to-azure-purview.md) 

## <a name="using-azure-purview-in-data-factory"></a>Data Factory에서 Azure 부서의 범위 사용 

Data Factory에서 Azure 부서의 범위 사용을 사용 하려면 해당 부서의 범위 계정에 대 한 액세스 권한이 있어야 합니다. 부서의 범위 사용 권한을 통과 Data Factory 합니다. 예를 들어, 큐레이터 권한 역할을 사용 하는 경우 Azure 부서의 범위에서 검색 된 메타 데이터를 편집할 수 있습니다. 

### <a name="data-discovery-search-datasets"></a>데이터 검색: 데이터 집합 검색 

Azure 부서의 범위에서 등록 하 고 검색 한 데이터를 검색 하기 위해 Data Factory 포털의 위쪽 가운데에 있는 검색 표시줄을 사용할 수 있습니다. Azure 부서의 범위를 선택 하 여 모든 조직 데이터를 검색 하는지 확인 합니다. 

:::image type="content" source="./media/data-factory-purview/search-dataset.png" alt-text="데이터 집합을 통해 수행 하는 스크린샷":::

### <a name="actions-that-you-can-perform-over-datasets-with-data-factory-resources"></a>Data Factory 리소스를 사용 하 여 데이터 집합에 대해 수행할 수 있는 작업 
Azure 부서의 범위에서 검색 하는 데이터에 대 한 연결 된 서비스, 데이터 집합 또는 데이터 흐름을 직접 만들 수 있습니다.

:::image type="content" source="./media/data-factory-purview/actions-over-purview-data.png" alt-text="Azure 부서의 범위에서 검색 하는 데이터에 대해 연결 된 서비스, 데이터 집합 또는 데이터 흐름을 직접 만들 수 있는 방법을 보여 주는 스크린샷":::

##  <a name="nextsteps"></a>다음 단계 

- [Azure 부서의 범위에서 Azure Data Factory 자산 등록 및 검사](../purview/register-scan-azure-synapse-analytics.md)
- [Azure 부서의 범위 Data Catalog에서 데이터를 검색 하는 방법](../purview/how-to-search-catalog.md)