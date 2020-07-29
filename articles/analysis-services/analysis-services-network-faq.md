---
title: 네트워크 연결 Analysis Services에 대 한 질문과 대답 | Microsoft Docs
description: 이 문서에서는 Analysis Services 네트워크 연결에 대 한 몇 가지 일반적인 질문에 대 한 답변을 제공 합니다.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/05/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: b60cf34e8efed2ed63b6e35cfaf7445edb701610
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "82838504"
---
# <a name="frequently-asked-questions-about-analysis-services-network-connectivity"></a>네트워크 연결 Analysis Services에 대 한 질문과 대답

이 문서에서는 저장소 계정, 데이터 원본, 방화벽 및 IP 주소에 연결 하는 방법에 대 한 일반적인 질문에 대 한 답변을 제공 합니다.

## <a name="backup-and-restore"></a>백업 및 복원

**질문** -방화벽 뒤에 있는 저장소 계정을 사용 하 여 백업 및 복원 하려면 어떻게 해야 하나요?   
**답변** -Azure Analysis Services는 고정 IP 주소 또는 서비스 태그를 사용 하지 않습니다. Analysis Services 서버에서 사용 하는 IP 주소 범위는 *Azure 지역*에 대 한 ip 주소 범위에 모두 있을 수 있습니다. 서버 IP 주소는 변수이 고 시간이 지남에 따라 변경 될 수 있기 때문에, 방화벽 규칙은 서버가 있는 전체 범위의 Azure 지역 IP 주소를 허용 해야 합니다.

**질문** -내 Azure storage 계정은 내 Analysis Services 서버와 다른 지역에 있습니다. 저장소 계정 방화벽 설정을 구성 어떻게 할까요??   
**응답** -저장소 계정이 다른 지역에 있는 경우 **선택한 네트워크**에서 액세스할 수 있도록 저장소 계정 방화벽 설정을 구성 합니다. 방화벽 **주소 범위**에서 Analysis Services 서버가 있는 지역의 IP 주소 범위를 지정 합니다. Azure 지역에 대 한 IP 범위를 가져오려면 [AZURE IP 범위 및 서비스 태그 – 공용 클라우드](https://www.microsoft.com/download/details.aspx?id=56519)를 참조 하세요. 모든 네트워크에서 액세스할 수 있도록 저장소 계정 방화벽 설정을 구성 하는 것이 지원 되지만 선택한 네트워크를 선택 하 고 IP 주소 범위를 지정 하는 것이 좋습니다. 

**질문** -내 Azure storage 계정은 내 Analysis Services 서버와 동일한 지역에 있습니다. 저장소 계정 방화벽 설정을 구성 하려면 어떻게 해야 하나요?   
**응답** – Analysis Services 서버와 저장소 계정이 동일한 지역에 있으므로 두 네트워크 간의 통신은 내부 ip 주소 범위를 사용 하므로 선택한 네트워크를 사용 하 고 IP 주소 범위를 지정 하도록 방화벽을 구성 하는 것은 지원 되지 않습니다. 조직 정책에 방화벽이 필요한 경우 모든 네트워크에서 액세스할 수 있도록 구성 해야 합니다.


## <a name="data-source-connections"></a>데이터 원본 연결

**질문** -데이터 원본 시스템에 대 한 VNET이 있습니다. 내 Analysis Services 서버에서 VNET의 데이터베이스에 액세스할 수 있도록 허용 하려면 어떻게 해야 하나요?   
**응답** Azure Analysis Services VNET에 가입할 수 없습니다. 여기에서 가장 좋은 방법은 VNET에서 온-프레미스 데이터 게이트웨이를 설치 및 구성한 다음 **AlwaysUseGateway** server 속성을 사용 하 여 Analysis Services 서버를 구성 하는 것입니다. 자세히 알아보려면 [Azure Virtual Network (VNet)에서 데이터 원본에 대 한 게이트웨이 사용](analysis-services-vnet-gateway.md)을 참조 하세요.

**질문** -방화벽 뒤에 원본 데이터베이스가 있습니다. 내 Analysis Services 서버에서 액세스할 수 있도록 방화벽을 구성 하려면 어떻게 해야 하나요?   
**답변** -Azure Analysis Services는 고정 IP 주소 또는 서비스 태그를 사용 하지 않습니다. Analysis Services 서버에서 사용 하는 IP 주소 범위는 *Azure 지역*에 대 한 ip 주소 범위에 모두 있을 수 있습니다. 원본 데이터베이스 방화벽 규칙에서 서버의 Azure 지역에 대 한 *전체 범위의* IP 주소를 제공 해야 합니다. 또 다른 방법은 온-프레미스 데이터 게이트웨이를 구성 하는 것입니다. 그런 다음 [AlwaysUseGateway 서버 속성](analysis-services-vnet-gateway.md#configure-alwaysusegateway-property)을 사용 하 여 Analysis Services 서버를 구성 하 고 온-프레미스 데이터 게이트웨이가 데이터 원본의 방화벽 규칙에서 허용 하는 IP 주소를 갖도록 할 수 있습니다.

## <a name="azure-apps-with-ip-address"></a>IP 주소를 사용 하는 Azure 앱

**질문** -Azure 기반 응용 프로그램 (예: Azure Functions, Azure Data Factory)을 사용 하 여 즉시 변경 하는 IP 주소를 사용 합니다. 앱이 실행 되는 IP 주소를 동적으로 허용 하도록 Azure Analysis Services 방화벽 규칙을 관리 하려면 어떻게 해야 하나요?   
**답변** -Azure Analysis Services은 개인 링크, Vnet 또는 서비스 태그를 지원 하지 않습니다. 예를 들어 https://github.com/mathwro/Scripts/blob/master/Azure/AllowAzure-AnalysisServer.ps1) 클라이언트 응용 프로그램의 IP 주소를 검색 하 고 방화벽 규칙을 자동으로 업데이트 하는 몇 가지 오픈 소스 솔루션이 있습니다.


## <a name="next-steps"></a>다음 단계

[온-프레미스 데이터 게이트웨이 설치 및 구성](analysis-services-gateway-install.md)   
[온-프레미스 데이터 게이트웨이를 사용 하 여 온-프레미스 데이터 원본에 연결](analysis-services-gateway.md)   
[Azure VNet(Virtual Network)에서 데이터 원본에 게이트웨이 사용](analysis-services-vnet-gateway.md)
