---
title: 인증 및 권한 부여
description: 앱 또는 서비스에서 Azure 공간 앵커에 대해 인증할 수 있는 다양 한 방법과 공간 앵커에 대 한 액세스를 게이트 하는 데 사용 하는 제어 수준에 대해 알아봅니다.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.custom: devx-track-csharp
ms.openlocfilehash: 0166a3b6031f9e1d364a37db99be5bc5a65267df
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2020
ms.locfileid: "95484613"
---
# <a name="authentication-and-authorization-to-azure-spatial-anchors"></a>Azure Spatial Anchors에 대한 인증 및 권한 부여

이 문서에서는 앱 또는 웹 서비스에서 Azure 공간 앵커로 인증할 수 있는 다양 한 방법을 알아봅니다. Azure AD (역할 기반 액세스 제어) Azure Active Directory에서 azure RBAC (역할 기반 액세스 제어)를 사용 하 여 공간 앵커 계정에 대 한 액세스를 제어 하는 방법에 대해서도 알아봅니다.

## <a name="overview"></a>개요

![Azure 공간 앵커로의 인증에 대 한 개요를 보여 주는 다이어그램입니다.](./media/spatial-anchors-authentication-overview.png)

지정된 Azure Spatial Anchors 계정에 액세스하려면 클라이언트는 먼저 Azure Mixed Reality STS(보안 토큰 서비스)에서 액세스 토큰을 가져와야 합니다. STS에서 가져온 토큰의 수명은 24 시간입니다. 여기에는 계정에 대 한 권한 부여 결정을 내리는 데 사용 되는 공간 앵커 서비스와 권한이 부여 된 보안 주체가 계정에 액세스할 수 있는 정보가 포함 됩니다.

계정 키 또는 Azure AD에서 발급 한 토큰에 대해 exchange에서 액세스 토큰을 가져올 수 있습니다.

계정 키를 사용 하면 Azure 공간 앵커 서비스를 사용 하 여 빠르게 시작할 수 있습니다. 하지만 응용 프로그램을 프로덕션 환경에 배포 하기 전에 Azure AD 인증을 사용 하도록 앱을 업데이트 하는 것이 좋습니다.

다음 두 가지 방법으로 Azure AD 인증 토큰을 얻을 수 있습니다.

- 엔터프라이즈 응용 프로그램을 빌드하고 회사에서 id 시스템으로 Azure AD를 사용 하는 경우 앱에서 사용자 기반 Azure AD 인증을 사용할 수 있습니다. 그런 다음 기존 Azure AD 보안 그룹을 사용 하 여 공간 앵커 계정에 대 한 액세스 권한을 부여 합니다. 조직의 사용자에 게 직접 액세스 권한을 부여할 수도 있습니다.
- 그렇지 않으면 앱을 지 원하는 웹 서비스에서 Azure AD 토큰을 가져오는 것이 좋습니다. 클라이언트 응용 프로그램에서 Azure 공간 앵커에 액세스 하기 위한 자격 증명을 포함 하지 않도록 하기 때문에 프로덕션 응용 프로그램에이 방법을 사용 하는 것이 좋습니다.

## <a name="account-keys"></a>계정 키

시작 하는 가장 쉬운 방법은 Azure 공간 앵커 계정에 액세스 하기 위해 계정 키를 사용 하는 것입니다. Azure Portal에서 계정 키를 가져올 수 있습니다. 계정으로 이동 하 고 **키** 탭을 선택 합니다.

![강조 표시 된 기본 키에 대 한 복사 단추가 있는 키 탭을 보여 주는 스크린샷](../../../includes/media/spatial-anchors-get-started-create-resource/view-account-key.png)

두 개의 키를 사용할 수 있습니다. 둘 다 공간 앵커 계정에 대 한 액세스에 동시에 유효 합니다. 계정에 액세스 하는 데 사용 하는 키를 정기적으로 업데이트 하는 것이 좋습니다. 유효 키가 두 개인 경우 이러한 업데이트를 가동 중지 시간 없이 수행할 수 있습니다. 기본 키와 보조 키만 업데이트 하면 됩니다.

