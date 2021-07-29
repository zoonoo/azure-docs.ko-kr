---
title: Azure Media Player 오류 코드
description: Azure Media Player에 대한 오류 코드 참조입니다.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: error-reference
ms.date: 04/05/2021
ms.openlocfilehash: 77bd59782d12d7d73e6e882e929ae9a4c95250a7
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449888"
---
# <a name="error-codes"></a>오류 코드 #

재생을 시작할 수 없거나 재생이 중지된 경우 오류 이벤트가 실행되고 `error()` 함수는 앱 개발자에게 자세한 정보를 제공하는 코드와 선택적 메시지를 반환합니다. `error().message`는 사용자에게 표시되는 메시지가 아닙니다.  사용자에게 표시되는 메시지는 `error().code` 비트 27-20을 기반으로 합니다. 아래 표를 참조하세요.

```javascript

    var myPlayer = amp('vid1');
    myPlayer.addEventListener('error', function() {
        var errorDetails = myPlayer.error();
        var code = errorDetails.code;
        var message = errorDetails.message;
    }
```

## <a name="error-codes-bits-31-28-4-bits"></a>오류 코드, 비트 [31-28](4비트) ##

오류 영역을 설명합니다.

- 0 - 알 수 없음
- 1 - AMP
- 2 - AzureHtml5JS
- 3 - FlashSS
- 4 - SilverlightSS
- 5 - Html5
- 6 - Html5FairPlayHLS

## <a name="error-codes-bits-27-0-28-bits"></a>오류 코드, 비트 [27-0](28비트) ##

오류 세부 정보를 설명합니다. 비트 27-20은 높은 수준을 제공하고 비트 19-0은 추가 세부 정보를 제공합니다(사용 가능한 경우).


| amp.errorCode.[name] | 코드, 비트 [27-0](28비트) | 설명 |
|---|---:|---|
| **MEDIA_ERR_ABORTED 오류 범위(0x0100000 - 0x01FFFFF)** | | |
| abortedErrUnknown | 0x0100000 | 일반 중단 오류 |
| abortedErrNotImplemented | 0x0100001 | 중단 오류, 구현되지 않음 |
| abortedErrHttpMixedContentBlocked | 0x0100002 | 중단 오류, 혼합 콘텐츠 차단됨 - 일반적으로 `https://` 페이지에서 `http://` 스트림을 로드할 때 발생함 |
| **MEDIA_ERR_NETWORK 오류 시작 값(0x0200000 - 0x02FFFFF)** | | |
| networkErrUnknown | 0x0200000 | 일반 네트워크 오류 |
| networkErrHttpBadUrlFormat | 0x0200190 | HTTP 400 오류 응답 |
| networkErrHttpUserAuthRequired | 0x0200191 | HTTP 401 오류 응답 |
| networkErrHttpUserForbidden | 0x0200193 | HTTP 403 오류 응답 |
| networkErrHttpUrlNotFound | 0x0200194 | HTTP 404 오류 응답 |
| networkErrHttpNotAllowed | 0x0200195 | HTTP 405 오류 응답 |
| networkErrHttpGone | 0x020019A | HTTP 410 오류 응답 |
| networkErrHttpPreconditionFailed | 0x020019C | HTTP 412 오류 응답 |
| networkErrHttpInternalServerFailure | 0x02001F4 | HTTP 500 오류 응답
| networkErrHttpBadGateway | 0x02001F6 | HTTP 502 오류 응답 |
| networkErrHttpServiceUnavailable | 0x02001F7 | HTTP 503 오류 응답 |
| networkErrHttpGatewayTimeout | 0x02001F8 | HTTP 504 오류 응답 |
| networkErrTimeout | 0x0200258 | 네트워크 시간 제한 오류
| networkErrErr | 0x0200259 | 네트워크 연결 오류 응답 |
| **MEDIA_ERR_DECODE 오류(0x0300000 - 0x03FFFFF)** | | |
| decodeErrUnknown | 0x0300000 | 일반 디코드 오류 |
| **MEDIA_ERR_SRC_NOT_SUPPORTED 오류(0x0400000 - 0x04FFFFF)** | | |
| srcErrUnknown | 0x0400000 | 일반 원본이 지원되지 않음 오류 |
| srcErrParsePresentation | 0x0400001 | 프레젠테이션 구문 분석 오류 |
| srcErrParseSegment | 0x0400002 | 세그먼트 구문 분석 오류 |
| srcErrUnsupportedPresentation | 0x0400003 | 프레젠테이션이 지원되지 않음 |
| srcErrInvalidSegment | 0x0400004 | 잘못된 세그먼트 |
| **MEDIA_ERR_ENCRYPTED 오류 시작 값(0x0500000 - 0x05FFFFF)** | | |
| encryptErrUnknown | 0x0500000 | 일반 암호화 오류 | 
| encryptErrDecrypterNotFound | 0x0500001 | Decrypter를 찾을 수 없음 |
| encryptErrDecrypterInit | 0x0500002 | Decrypter 초기화 오류 |
| encryptErrDecrypterNotSupported | 0x0500003 | Decrypter가 지원되지 않음 |
| encryptErrKeyAcquire | 0x0500004 | 키 획득 실패 |
| encryptErrDecryption | 0x0500005 | 세그먼트 암호 해독 실패 |
| encryptErrLicenseAcquire | 0x0500006 | 라이선스 획득 실패 |
| **SRC_PLAYER_MISMATCH 오류 시작 값(0x0600000 - 0x06FFFFF)** | | |
| srcPlayerMismatchUnknown | 0x0600000 | 원본을 재생하기 위한 일치하는 일반 기술 플레이어 없음 |
| srcPlayerMismatchFlashNotInstalled | 0x0600001 |플래시 플러그 인이 설치되지 않음. 설치된 경우 원본이 재생될 수 있음. ‘또는’ Flash 30이 설치되고 AES 콘텐츠를 재생함.  이 경우 다른 브라우저를 사용해 보세요. Flash 30은 6월 7일 현재 지원되지 않음. 자세한 내용은 [알려진 문제](azure-media-player-known-issues.md)를 참조하세요. 참고: 0x00600003 경우 techOrder에 지정된 경우 Flash 및 Silverlight가 둘 다 설치되지 않음.|
| srcPlayerMismatchSilverlightNotInstalled | 0x0600002 | Silverlight 플러그 인이 설치되지 않음. 설치된 경우 원본이 재생될 수 있음. 참고: 0x00600003 경우 techOrder에 지정된 경우 Flash 및 Silverlight가 둘 다 설치되지 않음. |
| | 0x00600003 | techOrder에 지정된 경우 Flash 및 Silverlight가 둘 다 설치되지 않음 |
| **알 수 없는 오류(0x0FF00000)** | | |
| errUnknown | 0xFF00000 | 알 수 없는 오류 |

