---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 06/11/2021
ms.author: mimart
ms.openlocfilehash: cd6bddb6988555b336cb920c481fe675e9f7a276
ms.sourcegitcommit: 8651d19fca8c5f709cbb22bfcbe2fd4a1c8e429f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/14/2021
ms.locfileid: "112073081"
---
1. 만든 *my-api1* 애플리케이션을 선택하여 **개요** 페이지를 엽니다.
1. **관리** 에서 **API 표시** 를 선택합니다.
1. **애플리케이션 ID URI** 옆에 있는 **설정** 링크를 선택합니다. 기본값(GUID)을 `tasks-api`와 같은 고유 이름으로 바꾸고 **저장** 을 선택합니다. 웹 애플리케이션은 웹 API에 대한 액세스 토큰을 요청할 때 API에 대해 정의된 각 범위의 접두사로 이 URI를 추가해야 합니다.
1. **이 API에서 정의한 범위** 에서 **범위 추가** 를 선택합니다.
1. 다음 값을 입력하여 API에 대한 읽기 권한을 정의하는 범위를 만든 다음, **범위 추가** 를 선택합니다.
    1. **범위 이름**: `tasks.read`
    1. **관리자 동의 표시 이름**: `Read access to tasks API`
    1. **관리자 동의 설명**: `Allows read access to the tasks API`
1. **범위 추가** 를 선택하고 다음 값을 입력하여 API에 대한 쓰기 권한을 정의하는 범위를 추가하고 **범위 추가** 를 선택합니다.
    1. **범위 이름**: `tasks.write`
    1. **관리자 동의 표시 이름**: `Write access to tasks API`
    1. **관리자 동의 설명**: `Allows write access to the tasks API`