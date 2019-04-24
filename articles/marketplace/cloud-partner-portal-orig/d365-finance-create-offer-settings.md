---
title: 제품 설정 양식을 작성하는 방법 | Microsoft Docs
description: 새 Dynamics 365 Business Central 애플리케이션의 제품 설정 양식에서 값을 입력해야 하는 여러 필드에 대해 설명합니다.
services: Azure, Marketplace, Cloud Partner Portal,
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
ms.date: 09/17/2018
ms.author: pbutlerm
ms.openlocfilehash: a050a8cfdd67f662786fc0b9b7ed5451c88dccb1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60397824"
---
<a name="how-to-fill-out-the-offer-settings-form"></a>제품 설정 양식을 작성하는 방법
=======================================

제품 설정 양식은 제품 설정을 지정하는 기본 양식입니다.
아래에서는 필수 필드에 대해 설명합니다.

### <a name="offer-id"></a>제공 ID

`OfferId`는 게시자 프로필 내의 제품 고유 ID입니다.
이 ID는 제품 URL에 표시됩니다. 소문자 영숫자 문자 또는 대시(-)로만 구성할 수 있습니다. ID는 대시로 끝내면 안 되며 최대 50문자를 포함할 수 있습니다. 이 필드는 제품이 라이브 상태가 되면 잠깁니다.

예를 들어 "Contoso" 파트너가 생성하는 제품 ID "sample-Web App"은 AppSource에 다음과 같이 표시됩니다.

&emsp; `https://appsource.microsoft.com/marketplace/apps/contoso.sample-Web App?tab=Overview`


### <a name="publisher-id"></a>게시자 ID

이 드롭다운 목록을 사용하여 이 제품을 게시할 게시자 프로필을 선택할 수 있습니다. 이 필드는 제품이 라이브 상태가 되면 잠깁니다.


### <a name="name"></a>Name

Microsoft [AppSource](https://appsource.microsoft.com/)에 표시되는 앱/제품의 표시 이름입니다. 최대 50문자를 포함할 수 있습니다.

> [!NOTE]
> 짧은 이름은 앱 매니페스트에 지정된 게시자 이름과 같아야 합니다.

**저장**을 클릭하여 진행 상황을 저장합니다. 다음 단계에서는 제품의 기술 정보를 추가합니다.
