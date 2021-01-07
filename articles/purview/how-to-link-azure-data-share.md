---
title: Azure 데이터 공유에 연결
description: 이 문서에서는 azure 부서의 범위를 사용 하 여 azure 데이터 공유 계정을 연결 하 여 자산을 검색 하 고 데이터 계보를 추적 하는 방법을 설명 합니다.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/25/2020
ms.openlocfilehash: edea881d67d5a677339c6ff357c1ac45f5dfd420
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96555359"
---
# <a name="how-to-connect-azure-data-share-and-azure-purview"></a>Azure 데이터 공유 및 Azure 부서의 범위를 연결 하는 방법

이 문서에서는 azure 부서의 범위를 사용 하 여 [Azure 데이터 공유](../data-share/overview.md) 계정을 연결 하 고 데이터 공간에서 들어오고 나가는 공유 데이터 집합을 관리 하는 방법을 설명 합니다. 다양 한 데이터 거 버 넌 스를 통해 조직, 부서 및 데이터 센터와 같은 경계에서 데이터 계보를 검색 하 고 추적할 수 있습니다.

## <a name="common-scenarios"></a>일반적인 시나리오

데이터 공유 계보는 근본 원인 분석 및 영향 분석에 대 한 자세한 정보를 제공 하기 위한 것입니다.

### <a name="scenario-1-360-view-of-datasets-shared-inout-for-a-partner-organization-or-internal-department"></a>파트너 조직 또는 내부 부서에 대해 공유/외부 데이터 집합의 시나리오 1:360 보기

데이터 책임자는 파트너 조직과 양방향 공유 된 모든 데이터 집합의 목록을 볼 수 있습니다. 조직 이름을 기준으로 데이터 집합을 검색 및 검색 하 고 모든 송신 및 들어오는 공유의 전체 보기를 볼 수 있습니다.

### <a name="scenario-2-root-cause-analysis---upstream-dependency-on-datasets-coming-into-organization-consumer-view-of-incoming-shares"></a>시나리오 2: 근본 원인 분석-조직에 들어오는 데이터 집합에 대 한 업스트림 종속성 (들어오는 공유의 소비자 보기)

외부 데이터 공유 계정의 업스트림 데이터 문제로 인해 보고서에 잘못 된 정보가 있습니다. 데이터 엔지니어가 업스트림 오류를 이해 하 고, 이유에 대해 알리고, 공유 소유자에 게 문의 하 여 데이터 불일치를 야기 하는 문제를 해결할 수 있습니다.

### <a name="scenario-3-impact-analysis-on-datasets-going-outside-organization-provider-view-of-outgoing-shares"></a>시나리오 3: 조직 외부에서 이동 하는 데이터 집합에 대 한 영향 분석 (보내는 공유의 공급자 보기)

데이터 생산자는 데이터 집합을 변경할 때 영향을 받는 사용자를 알고 싶습니다. 데이터 생산자는 계보를 사용 하 여 Azure 데이터 공유를 통해 데이터를 소비 하는 다운스트림 내부 또는 외부 파트너의 영향을 쉽게 파악할 수 있습니다.

## <a name="azure-data-share-and-purview-connected-experience"></a>Azure 데이터 공유 및 부서의 범위 연결 된 환경

Azure 데이터 공유 및 Azure 부서의 범위 계정을 연결 하려면 다음을 수행 합니다.

1. 부서의 범위 계정을 만듭니다. 모든 데이터 공유 계보 정보는 부서의 범위 계정에 의해 수집 됩니다. 기존 항목을 사용 하거나 새 부서의 범위 계정을 만들 수 있습니다.

1. Azure 데이터 공유를 부서의 범위 계정에 연결 합니다.

    1. 부서의 범위 포털에서 **관리 센터** 로 이동 하 여 Azure 데이터 공유를 **외부 연결** 섹션에 연결할 수 있습니다.
    1. 위쪽 막대에서 **+ 새로 만들기** 를 선택 하 고, 팝업 모음에서 Azure 데이터 공유를 찾고, 데이터 공유 계정을 추가 합니다. 데이터 공유 자산 및 계보 정보가 부서의 범위에 표시 되도록 데이터 공유를 부서의 범위 계정에 연결한 후 스냅숏 작업을 실행 합니다.

       :::image type="content" source="media/how-to-link-azure-data-share/connect-to-data-share.png" alt-text="Azure 데이터 공유에 연결 하는 관리 센터":::

1. Azure 데이터 공유에서 스냅숏을 실행 합니다.

    - Azure 부서의 범위를 사용 하 여 Azure 데이터 공유 연결을 설정 하면 기존 공유에 대 한 스냅숏을 실행할 수 있습니다. 
    - 기존 공유가 없는 경우 Azure 데이터 공유 포털로 이동 하 여 데이터를 공유 하 [share your data](../data-share/share-your-data.md) [고 데이터 공유를 구독](../data-share/subscribe-to-data-share.md)합니다.
    - 공유 스냅숏이 완료 되 면 부서의 범위에서 연결 된 데이터 공유 자산 및 계보를 볼 수 있습니다.

1. 부서의 범위 계정에서 데이터 공유 계정을 검색 하 고 정보를 공유 합니다.

    - 부서의 범위 계정의 홈 페이지에서 **자산 유형별 찾아보기** 를 선택 하 고 **Azure 데이터 공유** 타일을 선택 합니다. 계정 이름, 공유 이름, 공유 스냅숏 또는 파트너 조직을 검색할 수 있습니다. 그렇지 않으면 계정 이름, 공유 유형 (보낸 vs 공유 공유)에 대 한 검색 결과 페이지에 필터를 적용 합니다.

       :::image type="content" source="media/how-to-link-azure-data-share/azure-data-share-search-result-page.png" alt-text="검색 결과 페이지의 Azure 데이터 공유":::

    >[!Important]
    >부서의 범위에 표시 되는 데이터 공유 자산의 경우 데이터 공유를 부서의 범위에 연결한 후에 스냅숏 작업을 실행 해야 합니다.

1. Azure 데이터 공유와 공유 되는 데이터 집합의 계보를 추적 합니다.

    - 부서의 범위 검색 결과 페이지에서 데이터 공유 스냅숏 (수신/송신)을 선택 하 고 **계보** 탭을 선택 하 여 업스트림 및 다운스트림 종속성이 포함 된 계보 그래프를 확인 합니다.

    :::image type="content" source="media/how-to-link-azure-data-share/azure-data-share-lineage.png" alt-text="Azure 데이터 공유를 사용 하 여 공유 되는 데이터 집합의 계보":::

## <a name="next-steps"></a>다음 단계

- [카탈로그 계보 사용자 가이드](catalog-lineage-user-guide.md)
- [계보에 대 한 Azure Data Factory 링크](how-to-link-azure-data-factory.md)
