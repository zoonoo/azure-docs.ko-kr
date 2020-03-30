---
title: 인증 및 권한 부여
description: 앱 또는 서비스가 Azure 공간 앵커에 인증할 수 있는 다양한 방법 및 Azure 공간 앵커에 대한 액세스 권한을 게이트화하는 데 필요한 제어 수준에 대해 알아봅니다.
author: julianparismorgan
manager: vriveras
services: azure-spatial-anchors
ms.author: pmorgan
ms.date: 05/28/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: df27a77c202951a6c789703f12712e75bd8b5906
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77656986"
---
# <a name="authentication-and-authorization-to-azure-spatial-anchors"></a>Azure 공간 앵커에 대한 인증 및 권한 부여

이 섹션에서는 앱 또는 웹 서비스에서 Azure 공간 앵커를 인증할 수 있는 다양한 방법과 Azure 디렉터리(Azure AD)에서 역할 기반 액세스 제어를 사용하여 공간 앵커 계정에 대한 액세스를 제어하는 방법에 대해 설명합니다.

## <a name="overview"></a>개요

![Azure 공간 앵커에 대한 인증 개요](./media/spatial-anchors-authentication-overview.png)

지정된 Azure 공간 앵커 계정에 액세스하려면 클라이언트가 먼저 AZURE 혼합 현실 보안 토큰 서비스(STS)에서 액세스 토큰을 가져와야 합니다. STS에서 얻은 토큰은 24시간 동안 라이브로 표시되며, 공간 앵커 서비스에 대한 정보를 포함하여 계정에 대한 권한 부여 결정을 내리고 권한이 부여된 주체만 해당 계정에 액세스할 수 있도록 합니다.

액세스 토큰은 계정 키 또는 Azure AD 발급 토큰에서 교환하여 얻을 수 있습니다.

계정 키를 사용하면 Azure 공간 앵커 서비스를 빠르게 시작할 수 있습니다. 그러나 프로덕션에 응용 프로그램을 배포하기 전에 Azure AD 기반 인증을 사용하도록 앱을 업데이트하는 것이 좋습니다.

Azure AD 인증 토큰은 다음 두 가지 방법으로 얻을 수 있습니다.

- 엔터프라이즈 응용 프로그램을 빌드하는 경우 회사에서 Azure AD를 ID 시스템으로 사용하는 경우 앱에서 사용자 기반 Azure AD 인증을 사용하고 기존 Azure AD 보안 그룹을 사용하여 공간 앵커 계정에 대한 액세스 권한을 부여하거나 조직의 사용자에게 직접 문의할 수 있습니다.
- 그렇지 않으면 앱을 지원하는 웹 서비스에서 Azure AD 토큰을 가져오는 것이 좋습니다. 지원 웹 서비스를 사용하는 것은 클라이언트 응용 프로그램에 Azure 공간 앵커에 액세스하기 위한 자격 증명을 포함하지 않도록 프로덕션 응용 프로그램에 대해 인증하는 데 권장되는 방법입니다.

## <a name="account-keys"></a>계정 키

Azure 공간 앵커 계정에 액세스하기 위해 계정 키를 사용하는 것이 시작하는 가장 간단한 방법입니다. Azure 포털에서 계정 키를 찾을 수 있습니다. 계정으로 이동하여 "키" 탭을 선택합니다.

![Azure 공간 앵커에 대한 인증 개요](../../../includes/media/spatial-anchors-get-started-create-resource/view-account-key.png)


두 개의 키를 사용할 수 있으며, 둘 다 공간 앵커 계정에 액세스하는 데 동시에 유효합니다. 계정에 액세스하는 데 사용하는 키를 정기적으로 업데이트하는 것이 좋습니다. 두 개의 유효한 키가 있으면 가동 중지 시간 없이 이러한 업데이트를 수행할 수 있습니다. 기본 키와 보조 키만 업데이트하면 됩니다.

