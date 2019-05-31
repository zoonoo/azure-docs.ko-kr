---
title: 인증 및 Azure 공간 앵커에 대 한 권한 부여 | Microsoft Docs
description: Azure 공간 앵커를 Azure 공간 앵커에 액세스를 제어 해야 하는 제어의 수준을 앱 또는 서비스를 인증할 수 있는 다양 한 방법을 알아봅니다.
author: julianparismorgan
manager: vriveras
services: azure-spatial-anchors
ms.author: pmorgan
ms.date: 05/28/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: e641025d49dd42125aa692925c0697235489b1db
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66307154"
---
# <a name="authentication-and-authorization-to-azure-spatial-anchors"></a>인증 및 Azure 공간 앵커에 대 한 권한 부여

이 섹션에서는 Azure Directory (Azure AD)에서 역할 기반 Access Control을 사용할 수 있는 방법 및 앱 또는 웹 서비스에서 Azure 공간 앵커에 인증할 수 있습니다 하는 다양 한 방법을 공간 앵커 계정에 대 한 액세스 제어를 설명 합니다.  

## <a name="overview"></a>개요

![Azure 공간 앵커 인증 개요](./media/spatial-anchors-authentication-overview.png)

지정된 된 공간 앵커 Azure 계정에 액세스 하려면 클라이언트를 먼저 액세스 토큰을 가져올 Azure 혼합 현실 보안 토큰 서비스 (STS에서) 해야 합니다. STS에서 가져온 토큰 24 시간 동안 live 및 계정에 대 한 권한 부여 결정 및 권한이 있는 사용자만 해당 계정에 액세스할 수 있도록 공간 앵커 서비스에 대 한 정보를 포함 합니다. 

Azure AD에서 발급 한 토큰 또는 계정 키 중 하나에서 액세스 토큰을 exchange에서 얻을 수 있습니다. 

계정 키를 사용 하면 Azure 공간 앵커 서비스를 사용 하 여 신속 하 게 시작 하려면 그러나 프로덕션 환경에 응용 프로그램을 배포 하기 전에 Azure AD 기반 인증을 사용 하도록 앱을 업데이트 하는 것이 좋습니다. 

Azure AD 인증 토큰에는 두 가지 방법으로 얻을 수 있습니다.

- Azure 사용자 기반 엔터프라이즈 응용 프로그램을 작성 하는 경우 회사가 Azure AD를 사용 하는 해당 id 시스템으로 사용할 수 있습니다, 앱과 기존 Azure AD 보안 그룹을 사용 하 여 공간 앵커 계정에 액세스 권한 부여에 AD 인증 또는 직접 조직의 사용자에에서 게 프로그램입니다. 
- 그렇지 않으면 앱을 지 원하는 웹 서비스에서 Azure AD 토큰을 가져올 수 하는 것이 좋습니다. 지원 웹 서비스를 사용 하는 클라이언트 응용 프로그램의 Azure 공간 앵커에 액세스 하기 위한 자격 증명 포함 방지 하므로 프로덕션 응용 프로그램에 대 한 인증의 권장 되는 메서드입니다. 

## <a name="account-keys"></a>계정 키

공간 앵커 Azure 계정에 대 한 액세스에 대 한 계정 키를 사용 하 여 시작 하는 가장 간단한 방법은 됩니다. Azure portal에서 계정 키를 찾을 수 있습니다. 계정으로 이동한 다음 "키" 탭을 선택 합니다.

![Azure 공간 앵커 인증 개요](../../../includes/media/spatial-anchors-get-started-create-resource/view-account-key.png)


두 키를 사용할 수 있습니다, 둘 동시에 유효한 액세스에 대 한 공간 앵커 계정. 사용 하 여 계정의 액세스 키를 정기적으로 업데이트 하는 것이 좋습니다. 이러한 업데이트는 가동 중지 시간; 없이 올바른 키 사용을 별도 두 개의 기본 키 및 보조 키 또는 업데이트 해야 합니다. 

SDK는 기본 제공 계정 키를 사용 하 여 인증 단순히 해야 cloudSession 개체 AccountKey 속성을 설정 합니다. 

```csharp
this.cloudSession.Configuration.AccountKey = @"MyAccountKey";
```

완료 되 면 SDK는 액세스 토큰에 대 한 계정 키를 교환 하 고 앱에 대 한 토큰의 필요한 캐싱을 처리할 수 있습니다. 

> [!WARNING] 
> 빠른 온 보 딩, 있지만 개발/프로토타입만 하는 동안에 계정 키를 사용할 것이 좋습니다. 이 가장 좋습니다 그 안에 포함 된 계정 키를 사용 하 여 프로덕션 응용 프로그램 제공 않고 대신 사용 하 여 사용자 또는 서비스 기반 Azure AD 인증 나열 된 다음에 도달 합니다.

