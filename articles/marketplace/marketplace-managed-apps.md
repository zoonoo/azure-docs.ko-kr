---
title: Azure 응용 프로그램 관리 되는 응용 프로그램 제공 게시 가이드-Azure Marketplace
description: 이 문서에서는 Azure Marketplace에 관리 되는 응용 프로그램을 게시 하기 위한 요구 사항을 설명 합니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: msjogarrig
ms.author: jogarrig
ms.date: 09/04/2020
ms.openlocfilehash: 10ca7602dd3cf5a80e371ceda845977bcd8f218a
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2020
ms.locfileid: "89484242"
---
# <a name="publishing-guide-for-azure-managed-applications"></a>Azure 관리 되는 응용 프로그램 게시 가이드

Azure 관리 되는 *응용* 프로그램 제품은 Azure Marketplace에 azure 응용 프로그램을 게시 하는 한 가지 방법입니다. 관리 되는 응용 프로그램은 Azure Marketplace를 통해 배포 되 고 청구 되는 transact-sql 제품입니다. 사용자에 게 표시 되는 목록 옵션은 *지금 가져옵니다*.

이 문서에서는 관리 되는 응용 프로그램 제공 형식에 대 한 요구 사항을 설명 합니다.

다음 조건에서 관리 되는 응용 프로그램 제품 유형을 사용 합니다.

- VM (가상 머신) 또는 전체 IaaS (infrastructure as a service) 기반 솔루션을 사용 하 여 고객을 위한 구독 기반 솔루션을 배포 하 고 있습니다.
- 사용자 또는 고객은 파트너에 의해 솔루션을 관리 해야 합니다.

>[!NOTE]
>예를 들어 파트너는 시스템 통합자 또는 MSP (관리 서비스 공급자) 일 수 있습니다.  

## <a name="managed-application-offer-requirements"></a>관리 되는 응용 프로그램 제공 요구 사항

|요구 사항 |세부 정보  |
|---------|---------|
|Azure 구독 | 관리 되는 응용 프로그램은 고객의 구독에 배포 되어야 하지만 타사에서 관리할 수 있습니다. |
|청구 및 계량    |  리소스는 고객의 Azure 구독에서 제공 됩니다. 종 량 제 지불 모델을 사용 하는 Vm은 Microsoft를 통해 고객과 거래 되며 고객의 Azure 구독을 통해 청구 됩니다. <br><br> 사용자 라이선스 Vm의 경우 Microsoft는 고객 구독에서 발생 하는 모든 인프라 비용을 청구 하지만 고객과 직접 소프트웨어 라이선스 요금을 지불 합니다.        |
|Azure 호환 VHD (가상 하드 디스크)    |   VM은 Windows 또는 Linux에서 빌드해야 합니다.<br><br>Linux VHD를 만드는 방법에 대한 자세한 내용은 [Azure 보증 Linux 배포판](../virtual-machines/linux/endorsed-distros.md)을 참조하세요.<br><br>Windows VHD를 만드는 방법에 대 한 자세한 내용은 [Azure 응용 프로그램 제품 만들기](./partner-center-portal/create-new-azure-apps-offer.md)를 참조 하세요. |

---

> [!NOTE]
> 관리 되는 응용 프로그램은 Azure Marketplace를 통해 배포 가능 해야 합니다. 고객 통신이 중요 한 경우 잠재 고객 공유를 사용 하도록 설정한 후 관심 있는 고객에 게 연락 하세요.  

> [!Note]
> 이제 CSP (클라우드 솔루션 공급자) 파트너 채널 옵트인 (opt in)을 사용할 수 있습니다. Microsoft CSP 파트너 채널을 통한 제품 마케팅에 대 한 자세한 내용은 [클라우드 솔루션 공급자](./cloud-solution-providers.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

아직 수행하지 않은 경우 [Azure Marketplace로 클라우드 비즈니스를 확장](https://azuremarketplace.microsoft.com/sell)하는 방법을 알아보세요.

파트너 센터에서 등록하고 작업을 시작하려면

- 제품을 만들거나 완성할 수 있도록 [파트너 센터에 로그인](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership)합니다.
- 자세한 내용은 [Azure 응용 프로그램 제품 만들기](./partner-center-portal/create-new-azure-apps-offer.md) 를 참조 하세요.
