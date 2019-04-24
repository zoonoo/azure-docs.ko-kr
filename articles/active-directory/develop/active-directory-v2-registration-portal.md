---
title: 앱 등록 포털 도움말 항목 | Microsoft Docs
description: Microsoft 앱 등록 포털의 다양한 기능을 설명합니다.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: f0507c28-9464-4d3e-bd53-de9053fd5278
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2018
ms.author: celested
ms.reviewer: lenalepa
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b77b5185056329113ee1fd17fa3ed3f364380ca2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60411218"
---
# <a name="app-registration-reference"></a>앱 등록 참조
이 문서에서는 [Microsoft 앱 등록 포털](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/)에 있는 다양한 기능에 대한 컨텍스트와 설명을 제공합니다.

> [!NOTE]
> 우리는 더 이상 지원 등록 하 고에서 수렴 형 및 Azure AD 응용 프로그램을 관리 합니다 [응용 프로그램 등록 포털](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/) 2019 년 5 월 시작 합니다. 기존 응용 프로그램 관리를 사용 하 여 새 응용 프로그램을 등록 하는 것이 좋습니다 합니다 [앱 등록 (미리 보기)](https://aka.ms/appregistrations) Azure portal에서 발생 합니다.

## <a name="my-applications-or-converged-applications"></a>내 애플리케이션 또는 수렴된 애플리케이션
이 목록에는 Azure AD v2.0 엔드포인트에서 사용하도록 등록된 모든 애플리케이션이 포함되어 있습니다. 이러한 애플리케이션은 Azure Active Directory에서 개인 Microsoft 계정 및 회사/학교 계정 모두를 사용하는 사용자를 로그인할 수 있습니다. Azure AD v2.0 엔드포인트에 대한 자세한 내용은 [v2.0 개요](active-directory-appmodel-v2-overview.md)를 참조하세요. 이러한 애플리케이션을 사용하여 Microsoft 계정 인증 엔드포인트인 `https://login.live.com`과 통합할 수도 있습니다.

## <a name="azure-ad-only-applications"></a>Azure AD 전용 애플리케이션
이 목록에는 Azure AD v1.0 엔드포인트에서 사용하도록 등록된 모든 애플리케이션이 포함되어 있습니다. 이러한 애플리케이션에는 Azure Active Directory에서 회사/학교 계정을 사용하여 사용자를 로그인하는 기능만 있습니다. 이 목록에는 [Azure Portal](https://portal.azure.com)의 **앱 등록** 환경을 사용하여 등록된 애플리케이션이 포함됩니다.

## <a name="live-sdk-applications"></a>Live SDK 애플리케이션
이 목록에는 Microsoft 계정으로만 사용하도록 등록된 모든 애플리케이션이 포함되어 있습니다. Azure Active Directory에는 사용할 수 없습니다. 여기서는 이전에 `https://account.live.com/developers/applications`의 MSA 개발자 포털에 등록된 모든 애플리케이션을 찾을 수 있습니다. 이전에 `https://account.live.com/developers/applications`에서 수행한 모든 함수를 이제 이 새 포털 `https://apps.dev.microsoft.com`에서 수행할 수 있습니다.

## <a name="application-secrets"></a>애플리케이션 비밀
애플리케이션 비밀은 Azure AD에서 애플리케이션에서 신뢰할 수 있는 [클라이언트 인증](https://tools.ietf.org/html/rfc6749#section-2.3)이 되도록 하는 자격 증명입니다. OAuth 및 OpenID Connect에서 애플리케이션 비밀은 일반적으로 `client_secret`라고 합니다. v2.0 프로토콜에서, 웹 주소 지정 가능한 위치에서 `https` 구성표를 사용하여 보안 토큰을 받는 모든 애플리케이션에서는 해당 보안 토큰 상환 시 Azure AD에서 자신을 식별할 수 있도록 애플리케이션 암호를 사용해야 합니다. 또한 장치에서 토큰을 받는 모든 네이티브 클라이언트는 애플리케이션 비밀을 사용하여 클라이언트 인증을 수행할 수 없도록 금지됩니다. 이에 따라 안전하지 않은 환경에서 비밀을 저장할 필요가 없습니다.

각 앱에는 지정된 시점에 유효한 두 개의 애플리케이션 비밀이 포함될 수 있습니다. 두 개의 비밀을 유지하여 애플리케이션의 전체 환경에서 정기적으로 키 롤오버를 수행할 수 있습니다. 애플리케이션 전체를 새 비밀로 마이그레이션하면 이전 비밀을 삭제하고 새 비밀을 프로비전할 수 있습니다.

현재 앱 등록 포털에는 두 가지 유형의 애플리케이션 비밀만 허용됩니다. **새 암호 생성**을 선택하면 애플리케이션에 사용할 수 있는 공유 비밀이 각 데이터 저장소에 생성되어 저장됩니다. **새 키 쌍 생성**을 선택하면 Azure AD에 대한 클라이언트 인증을 위해 다운로드하여 사용할 수 있는 새 공개/개인 키가 만들어집니다. **공개 키 업로드**를 선택하면 자신의 공개/개인 키 쌍을 사용할 수 있습니다.
공개 키가 포함된 인증서를 업로드해야 합니다.

## <a name="profile"></a>프로필
앱 등록 포털의 프로필 섹션을 사용하여 애플리케이션에 대한 로그인 페이지를 사용자 지정할 수 있습니다. 현재 로그인 페이지의 애플리케이션 로고, 서비스 약관 URL 및 개인정보처리방침 URL을 변경할 수 있습니다. 로고는 15KB 이하의 GIF, PNG 또는 JPEG 파일로 된 투명한 48 x 48 또는 50 x 50픽셀 이미지여야 합니다. 값을 변경하고 결과 로그인 페이지를 확인해 보세요!

## <a name="live-sdk-support"></a>Live SDK 지원
"Live SDK 지원"을 사용하도록 설정하면 만든 애플리케이션 암호 모두가 Azure AD 및 Microsoft 계정 데이터 저장소 모두에 프로비전됩니다. 이렇게 하면 애플리케이션을 Microsoft 계정 서비스(login.live.com)와 직접 통합할 수 있습니다. Azure AD v2.0 엔드포인트를 사용하는 것이 아니라 Microsoft 계정을 직접 사용하여 앱을 빌드하려면 Live SDK 지원을 사용하도록 설정되어 있는지 확인해야 합니다.

Live SDK 지원을 사용하지 않도록 설정하면 애플리케이션 비밀만 Azure AD 데이터 저장소에 기록됩니다. Azure AD 데이터 저장소는 FISMA 규정 준수 등의 특정 표준을 충족하는 엔터프라이즈급 규정을 포함합니다. Live SDK 지원을 사용하도록 설정하면 애플리케이션이 이러한 일부 표준을 준수하지 못할 수 있습니다.

Azure AD v2.0 엔드포인트만 사용하려면 안전하게 Live SDK 지원을 사용하지 않도록 설정할 수 있습니다.

