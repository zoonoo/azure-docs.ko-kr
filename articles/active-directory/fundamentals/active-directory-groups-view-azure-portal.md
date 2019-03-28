---
title: 그룹 및 멤버를 보기 위한 빠른 시작 - Azure Active Directory | Microsoft Docs
description: 조직의 그룹 및 할당된 멤버를 검색하고 보는 방법을 설명하는 지침입니다.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: quickstart
ms.date: 09/24/2018
ms.author: lizross
ms.custom: it-pro, seodec18, seo-update-azuread-jan
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8eef6f7a363fe7b020a3ef18ae26799d7d5452ed
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2019
ms.locfileid: "57881013"
---
<!--As a brand-new Azure AD administrator, I need to view my organization’s groups along with the assigned members, so I can manage permissions to apps and services for people in my organization-->

# <a name="quickstart-view-your-organizations-groups-and-members-in-azure-active-directory"></a>빠른 시작: Azure Active Directory에서 조직의 그룹 및 멤버 보기
Azure Portal을 사용하여 조직의 기존 그룹 및 그룹 멤버를 볼 수 있습니다. 그룹은 잠재적으로 제한된 앱 및 서비스에 대해 동일한 액세스 및 권한을 필요로 하는 사용자(멤버)를 관리하는 데 사용됩니다.

이 빠른 시작에서는 조직의 모든 기존 그룹을 보고 할당된 멤버를 봅니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/) 을 만듭니다. 

## <a name="prerequisites"></a>필수 조건
시작하기 전에 다음이 필요합니다.

- Azure Active Directory 테넌트를 만듭니다. 자세한 내용은 [Azure Active Directory 포털 액세스 및 새 테넌트 만들기](active-directory-access-create-new-tenant.md)를 참조하세요.

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인
해당 디렉터리에 대한 글로벌 관리자 계정을 사용하여 [Azure Portal](https://portal.azure.com/)에 로그인해야 합니다.

## <a name="create-a-new-group"></a>새 그룹 만들기 
_MDM policy - West_라는 이름의 새 그룹을 만듭니다. 그룹 만들기에 대한 자세한 내용은 [기본 그룹을 만들고 멤버를 추가하는 방법](active-directory-groups-create-azure-portal.md)을 참조하세요.

1. **Azure Active Directory**, **그룹**을 선택한 다음, **새 그룹**을 선택합니다.

2. **그룹** 페이지를 완료합니다.
    
    - **그룹 유형:** **보안** 선택
    
    - **그룹 이름:** _MDM policy - West_ 입력
    
    - **멤버 자격 유형:** **할당됨** 선택

3. **만들기**를 선택합니다.

## <a name="create-a-new-user"></a>새 사용자 만들기
_Alain Charon_이라는 새 사용자를 만듭니다. 사용자는 그룹 멤버로 추가되기 전에 존재해야 합니다. 사용자 만들기에 대한 자세한 내용은 [사용자 추가 또는 삭제 방법](add-users-azure-active-directory.md)을 참조하세요.

1. **Azure Active Directory**, **사용자**를 선택한 다음, **새 사용자**를 선택합니다.

2. **사용자** 페이지를 완료합니다.

    - **이름:** _Alain Charon_ 입력

    - **사용자 이름:** *alain\@contoso.com*을 입력합니다.

3. **암호** 상자에 제공된 자동 생성된 암호를 복사한 다음, **만들기**를 선택합니다.

## <a name="add-a-group-member"></a>그룹 멤버 추가
이제 그룹 및 사용자가 생겼으며 _Alain Charon_을 _MDM policy - West_ 그룹에 멤버로 추가할 수 있습니다. 그룹 멤버 추가에 대한 자세한 내용은 [그룹 멤버 추가 또는 제거 방법](active-directory-groups-members-azure-portal.md)을 참조하세요.

1. **Azure Active Directory** > **그룹**을 선택합니다.

2. **그룹 - 모든 그룹** 페이지에서 **MDM policy - West** 그룹을 검색하여 선택합니다.

3. **MDM policy - West 개요** 페이지의 **관리** 영역에서 **멤버**를 선택합니다.

4. **멤버 추가**를 선택한 다음, **Alain Charon**을 검색하여 선택합니다.

5. **선택**을 선택합니다.

## <a name="view-all-groups"></a>모든 그룹 보기
Azure Portal의 **그룹 - 모든 그룹** 페이지에서 조직의 모든 그룹을 볼 수 있습니다.

- **Azure Active Directory** > **그룹**을 선택합니다.

    모든 활성 그룹을 보여 주는 **그룹 - 모든 그룹** 페이지가 나타납니다.

    ![모든 기존 그룹을 보여 주는 [그룹 - 모든 그룹] 페이지](media/active-directory-groups-view-azure-portal/groups-all-groups-blade-with-all-groups.png)

## <a name="search-for-the-group"></a>그룹 검색
**그룹 - 모든 그룹** 페이지를 검색하여 **MDM policy - West** 그룹을 찾습니다.

1. **그룹 - 모든 그룹** 페이지에서 **검색** 상자에 _MDM_을 입력합니다.

    _MDM policy - West_ 그룹을 포함한 검색 결과가 **검색** 상자 아래에 나타납니다.

    ![검색 상자가 입력된 [그룹 - 모든 그룹] 페이지](media/active-directory-groups-view-azure-portal/search-for-specific-group.png)

3. **MDM policy – West** 그룹을 선택합니다.

4. **MDM policy - West 개요** 페이지에서 해당 그룹의 멤버 수를 포함한 그룹 정보를 확인합니다.

    ![멤버 정보가 있는 [MDM policy – West 개요] 페이지](media/active-directory-groups-view-azure-portal/group-overview-blade.png)

## <a name="view-group-members"></a>그룹 멤버 보기
그룹을 찾았으므로 모든 할당된 멤버를 볼 수 있습니다.

- **관리** 영역에서 **멤버**를 선택한 다음, _Alain Charon_ 을 포함하여 특정 그룹에 할당된 전체 멤버 이름 목록을 검토합니다.

    ![MDM policy – West 그룹에 할당된 멤버 목록](media/active-directory-groups-view-azure-portal/groups-all-members.png)

## <a name="clean-up-resources"></a>리소스 정리
이 그룹은 이 설명서의 **방법 가이드** 섹션에 제공되는 여러 방법 프로세스에 사용됩니다. 그러나 차라리 이 그룹을 사용하지 않으려면 다음 단계를 사용하여 해당 그룹과 할당된 멤버를 삭제할 수 있습니다.

1. **그룹 - 모든 그룹** 페이지에서 **MDM policy - West** 그룹을 검색합니다.

2.  **MDM policy – West** 그룹을 선택합니다.

    **MDM policy - West 개요** 페이지가 나타납니다.

3. **삭제**를 선택합니다.

    그룹 및 연결된 멤버가 삭제됩니다.

    ![[삭제] 링크가 강조 표시된 [MDM policy – West 개요] 페이지](media/active-directory-groups-view-azure-portal/group-overview-blade-delete.png)

    >[!Important]
    >삭제된 그룹의 멤버 자격인 Alain Charon 사용자는 삭제되지 않습니다.

## <a name="next-steps"></a>다음 단계
다음 문서로 이동하여 Azure AD 디렉터리에 구독을 연결하는 방법에 대해 알아보세요.

> [!div class="nextstepaction"]
> [Azure 구독 연결](active-directory-how-subscriptions-associated-directory.md)
