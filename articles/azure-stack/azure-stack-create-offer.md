---
title: "Azure 스택에는 제안을 만듭니다. | Microsoft Docs"
description: "클라우드 관리자 Azure 스택에서 사용자가 제공을 만드는 방법에 설명 합니다."
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 96b080a4-a9a5-407c-ba54-111de2413d59
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/10/2017
ms.author: erikje
ms.openlocfilehash: 269a6106f657536ba74be366f842b2f9cd86c5dc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-offer-in-azure-stack"></a>Azure Stack에서 제품 만들기

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

[제공](azure-stack-key-features.md) 은 공급자를 구입 하거나 구독 하는 사용자에 게 제공 하는 하나 이상의 계획의 그룹입니다. 이 문서를 포함 하는 제품을 만드는 방법을 보여 줍니다.는 [만든 계획을](azure-stack-create-plan.md) 마지막 단계에서입니다. 이 제품은 구독자가 가상 컴퓨터를 프로 비전 하는 기능을 제공 합니다.

1. Azure 스택 관리자 포털 (https://adminportal.local.azurestack.external)에 로그인 > 클릭 **새로** > **테 넌 트 제공 + 계획**  >   **제공**합니다.

   ![](media/azure-stack-create-offer/image01.png)
2. 에 **새 제공** 블레이드에서 입력 **표시 이름** 및 **리소스 이름**, 선택한 다음 새 또는 기존 **리소스 그룹**합니다. 표시 이름 제품의 친근 한 이름 및 구독할 때 사용자에 게 표시할 제품에 대 한 유일한 정보입니다. 따라서를 통해 사용자는 제안 추가 되는 내용 이해 하는 직관적인 이름을 사용 해야 합니다. 관리자만 리소스 이름을 볼 수 있습니다. 관리자가 Azure 리소스 관리자 리소스로 제품 작업을 하는 데 사용하는 이름입니다.

   ![](media/azure-stack-create-offer/image01a.png)
3. 클릭 **계획 기본** 및에 **계획** 블레이드를 클릭 하 고는 제품에 포함할 원하는 계획을 선택 **선택**합니다. **만들기** 를 클릭하면 제품이 만들어집니다.

   ![](media/azure-stack-create-offer/image02.png)
4. 클릭 **모든 리소스**, 새 제안에 대 한 검색를 클릭 하 고 새 제안을 클릭 **상태 변경**, 클릭 하 고 **공용**합니다.

   ![](media/azure-stack-create-offer/image03.png)

서비스를 구독 하는 경우 전체 보기를 얻을 수 있는 사용자에 대 한 공용 수 있어야 합니다. 제공 될 수 있습니다.

* **공용**: 사용자에 게 표시 합니다.
* **개인**: 클라우드 관리자에만 표시 합니다. 계획 또는 제품을 초안을 작성 하는 동안 유용한 경우 클라우드 관리자가 모든 구독을 승인 해야 합니다.
* **역할 해제**: 새 구독자가 접근할 수 없습니다. 클라우드 관리자 사용 하 여 향후 구독을 방지 하지만 현재 구독자를 그대로 남겨 둡니다를 서비스 해제 합니다.

제품에 대 한 변경 내용을 사용자에 게 즉시 표시 되지 않습니다. "구독 선택"에서 새 구독을 확인 하려면 로그 아웃/로그인 할 수 변경 내용을 보려면, 리소스/리소스 그룹을 만들 때.

> [!NOTE]
>설명한 것 처럼 PowerShell을 사용 하 여 기본 제공, 계획 및 할당량을 만들 수도 있습니다는 [Azure 스택 서비스 관리자 추가 정보](https://github.com/Azure/AzureStack-Tools/tree/master/ServiceAdmin)합니다.
>


### <a name="next-steps"></a>다음 단계
[제품 구독 후 VM 프로비전](azure-stack-subscribe-plan-provision-vm.md)
