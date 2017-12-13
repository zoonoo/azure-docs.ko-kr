---
title: "Azure Media Services에 대한 인증 토큰 전달 | Microsoft Docs"
description: "클라이언트에서 Azure Media Services 키 배달 서비스로 인증 토큰을 보내는 방법을 알아봅니다."
services: media-services
keywords: "콘텐츠 보호, DRM, 토큰 인증"
documentationcenter: 
author: dbgeorge
manager: jasonsue
editor: 
ms.assetid: 7c3b35d9-1269-4c83-8c91-490ae65b0817
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2017
ms.author: dwgeo
ms.openlocfilehash: 0e56726266898e5738dd797a8a019987d457170e
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/07/2017
---
# <a name="how-clients-pass-tokens-to-azure-media-services-key-delivery-service"></a>클라이언트가 Azure Media Services 키 배달 서비스로 토큰을 전달하는 방법
플레이어가 토큰을 키 배달 서비스에 전달하여 확인되고 플레이어가 키를 얻는 방법에 대한 질문을 늘 받고 있습니다. 이러한 두 토큰 형식인 간단한 웹 토큰(SWT) 및 JSON 웹 토큰(JWT)을 지원합니다. 토큰 인증을 시스템에서 일반적인 암호화 또는 AES 봉투 암호화를 수행하든 상관없이 모든 키 형식에 적용할 수 있습니다.

플레이어를 사용하여 토큰을 전달할 수 있는 방법은 다음과 같으며, 대상으로 하는 플레이어 및 플랫폼에 달려 있습니다.
- HTTP 권한 부여 헤더를 통함.
> [!NOTE]
> OAuth 2.0 사양당 "Bearer" 접두사가 예상됩니다. Azure Media Player [데모 페이지](http://ampdemo.azureedge.net/)에 호스트된 토큰 구성을 사용하는 샘플 플레이어가 있습니다. 동영상 원본을 설정하려면 AES(JWT 토큰) 또는 AES(SWT 토큰)를 선택하십시오. 토큰은 권한 부여 헤더를 통해 전달됩니다.

- “token=tokenvalue”로 Url 쿼리 매개 변수 추가를 통해.  
> [!NOTE]
> "Bearer" 접두사는 사용할 수 없습니다. 토큰은 URL을 통해 보내지므로 토큰 문자열을 아머링해야 합니다. 작업을 수행하는 방법에 관한 C# 샘플 코드는 다음과 같습니다.

```csharp
    string armoredAuthToken = System.Web.HttpUtility.UrlEncode(authToken);
    string uriWithTokenParameter = string.Format("{0}&token={1}", keyDeliveryServiceUri.AbsoluteUri, armoredAuthToken);
    Uri keyDeliveryUrlWithTokenParameter = new Uri(uriWithTokenParameter);
```

- CustomData 필드를 통해.
PlayReady 라이선스 획득만인 경우 PlayReady License Acquisition Challenge의 CustomData 필드를 통해. 이 경우 토큰은 아래에 설명된 xml 문서 내에 있어야 합니다.

```xml
    <?xml version="1.0"?>
    <CustomData xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyCustomData/v1"> 
        <Token></Token> 
    </CustomData>
```
인증 토큰을 <Token> 요소에 넣습니다.

- 대체 HLS 재생 목록을 통해. iOS/Safari에서 HLS + AES 재생에 대한 토큰 인증을 구성해야 할 경우 토큰을 직접 보낼 수 있는 방법은 없습니다. 이 시나리오를 사용하도록 재생 목록을 대체하는 방법에 대해 이 [블로그 게시물](http://azure.microsoft.com/blog/2015/03/06/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/)을 참조하세요.

## <a name="next-steps"></a>다음 단계
Media Services 학습 경로를 검토합니다.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]