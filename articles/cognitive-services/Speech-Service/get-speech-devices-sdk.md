---
title: 음성 디바이스 SDK 가져오기
titleSuffix: Azure Cognitive Services
description: Speech Services는 다양한 디바이스 및 오디오 원본에서 작동합니다. 이제 일치하는 하드웨어 및 소프트웨어를 사용하여 음성 애플리케이션을 한 단계 업그레이드할 수 있습니다. 이 문서에서는 음성 장치 SDK에 대 한 액세스를 가져오고 개발을 시작 하는 방법을 배웁니다.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: b9a0890000cda0b3663ac29bee61fc1c702f6254
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65410700"
---
# <a name="get-the-cognitive-services-speech-devices-sdk"></a>Cognitive Services Speech Devices SDK 가져오기

음성 장치 SDK에 특화 된 개발 키트 및 다양 한 마이크 배열 구성을 사용 하도록 디자인 된 pretuned 라이브러리입니다.

## <a name="choose-a-development-kit"></a>개발 키트를 선택 합니다.

|디바이스|사양|설명|시나리오|
|--|--|--|--|
|[Roobo Smart Audio Dev Kit](https://ddk.roobo.com)</br>[설치 프로그램](speech-devices-sdk-roobo-v1.md) / [퀵 스타트](speech-devices-sdk-android-quickstart.md)![Roobo 스마트 오디오 Dev 키트](media/speech-devices-sdk/device-roobo-v1.jpg)|7 mic 배열에 ARM SOC WIFI, 오디오 출력, IO입니다. </br>Android|Microsoft Mic 배열 및 고품질 기록 및 음성 시나리오를 개발 하기 위한 SDK를 처리 하는 앞에 맞게 첫 번째 음성 장치 SDK|대화 기록, 스마트 발표자 음성 에이전트, 착용 식|
|[Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/)![Azure Kinect DK](media/speech-devices-sdk/device-azure-kinect-dk.jpg)|Mic 배열 RGB 및 깊이 카메라 7입니다. </br>Windows/Linux|복잡 한 컴퓨터 시각 및 음성 모델을 빌드하기 위한 고급 AI (인공 지능) 센서를 사용 하 여 개발자 키트. 비디오 카메라 및 방향 센서를 사용 하 여 최고의에서 공간 마이크 배열 및 깊이 카메라 결합-여러 모드, 옵션 및 Sdk의 범위를 수용 하기 위해 사용 하 여 하나의 작은 장치에서 모든 계산 형식입니다.|대화 기록, 로봇 공학 스마트 빌딩|
|Roobo Smart Audio Dev Kit 2![Roobo Smart Audio Dev Kit 2](media/speech-devices-sdk/device-roobo-v2.jpg)|7 mic 배열에 ARM SOC WIFI, Bluetooth, IO입니다. </br>Linux|두 번째 세대 대체 OS 및 비용 효율적인 참조 디자인에 더 많은 기능을 제공 하는 음성 장치 SDK입니다.|대화 기록, 스마트 발표자 음성 에이전트, 착용 식|
|URbetter T11 개발 보드![URbetter DDK](media/speech-devices-sdk/device-urbetter.jpg)|7 mic 배열에 ARM SOC WIFI, 이더넷, HDMI, USB 카메라입니다. </br>Linux|Microsoft Mic 배열을 조정 하 고 지 원하는 음성 장치 SDK는 업계 수준 확장 HDMI/이더넷 등 자세한 USB 주변 장치 I/O|에이전트, 음성 드라이브부터 대화 기록, 교육, 병원, 로봇, OTT 상자|

## <a name="download-the-speech-devices-sdk"></a>Speech Devices SDK 다운로드

다운로드 합니다 [음성 장치 SDK](https://aka.ms/sdsdk-download)합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Speech Devices SDK 시작](https://aka.ms/sdsdk-quickstart)
