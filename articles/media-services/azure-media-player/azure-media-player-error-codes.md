---
title: Azure 미디어 플레이어 오류 코드
description: Azure 미디어 플레이어에 대한 오류 코드 참조입니다.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: error-reference
ms.date: 04/20/2020
ms.openlocfilehash: 13d804ec39c3d7753d4ee04962a88d4451fb04d9
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727256"
---
# <a name="error-codes"></a>오류 코드 #

재생을 시작할 수 없거나 중지하면 오류 이벤트가 발생되고 `error()` 함수가 코드와 선택적 메시지를 반환하여 앱 개발자가 자세한 내용을 얻을 수 있도록 합니다. `error().message`사용자에게 표시되는 메시지가 아닙니다.  사용자에게 표시되는 메시지는 비트 27-20을 기반으로 `error().code` 하며 아래 표를 참조하십시오.

```javascript

    var myPlayer = amp('vid1');
    myPlayer.addEventListener('error', function() {
        var errorDetails = myPlayer.error();
        var code = errorDetails.code;
        var message = errorDetails.message;
    }
```

## <a name="error-codes-bits-31-28-4-bits"></a>오류 코드, 비트 [31-28] (4 비트) ##

오류 영역을 설명합니다.

- 0 - 알 수 없음
- 1 - 앰프
- 2 - AzureHtml5JS
- 3 - 플래시
- 4 - 실버라이트SS
- 5 - Html5
- 6 - Html5페어플레이HLS

## <a name="error-codes-bits-27-0-28-bits"></a>오류 코드, 비트 [27-0] (28비트) ##

오류의 세부 사항을 설명, 비트 27-20 높은 수준을 제공, 비트 19-0 가능한 경우 자세한 내용을 제공합니다.


| amp.errorCode. 【네임】 | 코드, 비트 [27-0] (28 비트) | 설명 |
|---|---:|---|
| **MEDIA_ERR_ABORTED 오류 범위(0x0100000 - 0x01FFFFF)** | | |
| 중단된 Err알 수 없음 | 0x0100000 | 일반 중단 오류 |
| 중단된ErrNot구현 | 0x0100001 | 구현되지 않은 오류 중단 |
| 중단된ErrHttpMixed콘텐츠차단 | 0x0100002 | 중단 오류, 혼합 콘텐츠 차단 - 일반적으로 `http://` 페이지에서 `https://` 스트림을 로드할 때 발생합니다. |
| **MEDIA_ERR_NETWORK 오류 시작 값(0x0200000 - 0x02FFFFF)** | | |
| 네트워크Err알 수 없음 | 0x0200000 | 일반 네트워크 오류 |
| 네트워크ErrHttpBadUrl형식 | 0x0200190 | Http 400 오류 응답 |
| 네트워크ErrHttpUserAuth필수 | 0x0200191 | Http 401 오류 응답 |
| 네트워크ErrHttpUser금지 | 0x0200193 | Http 403 오류 응답 |
| 네트워크ErrHttpUrlNot발견 | 0x0200194 | Http 404 오류 응답 |
| 네트워크ErrHttpNot허용됨 | 0x0200195 | Http 405 오류 응답 |
| 네트워크ErrHttpGone | 0x020019A | HTTP 410 오류 응답 |
| 네트워크ErrHttp사전 조건 실패 | 0x020019C | Http 412 오류 응답 |
| 네트워크ErrHttp내부 서버실패 | 0x02001F4 | Http 500 오류 응답
| 네트워크에르Http배드게이트웨이 | 0x02001F6 | Http 502 오류 응답 |
| 네트워크ErrHttp서비스사용할 수 없음 | 0x02001F7 | Http 503 오류 응답 |
| 네트워크ErrHttp게이트웨이타임아웃 | 0x02001F8 | Http 504 오류 응답 |
| 네트워크Err타임아웃 | 0x0200258 | 네트워크 시간 시간 오류
| 네트워크Errerr | 0x0200259 | 네트워크 연결 오류 응답 |
| **MEDIA_ERR_DECODE 오류(0x0300000 - 0x03FFFFF)** | | |
| 디코더알 수 없음 | 0x0300000 | 일반 디코딩 오류 |
| **MEDIA_ERR_SRC_NOT_SUPPORTED 오류(0x0400000 - 0x04FFFFF)** | | |
| srcErr알 수 없음 | 0x0400000 | 제네릭 소스가 지원되지 않는 오류 |
| srcerrParse프리젠테이션 | 0x0400001 | 프레젠테이션 구문 분석 오류 |
| srcerrParseSegment | 0x0400002 | 세그먼트 구문 분석 오류 |
| srcerrun지원프리젠테이션 | 0x0400003 | 프레젠테이션이 지원되지 않음 |
| srcErr유효 세그먼트 | 0x0400004 | 무효 세그먼트 |
| **MEDIA_ERR_ENCRYPTED 오류 시작 값(0x0500000 - 0x05FFFFF)** | | |
| 암호화어알 수 없음 | 0x0500000 | 일반 암호화 오류 | 
| 암호화어 해독기Not | 0x0500001 | 암호 해독기를 찾을 수 없습니다. |
| 암호화해독기Init | 0x0500002 | 암호 해독기 초기화 오류 |
| 암호화어 해독기Not지원되지 않음 | 0x0500003 | 암호 해독기가 지원되지 않음 |
| 암호화어키취득 | 0x0500004 | 키 획득 실패 |
| 암호화어 해독 | 0x0500005 | 세그먼트의 암호 해독실패 |
| 암호화어 라이센스취득 | 0x0500006 | 라이센스 취득 실패 |
| **SRC_PLAYER_MISMATCH 오류 시작 값(0x0600000 - 0x06FFFFF)** | | |
| srcPlayerMismatch알 수 없음 | 0x0600000 | 소스를 재생하는 일반 더 일치하는 기술 플레이어 |
| srcPlayer Mismatch플래시설치안 | 0x0600001 |플래시 플러그인이 설치되지 않은 경우 소스가 재생될 수 있습니다. *또는* 플래시 30이 설치되어 AES 콘텐츠를 재생합니다.  이 경우 다른 브라우저를 사용해 보십시오. 플래시 30은 6월 7일 현재 지원되지 않습니다. 자세한 내용은 [알려진 문제를](azure-media-player-known-issues.md) 참조하십시오. 참고: 0x00600003인 경우 techOrder에 지정된 경우 플래시와 실버라이트가 모두 설치되지 않습니다.|
| srcPlayerMismatch실버라이트 설치안 | 0x0600002 | 실버 라이트 플러그인이 설치되지 않은 경우 소스가 재생할 수 있습니다 설치. 참고: 0x00600003인 경우 techOrder에 지정된 경우 플래시와 실버라이트가 모두 설치되지 않습니다. |
| | 0x00600003 | techOrder에 지정된 경우 플래시와 실버라이트가 모두 설치되지 않습니다. |
| **알 수 없는 오류(0x0FF0000)** | | |
| 언루미티드 | 0xFF00000 | 알 수 없는 오류 |

