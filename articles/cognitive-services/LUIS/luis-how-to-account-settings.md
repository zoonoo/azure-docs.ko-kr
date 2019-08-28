---
title: 계정 및 키 관리-LUIS
titleSuffix: Azure Cognitive Services
description: LUIS 계정에 대한 두 가지 주요 정보는 사용자 계정과 작성 키입니다. 로그인 정보는 account.microsoft.com에서 관리 됩니다. 제작 키는 LUIS 포털 설정 페이지에서 관리 됩니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: 11a2040e674a0ee38fbce8b21097f058fe603da2
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68881941"
---
# <a name="manage-account-and-authoring-key"></a>계정 및 작성 키 관리

LUIS 계정에 대한 두 가지 주요 정보는 사용자 계정과 작성 키입니다. 로그인 정보는 [account.microsoft.com](https://account.microsoft.com)에서 관리됩니다. 제작 키는 [LUIS](luis-reference-regions.md) 포털 **설정** 페이지에서 관리 됩니다.

## <a name="authoring-key"></a>작성 키

**설정** 페이지에서이 단일 지역별 제작 키를 사용 하 여 [LUIS](luis-reference-regions.md) 포털 뿐만 아니라 [제작 api](https://go.microsoft.com/fwlink/?linkid=2092087)를 통해 모든 앱을 제작할 수 있습니다. 편의를 위해 작성 키를 사용하여 매월 [제한된](luis-boundaries.md) 수의 엔드포인트 쿼리를 만들 수 있습니다.

[![LUIS 설정 페이지](./media/luis-how-to-account-settings/account-settings.png)](./media/luis-how-to-account-settings/account-settings.png#lightbox)

작성 키는 사용자가 공동 작업자로 나열된 모든 앱 뿐만 아니라 사용자가 소유하는 모든 앱에서 사용됩니다.

## <a name="authoring-key-regions"></a>작성 키 지역

작성 키는 [작성 지역](luis-reference-regions.md#publishing-regions)에 국한합니다. 이 키는 다른 지역에서는 작동하지 않습니다.

## <a name="reset-authoring-key"></a>작성 키 다시 설정

작성 키가 손상되면 키를 다시 설정합니다. [LUIS](luis-reference-regions.md) 포털의 모든 앱에서 키가 다시 설정 됩니다. 작성 API를 통해 앱을 작성하는 경우 `Ocp-Apim-Subscription-Key` 값을 새 키로 변경해야 합니다.

## <a name="delete-account"></a>계정 삭제

계정을 삭제할 때 삭제되는 데이터에 대한 내용은 [데이터 스토리지 및 제거](luis-concept-data-storage.md#accounts)를 참조하세요.

## <a name="next-steps"></a>다음 단계

[작성 키](luis-concept-keys.md#authoring-key)에 대해 자세히 알아봅니다.

