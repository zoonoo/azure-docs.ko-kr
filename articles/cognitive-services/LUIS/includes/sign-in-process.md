---
title: 포함 파일
description: 포함 파일
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: include file
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.date: 10/23/2019
ms.author: diberry
ms.openlocfilehash: 1effd07cef439a6257028549b7b7114b742eb478
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73488801"
---
## <a name="sign-in-to-luis-portal"></a>LUIS 포털에 로그인

LUIS에 대한 새 사용자는 다음 절차를 수행해야 합니다.

1. [LUIS 포털](https://www.luis.ai)에 로그인하고 국가를 선택하고 사용 약관에 동의합니다.
1. **Azure 리소스 만들기**를 선택한 다음, **앱을 마이그레이션할 제작 리소스 만들기**를 선택합니다.

    ![Language Understanding 제작 리소스의 유형을 선택합니다.](../media/luis-how-to-azure-subscription/sign-in-create-resource.png)

1. 리소스에 대한 세부 정보를 입력합니다.

    ![제작 리소스 만들기](../media/migrate-authoring-key/choose-authoring-resource-form.png)

    **새 제작 리소스를 만들** 때 다음 정보를 제공합니다. 

    * **리소스 이름** - 사용자가 선택한 사용자 지정 이름으로, 작성 및 예측 엔드포인트 쿼리의 URL 중 일부로 사용됩니다.
    * **테넌트** - Azure 구독이 연결된 테넌트입니다. 
    * **구독 이름** - 리소스에 대해 청구되는 구독입니다.
    * **리소스 그룹** - 사용자가 선택하거나 만드는 사용자 지정 리소스 그룹 이름입니다. 리소스 그룹을 사용하면 액세스 및 관리를 위해 Azure 리소스를 그룹화할 수 있습니다. 
    * **위치** - 선택한 위치는 **리소스 그룹** 선택에 따라 결정됩니다.
    * **가격 책정 계층** - 가격 책정 계층은 초 및 월별 최대 트랜잭션 수를 결정합니다.

1. 만들 리소스에 대한 요약이 표시됩니다. **다음**을 선택합니다.

    ![제작 리소스 만들기](../media/sign-in/sign-in-confirm-key-selection.png)

1. **계속**을 선택하여 확인합니다. 

    ![제작 리소스 만들기](../media/sign-in/sign-in-confirm-continue.png)