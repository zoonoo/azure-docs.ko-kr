---
title: 사용자 지정 키워드-음성 서비스
titleSuffix: Azure Cognitive Services
description: 음성 SDK (소프트웨어 개발 키트)를 사용 하는 사용자 지정 키워드의 기능, 기능 및 제한 사항에 대 한 개요입니다.
services: cognitive-services
author: hasyashah
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: hasshah
ms.openlocfilehash: 0a6df626f9567b51a8371c17004b454f9c5b4597
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83202042"
---
# <a name="what-is-a-keyword"></a>키워드 란 무엇 인가요?

키워드는 제품이 음성으로 활성화 될 수 있도록 하는 단어 또는 짧은 문구입니다. 예를 들어 "안녕하세요 Cortana"는 Cortana 길잡이에 대 한 키워드입니다. 음성 정품 인증을 사용 하면 사용자가 키워드를 말하는 방법으로 제품을 완벽 하 게 자유롭게 조작할 수 있습니다. 제품이 키워드를 지속적으로 수신 하 게 되 면 데이터가 가능한 한 비공개로 유지 되도록 감지 될 때까지 모든 오디오가 사용자의 장치에서 로컬로 처리 됩니다.

## <a name="core-features-of-custom-keyword"></a>사용자 지정 키워드의 핵심 기능

사용자 지정 키워드의 사용자 지정, 성능 및 통합 기능을 사용 하면 제품의 비전과 사용자 요구에 가장 적합 한 음성 활성화를 조정할 수 있습니다.

| 기능 | Description |
|----------|----------|
| 키워드 사용자 지정 | 브랜드의 확장으로 키워드는 고객과 강화 하는 자본을 제공 합니다. Speech Studio의 사용자 지정 키워드 포털을 사용 하면 브랜드를 가장 잘 나타내는 단어 또는 짧은 구를 지정할 수 있습니다. 생성 된 키워드 모델에 의해 적용 되는 올바른 발음를 선택 하 여 키워드를 추가로 개인 설정할 수 있습니다.
| 키워드 확인 | 로컬에서 검색 되는 키워드에 높은 신뢰도가 있는 경우 사용자가 키워드를 추가 하 여 추가 확인을 위해 오디오를 클라우드로 보냅니다. 키워드 확인은 잘못 된 로컬 검색의 영향을 줄이고 사용자의 개인 정보를 보호 하 여 추가 보안 계층을 제공 합니다.
| 음성 도우미 & Speech SDK 통합 | Speech Studio에서 사용자 지정 키워드를 통해 생성 된 키워드는 음성 SDK를 통해 장치 또는 응용 프로그램 내에서 쉽게 통합 될 수 있습니다. SDK가 Speech Studio에서 제공 하는 키워드 모델을 가리키도록 하 고 제품이 음성으로 활성화 되며 키워드 확인으로 지원 됩니다. 사용자 고유의 [음성 도우미](voice-assistants.md)를 빌드하여 제품의 음성 환경을 완료할 수 있습니다.

## <a name="get-started-with-custom-keywords"></a>사용자 지정 키워드 시작

* 자습서: [Speech Studio를 사용 하 여 사용자 지정 키워드를 만드는](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws) 방법
* 자습서: 음성 SDK를 사용 하 여 제품을 음성으로 활성화 하는 방법 [(c # 사용)](tutorial-voice-enable-your-bot-speech-sdk.md)
* 빠른 시작: [c #을 사용 하는 유니버설 Windows 플랫폼에서 음성 SDK를](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/uwp/keyword-recognizer) 사용 하 여 키워드 인식
* 빠른 시작: [Java를 사용 하 여 Android에서 음성 SDK로 키워드 인식](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android/keyword-recognizer)

## <a name="next-steps"></a>다음 단계

* [평가판 Speech Service 구독 키 받기](get-started.md)
* [Speech SDK 가져오기](speech-sdk.md)
* [음성 도우미에 대 한 자세한 정보](voice-assistants.md)
