---
title: 인식 모드를 선택하는 방법 | Microsoft Docs
description: 최상의 인식 모드를 선택하는 방법입니다.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/15/2017
ms.author: zhouwang
ms.openlocfilehash: 4f02b683dde16b537ae5554e6435c068f0fcb808
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35373791"
---
# <a name="speech-recognition-modes"></a>음성 인식 모드

Microsoft의 *Speech to Text* API는 여러 음성 인식 모드를 지원합니다. 응용 프로그램에 가장 적합한 인식 결과를 생성하는 모드를 선택합니다.

| Mode | 설명 |
|---|---|
| *대화형* | 대화형 사용자 응용 프로그램 시나리오에 대한 "명령 및 제어" 인식입니다. 사용자는 응용 프로그램에 대한 명령으로 의도한 짧은 구를 말합니다. |
| *받아쓰기* | 받아쓰기 시나리오에 대한 연속 인식입니다. 사용자는 텍스트로 표시되는 긴 문장을 말합니다. 사용자는 더 정중한 발성 특성을 채택합니다. |
| *대화* | 인간 사이의 대화 전사에 대한 연속 인식입니다. 사용자는 덜 정중한 발성 특성을 채택하고, 긴 문장과 짧은 구 사이를 번갈아 사용할 수 있습니다.

> [!NOTE]
> 이러한 모드는 [REST API](../GetStarted/GetStartedREST.md)를 사용할 때 적용됩니다. [클라이언트 라이브러리](../GetStarted/GetStartedClientLibraries.md)는 다른 매개 변수를 사용하여 인식 모드를 지정합니다. 자세한 내용은 선택한 클라이언트 라이브러리를 참조하세요.

자세한 내용은 [인식 모드](../concepts.md#recognition-modes) 페이지를 참조하세요.
