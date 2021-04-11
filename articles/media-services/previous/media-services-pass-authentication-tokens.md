---
title: Azure Media Services에 대한 인증 토큰 전달 | Microsoft Docs
description: 클라이언트에서 Azure Media Services 키 배달 서비스로 인증 토큰을 보내는 방법을 알아봅니다.
services: media-services
keywords: 콘텐츠 보호, DRM, 토큰 인증
author: IngridAtMicrosoft
manager: femila
ms.assetid: 7c3b35d9-1269-4c83-8c91-490ae65b0817
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 1fe692e1eb20956f339c9b861f50163cee9c5063
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105564656"
---
# <a name="learn-how-clients-pass-tokens-to-the-azure-media-services-key-delivery-service"></a>클라이언트가 Azure Media Services 키 배달 서비스로 토큰을 전달하는 방법 알아보기

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

고객은 플레이어가 키를 얻을 수 있도록 인증을 위해 Azure Media Services 키 배달 서비스로 토큰을 전달하는 방법에 대해 자주 질문합니다. Media Services는 SWT(단순 웹 토큰) 및 JWT(JSON Web Token) 형식을 지원합니다. 토큰 인증은 시스템에서 일반적인 암호화 또는 AES(Advanced Encryption Standard) 봉투 암호화의 수행 여부와 상관없이 모든 키 형식에 적용할 수 있습니다.

 대상으로 하는 플레이어 및 플랫폼에 따라 다음 방법으로 플레이어를 사용하여 토큰을 전달할 수 있습니다.

- HTTP 권한 부여 헤더를 통함.
    > [!NOTE]
    > OAuth 2.0 사양마다 “Bearer” 접두사가 필요합니다. 동영상 원본을 설정하려면 **AES(JWT 토큰)** 또는 **AES(SWT 토큰)** 를 선택합니다. 토큰은 인증 헤더를 통해 전달됩니다.

- “token=tokenvalue”로 URL 쿼리 매개 변수 추가를 통해,  
    > [!NOTE]
    > “Bearer” 접두사가 필요하지 않습니다. 토큰은 URL을 통해 보내지므로 토큰 문자열을 아머링해야 합니다. 작업을 수행하는 방법에 관한 C# 샘플 코드는 다음과 같습니다.

    ```csharp
    string armoredAuthToken = System.Web.HttpUtility.UrlEncode(authToken);
    string uriWithTokenParameter = string.Format("{0}&token={1}", keyDeliveryServiceUri.AbsoluteUri, armoredAuthToken);
    Uri keyDeliveryUrlWithTokenParameter = new Uri(uriWithTokenParameter);
    ```

- CustomData 필드를 통해.
이 옵션은 PlayReady 라이선스 획득에서만 PlayReady License Acquisition Challenge의 CustomData 필드를 통해 사용됩니다. 이 경우 토큰은 아래에 설명된 XML 문서 내에 있어야 합니다.

    ```xml
    <?xml version="1.0"?>
    <CustomData xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyCustomData/v1"> 
        <Token></Token> 
    </CustomData>
    ```
    인증 토큰을 토큰 요소에 넣습니다.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]
