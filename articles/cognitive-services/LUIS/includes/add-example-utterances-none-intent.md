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
ms.date: 11/05/2019
ms.author: diberry
ms.openlocfilehash: bbe40083159d433c0b5746834e1c530b23b03851
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73648125"
---
클라이언트 애플리케이션은 발언에 의미가 있는지 또는 애플리케이션에 적합한지 여부를 알아야 합니다. 클라이언트 애플리케이션이 발언에 대답할 수 있는지 확인하기 위해 만들기 프로세스 과정에서 **없음** 의도가 각 애플리케이션에 추가됩니다.

LUIS가 발언에 대해 **없음** 의도를 반환하는 경우 클라이언트 애플리케이션에서는 사용자가 대화를 종료하려 하는지 물어보거나 대화를 계속 이어가기 위한 추가 지침을 제공할 수 있습니다. 

**None** 의도를 비워 두면 주체 도메인 외부에서 예측해야 하는 발화가 기존 주체 도메인 의도 중 하나로 예측됩니다. 따라서 채팅 봇과 같은 클라이언트 애플리케이션은 잘못된 예측에 따라 잘못된 작업을 수행합니다. 

1. 왼쪽 패널에서 **의도**를 선택합니다.

1. **없음** 의도를 선택합니다. 사용자가 입력할 수 있지만 앱과 관련이 없는 세 개의 발화를 추가합니다.

    |`None` 예제 발화|
    |--|
    |`Barking dogs are annoying`|
    |`Order a pizza for me`|
    |`Penguins in the ocean`|
