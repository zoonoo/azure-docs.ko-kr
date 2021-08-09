---
title: iOS 및 macOS용 MSAL 간 차이점 | Azure
titleSuffix: Microsoft identity platform
description: iOS와 macOS 간의 MSAL(Microsoft 인증 라이브러리) 사용 차이점에 대해 설명합니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: 59e4111b6dda386777e820c9be16ace9ff01135c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98064918"
---
# <a name="microsoft-authentication-library-for-ios-and-macos-differences"></a>iOS 및 macOS용 Microsoft 인증 라이브러리 차이점

이 문서에서는 iOS 및 macOS용 MSAL(Microsoft 인증 라이브러리) 간의 기능적 차이점에 대해 설명합니다.

> [!NOTE]
> Mac에서 MSAL은 macOS 앱만 지원합니다.

## <a name="general-differences"></a>일반적인 차이점

macOS용 MSAL은 iOS에서 사용할 수 있는 기능의 하위 집합입니다.

macOS용 MSAL은 다음을 지원하지 않습니다.

- `ASWebAuthenticationSession`, `SFAuthenticationSession`, `SFSafariViewController` 등의 다양한 브라우저 유형.
- Microsoft Authenticator 앱을 통한 조정된 인증은 macOS에 대해 지원되지 않습니다.

동일한 게시자의 앱 간에 키 집합 공유는 macOS 10.14 이하의 버전에서 더 제한적입니다. [액세스 제어 목록](https://developer.apple.com/documentation/security/keychain_services/access_control_lists?language=objc)을 사용하여 키 집합을 공유해야 하는 앱의 경로를 지정합니다. 사용자에게 추가 키 집합 프롬프트가 표시될 수 있습니다.

macOS 10.15 이상에서 MSAL의 동작은 iOS와 macOS 간에 동일합니다. MSAL은 키 집합 공유에 [키 집합 액세스 그룹](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps?language=objc)을 사용합니다. 

### <a name="conditional-access-authentication-differences"></a>조건부 액세스 인증 차이점

조건부 액세스 시나리오의 경우 iOS용 MSAL을 사용하면 사용자에게 더 적은 메시지가 표시됩니다. 이는 iOS에서 경우에 따라 사용자에게 메시지를 표시할 필요가 없는 브로커 앱(Microsoft Authenticator)을 사용하기 때문입니다.

### <a name="project-setup-differences"></a>프로젝트 설정 차이점

**macOS**

- macOS에서 프로젝트를 설정하는 경우 애플리케이션이 유효한 개발 또는 프로덕션 인증서로 서명되었는지 확인합니다. MSAL은 여전히 서명되지 않은 모드로 작동하지만 캐시 지속성과 관련하여 다르게 작동합니다. 앱은 디버깅 목적으로만 서명되지 않은 상태로 실행되어야 합니다. 서명되지 않은 앱을 배포하는 경우 다음 작업을 수행합니다.
1. 10.14 이하의 버전에서 MSAL은 앱을 다시 시작할 때마다 사용자에게 키 집합 암호를 묻는 메시지를 표시합니다.
2. 10.15 이상의 버전에서 MSAL은 토큰을 획득할 때마다 사용자에게 자격 증명을 묻는 메시지를 표시합니다. 

- macOS 앱은 AppDelegate 호출을 구현할 필요가 없습니다.

**iOS**

- 인증 브로커 흐름을 지원하도록 프로젝트를 설정하는 추가 단계가 있습니다. 해당 단계는 자습서에서 설명합니다.
- iOS 프로젝트는 info.plist에 사용자 지정 체계를 등록해야 합니다. macOS에서는 필요하지 않습니다.
