---
title: Azure Marketplace에서 가상 머신에 대 한 게시 가이드
description: 이 문서에서는 Azure Marketplace에서 배포할 가상 컴퓨터 및 소프트웨어 무료 평가판을 게시 하기 위한 요구 사항을 설명 합니다.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 09/04/2020
ms.openlocfilehash: cc6b040731cbeb7271d7a7c0de1c32fa2d007013
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2020
ms.locfileid: "89484191"
---
# <a name="publishing-guide-for-virtual-machine-offers"></a>가상 컴퓨터 제품 게시 가이드

VM (가상 컴퓨터) 이미지 게시는 Azure Marketplace에 솔루션을 게시 하는 주요 방법 중 하나입니다. 이 가이드를 사용 하 여이 제품 유형에 대 한 요구 사항을 이해 합니다. 

가상 컴퓨터 제공은 Azure Marketplace를 통해 배포 되 고 청구 되는 트랜잭션 제품입니다. 사용자에 게 표시 되는 목록 옵션은 *지금 가져옵니다*.

## <a name="free-trial"></a>평가판 

사용자가 제품을 테스트할 수 있도록 정렬 하려면 BYOL (사용자 라이선스 보유) 청구 모델을 사용 하는 경우 제한 된 소프트웨어 라이선스에 액세스 합니다. 

## <a name="test-drive"></a>시험 사용

IaaS (infrastructure as a service) 또는 SaaS (software as a service) 앱을 통해 하나 이상의 가상 머신을 배포할 수 있습니다. *테스트 드라이브* 게시 옵션의 혜택은 파트너 호스팅 둘러보기에 의해 가상 머신 또는 전체 솔루션의 자동화 된 설정입니다. 시험 사용을 통해 고객은 추가 비용 없이 Vm을 평가할 수 있습니다. 고객이 평가판 환경에 참여 하기 위해 기존 Azure 고객이 아니어도 됩니다. 

테스트 드라이브에 대 한 자세한 내용은 [테스트 드라이브 란?](what-is-test-drive.md) 을 참조 하세요.

|요구 사항  |세부 정보 |
|---------|---------|
| Azure Marketplace 앱이 있습니다.   |  IaaS 또는 SaaS를 통한 하나 이상의 가상 머신이 있습니다.      |

## <a name="interactive-demo"></a>대화형 데모

이 제품을 통해 고객에 게 대화형 데모를 사용 하 여 솔루션에 대 한 안내 환경을 제공할 수 있습니다. 대화형 데모 게시 옵션의 혜택은 복잡 한 솔루션을 복잡 하 게 설정 하지 않고도 평가판 환경을 제공할 수 있다는 것입니다. 

## <a name="virtual-machine-offer"></a>가상 머신 제안

사용자와 연결 된 구독에 가상 어플라이언스를 배포할 때 *가상 머신* 제품 유형을 사용 합니다. Vm은 종 량 제 또는 BYOL (사용자 라이선스 보유) 라이선스 모델을 사용 하 여 완벽 하 게 상거래를 지원 합니다. Microsoft는 사용자 대신, 상거래를 주도하고 고객에게 요금을 청구합니다. 사용자는 기업계약을 비롯하여 고객과 Microsoft 간에 선호하는 결제 방식을 채택할 수 있습니다.

> [!NOTE]
> 현재 기업계약와 관련 된 금액 약정은 VM의 Azure 사용에 대해 사용할 수 있지만 소프트웨어 라이선스 요금에 대해서는 사용할 수 없습니다.  
> 
> [!NOTE]
> 이미지와 가격을 개인 제품으로 게시 하 여 VM의 검색 및 배포를 특정 고객 집합으로 제한할 수 있습니다. 비공개 제품은 가장 가까운 고객을 위한 독점적인 제품을 만들고 사용자 지정 된 소프트웨어 및 조건을 제공 하는 기능을 잠금 해제 합니다. 이러한 사용자 지정된 사용 약관을 사용하면 사용자가 전문화된 가격 책정 및 조건뿐 아니라 제한된 릴리스 소프트웨어에 대한 초기 액세스를 사용하여 선도적인 거래를 포함한 다양한 시나리오를 강조 표시할 수 있습니다. 개인 제품을 사용 하면 해당 세부 정보를 사용 하 여 새 계획을 만들어 제한 된 고객 집합에 특정 가격 책정 또는 제품을 제공할 수 있습니다.  
>
> 자세한 내용은 [Azure Marketplace에서 비공개 제품](https://azure.microsoft.com/blog/private-offers-on-azure-marketplace)을 참조 하세요.  

| 요구 사항 | 세부 정보 |  
|:--- |:--- | 
| 청구 및 계량 | VM은 BYOL 또는 종 량 제 월별 요금 청구를 지원 해야 합니다. |  
| Azure 호환 VHD(가상 하드 디스크) | VM은 Windows 또는 Linux에서 빌드해야 합니다. VHD를 만드는 방법에 대 한 자세한 내용은 다음을 참조 하세요. <ul> <li>[Azure에서 linux 배포판 보증](../virtual-machines/linux/endorsed-distros.md) (linux vhd의 경우).</li> <li>[Azure 호환 VHD 만들기](./partner-center-portal/azure-vm-create-offer.md) (Windows vhd의 경우)</li> </ul> |  

>[!Note]
>이제 CSP (클라우드 솔루션 공급자) 파트너 채널 옵트인을 사용할 수 있습니다. Microsoft CSP 파트너 채널을 통한 제품 마케팅에 대 한 자세한 내용은 [클라우드 솔루션 공급자](./cloud-solution-providers.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

아직 수행하지 않은 경우 [Azure Marketplace로 클라우드 비즈니스를 확장](https://azuremarketplace.microsoft.com/sell)하는 방법을 알아보세요.

파트너 센터에서 등록하고 작업을 시작하려면

- 제품을 만들거나 완성할 수 있도록 [파트너 센터에 로그인](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership)합니다.
- 자세한 내용은 [가상 머신 제품 만들기](./partner-center-portal/azure-vm-create-offer.md) 를 참조 하세요.
