---
title: '빠른 시작: 웹 API에 액세스하도록 앱 구성 | Azure'
titleSuffix: Microsoft identity platform
description: 이 빠른 시작에서는 Microsoft ID 플랫폼에서 웹 API를 나타내는 앱 등록을 구성하여 클라이언트 애플리케이션에 대한 범위가 지정된 리소스 액세스(권한)를 사용하도록 설정합니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/03/2020
ms.author: marsma
ms.custom: aaddev, contperfq1
ms.reviewer: lenalepa, aragra, sureshja
ms.openlocfilehash: fc2f3202ac88e3ee6c24db21dd9072a13a8deef9
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2020
ms.locfileid: "89442318"
---
# <a name="quickstart-configure-a-client-application-to-access-a-web-api"></a>빠른 시작: 웹 API에 액세스하도록 클라이언트 애플리케이션 구성

이 빠른 시작에서는 사용자 고유의 웹 API에 대한 범위가 지정된 권한 기반 액세스를 Microsoft ID 플랫폼에 등록된 클라이언트 앱에 제공합니다. Microsoft Graph에 대한 클라이언트 앱 액세스도 제공합니다.

웹 API의 범위를 클라이언트 앱의 등록에 지정하면 클라이언트 앱이 Microsoft ID 플랫폼에서 해당 범위가 포함된 액세스 토큰을 가져올 수 있습니다. 그런 다음, 해당 코드 내에서 웹 API는 액세스 토큰에 있는 범위를 기준으로 리소스에 대한 권한 기반 액세스를 제공할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

