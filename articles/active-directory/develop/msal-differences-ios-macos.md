---
title: iOS용 MSAL & 맥OS 차이 | Azure
titleSuffix: Microsoft identity platform
description: iOS와 macOS 간의 MSAL(MsAL) 사용 차이에 대해 설명합니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: 62b79ee7398286b8e6c8ed8612bd001595e1f6ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084972"
---
# <a name="microsoft-authentication-library-for-ios-and-macos-differences"></a>iOS 및 macOS용 Microsoft 인증 라이브러리 차이점

이 문서에서는 iOS 및 macOS용 MSAL(Microsoft 인증 라이브러리) 간의 기능 차이에 대해 설명합니다.

> [!NOTE]
> Mac에서 MSAL은 macOS 앱만 지원합니다.

## <a name="general-differences"></a>일반적인 차이점

macOS용 MSAL은 iOS에서 사용할 수 있는 기능의 하위 집합입니다.

macOS용 MSAL은 다음을 지원하지 않습니다.

- 의 다른 브라우저 `ASWebAuthenticationSession` `SFAuthenticationSession`유형 `SFSafariViewController`(예: " )
- Microsoft 인증자 앱을 통해 중개된 인증은 macOS에 대해 지원되지 않습니다.

동일한 게시자의 앱 간에 키체인 공유는 macOS 10.14 이상에서 더 제한됩니다. [액세스 제어 목록을](https://developer.apple.com/documentation/security/keychain_services/access_control_lists?language=objc) 사용하여 키체인을 공유해야 하는 앱에 대한 경로를 지정합니다. 사용자가 추가 키체인 프롬프트를 볼 수 있습니다.

macOS 10.15+에서 MSAL의 동작은 iOS와 macOS 간에 동일합니다. MSAL은 [키체인 공유를](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps?language=objc) 위해 키체인 액세스 그룹을 사용합니다. 

### <a name="conditional-access-authentication-differences"></a>조건부 액세스 인증 차이

조건부 액세스 시나리오의 경우 iOS용 MSAL을 사용할 때 사용자 프롬프트가 줄어듭니다. 이는 iOS가 브로커 앱(Microsoft 인증자)을 사용하여 경우에 따라 사용자에게 메시지를 표시할 필요가 없기 때문입니다.

### <a name="project-setup-differences"></a>프로젝트 설정 차이

**macOS**

- macOS에서 프로젝트를 설정할 때 응용 프로그램이 유효한 개발 또는 프로덕션 인증서로 서명되었는지 확인합니다. MSAL은 여전히 서명되지 않은 모드에서 작동하지만 캐시 지속성과 관련하여 다르게 작동합니다. 앱은 디버깅을 위해 서명되지 않은 경우에만 실행되어야 합니다. 서명되지 않은 앱을 배포하면 다음과 같은 이 정보를 사용할 수 있습니다.
1. 10.14 이전, MSAL은 앱을 다시 시작할 때마다 키체인 암호를 사용자에게 묻는 메시지를 표시합니다.
2. 10.15+에서 MSAL은 모든 토큰 획득에 대한 자격 증명을 사용자에게 묻는 메시지를 표시합니다. 

- macOS 앱은 AppDelegate 호출을 구현할 필요가 없습니다.

**iOS**

- 인증 브로커 흐름을 지원하도록 프로젝트를 설정하는 추가 단계가 있습니다. 단계는 자습서에서 호출 됩니다.
- iOS 프로젝트는 info.plist에 사용자 지정 스키마를 등록해야 합니다. macOS에는 이 필수가 필요하지 않습니다.
