---
title: Power BI 앱 제품에 대 한 설정을 제공 | Azure Marketplace
description: Microsoft AppSource marketplace 용 Power BI 앱 제품에 대 한 제품 설정을 구성 합니다.
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: pabutler
ms.openlocfilehash: be2c2b4f5d9461aa0fdc6dde89931ed4b6418ced
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "64943457"
---
# <a name="power-bi-apps-offer-settings-tab"></a>Power BI 앱 제안 설정 탭

여는 경우는 **새 제품** 처음 서비스 앱에 대 한 페이지는 **제품 설정** 탭 합니다. 이 탭의 기본 식별자 및 제품에 대 한 이름을 제공합니다. 별표 (*) 필수 필드를 나타냅니다.

![제품 설정 탭](./media/offer-settings-tab.png)


## <a name="offer-settings-fields"></a>제안 설정 필드 

에 **제품 설정** 필수 필드에 정보를 입력 해야 하는 탭 합니다. 필수 필드는 별표(*)로 표시됩니다.

|  필드        |  설명                                                               |
|---------------|----------------------------------------------------------------------------|
| **제품 ID\***  | 게시자 프로필 내에 있는 제안에 대한 고유 식별자입니다. 이 식별자는 제품 URL, Azure Resource Manager 템플릿 및 청구 보고서에 표시됩니다. 최대 길이는 50자입니다. 소문자 영숫자 및 대시 (-)만 포함할 수 있습니다. 대시로 끝날 수 없습니다. 제품이 라이브 상태가 된 후에이 식별자를 변경할 수 없습니다. Contoso는 제품을 게시 하는 경우 제품 ID `sample-SvcApp`, 제품 AppSource URL에 할당 된 `https://appsource.microsoft.com/marketplace/apps/contoso.sample-SvcApp`합니다.      |
| **Publisher\*** | [AppSource](https://appsource.microsoft.com)에서 사용되는 조직의 고유 식별자입니다. 모든 제안은 게시자 ID와 연결되어야 합니다. 제안이 저장되면 이 값을 변경할 수 없습니다.                         |
| **이름\***      | 제품에 대 한 표시 이름입니다. AppSource에 클라우드 파트너 포털에서이 이름이 표시 됩니다. 최대 길이는 50자입니다. 제품에 대 한 인식 가능한 브랜드 이름을 사용 합니다. 해당 이름을 가진 앱이 마케팅 전략인 하지 않는 한 여기에 조직의 이름을 포함 하지 마십시오. 이 제품은 다른 웹 사이트에 게시를 제공 하는 경우 모든 게시에서 동일한 이름을 사용 합니다.    <br/>Power BI 앱에 대 한 미리 보기 기간 동안 제공 하는 서비스를 해제 하는 경우 문자열을 추가 합니다 `(Preview)` 다음과 같은 응용 프로그램의 이름, 끝: `Sample Scv App (Preview)`합니다. |
|     |     |


## <a name="next-steps"></a>다음 단계

다음 탭에서 지정 [기술 정보](./cpp-technical-info-tab.md) 제품에 대 한 합니다.
