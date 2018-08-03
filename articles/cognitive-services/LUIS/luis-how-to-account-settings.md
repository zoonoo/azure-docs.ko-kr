---
title: LUIS에서 계정 설정 관리 | Microsoft Docs
description: LUIS 웹 사이트를 사용하여 계정 설정을 관리합니다.
titleSuffix: Azure
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 07/08/2018
ms.author: diberry
ms.openlocfilehash: 963a7f8c196702ea899ddfe31e6187a15eb5f683
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/24/2018
ms.locfileid: "39223211"
---
# <a name="manage-account-and-authoring-key"></a>계정 및 작성 키 관리
LUIS 계정에 대한 두 가지 주요 정보는 사용자 계정과 작성 키입니다. 로그인 정보는 [account.microsoft.com](https://account.microsoft.com)에서 관리됩니다. 작성 키는 [LUIS](luis-reference-regions.md) 웹 사이트 **설정** 페이지에서 관리됩니다. 

## <a name="authoring-key"></a>작성 키

**설정** 페이지에 있는 이 지역별 단일 작성 키를 사용하면 [LUIS](luis-reference-regions.md) 웹 사이트와 [작성 API](https://aka.ms/luis-authoring-api)를 통해 모든 앱을 작성할 수 있습니다. 편의를 위해 작성 키를 사용하여 매월 [제한된](luis-boundaries.md) 수의 끝점 쿼리를 만들 수 있습니다. 

![LUIS 설정 페이지](./media/luis-how-to-account-settings/account-settings.png)

작성 키는 사용자가 공동 작업자로 나열된 모든 앱 뿐만 아니라 사용자가 소유하는 모든 앱에서 사용됩니다.

## <a name="authoring-key-regions"></a>작성 키 지역
작성 키는 [작성 지역](luis-reference-regions.md#publishing-regions)에 국한합니다. 이 키는 다른 지역에서는 작동하지 않습니다. 

## <a name="reset-authoring-key"></a>작성 키 다시 설정
작성 키가 손상되면 키를 다시 설정합니다. 이 키는 [LUIS](luis-reference-regions.md) 웹 사이트의 모든 앱에서 다시 설정됩니다. 작성 API를 통해 앱을 작성하는 경우 `Ocp-Apim-Subscription-Key` 값을 새 키로 변경해야 합니다. 

## <a name="delete-account"></a>계정 삭제
계정을 삭제할 때 삭제되는 데이터에 대한 내용은 [데이터 저장 및 제거](luis-concept-data-storage.md#accounts)를 참조하세요. 

## <a name="azure-active-directory-tenant-user"></a>Azure Active Directory 테넌트 사용자
LUIS는 표준 Azure AD(Azure Active Directory)에서 동의 흐름을 사용합니다. 

테넌트 관리자는 Azure AD에서 LUIS를 사용하기 위해 권한을 부여 받아야 하는 사용자와 함께 작업해야 합니다. 

첫째, 사용자가 LUIS에 로그인하면 관리 승인이 필요한 팝업 대화 상자가 표시됩니다. 사용자는 계속하기 위해 먼저 테넌트 관리자를 연락합니다. 

둘째, 테넌트 관리자가 LUIS에 로그인하면 동의 흐름 팝업 대화 상자가 표시됩니다. 이 대화 상자에서 관리자는 사용자에게 권한을 부여해야 합니다. 관리자가 권한을 수락하면 사용자는 LUIS를 계속 사용할 수 있습니다.

테넌트 관리자는 LUIS에 로그인하지 않을 경우 LUIS에 대한 [동의](https://account.activedirectory.windowsazure.com/r#/applications)에 액세스할 수 있습니다. 

![앱 웹 사이트별 Azure Active Directory 권한](./media/luis-how-to-account-settings/tenant-permissions.png)

테넌트 관리자가 특정 사용자만 LUIS를 사용하도록 지정하려고 할 경우 [ID 블로그](https://blogs.technet.microsoft.com/tfg/2017/10/15/english-tips-to-manage-azure-ad-users-consent-to-applications-using-azure-ad-graph-api/)를 참조하세요.

### <a name="user-accounts-with-multiple-emails-for-collaborators"></a>공동 작업자를 위한 여러 전자 메일에 있는 사용자 계정
LUIS 앱에 공동 작업자를 추가하는 경우 공동 작업자가 LUIS를 공동 작업자로 사용하기 위해 필요한 정확한 전자 메일 주소를 지정해야 합니다. Azure AD(Azure Active Directory)에서는 단일 사용자가 둘 이상의 전자 메일 계정을 함께 사용하도록 허용하지만, LUIS는 사용자가 공동 작업자 목록에 지정된 전자 메일 주소로 로그인하도록 요구합니다. 


## <a name="next-steps"></a>다음 단계

[작성 키](luis-concept-keys.md#authoring-key)에 대해 자세히 알아봅니다. 

