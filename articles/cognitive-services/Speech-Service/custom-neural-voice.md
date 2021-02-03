---
title: 사용자 지정 신경망 개요-음성 서비스
titleSuffix: Azure Cognitive Services
description: 사용자 지정 신경망은 사용자 고유의 오디오 데이터를 샘플로 제공 하 여 응용 프로그램에 대 한 일종의 사용자 지정 된 가상 음성을 만들 수 있도록 하는 텍스트 음성 변환 기능입니다.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/01/2020
ms.author: trbye
ms.openlocfilehash: 9e230ff4e84eba7b61846540fef2915765fa4f77
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99509698"
---
# <a name="what-is-custom-neural-voice"></a>사용자 지정 신경망 이란?

사용자 지정 신경망은 사용자 고유의 오디오 데이터를 샘플로 제공 하 여 응용 프로그램에 대 한 일종의 사용자 지정 된 가상 음성을 만들 수 있도록 하는 TTS ( [텍스트 음성 변환](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech) ) 기능입니다. 텍스트 음성 변환은 선택한 음성 처럼 소리나는 기계 학습 모델을 사용 하 여 텍스트를 합성 음성으로 변환 하는 방식으로 작동 합니다. [REST API](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-text-to-speech)를 사용 하 여 앱이 [미리 작성](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#neural-voices) 된 음성 또는 사용자 지정 신경망 기능을 통해 개발한 고유한 [사용자 지정 음성](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-custom-voice-prepare-data) 모델을 사용 하도록 설정할 수 있습니다. 사용자 지정 신경망은 인간 음성과 비교할 때 종종 구분 될 수 없는 자연 스러운 음 음성을 만드는 신경망을 기반으로 합니다.
사용자 지정 신경망의 실제적이 고 자연 스러운 음성은 브랜드, personify 컴퓨터를 나타낼 수 있으며, 사용자가 자연스럽 게 conversationally 응용 프로그램을 조작할 수 있습니다.

> [!NOTE]
> 사용자 지정 신경망 기능에는 등록이 필요 하며,이 기능에 대 한 액세스는 Microsoft의 자격 및 사용 조건에 따라 제한 됩니다. 이 기능을 사용 하려는 고객은 [흡입구 형식](https://aka.ms/customneural)으로 사용 사례를 등록 해야 합니다.

## <a name="the-basics-of-custom-neural-voice"></a>사용자 지정 신경망의 기본 사항

사용자 지정 신경망에 사용 되는 기본 신경망 기술은 텍스트 분석기, 신경망 모델 및 신경망 이라는 세 가지 주요 구성 요소로 구성 됩니다. 텍스트에서 자연 스러운 합성 음성을 생성 하기 위해 텍스트는 음소 시퀀스 형태로 출력을 제공 하는 텍스트 분석기에 대 한 첫 번째 입력입니다. 음소은 특정 언어로 한 단어를 구별 하는 사운드의 기본 단위입니다. 음소 시퀀스는 텍스트에 제공 된 단어의 발음를 정의 합니다. 

그런 다음 음소 시퀀스는 timbre, 말하는 스타일, 속도, intonations 및 스트레스 패턴과 같은 음성 신호를 정의 하는 음향 기능을 예측 하기 위해 신경망 모델에 들어갑니다. 마지막으로 신경망은 가상 음성이 생성 될 수 있도록 음향 기능을 가청 전파로 변환 합니다.

![사용자 지정 신경망의 소개 이미지입니다.](./media/custom-voice/cnv-intro.png)

신경망 음성 모델은 휴먼 음성의 기록 샘플을 기반으로 심층 신경망을 사용 하 여 학습 합니다. 이 [블로그에서](https://techcommunity.microsoft.com/t5/azure-ai/neural-text-to-speech-extends-support-to-15-more-languages-with/ba-p/1505911)신경망은 신경망의 최신 신경망 합성 모델에서 작동 하는 방법을 설명 합니다. 또한이 블로그에서는 대상 스피커에서 2 시간 미만의 음성 데이터 (또는 2000 미만의 기록 된 길이 발언 미만)로 유니버설 기본 모델을 적용할 수 있는 방법에 대해 설명 하 고 대상 스피커의 음성에서 이야기 하는 방법에 대해 알아봅니다. 신경망을 학습 하는 방법에 대 한 자세한 내용은 [블로그 게시물](https://techcommunity.microsoft.com/t5/azure-ai/azure-neural-tts-upgraded-with-hifinet-achieving-higher-audio/ba-p/1847860)을 참조 하세요.

사용자 지정 신경망의 사용자 지정 기능을 사용 하 여 사용자 시나리오에 맞게 신경망을 조정할 수 있습니다. 사용자 지정 신경망을 만들려면 [Speech Studio](https://speech.microsoft.com/customvoice) 를 사용 하 여 기록 된 오디오 및 해당 스크립트를 업로드 하 고, 모델을 학습 하 고, 음성을 사용자 지정 끝점에 배포 합니다. 사용 사례에 따라 사용자 지정 신경망을 사용 하 여 텍스트를 실시간으로 변환 하거나 (예: 스마트 가상 길잡이에서 사용 됨), 사용자가 제공 하는 텍스트 입력으로 오디오 콘텐츠를 오프 라인 (예: 전자 학습 응용 프로그램의 명령 또는 오디오 설명서에서 사용)으로 변환할 수 있습니다. [REST API](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-text-to-speech), [음성 SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started-text-to-speech?tabs=script%2Cwindowsinstall&pivots=programming-language-csharp)또는 [웹 포털](https://speech.microsoft.com/audiocontentcreation)을 통해 사용할 수 있습니다.

## <a name="terms-and-definitions"></a>용어 및 정의

| **기간**      | **정의**                                                                                                                                                                                                                                                                                                                                                                                       |
|---------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 음성 모델   | 대상 스피커의 고유한 vocal 특성을 모방할 수 있는 텍스트 음성 변환 모델입니다. 음성 *모델* 을 *음성 글꼴이* 나 *합성 음성이* 라고도 합니다. 음성 모델은 사람이 읽을 수 없고 오디오 녹음을 포함 하지 않는 이진 형식의 매개 변수 집합입니다. 휴먼 음성의 오디오를 파생 시키거나 구성 하기 위해 리버스 엔지니어링할 수 없습니다. |
| 성우  | 음성 인재의 음성 처럼 소리를 내기 위한 음성 모델을 만드는 데 사용 되는 음성이 기록 되 고 사용 되는 개인 또는 대상 스피커입니다.                                                                                                                                                                                                                                                   |
| 표준 TTS  | 음성 언어를 음성으로 구분 하는 TTS의 표준 또는 "기존" 메서드로, 기존 프로그래밍 또는 통계 방법을 사용 하 여 다시 혼합 하 고 일치 시킬 수 있습니다.                                                                                                                                                                                                    |
| 신경망    | 신경망을 사용 하는 심층 신경망을 사용 하는 합성 음성은 절차적 프로그래밍 또는 통계 방법을 사용 하는 대신 윗주를 자연 스러운 인간 음성으로 결합 하는 방법을 "학습" 합니다. 신경망은 대상 음성 인재 기록 외에도 다양 한 스피커의 음성 기록을 사용 하 여 작성 된 원본 라이브러리/기본 모델을 사용 합니다.          |
| 학습 데이터 | 음성 인재 오디오 녹음 및 연결 된 텍스트를 포함 하는 사용자 지정 신경망 학습 데이터 집합입니다.                                                                                                                                                                                                                                                               |
| Persona       | 사용자는이 음성을 사용할 사용자를 설명 합니다. 좋은 가상 사용자 디자인은 이미 생성 된 사용 가능한 음성 모델을 선택 하거나 새 음성 인재을 캐스트 하 고 기록 하 여 처음부터 다시 시작 하는 모든 음성 생성을 알립니다.                                                                                                |
| 스크립트        | 스크립트는 음성 인재 길이 발언를 포함 하는 텍스트 파일입니다. "*길이 발언*" 이라는 용어는 전체 문장과 짧은 구를 모두 포함 합니다.                                                                                                                                                                                                                               |

## <a name="responsible-use-of-ai"></a>AI의 책임 있는 사용

사용자 지정 신경망을 사용 하는 방법을 알아보려면 [투명성 메모](https://docs.microsoft.com/legal/cognitive-services/speech-service/custom-neural-voice/transparency-note-custom-neural-voice)를 참조 하세요. Microsoft의 투명 정보는 AI 기술의 작동 방식을 이해 하는 데 도움을 주기 위해 작성 된 시스템 소유자는 시스템 성능 및 동작에 영향을 줄 수 있으며 기술, 사용자 및 환경을 포함 하 여 전체 시스템에 대해 생각 하는 중요성을 이해 하는 데 도움을 주기 위한 것입니다.

## <a name="next-steps"></a>다음 단계

* [Custom Voice 시작](how-to-custom-voice.md)
* [사용자 지정 음성 끝점 만들기 및 사용](how-to-custom-voice-create-voice.md)