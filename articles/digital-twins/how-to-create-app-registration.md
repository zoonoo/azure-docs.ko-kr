---
title: 앱 등록 만들기
titleSuffix: Azure Digital Twins
description: 클라이언트 앱에 대 한 인증 옵션으로 Azure AD 앱 등록을 만드는 방법을 참조 하세요.
author: baanders
ms.author: baanders
ms.date: 10/13/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: f252471cd3cd7e3a950bf2cfe324e580da129041
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2020
ms.locfileid: "92210169"
---
# <a name="create-an-app-registration-to-use-with-azure-digital-twins"></a>Azure Digital Twins에서 사용할 앱 등록 만들기

Azure Digital Twins 인스턴스를 사용할 때 사용자 지정 클라이언트 앱 또는 [ADT 탐색기](quickstart-adt-explorer.md)와 같은 샘플과 같은 클라이언트 응용 프로그램을 통해 해당 인스턴스와 상호 작용 하는 것이 일반적입니다. 이러한 응용 프로그램은 상호 작용 하기 위해 Azure Digital Twins를 사용 하 여 인증 해야 하며, 앱에서 사용할 수 있는 일부 [인증 메커니즘](how-to-authenticate-client.md) 에는 [AZURE ACTIVE DIRECTORY (azure AD)](../active-directory/fundamentals/active-directory-whatis.md) **앱 등록이**포함 됩니다.

이는 모든 인증 시나리오에 필요 하지 않습니다. 그러나 **클라이언트 id** 및 **테 넌 트 ID**를 포함 하 여 앱 등록을 요구 하는 인증 전략이 나 코드 샘플을 사용 하는 경우이 문서에서는이를 설정 하는 방법을 보여 줍니다.

## <a name="using-azure-ad-app-registrations"></a>Azure AD 앱 등록 사용

[Azure Active Directory (AZURE AD)](../active-directory/fundamentals/active-directory-whatis.md) 는 Microsoft의 클라우드 기반 id 및 액세스 관리 서비스입니다. Azure AD에서 **앱 등록** 을 설정 하는 방법은 클라이언트 앱에 Azure Digital twins에 대 한 액세스 권한을 부여 하는 한 가지 방법입니다.

이 앱 등록에서는 [Azure Digital Twins api](how-to-use-apis-sdks.md)에 대 한 액세스 권한을 구성 합니다. 이후에 클라이언트 앱은 등록의 **클라이언트 및 테 넌 트 ID 값**을 사용 하 여 앱 등록에 대해 인증할 수 있으며, 그 결과 api에 대해 구성 된 액세스 권한이 부여 됩니다.

>[!TIP]
> 필요할 때마다 새 앱 등록을 설정 *하거나* , 한 번만 수행 하 고이를 필요로 하는 모든 시나리오에서 공유 될 단일 앱 등록을 설정 하는 것이 좋습니다.

## <a name="create-the-registration"></a>등록 만들기

Azure Portal에서 [Azure Active Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) 로 이동 하 여 시작 합니다 .이 링크를 사용 하거나 포털 검색 표시줄에서 찾을 수 있습니다. 서비스 메뉴에서 *앱 등록* 를 선택 하 고 *+ 새로 만들기*를 선택 합니다.

:::image type="content" source="media/how-to-create-app-registration/new-registration.png" alt-text="' 앱 등록 ' 메뉴 옵션 및 ' + 새 등록 ' 단추를 강조 표시 하 여 Azure Portal에서 Azure AD 서비스 페이지를 표시 합니다.":::

뒤에 나오는 *응용 프로그램 등록* 페이지에서 요청 된 값을 입력 합니다.
* **이름**: 등록과 연결할 Azure AD 응용 프로그램 표시 이름입니다.
* **지원 되는 계정 유형**: *이 조직 디렉터리 에서만 계정 선택 (기본 디렉터리만-단일 테 넌 트)*
* **리디렉션 URI**: azure ad 응용 프로그램에 대 한 *azure ad 응용 프로그램 회신 URL* 입니다. 에 대 한 *공용 클라이언트/네이티브 (모바일 & 데스크톱)* URI를 추가 `http://localhost` 합니다.

