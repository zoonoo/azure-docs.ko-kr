---
title: REST API를 사용하여 음성 인식
description: Speech Service에서 Speech to Text를 사용하는 방법 알아보기
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/16/2018
ms.author: v-jerkin
ms.openlocfilehash: 15db2d9b872c76d70fd531af07fb55c701e86494
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/27/2018
ms.locfileid: "39331340"
---
# <a name="recognize-speech-by-using-the-rest-api"></a>REST API를 사용하여 음성 인식

[!include[Selector](../../../includes/cognitive-services-speech-service-how-to-recognize-speech-selector.md)]

HTTP POST 요청을 사용하여 짧은 발언을 인식하는 데 REST API를 사용할 수 있습니다.

REST API는 SDK에서 지원하는 언어를 사용하지 않을 경우, 음성을 인식하는 가장 간단한 방법입니다.
서비스 끝점에 대한 HTTP POST 요청을 만들어 요청 본문으로 전체 발화를 전달합니다.
인식된 텍스트가 포함된 응답을 받습니다.

> [!NOTE]
> 발화는 REST API를 사용할 경우, 15초 이하로 제한됩니다.
> 긴 발언을 인식하는 방법에 대해서는 [Speech SDK](how-to-recognize-speech-csharp.md)를 참조하세요.

**Speech to Text** REST API에 대한 자세한 내용은 [REST API](rest-apis.md#speech-to-text)를 참조하세요. 이 기능이 작동하는지 확인하려면 GitHub에서 [REST API 샘플](https://github.com/Azure-Samples/SpeechToText-REST)을 다운로드합니다.

## <a name="next-steps"></a>다음 단계

- [REST API 개요 참조](rest-apis.md)
