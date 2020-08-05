---
title: Azure Media Player 보호 콘텐츠
description: Azure Media Player는 현재 AES-128비트 봉투 암호화 콘텐츠와 일반 암호화 콘텐츠를 지원합니다.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.custom: devx-track-javascript
ms.openlocfilehash: da79556b466e3511845724e969c76477ad2ba0a8
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87423017"
---
# <a name="protected-content"></a>보호된 콘텐츠 #

Azure Media Player는 현재 AES-128비트 봉투 암호화 콘텐츠 및 일반 암호화 콘텐츠(PlayReady 및 Widevine을 통해) 또는 FairPlay를 통해 암호화된 콘텐츠를 지원합니다. 보호된 콘텐츠를 올바르게 재생하려면 Azure Media Player에 `protectionInfo`를 알려야 합니다. 이 정보는 원본마다 존재하며 `data-setup`을 통해 `<source>` 태그에 직접 추가할 수 있습니다.  원본을 동적으로 설정하는 경우 `protectionInfo`를 매개 변수로 직접 추가할 수도 있습니다.

`protectionInfo`는 JSON 개체를 수락하며 다음을 포함합니다.

- `type`: `AES`, `PlayReady`, `Widevine` 또는 `FairPlay`
- `certificateUrl`: 호스팅된 FairPlay 인증서에 직접 연결되어야 합니다.

- `authenticationToken`: 인코딩되지 않은 인증 토큰을 추가하는 옵션 필드입니다.

> [!IMPORTANT]
> **certificateUrl** 개체는 FairPlay DRM에만 필요합니다.***
>[!NOTE]
> 기본 techOrder는 새로운 기술 `html5FairPlayHLS`를 지원하도록 변경되었으며, 특히 이를 지원하는 브라우저(OSX 8+의 Safari)에서 기본적으로 FairPlay 콘텐츠를 재생합니다. 재생할 FairPlay 콘텐츠가 **있고** 기본 techOrder를 애플리케이션의 사용자 지정 항목으로 변경한 경우 이 새 기술을 techOrder 개체에 추가해야 합니다. 콘텐츠가 PlayReady를 통해 재생되지 않도록 silverlightSS 앞에 포함하는 것이 좋습니다.

## <a name="code-sample"></a>코드 샘플 ##

```html
Ex:

    <video id="vid1" class="azuremediaplayer amp-default-skin">
        <source
            src="//example/path/to/myVideo.ism/manifest"
            type="application/vnd.ms-sstr+xml"
            data-setup='{"protectionInfo": [{"type": "AES", "authenticationToken": "Bearer=urn%3amicrosoft%3aazure%3amediaservices%3acontentkeyidentifier=8130520b-c116-45a9-824e-4a0082f3cb3c&Audience=urn%3atest&ExpiresOn=1450207516&Issuer=http%3a%2f%2ftestacs.com%2f&HMACSHA256=eV7HDgZ9msp9H9bnEPGN91sBdU7XsZ9OyB6VgFhKBAU%3d"}]}'
        />
    </video>
or

```javascript
    var myPlayer = amp("vid1", /* Options */);
    myPlayer.src([{
        src: "//example/path/to/myVideo.ism/manifest",
        type: "application/vnd.ms-sstr+xml",
        protectionInfo: [{
            type: "PlayReady",
            authenticationToken: "Bearer=urn%3amicrosoft%3aazure%3amediaservices%3acontentkeyidentifier=d5646e95-63ee-4fbe-ba4e-295c8d9502e0&Audience=urn%3atest&ExpiresOn=1450222961&Issuer=http%3a%2f%2ftestacs.com%2f&HMACSHA256=4Jop3kNJdzVI8L5IZLgFtPdImyE%2fHTRil0x%2bEikSdPs%3d"
        }] }, ]
    );
```

또는 다중 DRM 포함

```javascript
    var myPlayer = amp("vid1", /* Options */);
    myPlayer.src([{
        src: "//example/path/to/myVideo.ism/manifest",
        type: "application/vnd.ms-sstr+xml",
        protectionInfo: [{
                type: "PlayReady",
                authenticationToken: "Bearer=urn%3amicrosoft%3aazure%3amediaservices%3acontentkeyidentifier=d5646e95-63ee-4fbe-ba4e-295c8d9502e0&Audience=urn%3atest&ExpiresOn=1450222961&Issuer=http%3a%2f%2ftestacs.com%2f&HMACSHA256=4Jop3kNJdzVI8L5IZLgFtPdImyE%2fHTRil0x%2bEikSdPs%3d"
            },
            {
                type: "Widevine",
                authenticationToken: "Bearer=urn%3amicrosoft%3aazure%3amediaservices%3acontentkeyidentifier=d5646e95-63ee-4fbe-ba4e-295c8d9502e0&Audience=urn%3atest&ExpiresOn=1450222961&Issuer=http%3a%2f%2ftestacs.com%2f&HMACSHA256=4Jop3kNJdzVI8L5IZLgFtPdImyE%2fHTRil0x%2bEikSdPs%3d"
            },
            {
                   type: "FairPlay",
                  certificateUrl: "//example/path/to/myFairplay.der",
                   authenticationToken: "Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ1cm46bWljcm9zb2Z0OmF6dXJlOm1lZGlhc2VydmljZXM6Y29udGVudGtleWlkZW50aWZpZXIiOiIyMTI0M2Q2OC00Yjc4LTRlNzUtYTU5MS1jZWMzMDI0NDNhYWMiLCJpc3MiOiJodHRwOi8vY29udG9zbyIsImF1ZCI6InVybjp0ZXN0IiwiZXhwIjoxNDc0NTkyNDYzLCJuYmYiOjE0NzQ1ODg1NjN9.mE7UxgNhkieMMqtM_IiYQj-FK1KKIzB6lAptw4Mi67A"
        }] } ]
    );
```

> [!NOTE]
> 모든 브라우저/플랫폼이 보호된 콘텐츠를 재생할 수 있는 것은 아닙니다. 지원되는 항목에 대한 자세한 내용은 [재생 기술](azure-media-player-playback-technology.md) 섹션을 참조하세요.
> [!IMPORTANT]
> 플레이어에 전달된 토큰은 보안 콘텐츠를 위한 것이며 인증된 사용자 에게만 사용됩니다. 애플리케이션이 SSL 또는 다른 형태의 보안 측정을 사용한다고 가정합니다. 또한 최종 사용자는 토큰을 오용하지 않을 것으로 신뢰됩니다. 그렇지 않은 경우 보안 전문가에게 문의하세요.

## <a name="next-steps"></a>다음 단계 ##

- [Azure Media Player 빠른 시작](azure-media-player-quickstart.md)