## <a name="user-error-messages"></a>사용자 오류 메시지 ##

표시되는 사용자 메시지는 오류 코드의 비트 27-20을 기반으로 합니다.

- MEDIA_ERR_ABORTED (1) -"비디오 재생을 중단했습니다"
- MEDIA_ERR_NETWORK (2) - "네트워크 오류로 인해 비디오 다운로드가 부분적으로 실패했습니다."
- MEDIA_ERR_DECODE (3) - "비디오 재생이 손상 문제로 인해 중단되었거나 사용된 비디오가 브라우저에서 지원하지 않는 기능으로 인해 중단되었습니다."
- MEDIA_ERR_SRC_NOT_SUPPORTED (4)-"서버 또는 네트워크가 실패했거나 형식이 지원되지 않기 때문에 비디오를 로드할 수 없습니다."
- MEDIA_ERR_ENCRYPTED (5) - "비디오가 암호화되어 있으며 암호를 해독할 키가 없습니다."
- SRC_PLAYER_MISMATCH (6) - "이 비디오에 호환되는 소스를 찾을 수 없습니다."
- MEDIA_ERR_UNKNOWN(0xFF) - "알 수 없는 오류가 발생했습니다."

## <a name="examples"></a>예 ##

### <a name="0x10600001"></a>0x10600001 ##

"이 비디오에 호환되는 소스를 찾을 수 없습니다." 최종 사용자에게 표시됩니다.

요청된 소스를 재생할 수 있는 기술 플레이어는 없지만 Flash 플러그인이 설치되어 있으면 소스를 재생할 수 있습니다.  

### <a name="0x20200194"></a>0x20200194 ###

"네트워크 오류로 인해 비디오 다운로드가 부분적으로 실패했습니다." 최종 사용자에게 표시됩니다.

AzureHtml5JShttp 404 응답에서 재생하지 못했습니다.

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

### <a name="catching-a-specific-error"></a>특정 오류 잡기 ###

다음 코드는 404개의 오류만 catch합니다.

```javascript
    if(myPlayer.error().code & amp.errorCode.networkErrHttpUrlNotFound) {
        // all http 404 errors
    }
```

## <a name="next-steps"></a>다음 단계 ##

- [Azure 미디어 플레이어 빠른 시작](azure-media-player-quickstart.md)