작업이 완료 되 면 *등록* 단추를 누릅니다.

:::image type="content" source="media/how-to-create-app-registration/register-an-application.png" alt-text="' 앱 등록 ' 메뉴 옵션 및 ' + 새 등록 ' 단추를 강조 표시 하 여 Azure Portal에서 Azure AD 서비스 페이지를 표시 합니다.":::

등록이 완료 되 면 포털에서 세부 정보 페이지로 리디렉션됩니다.

## <a name="collect-client-id-and-tenant-id"></a>클라이언트 ID 및 테 넌 트 ID 수집

그런 다음 세부 정보 페이지에서 앱 등록에 대 한 몇 가지 중요 한 값을 수집 합니다.

:::image type="content" source="media/how-to-create-app-registration/app-important-values.png" alt-text="' 앱 등록 ' 메뉴 옵션 및 ' + 새 등록 ' 단추를 강조 표시 하 여 Azure Portal에서 Azure AD 서비스 페이지를 표시 합니다.":::

**페이지에** 표시 된 _**응용 프로그램 (클라이언트) id**_ 및 _**디렉터리 (테 넌 트) id**_ 를 기록해 둡니다. 이러한 값은 클라이언트 앱이이 등록을 사용 하 여 Azure Digital Twins로 인증 해야 하는 값입니다.

## <a name="provide-azure-digital-twins-api-permission"></a>Azure Digital Twins API 권한 제공

다음으로, 기본 사용 권한으로 만든 앱 등록을 Azure Digital Twins Api에 구성 합니다.

앱 등록에 대 한 포털 페이지의 메뉴에서 *API 권한* 을 선택 합니다. 다음 사용 권한 페이지에서 *+ 권한 추가* 단추를 누릅니다.

:::image type="content" source="media/how-to-create-app-registration/add-permission.png" alt-text="' 앱 등록 ' 메뉴 옵션 및 ' + 새 등록 ' 단추를 강조 표시 하 여 Azure Portal에서 Azure AD 서비스 페이지를 표시 합니다.":::

다음에 나오는 *api 권한 요청* 페이지에서 *내 조직* 에서 tab 키를 사용 하 고 *azure digital 쌍*를 검색 하는 api로 전환 합니다. Azure Digital Twins Api에 대 한 권한 할당을 진행 하려면 검색 결과에서 _**Azure Digital Twins**_ 를 선택 합니다.

:::image type="content" source="media/how-to-create-app-registration/request-api-permissions-1.png" alt-text="' 앱 등록 ' 메뉴 옵션 및 ' + 새 등록 ' 단추를 강조 표시 하 여 Azure Portal에서 Azure AD 서비스 페이지를 표시 합니다.":::

>[!NOTE]
> 구독에 서비스의 이전 공개 미리 보기 (7 월 2020 일 이전)에서 기존 Azure Digital Twins 인스턴스가 있는 경우 대신 _**Azure 스마트 공간 서비스**_ 를 검색 하 고 선택 해야 합니다. 동일한 Api 집합의 이전 이름입니다. *응용 프로그램 (클라이언트) ID* 는 위의 스크린샷에서와 동일 하며,이 단계를 벗어나 사용자 환경이 변경 되지 않습니다.
> :::image type="content" source="media/how-to-create-app-registration/request-api-permissions-1-smart-spaces.png" alt-text="' 앱 등록 ' 메뉴 옵션 및 ' + 새 등록 ' 단추를 강조 표시 하 여 Azure Portal에서 Azure AD 서비스 페이지를 표시 합니다.":::

다음으로 이러한 Api에 부여할 사용 권한을 선택 합니다. **읽기 (1)** 권한을 확장 하 고 *읽기. 쓰기* 확인란을 선택 하 여이 앱 등록 판독기 및 작성기 권한을 부여 합니다.

:::image type="content" source="media/how-to-create-app-registration/request-api-permissions-2.png" alt-text="' 앱 등록 ' 메뉴 옵션 및 ' + 새 등록 ' 단추를 강조 표시 하 여 Azure Portal에서 Azure AD 서비스 페이지를 표시 합니다.":::

완료 되 면 *권한 추가* 를 누릅니다.

