---
title: Azure 부서의 범위를 사용 하 여 Synapse에서 데이터 검색, 연결 및 탐색
description: 데이터를 검색 하 고, 연결 하 고, Synapse에서 탐색 하는 방법에 대 한 가이드
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 12/16/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 817f9a88bd3536e9c0ec8a1057098e6b4a96716e
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98676360"
---
# <a name="discover-connect-and-explore-data-in-synapse-using-azure-purview"></a>Azure 부서의 범위를 사용 하 여 Synapse에서 데이터 검색, 연결 및 탐색 

이 문서에서는 Azure 부서의 범위 계정을 Synapse에 등록할 때 수행할 수 있는 상호 작용의 유형에 대해 알아봅니다. 

## <a name="prerequisites"></a>사전 요구 사항 

- [Azure  Purview 계정](../../purview/create-catalog-portal.md) 
- [Synapse 작업 영역](../quickstart-create-workspace.md) 
- [Synapse에 Azure 부서의 범위 계정 연결](quickstart-connect-azure-purview.md) 

## <a name="using-azure-purview-in-synapse"></a>Synapse에서 Azure 부서의 범위 사용 

Synapse에서 Azure 부서의 범위 사용에서는 해당 부서의 범위 계정에 액세스할 수 있어야 합니다. Synapse는 부서의 범위 권한을 통과 합니다. 예를 들어, 큐레이터 권한 역할을 사용 하는 경우 Azure 부서의 범위에서 검색 된 메타 데이터를 편집할 수 있습니다. 

### <a name="data-discovery-search-datasets"></a>데이터 검색: 데이터 집합 검색 

Azure 부서의 범위에서 등록 하 고 검색 한 데이터를 검색 하기 위해 Synapse 작업 영역의 위쪽 가운데에 있는 검색 표시줄을 사용할 수 있습니다. Azure 부서의 범위를 선택 하 여 모든 조직 데이터를 검색 하는지 확인 합니다. 

## <a name="azure-purview-actions"></a>Azure 부서의 범위 작업 

Synapse에서 사용할 수 있는 Azure 부서의 범위 기능 목록은 다음과 같습니다. 
- 메타 데이터 **개요** 
- 분류, 용어집 용어, 데이터 형식 및 설명이 포함 된 메타 데이터의 **스키마** 를 보고 편집 합니다. 
- **계보** 를 보고 종속성을 이해 하 고 영향 분석을 수행 합니다. 에 대 한 자세한 내용은 [계보](../../purview/catalog-lineage-user-guide.md) 를 참조 하세요.
- **연락처** 보기 및 편집을 통해 데이터 집합에 대 한 소유자 또는 전문가 파악 
- 특정 데이터 집합의 계층 종속성을 이해 하는 것 **과 관련** 되어 있습니다. 이 환경은 데이터 계층을 탐색 하는 데 유용 합니다.

## <a name="actions-that-you-can-perform-over-datasets-with-synapse-resources"></a>Synapse 리소스를 사용 하 여 데이터 집합에 대해 수행할 수 있는 작업 

### <a name="connect-data-to-synapse"></a>Synapse에 데이터 연결 

- Synapse에 대 한 **새 연결 된 서비스** 를 만들 수 있습니다. 이러한 작업은 데이터를 Synapse로 복사 하거나 데이터 허브 (ADLSg2와 같은 지원 되는 데이터 원본)에 포함 하는 데 필요 합니다. 
- 파일, 폴더 또는 테이블과 같은 개체의 경우 **새 통합 데이터 집합** 을 직접 만들고 기존 연결 된 서비스를 활용할 수 있습니다 (이미 만든 경우). 

기존에 연결 된 서비스 또는 통합 데이터 집합이 있는지는 아직 유추할 수 없습니다. 

###  <a name="develop-in-synapse"></a>Synapse에서 개발 

다음 세 가지 작업을 수행할 수 있습니다. **새 SQL 스크립트**, **새 노트북** 및 **새 데이터 흐름** 입니다. 

**새 SQL 스크립트** 를 사용 하 여 지원 유형에 따라 다음을 수행할 수 있습니다. 
- 데이터의 셰이프를 이해 하기 위해 상위 100 행을 봅니다. 
- Synapse SQL database에서 외부 테이블 만들기 
- Synapse SQL 데이터베이스에 데이터 로드 
 
**새 노트북** 을 사용 하 여 다음을 수행할 수 있습니다. 
- Spark 데이터 프레임에 데이터 로드 
- Spark 테이블을 만듭니다. Parquet 형식에 대해이 작업을 수행 하는 경우 서버 리스 SQL 풀 테이블도 만듭니다. 
 
**새 데이터 흐름** 을 사용 하 여 데이터 흐름 파이프라인에서 원본으로 사용할 수 있는 통합 데이터 집합을 만들 수 있습니다. 데이터 흐름은 데이터 변환을 수행 하는 비 코드 개발자 기능입니다. [Synapse에서 데이터 흐름을 사용 하](../quickstart-data-flow.md)는 방법에 대 한 자세한 내용은을 (를).

##  <a name="nextsteps"></a>다음 단계 

- [Azure Purview에서 Azure Synapse 자산 등록 및 검사](../../purview/register-scan-azure-synapse-analytics.md)
- [Azure 부서의 범위 Data Catalog에서 데이터를 검색 하는 방법](../../purview/how-to-search-catalog.md)
