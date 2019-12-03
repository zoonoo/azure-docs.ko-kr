---
title: 포함 파일
description: 포함 파일
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 11/20/2019
ms.author: diberry
ms.openlocfilehash: e4147fbb27c8538f801f6c49f8b535a283faf50f
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325896"
---
클라이언트 애플리케이션은 발언에 의미가 있는지 또는 애플리케이션에 적합한지 여부를 알아야 합니다. **없음** 의도는 생성 프로세스의 일부로 각 애플리케이션에 추가되어 클라이언트 애플리케이션이 발화에 응답하지 않아야 하는지 여부를 확인합니다.

LUIS가 발언에 대해 **없음** 의도를 반환하는 경우 클라이언트 애플리케이션에서는 사용자가 대화를 종료하려 하는지 물어보거나 대화를 계속 이어가기 위한 추가 지침을 제공할 수 있습니다. 

**None** 의도를 비워 두면 주체 도메인 외부에서 예측해야 하는 발화가 기존 주체 도메인 의도 중 하나로 예측됩니다. 따라서 채팅 봇과 같은 클라이언트 애플리케이션은 잘못된 예측에 따라 잘못된 작업을 수행합니다. 

1. 왼쪽 패널에서 **의도**를 선택합니다.

1. **없음** 의도를 선택합니다. 사용자가 입력할 수 있지만 피자 주문 앱과 관련이 없는 세 개의 발화를 추가합니다.

    |`None` 예제 발화|
    |--|
    |`Barking dogs are annoying`|
    |`Order a pizza for me`|
    |`Penguins in the ocean`|

    이러한 예제에서는 `pizza`, `cheese`, `crust`, `pickup` `deliver`와 같이 주체 도메인에서 원하는 단어를 사용하지 않아야 합니다.