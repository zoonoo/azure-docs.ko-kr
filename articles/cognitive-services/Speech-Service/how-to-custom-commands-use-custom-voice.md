---
title: '방법: 사용자 지정 음성-음성 서비스에서 사용자 지정 명령 사용'
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 사용자 지정 명령 응용 프로그램의 출력 음성을 지정 합니다.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/16/2020
ms.author: sausin
ms.openlocfilehash: b1e488653f210089ee5ed6757fb2f3d1e4fb0fe4
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294300"
---
# <a name="use-custom-commands-with-custom-voice"></a>사용자 지정 음성과 함께 사용자 지정 명령 사용

이 문서에서는 사용자 지정 명령 응용 프로그램에 대 한 사용자 지정 출력 음성을 선택 하는 방법에 대해 알아봅니다.

## <a name="select-a-custom-voice"></a>사용자 지정 음성 선택

1. 사용자 지정 명령 응용 프로그램의 왼쪽 창에서 **설정** 을 선택 합니다.
1. 가운데 창에서 **사용자 지정 음성** 을 선택 합니다.
1. 테이블에서 원하는 사용자 지정 또는 공개 음성을 선택 합니다.
1. **저장**을 선택합니다.

> [!div class="mx-imgBorder"]
> ![매개 변수가 있는 샘플 문장](media/custom-commands/select-custom-voice.png)

> [!NOTE]
> - **공용 음성**의 경우 **신경망** 은 특정 지역 에서만 사용할 수 있습니다. 가용성을 확인 하려면 [지역/엔드포인트 별 표준 및 신경망](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#standard-and-neural-voices)을 참조 하세요.
> - **사용자 지정**음성의 경우 사용자 지정 음성 프로젝트 페이지에서 만들 수 있습니다. [사용자 지정 음성 시작을](./how-to-custom-voice.md)참조 하세요.

이제 응용 프로그램은 기본 음성 대신 선택한 음성으로 응답 합니다.
