---
title: Speech Services를 사용하여 음성 번역 | Microsoft Docs
description: Speech Service에서 Speech Translation을 사용하는 방법에 대해 알아봅니다.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: 7f39f284998489574049d82c44b3d3a0a3797adb
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/07/2018
ms.locfileid: "35383418"
---
# <a name="translate-speech-using-speech-service"></a>Speech Service를 사용하여 음성 번역

[Speech SDK](speech-sdk.md)는 응용 프로그램에서 음성 번역을 사용하는 가장 간단한 방법입니다. SDK는 서비스의 전체 기능을 제공합니다. 음성 번역을 수행하기 위한 기본 프로세스에는 다음 단계가 포함됩니다.

1. 음성 팩터리를 만들고 Speech Service 구독 키 또는 권한 부여 토큰을 제공합니다. 이때 원본 및 대상 번역 언어도 구성하고 텍스트 또는 음성 출력을 원하는지 여부를 지정합니다.

2. 팩터리에서 인식기를 가져옵니다. 번역을 위해 번역 인식기를 선택합니다. (다른 인식기는 *Speech to Text*용입니다. ) 사용 중인 오디오 원본에 따라 다양한 번역 인식기를 선택할 수 있습니다.

4. 필요한 경우, 비동기 작업에 대한 이벤트를 연결합니다. 중간 및 최종 결과가 있으면 인식기가 이벤트 처리기를 호출합니다. 결과가 없으면 응용 프로그램이 최종 번역 결과를 받습니다.

5. 인식 및 번역을 시작합니다.

# <a name="sdk-samples"></a>SDK 샘플

샘플의 최신 집합은 [Cognitive Services Speech SDK 샘플 GitHub 리포지토리](https://aka.ms/csspeech/samples)를 참조하세요.

# <a name="next-steps"></a>다음 단계

- [Speech 평가판 구독 가져오기](https://azure.microsoft.com/try/cognitive-services/)
- [C#에서 음성 인식](quickstart-csharp-windows.md)
