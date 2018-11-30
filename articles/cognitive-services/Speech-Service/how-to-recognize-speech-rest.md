---
title: REST API를 사용하여 음성 인식
titleSuffix: Azure Cognitive Services
description: Speech to Text API를 Cognitive Services Speech 서비스에 사용하는 방법을 알아봅니다.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 07/16/2018
ms.author: erhopf
ms.openlocfilehash: ceb8e929520becd2bf097fefc9c6fe8959b1bf85
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2018
ms.locfileid: "51709817"
---
# <a name="recognize-speech-by-using-the-rest-api"></a>REST API를 사용하여 음성 인식

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-how-to-recognize-speech-selector.md)]

HTTP POST 요청을 사용하여 짧은 발언을 인식하는 데 REST API를 사용할 수 있습니다.

REST API는 [SDK](speech-sdk.md)에서 지원하는 언어를 사용하지 않을 경우, 음성을 인식하는 가장 간단한 방법입니다. 서비스 엔드포인트에 대한 HTTP POST 요청을 만들어 요청 본문으로 전체 발언을 전달합니다. 인식된 텍스트가 포함된 응답을 받습니다.

> [!NOTE]
> REST API를 사용할 경우 발언이 15초 이하로 제한됩니다.
> 긴 발언을 인식하는 방법에 대해서는 [Speech SDK](how-to-recognize-speech-csharp.md)를 참조하세요.

**Speech to Text** REST API에 대한 자세한 내용은 [REST API](rest-apis.md#speech-to-text-api) 문서를 참조하세요. 이 API가 실제로 작동하는 모습을 보려면 GitHub에서 [REST API 샘플](https://github.com/Azure-Samples/SpeechToText-REST)을 다운로드합니다.

## <a name="next-steps"></a>다음 단계

- [REST API 개요](rest-apis.md)를 참조하세요.
