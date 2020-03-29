---
title: 음성 디바이스 SDK 가져오기
titleSuffix: Azure Cognitive Services
description: 음성 서비스는 다양한 장치 및 오디오 소스와 함께 작동합니다. 이제 일치하는 하드웨어 및 소프트웨어를 사용하여 음성 애플리케이션을 한 단계 업그레이드할 수 있습니다. 이 문서에서는 음성 장치 SDK에 액세스하고 개발을 시작하는 방법을 배웁니다.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: f8c1500dbbd9135a850e145199de8fea68cc4630
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79221428"
---
# <a name="get-the-cognitive-services-speech-devices-sdk"></a>Cognitive Services Speech Devices SDK 가져오기

음성 장치 SDK는 특수 제작된 개발 키트 및 다양한 마이크 어레이 구성과 함께 작동하도록 설계된 미리 조정된 라이브러리입니다.

## <a name="choose-a-development-kit"></a>개발 키트 선택

|디바이스|사양|설명|시나리오|
|--|--|--|--|
|[루보 스마트 오디오 개발자 키트](https://ddk.roobo.com)<br>[설치](speech-devices-sdk-roobo-v1.md)[퀵스타트](speech-devices-sdk-android-quickstart.md)![루보 스마트 오디오 개발 키트 / ](media/speech-devices-sdk/device-roobo-v1.jpg)|7 마이크 어레이, ARM SOC, 와이파이, 오디오 아웃, IO. <br>[Android](speech-devices-sdk-android-quickstart.md)|고품질 전사 및 음성 시나리오를 개발하기 위한 Microsoft 마이크 어레이 및 전면 처리 SDK를 적용하는 최초의 음성 장치 SDK|대화 전사, 스마트 스피커, 음성 에이전트, 웨어러블|
|[Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/)<br>[설치](https://docs.microsoft.com/azure/Kinect-dk/set-up-azure-kinect-dk)[퀵스타트](speech-devices-sdk-windows-quickstart.md)![Azure Kinect DK / ](media/speech-devices-sdk/device-azure-kinect-dk.jpg)|7 마이크 어레이 RGB 및 깊이 카메라. <br>[윈도우](speech-devices-sdk-windows-quickstart.md)/[리눅스](speech-devices-sdk-linux-quickstart.md)|정교한 컴퓨터 비전 및 음성 모델을 구축하기 위한 고급 인공 지능(AI) 센서가 있는 개발자 키트입니다. 동급 최강의 공간 마이크 어레이와 깊이 카메라를 비디오 카메라 및 방향 센서와 결합하여 다양한 모드, 옵션 및 SDK를 갖춘 하나의 소형 장치에 모두 결합하여 다양한 컴퓨팅 유형을 수용할 수 있습니다.|대화 전사, 로봇 공학, 스마트 빌딩|
|루보 스마트 오디오 개발자 키트 2<br>[설치](speech-devices-sdk-roobo-v2.md)<br>![루보 스마트 오디오 개발자 키트 2](media/speech-devices-sdk/device-roobo-v2.jpg)|7 마이크 어레이, ARM SOC, 와이파이, 블루투스, IO. <br>Linux|비용 효율적인 참조 설계에서 대체 OS 및 더 많은 기능을 제공하는 2세대 음성 장치 SDK.|대화 전사, 스마트 스피커, 음성 에이전트, 웨어러블|
|URbetter T11 개발 위원회![URbetter DDK](media/speech-devices-sdk/device-urbetter.jpg)|7 마이크 어레이, ARM SOC, 와이파이, 이더넷, HDMI, USB 카메라. <br>Linux|Microsoft 마이크 어레이를 조정하고 HDMI/이더넷 및 기타 USB 주변 장치와 같은 확장된 I/O를 지원하는 업계 수준의 음성 장치 SDK|대화 전사, 교육, 병원, 로봇, OTT 상자, 음성 에이전트, 드라이브 스루|

## <a name="download-the-speech-devices-sdk"></a>Speech Devices SDK 다운로드

음성 [장치 SDK를 다운로드합니다.](https://aka.ms/sdsdk-download)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Speech Devices SDK 시작](https://aka.ms/sdsdk-quickstart)
