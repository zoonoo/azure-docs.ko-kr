---
title: Azure Active Directory에서 앱에 대 한 사용자 프로 비전을 위한 SCIM 끝점 빌드
description: SCIM 끝점을 개발 하 고, SCIM API를 Azure AD와 통합 하 고, Azure Active Directory를 사용 하 여 클라우드 응용 프로그램에 사용자 및 그룹을 자동으로 프로 비전 하는 방법을 알아봅니다.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: conceptual
ms.date: 01/27/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 4e933000c8e700d8bfd193b542e3855b2fca26f8
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101689337"
---
# <a name="tutorial-develop-a-sample-scim-endpoint"></a>자습서: 샘플 SCIM 끝점 개발

새 끝점을 처음부터 빌드할 수 없으므로 [SCIM (도메인 간 Id 관리)을 위해 시스템](https://aka.ms/scimoverview)을 시작 하기 위한 몇 가지 [참조 코드](https://aka.ms/scimreferencecode) 를 만들었습니다. 5 분 내에 코드 없이 SCIM 끝점을 실행 하 여 실행할 수 있습니다.

이 자습서에서는 Azure에서 SCIM 참조 코드를 배포 하 고 Postman을 사용 하거나 Azure Active Directory (Azure AD) SCIM 클라이언트와 통합 하 여 테스트 하는 방법을 설명 합니다. 이 자습서는 SCIM을 시작 하려는 개발자 또는 SCIM 끝점을 테스트 하는 데 관심이 있는 개발자를 위한 것입니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
>
> * Azure에서 SCIM 끝점을 배포 합니다.
> * SCIM 끝점을 테스트 합니다.

## <a name="deploy-your-scim-endpoint-in-azure"></a>Azure에서 SCIM 끝점 배포

이 단계에서는 [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) 및 [Azure App Service](../../app-service/index.yml)를 사용 하 여 scim 끝점을 서비스에 배포 합니다. SCIM 참조 코드를 로컬로 실행 하거나 온-프레미스 서버에서 호스팅하거나 다른 외부 서비스에 배포할 수도 있습니다.

1. GitHub에서 [참조 코드로](https://github.com/AzureAD/SCIMReferenceCode) 이동 하 고 **복제 또는 다운로드** 를 선택 합니다.

1. **바탕 화면에서 열기** 를 선택 하거나, 링크를 복사 하 고, Visual Studio를 열고, **복제 또는 체크 아웃 코드** 를 선택 하 여 복사 된 링크를 입력 하 고 로컬 복사본을 만듭니다.

1. Visual Studio에서 호스팅 리소스에 대 한 액세스 권한이 있는 계정에 로그인 해야 합니다.

1. 솔루션 탐색기에서 *microsoft* 파일을 열고 *microsoft* 파일을 마우스 오른쪽 단추로 클릭 합니다. **게시** 를 선택합니다.

    ![샘플 파일을 보여 주는 스크린샷](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish.png)

    > [!NOTE]
    > 이 솔루션을 로컬로 실행 하려면 프로젝트를 두 번 클릭 하 고 **IIS Express** 를 선택 하 여 로컬 호스트 URL이 있는 웹 페이지로 프로젝트를 시작 합니다.

1. **프로필 만들기** 를 선택 하 고 **App Service** 및 **새로 만들기** 가 선택 되어 있는지 확인 합니다.

    ![게시 창을 보여 주는 스크린샷](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish-2.png)

1. 대화 상자 옵션을 단계별로 실행 하 고 앱 이름을 원하는 이름으로 바꿉니다. 이 이름은 앱과 SCIM 끝점 URL에 모두 사용 됩니다.

    ![새 app service를 만드는 과정을 보여 주는 스크린샷](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish-3.png)

1. 사용할 리소스 그룹을 선택 하 고 **게시** 를 선택 합니다.

    ![새 app service 게시를 보여 주는 스크린샷](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish-4.png)

1. **Azure App Service** 구성에서 응용 프로그램으로 이동 하  >   고 **새 응용 프로그램 설정** 을 선택 하 여 값을 사용 하 여 *Token__TokenIssuer* 설정을 추가 합니다 `https://sts.windows.net/<tenant_id>/` . `<tenant_id>`을 AZURE AD 테 넌 트 ID로 바꿉니다. [Postman](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint)을 사용 하 여 scim 끝점을 테스트 하려면 값을 사용 하 여 *ASPNETCORE_ENVIRONMENT* 설정을 추가 `Development` 합니다.

   ![응용 프로그램 설정 창을 보여 주는 스크린샷](media/use-scim-to-build-users-and-groups-endpoints/app-service-settings.png)

   [Azure Portal](use-scim-to-provision-users-and-groups.md#integrate-your-scim-endpoint-with-the-aad-scim-client)에서 엔터프라이즈 응용 프로그램을 사용 하 여 끝점을 테스트 하는 경우 두 가지 옵션이 있습니다. 환경을 유지 `Development` 하 고 끝점에서 테스트 토큰을 제공 `/scim/token` 하거나 환경을로 변경 하 `Production` 고 토큰 필드를 비워 둘 수 있습니다.

이것으로 끝입니다. 이제 SCIM 끝점이 게시 되 고 Azure App Service URL을 사용 하 여 SCIM 끝점을 테스트할 수 있습니다.

## <a name="test-your-scim-endpoint"></a>SCIM 끝점 테스트

SCIM 끝점에 대 한 요청은 권한 부여가 필요 합니다. SCIM 표준에는 쿠키, 기본 인증, TLS 클라이언트 인증 또는 [RFC 7644](https://tools.ietf.org/html/rfc7644#section-2)에 나열 된 방법을 포함 하 여 인증 및 권한 부여에 대 한 여러 옵션이 있습니다.

사용자 이름 및 암호와 같은 안전 하지 않은 메서드는 OAuth와 같은 보다 안전한 방법을 사용 하지 않아야 합니다. Azure AD는 긴 전달자 토큰 (갤러리 및 비 갤러리 응용 프로그램의 경우) 및 OAuth 권한 부여 (갤러리 응용 프로그램의 경우)를 지원 합니다.

> [!NOTE]
> 리포지토리에 제공 된 권한 부여 방법은 테스트용 으로만 제공 됩니다. Azure AD와 통합 하는 경우 권한 부여 지침을 검토할 수 있습니다. [SCIM 끝점에 대 한 프로 비전 계획을](use-scim-to-provision-users-and-groups.md)참조 하세요.

개발 환경에서는 참조 코드와 같이 프로덕션에 안전 하지 않은 기능을 사용 하 여 보안 토큰 유효성 검사의 동작을 제어할 수 있습니다. 토큰 유효성 검사 코드는 자체 서명 된 보안 토큰을 사용 하 고 서명 키는 구성 파일에 저장 됩니다. 파일 *의appsettings.Development.js* 에서 **Token: IssuerSigningKey** 매개 변수를 참조 하세요.

```json
"Token": {
    "TokenAudience": "Microsoft.Security.Bearer",
    "TokenIssuer": "Microsoft.Security.Bearer",
    "IssuerSigningKey": "A1B2C3D4E5F6A1B2C3D4E5F6",
    "TokenLifetimeInMins": "120"
}
```

> [!NOTE]
> 끝점에 **GET** 요청을 보내면 구성 된 `/scim/token` 키를 사용 하 여 토큰이 발급 됩니다. 이 토큰은 후속 권한 부여를 위한 전달자 토큰으로 사용할 수 있습니다.

기본 토큰 유효성 검사 코드는 Azure AD 토큰을 사용 하도록 구성 되며, *appsettings.js* 파일의 **Token: tokenissuer** 매개 변수를 사용 하 여 발급 테 넌 트를 구성 해야 합니다.

``` json
"Token": {
    "TokenAudience": "8adf8e6e-67b2-4cf2-a259-e3dc5476c621",
    "TokenIssuer": "https://sts.windows.net/<tenant_id>/"
}
```

### <a name="use-postman-to-test-endpoints"></a>Postman을 사용 하 여 끝점 테스트

SCIM 끝점을 배포한 후에는 SCIM RFC와 호환 되는지 테스트할 수 있습니다. 이 예제에서는 사용자와 그룹에 대 한 CRUD (만들기, 읽기, 업데이트 및 삭제) 작업, 필터링, 그룹 멤버 자격 업데이트 및 사용자 비활성화의 유효성을 검사 하는 Postman의 일련의 테스트를 제공 합니다.

끝점은 `{host}/scim/` 디렉터리에 있으며 표준 HTTP 요청을 사용 하 여 상호 작용할 수 있습니다. 경로를 수정 하려면 `/scim/` *ControllerConstant.cs* in **AzureADProvisioningSCIMreference**  >  **scimreferenceapi**  >  **controller** 를 참조 하세요.

> [!NOTE]
> 로컬 테스트에만 HTTP 끝점을 사용할 수 있습니다. Azure AD 프로 비전 서비스는 끝점에서 HTTPS를 지원 해야 합니다.

1. [Postman](https://www.getpostman.com/downloads/) 을 다운로드 하 고 응용 프로그램을 시작 합니다.
1. 이 링크를 복사 하 여 Postman에 붙여넣어 테스트 컬렉션을 가져옵니다 `https://aka.ms/ProvisioningPostman` .

    ![Postman에서 테스트 컬렉션을 가져오는 방법을 보여 주는 스크린샷](media/use-scim-to-build-users-and-groups-endpoints/postman-collection.png)

1. 다음 변수가 있는 테스트 환경을 만듭니다.

   |Environment|변수|값|
   |-|-|-|
   |IIS Express를 사용 하 여 로컬로 프로젝트 실행|||
   ||**Server**|`localhost`|
   ||**포트**|`:44359`*(를 잊지 마세요. **`:`** )*|
   ||**Api-version**|`scim`|
   |Kestrel을 사용 하 여 로컬에서 프로젝트 실행|||
   ||**Server**|`localhost`|
   ||**포트**|`:5001`*(를 잊지 마세요. **`:`** )*|
   ||**Api-version**|`scim`|
   |Azure에서 끝점 호스트|||
   ||**Server**|*(SCIM URL 입력)*|
   ||**포트**|*(비워 둠)*|
   ||**Api-version**|`scim`|

1. Postman collection의 **Get Key** 를 사용 하 여 토큰 끝점에 **get** 요청을 보내고 이후 요청에 대 한 **토큰** 변수에 저장할 보안 토큰을 검색 합니다.

   ![Postman Get Key 폴더를 보여 주는 스크린샷](media/use-scim-to-build-users-and-groups-endpoints/postman-get-key.png)

   > [!NOTE]
   > SCIM 끝점의 보안을 설정 하려면 연결 하기 전에 보안 토큰이 필요 합니다. 이 자습서에서는 끝점을 사용 하 여 `{host}/scim/token` 자체 서명 된 토큰을 생성 합니다.

이것으로 끝입니다. 이제 **Postman** collection을 실행 하 여 scim 끝점 기능을 테스트할 수 있습니다.

## <a name="next-steps"></a>다음 단계

클라이언트에 대 한 상호 운용성을 통해 SCIM 규격 사용자 및 그룹 끝점을 개발 하려면 [scim 클라이언트 구현](http://www.simplecloud.info/#Implementations2)을 참조 하세요.

> [!div class="nextstepaction"]
> [자습서: SCIM 끝점에 대 한 프로 비전 개발 및 계획](use-scim-to-provision-users-and-groups.md) 
>  [자습서: 갤러리 앱에 대 한 프로 비전 구성](configure-automatic-user-provisioning-portal.md)