SDK에는 계정 키로 인증할 수 있는 기본 제공 지원이 있습니다. cloudSession 개체에서 AccountKey 속성을 설정하기만 하면 됩니다.

# <a name="c"></a>[C #](#tab/csharp)

```csharp
this.cloudSession.Configuration.AccountKey = @"MyAccountKey";
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
_cloudSession.configuration.accountKey = @"MyAccountKey";
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.accountKey = "MyAccountKey"
```

# <a name="java"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAccountKey("MyAccountKey");
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AccountKey(R"(MyAccountKey)");
```

# <a name="c-winrt"></a>[C++ 윈RT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccountKey(LR"(MyAccountKey)");
```

---

이 작업이 완료되면 SDK는 액세스 토큰에 대한 계정 키 교환및 앱에 필요한 토큰 캐싱을 처리합니다.

> [!WARNING]
> 빠른 온보딩에는 계정 키를 사용하는 것이 좋지만 개발/프로토타이핑 중에는 사용하는 것이 좋습니다. 응용 프로그램에 포함된 계정 키를 사용하여 프로덕션으로 응용 프로그램을 제공하지 않고 다음에 나열된 사용자 기반 또는 서비스 기반 Azure AD 인증 방법을 사용하는 것이 좋습니다.

## <a name="azure-ad-user-authentication"></a>Azure AD 사용자 인증

Azure Active Directory 사용자를 대상으로 하는 응용 프로그램의 경우 [MSAL 라이브러리를](../../active-directory/develop/msal-overview.md)사용하여 가져올 수 있는 사용자에 대해 Azure AD 토큰을 사용하는 것이 좋습니다. 다음을 포함하는 앱 [빠른 시작 레지스터를](../../active-directory/develop/quickstart-register-app.md)나열한 단계를 따라야 합니다.

