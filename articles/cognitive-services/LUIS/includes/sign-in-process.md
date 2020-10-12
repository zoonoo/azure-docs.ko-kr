---
title: 포함 파일
description: 포함 파일
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 05/05/2020
ms.topic: include
ms.openlocfilehash: fda9df6c7e9651bbd3b0b70ad9d47f23c0c19d01
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91541437"
---
## <a name="sign-in-to-luis-portal"></a>LUIS 포털에 로그인

LUIS에 대한 새 사용자는 다음 절차를 수행해야 합니다.

1. [LUIS 포털](https://www.luis.ai)에 로그인한 후 국가/지역을 선택하고 사용 약관에 동의합니다. **내 앱**이 대신 표시되는 경우 LUIS 리소스가 이미 존재하므로 앱 만들기로 건너뛰어야 합니다. 지원되는 지역은 [작성 및 게시 지역과 관련 키](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions)를 방문하세요.

1. **Azure 리소스 만들기**를 선택한 다음, **앱을 마이그레이션할 제작 리소스 만들기**를 선택합니다.

    ![Language Understanding 제작 리소스의 유형을 선택합니다.](../media/luis-how-to-azure-subscription/sign-in-create-resource.png)

1. 리소스에 대한 세부 정보를 입력합니다.

    ![제작을 위한 새 리소스 만들기 창을 보여주는 스크린샷](../media/migrate-authoring-key/choose-authoring-resource-form.png)

    **새 제작 리소스를 만들** 때 다음 정보를 제공합니다.

    * **리소스 이름** - 사용자가 선택한 사용자 지정 이름으로, 작성 및 예측 엔드포인트 쿼리의 URL 중 일부로 사용됩니다.
    * **테넌트** - Azure 구독이 연결된 테넌트입니다.
    * **구독 이름** - 리소스에 대해 청구되는 구독입니다.
    * **리소스 그룹** - 사용자가 선택하거나 만드는 사용자 지정 리소스 그룹 이름입니다. 리소스 그룹을 사용하면 액세스 및 관리를 위해 Azure 리소스를 그룹화할 수 있습니다.
    * **위치** - 선택한 위치는 **리소스 그룹** 선택에 따라 결정됩니다.
    * **가격 책정 계층** - 가격 책정 계층은 초 및 월별 최대 트랜잭션 수를 결정합니다.

1. 만들 리소스에 대한 요약이 표시됩니다. **다음**을 선택합니다.

    ![Azure 계정에 연결하는 옵션이 있는 시작 페이지를 보여주는 스크린샷](../media/sign-in/sign-in-confirm-key-selection.png)

1. **계속**을 선택하여 확인합니다.

    ![Azure 계정에 연결한 후의 시작 페이지를 보여주는 스크린샷](../media/sign-in/sign-in-confirm-continue.png)
