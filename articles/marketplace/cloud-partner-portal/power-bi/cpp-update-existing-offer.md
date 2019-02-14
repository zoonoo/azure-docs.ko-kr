---
title: 기존 Power BI 앱 제안 업데이트- Azure Marketplace | Microsoft Docs
description: Power BI 앱 제안을 Microsoft AppSource Marketplace에 게시한 후에 업데이트합니다.
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: pbutlerm
ms.openlocfilehash: 635e2e71bb952aaee761df6a1d5d87c46db531f6
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/02/2019
ms.locfileid: "55665704"
---
# <a name="update-an-existing-power-bi-app-offer"></a>기존 Power BI 앱 제안 업데이트

이 문서에서는 [Cloud 파트너 포털](https://cloudpartner.azure.com/)에서 Power BI 앱 제안을 업데이트한 다음, 해당 제안을 다시 게시하는 다양한 측면을 안내합니다.  제안을 업데이트해야 하는 일반적인 이유는 다음과 같습니다.

- Power BI에서 앱 콘텐츠를 업데이트하고 새로 패키지된 앱에서 새 설치 URL을 가져옵니다.
- 제안에 대한 시장 메타데이터(영업, 마케팅 또는 지원 정보와 자산)를 업데이트합니다.
 
이러한 수정 작업을 지원하기 위해 포털에서 **비교** 및 **기록** 기능을 제공합니다.


## <a name="unpermitted-changes-to-offer"></a>제안에 대해 허용되지 않는 변경

AppSource에서 제안이 라이브 상태이면 수정할 수 없는 Power BI 앱 제안의 일부 특성(주로 **Offer ID**  및 **Publisher ID** )이 있습니다.


## <a name="common-update-operations"></a>일반적인 업데이트 작업

Power BI 앱 제안에서 변경할 수 있는 특성은 다양하지만 일반적인 작업은 다음과 같습니다.


### <a name="update-app-content-in-power-bi"></a>Power BI에서 앱 콘텐츠 업데이트

일반적으로 Power BI의 앱은 새 콘텐츠, 보안 패치, 추가 기능 등으로 정기적으로 업데이트됩니다. 이러한 시나리오에서 다음 단계를 사용하여 URL을 새 앱 콘텐츠 설치로 업데이트하려고 합니다.

1.  [Cloud 파트너 포털](https://cloudpartner.azure.com/)에 로그인합니다.
2.  **모든 제안** 아래에서 업데이트할 제안을 찾습니다.
3.  **기술 정보** 탭에서 새 설치 관리자 URL을 입력합니다.
4.  **게시**를 클릭하여 새 앱의 버전을 AppSource에 게시하는 워크플로를 시작합니다.


### <a name="update-offer-marketplace-metadata"></a>제품 마켓플레이스 메타데이터 업데이트

다음 단계를 사용하여 제안과 연결된 마켓플레이스 메타데이터(회사 이름, 로고 등)를 업데이트합니다.

1.  [Cloud 파트너 포털](https://cloudpartner.azure.com/)에 로그인합니다.
2.  **모든 제품** 아래에서 업데이트할 제품을 찾습니다.
3.  **상점 세부 정보** 탭으로 이동한 다음, [Power BI 앱 상점 세부 정보 탭](./cpp-storefront-details-tab.md)의 지침에 따라 메타데이터를 변경합니다.
4.  **게시**를 클릭하여 변경 내용을 게시하는 워크플로를 시작합니다.


## <a name="compare-feature"></a>기능 비교

이미 게시된 제안을 변경하는 경우 **비교** 기능을 사용하여 수행된 변경을 감사할 수 있습니다. 이 기능을 사용하려면 다음을 수행합니다.

1.  편집 프로세스 중에 언제든지 제안에 대한 **비교** 단추를 클릭합니다.

    ![기능 비교 단추](./media/compare-feature-button.png)

2.  마케팅 자산과 메타데이터의 버전이 나란히 표시됩니다.


## <a name="history-of-publishing-actions"></a>게시 작업의 기록

이전의 모든 게시 작업을 보려면 Cloud 파트너 포털의 왼쪽 탐색 메뉴 모음에서 **기록** 탭을 클릭합니다. 여기서는 AppSource 제안의 수명 동안 수행되어 타임스탬프가 적용된 작업을 확인할 수 있습니다.


## <a name="next-steps"></a>다음 단계

[Cloud 파트너 포털](https://cloudpartner.azure.com/#insights)의 [Seller Insights](../../cloud-partner-portal-orig/si-getting-started.md) 기능을 정기적으로 사용하여 마켓플레이스 고객 및 사용량에 대한 인사이트를 제공해야 합니다.  
