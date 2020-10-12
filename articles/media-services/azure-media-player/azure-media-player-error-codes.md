---
title: Azure Media Player 오류 코드
description: Azure Media Player에 대 한 오류 코드 참조입니다.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: error-reference
ms.date: 04/20/2020
ms.openlocfilehash: 13d804ec39c3d7753d4ee04962a88d4451fb04d9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "81727256"
---
# <a name="error-codes"></a>오류 코드 #

재생을 시작 하거나 중지할 수 없는 경우 오류 이벤트가 발생 하 고 `error()` 함수에서 코드 및 선택적 메시지를 반환 하 여 앱 개발자가 자세한 정보를 얻을 수 있도록 합니다. `error().message` 사용자에 게 표시 되는 메시지가 아닙니다.  사용자에 게 표시 되는 메시지는 `error().code` bits 27-20을 기반으로 합니다. 아래 표를 참조 하십시오.

```javascript

    var myPlayer = amp('vid1');
    myPlayer.addEventListener('error', function() {
        var errorDetails = myPlayer.error();
        var code = errorDetails.code;
        var message = errorDetails.message;
    }
```

## <a name="error-codes-bits-31-28-4-bits"></a>오류 코드, 비트 [31-28] (4 비트) ##

오류의 영역을 설명 합니다.

- 0 - 알 수 없음
- 1-AMP
- 2-AzureHtml5JS
- 3-Flashss 기술은
- 4-SilverlightSS
- 5-Html5
- 6-Html5FairPlayHLS

## <a name="error-codes-bits-27-0-28-bits"></a>오류 코드, 비트 [27-0] (28 비트) ##

오류에 대 한 세부 정보를 설명 하 고 bits 27-20는 높은 수준의 정보 19-0를 제공 합니다.


| amp. errorCode. 이름의 | 코드, 비트 [27-0] (28 비트) | 설명 |
|---|---:|---|
| **MEDIA_ERR_ABORTED 오류 범위 (0x0100000-0x01FFFFF)** | | |
| abortedErrUnknown | 0x0100000 | 일반 중단 오류 |
| abortedErrNotImplemented | 0x0100001 | 중단 오류, 구현 되지 않음 |
| abortedErrHttpMixedContentBlocked | 0x0100002 | 중단 오류, 혼합 된 콘텐츠 차단 됨-일반적으로 `http://` 페이지에서 스트림을 로드할 때 발생 합니다. `https://` |
| **MEDIA_ERR_NETWORK 오류 시작 값 (0x0200000-0X0200000)** | | |
| networkErrUnknown | 0x0200000 | 일반 네트워크 오류 |
| networkErrHttpBadUrlFormat | 0x0200190 | Http 400 오류 응답 |
| networkErrHttpUserAuthRequired | 0x0200191 | Http 401 오류 응답 |
| networkErrHttpUserForbidden | 0x0200193 | Http 403 오류 응답 |
| networkErrHttpUrlNotFound | 0x0200194 | Http 404 오류 응답 |
| networkErrHttpNotAllowed | 0x0200195 | Http 405 오류 응답 |
| networkErrHttpGone | 0x020019A | Http 410 오류 응답 |
| networkErrHttpPreconditionFailed | 0x020019C | Http 412 오류 응답 |
| networkErrHttpInternalServerFailure | 0x02001F4 | Http 500 오류 응답
| networkErrHttpBadGateway | 0x02001F6 | Http 502 오류 응답 |
| networkErrHttpServiceUnavailable 수 없음 | 0x02001F7 | Http 503 오류 응답 |
| networkErrHttpGatewayTimeout | 0x02001F8 | Http 504 오류 응답 |
| networkErrTimeout | 0x0200258 | 네트워크 시간 제한 오류
| networkErrErr | 0x0200259 | 네트워크 연결 오류 응답 |
| **MEDIA_ERR_DECODE 오류 (0x0300000-0X0300000)** | | |
| decodeErrUnknown | 0x0300000 | 일반 디코드 오류 |
| **MEDIA_ERR_SRC_NOT_SUPPORTED 오류 (0x0400000-0X0400000)** | | |
| srcErrUnknown | 0x0400000 | 제네릭 원본이 지원 되지 않음 오류 |
| srcErrParsePresentation | 0x0400001 | 프레젠테이션 구문 분석 오류 |
| srcErrParseSegment | 0x0400002 | 세그먼트 구문 분석 오류 |
| srcErrUnsupportedPresentation | 0x0400003 | 프레젠테이션이 지원 되지 않음 |
| srcErrInvalidSegment | 0x0400004 | 잘못 된 세그먼트 |
| **MEDIA_ERR_ENCRYPTED errors 시작 값 (0x0500000-0X0500000)** | | |
| encryptErrUnknown | 0x0500000 | 암호화 된 일반 오류 | 
| encryptErrDecrypterNotFound | 0x0500001 | Decrypter를 찾을 수 없음 |
| encryptErrDecrypterInit | 0x0500002 | Decrypter 초기화 오류 |
| encryptErrDecrypterNotSupported | 0x0500003 | Decrypter 지원 되지 않음 |
| encryptErrKeyAcquire | 0x0500004 | 키를 가져오지 못했습니다. |
| encryptErrDecryption | 0x0500005 | 세그먼트의 암호 해독이 실패 했습니다. |
| encryptErrLicenseAcquire | 0x0500006 | 라이선스 획득 실패 |
| **SRC_PLAYER_MISMATCH errors 시작 값 (0x0600000-0X0600000)** | | |
| srcPlayerMismatchUnknown | 0x0600000 | 소스를 재생 하는 일치 하는 기술 플레이어가 없는 제네릭 |
| srcPlayerMismatchFlashNotInstalled | 0x0600001 |플래시 플러그 인이 설치 되지 않았습니다. 설치 된 경우 원본을 재생할 수 있습니다. *또는* Flash 30이 설치 되 고 AES 콘텐츠를 재생 합니다.  이 경우 다른 브라우저를 사용해 보세요. Flash 30 년 6 월 7 일 현재 지원 되지 않습니다. 자세한 내용은 [알려진 문제](azure-media-player-known-issues.md) 를 참조 하세요. 참고: 0x00600003 인 경우 techOrder에 지정 된 경우 Flash 및 Silverlight가 설치 되지 않습니다.|
| srcPlayerMismatchSilverlightNotInstalled | 0x0600002 | Silverlight 플러그 인이 설치 되어 있지 않습니다. 설치 된 경우 원본이 재생 될 수 있습니다. 참고: 0x00600003 인 경우 techOrder에 지정 된 경우 Flash 및 Silverlight가 설치 되지 않습니다. |
| | 0x00600003 | TechOrder에 지정 된 경우 Flash 및 Silverlight가 설치 되지 않습니다. |
| **알 수 없는 오류 (0x0FF00000)** | | |
| errUnknown | 0xFF00000 | 알 수 없는 오류 |

