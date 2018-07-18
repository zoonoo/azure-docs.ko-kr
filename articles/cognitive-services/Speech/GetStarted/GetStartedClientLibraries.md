---
title: 클라이언트 라이브러리를 사용하여 Microsoft Speech Recognition API 시작 | Microsoft Docs
description: Microsoft Cognitive Services의 Microsoft Speech Service 클라이언트 라이브러리를 사용하여 음성 오디오를 텍스트로 변환하는 응용 프로그램을 개발합니다.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/15/2017
ms.author: zhouwang
ms.openlocfilehash: 5abe5bc48c2bd73d0facf33e41a8076df2972153
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35373799"
---
# <a name="get-started-with-speech-service-client-libraries"></a>Speech Service 클라이언트 라이브러리 시작

Speech Service는 REST API를 통해 직접 HTTP를 요청하는 것 외에도 개발자에게 다양한 언어의 Speech 클라이언트 라이브러리를 제공합니다. Speech 클라이언트 라이브러리의 특징은 다음과 같습니다.

- 실시간의 중간 결과, 긴 오디오 스트림(최대 10분) 및 연속 인식과 같은 음성 인식의 고급 기능을 지원합니다.
- 기본 설정 언어로 된 간단하고 관용적인 API를 제공합니다.
- 낮은 수준의 통신 세부 정보를 숨깁니다.

현재 사용할 수 있는 Speech 클라이언트 라이브러리는 다음과 같습니다.

- [C# 데스크톱 라이브러리](GetStartedCSharpDesktop.md)
- [C# 서비스 라이브러리](GetStartedCSharpServiceLibrary.md)
- [JavaScript 라이브러리](GetStartedJSWebsockets.md)
- [Android용 Java 라이브러리](GetStartedJavaAndroid.md)
- [iOS용 Objective-C 라이브러리](Get-Started-ObjectiveC-iOS.md)

## <a name="additional-resources"></a>추가 리소스

- [샘플](../samples.md) 페이지에서 Speech 클라이언트 라이브러리를 사용하는 완전한 샘플을 제공합니다.
- 아직 지원되지 않는 클라이언트 라이브러리가 필요한 경우 사용자 고유의 SDK를 만들 수 있습니다. 플랫폼에서 [Speech WebSocket 프로토콜](../API-Reference-REST/websocketprotocol.md)을 구현하고 선택한 언어를 사용합니다.

## <a name="license"></a>라이선스

Cognitive Services SDK 및 샘플은 모두 MIT 라이선스를 통해 사용이 허가됩니다. 자세한 내용은 [라이선스](https://github.com/Microsoft/Cognitive-Speech-STT-JavaScript/blob/master/LICENSE.md)를 참조하세요.
