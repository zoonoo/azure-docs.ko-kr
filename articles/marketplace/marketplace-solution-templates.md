---
title: Azure 응용 프로그램 솔루션 템플릿 제품 게시 가이드 | Azure Marketplace
description: 이 문서에서는 솔루션 템플릿을 Azure Marketplace에 게시하기 위한 요구 사항을 설명합니다.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: dsindona
ms.openlocfilehash: 6533fa930716552c91fffd13b196bdbf78158816
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82084858"
---
# <a name="azure-applications-solution-template-offer-publishing-requirements"></a>Azure 응용 프로그램: 솔루션 템플릿 제공 게시 요구 사항

이 문서에서는 Azure Marketplace에 Azure 응용 프로그램 제품을 게시 하는 한 가지 방법인 솔루션 템플릿 제품 유형에 대 한 요구 사항을 설명 합니다. 솔루션 템플릿 제품 형식에 솔루션 인프라를 자동으로 배포 하려면 [Azure Resource Manager 템플릿 (ARM 템플릿)](../azure-resource-manager/templates/overview.md) 이 필요 합니다.

다음 조건이 필요한 경우 Azure 응용 프로그램 솔루션 템플릿 제품 유형을 사용 합니다.

- 솔루션에는 Vm, 네트워킹 및 저장소 리소스 조합과 같은 단일 VM 이상의 추가 배포 및 구성 자동화가 필요 합니다.
- 고객이 솔루션 자체를 관리 하 게 될 예정입니다.

사용자가이 제안 유형에 대해 확인 하는 작업 호출은 "지금 가져오기"입니다.

## <a name="requirements-for-solution-template-offers"></a>솔루션 템플릿 제품에 대 한 요구 사항

| **요구 사항** | **세부 정보**  |
| ---------------  | -----------  |
|청구 및 계량    |  솔루션 템플릿 제품은 transact-sql 제품이 아니라 Microsoft 상업적 marketplace를 통해 청구 되는 유료 VM 제품을 배포 하는 데 사용할 수 있습니다. 솔루션의 ARM 템플릿이 배포 하는 리소스는 고객의 Azure 구독에서 프로 비전 됩니다. 종 량 제 (PAYGO) 가상 머신은 고객의 Azure 구독을 통해 청구 되는 Microsoft를 통해 고객에 게 거래 됩니다.<br/> BYOL(사용자 라이선스 필요)의 경우 Microsoft가 고객 구독에서 발생하는 인프라 비용을 청구하는 한편, 소프트웨어 라이선스 요금은 고객과 직접 거래합니다.   |
|Azure 호환 VHD(가상 하드 디스크)  |   VM은 Windows 또는 Linux에서 빌드해야 합니다. 자세한 내용은 다음을 참조하세요. <ul> <li>[Azure 응용 프로그램 제품 만들기](./partner-center-portal/create-new-azure-apps-offer.md)(Windows vhd의 경우)</li><li>[Azure에서 linux 배포판 보증](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) (linux vhd의 경우).</li></ul> |
| 고객 사용량 특성 | 고객 사용량 특성 활성화는 Azure Marketplace에 게시된 모든 솔루션 템플릿에 필요합니다. 고객 사용량 특성과 활성화 방법에 대한 자세한 내용은 [Azure 파트너 고객 사용량 특성](./azure-partner-customer-usage-attribution.md)을 참조하세요.  |
| Managed Disks 사용 | [Managed Disks](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) 은 Azure에서 IaaS vm의 지속형 디스크에 대 한 기본 옵션입니다. 솔루션 템플릿에서 Managed Disks를 사용 해야 합니다. <br> <br> 1. Azure ARM 템플릿에서 Managed Disks를 사용 하 여 솔루션 템플릿을 업데이트 하는 방법에 대 한 [지침](https://docs.microsoft.com/azure/virtual-machines/windows/using-managed-disks-template-deployments) 및 [샘플](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md) 을 따릅니다. <br> <br> 2. 아래 지침에 따라 Managed Disks의 기본 VHD를 저장소 계정으로 가져와 Marketplace에 이미지로 VHD를 게시할 수 있습니다. <br> <ul> <li> [PowerShell](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-vhd?toc=%2fpowershell%2fmodule%2ftoc.json) </li> <li> [CLI](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-vhd?toc=%2fcli%2fmodule%2ftoc.json) </li> </ul> |

## <a name="next-steps"></a>다음 단계

- 아직 수행 하지 않은 경우 Azure Marketplace에 [대해 알아보세요](https://azuremarketplace.microsoft.com/sell) .
- [파트너 센터에 로그인](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) 하 여 제품을 만들거나 완성 합니다.
- 자세한 내용은 [Azure 응용 프로그램 제품을 만듭니다](./partner-center-portal/create-new-azure-apps-offer.md) .
