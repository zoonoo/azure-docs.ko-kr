---
title: Media Encoder Standard를 사용하여 오버레이를 만드는 방법
description: Media Encoder Standard를 사용하여 오버레이를 만드는 방법에 대해 알아봅니다.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 08/31/2020
ms.openlocfilehash: 6d68f35cd7eff745a3d17009b65b1ed73a190173
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106490253"
---
# <a name="how-to-create-an-overlay-with-media-encoder-standard"></a>Media Encoder Standard를 사용하여 오버레이를 만드는 방법

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Media Encoder Standard를 사용하여 이미지, 오디오 파일 또는 다른 비디오를 다른 비디오에 오버레이 할 수 있습니다. 입력은 파일을 하나만 지정해야 합니다. 이미지 파일은 JPG, PNG, GIF 또는 BMP 형식 또는 오디오 파일(예: WAV, MP3, WMA, M4A 파일) 또는 비디오 파일에 지정할 수 있습니다.


## <a name="prerequisites"></a>필수 구성 요소

* 샘플에서 *appsettings.json* 파일을 구성하는 데 필요한 계정 정보를 수집합니다. 이 작업을 수행하는 방법을 잘 모르겠다면 [빠른 시작: Microsoft ID 플랫폼을 사용하여 애플리케이션 등록](../../active-directory/develop/quickstart-register-app.md)을 참조하세요. *appsettings.json* 파일에는 다음 값이 필요합니다.

    ```json
    {
    "AadClientId": "",
    "AadEndpoint": "https://login.microsoftonline.com",
    "AadSecret": "",
    "AadTenantId": "",
    "AccountName": "",
    "ArmAadAudience": "https://management.core.windows.net/",
    "ArmEndpoint": "https://management.azure.com/",
    "Location": "",
    "ResourceGroup": "",
    "SubscriptionId": ""
    }
    ```

변환에 익숙하지 않은 경우 다음 작업을 완료하는 것이 좋습니다.

* [Media Services를 사용하여 비디오 및 오디오 인코딩](encode-concept.md) 읽기
* [사용자 지정 변환을 사용하여 인코딩하는 방법 - .NET](transform-custom-presets-how-to.md)을 읽어보세요. 해당 문서의 단계에 따라 변환 작업에 필요한 .NET을 설정하고 여기로 돌아와 오버레이 미리 설정 샘플을 사용해 보세요.
* [변환 참조 문서](/rest/api/media/transforms)를 참조하세요.

변환에 익숙한 경우 오버레이 샘플을 다운로드합니다.

## <a name="overlays-preset-sample"></a>오버레이 미리 설정 샘플

오버레이를 시작하려면 [미디어 서비스 오버레이 샘플](https://github.com/Azure-Samples/media-services-overlays)을 다운로드합니다.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [transforms next steps](./includes/transforms-next-steps.md)]
