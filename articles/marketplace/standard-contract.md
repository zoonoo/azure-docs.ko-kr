---
title: 표준 계약 | Microsoft
description: Azure Marketplace 및 AppSource의 표준 계약
services: Azure, Marketplace, Compute, Storage, Networking
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 07/05/2019
ms.author: ellacroi
ms.openlocfilehash: 17c1bf9d20b6f2e3ec450ff7bfb54fe61494ff09
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819487"
---
# <a name="standard-contract"></a>표준 계약

고객을 위한 조달 프로세스를 간소화 하 고 소프트웨어 공급 업체에 대 한 법적 복잡성을 줄이기 위해 Microsoft에서는 marketplace에서 트랜잭션을 용이 하 게 하기 위해 표준 계약 템플릿을 제공 합니다. Azure Marketplace 게시자는 사용자 지정 약관을 만드는 대신 표준 계약에 따라 소프트웨어를 제공 하도록 선택할 수 있으며, 고객은 한 번만 심사 하 고 수락 하면 됩니다. 표준 계약은 [https://go.microsoft.com/fwlink/?linkid=2041178](https://go.microsoft.com/fwlink/?linkid=2041178)에서 찾을 수 있습니다. 

제품에 대 한 사용 약관은 Cloud 파트너 포털에서 제품을 만들 때 Marketplace 탭에 정의 되어 있습니다. 표준 계약 옵션은 설정을 예로 변경 하 여 활성화 됩니다.

![표준 계약 옵션 사용](media/marketplace-publishers-guide/standard-contract.png)

>[!Note] 
>표준 계약을 사용 하도록 선택 하는 경우 [클라우드 솔루션 공급자](./cloud-solution-providers.md) 채널에 대 한 별도의 사용 약관은 여전히 필요 합니다.

## <a name="standard-contract-amendments"></a>표준 계약 개정

표준 계약 개정을 사용 하면 게시자가 편의를 위해 표준 계약을 선택 하 고 해당 제품 또는 비즈니스에 맞게 사용자 지정 된 용어를 선택할 수 있습니다.  고객은 계약에 대 한 개정을 검토 하 여 Microsoft 표준 계약을 이미 검토 하 고 수락한 경우에만 해당 계약에 대 한 검토를 수행 해야 합니다.

Azure Marketplace 게시자에는 두 가지 종류의 개정를 사용할 수 있습니다.

* 유니버설 개정: 이러한 개정은 모든 고객에 대 한 표준 계약에 전체적으로 적용 됩니다. 유니버설 개정 구매 흐름에서 제품의 모든 고객에 게 표시 됩니다.

![범용 개정](media/marketplace-publishers-guide/universal-amendaments.png)

* 사용자 지정 개정: Azure Marketplace 테 넌 트를 대상으로 하는 사용자 지정 개정 프로 비전도 있습니다. 특정 고객을 대상으로 하는 표준 계약의 특별 한 개정입니다. 게시자는 대상으로 지정할 테 넌 트를 선택할 수 있습니다. 해당 테 넌 트의 고객은 표준 계약 및 대상 개정 제품을 구매 합니다.

![사용자 지정 개정](media/marketplace-publishers-guide/custom-amendaments.png)

>[!Note] 
>사용자 지정 개정를 대상으로 하는 고객은 구매 중에 표준 용어에 대 한 범용 수정도 받게 됩니다.

>[!Note]
>다음 제품 유형은 표준 계약 개정 Azure 응용 프로그램 (솔루션 템플릿 및 관리 되는 응용 프로그램), Virtual Machines, 컨테이너, 컨테이너 응용 프로그램을 지원 합니다.

### <a name="customer-experience"></a>사용자 환경

Azure Portal 구매 프로세스가 진행 되는 동안 고객은 제품과 관련 된 용어를 Microsoft 표준 계약 및 개정 볼 수 있습니다.

![사용자 환경 Azure Portal입니다.](media/marketplace-publishers-guide/ibiza-customer-experience.png)

### <a name="api"></a>API

고객은 `Get-AzureRmMarketplaceTerms`를 사용 하 여 제품의 약관을 검색 하 고 동의할 수 있습니다. 표준 계약과 연결 된 개정 cmdlet의 출력으로 반환 됩니다.

---
