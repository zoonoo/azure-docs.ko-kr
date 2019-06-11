---
title: 자습서 - 애플리케이션 등록 - Azure Active Directory B2C | Microsoft Docs
description: Azure Portal를 사용하여 Azure Active Directory B2C에서 웹 애플리케이션을 등록하는 방법을 알아봅니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 02/05/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 380fc1633f94f2365162c1a4e4087c9113e5f663
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66511936"
---
# <a name="tutorial-register-an-application-in-azure-active-directory-b2c"></a>자습서: Azure Active Directory B2C에서 애플리케이션 등록

[애플리케이션](active-directory-b2c-apps.md)이 Azure AD(Azure Active Directory) B2C와 상호 작용하려면 먼저 사용자가 관리하는 테넌트에 등록되어야 합니다. 이 자습서에서는 Azure Portal을 사용하여 웹 애플리케이션을 등록하는 방법을 보여줍니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * 웹 애플리케이션 등록
> * 클라이언트 비밀 만들기

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>필수 조건

아직 고유한 [Azure AD B2C 테넌트](tutorial-create-tenant.md)를 만들지 않았다면 지금 만듭니다. 기존 Azure AD B2C 테넌트를 사용해도 됩니다.

## <a name="register-a-web-application"></a>웹 애플리케이션 등록

1. Azure AD B2C 테넌트를 포함하는 디렉터리를 사용하려면 위쪽 메뉴에서 **디렉터리 및 구독 필터**를 클릭하고 테넌트가 포함된 디렉터리를 선택합니다.
2. Azure Portal의 왼쪽 위에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
3. **응용 프로그램**을 선택하고 **추가**를 선택합니다.
4. 애플리케이션의 이름을 입력합니다. 예를 들어 *webapp1*과 같습니다.
5. **웹앱/웹 API 포함** 및 **암시적 흐름 허용**에 **예**를 선택합니다.
6. **회신 URL**에는 Azure AD B2C에서 애플리케이션이 요청한 토큰을 반환하는 엔드포인트를 입력합니다. 예를 들어, `https://localhost:44316`에서 로컬로 수신하도록 설정할 수 있습니다. 포트 번호를 아직 모르는 경우 자리 표시자 값을 입력한 후 나중에 변경할 수 있습니다. 테스트 목적을 위해 조사할 토큰 내용을 표시하도록 `https://jwt.ms`로 설정할 수 있습니다. 이 자습서에서는 `https://jwt.ms`로 설정합니다. 

    회신 URL은 스키마 `https`로 시작해야 하고 모든 회신 URL 값은 단일 DNS 도메인을 공유해야 합니다. 예를 들어 애플리케이션의 회신 URL이 `https://login.contoso.com`이면 이 URL `https://login.contoso.com/new`와 같이 추가할 수 있습니다. 또는 `https://new.login.contoso.com`과 같이 `login.contoso.com`의 DNS 하위 도메인을 참조할 수 있습니다. `login-east.contoso.com` 및 `login-west.contoso.com`을 회신 URL로 사용하는 애플리케이션이 필요하면 회신 URL을 다음 순서로 추가해야 합니다. `https://contoso.com`, `https://login-east.contoso.com`, `https://login-west.contoso.com` 뒤의 두 개는 첫 번째 회신 URL `contoso.com`의 하위 도메인이므로 추가할 수 있습니다.

7. **만들기**를 클릭합니다.

## <a name="create-a-client-secret"></a>클라이언트 비밀 만들기

응용 프로그램 토큰에 대 한 코드를 교환 하는 경우 응용 프로그램 비밀을 만들려고 해야 합니다.

1. **키**를 선택한 다음, **키 생성**을 클릭합니다.
2. **저장**을 선택하여 키를 확인합니다. **앱 키** 값을 기록해 둡니다. 이 값을 애플리케이션의 코드에서 애플리케이션 비밀로 사용합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 웹 애플리케이션 등록
> * 클라이언트 비밀 만들기

> [!div class="nextstepaction"]
> [Azure Active Directory B2C에서 사용자 흐름 만들기](tutorial-create-user-flows.md)