* 활성 구독이 있는 Azure 계정 - [체험 계정 만들기](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [빠른 시작: 애플리케이션 등록](quickstart-register-app.md) 완료
* [빠른 시작: 웹 API를 공개하도록 애플리케이션 구성](quickstart-configure-app-expose-web-apis.md) 완료

## <a name="add-permissions-to-access-your-web-api"></a>웹 API에 액세스할 수 있는 권한 추가

첫 번째 시나리오에서는 클라이언트 앱 액세스 권한을 사용자 고유의 웹 API에 부여합니다. 이 두 가지 모두는 필수 구성 요소의 일부로 등록해야 합니다. 클라이언트 앱과 웹 API 모두를 아직 등록하지 않은 경우 이 문서의 두 가지 [필수 구성 요소](#prerequisites) 단계를 완료합니다.

이 다이어그램에서는 두 가지 앱 등록이 서로 관련되는 방법을 보여 줍니다. 이 섹션에서는 권한을 클라이언트 앱의 등록에 추가합니다.

:::image type="content" source="media/quickstart-configure-app-access-web-apis/diagram-01-app-permission-to-api-scopes.svg" alt-text="오른쪽에서 범위가 공개된 웹 API를 보여 주고, 왼쪽에서 이러한 범위가 권한으로 선택된 클라이언트 앱을 보여 주는 선 다이어그램" border="false":::

클라이언트 앱과 웹 API를 모두 등록하고 범위를 만들어 API를 공개한 후에는 다음 단계를 수행하여 API에 대한 클라이언트의 권한을 구성할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 여러 테넌트에 액세스할 수 있는 경우 위쪽 메뉴의 **디렉터리 + 구독** 필터 :::image type="icon" source="./media/quickstart-configure-app-access-web-apis/portal-01-directory-subscription-filter.png" border="false":::를 사용하여 클라이언트 앱의 등록이 포함된 테넌트를 선택합니다.
1. **Azure Active Directory** > **앱 등록**을 차례로 선택한 다음, 클라이언트 애플리케이션(웹 API가 *아님*)을 선택합니다.
1. **API 사용 권한** > **사용 권한 추가** > **내 API**를 선택합니다.
1. 필수 구성 요소의 일부로 등록한 웹 API를 선택합니다.

    **위임된 권한**이 기본적으로 선택되어 있습니다. 위임된 권한은 로그인한 사용자로 웹 API에 액세스하고 다음 단계에서 선택하는 권한으로 액세스를 제한해야 하는 클라이언트 앱에 적합합니다. 이 예에서는 **위임된 권한**이 선택된 상태로 둡니다.

    **애플리케이션 권한**은 로그인 또는 동의를 위한 사용자 상호 작용 없이 웹 API에 자체적으로 액세스해야 하는 서비스 또는 디먼 유형의 애플리케이션에 대한 권한입니다. 웹 API에 대한 애플리케이션 역할을 정의하지 않은 경우 이 옵션은 사용하지 않도록 설정됩니다.
1. **권한 선택** 아래에서 웹 API에 대해 정의한 범위에 해당하는 리소스를 펼치고, 로그인한 사용자를 대신하여 클라이언트 앱에서 보유해야 하는 권한을 선택합니다.

    이전 빠른 시작에서 지정한 범위 이름 예제를 사용한 경우 **Employees.Read.All** 및 **Employees.Write.All**이 표시됩니다.
    **Employees.Read.All** 또는 필수 구성 요소를 완료할 때 만들었을 수도 있는 다른 권한을 선택합니다.
1. **권한 추가**를 선택하여 프로세스를 완료합니다.

권한이 API에 추가되면 **구성된 권한** 아래에 선택한 권한이 표시됩니다. 다음 이미지에서는 클라이언트 앱의 등록에 추가된 *Employees.Read.All* 위임된 권한 예제를 보여 줍니다.

:::image type="content" source="media/quickstart-configure-app-access-web-apis/portal-02-configured-permissions-pane.png" alt-text="오른쪽에서 범위가 공개된 웹 API를 보여 주고, 왼쪽에서 이러한 범위가 권한으로 선택된 클라이언트 앱을 보여 주는 선 다이어그램":::

Microsoft Graph API에 대한 *User.Read* 권한도 확인할 수 있습니다. 이 권한은 Azure Portal에서 앱을 등록할 때 자동으로 추가됩니다.

## <a name="add-permissions-to-access-microsoft-graph"></a>Microsoft Graph에 액세스할 수 있는 권한 추가

애플리케이션에서 로그인한 사용자를 대신하여 사용자 고유의 웹 API에 액세스할 수 있을 뿐 아니라 Microsoft Graph에 저장된 사용자(또는 기타) 데이터에 액세스하거나 수정해야 할 수도 있습니다. 또는 사용자 상호 작용 없이 Microsoft Graph에 자체적으로 액세스하여 작업을 수행해야 하는 서비스 또는 디먼 앱이 있을 수 있습니다.

### <a name="delegated-permission-to-microsoft-graph"></a>Microsoft Graph에 대한 위임된 권한

클라이언트 애플리케이션에서 로그인한 사용자를 대신하여 작업(예: 이메일 읽기 또는 프로필 수정)을 수행할 수 있도록 Microsoft Graph에 대한 위임된 권한을 구성합니다. 기본적으로 로그인할 때 클라이언트 앱의 사용자에게 구성한 위임된 권한에 동의하라는 메시지가 표시됩니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 여러 테넌트에 액세스할 수 있는 경우 위쪽 메뉴의 **디렉터리 + 구독** 필터 :::image type="icon" source="./media/quickstart-configure-app-access-web-apis/portal-01-directory-subscription-filter.png" border="false":::를 사용하여 클라이언트 앱의 등록이 포함된 테넌트를 선택합니다.
1. **Azure Active Directory** > **앱 등록**을 차례로 선택한 다음, 클라이언트 애플리케이션을 선택합니다.
1. **API 권한** > **권한 추가** > **Microsoft Graph**를 차례로 선택합니다.
1. **위임된 권한**을 선택합니다. Microsoft Graph에서 가장 일반적으로 사용되는 권한(목록의 위쪽에 표시)을 포함하여 많은 권한을 공개합니다.
1. **권한 선택** 아래에서 다음 권한을 선택합니다.

    | 사용 권한       | Description                                         |
    |------------------|-----------------------------------------------------|
    | `email`          | 사용자의 이메일 주소 보기                           |
    | `offline_access` | 액세스 권한이 부여된 데이터에 대한 액세스 유지 |
    | `openid`         | 사용자 로그인                                       |
    | `profile`        | 사용자의 기본 프로필 보기기                           |
1. **권한 추가**를 선택하여 프로세스를 완료합니다.

권한을 구성할 때마다 로그인하는 경우 앱 사용자에게 앱에서 사용자를 대신하여 리소스 API에 액세스할 수 있도록 허용하는 데 동의하라는 메시지가 표시됩니다.

관리자는 사용자에게 메시지가 표시되지 않도록 *모든* 사용자를 대신하여 동의를 부여할 수도 있습니다. 관리자 동의는 이 문서의 뒷부분에 있는 [API 권한 및 관리자 동의에 대한 추가 정보](#more-on-api-permissions-and-admin-consent) 섹션에서 설명합니다.

### <a name="application-permission-to-microsoft-graph"></a>Microsoft Graph에 대한 애플리케이션 권한

사용자 상호 작용 또는 동의 없이 자체적으로 인증해야 하는 애플리케이션에 대한 애플리케이션 권한을 구성합니다. 애플리케이션 권한은 일반적으로 API에 "헤드리스" 방식으로 액세스하는 백그라운드 서비스 또는 디먼 앱 및 다른(다운스트림) API에 액세스하는 웹 API에서 사용됩니다.

예를 들어 다음 단계에서는 권한을 Microsoft Graph의 *Files.Read.All* 권한에 부여합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 여러 테넌트에 액세스할 수 있는 경우 위쪽 메뉴의 **디렉터리 + 구독** 필터 :::image type="icon" source="./media/quickstart-configure-app-access-web-apis/portal-01-directory-subscription-filter.png" border="false":::를 사용하여 클라이언트 앱의 등록이 포함된 테넌트를 선택합니다.
1. **Azure Active Directory** > **앱 등록**을 차례로 선택한 다음, 클라이언트 애플리케이션을 선택합니다.
1. **API 권한** > **권한 추가** > **Microsoft Graph** > **애플리케이션 권한**을 차례로 선택합니다.
1. **권한 선택** 아래에 Microsoft Graph에서 공개하는 모든 권한이 표시됩니다.
1. 애플리케이션에 부여하려는 하나 이상의 권한을 선택합니다. 예를 들어 조직의 파일을 검사하여 특정 파일 형식 또는 이름에 대해 경고하는 디먼 앱이 있을 수 있습니다.

    **권한 선택** 아래에서 **파일**을 펼친 다음, *Files.Read.All* 권한을 선택합니다.
1. **권한 추가**를 선택합니다.

Microsoft Graph의 *Files.Read.All* 권한과 같은 일부 권한에는 관리자 동의가 필요합니다. 나중에 [관리자 동의 단추](#admin-consent-button) 섹션에서 설명하는 **관리자 동의 허용** 단추를 선택하여 관리자 동의를 부여합니다.

### <a name="configure-client-credentials"></a>클라이언트 자격 증명 구성

애플리케이션 권한을 사용하는 앱은 사용자 상호 작용 없이 자체의 자격 증명을 사용하여 자체적으로 인증합니다. 애플리케이션(또는 API)에서 애플리케이션 권한을 사용하여 Microsoft Graph, 사용자 고유의 웹 API 또는 다른 API에 액세스하려면 먼저 해당 클라이언트 앱의 자격 증명을 구성해야 합니다.

앱의 자격 증명을 구성하는 방법에 대한 자세한 내용은 [빠른 시작: Microsoft ID 플랫폼에 애플리케이션 등록](quickstart-register-app.md)의 [자격 증명 추가](quickstart-register-app.md#add-credentials) 섹션을 참조하세요.

## <a name="more-on-api-permissions-and-admin-consent"></a>API 권한 및 관리자 동의에 대한 추가 정보

앱 등록의 **API 권한** 창에는 [구성된 권한](#configured-permissions) 테이블이 포함되어 있으며, [부여된 기타 권한](#other-permissions-granted) 테이블도 포함될 수 있습니다. 다음 섹션에서는 테이블 및 [관리자 동의 단추](#admin-consent-button) 모두에 대해 설명합니다.

### <a name="configured-permissions"></a>구성된 사용 권한

**API 권한** 창의 **구성된 권한** 테이블에는 애플리케이션에서 기본 작업에 필요한 권한 목록인 *RRA(필요한 리소스 액세스)* 목록이 표시됩니다. 사용자 또는 해당 관리자는 앱을 사용하기 전에 이러한 권한에 동의해야 합니다. 기타 선택적 권한은 나중에 동적 동의를 사용하여 런타임에 요청할 수 있습니다.

이는 사용자가 앱에 대해 동의해야 하는 최소 권한 목록입니다. 더 많이 있을 수 있지만, 이러한 권한은 항상 필요합니다. 보안을 위해 그리고 사용자와 관리자가 앱을 더 편안하게 사용할 수 있도록 지원하기 위해 필요하지 않은 권한은 요청하지 마세요.

위에서 설명하는 단계를 사용하거나 [부여된 기타 권한](#other-permissions-granted)에서 설명하는 단계(다음 섹션 참조)를 사용하여 이 테이블에 표시되는 권한을 추가하거나 제거할 수 있습니다. 관리자는 테이블에 표시되는 API 권한의 전체 세트에 대해 관리자 동의를 부여하고, 개별 권한에 대한 동의를 철회할 수 있습니다.

### <a name="other-permissions-granted"></a>부여된 기타 권한

**API 권한** 창에서 **{사용자의 테넌트}에 대해 부여된 기타 권한**이 표시될 수도 있습니다. **{사용자의 테넌트}에 대해 부여된 기타 권한** 테이블에는 애플리케이션 개체에 명시적으로 구성되지 않은 테넌트에 부여된 권한이 표시됩니다. 이러한 권한은 동적으로 요청되고 동의되었습니다. 적용되는 권한이 하나 이상 있는 경우에만 이 섹션이 표시됩니다.

이 테이블에 표시되는 API 권한 또는 개별 권한의 전체 세트를 **구성된 권한** 테이블에 추가할 수 있습니다. 관리자는 이 섹션에서 API 또는 개별 권한에 대한 관리자 동의를 철회할 수 있습니다.

### <a name="admin-consent-button"></a>관리자 동의 단추

**{사용자의 테넌트}에 대한 관리자 동의 허용** 단추를 사용하면 관리자가 애플리케이션에 대해 구성된 권한에 대한 관리자 동의를 부여할 수 있습니다. 단추를 선택하면 동의 작업을 확인하도록 요청하는 대화 상자가 표시됩니다.

:::image type="content" source="media/quickstart-configure-app-access-web-apis/portal-03-grant-admin-consent-button.png" alt-text="오른쪽에서 범위가 공개된 웹 API를 보여 주고, 왼쪽에서 이러한 범위가 권한으로 선택된 클라이언트 앱을 보여 주는 선 다이어그램":::

동의가 부여되면 관리자 동의가 필요한 권한이 동의가 부여된 것으로 표시됩니다.

:::image type="content" source="media/quickstart-configure-app-access-web-apis/portal-04-admin-consent-granted.png" alt-text="오른쪽에서 범위가 공개된 웹 API를 보여 주고, 왼쪽에서 이러한 범위가 권한으로 선택된 클라이언트 앱을 보여 주는 선 다이어그램":::

관리자가 아니거나 애플리케이션에 대한 권한이 구성되지 않은 경우 **관리자 동의** 단추는 *사용하지 않도록 설정*됩니다. 부여되었지만 아직 구성되지 않은 권한이 있는 경우 관리자 동의 단추는 이러한 권한을 처리하라는 메시지를 표시합니다. 이러한 권한은 구성된 권한에서 추가하거나 제거할 수 있습니다.

## <a name="next-steps"></a>다음 단계

애플리케이션에 액세스할 수 있는 계정 유형을 구성하는 방법을 알아보려면 시리즈의 다음 빠른 시작으로 이동합니다. 예를 들어 조직의 사용자(단일 테넌트)에 대해서만 액세스를 제한하거나 다른 Azure AD 테넌트(다중 테넌트)의 사용자 및 개별 MSA(Microsoft 계정)를 가진 사용자에게만 액세스를 허용할 수 있습니다.

> [!div class="nextstepaction"]
> [애플리케이션에서 지원되는 계정 수정](quickstart-modify-supported-accounts.md)