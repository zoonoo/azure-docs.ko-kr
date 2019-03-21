---
title: 계정 및 키 관리
titleSuffix: Language Understanding - Azure Cognitive Services
description: LUIS 계정에 대한 두 가지 주요 정보는 사용자 계정과 작성 키입니다. 로그인 정보 account.microsoft.com에서 관리 됩니다. 제작 키 LUIS 포털 설정 페이지에서 관리 됩니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 03/11/2019
ms.author: diberry
ms.openlocfilehash: d7d63ad642ab2d3b336e15dcca606077762ceb9d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58116625"
---
# <a name="manage-account-and-authoring-key"></a>계정 및 작성 키 관리

LUIS 계정에 대한 두 가지 주요 정보는 사용자 계정과 작성 키입니다. 로그인 정보는 [account.microsoft.com](https://account.microsoft.com)에서 관리됩니다. 제작 키에서 관리 되는 [LUIS](luis-reference-regions.md) 포털 **설정** 페이지입니다.

## <a name="authoring-key"></a>작성 키

이 단일, 지역별 제작에서 키를 **설정을** 페이지에서 모든 앱을 작성할 수 있습니다를 [LUIS](luis-reference-regions.md) 뿐만 포털를 [Api를 작성](https://aka.ms/luis-authoring-api)합니다. 편의를 위해 작성 키를 사용하여 매월 [제한된](luis-boundaries.md) 수의 엔드포인트 쿼리를 만들 수 있습니다.

[![LUIS 설정 페이지](./media/luis-how-to-account-settings/account-settings.png)](./media/luis-how-to-account-settings/account-settings.png#lightbox)

작성 키는 사용자가 공동 작업자로 나열된 모든 앱 뿐만 아니라 사용자가 소유하는 모든 앱에서 사용됩니다.

## <a name="authoring-key-regions"></a>작성 키 지역

작성 키는 [작성 지역](luis-reference-regions.md#publishing-regions)에 국한합니다. 이 키는 다른 지역에서는 작동하지 않습니다.

## <a name="reset-authoring-key"></a>작성 키 다시 설정

작성 키가 손상되면 키를 다시 설정합니다. 모든 앱에 키를 다시 설정 합니다 [LUIS](luis-reference-regions.md) 포털입니다. 작성 API를 통해 앱을 작성하는 경우 `Ocp-Apim-Subscription-Key` 값을 새 키로 변경해야 합니다.

## <a name="delete-account"></a>계정 삭제

계정을 삭제할 때 삭제되는 데이터에 대한 내용은 [데이터 저장 및 제거](luis-concept-data-storage.md#accounts)를 참조하세요.

## <a name="next-steps"></a>다음 단계

[작성 키](luis-concept-keys.md#authoring-key)에 대해 자세히 알아봅니다.

