---
title: Azure에서 LUIS 앱에 미리 빌드된 도메인 사용 | Microsoft Docs
description: LUIS(Language Understanding Intelligent Service) 응용 프로그램에서 미리 빌드된 도메인을 사용하는 방법을 알아봅니다.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 12/13/2017
ms.author: diberry
ms.openlocfilehash: df57f9adf5bf7f5f652a77ddeafe950463c6a9fc
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/24/2018
ms.locfileid: "39224180"
---
# <a name="use-prebuilt-domains-in-luis-apps"></a>LUIS 앱에서 미리 빌드된 도메인 사용  

LUIS(Language Understanding)는 클라이언트 응용 프로그램의 일반 범주 또는 도메인에 함께 사용되는 [의도](luis-how-to-add-intents.md) 및 [엔터티](luis-concept-entity-types.md)의 미리 빌드된 집합인 ‘미리 빌드된 도메인’을 제공합니다. 미리 빌드된 도메인은 미리 학습되었으며 LUIS 앱에 추가할 준비가 되어 있습니다. 미리 빌드된 도메인의 의도 및 엔터티는 앱에 추가한 후 완전히 사용자 지정할 수 있습니다. 사용자에 맞게 시스템의 발화를 사용하여 의도와 엔터티를 학습시킬 수 있습니다. 전체 미리 빌드된 도메인을 사용자 지정의 시작점으로 사용하거나 미리 빌드된 도메인에서 몇몇 의도 또는 엔터티를 빌려올 수 있습니다. 

**미리 빌드된 도메인** 탭을 검색하여 앱에서 사용할 수 있는 다른 미리 빌드된 도메인을 확인합니다. 도메인 타일을 클릭하여 도메인을 앱에 추가하거나, 타일에서 **자세한 정보**를 클릭하여 의도 및 엔터티에 대해 알아봅니다.

> [!TIP]
> [미리 빌드된 도메인 참조](./luis-reference-prebuilt-domains.md)에서 미리 빌드된 도메인의 전체 목록을 찾을 수 있습니다.

![미리 빌드된 도메인 추가](./media/luis-how-to-prebuilt-domain-entities/add-prebuilt-domain.png)


## <a name="add-a-prebuilt-domain"></a>미리 빌드된 도메인 추가
**미리 빌드된 도메인** 탭에서 RestaurantReservation 도메인을 찾아 **도메인 추가**를 클릭합니다. 미리 빌드된 도메인이 LUIS 앱에 추가된 후, **의도**를 열고 RestaurantReservation.Reserve 의도를 클릭합니다. 많은 예제 발화가 이미 제공되고 엔터티로 레이블이 지정되어 있음을 알 수 있습니다.

![RestaurantReservation.Reserve 의도](./media/luis-how-to-prebuilt-domain-entities/prebuilt-domain-restaurant-reservation.png)


## <a name="designing-luis-apps-from-prebuilt-domains"></a>미리 빌드된 도메인에서 LUIS 앱 디자인
LUIS 앱에서 미리 빌드된 도메인을 사용하는 경우 전체 미리 빌드된 도메인을 사용자 지정하거나 해당 의도 및 엔터티 중 일부로만 시작할 수 있습니다.

## <a name="customizing-an-entire-prebuilt-domain"></a>전체 미리 빌드된 도메인 사용자 지정
미리 빌드된 도메인은 범용으로 디자인됩니다. 도메인에는 요구 사항에 맞게 앱을 사용자 지정하기 위해 선택할 수 있는 많은 의도와 엔터티가 포함됩니다. 전체 미리 빌드된 도메인을 사용자 지정하는 것부터 시작할 경우에는 앱에서 사용할 필요가 없는 의도와 엔터티를 삭제합니다. 이미 미리 빌드된 도메인이 제공하는 집합에 일부 의도나 엔터티를 추가할 수도 있습니다. 예를 들어, 스포츠 이벤트 앱에 대해 **Events** 미리 빌드된 도메인을 사용하는 경우에는 스포츠 팀에 대한 엔터티를 추가할 수 있습니다. LUIS에 [발화 제공](luis-how-to-add-example-utterances.md)을 시작할 경우에는 앱에 관련된 조건을 포함합니다. LUIS는 미리 빌드된 도메인의 의도와 엔터티를 인식하고 앱의 요구 사항에 맞게 조정하도록 학습됩니다. 

> [!TIP]
> 미리 빌드된 도메인의 의도와 엔터티는 함께 사용할 때 가장 성능이 좋습니다. 가능하면 동일한 도메인의 의도와 엔터티를 결합하는 것이 좋습니다.
> * 동일한 도메인의 관련 의도를 사용하는 것이 좋습니다. 예를 들어, **Places** 도메인의 `MakeReservation` 의도를 사용자 지정할 경우 **Events** 또는 **Utilities** 도메인의 Cancel 의도 대신 **Places** 도메인의 `Cancel` 의도를 선택합니다.

## <a name="changing-the-behavior-of-a-prebuilt-domain-intent"></a>미리 빌드된 도메인 의도의 동작 변경
미리 빌드된 도메인에 포함되는 의도가 LUIS 앱에 포함하려는 의도와 유사함을 알 수 있지만 해당 의도가 다르게 동작하도록 합니다. 예를 들어, **Places** 미리 빌드된 도메인은 음식점을 예약하기 위한 `MakeReservation` 의도를 제공하지만, 앱에서는 해당 의도를 사용하여 호텔을 예약하도록 합니다. 이 경우 호텔 예약에 대한 발화를 LUIS에 제공하고 `MakeReservation` 의도로 레이블을 지정하여 해당 의도의 동작을 수정할 수 있으므로, LUIS는 호텔 예약 요청에서 `MakeReservation` 의도를 인식하도록 다시 학습될 수 있습니다.

> [!TIP]
> 모든 도메인에서 사용하도록 사용자 지정할 수 있는 미리 빌드된 의도의 경우 Utilities 도메인을 체크 아웃합니다. 예를 들어, `Utilities.Repeat`를 앱에 추가하고 사용자가 응용 프로그램에서 반복하려는 모든 작업을 인식하도록 학습시킬 수 있습니다.


## <a name="next-step"></a>다음 단계

추가적인 예제 발화를 추가하여 미리 빌드된 도메인을 사용자 지정합니다.

> [!div class="nextstepaction"]
> [예제 발화 추가](./luis-how-to-add-example-utterances.md)

## <a name="additional-resources"></a>추가 리소스

미리 빌드된 도메인에 대한 자세한 내용은 [미리 빌드된 도메인 참조](./luis-reference-prebuilt-domains.md)를 참조하세요.
