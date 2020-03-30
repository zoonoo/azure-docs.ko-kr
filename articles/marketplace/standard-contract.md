---
title: 표준 계약 | Azure
description: Azure 마켓플레이스 및 앱 소스에 대한 표준 계약
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: dsindona
ms.openlocfilehash: 00a83a1b3005043f317ed49cafa735540cd21793
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80284962"
---
# <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>마이크로소프트 상용 시장에 대 한 표준 계약

고객을 위한 조달 프로세스를 간소화하고 소프트웨어 공급업체의 법적 복잡성을 줄이기 위해 Microsoft는 시장에서 의 트랜잭션을 용이하게 하기 위해 Microsoft 상용 시장에 대한 표준 계약을 제공합니다. 상업용 마켓플레이스 퍼블리셔는 사용자 지정 이용 약관을 만드는 대신 고객이 한 번만 수의사와 수락을 할 수 있는 표준 계약에 따라 소프트웨어를 제공하도록 선택할 수 있습니다. 표준 계약은 여기에서 찾을 [https://go.microsoft.com/fwlink/?linkid=2041178](https://go.microsoft.com/fwlink/?linkid=2041178)수 있습니다.

오퍼의 이용 약관은 파트너 센터 또는 클라우드 파트너 포털에서 오퍼를 만들 때 정의됩니다. 사용자 지정 이용 약관을 제공하는 대신 Microsoft 상용 마켓플레이스에 표준 계약을 사용하도록 선택할 수 있습니다.

>[!Note]
>Microsoft 상용 마켓플레이스의 표준 계약을 사용하여 오퍼를 게시하면 사용자 지정 이용 약관을 사용할 수 없습니다. "또는" 시나리오입니다. 귀하는 표준 계약 *또는* 사용자 고유의 이용 약관에 따라 솔루션을 제공합니다. 표준 계약 약관을 수정하려면 표준 계약 개정을 통해 수정할 수 있습니다.

## <a name="standard-contract-amendments"></a>표준 계약 개정

표준 계약 개정을 통해 게시자는 단순하고 제품 또는 비즈니스에 대한 맞춤형 조건으로 표준 계약을 선택할 수 있습니다. 고객은 Microsoft 표준 계약을 이미 검토하고 수락한 경우에만 계약 수정을 검토하면 됩니다.

상업용 마켓플레이스 퍼블리셔가 사용할 수 있는 두 가지 종류의 수정안이 있습니다.

* 보편적 개정: 이러한 개정안은 모든 고객을 위한 표준 계약에 보편적으로 적용됩니다. 보편적 인 개정은 구매 흐름에서 제안의 모든 고객에게 표시됩니다. 고객은 제품을 사용하기 전에 표준 계약 약관 및 개정안에 동의해야 합니다.

* 사용자 지정 개정: 이러한 개정은 Azure 테넌트 아이디를 통해서만 특정 고객을 대상으로 하는 표준 계약에 대한 특별 개정입니다. 게시자는 타겟팅할 테넌트를 선택할 수 있습니다. 테넌트의 고객만 오퍼의 구매 흐름에서 사용자 지정 수정 약관이 제공됩니다.  고객은 귀하의 제안을 사용하기 전에 표준 계약 및 개정안의 약관에 동의해야 합니다.

>[!Note]
>이 두 가지 유형의 개정은 서로 겹치게 됩니다. 사용자 지정 개정 대상 고객은 구매 시 표준 계약에 대한 보편적 인 개정을 받게됩니다.

Azure 응용 프로그램(솔루션 템플릿 및 관리되는 응용 프로그램), 가상 컴퓨터, 컨테이너, 컨테이너 응용 프로그램, IoT Edge 모듈 및 SaaS의 다음 제안 유형에 대해 Microsoft 상용 마켓플레이스에 대한 표준 계약을 활용할 수 있습니다. .

## <a name="customer-experience"></a>고객 환경

Azure 마켓플레이스 또는 AppSource에서 검색 환경을 제공하는 동안 고객은 제안과 관련된 조건을 Microsoft 상용 마켓플레이스의 표준 계약 및 범용 개정으로 볼 수 있습니다.

![Azure 포털 고객 검색 환경입니다.](media/marketplace-publishers-guide/azure-discovery-process.png)

Azure 포털에서 구매 프로세스 중에 고객은 Microsoft 상용 마켓플레이스의 표준 계약 및 범용 및/또는 테넌트 관련 개정으로 오퍼와 관련된 조건을 확인할 수 있습니다.

![Azure 포털 고객 구매 환경입니다.](media/marketplace-publishers-guide/azure-purchase-process.png)

## <a name="api"></a>API

고객은 Get-AzureRmMarketplaceTerms를 사용하여 제안 조건을 검색하고 수락할 수 있습니다. 표준 계약 및 관련 개정안은 cmdlet의 출력으로 반환됩니다.
