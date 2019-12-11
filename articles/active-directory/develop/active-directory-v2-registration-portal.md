---
title: 앱 등록 포털 참조 | Microsoft
titleSuffix: Microsoft identity platform
description: Microsoft 앱 등록 포털의 기능에 대 한 설명입니다.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: f0507c28-9464-4d3e-bd53-de9053fd5278
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: ryanwi
ms.reviewer: lenalepa
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1699aad41d8a5da153199f79f65af67bd04c6b2c
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74966098"
---
# <a name="app-registration-reference"></a>Alkalmazásregisztráció-referencia

이 문서에서는 Azure Portal에서 [앱 등록](https://aka.ms/appregistrations) 환경에 있는 다양 한 기능에 대 한 컨텍스트 및 설명을 제공 합니다.

## <a name="my-applications-or-converged-applications"></a>내 응용 프로그램 또는 수렴 형 응용 프로그램

이 목록에는 v2.0 (Microsoft identity platform) 끝점과 함께 사용 하도록 등록 된 모든 응용 프로그램이 포함 되어 있습니다. 이러한 응용 프로그램에는 Azure Active Directory에서 개인 Microsoft 계정과 회사/학교 계정 둘 다로 사용자를 로그인 하는 기능이 있습니다. Id 플랫폼 끝점에 대 한 자세한 내용은 v2.0 [개요](active-directory-appmodel-v2-overview.md)를 참조 하세요. 이러한 응용 프로그램을 사용 하 여 Microsoft 계정 인증 끝점 `https://login.live.com`와 통합할 수도 있습니다.

## <a name="azure-ad-only-applications"></a>Azure AD 전용 응용 프로그램

이 목록에는 Azure AD v1.0 끝점과 함께 사용 하도록 등록 된 모든 응용 프로그램이 포함 되어 있습니다. 이러한 응용 프로그램에는 Azure Active Directory에서 회사/학교 계정으로 사용자를 로그인 하는 기능만 있습니다. 이 목록에는 [Azure Portal](https://portal.azure.com)에서 **앱 등록** 환경을 사용 하 여 등록 된 응용 프로그램이 포함 됩니다.

## <a name="live-sdk-applications"></a>라이브 SDK 응용 프로그램

이 목록에는 Microsoft 계정만 사용 하도록 등록 된 모든 응용 프로그램이 포함 됩니다. Azure Active Directory에서 사용할 수 없습니다. `https://account.live.com/developers/applications`에서 MSA 개발자 포털에 이전에 등록 된 응용 프로그램을 찾을 수 있습니다. `https://account.live.com/developers/applications`에서 이전에 수행한 모든 함수를 이제 [앱 등록](https://aka.ms/appregistrations)에서 수행할 수 있습니다.

## <a name="application-secrets"></a>Titkos alkalmazáskulcsok

응용 프로그램 암호는 응용 프로그램이 Azure AD를 사용 하 여 신뢰할 수 있는 [클라이언트 인증](https://tools.ietf.org/html/rfc6749#section-2.3) 을 수행할 수 있도록 하는 자격 증명입니다. OAuth & Openid connect Connect에서 응용 프로그램 암호를 일반적으로 `client_secret`라고 합니다. V2.0 프로토콜에서 웹 주소 지정 가능한 위치 (`https` 체계 사용)에서 보안 토큰을 수신 하는 응용 프로그램은 해당 보안 토큰을 상환 하는 경우 응용 프로그램 암호를 사용 하 여 Azure AD에서 자신을 식별 해야 합니다. 또한 장치에서 토큰을 수신 하는 모든 native client는 응용 프로그램 암호를 사용 하 여 클라이언트 인증을 수행할 수 없습니다. 이로 인해 안전 하지 않은 환경에서 암호를 저장 하지 않아도 됩니다.

각 앱은 지정 된 시간에 두 개의 유효한 응용 프로그램 암호를 포함할 수 있습니다. 두 비밀을 유지 하면 응용 프로그램 전체 환경에서 정기적인 키 롤오버를 수행할 수 있습니다. 응용 프로그램 전체를 새 암호로 마이그레이션한 후 이전 암호를 삭제 하 고 새 암호를 프로 비전 할 수 있습니다.

지금은 앱 등록 포털에서 두 가지 유형의 응용 프로그램 비밀이 허용 됩니다. **새 암호 생성** 을 선택 하면 응용 프로그램에서 사용할 수 있는 해당 데이터 저장소에 공유 암호가 생성 되 고 저장 됩니다. **새 키 쌍 생성** 을 선택 하면 Azure AD에 대 한 클라이언트 인증에 다운로드 하 여 사용할 수 있는 새 공개/개인 키 쌍이 생성 됩니다. **공개 키 업로드** 를 선택 하면 사용자 고유의 공개/개인 키 쌍을 사용할 수 있습니다.
공개 키가 포함 된 인증서를 업로드 해야 합니다.

## <a name="profile"></a>Profil

앱 등록 포털의 프로필 섹션을 사용 하 여 응용 프로그램에 대 한 로그인 페이지를 사용자 지정할 수 있습니다. 이번에는 로그인 페이지의 응용 프로그램 로고, 서비스 약관 URL 및 개인정보 처리 방침 URL을 변경할 수 있습니다. 로고는 15KB 미만의 GIF, PNG 또는 JPEG 파일의 투명 한 48 x 48 또는 50 x 50 픽셀 이미지 여야 합니다. 값을 변경 하 고 결과 로그인 페이지를 봅니다.

## <a name="live-sdk-support"></a>Live SDK 지원

"Live SDK 지원"을 사용 하도록 설정 하면 사용자가 만든 모든 응용 프로그램 비밀이 Azure AD 및 Microsoft 계정 데이터 저장소에 프로 비전 됩니다. 이렇게 하면 응용 프로그램을 login.live.com (Microsoft 계정 서비스)와 직접 통합할 수 있습니다. V2.0 끝점을 사용 하는 대신 Microsoft 계정을 사용 하 여 앱을 직접 빌드하려면 Live SDK 지원이 사용 하도록 설정 되어 있는지 확인 해야 합니다.

라이브 SDK 지원을 사용 하지 않도록 설정 하면 응용 프로그램 비밀이 Azure AD 데이터 저장소에만 기록 됩니다. Azure AD 데이터 저장소는 FISMA 규정 준수와 같은 특정 표준을 충족할 수 있는 엔터프라이즈급 규정을 통합 합니다. 라이브 SDK 지원을 사용 하도록 설정 하면 응용 프로그램에서 이러한 표준 중 일부를 준수 하지 않을 수 있습니다.

V2.0 끝점을 사용 하려는 경우에만 라이브 SDK 지원을 안전 하 게 비활성화할 수 있습니다.
