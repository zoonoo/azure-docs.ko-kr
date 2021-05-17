---
title: Media Services v3에 인증 토큰 전달 | Microsoft Docs
description: 클라이언트에서 Media Services v3 키 전달 서비스로 인증 토큰을 보내는 방법을 알아봅니다.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: how-to
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 590309ad392876ba3c5cb6d21abe777ab5a93efb
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106281621"
---
# <a name="pass-tokens-to-the-azure-media-services-v3-key-delivery-service"></a>Azure Media Services v3 키 전달 서비스에 토큰 전달

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

고객은 플레이어가 키를 얻을 수 있도록 인증을 위해 Azure Media Services 키 배달 서비스로 토큰을 전달하는 방법에 대해 자주 질문합니다. Media Services는 SWT(단순 웹 토큰) 및 JWT(JSON Web Token) 형식을 지원합니다. 토큰 인증은 시스템에서 일반적인 암호화 또는 AES(Advanced Encryption Standard) 봉투 암호화의 수행 여부와 상관없이 모든 키 형식에 적용할 수 있습니다.

 대상으로 하는 플레이어 및 플랫폼에 따라 다음 방법으로 플레이어를 사용하여 토큰을 전달할 수 있습니다.

## <a name="pass-a-token-through-the-http-authorization-header"></a>HTTP 권한 부여 헤더를 통해 토큰 전달

> [!NOTE]
> OAuth 2.0 사양마다 “Bearer” 접두사가 필요합니다. 동영상 원본을 설정하려면 **AES(JWT 토큰)** 또는 **AES(SWT 토큰)** 를 선택합니다. 토큰은 인증 헤더를 통해 전달됩니다.

## <a name="pass-a-token-via-the-addition-of-a-url-query-parameter-with-tokentokenvalue"></a>“token=tokenvalue”가 포함된 URL 쿼리 매개 변수를 추가하여 토큰을 전달합니다.

> [!NOTE]
> “Bearer” 접두사가 필요하지 않습니다. 토큰은 URL을 통해 보내지므로 토큰 문자열을 아머링해야 합니다. 작업을 수행하는 방법에 관한 C# 샘플 코드는 다음과 같습니다.

```csharp
    string armoredAuthToken = System.Web.HttpUtility.UrlEncode(authToken);
    string uriWithTokenParameter = string.Format("{0}&token={1}", keyDeliveryServiceUri.AbsoluteUri, armoredAuthToken);
    Uri keyDeliveryUrlWithTokenParameter = new Uri(uriWithTokenParameter);
```

## <a name="pass-a-token-through-the-customdata-field"></a>CustomData 필드를 통해 토큰 전달

이 옵션은 PlayReady 라이선스 획득에서만 PlayReady License Acquisition Challenge의 CustomData 필드를 통해 사용됩니다. 이 경우 토큰은 아래에 설명된 XML 문서 내에 있어야 합니다.

```xml
    <?xml version="1.0"?>
    <CustomData xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyCustomData/v1"> 
        <Token></Token> 
    </CustomData>
```

인증 토큰을 토큰 요소에 넣습니다.
