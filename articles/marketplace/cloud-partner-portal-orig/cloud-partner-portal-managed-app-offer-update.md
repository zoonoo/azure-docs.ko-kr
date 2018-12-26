---
title: Azure Marketplace에 대한 기존 제품 업데이트
description: Azure Marketplace에 대한 기존 제품 업데이트
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: qianw211
manager: pbutlerm
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 5633392bdf1293ee9196fafe67cf901e0d8c8014
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48807565"
---
<a name="update-an-existing-offer-for-azure-marketplace"></a>Azure Marketplace에 대한 기존 제품 업데이트 
==============================================

제품을 라이브로 전환한 후 다양한 종류의 업데이트를 수행할 수 있습니다.

1.  기존 SKU에 새 \"패키지\" 추가
2.  기존 제품에 새 SKU 추가
3.  제품/SKU 마켓플레이스 메타데이터 업데이트

Cloud 파트너 포털에서 제품을 업데이트하고 다시 게시해야 합니다. 이 문서에서는 Azure 응용 프로그램 제품을 업데이트하는 다양한 측면을 안내합니다.

<a name="unpermitted-changes-to-azure-application-offersku"></a>Azure 응용 프로그램 제품/SKU에 대한 허용되지 않는 변경 내용 
--------------------------------------------------

제품이 Azure Marketplace에서 라이브로 전환된 후에는 수정할 수 없는 Azure 응용 프로그램 제품/SKU의 특성이 있습니다.

1.  해당 제품의 제품 ID 및 게시자 ID
2.  기존 SKU의 SKU ID
3.  게시된 패키지를 업데이트합니다.

<a name="adding-a-new-package-to-an-existing-sku"></a>기존 SKU에 새 패키지 추가 
---------------------------------------

게시자가 기존 패키지를 업데이트하기 위해 패키지의 새 버전을 추가할 수 있습니다. 이 작업은 다른 버전 번호로 새 패키지를 업로드하여 수행할 수 있습니다.

1.  [Cloud 파트너 포털](http://cloudpartner.azure.com)에 로그인합니다.
2.  모든 제품에서 업데이트할 제품을 찾습니다.
3.  SKU 양식에서 패키지를 업데이트하려는 SKU를 클릭합니다.
4.  \"새 패키지\"를 클릭하고 새 버전을 입력합니다.
5.  업데이트된 템플릿 파일을 포함하는 새 .zip 파일을 업로드합니다.
6.  게시를 클릭하고 게시 워크플로를 시작하여 새 SKU를 라이브로 전환합니다.

<a name="adding-a-new-sku-to-an-existing-offer"></a>기존 제품에 새 SKU 추가
-------------------------------------

기존 제품에 대해 새 SKU를 사용할 수 있도록 선택할 수 있습니다. 이렇게 하려면 다음 단계를 수행하세요.

1.  [클라우드 파트너 포털](http://cloudpartner.azure.com)에 로그인합니다.
2.  모든 제품에서 업데이트할 제품을 찾습니다.
3.  SKU 양식에서 새 SKU 추가를 클릭하고 팝업에 SKU ID를 입력합니다.
4.  [여기](./cloud-partner-portal-managed-app-publish.md)에 지정된 대로 나머지 단계를 수행합니다.
5.  게시를 클릭하고 게시 워크플로를 시작하여 새 SKU를 라이브로 전환합니다.

<a name="updating-offer-marketplace-metadata"></a>제품 마켓플레이스 메타데이터 업데이트 
-----------------------------------

회사 로고를 업데이트하는 등 제품과 관련된 Marketplace 메타데이터를 업데이트해야 하는 시나리오가 있을 수 있습니다. 아래 단계를 따르세요.

1.  Cloud 파트너 포털에 로그인합니다.
2.  모든 제품에서 업데이트할 제품을 찾습니다.
3.  Marketplace 양식으로 이동하여 [여기](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-push-to-staging)에 있는 지침에 따라 변경합니다.
4.  게시를 클릭하고 게시 워크플로를 시작하여 변경 내용을 라이브로 전환합니다.
