---
title: 몰입 형 판독기 iOS SDK 참조
titlesuffix: Azure Cognitive Services
description: 몰입 형 판독기 iOS SDK에 대 한 참조
services: cognitive-services
author: MeganRoach
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 08/01/2019
ms.author: t-meroa
ms.openlocfilehash: acdaaf0bf08644053e86343ae4b002002fee6a84
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68966592"
---
# <a name="immersive-reader-sdk-reference"></a>몰입 형 판독기 SDK 참조

몰입 형 판독기 iOS SDK는 몰입 형 판독기를 iOS 응용 프로그램에 통합할 수 있는 Swift CocoaPod입니다.

## <a name="functions"></a>함수

SDK는 단일 함수인를 `launchImmersiveReader(navController, token, subdomain, content, options, onSuccess, onFailure)`노출 합니다.

### <a name="launchimmersivereader"></a>launchImmersiveReader

IOS 응용 프로그램에서 뷰 컨트롤러를 시작 하 여 몰입 형 판독기를 시작 합니다.

```swift
public func launchImmersiveReader(navController: UINavigationController, token: String, subdomain: String, content: Content, options: Options?, onSuccess: @escaping () -> Void, onFailure: @escaping (_ error: Error) -> Void)
```

#### <a name="parameters"></a>매개 변수

| 이름 | 형식 | 설명 |
| ---- | ---- |------------ |
| `navController` | UINavigationController | 함수를 호출 하는 iOS 응용 프로그램에 대 한 탐색 컨트롤러입니다. |
| `token` | String | Azure AD 인증 토큰입니다. [AZURE AD 인증 방법을](./azure-active-directory-authentication.md)참조 하세요. |
| `subdomain` | String | Azure에서 몰입 형 판독기 리소스의 사용자 지정 하위 도메인입니다. [AZURE AD 인증 방법을](./azure-active-directory-authentication.md)참조 하세요. |
| `content` | [콘텐츠](#content) | 몰입 형 판독기에 표시할 콘텐츠를 포함 하는 개체입니다. |
| `options` | [옵션](#options) | 몰입 형 판독기의 특정 동작을 구성 하기 위한 옵션입니다. 선택 사항입니다. |
| `onSuccess` | ()-> Void | 몰입 형 판독기가 성공적으로 시작 될 때 호출 되는 닫기입니다. |
| `onFailure` | (_ 오류: [오류](#error)) -> Void | 몰입 형 판독기를 로드 하지 못할 때 호출 되는 닫기입니다. 이 클로저는 오류 [`Error`](#error) 코드 및 오류와 관련 된 오류 메시지를 나타내는 개체를 반환 합니다. 자세한 내용은 [오류 코드](#error-codes)를 참조 하세요. |

## <a name="types"></a>유형

### <a name="content"></a>콘텐츠

몰입 형 판독기에 표시할 콘텐츠를 포함 합니다.

```swift
struct Content: Encodable {
    var title: String
    var chunks: [Chunk]
}
```

#### <a name="supported-mime-types"></a>지원 되는 MIME 형식

| MIME 형식 | Description |
| --------- | ----------- |
| 텍스트/일반 | 일반 텍스트입니다. |
| application/mathml + xml | MathML (수학 Markup Language). [자세히 알아보기](https://developer.mozilla.org/en-US/docs/Web/MathML).

### <a name="options"></a>변수

몰입 형 판독기의 특정 동작을 구성 하는 속성을 포함 합니다.

```swift
struct Options {
    var uiLang: String?
    var timeout: TimeInterval?
}
```

### <a name="error"></a>Error

오류에 대 한 정보를 포함 합니다.

```swift
struct Error {
    var code: String
    var message: String
}
```

#### <a name="error-codes"></a>오류 코드

| 코드 | 설명 |
| ---- | ----------- |
| BadArgument | 제공 된 인수가 잘못 되었습니다. `message` 자세한 내용은를 참조 하십시오. |
| 제한 시간 | 몰입 형 판독기를 지정 된 시간 제한 내에 로드 하지 못했습니다. |
| TokenExpired | 제공 된 토큰이 만료 되었습니다. |
| 정체됨 | 호출 속도로 제한을 초과 했습니다. |
| InternalError | 몰입 형 판독기 뷰 컨트롤러 내에서 내부 오류가 발생 했습니다. 자세한 내용은 `message`를 참조하세요.|

## <a name="os-version-support"></a>OS 버전 지원

몰입 형 판독기 iOS SDK는 iPad 및 iPhone의 iOS 9.0 이상에서 지원 됩니다.

## <a name="next-steps"></a>다음 단계

* [GitHub의 몰입 형 판독기 IOS SDK](https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS) 살펴보기
* [빠른 시작: 몰입 형 판독기를 실행 하는 iOS 앱 만들기 (Swift)](./ios-quickstart.md)