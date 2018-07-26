---
title: Text to Speech 정보 | Microsoft Docs
description: Text to Speech의 기능 개요입니다.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: e3543c2056934372a2795bd50f340432ceec3b6d
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/26/2018
ms.locfileid: "39257949"
---
# <a name="about-the-text-to-speech-api"></a>Text to Speech API 정보

Speech Service의 TTS(**Text to Speech**) API는 입력 텍스트를 자연스러운 발음으로 변환합니다(*음성 합성*이라고도 함).

음성을 생성하기 위해 응용 프로그램은 Speech Service에 HTTP POST 요청을 보냅니다. 여기에서 텍스트는 인간의 음성으로 합성된 후 오디오 파일로 반환됩니다. 다양한 음성 및 언어가 지원됩니다.

음성 합성이 채택되는 시나리오는 다음과 같습니다.

* *접근성 개선:* **Text to Speech** 기술을 사용하여 콘텐츠 소유자와 게시자는 사람들이 해당 콘텐츠와 상호 작용하는 다양한 방식에 대응합니다. 시각 장애가 있거나 난독증이 있는 사람들은 콘텐츠를 들을 수 있다는 사실에 고마움을 느낍니다. 또한 음성 출력은 사람들이 출퇴근하거나 운동을 하는 동안 모바일 장치에서 신문이나 블로그 같은 텍스트 콘텐츠를 보다 쉽게 즐길 수 있도록 해줍니다.

* *멀티태스킹 시나리오에 대응:* **Text to Speech** 기술은 사람들이 운전 중이거나 편안하게 읽을 수 없는 환경에 있을 때 중요한 정보를 빠르고 편안하게 습득할 수 있도록 합니다. 이러한 분야에서 일반적인 응용 사례가 바로 네비게이션입니다. 

* *여러 모드를 사용하여 학습 개선:* 사용자마다 가장 잘 학습할 수 있는 방식은 다릅니다. 온라인 학습 전문가에 따르면 음성과 텍스트를 함께 제공할 경우 정보를 보다 쉽게 학습하고 지속할 수 있다고 합니다.

* *직관적인 봇 또는 도우미 제공:* 말하는 능력은 지능형 챗봇이나 가상 도우미에게 필수적인 부분으로 통합될 수 있습니다. 점점 더 많은 회사들이 고객에게 참여형 서비스 환경을 제공하기 위해 챗봇을 개발하고 있습니다. 음성은 봇 응답을 음성(예: 전화)으로 받을 수 있도록 하여 한 차원 높아진 서비스를 가능하게 합니다.

## <a name="voice-support"></a>음성 지원

Microsoft **Text-to-Speech** 서비스는 45개 이상의 언어 및 로캘로 75가지 이상의 음성을 제공합니다. 이러한 표준 "음성 글꼴"을 사용하려는 경우 서비스의 REST API를 호출할 때 몇 개의 매개 변수를 사용해서 음성 이름을 지정하기만 하면 됩니다. 지원되는 음성에 대한 자세한 내용은 [지원되는 언어](https://docs.microsoft.com/azure/cognitive-services/speech-service/supported-languages#text-to-speech)를 참조하세요. 

독특한 음성을 응용 프로그램에 사용하려면 자체 음성 샘플에서 [사용자 지정 음성 글꼴](how-to-customize-voice-font.md)을 만들면 됩니다.

## <a name="next-steps"></a>다음 단계

* [음성 평가판 구독 가져오기](https://azure.microsoft.com/try/cognitive-services/)
* [C#에서 음성을 인식하는 방법 참조](quickstart-csharp-dotnet-windows.md)
