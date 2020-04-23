---
title: Azure 응용 프로그램 관리 응용 프로그램 제공 게시 가이드 | Azure 마켓플레이스
description: 이 문서에서는 Azure Marketplace에서 관리되는 응용 프로그램을 게시하는 요구 사항에 대해 설명합니다.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: dsindona
ms.openlocfilehash: 946e7524eada600d5ef17b2663a3fea066dcfaa2
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82084875"
---
# <a name="azure-applications-managed-application-offer-publishing-requirements"></a>Azure 응용 프로그램: 관리되는 응용 프로그램 제공 게시 요구 사항

이 문서에서는 Azure Marketplace에서 Azure 응용 프로그램 오퍼를 게시하는 한 가지 방법인 관리되는 응용 프로그램 제공 유형에 대한 요구 사항을 설명합니다. 관리되는 응용 프로그램은 Azure Marketplace를 통해 배포되고 요금이 청구되는 오퍼를 거래합니다. 사용자에게 표시되는 작업에 대한 호출은 "지금 가져오기"입니다.

다음 조건이 필요한 경우 관리되는 응용 프로그램 제공 유형을 사용합니다.

- VM 또는 전체 IaaS 기반 솔루션을 사용하여 고객을 위한 구독 기반 솔루션을 배포합니다.
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
>이제 CSP(클라우드 솔루션 공급자) 파트너 채널 옵트인을 사용할 수 있습니다. Microsoft CSP 파트너 채널을 통한 오퍼 마케팅에 대한 자세한 내용은 [클라우드 솔루션 공급자](./cloud-solution-providers.md)를 참조하십시오.

## <a name="next-steps"></a>다음 단계

- 아직 완료하지 않은 경우 Azure 마켓플레이스에 대해 [알아봅니다.](https://azuremarketplace.microsoft.com/sell)
- [파트너 센터에 로그인하여](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) 쿠폰을 만들거나 완료하세요.
- 자세한 [내용은 Azure 응용 프로그램 오퍼를 만듭니다.](./partner-center-portal/create-new-azure-apps-offer.md)
