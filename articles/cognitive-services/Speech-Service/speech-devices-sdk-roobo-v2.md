---
title: 음성 장치 SDK 루보 스마트 오디오 개발자 키트 v2 - 음성 서비스
titleSuffix: Azure Cognitive Services
description: 음성 장치 SDK, Roobo 스마트 오디오 개발자 키트 v2를 시작하기위한 전제 조건 및 지침.
services: cognitive-services
author: anushapatnala
manager: wellsi
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: v-anusp
ms.openlocfilehash: 2f325c80877068c53d690bd7ff74f768dab3a174
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80371589"
---
# <a name="device-roobo-smart-audio-dev-kit-v2"></a>장치: 루보 스마트 오디오 개발자 키트 v2

이 문서에서는 Roobo 스마트 오디오 개발자 키트2에 대한 장치별 정보를 제공합니다.

## <a name="set-up-the-development-kit"></a>개발 키트 설정

1. 개발 키트에는 마이크로 USB 커넥터 2개가 있습니다. 왼쪽 커넥터는 개발 키트에 전원을 공급하기 위한 것이며 아래 이미지에서 전원으로 강조 표시됩니다. 오른쪽 커넥터는 개발 키트를 제어하기 위한 것이며 해당 이미지에 디버그로 표시됩니다. 
    ![개발 키트 연결](media/speech-devices-sdk/roobo-v2-connections.png)
1. 마이크로 USB 케이블을 사용해 전원 포트를 PC 또는 전원 어댑에 연결하여 개발 키트에 전원을 공급합니다. 상단 보드 아래에서 녹색 전원 표시기가 켜집니다.
1. 개발 키트를 제어하려면 두 번째 마이크로 USB 케이블을 사용하여 디버그 포트를 컴퓨터에 연결합니다. 안정적인 통신을 보장하기 위해 고품질 케이블을 사용하는 것이 필수적입니다.
1. 개발 키트를 원형으로 지향 - 위와 같이 마이크가 천장을 향하여 똑바로 세워서


## <a name="development-information"></a>개발 정보

자세한 개발 정보는 [Roobo 개발 가이드를](http://dwn.roo.bo/server_upload/ddk/ROOBO%20Dev%20Kit-User%20Guide.pdf)참조하십시오.

## <a name="audio-recordplay"></a>오디오 레코드/재생

DDK2 오디오 작업은 다음과 같은 방법으로 수행할 수 있습니다.
* ALSA 오픈 소스 라이브러리 및 해당 응용 프로그램을 사용합니다.
* appmainprog 인터페이스를 사용하여 응용 프로그램 개발을 수행합니다. DDK2 오디오 - 관련 소프트웨어 프레임 워크는 표준 ALSA 프레임 워크를 사용, 당신은 libasound를 사용할 수 있습니다. 그래서 직접 소프트웨어를 개발합니다. 따라서 ALSA의 arecord 및 aplay를 사용하여 오디오를 직접 녹음하고 재생할 수 있습니다.
