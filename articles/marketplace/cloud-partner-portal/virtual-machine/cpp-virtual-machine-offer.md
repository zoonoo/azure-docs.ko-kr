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
ms.date: 12/04/2018
ms.author: pbutlerm
ms.openlocfilehash: d51fffad897ba2658c7bee51c26e7e3be9f10e88
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2018
ms.locfileid: "53188761"
---
# <a name="virtual-machine-offer"></a>가상 머신 제안

|    |    |
|-----------------------------------------------------------------|------------------------------------------|
| 이 섹션에서는 [Azure Marketplace](https://azuremarketplace.microsoft.com)에 새 가상 머신 제품을 게시하는 방법을 설명합니다. 운영 체제 VHD(가상 하드 디스크) 및 0개 이상의 데이터 VHD를 포함하여 Windows 기반 가상 머신과 Linux 기반 가상 머신 둘 다가 지원됩니다. | ![가상 머신 아이콘](./media/virtual-machine-icon.png)  |


## <a name="publishing-overview"></a>게시 개요

다음 [Azure Marketplace 제품 최적화](https://channel9.msdn.com/Events/Build/2017/P4026?ocid=player) 비디오에서는 이 마켓플레이스에 게시하는 방법(가상 머신 솔루션 사용), 제품 페이지 및 선택적 시험 사용 환경을 사용하여 사용자 경험을 최적화하는 방법, 사용자 잠재 고객이 생성되는 방법, 잠재 고객을 사용하고 고객 참여를 최적화하는 방법을 포함하여 Azure Marketplace에 대한 광범위한 개요를 제공합니다.

> [!VIDEO https://channel9.msdn.com/Events/Build/2017/P4026]


## <a name="vm-publishing-process-flow"></a>VM 게시 프로세스 흐름

다음 다이어그램에서는 VM 제안을 게시하는 고급 단계를 보여 줍니다. 

![VM 게시 프로세스](./media/publishvm_001.png)

1. 제안 만들기 - 제안 설명, 마케팅 자료, 법적 정보, 지원 정보 및 자산 사양을 포함하여 제안에 대한 모든 세부 정보 및 정보가 구성됩니다.

2. 비즈니스 및 기술 자산 만들기 - 연결된 솔루션(여기서는 VM 및 연결된 디스크)에 대한 비즈니스 자산(법률 문서 및 마케팅 자료) 및 기술 자산을 만듭니다. 

3. SKU 만들기 - 제안과 연결된 SKU를 만들어 제출합니다.  게시하려는 각 이미지마다 고유한 SKU가 필요합니다. 
 
4. 제안 만들기 및 게시 - 제안 및 기술 자산이 완성되면 해당 제안을 제출할 수 있습니다. 이 제출은 솔루션을 테스트, 유효성 검사 및 인증한 다음, 마켓플레이스에서 "라이브 상태"가 되도록 하는 게시 프로세스를 시작합니다.  

## <a name="next-steps"></a>다음 단계

이러한 단계를 고려하기 전에 VM을 Microsoft Azure Marketplace에 게시하기 위한 [기술 및 비즈니스 요구 사항](./cpp-prerequisites.md)이 충족되어야 합니다. 
