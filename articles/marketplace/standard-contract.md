---
title: Microsoft 상업적 marketplace에 대 한 표준 계약
description: 파트너 센터의 Azure Marketplace 및 AppSource에 대 한 표준 계약
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: dsindona
ms.openlocfilehash: 3886b29a47edcfb36114cedf8b8edb3799e0c345
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82871425"
---
# <a name="standard-contract-for-microsoft-commercial-marketplace"></a>Microsoft 상업적 marketplace에 대 한 표준 계약

Microsoft는 Microsoft 상업적 marketplace에 대 한 표준 계약을 제공 합니다. 이를 통해 고객에 대 한 조달 프로세스를 간소화 하 고, 소프트웨어 공급 업체의 법적 복잡성을 줄이고, marketplace에서 트랜잭션을 용이 하 게 할 수 있습니다. 상용 마켓플레이스 게시자로 사용자 지정 약관을 만드는 대신 [표준 계약](https://go.microsoft.com/fwlink/?linkid=2041178)에 따라 소프트웨어를 제공 하도록 선택할 수 있으며,이는 고객이 심사 하 고 한 번만 수락 하면 됩니다.

제품에 대 한 사용 조건은 파트너 센터에서 제품을 만들 때 정의 됩니다. 사용자 고유의 사용자 지정 사용 약관을 제공 하는 대신 Microsoft 상업적 marketplace에 대 한 표준 계약을 사용 하도록 선택할 수 있습니다.

>[!Note]
>Microsoft 상업적 marketplace에 대 한 표준 계약을 사용 하 여 제품을 게시 하면 사용자 고유의 사용자 지정 약관을 사용할 수 없습니다. "Or" 시나리오입니다. 표준 계약이 *나* 사용자 고유의 사용 약관에 따라 솔루션을 제공 합니다. 표준 계약의 조건을 수정 하려면 표준 계약 개정을 통해이 작업을 수행할 수 있습니다.

## <a name="standard-contract-amendments"></a>표준 계약 개정

표준 계약 개정을 사용 하면 게시자가 편의를 위해 표준 계약을 선택 하 고 해당 제품 또는 비즈니스에 맞게 사용자 지정 된 용어를 선택할 수 있습니다. 고객은 계약에 대 한 개정을 검토 하 여 Microsoft 표준 계약을 이미 검토 하 고 수락한 경우에만 해당 계약에 대 한 검토를 수행 해야 합니다.

상용 marketplace 게시자에 사용할 수 있는 개정에는 두 가지 종류가 있습니다.

* 유니버설 개정: 이러한 개정은 모든 고객에 대 한 표준 계약에 전체적으로 적용 됩니다. 유니버설 개정 구매 흐름에서 제품의 모든 고객에 게 표시 됩니다. 고객은 제품을 사용 하기 전에 표준 계약 및 수정 내용에 동의 해야 합니다.

* Custom 개정: 이러한 개정는 Azure 테 넌 트 Id를 통해서만 특정 고객을 대상으로 하는 표준 계약에 대 한 특별 한 개정입니다. 게시자는 대상으로 지정할 테 넌 트를 선택할 수 있습니다. 테 넌 트의 고객만 제안의 구매 흐름에 사용자 지정 수정 용어가 표시 됩니다.  고객은 제품을 사용 하기 전에 표준 계약 및 수정 내용에 동의 해야 합니다.

>[!Note]
>이러한 두 가지 유형의 개정 stack이 서로 위에 있습니다. 사용자 지정 개정를 대상으로 하는 고객은 구매 중에 표준 계약에 대 한 범용 수정도 받게 됩니다.

Microsoft 상업적 marketplace에 대 한 표준 계약을 활용할 수 있습니다. Azure 응용 프로그램 (솔루션 템플릿 및 관리 되는 응용 프로그램), Virtual Machines, 컨테이너, 컨테이너 응용 프로그램, IoT Edge 모듈 및 SaaS의 제공 유형입니다.

## <a name="customer-experience"></a>고객 환경

Azure Marketplace 또는 AppSource의 검색 환경을 사용 하는 동안 고객은 제품과 관련 된 용어를 Microsoft 상업적 Marketplace 및 모든 universal 개정의 표준 계약으로 볼 수 있습니다.

![Azure Portal 고객 검색 환경입니다.](media/marketplace-publishers-guide/azure-discovery-process.png)

Azure Portal 구매 프로세스가 진행 되는 동안 고객은 제품과 관련 된 용어를 Microsoft 상업적 marketplace 및 유니버설 및/또는 테 넌 트 별 개정에 대 한 표준 계약으로 볼 수 있습니다.

![Azure Portal 고객 구매 환경.](media/marketplace-publishers-guide/azure-purchase-process.png)

## <a name="api"></a>API

고객은 AzureRmMarketplaceTerms를 사용 하 여 제품의 약관을 검색 하 고 동의할 수 있습니다. 표준 계약과 연결 된 개정 cmdlet의 출력으로 반환 됩니다.
