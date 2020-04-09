---
title: TLS/SSL 문제 해결(MSAL iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: MSAL을 사용하여 TLS/SSL 인증서를 사용하여 다양한 문제에 대해 수행하는 작업에 대해 알아봅니다. 목표-C 라이브러리.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: marsma
ms.custom: aaddev
ms.openlocfilehash: 1507231c3ab395319d5ce95ec06dbb592c324aa6
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80881080"
---
# <a name="how-to-troubleshoot-msal-for-ios-and-macos-tlsssl-issues"></a>방법: iOS 및 macOS TLS/SSL 문제에 대 한 MSAL 문제 해결

이 문서에서는 [iOS 및 macOS용 MSAL(Microsoft 인증 라이브러리)을](reference-v2-libraries.md) 사용하는 동안 발생할 수 있는 문제를 해결하는 데 도움이 되는 정보를 제공합니다.

## <a name="network-issues"></a>네트워크 문제

**오류 -1200**: "SSL 오류가 발생하여 서버에 대한 보안 연결을 만들 수 없습니다."

이 오류는 연결이 안전하지 않다는 것을 의미합니다. 인증서가 유효하지 않은 경우에 발생합니다. TLS 검사에 실패하는 서버를 비롯한 자세한 `NSURLErrorFailingURLErrorKey` 내용은 `userInfo` 오류 개체의 사전을 참조하십시오.

이 오류는 Apple의 네트워킹 라이브러리에서 발생합니다. NSURL 오류 코드의 전체 목록은 macOS 및 iOS SDK의 NSURLError.h에 있습니다. 이 오류에 대한 자세한 내용은 [URL 로드 시스템 오류 코드를](https://developer.apple.com/documentation/foundation/1508628-url_loading_system_error_codes?language=objc)참조하십시오.

## <a name="certificate-issues"></a>인증서 문제

잘못된 인증서를 제공하는 URL이 인증 흐름의 일부로 사용하려는 서버에 연결되는 경우 문제를 진단하는 좋은 시작은 [SSL 서버 테스트와](https://www.ssllabs.com/ssltest/analyze.html)같은 SSL 유효성 검사 서비스로 URL을 테스트하는 것입니다. 서버를 다양한 시나리오와 브라우저에 대해 테스트하고 알려진 많은 취약점을 확인합니다.

기본적으로 Apple의 새로운 [ATS(앱 전송 보안)](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW35) 기능은 TLS/SSL 인증서를 사용하는 앱에 보다 엄격한 보안 정책을 적용합니다. 일부 운영 체제 및 웹 브라우저는 기본적으로 이러한 정책 중 일부를 적용하기 시작했습니다. 보안상의 이유로 ATS를 사용하지 않도록 설정하는 것이 좋습니다.

SHA-1 해시를 사용하는 인증서에는 취약점이 있습니다. 대부분의 최신 웹 브라우저는 SHA-1 해시가 있는 인증서를 허용하지 않습니다.

## <a name="captive-portals"></a>포로 포털

포로 포털은 사용자가 Wi-Fi 네트워크에 처음 액세스하고 해당 네트워크에 대한 액세스 권한을 아직 부여하지 않은 경우 웹 페이지를 제공합니다. 사용자가 포털의 요구 사항을 충족할 때까지 인터넷 트래픽을 차단합니다. 사용자가 포털을 통해 연결할 때까지 사용자가 네트워크 리소스에 연결할 수 없기 때문에 네트워크 오류가 예상됩니다.

## <a name="next-steps"></a>다음 단계

포로 [포털](https://en.wikipedia.org/wiki/Captive_portal) 및 Apple의 새로운 [ATS(앱 전송 보안)](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW35) 기능에 대해 알아봅니다.