SDK는 계정 키를 통해 인증을 기본적으로 지원 합니다. 개체의 속성을 설정 하기만 하면 됩니다 `AccountKey` `cloudSession` .

# <a name="c"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AccountKey = @"MyAccountKey";
```

# <a name="objective-c"></a>[Objective-C](#tab/objc)

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

# <a name="cwinrt"></a>[C++/WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccountKey(LR"(MyAccountKey)");
```

---

해당 속성을 설정한 후 SDK는 액세스 토큰에 대 한 계정 키의 교환과 앱에 필요한 토큰의 캐싱을 처리 합니다.

> [!WARNING]
> 빠른 등록을 위해 계정 키를 사용 하는 것이 좋지만, 개발/프로토타입 중에만 사용 하는 것이 좋습니다. 포함 된 계정 키를 사용 하 여 프로덕션 환경에 응용 프로그램을 제공 하지 않는 것이 좋습니다. 대신, 다음에 설명 된 사용자 기반 또는 서비스 기반 Azure AD 인증 방법을 사용 합니다.

## <a name="azure-ad-user-authentication"></a>Azure AD 사용자 인증

Azure Active Directory 사용자를 대상으로 하는 응용 프로그램의 경우 사용자에 대 한 Azure AD 토큰을 사용 하는 것이 좋습니다. [Msal](../../active-directory/develop/msal-overview.md)을 사용 하 여이 토큰을 가져올 수 있습니다. [앱 등록에 대 한 빠른](../../active-directory/develop/quickstart-register-app.md)시작의 단계를 수행 합니다. 여기에는 다음이 포함 됩니다.

**Azure Portal에서**
1.    응용 프로그램을 네이티브 응용 프로그램으로 Azure AD에 등록 합니다. 등록 하는 과정에서 응용 프로그램이 다중 테 넌 트 되어야 하는지 여부를 확인 해야 합니다. 응용 프로그램에 허용 되는 리디렉션 Url도 제공 해야 합니다.
1.  **API 사용 권한** 탭으로 이동 합니다.
2.  **사용 권한 추가** 를 선택합니다.
    1.  내 조직에서 탭을 **사용 하는 api** 에서 **혼합 현실 리소스 공급자** 를 선택 합니다.
    2.  **위임된 권한** 을 선택합니다.
    3.  **Mixedreality** 아래에서 **mixedreality을** 선택 합니다.
    4.  **권한 추가** 를 선택합니다.
3.  **관리자 동의 부여** 를 선택 합니다.

