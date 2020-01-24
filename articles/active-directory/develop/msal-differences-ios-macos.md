---
title: IOS 용 MSAL & macOS 차이점 | Microsoft
titleSuffix: Microsoft identity platform
description: IOS와 macOS 간의 MSAL (Microsoft 인증 라이브러리) 사용 차이점에 대해 설명 합니다.
services: active-directory
author: TylerMSFT
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: twhitney
ms.reviewer: oldalton
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: a17739151c56fec92a9697e1e04c39cb44da5088
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76696234"
---
# <a name="microsoft-authentication-library-for-ios-and-macos-differences"></a>iOS 및 macOS용 Microsoft 인증 라이브러리 차이점

이 문서에서는 iOS 및 macOS 용 MSAL (Microsoft 인증 라이브러리) 간의 기능 차이점에 대해 설명 합니다.

> [!NOTE]
> Mac에서 MSAL은 macOS 앱만 지원 합니다.

## <a name="general-differences"></a>일반적인 차이점

MacOS 용 MSAL은 iOS에서 사용할 수 있는 기능의 하위 집합입니다.

MacOS 용 MSAL은 다음을 지원 하지 않습니다.

- `ASWebAuthenticationSession`, `SFAuthenticationSession`, `SFSafariViewController`등의 다양 한 브라우저 유형이 있습니다.
- Microsoft Authenticator 앱을 통한 조정 된 인증은 macOS에 대해 지원 되지 않습니다.

동일한 게시자의 앱 간에 키 집합 공유는 macOS 10.14 이전 버전에서 더 제한적입니다. [액세스 제어 목록을](https://developer.apple.com/documentation/security/keychain_services/access_control_lists?language=objc) 사용 하 여 키 집합을 공유 하는 앱의 경로를 지정 합니다. 사용자에 게 추가 키 집합 프롬프트가 표시 될 수 있습니다.

MacOS 10.15 +에서 MSAL의 동작은 iOS와 macOS 사이에서 동일 합니다. MSAL은 키 집합 공유에 키 [집합 액세스 그룹](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps?language=objc) 을 사용 합니다. 

### <a name="conditional-access-authentication-differences"></a>조건부 액세스 인증 차이점

조건부 액세스 시나리오의 경우 iOS에 MSAL을 사용 하는 경우 더 많은 사용자 프롬프트가 표시 됩니다. 이는 iOS에서 broker 앱 (Microsoft Authenticator)을 사용 하 여 사용자에 게 사용자에 게 메시지를 표시 하지 않아도 되는 경우가 있기 때문입니다.

### <a name="project-setup-differences"></a>프로젝트 설정 차이점

**macOS**

- MacOS에서 프로젝트를 설정 하는 경우 응용 프로그램이 유효한 개발 또는 프로덕션 인증서로 서명 되었는지 확인 합니다. MSAL은 여전히 서명 되지 않은 모드에서 작동 하지만 캐시 지 속성과 관련 하 여 다르게 작동 합니다. 앱은 디버깅 목적으로 서명 되지 않은 경우에만 실행 해야 합니다. 서명 되지 않은 앱을 배포 하는 경우 다음 작업을 수행 합니다.
1. 10.14 및 이전 버전에서 MSAL은 앱을 다시 시작할 때마다 사용자에 게 키 집합 암호를 묻는 메시지를 표시 합니다.
2. 10.15 +에서 MSAL은 모든 토큰 획득에 대 한 자격 증명을 묻는 메시지를 사용자에 게 표시 합니다. 

- macOS 앱은 AppDelegate 호출을 구현할 필요가 없습니다.

**iOS**

- 인증 브로커 흐름을 지원 하도록 프로젝트를 설정 하는 추가 단계가 있습니다. 이 단계는 자습서에서 호출 됩니다.
- iOS 프로젝트는 info.plist에서 사용자 지정 스키마를 등록 해야 합니다. MacOS에서는 필요 하지 않습니다.
