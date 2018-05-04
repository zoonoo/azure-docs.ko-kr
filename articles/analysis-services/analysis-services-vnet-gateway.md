---
title: Azure Virtual Network 데이터 원본에 온-프레미스 데이터 게이트웨이 사용 | Microsoft Docs
description: VNET에 대한 데이터 원본에 게이트웨이를 사용하도록 서버를 구성하는 방법을 알아봅니다.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 61c4463060016ecbd319b027d48c87f3a6a8ad48
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2018
---
# <a name="use-gateway-for-datasources-on-an-azure-virtual-network-vnet"></a>Azure VNET(Virtual Network)에서 데이터 원본에 게이트웨이 사용

이 문서에서는 데이터 원본이 [Azure VNET(Virtual Network)](../virtual-network/virtual-networks-overview.md)에 있는 경우 사용을 위한 **AlwaysUseGateway** 서버 속성을 설명합니다.

## <a name="server-access-to-vnet-datasources"></a>VNET 데이터 원본에 대한 서버 액세스

VNET을 통해 데이터 원본에 액세스하는 경우 Azure Analysis Services 서버는 해당 데이터 원본이 사용자 환경의 온-프레미스에 있는 경우 해당 데이터 원본에 연결해야 합니다. **AlwaysUseGateway** 서버 속성을 구성하여 [온-프레미스 게이트웨이](analysis-services-gateway.md)를 통해 모든 데이터 원본 데이터에 액세스하도록 서버를 지정할 수 있습니다. 

> [!NOTE]
> 이 속성은 [온-프레미스 데이터 게이트웨이](analysis-services-gateway.md)가 설치되고 구성된 경우에만 유효합니다. 게이트웨이는 VNET에 있을 수 있습니다.

## <a name="configure-alwaysusegateway-property"></a>AlwaysUseGateway 속성 구성

1. SSMS > 서버 > **속성** > **일반**에서 **고급 속성 표시(모두)** 를 선택합니다.
2. **ASPaaS\AlwaysUseGateway**에서 **true**를 선택합니다.

    ![항상 게이트웨이 속성 사용](media/analysis-services-vnet-gateway/aas-ssms-always-property.png)


## <a name="see-also"></a>참고 항목
[온-프레미스 데이터 원본에 연결](analysis-services-gateway.md)   
[온-프레미스 데이터 게이트웨이 설치 및 구성](analysis-services-gateway-install.md)   
[Azure VNET(Virtual Network)](../virtual-network/virtual-networks-overview.md)   

