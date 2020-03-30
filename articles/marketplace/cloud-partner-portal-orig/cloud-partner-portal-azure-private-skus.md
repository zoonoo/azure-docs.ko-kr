---
title: 프라이빗 SUS 및 플랜 | Azure 마켓플레이스
description: 프라이빗 SKU를 사용하여 제품 가용성을 관리하는 방법입니다.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/15/2019
ms.author: dsindona
ms.openlocfilehash: ee3ab7be4d15b13a3c0bb014a3ca4d4096299b4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280391"
---
<a name="private-skus-and-plans"></a>프라이빗 SKU 및 플랜
============

프라이빗 SKU를 사용하면 SKU의 가용성을 특정 고객으로 제한할 수 있습니다. SKU가 프라이빗으로 표시되는 경우 [Azure Marketplace](https://azuremarketplace.microsoft.com) 및 [Azure Portal](https://portal.azure.com)을 포함한 공용 카탈로그에서는 사용할 수 없습니다. Azure Portal에서 해당 SKU에 대한 액세스 권한이 있는 고객만 SKU를 볼 수 있습니다. 또한 프라이빗 제품에 대한 액세스 권한이 있다는 메시지가 표시됩니다.

>[!NOTE]
>공개 SKU와의 충돌을 피하기 위해 프라이빗 SKU에는 새 고유 SKU/플랜 ID가 있어야 합니다.

프라이빗 SKU를 사용하여 다음 시나리오를 처리할 수 있습니다.

1.  일반 공급하지 않고 특정 고객에게만 제공하려는 소프트웨어를 게시합니다.
2.  특정 고객에게 사용자 지정 가격으로 제공하려는 공개 소프트웨어의 변형을 게시합니다.
3.  새 제품을 통해 사용자 지정 설명 및 조건으로 공개 소프트웨어의 변형을 게시합니다.

가격만 변경하려는 경우 동일한 제품에 있는 다른 SKU의 디스크를 재사용할 수 있습니다. 프라이빗 SKU를 사용하면 SKU 간에 디스크를 다시 제출할 필요가 없습니다.

<a name="mark-a-sku-private"></a>SKU를 프라이빗으로 표시
---------------------

SKU를 프라이빗으로 표시하려면 SKU가 프라이빗인지 여부를 묻는 옵션을 토글합니다.

![SKU를 프라이빗으로 표시](./media/cloud-partner-portal-publish-virtual-machine/markingskuprivate.png)

다른 SKU의 디스크를 재사용하고 가격 또는 설명을 수정할 수 있습니다. 디스크를 재사용하려면 "이 SKU가 공용 SKU에서 이미지를 재사용합니까?" 프롬프트에 대한 응답으로 **예를** 선택합니다.

SKU가 프라이빗으로 표시되었으며 제품이 재사용 가능한 디스크가 있는 다른 SKU를 포함하는 경우 SKU에서 다른 SKU의 디스크를 재사용함을 표시해야 합니다. 또한 프라이빗 SKU의 대상 그룹을 지정해야 합니다.

>[!NOTE]
>게시된 후에는 공개 SKU를 프라이빗으로 설정할 수 없습니다.

<a name="select-an-image"></a>이미지 선택
------------------

프라이빗 SKU에 대해 새 디스크를 제공하거나, 다른 SKU에 이미 제공된 동일한 디스크를 재사용하고 가격 또는 설명만 수정할 수 있습니다. 디스크를 재사용하려면 "공용 SKU에서 이 SKU 재사용 이미지" 프롬프트에 대한 응답으로 **예를** 선택합니다.

![이미지 재사용 표시](./media/cloud-partner-portal-publish-virtual-machine/selectimage1.png)

SKU에서 이미지를 재사용한다는 것을 확인한 후 이미지의 소스 또는 *기본* SKU를 선택합니다.

![이미지 선택](./media/cloud-partner-portal-publish-virtual-machine/selectimage2.png)

제품을 게시할 때 선택한 SKU의 이미지가 사용자 지정 요금/조건과 함께 프라이빗 SKU ID 아래에 제공됩니다. 프라이빗 SKU는 대상 그룹에게만 표시됩니다.

이미지 업데이트의 경우 기본 SKU의 이미지만 업데이트하면 됩니다. 그러면 프라이빗 SKU의 이미지도 자동으로 업데이트됩니다. 마찬가지로, 기본 SKU에서 이미지를 삭제하면 프라이빗 SKU의 이미지도 제거됩니다.

<a name="restricting-the-audience"></a>대상 그룹 제한
------------------------

프라이빗 제품은 대상 사용자만 찾아서 배포할 수 있습니다.
현재, 구독 ID를 사용하여 대상 사용자를 지정할 수 있습니다.

이러한 구독은 수동 입력 양식을 통해 **최대 10개 구독**까지 입력하거나, CSV 파일을 업로드하여 **최대 20,000개의 구독**까지 입력할 수 있습니다.

제한된 대상 그룹에 대한 수동 입력:

![수동으로 대상 그룹 제한](./media/cloud-partner-portal-publish-virtual-machine/restrictaudience1.png)

제한된 대상 그룹에 대한 CSV 업로드:

![CSV를 사용하여 대상 그룹 제한](./media/cloud-partner-portal-publish-virtual-machine/restrictaudience2.png)

샘플 CSV 파일 콘텐츠:

            Type,Id,Description
            SubscriptionId,7738d703-3135-4e8d-8b81-1e70379abd9d,Private Customer

수동 입력에서 CSV 업로드 뷰로 전환하거나 CSV에서 수동 입력으로 전환할 때 SKU에 대한 액세스 권한이 있는 이전 구독 ID 목록은 유지되지 않습니다. 경고가 표시되며, 제품을 저장하는 경우에만 목록을 덮어씁니다.

<a name="managing-private-audiences"></a>비공개 잠재고객 관리
-------------------------

**전체 오퍼를 다시 게시하지 않고 잠재고객을 업데이트하려면 UI 또는 API를 사용하여 원하는 잠재고객을 변경한 다음 "비공개 잠재고객 동기화" 작업을 시작합니다.**

잠재고객이 구독수가 10개 이하인 경우 CPP UI를 사용하여 완전히 관리할 수 있습니다.

잠재고객이 10개 이상의 구독인 경우 CPP UI에 업로드하거나 API를 사용할 수 있는 CSV 파일을 사용하여 구독을 관리할 수 있습니다.

API를 사용하고 CSV 파일을 유지 관리하지 않으려면 아래 지침에 따라 API를 사용하여 대상 을 직접 관리할 수 있습니다.

> [!NOTE]
> Azure 구독 ID(요금제 및 SCO) 또는 테넌트 ID(요금제만 해당)를 사용하여 비공개 오퍼에 잠재고객을 추가합니다.

###  <a name="managing-subscriptions-with-the-api"></a>API를 통해 구독 관리

API를 사용하여 CSV를 업로드하거나 CSV를 사용하지 않고 잠재고객을 직접 관리할 수 있습니다. 일반적으로 오퍼를 검색하고 객체를 업데이트한 `restrictedAudience` 다음 해당 변경 내용을 오퍼에 다시 제출하여 잠재고객 구성원을 추가하거나 제거하면 됩니다.

잠재고객 목록을 프로그래밍 방식으로 업데이트하는 방법은 다음과 같습니다.

1. [오퍼](cloud-partner-portal-api-retrieve-specific-offer.md) 데이터 검색:

    ```
    GET https://cloudpartner.azure.com/api/publishers//offers/?api-version=2017-10-31&includeAllPricing=true
    ```

2. 이 JPath 쿼리를 사용하여 오퍼의 각 SKU에서 제한된 잠재고객 개체를 찾습니다.

    ```
    $.definition.plans[*].restrictedAudience
    ```
3. 오퍼의 제한된 잠재고객 개체를 업데이트합니다.

    **CSV 파일에서 비공개 오퍼의 구독 목록을 원래 업로드한 경우:**

    *제한Audience* 개체는 다음과 같습니다.
    ```
    "restrictedAudience": {
                  "uploadedCsvUri": "{SasUrl}"
    }
    ```

    제한된 각 잠재고객 개체에 대해:

    a. 의 내용을 `restrictedAudience.uploadedCsvUri`다운로드합니다. 콘텐츠는 단순히 헤더가 있는 CSV 파일입니다. 예를 들어:

        type,id,description
        subscriptionId,541a269f-3df2-486e-8fe3-c8f9dcf28205,sub1
        subscriptionId,c0da499c-25ec-4e4b-a42a-6e75635253b9,sub2

    b. 필요에 따라 다운로드한 CSV 파일에 구독을 추가하거나 삭제합니다.

    다. 업데이트된 CSV 파일을 [Azure Blob 저장소](../../storage/blobs/storage-blobs-overview.md) 또는 [OneDrive와](https://onedrive.live.com)같은 위치에 업로드하고 파일에 대한 읽기 전용 링크를 만듭니다. 이것은 당신의 새로운 *SasUrl이*될 것입니다.

    d. 새 `restrictedAudience.uploadedCsvUri` *SasUrl로*키를 업데이트합니다.

    **클라우드 파트너 포털에서 비공개 제품의 원래 구독 목록을 수동으로 입력한 경우:**

    *제한 Audience* 개체는 다음과 같이 표시됩니다.

    ```
    "restrictedAudience": {
        "manualEntries": [{
            "type": "subscriptionId",
            "id": "541a269f-3df2-486e-8fe3-c8f9dcf28205",
            "description": "sub1"
            }, {
            "type": "subscriptionId",
            "id": "c0da499c-25ec-4e4b-a42a-6e75635253b9",
            "description": "sub2"
            }
        ]}
    ```

    a. 제한된 각 잠재고객 개체에 대해 `restrictedAudience.manualEntries` 필요에 따라 목록에 항목을 추가하거나 삭제합니다.

4. 모든 *제한 업데이트* 완료되면개인 제안의 각 SKU에 대한 Audience 개체를 [업데이트, 제안을 업데이트](cloud-partner-portal-api-creating-offer.md):

    ```
    PUT https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>?api-version=2017-10-31
    ```
    이를 통해 업데이트된 잠재고객 목록이 적용됩니다.

<a name="previewing-private-offers"></a>비공개 오퍼 미리보기
-------------------------

미리 보기/스테이징 단계 중에는 제품 수준 미리 보기 구독만 SKU에 액세스할 수 있습니다. 이 테스트 단계에서는 대상 고객에게 표시되는 대로 제품을 미리 볼 수 있습니다.

스테이징된 제품에 액세스하기 위한 제품 수준 미리 보기 구독:

![미리 보기 구독 ID](./media/cloud-partner-portal-publish-virtual-machine/previewoffer1.png)

제품이 라이브 상태가 되면 제한된 대상 구독(수동 입력 또는 CSV를 통해 입력됨)만 프라이빗 SKU를 보고 배포할 수 있습니다. 유효성 검사를 위해 프라이빗 SKU의 **제한된 대상 그룹에 자신의 구독을 항상 포함**하는 것이 좋습니다.

>[!NOTE]
>디버깅을 위해 Microsoft 지원 및 엔지니어링 팀도 이러한 프라이빗 제품에 액세스할 수 있습니다.
