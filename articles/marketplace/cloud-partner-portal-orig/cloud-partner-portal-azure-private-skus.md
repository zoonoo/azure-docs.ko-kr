---
title: 개인 Sku 및 계획 | Azure Marketplace
description: 프라이빗 SKU를 사용하여 제품 가용성을 관리하는 방법입니다.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 6efdb1c28777d9230727066fdba03d2850be62b0
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64935917"
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

다른 SKU의 디스크를 재사용하고 가격 또는 설명을 수정할 수 있습니다. 디스크를 재사용하려면 “이 SKU에서 공개 SKU의 이미지를 재사용합니까” 프롬프트에 대한 응답으로 **예**를 선택합니다.

SKU가 프라이빗으로 표시되었으며 제품이 재사용 가능한 디스크가 있는 다른 SKU를 포함하는 경우 SKU에서 다른 SKU의 디스크를 재사용함을 표시해야 합니다. 또한 프라이빗 SKU의 대상 그룹을 지정해야 합니다.

>[!NOTE]
>게시된 후에는 공개 SKU를 프라이빗으로 설정할 수 없습니다.

<a name="select-an-image"></a>이미지 선택
------------------

프라이빗 SKU에 대해 새 디스크를 제공하거나, 다른 SKU에 이미 제공된 동일한 디스크를 재사용하고 가격 또는 설명만 수정할 수 있습니다. 디스크를 재사용하려면 “이 SKU에서 공개 SKU의 이미지를 재사용합니까” 프롬프트에 대한 응답으로 **예**를 선택합니다.

![이미지 재사용 표시](./media/cloud-partner-portal-publish-virtual-machine/selectimage1.png)

SKU가 다른 SKU의 이미지를 재사용함을 확인한 후 이미지 원본인 SKU를 식별합니다.

다음 화면 캡처의 프롬프트는 프라이빗 SKU에서 선택한 SKU의 이미지를 재사용함을 식별하는 방법을 보여 줍니다.

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

<a name="sync-private-subscriptions"></a>프라이빗 구독 동기화
-------------------------

프라이빗 SKU 또는 플랜을 사용하여 게시된 제품에 구독을 추가하는 경우 제품을 다시 게시하여 대상 그룹 정보를 추가할 필요가 없습니다. 단순히 Azure 구독 ID(플랜 및 SKU) 또는 테넌트 ID(플랜 전용)를 사용하여 대상 그룹을 추가합니다.

<a name="previewing-private-offers"></a>프라이빗 제품 미리 보기
-------------------------

미리 보기/스테이징 단계 중에는 제품 수준 미리 보기 구독만 SKU에 액세스할 수 있습니다. 이는 제품이 대상 고객에게 어떻게 표시되는지 검증할 수 있는 테스트 단계이며, 모든 게시 유형에 대해 표준입니다.

스테이징된 제품에 액세스하기 위한 제품 수준 미리 보기 구독:

![미리 보기 구독 ID](./media/cloud-partner-portal-publish-virtual-machine/previewoffer1.png)

제품이 라이브 상태가 되면 제한된 대상 구독(수동 입력 또는 CSV를 통해 입력됨)만 프라이빗 SKU를 보고 배포할 수 있습니다. 유효성 검사를 위해 프라이빗 SKU의 **제한된 대상 그룹에 자신의 구독을 항상 포함**하는 것이 좋습니다.

>[!NOTE]
>디버깅을 위해 Microsoft 지원 및 엔지니어링 팀도 이러한 프라이빗 제품에 액세스할 수 있습니다.
