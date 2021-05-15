---
title: Analysis Services 네트워크 연결에 대한 자주 묻는 질문과 대답 | Microsoft Docs
description: 이 문서에서는 Analysis Services 네트워크 연결에 대한 몇 가지 일반적인 질문에 대한 답변을 제공합니다.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/05/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: b60cf34e8efed2ed63b6e35cfaf7445edb701610
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "82838504"
---
# <a name="frequently-asked-questions-about-analysis-services-network-connectivity"></a>Analysis Services 네트워크 연결과 관련된 질문과 대답

이 문서에서는 스토리지 계정, 데이터 원본, 방화벽, IP 주소에 연결하는 방법과 관련된 일반적인 질문에 대한 답변을 제공합니다.

## <a name="backup-and-restore"></a>백업 및 복원

**질문** - 방화벽 뒤에 있는 스토리지 계정을 사용하여 백업하고 복원하려면 어떻게 해야 하나요?   
**답변** - Azure Analysis Services는 고정 IP 주소 또는 서비스 태그를 사용하지 않습니다. Analysis Services 서버에서 사용하는 IP 주소 범위는 *Azure 지역* 에 대한 IP 주소 범위에 있는 모든 것이 될 수 있습니다. 서버 IP 주소는 변할 수 있으며 시간이 지남에 따라 변화하므로 방화벽 규칙은 서버가 있는 Azure 지역 IP 주소의 전체 범위를 허용해야 합니다.

**질문** - 내 Azure 스토리지 계정은 내 Analysis Services 서버와 다른 지역에 있습니다. 스토리지 계정 방화벽 설정 구성을 어떻게 할까요?   
**답변** - 스토리지 계정이 다른 지역에 있는 경우 **선택한 네트워크** 에서 액세스를 허용하는 스토리지 계정 방화벽 설정을 구성합니다. 방화벽 **주소 범위** 에서 Analysis Services 서버가 있는 지역의 IP 주소 범위를 지정합니다. Azure 지역에 대한 IP 범위를 가져오려면 [Azure IP 범위 및 서비스 태그 – 퍼블릭 클라우드](https://www.microsoft.com/download/details.aspx?id=56519)를 참조하세요. 모든 네트워크에서의 액세스를 허용하는 스토리지 계정 방화벽 설정 구성이 지원되지만 선택한 네트워크를 선택하고 IP 주소 범위를 지정하는 것이 좋습니다. 

**질문** - 내 Azure 스토리지 계정은 내 Analysis Services 서버와 동일한 지역에 있습니다. 스토리지 계정 방화벽 설정 구성을 어떻게 할까요?   
**답변** – Analysis Services 서버와 스토리지 계정이 동일한 지역에 있으므로 두 네트워크 간의 통신은 내부 IP 주소 범위를 사용합니다. 따라서 선택한 네트워크를 사용하고 IP 주소 범위를 지정하도록 방화벽을 구성하는 것은 지원되지 않습니다. 조직 정책에 방화벽이 필요한 경우 모든 네트워크에서 액세스할 수 있도록 구성해야 합니다.


## <a name="data-source-connections"></a>데이터 원본 연결

**질문** - 데이터 원본 시스템에 대한 VNET이 있습니다. 내 Analysis Services 서버에서 VNET의 데이터베이스에 액세스할 수 있도록 허용하려면 어떻게 해야 하나요?   
**답변** - Azure Analysis Services는 VNET에 조인할 수 없습니다. 가장 좋은 방법은 VNET에서 온-프레미스 데이터 게이트웨이를 설치하고 구성한 다음 **AlwaysUseGateway** 서버 속성을 사용하여 Analysis Services 서버를 구성하는 것입니다. 자세한 내용은 [Azure VNet(Virtual Network)에서 데이터 원본에 게이트웨이 사용](analysis-services-vnet-gateway.md)을 참조하세요.

**질문** - 방화벽 뒤에 원본 데이터베이스가 있습니다. 내 Analysis Services 서버에서 액세스할 수 있도록 방화벽을 구성하려면 어떻게 할까요?   
**답변** - Azure Analysis Services는 고정 IP 주소 또는 서비스 태그를 사용하지 않습니다. Analysis Services 서버에서 사용하는 IP 주소 범위는 *Azure 지역* 에 대한 IP 주소 범위에 있는 모든 것이 될 수 있습니다. 원본 데이터베이스 방화벽 규칙에서 서버의 Azure 지역에 대한 *전체 범위* 의 IP 주소를 제공해야 합니다. 또 다른 방법은 온-프레미스 데이터 게이트웨이를 구성하는 것입니다. 그런 다음 [AlwaysUseGateway 서버 속성](analysis-services-vnet-gateway.md#configure-alwaysusegateway-property)을 사용하여 Analysis Services 서버를 구성하고 온-프레미스 데이터 게이트웨이가 데이터 원본의 방화벽 규칙에서 허용하는 IP 주소를 가지도록 할 수 있습니다.

## <a name="azure-apps-with-ip-address"></a>IP 주소를 사용하는 Azure 앱

**질문** - 즉시 변경되는 IP 주소와 함께 Azure 기반 애플리케이션(예: Azure Functions, Azure Data Factory)을 사용합니다. 앱이 실행되는 IP 주소를 동적으로 허용하도록 Azure Analysis Services 방화벽 규칙을 관리하려면 어떻게 해야 하나요?   
**답변** - Azure Analysis Services는 프라이빗 링크, VNET 또는 서비스 태그를 지원하지 않습니다. 클라이언트 애플리케이션의 IP 주소를 검색하고 방화벽 규칙을 자동으로 업데이트하는 일부 오픈 소스 솔루션(예: https://github.com/mathwro/Scripts/blob/master/Azure/AllowAzure-AnalysisServer.ps1) 이 있습니다.


## <a name="next-steps"></a>다음 단계

[온-프레미스 데이터 게이트웨이 설치 및 구성](analysis-services-gateway-install.md)   
[온-프레미스 데이터 게이트웨이를 사용하여 온-프레미스 데이터 원본에 연결](analysis-services-gateway.md)   
[Azure VNet(Virtual Network)에서 데이터 원본에 게이트웨이 사용](analysis-services-vnet-gateway.md)
