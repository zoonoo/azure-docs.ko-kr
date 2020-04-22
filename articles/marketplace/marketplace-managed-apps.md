---
title: Azure 애플리케이션 관리되는 애플리케이션 제품 게시 가이드
description: 이 아티클에서는 관리되는 애플리케이션을 Marketplace에 게시하기 위한 요구 사항을 설명합니다.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: dsindona
ms.openlocfilehash: 56281ff8faba6b4b950334e2b0018d48c8e7aeb3
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687572"
---
# <a name="azure-applications-managed-application-offer-publishing-guide"></a>Azure 애플리케이션: 관리되는 애플리케이션 제품 게시 가이드

관리되는 애플리케이션은 Marketplace에서 솔루션을 게시하는 주요 방법 중 하나입니다. 이 가이드를 사용하여 이 제품에 대한 요구 사항을 이해합니다. 

마켓플레이스를 통해 배포되고 청구되는 거래 제안입니다. 사용자에게 표시되는 작업에 대한 호출은 "지금 가져오기"입니다.

다음 조건이 필요한 경우 Azure 앱: 관리되는 앱 제품 형식을 사용합니다.
- VM 또는 전체 IaaS 기반 솔루션을 사용하여 고객에게 구독 기반 솔루션을 배포합니다.
- 사용자 또는 고객이 솔루션을 파트너가 관리하도록 요구합니다.

>[!NOTE]
>예를 들어, 파트너는 SI 또는 MSP(관리 서비스 제공자)일 수 있습니다.  

## <a name="managed-application-offer"></a>관리되는 애플리케이션 제품

|요구 사항 |세부 정보  |
|---------|---------|
|고객의 Azure 구독에 배포 | 관리되는 앱은 고객의 구독에 배포되어야 하며 타사에서 관리할 수 있습니다. | 
|청구 및 계량    |  리소스는 고객의 Azure 구독에 프로비전됩니다. 종량제(PAYGO) 가상 머신은 고객의 Azure 구독(PAYGO)을 통해 청구되는 Microsoft를 통해 고객과 거래됩니다. <br> 사용자 라이선스를 가져오는 경우 Microsoft는 고객 구독에서 발생하는 인프라 비용을 청구하지만 소프트웨어 라이선스 요금을 고객에게 직접 거래합니다.        |
|Azure 호환 VHD(가상 하드 디스크)    |   VM은 Windows 또는 Linux에서 빌드해야 합니다.<ul> <ul> <li>Linux VHD를 만드는 방법에 대한 자세한 내용은 [Azure 보증 Linux 배포판](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)을 참조하세요.</li> <li>Windows VHD 를 만드는 데 대한 자세한 내용은 [Azure 응용 프로그램 오퍼 만들기를](./partner-center-portal/create-new-azure-apps-offer.md)참조하십시오.</li> </ul> |

>[!NOTE]
> 관리되는 앱은 마켓플레이스를 통해 배포 가능해야 합니다. 고객 커뮤니케이션이 중요한 경우, 잠재 고객 공유를 사용하도록 설정한 후 관심 있는 고객에게 연결해야 합니다.  

>[!Note]
>이제 CSP(클라우드 솔루션 공급자) 파트너 채널 옵트인을 사용할 수 있습니다.  Microsoft CSP 파트너 채널을 통한 오퍼 마케팅에 대한 자세한 내용은 [클라우드 솔루션 공급자를](./cloud-solution-providers.md) 참조하십시오.

## <a name="next-steps"></a>다음 단계

아직 수행하지 않았다면 

- 마켓플레이스에 대해 [알아보세요.](https://azuremarketplace.microsoft.com/sell)

파트너 센터에 등록하려면 새 오퍼를 만들거나 기존 오퍼를 작성하기 시작합니다.

- [파트너 센터에 로그인하여](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) 쿠폰을 만들거나 완료하세요.
- 자세한 [내용은 Azure 응용 프로그램 오퍼 만들기를](./partner-center-portal/create-new-azure-apps-offer.md) 참조하십시오.
