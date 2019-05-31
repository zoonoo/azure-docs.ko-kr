---
title: 웹 앱의 코드 구성 Api를 호출 되는 모바일 앱 | Microsoft id 플랫폼
description: 모바일 앱을 빌드하는 방법을 알아봅니다 호출 web Api (응용 프로그램의 코드 구성)는
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
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
ms.openlocfilehash: 2b6ebab0eeca6895e1c7a0f6008972030d81da42
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/21/2019
ms.locfileid: "65962394"
---
# <a name="mobile-app-that-calls-web-apis---app-registration"></a>웹 앱 등록 Api를 호출 되는 모바일 앱

이 문서에서는 모바일 응용 프로그램을 만들기 위한 앱 등록 지침을 포함 합니다.

## <a name="supported-account-types"></a>지원되는 계정 유형

모바일 응용 프로그램에서 지원 되는 계정 유형을 사용 하도록 설정 하려는 환경 및 앱을 대상으로 하는 사용자에 따라 달라 집니다.

## <a name="platform-configuration-and-redirect-uris"></a>플랫폼 구성 및 리디렉션 Uri  

모바일 앱을 빌드하는 경우 가장 중요 한 등록 단계는 리디렉션 URI입니다. 이 통해 설정할 수 있습니다 합니다 [인증 블레이드에서 플랫폼 구성을](https://aka.ms/MobileAppReg)합니다.

이 환경을 얻기 위해 응용 프로그램에서 single sign-on (SSO) Microsoft Authenticator (및 Android에서 Intune 회사 포털)을 통해 지원 뿐만 아니라 장치 관리 정책을 사용 하도록 설정 됩니다.

리디렉션 URI를 수동으로 구성 하려는 경우에이 응용 프로그램 매니페스트를 통해 수행할 수 있습니다. 권장 되는 형식은 다음과 같습니다.

- ***iOS***: `msauth.<BUNDLE_ID>://auth`
- ***Android***: `msauth://<PACKAGE_NAME>/<SIGNATURE_HASH>`
  - KeyTool 명령을 통해 릴리스 또는 디버그 키를 사용 하 여 Android 서명 해시를 생성할 수 있습니다.

## <a name="api-permissions"></a>API 사용 권한

모바일 응용 프로그램에서 로그인 한 사용자를 대신 하 여 Api를 호출합니다. 앱 범위 라고도 하는 위임 된 권한을 요청 해야 합니다. 원하는 환경에 따라 이렇게 하려면 정적으로 Azure portal을 통해 또는 런타임 시 동적으로 합니다. 정적으로 등록 하는 권한을 쉽게 앱을 승인 하는 관리자를 사용 하면 있으며 권장 됩니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [토큰 가져오기](scenario-mobile-acquire-token.md)
