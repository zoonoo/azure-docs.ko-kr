---
title: Azure의 LUIS 앱에서 다른 참가자와 공동 작업 | Microsoft Docs
description: LUIS(Language Understanding) 응용 프로그램에서 다른 참가자와 공동으로 작업하는 방법을 알아봅니다.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 07/31/2018
ms.author: diberry
ms.openlocfilehash: 99f37cb6dc5e05fc5eb4bde09685435ee57fecc6
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/01/2018
ms.locfileid: "39397791"
---
# <a name="how-to-manage-authors-and-collaborators"></a>작성자 및 협력자를 관리하는 방법 

LUIS 앱에서 다른 사용자와 공동으로 작업할 수 있습니다. 

## <a name="owner-and-collaborators"></a>소유자 및 협력자

앱의 작성자, 소유자는 한 명이지만 협력자는 많을 수 있습니다. 

## <a name="add-collaborator"></a>협력자 추가

협력자가 LUIS 앱을 편집할 수 있도록 허용하려면 LUIS 앱의 **설정** 페이지에서 협력자의 메일을 입력하고 **협력자 추가**를 클릭합니다. 협력자는 사용자가 앱에서 작업하는 동안 로그인하여 LUIS 앱을 편집할 수 있습니다.

![협력자 추가](./media/luis-how-to-collaborate/add-collaborator.png)

## <a name="transfer-of-ownership"></a>소유권 이전

LUIS는 현재 소유권 이전을 지원하지 않지만, 앱을 내보내고 다른 LUIS 사용자가 앱을 가져올 수 있습니다. 두 응용 프로그램의 LUIS 점수가 약간 다를 수도 있습니다. 

## <a name="azure-active-directory-tenant-user"></a>Azure Active Directory 테넌트 사용자

LUIS는 표준 Azure AD(Azure Active Directory)에서 동의 흐름을 사용합니다. 

테넌트 관리자는 Azure AD에서 LUIS를 사용하기 위해 권한을 부여 받아야 하는 사용자와 함께 작업해야 합니다. 

첫째, 사용자가 LUIS에 로그인하면 관리 승인이 필요한 팝업 대화 상자가 표시됩니다. 사용자는 계속하기 위해 먼저 테넌트 관리자를 연락합니다. 

둘째, 테넌트 관리자가 LUIS에 로그인하면 동의 흐름 팝업 대화 상자가 표시됩니다. 이 대화 상자에서 관리자는 사용자에게 권한을 부여해야 합니다. 관리자가 권한을 수락하면 사용자는 LUIS를 계속 사용할 수 있습니다.

테넌트 관리자는 LUIS에 로그인하지 않을 경우 LUIS에 대한 [동의](https://account.activedirectory.windowsazure.com/r#/applications)에 액세스할 수 있습니다. 

![앱 웹 사이트별 Azure Active Directory 권한](./media/luis-how-to-account-settings/tenant-permissions.png)

테넌트 관리자가 특정 사용자만 LUIS를 사용하도록 지정하려고 할 경우 [ID 블로그](https://blogs.technet.microsoft.com/tfg/2017/10/15/english-tips-to-manage-azure-ad-users-consent-to-applications-using-azure-ad-graph-api/)를 참조하세요.

### <a name="user-accounts-with-multiple-emails-for-collaborators"></a>공동 작업자를 위한 여러 전자 메일에 있는 사용자 계정

LUIS 앱에 공동 작업자를 추가하는 경우 공동 작업자가 LUIS를 공동 작업자로 사용하기 위해 필요한 정확한 전자 메일 주소를 지정해야 합니다. Azure AD(Azure Active Directory)에서는 단일 사용자가 둘 이상의 이메일 계정을 함께 사용하도록 허용하지만, LUIS는 사용자가 협력자 목록에 지정된 이메일 주소를 사용하여 로그인하도록 요구합니다.