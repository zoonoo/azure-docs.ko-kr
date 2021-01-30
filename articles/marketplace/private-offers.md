---
title: Microsoft 상업적 marketplace의 개인 제품
description: 앱 및 서비스 게시자를 위한 Microsoft 상업적 marketplace의 개인 제품입니다.
ms.subservice: partnercenter-marketplace-publisher
ms.service: marketplace
ms.topic: article
author: vikrambmsft
ms.author: vikramb
ms.date: 01/28/2021
ms.openlocfilehash: 786ecbf553ace6a90515347e8138eeb6e022589b
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99063099"
---
# <a name="private-offers-in-the-microsoft-commercial-marketplace"></a>Microsoft 상업적 marketplace의 개인 제품

개인 제안 (비공개 요금제 라고도 함)을 통해 게시자는 대상 고객 에게만 표시 되는 계획을 만들 수 있습니다. 이 문서에서는 개인 제품의 옵션 및 이점에 대해 설명 합니다.

## <a name="unlock-enterprise-deals-with-private-offers"></a>Enterprise에서 개인 제품으로 거래

개인 제안을 만들면 게시자는 회사에서 요구 하는 기능을 사용 하 여 대상 고객에 게 사용자 지정 된 솔루션을 개인적으로 제공할 수 있습니다.

- _가격 책정 협상_ 을 통해 게시자가 할인을 확장하고 공개적으로 사용 가능한 제품의 가격 책정을 목록에서 지울 수 있습니다.
- _프라이빗 사용 약관_ 을 사용하면 게시자가 사용 약관을 특정 고객에게 맞게 조정할 수 있습니다.
- _특별 한 구성을_ 통해 게시자는 Virtual Machines, Azure 응용 프로그램 및 SaaS (software as a service)를 개별 고객의 요구에 맞게 조정할 수 있습니다. 이 옵션을 사용 하면 게시자가 모든 고객에 게 해당 기능을 실행 하기 전에 새 제품 기능에 대 한 미리 보기 액세스를 제공할 수도 있습니다.

개인 제공을 통해 게시자는 사용자 지정 거래 및 구성을 협상 하 고 제공 하는 데 필요한 유연성과 제어를 통해 공용 marketplace의 규모와 글로벌 가용성을 활용할 수 있습니다. 이제 기업에서 기대한 방법으로 구입 하 고 판매할 수 있습니다. 

## <a name="create-private-offers-using-plans"></a>요금제를 사용 하 여 개인 제안 만들기

요금제를 사용 하는 *신규 또는 기존* 제안의 경우, 게시자는 새 계획 (이전의 sku)을 만들어 개인 변형으로 표시 하 여 새 개인 변형을 쉽게 만들 수 있습니다. 각 제품에는 최대 45 개의 개인 계획이 있을 수 있습니다.

<!--- [Private SKUs]() --->

비공개 요금제는 다음과 같은 제안 유형에 사용할 수 있습니다.

- Azure Virtual Machine
- Azure 애플리케이션 (솔루션 템플릿 또는 관리 되는 응용 프로그램으로 구현 됨)
- 관리 서비스
- SaaS 제품

비공개 요금제는 제품의 구성 요소 이며 대상 고객 에게만 표시 되 고 구입 가능 됩니다. 비공개 요금제는 대상 고객 에게만 표시 되 고 구입 가능 됩니다.  개인 계획은 Azure Global 및 Azure Government 모두의 고객에 게 제공 될 수 있습니다.

비공개 요금제는 공개 계획에 이미 게시 된 기본 이미지 및/또는 제안 메타 데이터를 다시 사용할 수 있습니다. 이 옵션을 사용 하면 게시자가 동일한 기본 이미지의 여러 버전을 게시 하 고 메타 데이터를 제공할 필요 없이 공용 제품의 여러 개인 변형을 만들 수 있습니다. Azure 가상 머신 및 Azure 응용 프로그램 제공의 경우에만 비공개 계획에서 공개 요금제를 사용 하 여 기본 이미지를 공유 하는 경우 제품의 기본 이미지에 대 한 모든 변경 내용이 해당 기본 이미지를 사용 하 여 모든 공용 및 비공개 계획에 전파 됩니다.

*비공개 요금제만 포함 된 새 제품* 의 경우, 게시자는 해당 제품을 다른 제품으로 만든 다음 해당 계획을 비공개로 표시할 수 있습니다. 비공개 요금제만 있는 제품은 제공에 연결 되지 않은 고객의 [Azure Portal](https://azure.microsoft.com/features/azure-portal/) 에서 검색 되거나 액세스할 수 없게 됩니다.

>[!NOTE]
>비공개 계획만 포함 된 제안은 공용 Azure Marketplace 또는 AppSource에 표시 되지 않습니다.

## <a name="target-customers-with-private-offers"></a>비공개 제품을 사용 하는 대상 고객

새로운 프라이빗 제품 및 기존 전용 제품의 경우 게시자는 구독 식별자를 사용하여 고객을 대상으로 지정할 수 있습니다. Azure 가상 머신, Azure 애플리케이션 및 관리 서비스 제공의 경우 게시자는 개인 계획의 가용성을 개별 Azure 구독 ID로 제한 하거나 최대 1만 Azure 구독 id의 CSV를 업로드할 수 있습니다. SaaS 제품의 경우 게시자는 수동 또는 CSV 업로드 방법을 사용 하 여 Azure Active Directory 테 넌 트 ID를 연결 하 여 개인 계획의 가용성을 제한할 수 있습니다.

제품이 인증 되 고 게시 되 면 개인 구독 동기화 기능을 사용 하 여 계획에서 고객을 업데이트 하거나 제거할 수 있습니다. 이 기능을 통해 게시자는 제품을 인증 하거나 게시 하지 않고도 비공개 요금제가 제공 되는 고객의 목록을 빠르고 쉽게 업데이트할 수 있습니다.

## <a name="deploying-private-offers"></a>개인 제품 배포

Azure Portal에 로그인 한 후에는 다음 단계에 따라 개인 제품을 선택할 수 있습니다.

1. [Azure Portal](https://ms.portal.azure.com/)에 로그인 합니다.
1. **Azure 서비스** 에서 **리소스 만들기** 를 선택 합니다.
1. **새로 만들기** 페이지의 **Azure Marketplace** 옆에서 **모두 보기** 를 선택 합니다. Marketplace 페이지가 나타납니다.
1. 왼쪽 탐색 영역에서 **비공개 제품** 을 선택 합니다.

> [!NOTE]
> 개인 제공은 [Azure Portal](https://azure.microsoft.com/features/azure-portal/)에서만 검색할 수 있습니다. [Microsoft AppSource](https://appsource.microsoft.com/) 또는 [Azure Marketplace](https://azuremarketplace.microsoft.com)에는 표시 되지 않습니다. 다른 상업용 marketplace 온라인 상점에 게시 하는 방법에 대 한 자세한 내용은 [목록 옵션 소개](./determine-your-listing-type.md)를 참조 하세요.

개인 제안은 검색 결과에 표시 되 고 다른 모든 제품 처럼 명령줄 및 Azure Resource Manager 템플릿을 통해 배포할 수 있습니다.

![[프라이빗 제품]](./media/marketplace-publishers-guide/private-offer.png)

또한 프라이빗 제품은 검색 결과에 나타납니다. **비공개** 배지만 찾습니다.

>[!Note]
>CSP (클라우드 솔루션 공급자) 프로그램의 재판매인을 통해 설정 된 구독은 개인 제공을 지원 하지 않습니다.


<!---
## Next steps

To start using private offers, follow the steps in the [Private SKUs and Plans]() guide.
--->
