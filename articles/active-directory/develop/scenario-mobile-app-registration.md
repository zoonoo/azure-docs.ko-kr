---
title: 웹 Api를 호출 하는 모바일 앱-앱의 코드 구성 | Microsoft id 플랫폼
description: 웹 Api를 호출 하는 모바일 앱을 빌드하는 방법에 대해 알아봅니다 (앱 코드 구성).
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f43ae9da51f68c9765a36d27c993d1c9935d61fa
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326113"
---
# <a name="mobile-app-that-calls-web-apis---app-registration"></a>웹 Api를 호출 하는 모바일 앱-앱 등록

이 문서에는 모바일 응용 프로그램을 만들기 위한 앱 등록 지침이 포함 되어 있습니다.

## <a name="supported-account-types"></a>지원되는 계정 유형

모바일 응용 프로그램에서 지원 되는 계정 유형은 사용 하려는 환경과 앱이 대상으로 하는 사용자에 따라 달라 집니다.

## <a name="platform-configuration-and-redirect-uris"></a>플랫폼 구성 및 리디렉션 Uri  

모바일 앱을 빌드할 때 가장 중요 한 등록 단계는 리디렉션 URI입니다. 이는 [인증 블레이드의 플랫폼 구성을](https://aka.ms/MobileAppReg)통해 설정할 수 있습니다.

이 환경에서는 앱이 Microsoft Authenticator (및 Android의 Intune 회사 포털)를 통해 SSO (single sign-on)를 가져오고 장치 관리 정책을 지원할 수 있습니다.

리디렉션 URI를 수동으로 구성 하려는 경우 응용 프로그램 매니페스트를 통해 수행할 수 있습니다. 권장 되는 형식은 다음과 같습니다.

- ***iOS***: `msauth.<BUNDLE_ID>://auth`
- ***Android***:`msauth://<PACKAGE_NAME>/<SIGNATURE_HASH>`
  - Android 서명 해시는 KeyTool 명령을 통해 릴리스 또는 디버그 키를 사용 하 여 생성할 수 있습니다.

## <a name="api-permissions"></a>API 사용 권한

모바일 응용 프로그램은 로그인 한 사용자를 대신 하 여 Api를 호출 합니다. 앱에서 위임 된 권한 (범위 라고도 함)을 요청 해야 합니다. 원하는 환경에 따라이 작업은 Azure Portal를 통해 정적으로 또는 런타임에 동적으로 수행할 수 있습니다. 권한을 정적으로 등록 하면 관리자가 앱을 쉽게 승인할 수 있으며 권장 됩니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [토큰 획득](scenario-mobile-acquire-token.md)
