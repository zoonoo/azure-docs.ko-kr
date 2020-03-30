---
title: 파워 BI 앱 오퍼에 대한 설정 제공 | Azure 마켓플레이스
description: Microsoft AppSource 마켓플레이스에 대한 Power BI 앱 오퍼에 대한 오퍼 설정을 구성합니다.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: dsindona
ms.openlocfilehash: f3a8d740d391edc09a290d3dba4307af7eec00b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80286356"
---
# <a name="power-bi-apps-offer-settings-tab"></a>Power BI 앱 제안 설정 탭

서비스 앱의 **새 쿠폰** 페이지를 열면 먼저 쿠폰 **설정** 탭이 표시됩니다. 이 탭에서 기본 식별자와 오퍼의 이름을 입력합니다. 별표(*)는 필수 필드를 나타냅니다.

![제품 설정 탭](./media/offer-settings-tab.png)


## <a name="offer-settings-fields"></a>제안 설정 필드 

쿠폰 **설정** 탭에서 다음 필수 필드에 정보를 입력해야 합니다. 필수 필드는 별표(*)로 표시됩니다.

|  필드        |  설명                                                               |
|---------------|----------------------------------------------------------------------------|
| **제품 ID\***  | 게시자 프로필 내에 있는 제안에 대한 고유 식별자입니다. 이 식별자는 제품 URL, Azure Resource Manager 템플릿 및 청구 보고서에 표시됩니다. 최대 길이는 50자입니다. 소문자 영숫자 문자와 대시(-)만 포함할 수 있습니다. 그것은 대시로 끝날 수 없습니다. 이 식별자는 오퍼가 라이브로 진행된 후에는 변경할 수 없습니다. Contoso가 오퍼 ID로 `sample-SvcApp`오퍼를 게시하는 경우 `https://appsource.microsoft.com/marketplace/apps/contoso.sample-SvcApp`쿠폰에 AppSource URL이 할당됩니다.      |
| **게시자\*** | [AppSource](https://appsource.microsoft.com)에서 사용되는 조직의 고유 식별자입니다. 모든 제안은 게시자 ID와 연결되어야 합니다. 제안이 저장되면 이 값을 변경할 수 없습니다.                         |
| **이름\***      | 오퍼의 표시 이름입니다. 이 이름은 AppSource 및 클라우드 파트너 포털에 나타납니다. 최대 길이는 50자입니다. 제품에 대해 알아볼 수 있는 브랜드 이름을 사용합니다. 앱이 해당 이름으로 판매되지 않는 한 조직의 이름을 여기에 포함하지 마세요. 다른 웹 사이트 및 게시에서 이 제안을 제공하는 경우 모든 게시에서 동일한 이름을 사용합니다.    <br/>Power BI Apps의 미리 보기 기간 동안 오퍼를 릴리스하는 경우 다음과 같이 응용 프로그램 이름 끝에 문자열을 `(Preview)` `Sample Scv App (Preview)`추가합니다. |
|     |     |


## <a name="next-steps"></a>다음 단계

다음 탭에서 오퍼에 대한 [기술 정보를](./cpp-technical-info-tab.md) 지정합니다.
