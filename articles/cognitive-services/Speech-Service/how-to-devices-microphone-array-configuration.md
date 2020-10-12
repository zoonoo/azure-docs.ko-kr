---
title: 마이크 배열 구성 방법-음성 서비스
titleSuffix: Azure Cognitive Services
description: 음성 장치 SDK에서 사용할 수 있도록 마이크 배열을 구성 하는 방법에 대해 알아봅니다.
services: cognitive-services
author: mswellsi
manager: yanbo
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/01/2020
ms.author: wellsi
ms.openlocfilehash: a2652bed6c8e7dec0a6fe8f9471793c3873646bf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "82781775"
---
# <a name="how-to-configure-a-microphone-array"></a>마이크 배열을 구성하는 방법

이 문서에서는 [마이크 배열을](https://aka.ms/sdsdk-microphone)구성 하는 방법에 대해 알아봅니다. 작업 각도 설정, 음성 장치 SDK에 사용 되는 마이크를 선택 하는 방법 등을 포함 합니다.

음성 장치 SDK는 [지침](https://aka.ms/sdsdk-microphone)에 따라 설계 된 마이크 배열에서 가장 잘 작동 합니다. 마이크 배열 구성은 운영 체제에서 제공 하거나 다음 방법 중 하나를 통해 제공 될 수 있습니다.

음성 장치 SDK는 고정 된 구성 집합에서 선택 하 여 처음에 마이크 배열을 지원 합니다.

```java
private static String DeviceGeometry = "Circular6+1"; // "Circular6+1", "Linear4",
private static String SelectedGeometry = "Circular6+1"; // "Circular6+1", "Circular3+1", "Linear4", "Linear2"
```

Windows에서 마이크 배열 구성은 오디오 드라이버에서 제공 됩니다.

V 1.11.0에서 음성 장치 SDK는 [JSON 파일](https://aka.ms/sdsdk-micarray-json)의 구성도 지원 합니다.


## <a name="windows"></a>Windows
Windows에서 마이크 배열 기 하 도형 정보는 오디오 드라이버에서 자동으로 가져옵니다. 따라서, 및 속성 `DeviceGeometry` 은  `SelectedGeometry` `MicArrayGeometryConfigFile` 선택 사항입니다. 에서 제공 하는 [JSON 파일](https://aka.ms/sdsdk-micarray-json) 을 사용 하 여 `MicArrayGeometryConfigFile` beamforming 범위만 가져옵니다.

를 사용 하 여 마이크 배열을 지정 하는 경우 `AudioConfig::FromMicrophoneInput` 지정 된 마이크를 사용 합니다. 마이크가 지정 되지 않았거나 `AudioConfig::FromDefaultMicrophoneInput` 가 호출 되 면 Windows의 소리 설정에 지정 된 기본 마이크를 사용 합니다.
음성 장치 SDK의 Microsoft 오디오 스택은 16 KHz의 정수 배수인 샘플 요금에 대해서만 다운 샘플링을 지원 합니다.

## <a name="linux"></a>Linux
Linux에서 마이크 기 하 도형 정보를 제공 해야 합니다. 및의 사용 `DeviceGeometry` 은 `SelectedGeometry` 계속 지원 됩니다. 속성을 사용 하 여 JSON 파일을 통해 제공 될 수도 있습니다 `MicArrayGeometryConfigFile` . Windows와 마찬가지로, beamforming 범위는 JSON 파일에서 제공할 수 있습니다.

를 사용 하 여 마이크 배열을 지정 하는 경우 `AudioConfig::FromMicrophoneInput` 지정 된 마이크를 사용 합니다. 마이크가 지정 되지 않았거나 `AudioConfig::FromDefaultMicrophoneInput` 가 호출 된 경우에는 *기본*이라는 alsa 장치에서 기록 합니다. 기본적으로 *기본* 은 항상 카드 0 장치 0을 가리키지만 사용자가 파일에서 변경할 수 있습니다 `asound.conf` . 

음성 장치 SDK의 Microsoft 오디오 스택은 16 KHz의 정수 배수가 되는 샘플 요금에 대 한 다운 샘플링만 지원 합니다. 또한 32 비트 IEEE little endian float, 32 비트 little endian signed int, 24 비트 little endian signed int, 16 비트 little endian signed int 및 8 비트의 부호 있는 int 형식이 지원 됩니다.

## <a name="android"></a>Android
현재는 [Roobo v1](speech-devices-sdk-android-quickstart.md) 만 음성 장치 SDK에서 지원 됩니다. 동작은 이전 릴리스와 동일 `MicArrayGeometryConfigFile` 합니다. 여기서는 현재 속성을 사용 하 여 beamforming 범위가 포함 된 JSON 파일을 지정할 수 있습니다.

## <a name="microphone-array-configuration-json"></a>마이크 배열 구성 JSON

마이크 배열 기 하 도형 구성에 대 한 JSON 파일은 [json 스키마](https://aka.ms/sdsdk-micarray-json)를 따릅니다. 다음은 스키마를 따르는 몇 가지 예입니다.


```json
{
    "micArrayType": "Linear",
    "geometry": "Linear4"
}
```


또는


```json
{
    "micArrayType": "Planar",
    "horizontalAngleBegin": 0,
    "horizontalAngleEnd": 360,
    "numberOfMicrophones": 4,
    "micCoord": [
        {
            "xCoord": 0,
            "yCoord": 0,
            "zCoord": 0
        },
        {
            "xCoord": 40,
            "yCoord": 0,
            "zCoord": 0
        },
        {
            "xCoord": -20,
            "yCoord": -35,
            "zCoord": 0
        },
        {
            "xCoord": -20,
            "yCoord": 35,
            "zCoord": 0
        }
    ]
}
```

또는

```json
{
    "micArrayType": "Linear",
    "horizontalAngleBegin": 70,
    "horizontalAngleEnd": 110
}
```


## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [음성 장치 선택](get-speech-devices-sdk.md)
