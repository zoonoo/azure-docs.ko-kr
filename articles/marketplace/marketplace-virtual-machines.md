---
title: Azure Marketplace에 대한 Virtual Machine 제품 게시 가이드
description: 이 아티클에서는 Marketplace에서 배포할 가상 머신 및 소프트웨어 평가판을 게시하기 위한 요구 사항을 설명합니다.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: dsindona
ms.openlocfilehash: 2fa67d81546db86535c179a9c59d0602c1175cba
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687508"
---
# <a name="virtual-machine-offer-publishing-guide"></a>Virtual Machines 제품 게시 가이드

Virtual Machines 이미지는 Azure Marketplace에서 솔루션을 게시하는 주요 방법 중 하나입니다. 이 가이드를 사용하여 이 제품에 대한 요구 사항을 이해합니다. 

Marketplace를 통해 배포되고 청구되는 트랜잭션 제품은 다음과 같습니다. 사용자에게 표시되는 작업에 대한 호출은 "지금 가져오기"입니다.

## <a name="free-trial"></a>평가판 

BYOL(사용자 라이선스 필요) 청구 모델을 사용할 때 한정된 기간 소프트웨어 라이선스에 액세스하여 제품을 테스트하도록 사용자를 위해 정렬할 수 있습니다. 

## <a name="test-drive"></a>시험 사용

IaaS(Infrastructure-as-a-Service) 또는 SaaS(Software-as-a-Service) 앱을 통해 하나 이상의 가상 머신을 배포합니다. 시험 사용 게시 옵션의 이점은 파트너가 호스트하는 둘러보기를 통해 이루어지는 가상 머신 또는 전체 솔루션의 자동화된 프로비전이 가능하다는 것입니다. 시험 사용은 추가 비용 없이 고객에게 평가를 제공합니다. 고객은 평가판 환경을 사용하기 위해 기존 Azure 고객일 필요가 없습니다. 

시작하려면 [amp-testdrive](mailto:amp-testdrive@microsoft.com)를 통해 문의하세요. 

|요구 사항  |세부 정보 |
|---------|---------|
| Marketplace 앱이 있습니다.   |    IaaS 또는 SaaS를 통한 하나 이상의 가상 머신이 있습니다.      |

## <a name="interactive-demo"></a>대화형 데모

대화형 데모를 사용하여 솔루션의 안내 방식 환경을 고객에게 제공합니다. 대화형 데모 게시 옵션의 이점은 복잡한 솔루션에 대한 복잡한 프로비전 없이도 평가판 환경을 제공한다는 것입니다. 

## <a name="virtual-machine-offer"></a>Virtual Machine 제품

고객과 연결된 구독에 가상 어플라이언스를 배포할 경우 가상 머신 제품 형식을 사용합니다. VM은 종량제 또는 BYOL(사용자 라이선스 필요) 라이선스 모델을 사용하여 완전하게 거래될 수 있습니다. Microsoft는 사용자 대신, 상거래를 주도하고 고객에게 요금을 청구합니다. 사용자는 기업계약을 비롯하여 고객과 Microsoft 간에 선호하는 결제 방식을 채택할 수 있습니다.

> [!NOTE]
> 현재 기업계약과 연결된 금액 약정은 VM의 Azure 사용량에 대해 사용할 수 있지만, 소프트웨어 라이선스 요금에는 사용할 수 없습니다.  
> 
> [!NOTE]
> 이미지를 게시하고 프라이빗 제품으로 가격 책정하여 고객의 특정 집합으로 VM의 배포 및 검색을 제한할 수 있습니다. 프라이빗 제품은 사용자의 능력을 풀어주어 가장 가까운 고객에게 독점적인 혜택을 만들고 사용자 지정된 소프트웨어 및 사용 약관을 제공합니다. 이러한 사용자 지정된 사용 약관을 사용하면 사용자가 전문화된 가격 책정 및 조건뿐 아니라 제한된 릴리스 소프트웨어에 대한 초기 액세스를 사용하여 선도적인 거래를 포함한 다양한 시나리오를 강조 표시할 수 있습니다. 프라이빗 제품을 통해 이러한 세부 정보로 새 SKU를 만들어 특정 가격 책정 또는 제품을 제한된 고객 집합에게 제공할 수 있습니다.  
> *   프라이빗 제품에 대한 자세한 내용은 [azure.microsoft.com/blog/private-offers-on-azure-marketplace](https://azure.microsoft.com/blog/private-offers-on-azure-marketplace)에 있는 Azure Marketplace의 프라이빗 제품 페이지를 참조하세요.  

| 요구 사항 | 세부 정보 |  
|:--- |:--- | 
| 청구 및 계량 | VM은 BYOL 또는 종량제 월간 청구를 지원해야 합니다. |  
| Azure 호환 VHD(가상 하드 디스크) | VM은 Windows 또는 Linux에서 빌드해야 합니다. <ul> <li>Linux VHD를 만드는 방법에 대한 자세한 내용은 [Azure 보증 Linux 배포판](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)을 참조하세요.</li> <li>Windows VHD를 만드는 방법에 대한 자세한 내용은 [Azure 호환 VHD 만들기](./partner-center-portal/azure-vm-create-offer.md)를 참조하세요.</li> </ul> |  

>[!Note]
>이제 CSP(클라우드 솔루션 공급자) 파트너 채널 옵트인을 사용할 수 있습니다.  Microsoft CSP 파트너 채널을 통한 오퍼 마케팅에 대한 자세한 내용은 [클라우드 솔루션 공급자를](./cloud-solution-providers.md) 참조하십시오.

## <a name="next-steps"></a>다음 단계

아직 수행하지 않았다면 

- 마켓플레이스에 대해 [알아보세요.](https://azuremarketplace.microsoft.com/sell)

새 제품을 등록하고 만들거나 기존에서 작업하는 경우

- [파트너 센터에 로그인하여](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) 쿠폰을 만들거나 완료하세요.
- 자세한 내용은 [가상 시스템 오퍼 만들기를](./partner-center-portal/azure-vm-create-offer.md) 참조하십시오.