2. 리소스에 애플리케이션 또는 사용자 액세스 권한을 부여합니다.
   1.    Azure Portal의 공간 앵커 리소스로 이동 합니다.
   2.    **액세스 제어 (IAM)** 탭으로 이동 합니다.
   3.    **역할 할당 추가** 를 선택합니다.
   1.    [역할을 선택](#azure-role-based-access-control)합니다.
   2.    **선택** 상자에서 액세스 권한을 할당 하려는 사용자, 그룹 및/또는 응용 프로그램의 이름을 입력 합니다.
   3.    **저장** 을 선택합니다.

**코드에서**
1.    MSAL의 **클라이언트 ID** 및 **redirecturi** 매개 변수에 대 한 사용자 고유의 Azure AD 응용 프로그램의 응용 프로그램 ID 및 리디렉션 uri를 사용 해야 합니다.
2.    테넌트 정보 설정:
        1.    응용 프로그램에서 **내 조직만** 지 원하는 경우이 값을 **테 넌 트 ID** 또는 **테 넌 트 이름** 으로 바꿉니다. 예를 들면 contoso.microsoft.com입니다.
        2.    응용 프로그램에서 **조직 디렉터리의 계정을** 지 원하는 경우이 값을 **조직** 으로 바꿉니다.
        3.    응용 프로그램에서 **모든 Microsoft 계정 사용자** 를 지 원하는 경우이 값을 **일반** 값으로 바꿉니다.
3.    토큰 요청에서 **범위** 를 **" `https://sts.<account-domain>//.default` "** 로 설정 합니다 `<account-domain>` . 여기서는 Azure 공간 앵커 계정에 대 한 **계정 도메인** 으로 바뀝니다. 미국 동부 2 계정 도메인의 Azure 공간 앵커 계정에 대 한 예제 범위는 **" `https://sts.mixedreality.azure.com//.default` "** 입니다. 이 범위는 애플리케이션이 Mixed Reality STS(보안 토큰 서비스)에 대한 토큰을 요청하고 있음을 Azure AD에 나타냅니다.

이러한 단계를 완료 한 후 응용 프로그램은 MSAL에서 Azure AD 토큰을 얻을 수 있어야 합니다. Azure AD 토큰을 `authenticationToken` 클라우드 세션 구성 개체에서로 설정할 수 있습니다.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AuthenticationToken = @"MyAuthenticationToken";
```

# <a name="objective-c"></a>[Objective-C](#tab/objc)

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

# <a name="cwinrt"></a>[C++/WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AuthenticationToken(LR"(MyAuthenticationToken)");
```

---

## <a name="azure-ad-service-authentication"></a>Azure AD 서비스 인증

Azure 공간 앵커를 사용 하는 앱을 프로덕션에 배포 하려면 인증 요청을 브로커 하는 백 엔드 서비스를 사용 하는 것이 좋습니다. 프로세스에 대 한 개요는 다음과 같습니다.

![Azure 공간 앵커로의 인증에 대 한 개요를 제공 하는 다이어그램입니다.](./media/spatial-anchors-aad-authentication.png)

여기서는 앱이 자체 메커니즘을 사용 하 여 백 엔드 서비스를 인증 하는 것으로 가정 합니다. 예를 들어 Microsoft 계정, PlayFab, Facebook, Google ID, 사용자 지정 사용자 이름 및 암호 등이 있습니다.  사용자를 백 엔드 서비스에 인증 한 후에는 해당 서비스에서 Azure AD 토큰을 검색 하 고, Azure 공간 앵커에 대 한 액세스 토큰을 교환 하 고, 클라이언트 응용 프로그램에 다시 반환할 수 있습니다.

Azure AD 액세스 토큰은 [Msal](../../active-directory/develop/msal-overview.md)을 통해 검색 됩니다. [앱 등록 빠른](../../active-directory/develop/quickstart-register-app.md)시작의 단계를 따릅니다. 여기에는 다음이 포함 됩니다.

**Azure Portal에서**
1.    Azure AD에 애플리케이션을 등록합니다.
        1.    Azure Portal에서 **Azure Active Directory** 를 선택 하 고 **앱 등록** 를 선택 합니다.
        2.    **새 등록** 을 선택합니다.
        3.    애플리케이션 이름을 입력하고 애플리케이션 유형으로 **웹앱/API** 를 선택한 후 서비스의 인증 URL을 입력합니다. **만들기** 를 선택합니다.
2.    응용 프로그램에서 **설정** 을 선택한 다음 **인증서 및 암호** 탭을 선택 합니다. 새 클라이언트 암호를 만들고 기간을 선택한 다음 **추가** 를 선택 합니다. 비밀 값을 저장 해야 합니다. 웹 서비스의 코드에 포함 해야 합니다.
3.    애플리케이션 및/또는 사용자에게 리소스에 대한 액세스 권한을 부여합니다.
        1.    Azure Portal의 공간 앵커 리소스로 이동 합니다.
        2.    **액세스 제어 (IAM)** 탭으로 이동 합니다.
        3.    **역할 할당 추가** 를 선택합니다.
        4.    [역할을 선택](#azure-role-based-access-control)합니다.
        5.    **선택** 상자에 액세스 권한을 할당 하려는 응용 프로그램의 이름 또는 이름을 입력 합니다. 앱의 사용자에 게 공간 앵커 계정에 대해 다른 역할을 지정 하려면 Azure AD에 여러 응용 프로그램을 등록 하 고 각 응용 프로그램에 별도의 역할을 할당 합니다. 그런 다음 사용자에게 올바른 역할을 사용하도록 권한 부여 논리를 구현합니다.

              > [!NOTE]
              > **역할 할당 추가** 창의에 대 한 **액세스 할당** 에서 **Azure AD 사용자, 그룹 또는 서비스 주체** 를 선택 합니다.

        6.    **저장** 을 선택합니다.

**코드에서**

>[!NOTE]
> GitHub에서 사용할 수 있는 서비스 샘플을 사용할 수 있습니다.

1.    사용자 고유의 Azure AD 응용 프로그램의 응용 프로그램 ID, 응용 프로그램 암호 및 리디렉션 URI를 MSAL의 **클라이언트 ID**, **비밀** 및 **redirecturi** 매개 변수로 사용 해야 합니다.
2.    MSAL의 **authority** 매개 변수에서 테 넌 트 id를 자신의 Azure AD 테 넌 트 id로 설정 합니다.
3.    토큰 요청에서 **범위** 를 **" `https://sts.<account-domain>//.default` "** 로 설정 합니다 `<account-domain>` . 여기서는 Azure 공간 앵커 계정에 대 한 **계정 도메인** 으로 바뀝니다. 미국 동부 2 계정 도메인의 Azure 공간 앵커 계정에 대 한 예제 범위는 **" `https://sts.mixedreality.azure.com//.default` "** 입니다.

이러한 단계를 완료 한 후 백 엔드 서비스에서 Azure AD 토큰을 검색할 수 있습니다. 그런 다음 클라이언트로 다시 반환할 MR 토큰으로 교환할 수 있습니다. Azure AD 토큰을 사용한 MR 토큰 검색은 REST 호출을 통해 수행됩니다. 샘플 호출은 다음과 같습니다.

```
GET https://sts.mixedreality.azure.com/Accounts/35d830cb-f062-4062-9792-d6316039df56/token HTTP/1.1
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1Ni<truncated>FL8Hq5aaOqZQnJr1koaQ
Host: sts.mixedreality.azure.com
Connection: Keep-Alive

HTTP/1.1 200 OK
Date: Sun, 24 Feb 2019 08:00:00 GMT
Content-Type: application/json; charset=utf-8
Content-Length: 1153
Accept: application/json
MS-CV: 05JLqWeKFkWpbdY944yl7A.0
{"AccessToken":"eyJhbGciOiJSUzI1NiIsImtpZCI6IjI2MzYyMTk5ZTI2NjQxOGU4ZjE3MThlM2IyMThjZTIxIiwidHlwIjoiSldUIn0.eyJqdGkiOiJmMGFiNWIyMy0wMmUxLTQ1MTQtOWEzNC0xNzkzMTA1NTc4NzAiLCJjYWkiOiIzNWQ4MzBjYi1mMDYyLTQwNjItOTc5Mi1kNjMxNjAzOWRmNTYiLCJ0aWQiOiIwMDAwMDAwMC0wMDAwLTAwMDAtMDAwMC0wMDAwMDAwMDAwMDAiLCJhaWQiOiIzNWQ4MzBjYi1mMDYyLTQwNjItOTc5Mi1kNjMxNjAzOWRmNTYiLCJhYW8iOi0xLCJhcHIiOiJlYXN0dXMyIiwicmlkIjoiL3N1YnNjcmlwdGlvbnMvNzIzOTdlN2EtNzA4NC00ODJhLTg3MzktNjM5Y2RmNTMxNTI0L3Jlc291cmNlR3JvdXBzL3NhbXBsZV9yZXNvdXJjZV9ncm91cC9wcm92aWRlcnMvTWljcm9zb2Z0Lk1peGVkUmVhbGl0eS9TcGF0aWFsQW5jaG9yc0FjY291bnRzL2RlbW9fYWNjb3VudCIsIm5iZiI6MTU0NDU0NzkwMywiZXhwIjoxNTQ0NjM0MzAzLCJpYXQiOjE1NDQ1NDc5MDMsImlzcyI6Imh0dHBzOi8vbXJjLWF1dGgtcHJvZC50cmFmZmljbWFuYWdlci5uZXQvIiwiYXVkIjoiaHR0cHM6Ly9tcmMtYW5jaG9yLXByb2QudHJhZmZpY21hbmFnZXIubmV0LyJ9.BFdyCX9UJj0i4W3OudmNUiuaGgVrlPasNM-5VqXdNAExD8acFJnHdvSf6uLiVvPiQwY1atYyPbOnLYhEbIcxNX-YAfZ-xyxCKYb3g_dbxU2w8nX3zDz_X3XqLL8Uha-rkapKbnNgxq4GjM-EBMCill2Svluf9crDmO-SmJbxqIaWzLmlUufQMWg_r8JG7RLseK6ntUDRyDgkF4ex515l2RWqQx7cw874raKgUO4qlx0cpBAB8cRtGHC-3fA7rZPM7UQQpm-BC3suXqRgROTzrKqfn_g-qTW4jAKBIXYG7iDefV2rGMRgem06YH_bDnpkgUa1UgJRRTckkBuLkO2FvA"}
```

권한 부여 헤더의 형식은 다음과 같습니다. `Bearer <Azure_AD_token>`

응답에는 일반 텍스트의 MR 토큰이 포함 되어 있습니다.

그러면 MR 토큰이 클라이언트로 반환됩니다. 그런 다음 클라이언트 앱에서 클라우드 세션 구성의 액세스 토큰으로 설정할 수 있습니다.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AccessToken = @"MyAccessToken";
```

# <a name="objective-c"></a>[Objective-C](#tab/objc)

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

# <a name="cwinrt"></a>[C++/WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccessToken(LR"(MyAccessToken)");
```

---

## <a name="azure-role-based-access-control"></a>Azure 역할 기반 액세스 제어

서비스의 응용 프로그램, 서비스 또는 Azure AD 사용자에 게 부여 되는 액세스 수준을 제어할 수 있도록 Azure 공간 앵커 계정에 필요에 따라 다음과 같은 기존 역할을 할당할 수 있습니다.

- **공간 앵커 계정 소유자** 입니다. 이 역할을 가진 응용 프로그램이 나 사용자는 공간 앵커를 만들고,이를 쿼리하고, 삭제할 수 있습니다. 계정 키를 사용 하 여 계정에 인증 하는 경우 공간 앵커 계정 소유자 역할이 인증 된 보안 주체에 할당 됩니다.
- **공간 앵커 계정 기여자** 입니다. 이 역할을 가진 응용 프로그램이 나 사용자는 공간 앵커를 만들고 해당 앵커를 쿼리할 수 있지만 삭제할 수는 없습니다.
- **공간 앵커 계정 판독기** 입니다. 이 역할이 있는 응용 프로그램 또는 사용자는 공간 앵커에 대해서만 쿼리할 수 있습니다. 새 템플릿을 만들거나 기존 항목을 삭제 하거나 메타 데이터를 업데이트할 수 없습니다. 이 역할은 일반적으로 일부 사용자가 환경을 저장 하는 응용 프로그램에 사용 되며, 다른 사용자는 이전에 환경에 배치 된 앵커로 회수할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure 공간 앵커를 사용 하 여 첫 번째 앱 만들기:

> [!div class="nextstepaction"]
> [Unity(HoloLens)](../quickstarts/get-started-unity-hololens.md)

> [!div class="nextstepaction"]
> [Unity(iOS)](../quickstarts/get-started-unity-ios.md)

> [!div class="nextstepaction"]
> [Unity(Android)](../quickstarts/get-started-unity-android.md)

> [!div class="nextstepaction"]
> [iOS](../quickstarts/get-started-ios.md)

> [!div class="nextstepaction"]
> [Android](../quickstarts/get-started-android.md)

> [!div class="nextstepaction"]
> [HoloLens](../quickstarts/get-started-hololens.md)

> [!div class="nextstepaction"]
> [Xamarin(Android)](../quickstarts/get-started-xamarin-android.md)

> [!div class="nextstepaction"]
> [Xamarin(iOS)](../quickstarts/get-started-xamarin-ios.md)
