---
title: 목표에 대 한 MSAL 문제 해결-C 문제
titleSuffix: Microsoft identity platform
description: MSAL을 사용 하 여 SSL 인증서를 사용 하는 다양 한 문제에 대해 알아봅니다. 목표-C 라이브러리입니다.
services: active-directory
documentationcenter: ''
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: twhitney
ms.reviewer: ''
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: fc918502cd252b4e53af8bcbd209a8387ef4d8c2
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72803658"
---
# <a name="how-to-troubleshoot-msal-for-ios-and-macos-ssl-issues"></a>방법: iOS 및 macOS SSL 문제에 대 한 MSAL 문제 해결

이 문서에서는 [iOS 및 macOS 용 MSAL (Microsoft 인증 라이브러리)](reference-v2-libraries.md) 을 사용 하는 동안 발생할 수 있는 문제를 해결 하는 데 도움이 되는 정보를 제공 합니다.

## <a name="network-issues"></a>네트워크 문제

**오류-1200**: "SSL 오류가 발생 하 여 서버에 대 한 보안 연결을 만들 수 없습니다."

이 오류는 연결이 안전 하지 않음을 의미 합니다. 인증서가 유효 하지 않은 경우에 발생 합니다. SSL 검사가 실패 하는 서버를 포함 하 여 자세한 내용은 오류 개체의 `userInfo` 사전에서 `NSURLErrorFailingURLErrorKey`를 참조 하세요.

이 오류는 Apple의 네트워킹 라이브러리에 있습니다. NSURL 오류 코드의 전체 목록은 macOS 및 iOS Sdk의 NSURLError에 있습니다. 이 오류에 대 한 자세한 내용은 [URL 로딩 시스템 오류 코드](https://developer.apple.com/documentation/foundation/1508628-url_loading_system_error_codes?language=objc)를 참조 하세요.

## <a name="certificate-issues"></a>인증서 문제

잘못 된 인증서를 제공 하는 URL이 인증 흐름의 일부로 사용 하려는 서버에 연결 되는 경우 문제를 진단 하는 좋은 방법은 [QUALYS Ssl Labs Analyzer](https://www.ssllabs.com/ssltest/analyze.html)와 같은 ssl 유효성 검사 서비스를 사용 하 여 url을 테스트 하는 것입니다. 광범위 한 시나리오와 브라우저에 대해 서버를 테스트 하 고 알려진 많은 취약점을 확인 합니다.

기본적으로 Apple의 새로운 [ATS (App Transport Security)](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW35) 기능은 SSL 인증서를 사용 하는 앱에 보다 엄격한 보안 정책을 적용 합니다. 일부 운영 체제와 웹 브라우저는 기본적으로 이러한 정책 중 일부를 적용 하기 시작 했습니다. 보안상의 이유로 ATS를 사용 하지 않도록 설정 하는 것이 좋습니다.

SHA-1 해시를 사용 하는 인증서에는 알려진 취약성이 있습니다. 대부분의 최신 웹 브라우저는 SHA-1 해시가 있는 인증서를 허용 하지 않습니다.

## <a name="captive-portals"></a>조임 포털

조임 된 포털은 사용자가 Wi-fi 네트워크에 처음 액세스할 때 해당 네트워크에 대 한 액세스 권한이 아직 부여 되지 않은 사용자에 게 웹 페이지를 제공 합니다. 사용자가 포털의 요구 사항을 충족할 때까지 인터넷 트래픽을 차단 합니다. 사용자가 포털을 통해 연결할 수 있을 때까지 사용자가 네트워크 리소스에 연결할 수 없기 때문에 네트워크 오류가 발생 합니다.

## <a name="next-steps"></a>다음 단계

[조임 포털](https://en.wikipedia.org/wiki/Captive_portal) 및 Apple의 새로운 [ATS (앱 전송 보안)](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW35) 기능에 대해 알아봅니다.