## <a name="azure-ad-user-authentication"></a>Azure AD 사용자 인증

Azure Active Directory 사용자를 대상으로 하는 응용 프로그램에 대 한 다음 문서에 설명 된 대로 ADAL 라이브러리를 사용 하 여 얻을 수 있는 사용자에 대 한 Azure AD 토큰을 사용할 것이 좋습니다: https://docs.microsoft.com/azure/active-directory/develop/v1-overview; 나열 된 단계를 따라야 합니다. 아래 "빠른 시작"는 다음과 같습니다.

1. Azure portal에서 구성
    1.  Azure AD에 응용 프로그램 등록 **네이티브 응용 프로그램**합니다. 등록의 일환으로, 응용 프로그램, 다중 테 넌 트 수 해야 및 제공 응용 프로그램에 대해 허용 Url 리디렉션 되었는지 확인 해야 합니다.  
    2.  리소스에 응용 프로그램 또는 사용자 액세스 허용: 
        1.  Azure portal에서 공간 앵커 리소스로 이동
        2.  으로 전환 합니다 **액세스 제어 (IAM)** 탭
        3.  적중 **역할 할당 추가**
            1.  [역할 선택](#role-based-access-control)
            2.  에 **선택** 필드, 사용자, 그룹 및/또는 액세스 권한을 할당 하려는 응용 프로그램의 이름을 입력 합니다. 
            3.  적중 **저장할**합니다.
2. 코드:
    1.  사용 해야 합니다 **응용 프로그램 ID** 및 **리디렉션 Uri** 사용자 고유의 Azure AD 응용 프로그램의 합니다 **클라이언트 ID** 및 **RedirectUri** ADAL의 매개 변수
    2.  테 넌 트 정보를 설정 합니다.
        1.  응용 프로그램이 지 원하는 경우 **내 조직만**를 사용 하 여이 값을 대체 하 **테 넌 트 ID** 또는 **테 넌 트 이름** (예를 들어 contoso.microsoft.com)
        2.  응용 프로그램이 지 원하는 경우 **모든 조직 디렉터리의 계정**를 사용 하 여이 값을 대체 **조직**
        3.  응용 프로그램이 지 원하는 경우 **모든 Microsoft 계정 사용자**를 사용 하 여이 값을 대체 **일반적인**
    3.  토큰 요청을 설정 합니다 **리소스** 에 "https://sts.mixedreality.azure.com"입니다. 이 "resource" 응용 프로그램의 Azure 공간 앵커 서비스에 대 한 토큰을 요청 하는 Azure AD에 표시 됩니다.  

사용 하 여 응용 프로그램을 Azure AD 토큰을 ADAL에서 얻을 수 해야 해당 Azure AD 토큰으로 설정할 수 있습니다 합니다 **authenticationToken** 클라우드 세션 구성 개체입니다. 

```csharp
this.cloudSession.Configuration.AuthenticationToken = @"MyAuthenticationToken";
```

## <a name="azure-ad-service-authentication"></a>Azure AD 서비스 인증

인증 요청을 broker는 백 엔드 서비스를 활용 하 여 프로덕션을 Azure 공간 앵커를 활용 하 여 앱을 배포 하려면 권장 되는 옵션이입니다. 이 다이어그램에 설명 된 대로 일반적인 구성표 있어야 합니다.

![Azure 공간 앵커 인증 개요](./media/spatial-anchors-aad-authentication.png)

앱 자체 메커니즘이 가정 여기에서 (예: Microsoft 계정, PlayFab, Facebook, Google ID, 사용자 지정 사용자 이름/암호, 등.) 해당 백 엔드 서비스에 인증 합니다. 사용자가 서비스를 검색할 수 있는 백 엔드 서비스에 인증 되 면 Azure AD 토큰을 Azure 공간 앵커에 대 한 액세스 토큰에 대 한 교환 및 클라이언트 응용 프로그램으로 다시 반환 합니다.

다음 문서에 설명 된 대로 ADAL 라이브러리를 사용 하 여 Azure AD 액세스 토큰이 검색 됩니다: https://docs.microsoft.com/azure/active-directory/develop/v1-overview; 포함 된 "빠른 시작" 아래에 나열 된 단계를 따라야 합니다.

1.  Azure portal에서 구성 합니다.
    1.  Azure AD에 응용 프로그램을 등록 합니다.
        1.  Azure portal로 이동 **Azure Active Directory**, 선택한 **앱 등록**
        2.  선택 **새 응용 프로그램 등록**
        3.  응용 프로그램 선택의 이름을 입력 **웹 앱 / API** 응용 프로그램 유형으로 서비스에 대 한 인증 URL을 입력 합니다. 그런 다음을 누릅니다 **만들기**합니다.
        4.  해당 응용 프로그램에 적중 **설정을**를 선택 하 고는 **키** 탭 합니다. 키의 이름을 입력, 기간을 선택 하 고 적중 **저장할**합니다. 웹 서비스의 코드에 포함 해야 합니다. 이때 표시 되는 키 값을 저장 해야 합니다.
    2.  리소스에 대 한 응용 프로그램 및/또는 사용자 액세스 권한을 부여 합니다.
        1.  Azure portal에서 공간 앵커 리소스로 이동
        2.  으로 전환 합니다 **액세스 제어 (IAM)** 탭
        3.  적중 **역할 할당 추가**
        1.  [역할 선택](#role-based-access-control)
        2.  에 **선택** 필드, 사용자가 만든 응용 프로그램의 이름을 입력 하 고 할당 하려는 액세스 합니다. 공간 앵커 계정에 대해 다양 한 역할을 할 앱의 사용자를 하려는 경우에 Azure AD에서 여러 응용 프로그램을 등록 하 고 각각 별도 역할을 할당 해야 합니다. 사용자에 대 한 올바른 역할을 사용 하 여 권한 부여 논리를 구현 합니다.  
    3.  적중 **저장할**합니다.
2.  코드에서 (참고: GitHub에서 포함 된 서비스 샘플을 사용할 수 있습니다).
    1.  응용 프로그램 ID, 응용 프로그램 비밀을 사용 하도록 사용자 고유의 Azure AD 응용 프로그램 클라이언트 ID로의 리디렉션 Uri 및 비밀 및 ADAL RedirectUri 매개 변수
    2.  ADAL 인증 기관 매개 변수에서 사용자 고유의 AAAzure 추가 테 넌 트 ID를 테 넌 트 ID를 설정 합니다.
    3.  토큰 요청을 설정 합니다 **자원** 에 "https://sts.mixedreality.azure.com" 

사용 하 여 백 엔드 서비스는 Azure AD 토큰을 검색할 수 있습니다. 클라이언트에 다시 반환 하는 MR 토큰에 대해 교환할 수 있습니다 것입니다. 이루어집니다 MR 토큰을 검색 하는 Azure AD 토큰을 사용 하 여 REST 호출을 통해. 샘플 호출은 다음과 같습니다.

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

여기서 인증 헤더 형식은 다음과 같습니다. `Bearer <accoundId>:<accountKey>`

및 응답에는 일반 텍스트로 MR 토큰을 포함 합니다.
 
해당 MR 토큰을 클라이언트에 반환 됩니다. 클라이언트 앱 클라우드 세션 구성에서 해당 액세스 토큰으로 것을 설정할 수 있습니다.

```csharp
this.cloudSession.Configuration.AccessToken = @"MyAccessToken";
```

## <a name="role-based-access-control"></a>역할 기반 액세스 제어

제어 하는 데 대 한 액세스 수준을 공간 앵커 Azure 계정에 대해 필요에 따라 할당할 수 있습니다 다음 역할을 만든 응용 프로그램, 서비스 또는 서비스의 Azure AD 사용자에 게 부여:

- **공간 앵커 계정 소유자**: 응용 프로그램 또는이 역할이 있는 사용자 공간 앵커 쿼리를 만들고 삭제할 수 있습니다. 계정 키를 사용 하 여 계정에 인증할 때 합니다 **공간 앵커 계정 소유자** 역할은 인증 된 보안 주체에 할당 합니다. 
- **공간 앵커 계정 참가자**: 응용 프로그램 또는이 역할이 있는 사용자 공간 앵커를 해당 쿼리를 만들 수 있지만 삭제할 수는 없습니다. 
- **공간 앵커 계정 판독기**: 응용 프로그램 또는이 역할이 있는 사용자 수만 공간 앵커에 대 한 쿼리 하지만 새로 만들지, 기존 파일을 삭제 하거나 업데이트할 수 없습니다 공간 앵커에 대 한 메타 데이터입니다. 이 일반적으로 사용 응용 프로그램에 대 한 다른 사용자만 해당 환경에서 이전에 배치 하는 앵커를 회수할 수 있지만 일부 사용자 환경에을 조정 하는 위치입니다.

## <a name="next-steps"></a>다음 단계

Azure 공간 앵커를 사용 하 여 첫 번째 앱을 만듭니다.

> [!div class="nextstepaction"]
> [Unity](../unity-overview.yml)

> [!div class="nextstepaction"]
> [iOS](../quickstarts/get-started-ios.md)

> [!div class="nextstepaction"]
> [Android](../quickstarts/get-started-android.md)

> [!div class="nextstepaction"]
> [HoloLens](../quickstarts/get-started-hololens.md)
