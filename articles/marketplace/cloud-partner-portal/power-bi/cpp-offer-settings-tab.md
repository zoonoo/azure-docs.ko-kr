---
title: Power BI 앱 제안에 대한 제안 설정 - Azure Marketplace | Microsoft Docs
description: Microsoft AppSource Marketplace의 Power BI 앱 제안에 대한 제안 설정을 구성합니다.
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
ms.date: 01/29/2019
ms.author: pbutlerm
ms.openlocfilehash: fef2455129d94913c5b51a08c04403834861bb48
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/02/2019
ms.locfileid: "55665814"
---
# <a name="power-bi-apps-offer-settings-tab"></a>Power BI 앱 제안 설정 탭

서비스 앱에 대한 **새 제안** 페이지는 **제안 설정**이라는 첫 번째 탭에서 열립니다.  이 탭에서 제안에 대한 기본 식별자와 이름을 제공합니다.  필드 이름에 별표(*)가 추가되면 필수 항목임을 나타냅니다.

![제품 설정 탭](./media/offer-settings-tab.png)


## <a name="offer-settings-fields"></a>제안 설정 필드 

**제안 설정** 탭에서 제공해야 하는 필수 필드는 다음과 같습니다.

|  필드        |  설명                                                               |
|---------------|----------------------------------------------------------------------------|
| **제안 ID**  | 게시자 프로필 내에 있는 제안에 대한 고유 식별자입니다. 이 식별자는 제품 URL, Resource Manager 템플릿 및 청구 보고서에 표시됩니다. 최대 길이는 50자이며, 소문자 영숫자와 대시(-)로만 구성할 수 있지만 대시로 끝날 수 없습니다. 제안이 라이브 상태가 되면 이 필드를 변경할 수 없습니다. 예를 들어 Contoso에서 제안 ID가 `sample-SvcApp`인 제안을 게시하면 `https://appsource.microsoft.com/marketplace/apps/contoso.sample-SvcApp`이라는 AppSource URL이 할당됩니다.      |
| **게시자** | [AppSource](https://appsource.microsoft.com)에서 사용되는 조직의 고유 식별자입니다. 모든 제안은 게시자 ID와 연결되어야 합니다. 제안이 저장되면 이 값을 수정할 수 없습니다.                         |
| **Name**      | 제안에 대한 표시 이름입니다. 이 이름은 AppSource 및 Cloud 파트너 포털에 표시됩니다. 최대 50문자를 포함할 수 있습니다. 여기서 제공하는 지침은 제품의 인식 가능한 브랜드 이름을 포함하는 것입니다. 앱을 마케팅하는 경우를 제외하고는 조직 이름을 여기에 포함시키지 않습니다. 다른 웹 사이트 및 게시물에서 이 제안을 마케팅하는 경우 이름은 모든 게시물에서 동일해야 합니다.    <br/>Power BI 앱의 미리 보기 기간 동안 제안을 미리 보기 모드로 릴리스하는 경우 `(Preview)` 문자열을 애플리케이션 이름에 추가합니다(예: `Sample Scv App (Preview)`). |
|     |     |


## <a name="next-steps"></a>다음 단계

다음 탭에서 제안에 대한 [기술 정보](./cpp-technical-info-tab.md)를 지정합니다.