## <a name="user-error-messages"></a>사용자 오류 메시지 ##

표시 되는 사용자 메시지는 오류 코드의 비트 27-20을 기반으로 합니다.

- MEDIA_ERR_ABORTED (1)-"비디오 재생을 중단 했습니다."
- MEDIA_ERR_NETWORK (2)-"네트워크 오류가 발생 하 여 비디오 다운로드가 부분적으로 실패 했습니다."
- MEDIA_ERR_DECODE (3)-"손상 문제로 인해 비디오 재생이 중단 되었거나 브라우저에서 사용 하는 비디오 기능이 지원 되지 않기 때문에 비디오 재생이 중단 되었습니다."
- MEDIA_ERR_SRC_NOT_SUPPORTED (4)-"서버나 네트워크가 실패 했거나 형식이 지원 되지 않기 때문에 비디오를 로드할 수 없습니다."
- MEDIA_ERR_ENCRYPTED (5)-"비디오가 암호화 되어 있으며 암호를 해독할 키가 없습니다."
- SRC_PLAYER_MISMATCH (6)-"이 비디오에 대해 호환 되는 소스를 찾을 수 없습니다."
- MEDIA_ERR_UNKNOWN (0xFF)-"알 수 없는 오류가 발생 했습니다."

## <a name="examples"></a>예 ##

### <a name="0x10600001"></a>0x10600001 ##

"이 비디오에 대해 호환 되는 소스를 찾을 수 없습니다." 최종 사용자에 게 표시 됩니다.

요청 된 원본을 재생할 수 있는 기술 플레이어가 없지만, 플래시 플러그 인이 설치 된 경우에는 원본을 재생할 수 있습니다.  

### <a name="0x20200194"></a>0x20200194 ###

"네트워크 오류가 발생 하 여 비디오 다운로드가 부분적으로 실패 했습니다." 최종 사용자에 게 표시 됩니다.

AzureHtml5JS가 http 404 응답에서 재생 하지 못했습니다.

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

### <a name="catching-a-specific-error"></a>특정 오류 catch ###

다음 코드에서는 404 오류만 catch 합니다.

```javascript
    if(myPlayer.error().code & amp.errorCode.networkErrHttpUrlNotFound) {
        // all http 404 errors
    }
```

## <a name="next-steps"></a>다음 단계 ##

- [Azure Media Player 빠른 시작](azure-media-player-quickstart.md)