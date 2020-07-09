---
title: Microsoft 상업적 marketplace의 개인 제품
description: 앱 및 서비스 게시자를 위한 Microsoft 상업적 marketplace의 개인 제품입니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: vikrambmsft
ms.author: vikramb
ms.date: 04/19/2020
ms.openlocfilehash: b558cbc53ba09a86f7277df0ee5d45a9bdac56e3
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86121013"
---
# <a name="private-offers-in-the-microsoft-commercial-marketplace"></a>Microsoft 상업적 marketplace의 개인 제품

의 개인 제공 [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/) 게시자는 대상 고객 에게만 표시 되는 계획을 만들 수 있습니다. 이 문서에서는 개인 제품의 옵션 및 이점에 대해 알아봅니다.

## <a name="unlock-enterprise-deals-with-private-offers"></a>Enterprise에서 개인 제품으로 거래

점점 더 많은 엔터프라이즈 고객이 온라인 마켓플레이스를 사용하여 클라우드 솔루션을 찾고, 사용하고, 구입합니다. 이제 프라이빗 제품에서 게시자는 마켓플레이스를 사용하여 개별적으로 엔터프라이즈가 요구하는 기능을 포함한 사용자 지정 솔루션을 대상 고객과 공유할 수 있습니다.

- *가격 책정 협상*을 통해 게시자가 할인을 확장하고 공개적으로 사용 가능한 제품의 가격 책정을 목록에서 지울 수 있습니다.
- *프라이빗 사용 약관*을 사용하면 게시자가 사용 약관을 특정 고객에게 맞게 조정할 수 있습니다.
- *특수 구성*을 통해 게시자가 Virtual Machines, Azure 애플리케이션 및 SaaS 앱 제품을 개별 고객의 요구 사항에 맞게 조정할 수 있습니다. 이 옵션을 사용하면 게시자가 보다 광범위하게 모든 고객에게 제공하기 전에 새 제품 기능에 대한 미리 보기 액세스를 제공할 수도 있습니다.

프라이빗 제품을 사용하면 게시자가 사용자 지정 거래 및 구성을 협상하고 제공하는 데 필요한 유연성 및 컨트롤을 포함한 공용 마켓플레이스의 규모 및 글로벌 가용성을 활용할 수 있습니다. 이러한 기능을 통해 클라우드 마켓플레이스를 신속하게 엔터프라이즈에 도입하게 됩니다. 이제 엔터프라이즈는 기대하고 요구하는 방법으로 제품을 구입하고 판매할 수 있습니다.

프라이빗 제품은 Virtual Machine, Azure 애플리케이션(솔루션 템플릿 또는 관리 애플리케이션으로 구현) 및 SaaS 앱에 사용할 수 있습니다. 

<!--- Like public offers, private offers can be created and managed via the [Cloud Partner Portal](./cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md).  Customers can be granted or revoked access to private offers in minutes.
--->

## <a name="creating-private-offers-using-skus-and-plans"></a>Sku 및 요금제를 사용 하 여 개인 제안 만들기

*공용 SKU 또는 계획을 포함한 새 제품 또는 기존 제품*의 경우 게시자는 새로운 SKU 또는 계획을 만들고 프라이빗으로 표시하여 새로운 프라이빗 변형을 쉽게 만들 수 있습니다.  

<!--- [Private SKUs](./cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md) ---> 

프라이빗 SKU 및 계획은 제품의 구성 요소이며 대상 고객에게만 표시되고 구입 가능합니다. 프라이빗 SKU 및 계획은 기본 이미지를 다시 사용하거나 공용 SKU 또는 계획에 이미 게시된 메타데이터를 제공할 수 있습니다. 이 옵션을 통해 게시자가 동일한 기본 이미지의 여러 버전을 게시하고 메타데이터를 제공하지 않고 공용 제품의 여러 프라이빗 변형을 만들 수 있습니다. 가상 컴퓨터 및 Azure 응용 프로그램의 경우에만, 전용 SKU가 공용 SKU를 사용 하 여 기본 이미지를 공유 하는 경우 제품의 기본 이미지에 대 한 모든 변경 내용이 해당 기본 이미지를 사용 하 여 모든 공용 및 사설 Sku에 전파 됩니다.

*프라이빗 SKU 또는 계획을 포함하는 새 제품*의 경우 게시자는 해당 제품을 다른 제품으로 만든 다음, SKU 또는 계획을 프라이빗으로 표시할 수 있습니다. 비공개 Sku 또는 요금제만 포함 하는 제안은 Microsoft 상용 marketplace 또는 제품에 연결 되지 않은 고객의 [Azure Portal](https://azure.microsoft.com/features/azure-portal/) 을 통해 검색 되거나 액세스할 수 없게 됩니다.

## <a name="targeting-customers-with-private-offers"></a>비공개 제품을 사용 하 여 고객 대상 지정

새로운 프라이빗 제품 및 기존 전용 제품의 경우 게시자는 구독 식별자를 사용하여 고객을 대상으로 지정할 수 있습니다. Virtual Machine 또는 Azure 애플리케이션 제품을 사용하는 게시자는 개별 Azure 구독 ID로 프라이빗 SKU의 가용성을 제한하거나 최대 20,000개의 Azure 구독 ID의 CSV를 업로드할 수 있습니다. SaaS 앱 개인 제안을 사용 하는 동안 게시자는 수동 또는 CSV 업로드 방법을 사용 하 여 테 넌 트 ID를 연결 하 여 개인 계획의 가용성을 제한할 수 있습니다.

제품이 인증되고 게시되면 고객은 동기화 프라이빗 구독 기능을 사용하여 몇 분 내에 SKU 또는 계획에서 업데이트되거나 제거될 수 있습니다. 이 기능을 통해 게시자는 제품을 인증 하거나 게시 하지 않고도 비공개 SKU 또는 요금제가 제공 되는 고객 목록을 빠르고 쉽게 업데이트할 수 있습니다.

## <a name="deploying-private-offers"></a>개인 제품 배포

개인 제공은 [Azure Portal](https://azure.microsoft.com/features/azure-portal/) 통해서만 검색할 수 있으며 [Microsoft AppSource](https://appsource.microsoft.com/) 또는 [Azure Marketplace](https://azuremarketplace.microsoft.com)에는 표시 되지 않습니다. 다른 상업용 marketplace 상점에 게시 하는 방법에 대 한 자세한 내용은 [게시 옵션 결정](./determine-your-listing-type.md)을 참조 하세요.

Azure Portal에 로그인하면 고객은 프라이빗 제품에 액세스할 Marketplace 검색 요소를 선택할 수 있습니다. 개인 제안은 검색 결과에 표시 되 고 다른 모든 제품 처럼 명령줄 및 Azure Resource Manager 템플릿을 통해 배포할 수 있습니다.

![[프라이빗 제품]](./media/marketplace-publishers-guide/private-offer.png)

또한 프라이빗 제품은 검색 결과에 나타납니다. "개인" 배지만 찾습니다.

>[!Note]
>사설 제품은 클라우드 솔루션 공급자 프로그램 (CSP)의 재판매인을 통해 설정 된 구독에서 지원 되지 않습니다.

<!---
## Next steps

To start using private offers, follow the steps in the [Private SKUs and Plans](./cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md) guide.

--->