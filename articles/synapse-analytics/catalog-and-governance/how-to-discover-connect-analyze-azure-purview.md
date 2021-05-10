---
title: Azure Purview를 사용하여 Synapse에서 데이터 검색, 연결 및 탐색
description: Synapse에서 데이터를 검색하고, 연결하고, 탐색하는 방법에 대한 가이드
services: synapse-analytics
author: Rodrigossz
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 12/16/2020
ms.author: rosouz
ms.reviewer: jrasnick
ms.openlocfilehash: 79090bfbf08cde3f18b1ca734b8af22d16e7e0ea
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105628665"
---
# <a name="discover-connect-and-explore-data-in-synapse-using-azure-purview"></a>Azure Purview를 사용하여 Synapse에서 데이터 검색, 연결 및 탐색 

이 문서에서는 Synapse에 Azure Purview 계정을 등록할 때 수행할 수 있는 상호 작용의 유형에 대해 알아봅니다. 

## <a name="prerequisites"></a>사전 요구 사항 

- [Azure  Purview 계정](../../purview/create-catalog-portal.md) 
- [Synapse 작업 영역](../quickstart-create-workspace.md) 
- [Synapse에 Azure Purview 계정 연결](quickstart-connect-azure-purview.md) 

## <a name="using-azure-purview-in-synapse"></a>Synapse에서 Azure Purview 사용 

Synapse에서 Azure Purview를 사용하려면 해당 Purview 계정에 대한 액세스 권한이 있어야 합니다. Synapse가 Purview 권한을 통과합니다. 예를 들어, 큐레이터 권한 역할을 사용하는 경우 Azure Purview에서 검색된 메타데이터를 편집할 수 있습니다. 

### <a name="data-discovery-search-datasets"></a>데이터 검색: 데이터 세트 검색 

Azure Purview에서 등록하고 검색한 데이터를 검색하기 위해 Synapse 작업 영역의 위쪽 가운데에 있는 검색 표시줄을 사용할 수 있습니다. Azure Purview를 선택하여 모든 조직 데이터를 검색합니다. 

[![Azure Purview 자산 검색](./media/purview-access.png)](./media/purview-access.png#lightbox)

## <a name="azure-purview-actions"></a>Azure Purview 작업 

Synapse에서 사용할 수 있는 Azure Purview 기능 목록은 다음과 같습니다. 
- 메타데이터 **개요** 
- 분류, 용어집 용어, 데이터 형식 및 설명이 포함된 메타데이터의 **스키마** 를 보고 편집합니다. 
- **계보** 를 보고, 종속성을 이해하고 영향 분석을 수행합니다. 자세한 내용은 [계보](../../purview/catalog-lineage-user-guide.md)를 참조하세요.
- **연락처** 를 보고 편집하여 데이터 세트에 대한 소유자 또는 전문가를 파악합니다. 
- **관련 항목** 을 통해 특정 데이터 세트의 계층 종속성을 이해할 수 있습니다. 이 환경은 데이터 계층을 파악하는 데 유용합니다.

## <a name="actions-that-you-can-perform-over-datasets-with-synapse-resources"></a>Synapse 리소스를 사용하여 데이터 세트에 수행할 수 있는 작업 

### <a name="connect-data-to-synapse"></a>Synapse에 데이터 연결 

- Synapse에 대한 **연결된 새 서비스** 를 만들 수 있습니다. 이러한 작업은 데이터를 Synapse로 복사하거나 데이터 허브(ADLSg2와 같은 지원되는 데이터 원본에 해당)에 포함하는 데 필요합니다. 
- 파일, 폴더 또는 테이블과 같은 개체의 경우 **새 통합 데이터 세트** 를 직접 만들고 기존의 연결된 서비스를 활용할 수 있습니다(이미 만든 경우). 

기존에 연결된 서비스 또는 통합 데이터 세트가 있는지 여부는 아직 유추할 수 없습니다. 

###  <a name="develop-in-synapse"></a>Synapse에서 개발 

**새 SQL 스크립트**, **새 Notebook** 및 **새 데이터 흐름** 의 세 가지 작업을 수행할 수 있습니다. 

**새 SQL 스크립트** 를 사용하여 지원 유형에 따라 다음을 수행할 수 있습니다. 
- 데이터의 모양을 이해하기 위해 상위 100개 행을 봅니다. 
- Synapse SQL 데이터베이스에서 외부 테이블을 만듭니다. 
- Synapse SQL 데이터베이스에 데이터를 로드합니다. 
 
**새 Notabook** 을 사용하여 다음을 수행할 수 있습니다. 
- Spark 데이터 프레임에 데이터 로드 
- Spark 테이블을 만듭니다. Parquet 형식에 대해 이 작업을 수행하는 경우 서버리스 SQL 풀 테이블도 만들어집니다. 
 
**새 데이터 흐름** 을 사용하여 데이터 흐름 파이프라인에서 원본으로 사용할 수 있는 통합 데이터 세트를 만들 수 있습니다. 데이터 흐름은 데이터 변환을 수행하기 위한 비코드 개발자 기능입니다. [Synapse에서 데이터 흐름을 사용하는](../quickstart-data-flow.md) 방법에 대해 자세히 알아봅니다.

##  <a name="nextsteps"></a>다음 단계 

- [Azure Purview에서 Azure Synapse 자산 등록 및 검사](../../purview/register-scan-azure-synapse-analytics.md)
- [Azure Purview Data Catalog에서 데이터를 검색하는 방법](../../purview/how-to-search-catalog.md)
