---
title: Azure 응용 프로그램 솔루션 템플릿 제품 게시 가이드
description: 이 아티클에서는 솔루션 템플릿을 Marketplace에 게시하기 위한 요구 사항을 설명합니다.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: ellacroi
manager: nunoc
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 07/09/2018
ms.author: ellacroi
ms.openlocfilehash: 44d081a0666aa37ec0bf8eeac540b7a7f4f4f904
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/14/2018
ms.locfileid: "39057885"
---
# <a name="azure-applications-solution-template-offer-publishing-guide"></a>Azure 응용 프로그램: 솔루션 템플릿 제품 게시 가이드

솔루션 템플릿은 Marketplace에서 솔루션을 게시하는 주요 방법 중 하나입니다. 이 가이드를 사용하여 이 제품에 대한 요구 사항을 이해합니다. 

Marketplace를 통해 배포되고 청구되는 트랜잭션 제품은 다음과 같습니다. 사용자에게 표시되는 작업에 대한 호출은 "지금 가져오기"입니다.

솔루션에 단순 VM 이외의 추가 배포 및 구성 자동화가 필요한 경우, Azure 앱: 솔루션 템플릿 제품 유형을 사용합니다. Azure 앱: 솔루션 템플릿을 사용하여 하나 이상의 VM 프로비전을 자동화할 수 있습니다. 네트워킹 및 저장소 리소스를 프로비전할 수도 있습니다. Azure 앱: 솔루션 템플릿 제품 형식은 단일 VM 및 전체 IaaS 기반 솔루션에 대한 자동화 이점을 제공합니다.

## <a name="requirements-for-solution-templates"></a>솔루션 템플릿에 대한 요구 사항

|요구 사항 |세부 정보  |
|---------|---------|
|청구 및 계량    |  고객의 Azure 구독에서 리소스를 프로비전합니다. 종량제(PAYGO) 가상 머신은 Microsoft를 통해 고객과 거래되고 고객의 Azure 구독(PAYGO)을 통해 청구됩니다. 
BYOL(사용자 라이선스 필요)의 경우 Microsoft가 고객 구독에서 발생하는 인프라 비용을 청구하는 동안 소프트웨어 라이선스 요금을 고객과 직접 거래합니다.        |
|Azure 호환 VHD(가상 하드 디스크)    |   VM은 Windows 또는 Linux에서 빌드해야 합니다.<ul> <li>Linux VHD를 만드는 방법에 대한 자세한 내용은 [docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#2-create-an-azure-compatible-vhd-linux-based](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#2-create-an-azure-compatible-vhd-linux-based)에 있는 Azure 호환 VHD(Linux 기반) 만들기 섹션을 참조하세요.</li> <li>Windows VHD를 만드는 방법에 대한 자세한 내용은 [docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#3-create-an-azure-compatible-vhd-windows-based](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#3-create-an-azure-compatible-vhd-windows-based)에 있는 Azure 호환 VHD(Windows 기반) 만들기 섹션을 참조하세요.</li> </ul>      |



## <a name="next-steps"></a>다음 단계
아직 수행하지 않았다면 

- Marketplace에서 [등록](https://azuremarketplace.microsoft.com/sell)

새 제품을 등록하고 만들거나 기존에서 작업하는 경우

- 제품을 만들거나 완료하기 위해 [Cloud 파트너 포털에 로그인](https://cloudpartner.azure.com)
