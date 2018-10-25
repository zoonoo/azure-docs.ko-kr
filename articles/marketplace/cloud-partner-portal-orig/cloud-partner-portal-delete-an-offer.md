---
title: Azure Marketplace에서 제안 또는 SKU 삭제 | Microsoft Docs
description: 제안 또는 SKU를 삭제하기 위한 단계입니다.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: cc172e35e8964fad3b1a1410d1f1f3240c423ab3
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48807605"
---
<a name="delete-an-offer-or-sku-from-azure-marketplace"></a>Azure Marketplace에서 제안 또는 SKU 삭제
==========================================

다양한 이유로 Marketplace에서 제품을 제거하도록 결정할 수 있습니다. 제품 제거는 새 고객이 더 이상 제품을 구매하거나 배포할 수 없게 하지만 기존 고객에게는 영향을 주지 않습니다.
제품 종료는 귀하와 기존 고객 간의 서비스 및/또는 사용권 계약이 종료되는 프로세스입니다. 제공 서비스 제거 및 종료와 관련된 지침과 정책은 [Microsoft Marketplace 판매자 계약](http://go.microsoft.com/fwlink/?LinkID=699560)(7절 참조)
7) 및 [참가 정책](https://azure.microsoft.com/support/legal/marketplace/participation-policies/)(6.2절 참조)을 따릅니다. 이 문서에서는 수행할 수 있는 지원되는 다른 삭제 시나리오 및 단계를 설명합니다.

<a name="delete-a-live-sku-from-azure-marketplace"></a>Azure Marketplace에서 라이브 SKU 삭제
----------------------------------------

다음 단계를 사용하여 Azure Marketplace에서 라이브 SKU를 삭제할 수 있습니다.

1.  [클라우드 파트너 포털](https://cloudpartner.azure.com/)에 로그인합니다.

2.  **모든 제품** 탭에서 제품을 선택합니다.

3.  화면 왼쪽의 창에서 **SKU** 탭을 선택합니다.

4.  삭제할 SKU를 선택하고 해당 SKU에 대한 삭제 단추를 클릭합니다.

5.  제품을 Azure Marketplace에 [다시 게시](./cloud-partner-portal-make-offer-live-on-Azure-Marketplace.md)합니다.

Azure Marketplace에 제품이 라이브되면 SKU가 Azure Marketplace 및 Azure Portal에서 삭제됩니다.

<a name="roll-back-to-a-previous-sku-version"></a>이전 SKU 버전으로 롤백
----------------------------------

다음 단계를 수행하여 Azure Marketplace에서 라이브 SKU의 현재 버전을 삭제할 수 있습니다. 이 프로세스가 완료되면 SKU가 이전 버전으로 롤백됩니다.

1.  [클라우드 파트너 포털](https://cloudpartner.azure.com/)에 로그인합니다.

2.  **모든 제품** 탭에서 제품을 선택합니다.

3.  화면 왼쪽의 창에서 **SKU** 탭을 선택합니다.

4.  게시된 디스크 버전의 목록에서 최신 **디스크 버전**을 삭제합니다.

5.  제품을 Azure Marketplace에 [다시 게시](./cloud-partner-portal-make-offer-live-on-Azure-Marketplace.md)합니다.

Azure Marketplace에 제품이 다시 라이브되면 나열된 SKU의 현재 버전이 Azure Marketplace 및 Azure Portal에서 삭제됩니다.
SKU는 이전 버전으로 롤백됩니다.

<a name="delete-a-live-offer"></a>라이브 제품 삭제
-------------------

라이브 제품을 제거하는 요청 시 해결해야 하는 다양한 측면이 있습니다. Azure Marketplace에서 라이브 제품을 제거하려면 다음 단계를 수행하여 지원 팀의 지침을 확인하세요.

1.  이 [링크](https://go.microsoft.com/fwlink/?linkid=844975)를 사용하거나 Cloud 파트너 포털의 상단 오른쪽에 있는 지원을 클릭하여 지원 티켓을 제기합니다.

2.  **문제 유형** 목록에서 특정 제안 유형을 선택하고 **범주** 목록의 **게시된 제안 제거**를 선택합니다.

3.  요청을 제출합니다.

지원 팀은 제품 삭제 프로세스를 안내합니다.

>[!NOTE]
>SKU/제안을 삭제해도 현재 SKU/제안의 구매에는 영향을 주지 않습니다. 이러한 SKU/제안 구매는 이전처럼 진행됩니다. 그렇지만 앞으로 새로 구매할 수는 없습니다.
