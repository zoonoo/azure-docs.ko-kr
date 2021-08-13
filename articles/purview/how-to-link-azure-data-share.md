---
title: Azure Data Share에 연결
description: 이 문서에서는 Azure Data Share 계정을 Azure Purview에 연결하여 자산을 검색하고 데이터 계보를 추적하는 방법을 설명합니다.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/25/2020
ms.openlocfilehash: edea881d67d5a677339c6ff357c1ac45f5dfd420
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96555359"
---
# <a name="how-to-connect-azure-data-share-and-azure-purview"></a>Azure Data Share 및 Azure Purview를 연결하는 방법

이 문서에서는 [Azure Data Share](../data-share/overview.md) 계정을 Azure Purview에 연결하고 데이터 자산에서 들어오고 나가는 공유 데이터 세트를 관리하는 방법을 설명합니다. 다양한 데이터 거버넌스 가상 사용자는 조직, 부서 및 데이터 센터와 같은 경계를 넘어 데이터 계보를 검색하고 추적할 수 있습니다.

## <a name="common-scenarios"></a>일반적인 시나리오

데이터 공유 계보는 근본 원인 분석 및 영향 분석에 대한 자세한 정보를 제공하기 위한 것입니다.

### <a name="scenario-1-360-view-of-datasets-shared-inout-for-a-partner-organization-or-internal-department"></a>시나리오 1: 파트너 조직 또는 내부 부서와 공유되는 데이터 세트의 다각도 보기

데이터 책임자는 파트너 조직과 양방향으로 공유되는 모든 데이터 세트의 목록을 볼 수 있습니다. 조직 이름을 기준으로 데이터 세트를 검색하고 나가고 들어오는 모든 공유의 전체 보기를 볼 수 있습니다.

### <a name="scenario-2-root-cause-analysis---upstream-dependency-on-datasets-coming-into-organization-consumer-view-of-incoming-shares"></a>시나리오 2: 근본 원인 분석 - 조직에 들어오는 데이터 세트에 대한 업스트림 종속성(소비자 관점의 들어오는 공유)

외부 Data Share 계정의 업스트림 데이터 문제로 인해 보고서에 잘못된 정보가 있습니다. 데이터 엔지니어는 업스트림 오류를 이해하고, 이유에 대한 알림을 받고, 공유 소유자에게 문의하여 데이터 불일치를 야기하는 문제를 해결할 수 있습니다.

### <a name="scenario-3-impact-analysis-on-datasets-going-outside-organization-provider-view-of-outgoing-shares"></a>시나리오 3: 조직 외부로 나가는 데이터 세트에 대한 영향 분석(공급자 관점의 나가는 공유)

데이터 생산자는 데이터 세트를 변경할 때 어떤 사용자가 영향을 받게 될지 알려고 합니다. 데이터 생산자는 계보를 사용하여 Azure Data Share를 통해 데이터를 소비하는 다운스트림 내부 또는 외부 파트너가 받게 될 영향을 쉽게 이해할 수 있습니다.

## <a name="azure-data-share-and-purview-connected-experience"></a>Azure Data Share 및 Purview의 연결된 환경

Azure Data Share 및 Azure Purview 계정을 연결하려면 다음을 수행합니다.

1. Purview 계정을 만듭니다. 모든 Data Share 계보 정보는 Purview 계정에서 수집됩니다. 기존 Purview 계정을 사용하거나 새 계정을 만들 수 있습니다.

1. Azure Data Share를 Purview 계정에 연결합니다.

    1. Purview 포털에서 **관리 센터** 로 이동하고 **외부 연결** 섹션에서 Azure Data Share를 연결할 수 있습니다.
    1. 위쪽 표시줄에서 **+새로 만들기** 를 선택하고 팝업 사이드바에서 Azure Data Share를 찾고 Data Share 계정을 추가합니다. Purview 계정에 Data Share를 연결한 후 스냅샷 작업을 실행하여 Data Share 자산 및 계보 정보를 Purview에 표시합니다.

       :::image type="content" source="media/how-to-link-azure-data-share/connect-to-data-share.png" alt-text="Azure Data Share를 연결하기 위한 관리 센터":::

1. Azure Data Share에서 스냅샷을 실행합니다.

    - Azure Purview를 통해 Azure Data Share 연결이 설정되면 기존 공유에 대한 스냅샷을 실행할 수 있습니다. 
    - 기존 공유가 없는 경우 Azure Data Share 포털로 이동하여 [데이터를 공유](../data-share/share-your-data.md)하고 [데이터 공유를 구독](../data-share/subscribe-to-data-share.md)합니다.
    - 공유 스냅샷이 완료되면 Purview에서 연결된 Data Share 자산 및 계보를 볼 수 있습니다.

1. Data Share 계정을 검색하고 Purview 계정에서 정보를 공유합니다.

    - Purview 계정의 홈페이지에서 **자산 유형별 찾아보기** 를 선택하고 **Azure Data Share** 타일을 선택합니다. 계정 이름, 공유 이름, 공유 스냅샷 또는 파트너 조직을 검색할 수 있습니다. 그렇지 않으면 검색 결과 페이지에 계정 이름, 공유 유형(보낸 공유 및 받은 공유)에 대한 필터를 적용합니다.

       :::image type="content" source="media/how-to-link-azure-data-share/azure-data-share-search-result-page.png" alt-text="검색 결과 페이지의 Azure Data Share":::

    >[!Important]
    >Data Share 자산이 Purview에 표시되려면 Data Share를 Purview에 연결한 후 스냅샷 작업을 실행해야 합니다.

1. Azure Data Share과 공유된 데이터 세트의 계보를 추적합니다.

    - Purview 검색 결과 페이지에서 Data Share 스냅샷(받은/보낸)을 선택하고 **계보** 탭을 선택하여 업스트림 및 다운스트림 종속성을 포함하는 계보 그래프를 봅니다.

    :::image type="content" source="media/how-to-link-azure-data-share/azure-data-share-lineage.png" alt-text="Azure Data Share를 사용하여 공유된 데이터 세트 계보":::

## <a name="next-steps"></a>다음 단계

- [카탈로그 계보 사용자 가이드](catalog-lineage-user-guide.md)
- [계보에 대한 Azure Data Factory 링크](how-to-link-azure-data-factory.md)
