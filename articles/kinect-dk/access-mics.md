---
title: Azure Kinect DK 마이크 입력 데이터 액세스
description: Azure Kinect DK 마이크 배열을 사용하여 마이크 데이터를 얻는 방법을 이해합니다.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect, azure, 센서, sdk, 마이크, 마이크 액세스, 마이크 데이터
ms.openlocfilehash: 76edb50c7ac07e743fa015ed503221143fdfb7d9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "94359632"
---
# <a name="access-azure-kinect-dk-microphone-input-data"></a>Azure Kinect DK 마이크 입력 데이터 액세스

[Speech SDK 빠른 시작](../cognitive-services/speech-service/index.yml)은 다양한 프로그래밍 언어에서 Azure Kinect DK 마이크 배열을 사용하는 방법에 대한 예를 제공합니다.
예를 들어 **Speech SDK를 사용하여 Windows의 C++에서 음성 인식** 빠른 시작을 참조하세요. 코드는 [GitHub에서](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp) 사용 가능합니다.

Windows API를 통해서도 마이크 배열에 액세스합니다. Windows 설명서에 대한 세부 정보는 다음 문서를 참조하세요.

* [Windows 오디오 아키텍처](/windows-hardware/drivers/audio/windows-audio-architecture)
* [Windows.Media.Capture 문서](/uwp/api/Windows.Media.Capture)
* [웹캠 캡처에 대한 자습서](/windows/uwp/audio-video-camera/basic-photo-video-and-audio-capture-with-mediacapture)
* [USB 오디오 정보](/windows-hardware/drivers/audio/usb-2-0-audio-drivers)

또한 [마이크 배열 하드웨어 사양](hardware-specification.md#microphone-array)을 검토할 수 있습니다.

## <a name="next-steps"></a>다음 단계

>[!div class="nextstepaction"]
>[Speech Services SDK](../cognitive-services/speech-service/index.yml)