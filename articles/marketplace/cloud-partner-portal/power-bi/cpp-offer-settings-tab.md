---
title: Power BI 앱 제안에 대 한 제품 설정 | Azure Marketplace
description: Microsoft AppSource marketplace에 대 한 Power BI 앱 제품의 제안 설정을 구성 합니다.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: ddedc15b41b2b163f85f5443275daf9fbb56bac8
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82143042"
---
# <a name="power-bi-apps-offer-settings-tab"></a>Power BI 앱 제안 설정 탭

>[!Important]
>2020 년 4 월 13 일부 터 파트너 센터에 Power BI 앱 제품 관리를 이동 하기 시작 합니다. 마이그레이션 후 파트너 센터에서 제품을 만들고 관리 합니다. [앱 만들기 개요 Power BI](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-power-bi-app-offer) 의 지침에 따라 마이그레이션된 제안을 관리 합니다.

서비스 앱에 대 한 **새 제품** 페이지를 열면 먼저 **제품 설정** 탭이 표시 됩니다. 이 탭에서 제품의 기본 식별자와 이름을 제공 합니다. 별표 (*)는 필수 필드를 나타냅니다.

![제품 설정 탭](./media/offer-settings-tab.png)


## <a name="offer-settings-fields"></a>제안 설정 필드 

**제품 설정** 탭에서 다음 필수 필드에 정보를 입력 해야 합니다. 필수 필드는 별표(*)로 표시됩니다.

|  필드        |  설명                                                               |
|---------------|----------------------------------------------------------------------------|
| **제품 ID\***  | 게시자 프로필 내에 있는 제안에 대한 고유 식별자입니다. 이 식별자는 제품 URL, Azure Resource Manager 템플릿 및 청구 보고서에 표시됩니다. 최대 길이는 50자입니다. 소문자 영숫자 및 대시 (-)만 포함할 수 있습니다. 대시로 끝나지 않습니다. 제품이 라이브 상태가 된 후에는이 식별자를 변경할 수 없습니다. Contoso가 제안 ID `sample-SvcApp`를 사용 하 여 제품을 게시 하는 경우 제품에 APPSOURCE URL `https://appsource.microsoft.com/marketplace/apps/contoso.sample-SvcApp`이 할당 됩니다.      |
| **게시자\*** | [AppSource](https://appsource.microsoft.com)에서 사용되는 조직의 고유 식별자입니다. 모든 제안은 게시자 ID와 연결되어야 합니다. 제안이 저장되면 이 값을 변경할 수 없습니다.                         |
| **Name\***      | 제안에 대 한 표시 이름입니다. 이 이름은 AppSource 및 Cloud 파트너 포털에 표시 됩니다. 최대 길이는 50자입니다. 제품에 대해 인식 가능한 브랜드 이름을 사용 합니다. 앱이 해당 이름으로 마케팅 전략인 않는 경우에는 여기에 조직의 이름을 포함 하지 마세요. 다른 웹 사이트 및 게시에이 제품을 제공 하는 경우 모든 게시에서 동일한 이름을 사용 합니다.    <br/>Power BI 앱에 대 한 미리 보기 기간에 제품을 릴리스 하는 경우 다음과 `(Preview)` `Sample Scv App (Preview)`같이 응용 프로그램 이름 끝에 문자열을 추가 합니다. |
|     |     |


## <a name="next-steps"></a>다음 단계

다음 탭에서 제품에 대 한 [기술 정보](./cpp-technical-info-tab.md) 를 지정 합니다.
