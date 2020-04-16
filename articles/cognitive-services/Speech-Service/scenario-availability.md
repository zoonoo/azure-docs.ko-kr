---
title: 시나리오 가용성 - 음성 서비스
titleSuffix: Azure Cognitive Services
description: Speech SDK는 다양한 프로그래밍 언어 및 환경에 걸쳐 다양한 시나리오를 제공합니다. 모든 시나리오가 아직 모든 프로그래밍 언어 또는 모든 환경에서 사용할 수 있는 것은 아닙니다. 다음은 각 시나리오의 가용성입니다.
services: cognitive-services
author: chrisbasoglu
manager: xdh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: cbasoglu
ms.openlocfilehash: a72e5055a3b0f858a732ec28eeab511b09dd450c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400154"
---
# <a name="scenario-availability"></a>시나리오 가용성

Speech SDK는 다양한 프로그래밍 언어 및 환경에 걸쳐 다양한 시나리오를 제공합니다. 모든 시나리오가 아직 모든 프로그래밍 언어 또는 모든 환경에서 사용할 수 있는 것은 아닙니다. 다음은 각 시나리오의 가용성입니다.

- **음성 인식(SR), 구 목록, 의도, 번역 및 온-프레미스 컨테이너**
  - C ++/윈도우 & 리눅스 & 맥 OS
  - C # (프레임 & .NET 코어)/윈도우 UWP & 유니티 & 자마린 & 리눅스 & 맥OS를 &
  - 자바 (제레와 안드로이드)
  - 자바 스크립트 (브라우어와 노드 JS)
  - Python
  - Swift
  - Objective-C  
- **텍스트 음성 변환(TTS)**
  - C ++/윈도우 & 리눅스
  - UWP & 유니티를 & C#/윈도우
  - 자바 (제레와 안드로이드)
  - Python
  - Swift
  - Objective-C
  - TTS REST API는 다른 모든 상황에서 사용할 수 있습니다.
- **키워드 스포팅(KWS)**
  - C ++/윈도우 & 리눅스
  - C #/ 윈도우 & 리눅스
  - 파이썬 / 윈도우 & 리눅스
  - 자바 / 윈도우 & 리눅스 & 안드로이드 (음성 장치 SDK)
  - 키워드 스포팅(KWS) 기능은 모든 마이크 유형에서 작동할 수 있지만 공식 KWS 지원은 현재 Azure Kinect DK 하드웨어 또는 음성 장치 SDK에 있는 마이크 어레이로 제한됩니다.
- **음성 도우미**
  - C ++/윈도우 & 리눅스 & 맥 OS
  - C#/Windows
  - 자바 / 윈도우 & 리눅스 & 맥OS & 안드로이드 (음성 장치 SDK)
- **대화 전사**
  - C ++/윈도우 & 리눅스
  - C # (프레임 & .NET 코어)/윈도우 & UWP & 리눅스
  - 자바 / 윈도우 & 리눅스 & 안드로이드 (음성 장치 SDK)
- **다중 디바이스 대화**
  - C ++/윈도우
  - C # (프레임 워크 & .NET 코어)/윈도우
- **콜 센터 전사**
  - REST API 및 모든 상황에서 사용할 수 있습니다
- **코덱 압축 오디오 입력**
  - C ++/리눅스
  - C #/리눅스
  - 자바 / 리눅스, 안드로이드, 아이폰 OS
