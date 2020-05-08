---
title: '빠른 시작: 사용자 지정 음성 (미리 보기)-음성 서비스에서 사용자 지정 명령 사용'
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 사용자 지정 명령 응용 프로그램의 출력 음성을 지정 합니다.
services: cognitive-services
author: anhoang
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: anhoang
ms.openlocfilehash: c2b9b4d51e89975d988ed94bf85695bd8a1cc770
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82872447"
---
# <a name="quickstart-use-custom-commands-with-custom-voice-preview"></a>빠른 시작: 사용자 지정 음성 (미리 보기)으로 사용자 지정 명령 사용

[이전 문서](./quickstart-custom-speech-commands-create-parameters.md)에서는 매개 변수를 사용 하 여 명령에 응답 하는 새 사용자 지정 명령 프로젝트를 만들었습니다.

이 문서에서는 사용자가 만든 응용 프로그램에 대 한 사용자 지정 출력 음성을 선택 합니다.

## <a name="select-a-custom-voice"></a>사용자 지정 음성 선택

1. [이전에 만든](./quickstart-custom-speech-commands-create-parameters.md) 프로젝트 열기
1. 왼쪽 창에서 **설정** 을 선택 합니다.
1. 가운데 창에서 **사용자 지정 음성** 선택
1. 테이블에서 원하는 사용자 지정 또는 공개 음성을 선택 합니다.
1. **저장**을 선택합니다.

> [!div class="mx-imgBorder"]
> ![매개 변수가 있는 샘플 문장](media/custom-speech-commands/select-custom-voice.png)

> [!NOTE]
> - **공용 음성**의 경우 **신경망** 은 특정 지역 에서만 사용할 수 있습니다. 가용성을 확인 하려면 [지역/엔드포인트 별 표준 및 신경망](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#standard-and-neural-voices)을 참조 하세요.
> - **사용자 지정**음성의 경우 사용자 지정 음성 프로젝트 페이지에서 만들 수 있습니다. [사용자 지정 음성 시작을](./how-to-custom-voice.md)참조 하세요.

이제 응용 프로그램은 기본 음성 대신 선택한 음성으로 응답 합니다.

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [빠른 시작: 음성 SDK (미리 보기)를 사용 하 여 사용자 지정 명령 응용 프로그램에 연결](./quickstart-custom-speech-commands-speech-sdk.md)

