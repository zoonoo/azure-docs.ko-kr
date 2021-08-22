---
title: Azure 애플리케이션 솔루션 템플릿 제품에 대한 게시 가이드 - Azure Marketplace
description: 이 문서에서는 Azure Marketplace에서 솔루션 템플릿을 게시하기 위한 요구 사항을 설명합니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: msjogarrig
ms.author: jogarrig
ms.date: 07/01/2021
ms.openlocfilehash: 5090b5e4c34e70146182fbd472b7e24f9f919c15
ms.sourcegitcommit: 285d5c48a03fcda7c27828236edb079f39aaaebf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2021
ms.locfileid: "113231290"
---
# <a name="publishing-guide-for-azure-applications-solution-template-offers"></a>Azure 애플리케이션 솔루션 템플릿 제품에 대한 게시 가이드

이 문서에서는 Azure Marketplace에서 Azure 애플리케이션 제품을 게시하는 한가지 방법인 솔루션 템플릿 제품을 게시하기 위한 요구 사항을 설명합니다. 솔루션 템플릿 제품 유형에는 솔루션 인프라를 자동으로 배포하기 위해 [ARM 템플릿(Azure Resource Manager 템플릿)](../azure-resource-manager/templates/overview.md)이 필요합니다.

다음 조건에서 Azure 애플리케이션 *솔루션 템플릿* 제품 유형을 사용합니다.

- 솔루션에는 VM, 네트워킹 및 스토리지 리소스의 조합과 같은 단일 VM(가상 머신) 이상의 추가 배포 및 구성 자동화가 필요합니다.
- 고객은 솔루션 자체를 관리하려고 합니다.

이 제품 유형에 대해 고객에게 표시되는 목록 옵션은 *지금 가져오기* 입니다.

## <a name="requirements-for-solution-template-offers"></a>솔루션 템플릿 제품에 대한 요구 사항

| **요구 사항** | **세부 정보**  |
| ---------------  | -----------  |
|청구 및 계량    |  솔루션 템플릿 제품은 트랜잭션 제품이 아니지만 Microsoft 상업용 Marketplace를 통해 청구되는 유료 VM 제품을 배포하는 데 사용할 수 있습니다. 솔루션의 ARM 템플릿에서 배포하는 리소스는 고객의 Azure 구독에 설정됩니다. 종량제 가상 머신은 Microsoft를 통해 고객과 거래되고 고객의 Azure 구독을 통해 청구됩니다.<br/> BYOL(사용자 라이선스 필요) 청구의 경우 Microsoft가 고객 구독에서 발생하는 인프라 비용을 청구하지만 소프트웨어 라이선스 요금은 고객과 직접 거래합니다.   |
|Azure 호환 VHD(가상 하드 디스크)  |   VM은 Windows 또는 Linux에서 빌드해야 합니다. 자세한 내용은 다음을 참조하세요. <ul> <li>[Azure 애플리케이션 제품 만들기](azure-app-offer-setup.md)(Windows VHD용).</li><li>[Azure에서 보증된 Linux 배포](../virtual-machines/linux/endorsed-distros.md)(Linux VHD용)</li></ul> |
| 고객 사용량 특성 | 고객 사용량 특성 활성화는 Azure Marketplace에 게시된 모든 솔루션 템플릿에 필요합니다. 고객 사용 특성 및 사용 설정 방법에 대한 자세한 내용은 [Azure 파트너 고객 사용 특성](./azure-partner-customer-usage-attribution.md)을 참조하세요.  |
| 관리 디스크 사용 | [관리 디스크](../virtual-machines/managed-disks-overview.md)는 Azure에서 IaaS(Infrastructure as a Service) VM의 영구 디스크에 대한 기본 옵션입니다. 솔루션 템플릿에서 관리 디스크를 사용해야 합니다. <ul><li>솔루션 템플릿을 업데이트하려면 [Azure Resource Manager 템플릿에서 관리 디스크 사용](../virtual-machines/using-managed-disks-template-deployments.md)의 지침을 따르고 제공된 [샘플](https://github.com/Azure/azure-quickstart-templates)을 사용합니다.<br><br> </li><li>Azure Marketplace 이미지로 VHD를 게시하려면 다음 방법 중 하나를 사용하여 관리 디스크의 기본 VHD를 스토리지 계정으로 가져옵니다.<ul><li>[Azure PowerShell](/previous-versions/azure/virtual-machines/scripts/virtual-machines-powershell-sample-copy-managed-disks-vhd) </li> <li> [Azure CLI](/previous-versions/azure/virtual-machines/scripts/virtual-machines-cli-sample-copy-managed-disks-vhd) </li> </ul></ul> |

## <a name="next-steps"></a>다음 단계

아직 수행하지 않은 경우 [Azure Marketplace로 클라우드 비즈니스를 확장](https://azuremarketplace.microsoft.com/sell)하는 방법을 알아보세요.

파트너 센터에서 등록하고 작업을 시작하려면

- 제품을 만들거나 완성할 수 있도록 [파트너 센터에 로그인](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership)합니다.
- 자세한 내용은 [Azure 애플리케이션 제품 만들기](./azure-app-offer-setup.md)를 참조하세요.
