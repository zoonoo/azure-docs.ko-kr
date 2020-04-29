---
title: 시나리오 가용성-음성 서비스
titleSuffix: Azure Cognitive Services
description: 음성 SDK는 다양 한 프로그래밍 언어 및 환경에서 다양 한 시나리오를 제공 합니다. 모든 시나리오를 모든 프로그래밍 언어 또는 모든 환경에서 아직 사용할 수 있는 것은 아닙니다. 각 시나리오의 가용성은 아래에 나와 있습니다.
services: cognitive-services
author: chrisbasoglu
manager: xdh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: cbasoglu
ms.openlocfilehash: a72e5055a3b0f858a732ec28eeab511b09dd450c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2020
ms.locfileid: "81400154"
---
# <a name="scenario-availability"></a>시나리오 가용성

음성 SDK는 다양 한 프로그래밍 언어 및 환경에서 다양 한 시나리오를 제공 합니다. 모든 시나리오를 모든 프로그래밍 언어 또는 모든 환경에서 아직 사용할 수 있는 것은 아닙니다. 각 시나리오의 가용성은 아래에 나와 있습니다.

- **SR (음성 인식), 구 목록, 의도, 번역 및 온-프레미스 컨테이너**
  - C + +/Windows & Linux & macOS
  - C # (Framework & .NET Core)/Windows & UWP & Unity & Xamarin & Linux & macOS
  - Java (Jre 및 Android)
  - JavaScript (브라우저 및 NodeJS)
  - Python
  - Swift
  - Objective-C  
- **텍스트 음성 변환 (TTS)**
  - C + +/Windows & Linux
  - C #/Windows & UWP & Unity
  - Java (Jre 및 Android)
  - Python
  - Swift
  - Objective-C
  - TTS REST API은 다른 모든 상황에서 사용할 수 있습니다.
- **KWS (Keyword 발견)**
  - C + +/Windows & Linux
  - C #/Windows & Linux
  - Python/Windows & Linux
  - Java/Windows & Linux & Android (음성 장치 SDK)
  - KWS (Keyword 발견) 기능은 모든 마이크 종류에 사용할 수 있지만, 공식 KWS 지원은 현재 Azure Kinect 진한 하드웨어 또는 음성 장치 SDK에 있는 마이크 배열로 제한 되어 있습니다.
- **음성 도우미**
  - C + +/Windows & Linux & macOS
  - C#/Windows
  - Java/Windows & Linux & macOS & Android (음성 장치 SDK)
- **대화 전사**
  - C + +/Windows & Linux
  - C # (Framework & .NET Core)/Windows & UWP & Linux
  - Java/Windows & Linux & Android (음성 장치 SDK)
- **다중 디바이스 대화**
  - C + +/Windows
  - C # (Framework & .NET Core)/Windows
- **콜 센터 기록**
  - REST API 및 모든 상황에서 사용할 수 있습니다.
- **코덱 압축 오디오 입력**
  - C + +/Linux
  - C #/Linux
  - Java/Linux, Android 및 iOS
