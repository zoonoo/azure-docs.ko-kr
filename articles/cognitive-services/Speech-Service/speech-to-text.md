---
title: 음성-텍스트 - 음성 서비스
titleSuffix: Azure Cognitive Services
description: 음성-텍스트 기능을 통해 오디오 스트림을 텍스트로 실시간으로 녹음할 수 있습니다. 응용 프로그램, 도구 또는 장치는 이 텍스트 입력을 사용, 표시 및 조치를 취할 수 있습니다. 이 서비스는 텍스트 음성 변환(음성 합성) 및 음성 변환 기능과 원활하게 작동합니다.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: dapine
ms.openlocfilehash: 2854f4e8d91164c8ae1f35761d6f605cae725245
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80052639"
---
# <a name="what-is-speech-to-text"></a>음성 텍스트 변환이란?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

음성 인식이라고도 하는 음성 서비스에서 음성-텍스트 변환을 통해 오디오 스트림을 텍스트로 실시간으로 녹음할 수 있습니다. 응용 프로그램, 도구 또는 장치는 명령 입력으로 이 텍스트를 사용, 표시 및 조치를 취할 수 있습니다. 이 서비스는 Microsoft가 Cortana 및 Office 제품에 사용하는 것과 동일한 인식 기술에 의해 구동됩니다. <a href="./speech-translation.md" target="_blank">번역 <span class="docon docon-navigate-external x-hidden-focus"></span> </a> 및 텍스트 음성 <a href="./text-to-speech.md" target="_blank">변환 <span class="docon docon-navigate-external x-hidden-focus"></span> </a> 서비스 오퍼링과 원활하게 작동합니다. 사용 가능한 음성-텍스트 언어의 전체 목록은 [지원되는 언어를](language-support.md#speech-to-text)참조하십시오.

음성-텍스트 서비스는 유니버설 언어 모델을 사용하는 데 기본설정이 됩니다. 이 모델은 Microsoft 소유 데이터를 사용하여 학습되었으며 클라우드에 배포되었습니다. 대화형 및 받아쓰기 시나리오에 최적입니다. 고유한 환경에서 음성-텍스트 변환을 사용하여 사용자 지정 어쿠스틱, 언어 및 발음 모델을 만들고 학습할 수 있습니다. 사용자 지정은 주변 소음 또는 업계별 어휘를 해결하는 데 유용합니다.

> [!NOTE]
> 빙 연설은 2019년 10월 15일에 폐기되었습니다. 응용 프로그램, 도구 또는 제품이 Bing 음성 API를 사용하는 경우 음성 서비스로 마이그레이션하는 데 도움이 되는 가이드를 만들었습니다.
> - [Bing 음성에서 음성 서비스로 마이그레이션](how-to-migrate-from-bing-speech.md)

## <a name="get-started-with-speech-to-text"></a>음성-텍스트 시작

음성-텍스트 서비스는 [음성 SDK를](speech-sdk.md)통해 사용할 수 있습니다. 퀵스타트로 사용할 수 있는 몇 가지 일반적인 시나리오가 있으며, 다양한 언어와 플랫폼에서 제공됩니다.

 - [빠른 시작: 마이크 입력으로 음성 인식](quickstarts/speech-to-text-from-microphone.md)
 - [빠른 시작: 파일에서 음성 인식](quickstarts/speech-to-text-from-file.md)
 - [빠른 시작: Blob 저장소에 저장된 음성 인식](quickstarts/from-blob.md)

음성-텍스트 REST 서비스를 사용하려면 [REST API를](rest-speech-to-text.md)참조하십시오.

## <a name="tutorials-and-sample-code"></a>자습서 및 샘플 코드

Speech Service를 사용할 기회가 있으면 Speech SDK 및 LUIS를 사용하여 음성에서 의도를 인식하는 방법을 설명하는 자습서를 사용해 보세요.

- [자습서: 음성 SDK 및 LUIS를 사용하여 음성의 의도를 인식합니다. #](how-to-recognize-intents-from-speech-csharp.md)

음성 SDK에 대한 샘플 코드는 GitHub에서 사용할 수 있습니다. 이러한 샘플은 파일 또는 스트림에서 오디오 읽기, 연속 및 1단계 인식 및 사용자 지정 모델 사용과 같은 일반적인 시나리오를 다룹니다.

- [음성-텍스트 샘플(SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [일괄 처리 기록 샘플(REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)

## <a name="customization"></a>사용자 지정

표준 음성 서비스 모델 외에도 사용자 지정 모델을 만들 수 있습니다. 사용자 지정은 말하기 스타일, 어휘 및 배경 잡음과 같은 음성 인식 장벽을 극복하는 데 도움이 [되며 사용자 지정 음성을](how-to-custom-speech.md)참조하십시오. 사용자 지정 옵션은 언어/로캘에 따라 다르며 [지원되는 언어를](supported-languages.md) 참조하여 지원을 확인합니다.

[!INCLUDE [speech-reference-doc-links](includes/speech-reference-doc-links.md)]

## <a name="next-steps"></a>다음 단계

- [평가판 Speech Service 구독 키 받기](get-started.md)
- [Speech SDK 가져오기](speech-sdk.md)
