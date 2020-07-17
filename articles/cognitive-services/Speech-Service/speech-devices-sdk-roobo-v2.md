---
title: 음성 장치 SDK Roobo 스마트 오디오 개발자 키트 v2-Speech Service
titleSuffix: Azure Cognitive Services
description: 음성 장치 SDK, Roobo Smart Audio Dev Kit v2를 시작 하기 위한 필수 구성 요소 및 지침입니다.
services: cognitive-services
author: anushapatnala
manager: wellsi
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: v-anusp
ms.openlocfilehash: 2f325c80877068c53d690bd7ff74f768dab3a174
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2020
ms.locfileid: "80371589"
---
# <a name="device-roobo-smart-audio-dev-kit-v2"></a>장치: Roobo Smart Audio Dev Kit v2

이 문서에서는 Roobo Smart Audio Dev Kit2에 대 한 장치 관련 정보를 제공 합니다.

## <a name="set-up-the-development-kit"></a>개발 키트 설정

1. 개발 키트에는 마이크로 USB 커넥터 2개가 있습니다. 왼쪽 커넥터는 개발 키트에 전원을 공급하기 위한 것이며 아래 이미지에서 전원으로 강조 표시됩니다. 오른쪽 커넥터는 개발 키트를 제어하기 위한 것이며 해당 이미지에 디버그로 표시됩니다. 
    ![개발 키트 연결](media/speech-devices-sdk/roobo-v2-connections.png)
1. 마이크로 USB 케이블을 사용해 전원 포트를 PC 또는 전원 어댑에 연결하여 개발 키트에 전원을 공급합니다. 상단 보드 아래에서 녹색 전원 표시기가 켜집니다.
1. 개발 키트를 제어 하려면 두 번째 마이크로 USB 케이블을 사용 하 여 디버그 포트를 컴퓨터에 연결 합니다. 안정적인 통신을 위해서는 고품질 케이블을 사용 하는 것이 중요 합니다.
1. 위에 표시 된 것 처럼 순환적으로 자신를 향한 마이크를 사용 하 여 개발 키트를 수직으로 지향


## <a name="development-information"></a>개발 정보

개발에 대 한 자세한 내용은 [Roobo 개발 가이드](http://dwn.roo.bo/server_upload/ddk/ROOBO%20Dev%20Kit-User%20Guide.pdf)를 참조 하세요.

## <a name="audio-recordplay"></a>오디오 레코드/재생

DDK2 오디오 작업은 다음과 같은 방법으로 수행할 수 있습니다.
* ALSA 오픈 소스 라이브러리 및 해당 응용 프로그램을 사용 합니다.
* Appmainprog 인터페이스를 사용 하 여 응용 프로그램 개발을 수행 합니다. DDK2 audio 관련 소프트웨어 프레임 워크는 표준 ALSA 프레임 워크를 사용 하며 libasound를 사용할 수 있습니다. 따라서 소프트웨어를 직접 개발할 수 있습니다. 따라서 ALSA arecord 및 arecord를 직접 사용 하 여 오디오를 기록 하 고 재생할 수 있습니다.
