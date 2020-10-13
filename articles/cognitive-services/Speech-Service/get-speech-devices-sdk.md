---
title: 음성 디바이스 SDK 가져오기
titleSuffix: Azure Cognitive Services
description: 음성 서비스는 다양 한 장치 및 오디오 소스에서 작동 합니다. 이제 일치하는 하드웨어 및 소프트웨어를 사용하여 음성 애플리케이션을 한 단계 업그레이드할 수 있습니다. 이 문서에서는 음성 장치 SDK에 액세스 하 여 개발을 시작 하는 방법을 알아봅니다.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/14/2019
ms.author: erhopf
ms.openlocfilehash: 0bc1a7b5e443de0c1a95fa209d2e5a280cf28ef2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87385843"
---
# <a name="get-the-cognitive-services-speech-devices-sdk"></a>Cognitive Services Speech Devices SDK 가져오기

음성 장치 SDK는 목적에 따라 제작 된 개발 키트와 다양 한 마이크 배열 구성에서 작동 하도록 설계 된 pretuned 라이브러리입니다.

## <a name="choose-a-development-kit"></a>개발 키트 선택

|디바이스|사양|Description|시나리오|
|--|--|--|--|
|[Urbetter Dev Kit](http://www.urbetter.com/products_56/278.html) ![ URbetter DDK](media/speech-devices-sdk/device-urbetter.jpg)|7 Mic 배열, ARM SOC, WIFI, 이더넷, HDMI, USB 카메라. <br>Linux|Microsoft Mic 배열을 적응 하 고 HDMI/이더넷 및 기타 USB 주변 기기와 같은 확장 i/o를 지 원하는 업계 수준 음성 장치 SDK <br> [Urbetter에 문의](http://www.urbetter.com/products_56/278.html)|대화 내용, 교육, 병원, 로봇, 고 대 상자, 음성 에이전트, 드라이브 통과|
|[Roobo 스마트 오디오 개발자 키트](http://ddk.roobo.com)<br>[설치](speech-devices-sdk-roobo-v1.md)  /  [빠른](speech-devices-sdk-android-quickstart.md) ![ 시작 Roobo 스마트 오디오 개발자 키트](media/speech-devices-sdk/device-roobo-v1.jpg)|7 Mic 배열, ARM SOC, WIFI, 오디오 출력, IO. <br>[Android](speech-devices-sdk-android-quickstart.md)|고품질의 기록 및 음성 시나리오를 개발 하기 위한 Microsoft Mic 배열 및 프론트 프로세싱 SDK를 적용 하는 첫 번째 음성 장치 SDK|대화 기록, 스마트 스피커, 음성 에이전트, Wearable|
|[Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/)<br>[설치](https://docs.microsoft.com/azure/Kinect-dk/set-up-azure-kinect-dk)  /  [빠른](speech-devices-sdk-windows-quickstart.md) ![ 시작 Azure Kinect 진한](media/speech-devices-sdk/device-azure-kinect-dk.jpg)|7 Mic 배열 RGB 및 깊이 카메라. <br>[Windows](speech-devices-sdk-windows-quickstart.md) / [Linux](speech-devices-sdk-linux-quickstart.md)|고급 AI (인공 지능) 센서를 사용 하 여 정교한 컴퓨터 비전과 음성 모델을 빌드하기 위한 개발자 키트입니다. 이 도구는 다양 한 계산 유형을 수용 하기 위해 여러 모드, 옵션 및 Sdk를 갖춘 하나의 소형 장치에서 최고의 공간 마이크 배열 및 깊이 카메라를 비디오 카메라 및 방향 센서와 결합 합니다.|대화 내용, 로봇 공학, 스마트 빌드|
|Roobo 스마트 오디오 개발자 키트 2<br>[설치](speech-devices-sdk-roobo-v2.md)<br>![Roobo 스마트 오디오 개발자 키트 2](media/speech-devices-sdk/device-roobo-v2.jpg)|7 Mic 배열, ARM SOC, WIFI, Bluetooth, IO. <br>Linux|두 번째 차세대 음성 장치 SDK는 비용 효율적인 참조 디자인에서 대체 OS와 더 많은 기능을 제공 합니다.|대화 기록, 스마트 스피커, 음성 에이전트, Wearable|


## <a name="download-the-speech-devices-sdk"></a>Speech Devices SDK 다운로드

[음성 장치 SDK](https://aka.ms/sdsdk-download)를 다운로드 합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Speech Devices SDK 시작](https://aka.ms/sdsdk-quickstart)
