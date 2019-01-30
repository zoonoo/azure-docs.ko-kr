---
title: 포함 파일
description: 포함 파일
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: include
ms.custom: include file
ms.date: 12/21/2018
ms.author: diberry
ms.openlocfilehash: 0579b1e21e714e25e197cb5abf46793a38978d06
ms.sourcegitcommit: 7862449050a220133e5316f0030a259b1c6e3004
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/22/2018
ms.locfileid: "53755670"
---
클라이언트 애플리케이션은 발언에 의미가 있는지 또는 애플리케이션에 적합한지 여부를 알아야 합니다. 클라이언트 애플리케이션이 발언에 대답할 수 있는지 확인하기 위해 만들기 프로세스 과정에서 **없음** 의도가 각 애플리케이션에 추가됩니다.

LUIS가 발언에 대해 **없음** 의도를 반환하는 경우 클라이언트 애플리케이션에서는 사용자가 대화를 종료하려 하는지 물어보거나 대화를 계속 이어가기 위한 추가 지침을 제공할 수 있습니다. 

> [!CAUTION] 
> **없음** 의도를 비워 놓지 마세요. 

1. 왼쪽 패널에서 **의도**를 선택합니다.

2. **없음** 의도를 선택합니다. 사용자가 입력할 수 있지만 인적 자원 앱과 관련이 없는 세 개의 발언을 추가합니다.

    | 예제 발화|
    |--|
    |개가 짖어서 시끄럽다|
    |피자 주문해 줘|
    |바다의 펭귄|