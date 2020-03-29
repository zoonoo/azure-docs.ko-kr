---
title: 다른 사용자와 공동 작업 - LUIS
titleSuffix: Azure Cognitive Services
description: 앱 소유자는 작성 리소스에 기여도를 추가할 수 있습니다. 이러한 참여자는 모델을 수정하고, 학습하고, 앱을 게시할 수 있습니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: 913a2b26f67773d9fafbc0a8430d121fbabb97cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80053452"
---
# <a name="add-contributors-to-your-app"></a>앱에 기여자 추가

앱 소유자는 앱에 기여도를 추가할 수 있습니다. 이러한 협력자는 모델을 수정하고, 앱을 학습시키고, 게시할 수 있습니다. 

[!INCLUDE [Manage collaborators and contributors based on migrated or not-migrated apps](./includes/manage-contributor-collaborator-migration.md)]

## <a name="add-contributor-to-azure-authoring-resource"></a>Azure 작성 리소스에 기여자 추가

다음 절차는 Azure 작성 리소스를 사용하도록 **마이그레이션된** 모든 사용자를 위한 것입니다.

LUIS 작성 환경이 LUIS 포털의 **> Azure 리소스 관리** 페이지의 작성 리소스에 연결되어 있는 경우 마이그레이션했습니다.

1. Azure 포털에서 언어 이해(LUIS) 작성 리소스를 찾습니다. 형식이 `LUIS.Authoring`있습니다.
1. 이 리소스의 **IAM(액세스 제어)** 페이지에서 **+Add를** 선택한 다음 **역할 할당 추가를**선택합니다.

    ![Azure 포털에서 작성 리소스에 역할 할당을 추가합니다.](./media/luis-how-to-collaborate/authoring-resource-access-control-add-role.png)