## <a name="user-error-messages"></a>사용자 오류 메시지 ##

표시되는 사용자 메시지는 오류 코드의 비트 27-20을 기반으로 합니다.

- MEDIA_ERR_ABORTED (1) - “비디오 재생을 중단했습니다.”
- MEDIA_ERR_NETWORK (2) - “네트워크 오류로 인해 비디오 다운로드가 부분적으로 실패했습니다.”
- MEDIA_ERR_DECODE (3) - “손상 문제가 발생했거나 브라우저에서 지원하지 않는 기능이 비디오에 사용되었기 때문에 비디오 재생이 중단되었습니다.”
- MEDIA_ERR_SRC_NOT_SUPPORTED (4) - “서버나 네트워크에서 오류가 발생했거나 형식이 지원되지 않기 때문에 비디오를 로드할 수 없습니다.”
- MEDIA_ERR_ENCRYPTED (5) - “비디오가 암호화되어 있으며 암호를 해독할 키가 없습니다.”
- SRC_PLAYER_MISMATCH (6) - “이 비디오에 대해 호환되는 원본을 찾을 수 없습니다.”
- MEDIA_ERR_UNKNOWN (0xFF) - “알 수 없는 오류가 발생했습니다.”

## <a name="examples"></a>예 ##

### <a name="0x10600001"></a>0x10600001 ##

“이 비디오에 대해 호환되는 원본을 찾을 수 없습니다.”가 최종 사용자에게 표시됩니다.

요청된 원본을 재생할 수 있는 기술 플레이어가 없지만 Flash 플러그 인이 설치된 경우 원본이 재생될 수 있습니다.  

### <a name="0x20200194"></a>0x20200194 ###

“네트워크 오류로 인해 비디오 다운로드가 부분적으로 실패했습니다.”가 최종 사용자에게 표시됩니다.

AzureHtml5JS가 HTTP 404 응답에서 재생에 실패했습니다.

### <a name="categorizing-errors"></a>오류 분류 ###

```javascript
    if(myPlayer.error().code & amp.errorCode.abortedErrStart) {
        // MEDIA_ERR_ABORTED errors
    }
    else if(myPlayer.error().code & amp.errorCode.networkErrStart) {
        // MEDIA_ERR_NETWORK errors
    }
    else if(myPlayer.error().code & amp.errorCode.decodeErrStart) {
        // MEDIA_ERR_DECODE errors
    }
    else if(myPlayer.error().code & amp.errorCode.srcErrStart) {
        // MEDIA_ERR_SRC_NOT_SUPPORTED errors
    }
    else if(myPlayer.error().code & amp.errorCode.encryptErrStart) {
        // MEDIA_ERR_ENCRYPTED errors
    }
    else if(myPlayer.error().code & amp.errorCode.srcPlayerMismatchStart) {
        // SRC_PLAYER_MISMATCH errors
    }
    else {
        // unknown errors
    }
```

### <a name="catching-a-specific-error"></a>특정 오류 Catch ###

다음 코드는 404 오류만 catch합니다.

```javascript
    if(myPlayer.error().code & amp.errorCode.networkErrHttpUrlNotFound) {
        // all http 404 errors
    }
```

## <a name="next-steps"></a>다음 단계 ##

- [Azure Media Player 빠른 시작](azure-media-player-quickstart.md)