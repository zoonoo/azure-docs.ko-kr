---
title: Azure Marketplace의 가상 머신 제안 | Microsoft Docs
description: Azure Marketplace에서 VM 제안을 게시하는 프로세스에 대한 개요입니다.
services: Azure, Marketplace, Cloud Partner Portal
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 09/28/2018
ms.author: pbutlerm
ms.openlocfilehash: d3682d18fb849b2d851bae0986f9e61f216aaf2c
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2018
ms.locfileid: "49639382"
---
# <a name="virtual-machine-offer"></a>가상 머신 제안

이 섹션에서는 VM(Virtual Machine)을 게시하는 요소에 대해 간략히 설명하고, [Azure Marketplace](https://azuremarketplace.microsoft.com)의 게시자를 위한 지침으로 사용됩니다.  이 관점에서 다음과 같은 주요 부분으로 나뉩니다.

- [필수 조건](./cpp-prerequisites.md) - VM 제안을 만들거나 게시하기 전에 기술 및 비즈니스 요구 사항을 나열합니다.
- [VM 제안 만들기](./cpp-create-offer.md) - [Cloud 파트너 포털](https://cloudpartner.azure.com)을 사용하여 새 VM 제안 항목을 만드는 데 필요한 단계를 나열합니다.
- [VM 기술 자산 만들기](./cpp-create-technical-assets.md) - VM 솔루션에 대한 기술 자산을 만드는 방법과 Azure Marketplace에서 이 패키지를 VM 제안으로 구성하는 방법입니다.
- [VM 제안 게시](./cpp-publish-offer.md) - Azure Marketplace에 게시할 제안을 제출하는 방법입니다.


## <a name="vm-publishing-process-flow"></a>VM 게시 프로세스 흐름

다음 다이어그램에서는 VM 제안을 게시하는 고급 단계를 보여 줍니다. 

![VM 게시 프로세스](./media/publishvm_001.png)

1. 제안 만들기 - 제안 설명, 마케팅 자료, 법적 정보, 지원 정보 및 자산 사양을 포함하여 제안에 대한 모든 세부 정보 및 정보가 구성됩니다.

2. 비즈니스 및 기술 자산 만들기 - 연결된 솔루션(여기서는 VM 및 연결된 디스크)에 대한 비즈니스 자산(법률 문서 및 마케팅 자료) 및 기술 자산을 만듭니다. 

3. SKU 만들기 - 제안과 연결된 SKU를 만들어 제출합니다.  게시하려는 각 이미지마다 고유한 SKU가 필요합니다. 
 
4. 제안 만들기 및 게시 - 제안 및 기술 자산이 완성되면 해당 제안을 제출할 수 있습니다. 이 제출은 솔루션을 테스트, 유효성 검사 및 인증한 다음, 마켓플레이스에서 "라이브 상태"가 되도록 하는 게시 프로세스를 시작합니다.  

## <a name="next-steps"></a>다음 단계

이러한 단계를 고려하기 전에 VM을 Microsoft Azure Marketplace에 게시하기 위한 [기술 및 비즈니스 요구 사항](./cpp-prerequisites.md)이 충족되어야 합니다. 