1. Azure 포털의 구성
    1.  Azure AD에 응용 프로그램을 **네이티브 응용 프로그램으로**등록합니다. 등록의 일환으로 응용 프로그램이 다중 테넌트인지 여부를 결정하고 응용 프로그램에 허용되는 리디렉션 URL을 제공해야 합니다.
        1.  **API 권한** 탭으로 전환
        2.  **권한 추가** 선택
            1.  **조직에서 사용하는 API** 에서 혼합 현실 리소스 **공급자** 선택 탭
            2.  **위임된 권한** 선택
            3.  **혼합현실.사인인** 에 대한 **체크박스를 선택합니다.**
            4.  **권한 추가** 선택
        3.  **관리자 동의 부여** 선택
    2.  응용 프로그램 또는 사용자에게 리소스에 대한 액세스 권한을 부여합니다.
        1.  Azure 포털의 공간 앵커 리소스로 이동
        2.  **IAM(액세스 제어)** 탭으로 전환
        3.  **역할 할당 추가** 중적 시
            1.  [역할 선택](#role-based-access-control)
            2.  **선택** 필드에 액세스를 할당할 사용자 이름, 그룹(그룹) 및/또는 응용 프로그램(들)을 입력합니다.
            3.  **저장**을 누릅니다.
2. 코드에서 다음을 수행합니다.
    1.  **응용 프로그램 ID를** 사용하고 ADAL에서 **클라이언트 ID** 및 **리디렉션Uri** 매개 변수로 자신의 Azure AD 응용 프로그램의 **Uri를 리디렉션해야** 합니다.
    2.  테넌트 정보 설정:
        1.  응용 프로그램에서 **내 조직만**지원하는 경우 이 값을 **테넌트 ID** 또는 **테넌트** 이름(예: contoso.microsoft.com)으로 바꿉니다.
        2.  응용 프로그램이 **모든 조직 디렉터리에서 계정을**지원하는 경우 이 값을 **조직으로** 바꿉꿉
        3.  응용 프로그램이 **모든 Microsoft 계정 사용자를**지원하는 경우 이 값을 **공통값으로** 바꿉니다.
    3.  토큰 요청에서 **리소스를** ""로https://sts.mixedreality.azure.com설정합니다. 이 "리소스"는 응용 프로그램이 Azure 공간 앵커 서비스에 대한 토큰을 요청하고 있음을 Azure AD에 나타냅니다.

이 경우 응용 프로그램은 MSAL에서 Azure AD 토큰을 가져올 수 있어야 합니다. Azure AD 토큰을 클라우드 세션 구성 개체의 **인증토큰으로** 설정할 수 있습니다.

# <a name="c"></a>[C #](#tab/csharp)

```csharp
this.cloudSession.Configuration.AuthenticationToken = @"MyAuthenticationToken";
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
_cloudSession.configuration.authenticationToken = @"MyAuthenticationToken";
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.authenticationToken = "MyAuthenticationToken"
```

# <a name="java"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAuthenticationToken("MyAuthenticationToken");
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AuthenticationToken(R"(MyAuthenticationToken)");
```

# <a name="c-winrt"></a>[C++ 윈RT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AuthenticationToken(LR"(MyAuthenticationToken)");
```

---

## <a name="azure-ad-service-authentication"></a>Azure AD 서비스 인증

Azure Spatial 앵커를 사용하여 프로덕션에 앱을 배포하는 데 권장되는 옵션은 인증 요청을 중개하는 백 엔드 서비스를 활용하는 것입니다. 일반적인 체계는 이 다이어그램에 설명된 대로 해야 합니다.

![Azure 공간 앵커에 대한 인증 개요](./media/spatial-anchors-aad-authentication.png)

여기서앱이 자체 메커니즘(예: Microsoft 계정, PlayFab, Facebook, Google ID, 사용자 지정 사용자 이름/암호 등)을 사용하여 백 엔드 서비스를 인증하는 것으로 가정합니다. 사용자가 백 엔드 서비스에 인증되면 해당 서비스는 Azure AD 토큰을 검색하고 Azure 공간 앵커에 대한 액세스 토큰으로 교환한 다음 클라이언트 응용 프로그램으로 반환할 수 있습니다.

Azure AD 액세스 토큰은 [MSAL 라이브러리를](../../active-directory/develop/msal-overview.md)사용하여 검색됩니다. 다음을 포함하는 앱 [빠른 시작 레지스터를](../../active-directory/develop/quickstart-register-app.md)나열한 단계를 따라야 합니다.

1.  Azure 포털의 구성:
    1.  Azure AD에 응용 프로그램을 등록합니다.
        1.  Azure 포털에서 **Azure Active Directory로**이동하여 **앱 등록을 선택합니다.**
        2.  **새 응용 프로그램 등록** 선택
        3.  응용 프로그램 이름을 입력하고 **웹 앱/API를** 응용 프로그램 유형으로 선택하고 서비스에 대한 인증 URL을 입력합니다. 그런 다음 **만들기를**누르고 있습니다.
        4.  해당 응용 프로그램에서 **설정을**누르고 **키** 탭을 선택합니다. 키 이름을 입력하고 지속 시간을 선택하고 **저장을**누르십시오. 웹 서비스의 코드에 포함해야 하므로 해당 시점에 표시되는 키 값을 저장해야 합니다.
    2.  응용 프로그램 및/또는 사용자에게 리소스에 대한 액세스 권한을 부여합니다.
        1.  Azure 포털의 공간 앵커 리소스로 이동
        2.  **IAM(액세스 제어)** 탭으로 전환
        3.  **역할 할당 추가** 중적 시
        1.  [역할 선택](#role-based-access-control)
        2.  **선택** 필드에 만든 응용 프로그램과 액세스를 할당할 응용 프로그램의 이름을 입력합니다. 앱 사용자가 공간 앵커 계정에 대해 서로 다른 역할을 하도록 하려면 Azure AD에 여러 응용 프로그램을 등록하고 각 역할에 별도의 역할을 할당해야 합니다. 그런 다음 권한 부여 논리를 구현하여 사용자에게 적합한 역할을 사용합니다.
    3.  **저장**을 누릅니다.
2.  코드에서(참고: GitHub에 포함된 서비스 샘플을 사용할 수 있음):
    1.  응용 프로그램 ID, 응용 프로그램 보안 및 클라이언트 ID, 비밀 및 RedirectUri 매개 변수로 자신의 Azure AD 응용 프로그램의 Uri를 리디렉션해야 합니다.
    2.  ADAL의 기관 매개 변수에서 테넌트 ID를 고유한 AAAzure ADD 테넌트 ID로 설정합니다.
    3.  토큰 요청에 **대해 리소스를** "https://sts.mixedreality.azure.com" 로 설정합니다.

이를 통해 백 엔드 서비스는 Azure AD 토큰을 검색할 수 있습니다. 그런 다음 MR 토큰으로 교환하여 클라이언트로 다시 돌아갈 수 있습니다. Azure AD 토큰을 사용하여 MR 토큰을 검색하는 작업은 REST 호출을 통해 수행됩니다. 다음은 샘플 호출입니다.

```
GET https://mrc-auth-prod.trafficmanager.net/Accounts/35d830cb-f062-4062-9792-d6316039df56/token HTTP/1.1
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1Ni<truncated>FL8Hq5aaOqZQnJr1koaQ
Host: mrc-auth-prod.trafficmanager.net
Connection: Keep-Alive

HTTP/1.1 200 OK
Date: Sun, 24 Feb 2019 08:00:00 GMT
Content-Type: application/json; charset=utf-8
Content-Length: 1153
Accept: application/json
MS-CV: 05JLqWeKFkWpbdY944yl7A.0
{"AccessToken":"eyJhbGciOiJSUzI1NiIsImtpZCI6IjI2MzYyMTk5ZTI2NjQxOGU4ZjE3MThlM2IyMThjZTIxIiwidHlwIjoiSldUIn0.eyJqdGkiOiJmMGFiNWIyMy0wMmUxLTQ1MTQtOWEzNC0xNzkzMTA1NTc4NzAiLCJjYWkiOiIzNWQ4MzBjYi1mMDYyLTQwNjItOTc5Mi1kNjMxNjAzOWRmNTYiLCJ0aWQiOiIwMDAwMDAwMC0wMDAwLTAwMDAtMDAwMC0wMDAwMDAwMDAwMDAiLCJhaWQiOiIzNWQ4MzBjYi1mMDYyLTQwNjItOTc5Mi1kNjMxNjAzOWRmNTYiLCJhYW8iOi0xLCJhcHIiOiJlYXN0dXMyIiwicmlkIjoiL3N1YnNjcmlwdGlvbnMvNzIzOTdlN2EtNzA4NC00ODJhLTg3MzktNjM5Y2RmNTMxNTI0L3Jlc291cmNlR3JvdXBzL3NhbXBsZV9yZXNvdXJjZV9ncm91cC9wcm92aWRlcnMvTWljcm9zb2Z0Lk1peGVkUmVhbGl0eS9TcGF0aWFsQW5jaG9yc0FjY291bnRzL2RlbW9fYWNjb3VudCIsIm5iZiI6MTU0NDU0NzkwMywiZXhwIjoxNTQ0NjM0MzAzLCJpYXQiOjE1NDQ1NDc5MDMsImlzcyI6Imh0dHBzOi8vbXJjLWF1dGgtcHJvZC50cmFmZmljbWFuYWdlci5uZXQvIiwiYXVkIjoiaHR0cHM6Ly9tcmMtYW5jaG9yLXByb2QudHJhZmZpY21hbmFnZXIubmV0LyJ9.BFdyCX9UJj0i4W3OudmNUiuaGgVrlPasNM-5VqXdNAExD8acFJnHdvSf6uLiVvPiQwY1atYyPbOnLYhEbIcxNX-YAfZ-xyxCKYb3g_dbxU2w8nX3zDz_X3XqLL8Uha-rkapKbnNgxq4GjM-EBMCill2Svluf9crDmO-SmJbxqIaWzLmlUufQMWg_r8JG7RLseK6ntUDRyDgkF4ex515l2RWqQx7cw874raKgUO4qlx0cpBAB8cRtGHC-3fA7rZPM7UQQpm-BC3suXqRgROTzrKqfn_g-qTW4jAKBIXYG7iDefV2rGMRgem06YH_bDnpkgUa1UgJRRTckkBuLkO2FvA"}
```

권한 부여 헤더의 서식이 다음과 같이 서식이 지정되는 위치:`Bearer <accoundId>:<accountKey>`

그리고 응답에는 MR 토큰이 일반 텍스트로 포함되어 있습니다.

그런 다음 MR 토큰이 클라이언트에 반환됩니다. 그런 다음 클라이언트 앱은 클라우드 세션 구성에서 액세스 토큰으로 설정할 수 있습니다.

# <a name="c"></a>[C #](#tab/csharp)

```csharp
this.cloudSession.Configuration.AccessToken = @"MyAccessToken";
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
_cloudSession.configuration.accessToken = @"MyAccessToken";
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.accessToken = "MyAccessToken"
```

# <a name="java"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAccessToken("MyAccessToken");
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AccessToken(R"(MyAccessToken)");
```

# <a name="c-winrt"></a>[C++ 윈RT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccessToken(LR"(MyAccessToken)");
```

---

## <a name="role-based-access-control"></a>역할 기반 액세스 제어

서비스의 응용 프로그램, 서비스 또는 Azure AD 사용자에게 부여된 액세스 수준을 제어하기 위해 Azure 공간 앵커 계정에 대해 필요에 따라 할당할 수 있도록 다음 역할이 만들어졌습니다.

- **공간 앵커 계정 소유자**: 이 역할을 가진 응용 프로그램 또는 사용자는 공간 앵커를 만들고 쿼리하고 삭제할 수 있습니다. 계정 키를 사용하여 계정에 인증하면 **공간 앵커 계정 소유자** 역할이 인증된 보안 주체에 할당됩니다.
- **공간 앵커 계정 기여자**: 이 역할을 가진 응용 프로그램 이나 사용자는 공간 앵커를 만들고 쿼리할 수 있지만 삭제할 수는 없습니다.
- **공간 앵커 계정 리더**: 이 역할을 가진 응용 프로그램 이나 사용자는 공간 앵커에 대해서만 쿼리할 수 있지만 새 앵커를 만들거나 기존 앵커를 삭제하거나 공간 앵커에서 메타데이터를 업데이트할 수는 없습니다. 일반적으로 일부 사용자가 환경을 큐레이트하는 응용 프로그램에 사용되며 다른 사용자는 해당 환경에 이전에 배치된 앵커만 회수할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure 공간 앵커를 사용하여 첫 번째 앱을 만듭니다.

> [!div class="nextstepaction"]
> [유니티 (홀로렌즈)](../quickstarts/get-started-unity-hololens.md)

> [!div class="nextstepaction"]
> [유니티 (iOS)](../quickstarts/get-started-unity-ios.md)

> [!div class="nextstepaction"]
> [유니티 (안드로이드)](../quickstarts/get-started-unity-android.md)

> [!div class="nextstepaction"]
> [iOS](../quickstarts/get-started-ios.md)

> [!div class="nextstepaction"]
> [Android](../quickstarts/get-started-android.md)

> [!div class="nextstepaction"]
> [홀로 렌즈](../quickstarts/get-started-hololens.md)

> [!div class="nextstepaction"]
> [자마린 (안드로이드)](../quickstarts/get-started-xamarin-android.md)

> [!div class="nextstepaction"]
> [자마린 (아이폰 OS)](../quickstarts/get-started-xamarin-ios.md)
