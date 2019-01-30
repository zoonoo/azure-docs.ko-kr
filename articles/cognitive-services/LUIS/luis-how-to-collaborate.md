---
title: 다른 사람과 공동 작업
titleSuffix: Language Understanding - Azure Cognitive Services
description: 앱 소유자는 앱에 협력자를 추가할 수 있습니다. 이러한 협력자는 모델을 수정하고, 앱을 학습시키고, 게시할 수 있습니다.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: d1db8974ce134b50340db500c9ea1b00126fe10a
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53086422"
---
# <a name="how-to-manage-authors-and-collaborators"></a>작성자 및 협력자를 관리하는 방법 

앱 소유자는 앱에 협력자를 추가할 수 있습니다. 이러한 협력자는 모델을 수정하고, 앱을 학습시키고, 게시할 수 있습니다. 

<a name="owner-and-collaborators"></a>

## <a name="add-collaborator"></a>협력자 추가

앱의 작성자, 소유자는 한 명이지만 협력자는 많을 수 있습니다. 협력자가 LUIS 앱을 편집하도록 허용하려면 LUIS 호털에 액세스하는 데 사용되는 이메일을 협력자 목록에 추가해야 합니다. 추가되면 앱이 LUIS 포털에 표시됩니다.

1. 맨 위 오른쪽 메뉴에서 **관리**를 선택한 다음, 왼쪽 메뉴에서 **협력자**를 선택합니다.

2. 도구 모음에서 **협력자 추가**를 선택합니다.

    [![협력자 추가](./media/luis-how-to-collaborate/add-collaborator.png "협력자 추가")](./media/luis-how-to-collaborate/add-collaborator.png#lightbox)

3. 협력자가 LUIS 포털에 로그인할 때 사용하는 이메일 주소를 입력합니다.

    ![협력자의 이메일 주소를 추가합니다.](./media/luis-how-to-collaborate/add-collaborator-pop-up.png)

## <a name="transfer-of-ownership"></a>소유권 이전

LUIS는 현재 소유권 이전을 지원하지 않지만, 앱을 내보내고 다른 LUIS 사용자가 앱을 가져올 수 있습니다. 두 응용 프로그램의 LUIS 점수가 약간 다를 수도 있습니다. 

## <a name="azure-active-directory-resources"></a>Azure Active Directory 리소스

조직에서 Azure Active Directory(Azure AD)를 사용하는 경우 사용자가 LUIS를 사용하려고 할 때 LUIS에 사용자의 액세스 정보에 대한 권한이 필요합니다. LUIS에서는 최소한의 리소스를 필요로 합니다. 

관리자가 동의하거나 관리자의 동의가 필요하지 않은 계정으로 가입하려고 하면 자세한 설명이 표시됩니다.

* 조직 계정으로 앱에 로그인하고 앱이 프로필을 읽도록 할 수 있습니다. 또한 앱이 기본 회사 정보를 읽도록 할 수 있습니다.
* 현재 앱을 사용하고 있지 않을 때도 앱이 데이터를 표시하고 업데이트하도록 할 수 있습니다.

첫 번째 권한은 LUIS에 사용자 ID, 이메일, 이름과 같은 기본 프로필 데이터를 읽을 수 있는 권한을 부여합니다. 두 번째 권한은 사용자의 액세스 토큰을 새로 고치는 데 필요합니다.

## <a name="azure-active-directory-tenant-user"></a>Azure Active Directory 테넌트 사용자

LUIS는 표준 Azure AD(Azure Active Directory)에서 동의 흐름을 사용합니다. 

테넌트 관리자는 Azure AD에서 LUIS를 사용하기 위해 권한을 부여 받아야 하는 사용자와 함께 작업해야 합니다. 

첫째, 사용자가 LUIS에 로그인하면 관리 승인이 필요한 팝업 대화 상자가 표시됩니다. 사용자는 계속하기 위해 먼저 테넌트 관리자를 연락합니다. 

둘째, 테넌트 관리자가 LUIS에 로그인하면 동의 흐름 팝업 대화 상자가 표시됩니다. 이 대화 상자에서 관리자는 사용자에게 권한을 부여해야 합니다. 관리자가 권한을 수락하면 사용자는 LUIS를 계속 사용할 수 있습니다.

테넌트 관리자는 LUIS에 로그인하지 않을 경우 LUIS에 대한 [동의](https://account.activedirectory.windowsazure.com/r#/applications)에 액세스할 수 있습니다. 

![앱 웹 사이트별 Azure Active Directory 권한](./media/luis-how-to-collaborate/tenant-permissions.png)

테넌트 관리자가 특정 사용자만 LUIS를 사용하도록 지정하려고 할 경우 [ID 블로그](https://blogs.technet.microsoft.com/tfg/2017/10/15/english-tips-to-manage-azure-ad-users-consent-to-applications-using-azure-ad-graph-api/)를 참조하세요.

### <a name="user-accounts-with-multiple-emails-for-collaborators"></a>공동 작업자를 위한 여러 전자 메일에 있는 사용자 계정

LUIS 앱에 공동 작업자를 추가하는 경우 공동 작업자가 LUIS를 공동 작업자로 사용하기 위해 필요한 정확한 전자 메일 주소를 지정해야 합니다. Azure AD(Azure Active Directory)에서는 단일 사용자가 둘 이상의 이메일 계정을 함께 사용하도록 허용하지만, LUIS는 사용자가 협력자 목록에 지정된 이메일 주소를 사용하여 로그인하도록 요구합니다.

