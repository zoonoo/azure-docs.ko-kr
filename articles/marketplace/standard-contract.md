---
title: Microsoft 상업용 마켓플레이스 표준 계약
description: 파트너 센터의 Azure Marketplace 및 AppSource에 대한 표준 계약
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/20/2020
ms.author: dsindona
ms.openlocfilehash: 53a22140fe62ce0db18c1d02d816eb31b12a381b
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83714154"
---
# <a name="standard-contract-for-microsoft-commercial-marketplace"></a>Microsoft 상업용 마켓플레이스 표준 계약

Microsoft는 Microsoft 상업용 마켓플레이스에 대한 표준 계약을 제공합니다. 이는 고객의 조달 프로세스를 간소화하고, 소프트웨어 공급업체의 법적 복잡성을 줄이고, 마켓플레이스의 트랜잭션을 원활하게 진행하는 데 도움이 됩니다. 상업용 마켓플레이스 게시자는 사용자 지정 약관을 만드는 대신 [표준 계약](https://go.microsoft.com/fwlink/?linkid=2041178)에 따라 소프트웨어를 제공하도록 선택할 수 있으며, 고객은 이를 한 번만 심사하고 수락하면 됩니다.

제품의 사용 약관은 파트너 센터에서 제품을 만들 때 정의됩니다. 사용자 지정 약관을 제공하는 대신 Microsoft 상업용 마켓플레이스에 대한 표준 계약을 사용하도록 선택할 수 있습니다.

>[!Note]
>Microsoft 상업용 마켓플레이스 표준 계약을 사용하여 제품을 게시한 후에는 사용자 지정 약관을 사용할 수 없습니다. 이는 "또는" 시나리오입니다. 표준 계약 *또는* 게시자 고유 사용 약관에 따라 솔루션을 제공합니다. 표준 계약의 조건을 수정하려면 표준 계약 수정을 통해 이 작업을 수행할 수 있습니다.

## <a name="standard-contract-amendments"></a>표준 계약 수정

게시자는 표준 계약 수정을 통해 간편하게 표준 계약을 선택하고 제품 또는 비즈니스에 대한 조건을 사용자 지정할 수 있습니다. 고객은 Microsoft 표준 계약을 이미 검토하고 수락한 경우 계약 수정 내용만 검토하면 됩니다.

상업용 마켓플레이스 게시자가 사용할 수 있는 수정은 두 가지 종류가 있습니다.

* 범용 수정: 이러한 수정은 모든 고객에 대한 표준 계약에 전체적으로 적용됩니다. 범용 수정은 구매 흐름에서 제품의 모든 고객에게 표시됩니다. 고객은 제품을 사용하기 전에 표준 계약 및 수정 내용에 동의해야 합니다.

* 사용자 지정 수정: 이러한 수정은 Azure 테넌트 ID를 통해 특정 고객만을 대상으로 하는 표준 계약 특별 수정입니다. 게시자는 대상으로 지정할 테넌트를 선택할 수 있습니다. 테넌트의 고객만 제품의 구매 흐름에 사용자 지정 수정 조건이 표시됩니다.  고객은 제품을 사용하기 전에 표준 계약 및 수정 내용에 동의해야 합니다.

>[!Note]
>이러한 두 가지 유형의 수정은 누적됩니다. 사용자 지정 수정을 대상으로 하는 고객은 구매 중 표준 계약에 대한 범용 수정도 적용받게 됩니다.

Microsoft 상업적 마켓플레이스에 대한 표준 계약은  Azure 애플리케이션(솔루션 템플릿 및 관리형 애플리케이션), Virtual Machines 및 SaaS와 같은 제품 유형에 활용할 수 있습니다.

## <a name="customer-experience"></a>고객 환경

Azure Marketplace 또는 AppSource의 검색 환경을 사용하는 동안 고객은 Microsoft 상업용 마켓플레이스에 대한 표준 계약과 범용 수정을 통해 제품과 관련된 사용 약관을 볼 수 있습니다.

![Azure Portal 고객 검색 환경](media/marketplace-publishers-guide/azure-discovery-process.png)

Azure Portal에서 구매 프로세스가 진행되는 동안 고객은 Microsoft 상업용 마켓플레이스에 대한 표준 계약과 범용 및/또는 테넌트 관련 수정을 통해 제품과 관련된 사용 약관을 볼 수 있습니다.

![Azure Portal 고객 구매 환경](media/marketplace-publishers-guide/azure-purchase-process.png)

## <a name="api"></a>API

고객은 Get-AzureRmMarketplaceTerms를 사용하여 제품의 사용 약관을 검색하고 동의할 수 있습니다. 표준 계약과 관련 수정은 cmdlet의 출력으로 반환됩니다.
