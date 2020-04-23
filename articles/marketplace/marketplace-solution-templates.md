---
title: Azure 응용 프로그램 솔루션 템플릿 제공 게시 가이드 | Azure 마켓플레이스
description: 이 문서에서는 솔루션 템플릿을 Azure Marketplace에 게시하기 위한 요구 사항을 설명합니다.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: dsindona
ms.openlocfilehash: 6533fa930716552c91fffd13b196bdbf78158816
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82084858"
---
# <a name="azure-applications-solution-template-offer-publishing-requirements"></a>Azure 응용 프로그램: 솔루션 템플릿제공 게시 요구 사항

이 문서에서는 Azure Marketplace에서 Azure 응용 프로그램 오퍼를 게시하는 한 가지 방법인 솔루션 템플릿 제공 유형에 대한 요구 사항을 설명합니다. 솔루션 템플릿 제공 유형에는 솔루션 인프라를 자동으로 배포하려면 [AZURE 리소스 관리자 템플릿(ARM 템플릿)이](../azure-resource-manager/templates/overview.md) 필요합니다.

다음 조건이 필요한 경우 Azure 응용 프로그램 솔루션 제공 형식을 사용합니다.

- 솔루션에는 VM, 네트워킹 및 스토리지 리소스의 조합과 같은 단일 VM 을 넘어서는 추가 배포 및 구성 자동화가 필요합니다.
- 고객은 솔루션 을 자체적으로 관리해야 합니다.

사용자가 이 쿠폰 유형에 대해 표시되는 클릭 유도문의 경우는 "지금 받기"입니다.

## <a name="requirements-for-solution-template-offers"></a>솔루션 템플릿 제공에 대한 요구 사항

| **요구 사항** | **세부 정보**  |
| ---------------  | -----------  |
|청구 및 계량    |  솔루션 템플릿 오퍼는 거래 제안이 아니지만 Microsoft 상용 마켓플레이스를 통해 청구되는 유료 VM 오퍼를 배포하는 데 사용할 수 있습니다. 솔루션의 ARM 템플릿이 배포하는 리소스는 고객의 Azure 구독에 프로비전됩니다. 종량제(PAYGO) 가상 머신은 고객의 Azure 구독을 통해 청구되는 Microsoft를 통해 고객과 거래됩니다.<br/> BYOL(사용자 라이선스 필요)의 경우 Microsoft가 고객 구독에서 발생하는 인프라 비용을 청구하는 한편, 소프트웨어 라이선스 요금은 고객과 직접 거래합니다.   |
|Azure 호환 VHD(가상 하드 디스크)  |   VM은 Windows 또는 Linux에서 빌드해야 합니다. 자세한 내용은 다음을 참조하세요. <ul> <li>[Azure 응용 프로그램 오퍼 만들기(Windows](./partner-center-portal/create-new-azure-apps-offer.md)VHD용)</li><li>[리눅스 배포판](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) (리눅스 VHDs에 대 한) Azure에 승인.</li></ul> |
| 고객 사용량 특성 | 고객 사용량 특성 활성화는 Azure Marketplace에 게시된 모든 솔루션 템플릿에 필요합니다. 고객 사용량 특성과 활성화 방법에 대한 자세한 내용은 [Azure 파트너 고객 사용량 특성](./azure-partner-customer-usage-attribution.md)을 참조하세요.  |
| Managed Disks 사용 | [관리되는 디스크는](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) Azure에서 IaaS VM의 지속 디스크에 대한 기본 옵션입니다. 솔루션 템플릿에서 관리 디스크를 사용해야 합니다. <br> <br> 1. Azure ARM 템플릿에서 관리디스크를 사용하여 솔루션 템플릿을 업데이트하는 방법에 대한 [지침](https://docs.microsoft.com/azure/virtual-machines/windows/using-managed-disks-template-deployments) 및 [샘플을](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md) 따릅니다. <br> <br> 2. 아래 지침에 따라 관리형 디스크의 기본 VHD를 저장소 계정으로 가져와 VHD를 마켓플레이스에 이미지로 게시합니다. <br> <ul> <li> [PowerShell](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-vhd?toc=%2fpowershell%2fmodule%2ftoc.json) </li> <li> [CLI](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-vhd?toc=%2fcli%2fmodule%2ftoc.json) </li> </ul> |

## <a name="next-steps"></a>다음 단계

- 아직 완료하지 않은 경우 Azure 마켓플레이스에 대해 [알아봅니다.](https://azuremarketplace.microsoft.com/sell)
- [파트너 센터에 로그인하여](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) 쿠폰을 만들거나 완료하세요.
- 자세한 [내용은 Azure 응용 프로그램 오퍼를 만듭니다.](./partner-center-portal/create-new-azure-apps-offer.md)