### <a name="verify-success"></a>성공 확인

*API 사용 권한* 페이지에서 읽기/쓰기 권한을 반영 하는 Azure Digital twins 항목이 있는지 확인 합니다.

:::image type="content" source="media/how-to-create-app-registration/verify-api-permissions.png" alt-text="' 앱 등록 ' 메뉴 옵션 및 ' + 새 등록 ' 단추를 강조 표시 하 여 Azure Portal에서 Azure AD 서비스 페이지를 표시 합니다.":::

API 사용 권한을 추가할 때 Azure Digital Twins 정보를 사용 하 여 자동으로 업데이트 된 앱 등록의 *manifest.js*에서 Azure Digital twins에 대 한 연결을 확인할 수도 있습니다.

이렇게 하려면 메뉴에서 *매니페스트* 를 선택 하 여 앱 등록의 매니페스트 코드를 확인 합니다. 코드 창의 아래쪽으로 스크롤하고 아래에서 이러한 필드를 찾습니다 `requiredResourceAccess` . 값은 아래 스크린샷에 있는 값과 일치 해야 합니다.

:::image type="content" source="media/how-to-create-app-registration/verify-manifest.png" alt-text="' 앱 등록 ' 메뉴 옵션 및 ' + 새 등록 ' 단추를 강조 표시 하 여 Azure Portal에서 Azure AD 서비스 페이지를 표시 합니다.":::

이러한 값이 없는 경우 [API 권한 추가에 대 한 섹션](#provide-azure-digital-twins-api-permission)의 단계를 다시 시도 합니다.

## <a name="other-possible-steps-for-your-organization"></a>조직의 다른 가능한 단계

조직에서 앱 등록을 성공적으로 설정 하려면 구독 소유자/관리자의 추가 작업이 필요할 수 있습니다. 필요한 단계는 조직의 특정 설정에 따라 달라질 수 있습니다.

다음은 구독의 소유자/관리자가 수행 해야 하는 몇 가지 일반적인 잠재적 활동입니다. 이러한 작업 및 기타 작업은 Azure Portal의 [*Azure AD 앱 등록*](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) 페이지에서 수행할 수 있습니다.
* 앱 등록에 대 한 관리자 동의를 부여 합니다. 조직에서 구독 내의 모든 앱 등록에 대해 Azure AD에서 전역으로 설정 해야 하는 *관리자 동의가* 있을 수 있습니다. 그렇다면 소유자/관리자가 앱 등록의 *API 사용 권한* 페이지에서 회사에 대 한이 단추를 선택 하 여 앱 등록을 유효 하 게 설정 해야 합니다.

    :::image type="content" source="media/how-to-create-app-registration/grant-admin-consent.png" alt-text="' 앱 등록 ' 메뉴 옵션 및 ' + 새 등록 ' 단추를 강조 표시 하 여 Azure Portal에서 Azure AD 서비스 페이지를 표시 합니다.":::
  - 동의가 성공적으로 부여 된 경우 Azure Digital Twins의 항목에는 _ **(회사)** 에 대해 부여_ 된 *상태* 값이 표시 되어야 합니다.
   
    :::image type="content" source="media/how-to-create-app-registration/granted-admin-consent-done.png" alt-text="' 앱 등록 ' 메뉴 옵션 및 ' + 새 등록 ' 단추를 강조 표시 하 여 Azure Portal에서 Azure AD 서비스 페이지를 표시 합니다.":::
* 공용 클라이언트 액세스 활성화
* 웹 및 데스크톱 액세스에 대 한 특정 회신 Url 설정
* 암시적 OAuth2 인증 흐름 허용

앱 등록 및 다양 한 설치 옵션에 대 한 자세한 내용은 [*Microsoft id 플랫폼을 사용 하 여 응용 프로그램 등록*](/graph/auth-register-app-v2)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Digital Twins Api를 사용 하 여 클라이언트 응용 프로그램을 인증 하는 데 사용할 수 있는 Azure AD 앱 등록을 설정 합니다.

다음으로, 앱 등록을 사용 하는 인증 메커니즘과
* [*방법: 앱 인증 코드 작성*](how-to-authenticate-client.md)