1. 역할 **할당 추가** 창에서 기여자의 **역할을** 선택합니다. 옵션에 **대한 액세스 할당에서** **Azure AD 사용자, 그룹 또는 서비스 주체를 선택합니다.** **선택** 옵션에서 사용자의 전자 메일 주소를 입력합니다. 사용자가 동일한 도메인에 대해 1개 이상의 이메일 주소로 알려진 경우 _기본_ 이메일 계정을 입력해야 합니다.

    ![Azure AD의 기여자 역할에 사용자 전자 메일 추가](./media/luis-how-to-collaborate/add-role-assignment-for-contributor.png)

    사용자의 전자 메일이 발견되면 계정을 선택하고 **저장을**선택합니다. 

    이 역할 할당에 문제가 있는 경우 Azure 역할 할당 및 [Azure 액세스 제어 문제 해결을](../../role-based-access-control/troubleshooting.md#problems-with-azure-role-assignments) [검토합니다.](../../role-based-access-control/role-assignments-portal.md)

## <a name="add-collaborator-to-luis-app"></a>LUIS 앱에 공동 작업자 추가

다음 절차는 Azure 작성 리소스를 사용하도록 **마이그레이션되지 않은** 모든 사용자를 위한 것입니다.

LUIS 작성 환경이 LUIS 포털의 **> Azure 리소스 관리** 페이지의 작성 리소스에 연결되지 않은 경우 마이그레이션되지 않았습니다.

앱의 작성자, 소유자는 한 명이지만 협력자는 많을 수 있습니다. 협력자가 LUIS 앱을 편집하도록 허용하려면 LUIS 호털에 액세스하는 데 사용되는 이메일을 협력자 목록에 추가해야 합니다. 추가되면 앱이 LUIS 포털에 표시됩니다.

1. 맨 위 오른쪽 메뉴에서 **관리**를 선택한 다음, 왼쪽 메뉴에서 **협력자**를 선택합니다.

1. 도구 모음에서 **협력자 추가**를 선택합니다.

1. 협력자가 LUIS 포털에 로그인할 때 사용하는 이메일 주소를 입력합니다.

    ![협력자의 이메일 주소를 추가합니다.](./media/luis-how-to-collaborate/add-collaborator-pop-up.png)


### <a name="users-with-multiple-emails"></a>이메일이 여러 개인 사용자 

LUIS 앱에 기여자/공동 작업자를 추가하면 정확한 이메일 주소를 지정합니다. Azure Active Directory(Azure AD)를 사용하면 단일 사용자가 두 개 이상의 전자 메일 계정을 상호 교환하여 사용할 수 있지만 LUIS는 사용자가 기여자/공동 작업자를 추가할 때 지정된 전자 메일 주소로 로그인해야 합니다.

<a name="owner-and-collaborators"></a>

### <a name="azure-active-directory-resources"></a>Azure Active Directory 리소스

조직에서 Azure AD([Azure Active Directory](https://docs.microsoft.com/azure/active-directory/))를 사용하는 경우 사용자가 LUIS를 사용하려고 할 때 LUIS에 사용자의 액세스 정보에 대한 권한이 필요합니다. LUIS에서는 최소한의 리소스를 필요로 합니다. 

관리자가 동의하거나 관리자의 동의가 필요하지 않은 계정으로 가입하려고 하면 자세한 설명이 표시됩니다.

* 조직 계정으로 앱에 로그인하고 앱이 프로필을 읽도록 할 수 있습니다. 또한 앱이 기본 회사 정보를 읽도록 할 수 있습니다. LUIS에 사용자 ID, 이메일, 이름과 같은 기본 프로필 데이터를 읽을 수 있는 권한을 부여합니다.
* 현재 앱을 사용하고 있지 않을 때도 앱이 데이터를 표시하고 업데이트하도록 할 수 있습니다. 사용자의 액세스 토큰을 새로 고치는 데 권한이 필요합니다.


### <a name="azure-active-directory-tenant-user"></a>Azure Active Directory 테넌트 사용자

LUIS는 표준 Azure AD(Azure Active Directory)에서 동의 흐름을 사용합니다. 

테넌트 관리자는 Azure AD에서 LUIS를 사용하기 위해 권한을 부여 받아야 하는 사용자와 함께 작업해야 합니다. 

* 첫째, 사용자가 LUIS에 로그인하면 관리 승인이 필요한 팝업 대화 상자가 표시됩니다. 사용자는 계속하기 위해 먼저 테넌트 관리자를 연락합니다. 
* 둘째, 테넌트 관리자가 LUIS에 로그인하면 동의 흐름 팝업 대화 상자가 표시됩니다. 이 대화 상자에서 관리자는 사용자에게 권한을 부여해야 합니다. 관리자가 권한을 수락하면 사용자는 LUIS를 계속 사용할 수 있습니다. 테넌트 관리자가 LUIS에 로그인하지 않을 경우 관리자는 다음 스크린샷에 나온 LUIS에 대한 [동의](https://account.activedirectory.windowsazure.com/r#/applications)에 액세스할 수 있습니다. `LUIS` 이름이 포함된 항목으로 목록이 필터링됩니다.

![앱 웹 사이트별 Azure Active Directory 권한](./media/luis-how-to-collaborate/tenant-permissions.png)

테넌트 관리자는 특정 사용자만 LUIS를 사용하도록 지정하려는 경우 다음과 같은 몇 가지 방법을 사용할 수 있습니다.
* "관리자 동의"(Azure AD의 모든 사용자에 대한 동의)를 제공한 다음 엔터프라이즈 애플리케이션 속성 아래의 "사용자 할당 필요"를 "예"로 설정하고, 마지막으로 원하는 사용자만 애플리케이션에 할당/추가합니다. 이 방법을 사용하는 경우에도 관리자는 앱에 "관리자 동의"를 제공하지만, 앱에 액세스할 수 있는 사용자를 제어할 수 있습니다.
* 두 번째 해결책은 [Microsoft 그래프의 Azure AD ID 및 액세스 관리 API를](https://docs.microsoft.com/graph/azuread-identity-access-management-concept-overview) 사용하여 각 특정 사용자에게 동의를 제공하는 것입니다. 

Azure Active Directory 사용자 및 동의에 대해 자세히 알아보세요. 
* 특정 사용자 세트만 사용 가능하도록 [앱 제한](../../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md)

## <a name="next-steps"></a>다음 단계

* [버전을 사용하여](luis-how-to-manage-versions.md) 앱 수명 주기를 제어하는 방법을 알아보세요.
* 해당 [리소스의 작성 리소스](luis-concept-keys.md#authoring-key) 및 참여자 등의 개념을 [이해합니다.](luis-concept-keys.md#contributions-from-other-authors)
* 작성 및 런타임 [리소스를 만드는 방법](luis-how-to-azure-subscription.md) 알아보기
* 새 [작성 리소스로](luis-migration-authoring.md) 마이그레이션 
