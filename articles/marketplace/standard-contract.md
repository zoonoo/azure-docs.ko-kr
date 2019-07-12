---
title: 표준 계약 | Azure
description: Azure Marketplace 및 AppSource에서 표준 계약
services: Azure, Marketplace, Compute, Storage, Networking
author: qianw211
ms.service: marketplace
ms.topic: article
ms.date: 07/05/2019
ms.author: ellacroi
ms.openlocfilehash: 80c157423572d356026f257e81d52650ce01d3e8
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620343"
---
# <a name="standard-contract"></a>표준 계약

고객에 대 한 조달 프로세스를 간소화 하 고 소프트웨어 공급 업체에 대 한 법적 복잡성을 줄이기, Microsoft는 marketplace에서 트랜잭션을 용이 하 게 하기 위해 표준 계약 템플릿을 제공 합니다. 사용자 지정 사용 약관을 작성 하는 대신 Azure Marketplace 게시자 고객에 게 심사 하 고 동의 한 번만 해야 하는 표준 계약에 따라 해당 소프트웨어를 제공할 수 있습니다. 표준 계약을 확인할 수 있습니다: [ https://go.microsoft.com/fwlink/?linkid=2041178 ](https://go.microsoft.com/fwlink/?linkid=2041178)합니다. 

클라우드 파트너 포털에서 제품을 만들면 Marketplace 탭의 제품에 대 한 조건에 정의 됩니다. 표준 계약 옵션은 예 설정을 변경 하 여 활성화 됩니다.

![표준 계약 옵션을 사용 하도록 설정](media/marketplace-publishers-guide/standard-contract.png)

>[!Note] 
>별도 사용 약관은 여전히 필요 Standard 계약을 사용 하려는 경우는 [클라우드 솔루션 공급자](./cloud-solution-providers.md) 채널입니다.

## <a name="standard-contract-amendments"></a>표준 계약 수정

편의상 및 해당 제품 또는 비즈니스를 위한 사용자 지정 된 용어를 사용 하 여 표준 계약을 선택 하 여 게시자가 허용 하는 표준 계약 수정 합니다.  고객에 게는만 보유 이미 검토 하 고 Microsoft 표준 계약을 수락 하는 경우 계약의 수정 검토 해야 합니다.

두 가지 종류 수정의 Azure Marketplace 게시자에 대해 사용할 수 있습니다.

* 유니버설 수정: 이러한 수정 모든 고객에 대 한 표준 계약을 보편적으로 적용 됩니다. 유니버설 수정 구매 흐름에서 제품의 모든 고객에 게 표시 됩니다.

![유니버설 수정](media/marketplace-publishers-guide/universal-amendaments.png)

* 사용자 지정 수정: Azure Marketplace에는 테 넌 트를 대상으로 하는 사용자 지정 수정에 대 한 프로 비전에 있습니다. 특정 고객에만 대상으로 하는 표준 계약에 특별 한 수정 됩니다. 게시자는 대상으로 하려는 테 넌 트를 선택할 수 있습니다. 해당 테 넌 트에서 고객 표준 계약 및 대상된 수정에서 제품을 구입할 것입니다.

![사용자 지정 수정](media/marketplace-publishers-guide/custom-amendaments.png)

>[!Note] 
>사용자 지정 수정으로 대상 고객 구매 중 표준 용어에 유니버설 수정 계약이 발생 하기도 합니다.

>[!Note]
>제품 유형은 표준 계약 수정 지원합니다. Azure 응용 프로그램 (솔루션 템플릿 및 관리 되는 응용 프로그램), 가상 머신, 컨테이너, 컨테이너 응용 프로그램입니다.

### <a name="customer-experience"></a>사용자 환경

Azure portal에서 구매 과정 고객에 게는 Microsoft 표준 계약 및 수정 된 제품과 연결 된 사용 약관을 볼 수 됩니다.

![Azure 포털 고객 경험 합니다.](media/marketplace-publishers-guide/ibiza-customer-experience.png)

### <a name="api"></a>API

고객은 사용할 수 있습니다 `Get-AzureRmMarketplaceTerms` 를 제품의 용어를 검색 하 여 수락 합니다. 표준 계약과 연결된 수정 cmdlet의 출력에 반환 됩니다.

---
