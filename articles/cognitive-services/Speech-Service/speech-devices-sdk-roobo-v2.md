---
title: Speech Devices SDK Roobo 스마트 오디오 개발 키트 v2 - 음성 서비스
titleSuffix: Azure Cognitive Services
description: Speech Devices SDK Roobo 스마트 오디오 개발 키트 v2를 시작하기 위한 필수 구성 요소 및 지침입니다.
services: cognitive-services
author: anushapatnala
manager: wellsi
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: v-anusp
ms.openlocfilehash: 2f325c80877068c53d690bd7ff74f768dab3a174
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "80371589"
---
# <a name="device-roobo-smart-audio-dev-kit-v2"></a>디바이스: Roobo 스마트 오디오 개발 키트 v2

이 문서에서는 Roobo 스마트 오디오 개발 키트 v2의 디바이스 관련 정보를 제공합니다.

## <a name="set-up-the-development-kit"></a>개발 키트 설정

1. 개발 키트에는 마이크로 USB 커넥터 2개가 있습니다. 왼쪽 커넥터는 개발 키트에 전원을 공급하기 위한 것이며 아래 이미지에서 전원으로 강조 표시됩니다. 오른쪽 커넥터는 개발 키트를 제어하기 위한 것이며 해당 이미지에 디버그로 표시됩니다. 
    ![개발 키트 연결](media/speech-devices-sdk/roobo-v2-connections.png)
1. 마이크로 USB 케이블을 사용해 전원 포트를 PC 또는 전원 어댑에 연결하여 개발 키트에 전원을 공급합니다. 상단 보드 아래에서 녹색 전원 표시기가 켜집니다.
1. 개발 키트를 제어하려면 두 번째 마이크로 USB 케이블을 사용하여 디버그 포트를 컴퓨터에 연결합니다. 신뢰할 수 있는 통신을 확보하기 위해 반드시 고품질 케이블을 사용해야 합니다.
1. 개발 키트 방향을 원형으로 배치합니다(수직, 그림과 같이 마이크가 천장을 향함).


## <a name="development-information"></a>개발 정보

자세한 개발 정보는 [Roobo 개발 가이드](http://dwn.roo.bo/server_upload/ddk/ROOBO%20Dev%20Kit-User%20Guide.pdf)를 참조하세요.

## <a name="audio-recordplay"></a>오디오 녹음/재생

DDK2 오디오 작업은 다음과 같은 방법으로 수행할 수 있습니다.
* ALSA 오픈 소스 라이브러리 및 해당 애플리케이션을 사용합니다.
* Appmainprog 인터페이스를 사용하여 애플리케이션 개발을 수행합니다. DDK2 오디오 관련 소프트웨어 프레임워크는 표준 ALSA 프레임워크를 사용하며, 개발자는 libasound를 사용하여 소프트웨어를 직접 개발할 수 있습니다. 따라서 ALSA의 arecord 및 aplay를 직접 사용하여 오디오를 녹음하고 재생할 수 있습니다.
