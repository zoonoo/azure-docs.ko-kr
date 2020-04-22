---
title: 프라이빗 프로모션 | Azure 마켓플레이스
description: 앱 및 서비스 게시자용 Azure Marketplace의 프라이빗 제품
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/19/2020
ms.author: dsindona
ms.openlocfilehash: efda9fb66a65f1f2cd40de8bf32a8a3fa99aea7f
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681426"
---
# <a name="private-offers"></a>프라이빗 제품

[Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/)의 프라이빗 제품을 사용하면 게시자가 대상 고객에게만 표시되는 SKU를 만들 수 있습니다.

## <a name="unlock-enterprise-deals-with-private-offers"></a>프라이빗 오퍼로 엔터프라이즈 거래의 잠금 해제

점점 더 많은 엔터프라이즈 고객이 온라인 마켓플레이스를 사용하여 클라우드 솔루션을 찾고, 사용하고, 구입합니다. 이제 프라이빗 제품에서 게시자는 마켓플레이스를 사용하여 개별적으로 엔터프라이즈가 요구하는 기능을 포함한 사용자 지정 솔루션을 대상 고객과 공유할 수 있습니다.

- *가격 책정 협상*을 통해 게시자가 할인을 확장하고 공개적으로 사용 가능한 제품의 가격 책정을 목록에서 지울 수 있습니다.
- *프라이빗 사용 약관*을 사용하면 게시자가 사용 약관을 특정 고객에게 맞게 조정할 수 있습니다.
- *특수 구성*을 통해 게시자가 Virtual Machines, Azure 애플리케이션 및 SaaS 앱 제품을 개별 고객의 요구 사항에 맞게 조정할 수 있습니다. 이 옵션을 사용하면 게시자가 보다 광범위하게 모든 고객에게 제공하기 전에 새 제품 기능에 대한 미리 보기 액세스를 제공할 수도 있습니다.

프라이빗 제품을 사용하면 게시자가 사용자 지정 거래 및 구성을 협상하고 제공하는 데 필요한 유연성 및 컨트롤을 포함한 공용 마켓플레이스의 규모 및 글로벌 가용성을 활용할 수 있습니다. 이러한 기능을 통해 클라우드 마켓플레이스를 신속하게 엔터프라이즈에 도입하게 됩니다. 이제 엔터프라이즈는 기대하고 요구하는 방법으로 제품을 구입하고 판매할 수 있습니다.

프라이빗 제품은 Virtual Machine, Azure 애플리케이션(솔루션 템플릿 또는 관리 애플리케이션으로 구현) 및 SaaS 앱에 사용할 수 있습니다. 

<!--- Like public offers, private offers can be created and managed via the [Cloud Partner Portal](./cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md).  Customers can be granted or revoked access to private offers in minutes.
--->

## <a name="creating-private-offers-using-skus-and-plans"></a>SUS 및 계획을 사용하여 비공개 오퍼 만들기

*공용 SKU 또는 계획을 포함한 새 제품 또는 기존 제품*의 경우 게시자는 새로운 SKU 또는 계획을 만들고 프라이빗으로 표시하여 새로운 프라이빗 변형을 쉽게 만들 수 있습니다.  

<!--- [Private SKUs](./cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md) ---> 

프라이빗 SKU 및 계획은 제품의 구성 요소이며 대상 고객에게만 표시되고 구입 가능합니다. 프라이빗 SKU 및 계획은 기본 이미지를 다시 사용하거나 공용 SKU 또는 계획에 이미 게시된 메타데이터를 제공할 수 있습니다. 이 옵션을 통해 게시자가 동일한 기본 이미지의 여러 버전을 게시하고 메타데이터를 제공하지 않고 공용 제품의 여러 프라이빗 변형을 만들 수 있습니다. 가상 컴퓨터 및 Azure 응용 프로그램에서만 개인 SKU가 공용 SKU와 기본 이미지를 공유하는 경우에만 해당 기본 이미지를 사용하여 오퍼의 기본 이미지에 대한 변경 내용이 모든 공용 및 개인 SKU에 전파됩니다.

*프라이빗 SKU 또는 계획을 포함하는 새 제품*의 경우 게시자는 해당 제품을 다른 제품으로 만든 다음, SKU 또는 계획을 프라이빗으로 표시할 수 있습니다. 개인 SCO 또는 계획만 있는 오퍼는 Microsoft 상용 마켓플레이스 또는 오퍼와 연결되지 않은 고객이 [Azure 포털을](https://azure.microsoft.com/features/azure-portal/) 통해 검색하거나 액세스할 수 없습니다.

## <a name="targeting-customers-with-private-offers"></a>비공개 오퍼로 고객 타겟팅

새로운 프라이빗 제품 및 기존 전용 제품의 경우 게시자는 구독 식별자를 사용하여 고객을 대상으로 지정할 수 있습니다. Virtual Machine 또는 Azure 애플리케이션 제품을 사용하는 게시자는 개별 Azure 구독 ID로 프라이빗 SKU의 가용성을 제한하거나 최대 20,000개의 Azure 구독 ID의 CSV를 업로드할 수 있습니다. 게시자는 SaaS 앱 비공개 오퍼를 사용하는 동안 테넌트 ID를 연결하여 수동 또는 CSV 업로드 방법을 사용하여 개인 요금제의 가용성을 제한할 수 있습니다.

제품이 인증되고 게시되면 고객은 동기화 프라이빗 구독 기능을 사용하여 몇 분 내에 SKU 또는 계획에서 업데이트되거나 제거될 수 있습니다. 이 기능을 사용하면 게시자가 제안을 다시 인증하거나 게시하지 않고도 개인 SKU 또는 계획이 표시되는 고객 목록을 빠르고 쉽게 업데이트할 수 있습니다.

## <a name="deploying-private-offers"></a>비공개 오퍼 배포

비공개 오퍼는 Azure [포털을](https://azure.microsoft.com/features/azure-portal/) 통해서만 검색할 수 있으며 [Microsoft AppSource](https://appsource.microsoft.com/) 또는 [Azure 마켓플레이스에](https://azuremarketplace.microsoft.com)표시되지 않습니다. 다른 상업용 마켓플레이스 상점에 게시하는 방법에 대해 자세히 알아보려면 [게시 결정 옵션을](./determine-your-listing-type.md)참조하십시오.

Azure Portal에 로그인하면 고객은 프라이빗 제품에 액세스할 Marketplace 검색 요소를 선택할 수 있습니다. 비공개 오퍼는 검색 결과에도 표시되며 다른 오퍼와 마찬가지로 명령줄 및 Azure Resource Manager 템플릿을 통해 배포할 수 있습니다.

![[프라이빗 제품]](./media/marketplace-publishers-guide/private-offer.png)

또한 프라이빗 제품은 검색 결과에 나타납니다. "비공개" 배지를 찾으세요.

>[!Note]
>비공개 오퍼는 CSP(클라우드 솔루션 공급자 프로그램)의 리셀러를 통해 설정된 구독에서는 지원되지 않습니다.

<!---
## Next steps

To start using private offers, follow the steps in the [Private SKUs and Plans](./cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md) guide.

--->