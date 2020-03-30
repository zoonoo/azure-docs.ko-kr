---
title: VNet 데이터 원본에 대한 Azure 분석 서비스 구성 | 마이크로 소프트 문서
description: Azure 가상 네트워크(VNet)의 데이터 원본에 대한 게이트웨이를 사용하도록 Azure 분석 서비스 서버를 구성하는 방법에 대해 알아봅니다.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 384f4c2ee3021678c530aa5c98eaf4dbfd661e95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73572272"
---
# <a name="use-gateway-for-data-sources-on-an-azure-virtual-network-vnet"></a>Azure VNet(Virtual Network)에서 데이터 원본에 게이트웨이 사용

이 문서에서는 데이터 원본이 [Azure 가상 네트워크(VNet)에](../virtual-network/virtual-networks-overview.md)있을 때 사용할 Azure 분석 서비스 **AlwaysUseGateway** 서버 속성에 대해 설명합니다.

## <a name="server-access-to-vnet-data-sources"></a>VNet 데이터 원본에 대한 서버 액세스

VNet을 통해 데이터 원본에 액세스하는 경우 Azure Analysis Services 서버는 해당 데이터 원본이 사용자 환경의 온-프레미스에 있는 경우 해당 데이터 원본에 연결해야 합니다. **AlwaysUseGateway** 서버 속성을 구성하여 [온-프레미스 게이트웨이를](analysis-services-gateway.md)통해 모든 데이터 원본에 액세스할 수 있는 서버를 지정할 수 있습니다. 

Azure SQL 데이터베이스 관리 인스턴스 데이터 원본은 개인 IP 주소가 있는 Azure VNet 내에서 실행됩니다. 인스턴스에서 공용 끝점을 사용하도록 설정하면 게이트웨이가 필요하지 않습니다. 공용 끝점을 사용할 수 없는 경우 온-프레미스 데이터 게이트웨이가 필요하고 AlwaysUseGateway 속성을 true로 설정해야 합니다.

> [!NOTE]
> 이 속성은 [온-프레미스 데이터 게이트웨이를](analysis-services-gateway.md) 설치 하 고 구성 하는 경우에 효과적입니다. 게이트웨이는 VNet에 있을 수 있습니다.

## <a name="configure-alwaysusegateway-property"></a>AlwaysUseGateway 속성 구성

1. SSMS > 서버 > **속성** > **일반에서** **고급(모두) 속성 표시를**선택합니다.
2. **ASPaaS\AlwaysUseGateway**에서 **true**를 선택합니다.

    ![항상 게이트웨이 속성 사용](media/analysis-services-vnet-gateway/aas-ssms-always-property.png)


## <a name="see-also"></a>참조
[온-프레미스 데이터 원본에 연결](analysis-services-gateway.md)   
[온-프레미스 데이터 게이트웨이 설치 및 구성](analysis-services-gateway-install.md)   
[Azure VNET(Virtual Network)](../virtual-network/virtual-networks-overview.md)   

