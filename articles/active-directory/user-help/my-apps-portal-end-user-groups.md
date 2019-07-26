---
title: 내 앱 포털에서 그룹 정보 업데이트-Azure Active Directory | Microsoft Docs
description: 소유 하 고 있는 그룹을 보고, 새 그룹을 만들고, 이미 구성원 인 그룹을 보고, 이미 속해 있지 않은 그룹을 조인 하는 등 그룹 관련 정보를 보고 업데이트 하는 방법에 대해 알아봅니다.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 3/21/2019
ms.author: lizross
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: a11b43d040dec838de350c23614ae42b6756ec6e
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68383175"
---
# <a name="update-your-groups-info-from-the-my-apps-portal"></a>내 앱 포털에서 그룹 정보 업데이트

웹 기반 **My Apps** 포털을 통해 회사 또는 학교 계정을 사용하여 다양한 조직의 클라우드 기반 앱을 확인 및 시작하고, 일부 프로필 및 계정 정보를 업데이트하고, **그룹** 정보를 보고, 앱 및 그룹에 대한 **액세스 검토**를 수행할 수 있습니다. **My Apps** 포털에 액세스할 수 없는 경우 사용 권한에 대해 기술 지원팀에 문의해야 합니다.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

>[!Important]
>이 콘텐츠는 사용자를 위한 것입니다. 관리자인 경우 [애플리케이션 관리 설명서](https://docs.microsoft.com/azure/active-directory/manage-apps)에서 클라우드 기반 앱을 설정 및 관리하는 방법에 대한 더 자세한 정보를 확인할 수 있습니다.

## <a name="view-your-groups-information"></a>그룹 정보 보기

관리자에 게 **그룹** 타일을 볼 수 있는 권한이 부여 된 경우 다음을 수행할 수 있습니다.

- **그룹 구성원으로.** 세부 정보를 보거나 그룹을 그대로 둡니다.

- **그룹 소유자로.** 세부 정보를 보거나, 새 그룹을 만들거나, 구성원을 추가 또는 제거 하거나, 그룹을 삭제 합니다.

### <a name="to-view-your-groups-information"></a>그룹 정보를 보려면

1. 회사 또는 학교 계정에 로그인합니다.

2. 웹 브라우저를 열고로 https://myapps.microsoft.com 이동 하거나 조직에서 제공한 링크를 사용 합니다. 예를 들어와 https://myapps.microsoft.com/contoso.com 같은 조직의 사용자 지정 페이지로 이동할 수 있습니다.

    조직에서 소유 하 고 사용할 수 있는 모든 클라우드 기반 앱을 보여 주는 **앱** 페이지가 나타납니다.

    ![내 앱 포털의 앱 페이지](media/my-apps-portal/my-apps-portal-apps-page-groups-tile.png)

3. 그룹 타일 **을** 선택 하 여 그룹 관련 정보를 확인 합니다.

    ![소유 그룹과 구성원 그룹이 모두 포함 된 그룹 페이지](media/my-apps-portal/my-apps-portal-groups-page.png)

4. 사용 권한에 따라 **그룹** 페이지를 사용 하 여 다음 작업을 수행할 수 있습니다.

    - **소유 하 고 있는 그룹을 검토 합니다.** 조직 내에서 소유 하 고 있는 **그룹** 에 대 한 정보를 확인 합니다. 특정 그룹 이름을 선택 하면 그룹 유형, 구성원 수, 조인 정책 및 활성 멤버 목록을 포함 하 여 그룹에 대 한 자세한 정보가 제공 됩니다.

    - **새 그룹을 만듭니다.** 소유 하 고 있는 **그룹** 의 소유자로 새 그룹을 만듭니다. 특정 단계는이 문서의 [새 그룹 만들기](#create-a-new-group) 섹션을 참조 하세요.

    - **기존 그룹을 편집 합니다.** 사용자 고유의 그룹에 대 한 세부 정보를 편집 합니다. 특정 단계는이 문서의 [기존 그룹 편집](#edit-an-existing-group) 섹션을 참조 하세요.

    - **멤버를 추가 하거나 제거 합니다.** 소유 하 고 있는 그룹의 구성원을 추가 하거나 제거 합니다. 특정 단계는이 문서의 [구성원 추가 또는 제거](#add-or-remove-a-member) 섹션을 참조 하세요.

    - **Office 365 그룹을 갱신 합니다.** 조직에서 허용 하는 경우 Office 365 그룹을 갱신할 수 있습니다. 특정 단계는이 문서의 [Office 365 그룹 갱신](#renew-an-office-365-group) 섹션을 참조 하세요. 

    - **그룹을 삭제 합니다.** 소유 하 고 있는 그룹을 삭제 합니다. 특정 단계는이 문서의 [그룹 삭제](#delete-a-group) 섹션을 참조 하세요.

    - **속해 있는 그룹을 검토 합니다.** **내가 속한 그룹** 에서 멤버로 속한 모든 그룹의 이름을 표시 합니다. 특정 그룹 이름을 선택 하면 그룹 유형, 구성원 수, 조인 정책 및 활성 멤버 목록을 포함 하 여 그룹에 대 한 자세한 정보가 제공 됩니다.

    - **그룹에 가입 합니다.** 이미 구성원으로 속해 있지 않은 기존 그룹은 **내가 속한 그룹에** 참가 합니다. 특정 단계는 [기존 그룹에 가입](#join-an-existing-group)을 참조 하세요.

## <a name="create-a-new-group"></a>새 그룹 만들기

1. **그룹** 페이지에서 소유 하는 **그룹** 영역에서 **그룹 만들기** 를 선택 합니다.

    **그룹 만들기** 상자가 나타납니다.

    ![그룹 상자 만들기](media/my-apps-portal/my-apps-portal-create-group-page.png)

2. 필요한 정보를 입력합니다.

    - **그룹 유형:**

        - **보안.** 사용자 그룹의 공유 리소스에 대한 멤버 및 컴퓨터 액세스를 관리하는 데 사용됩니다. 예를 들어 특정 보안 정책의 보안 그룹을 만들 수 있습니다. 이렇게 하면 각 멤버에 개별적으로 권한을 추가할 필요 없이 한 번에 모든 멤버에게 권한 집합을 제공할 수 있습니다.

        - **Office 365.** 공유 사서함, 일정, 파일, SharePoint 사이트 등에 대한 액세스를 멤버에게 부여하여 협업 기회를 제공합니다. 이 옵션을 통해 조직 외부에 있는 사용자에게 그룹에 대한 액세스를 제공할 수 있습니다.

    - **그룹 이름.** 기억하기 쉽고 의미 있는 그룹 이름을 추가합니다.

    - **그룹 설명 (선택 사항).** 그룹에 선택적 설명을 추가합니다.

    - **그룹 정책.** 모든 사용자가 그룹에 가입 하도록 허용 하거나 그룹 소유자만 구성원을 추가 하도록 허용 하려면 선택 합니다.

3. **만들기**를 선택합니다.

    새 그룹은 사용자로 소유자에 게 생성 되 고 **그룹 소유** 목록에 표시 됩니다. 소유자 이기 때문에이 그룹은 내가 내가 만든 **그룹** 목록에도 표시 됩니다.

## <a name="edit-an-existing-group"></a>기존 그룹 편집

그룹을 만든 후에는 기존 정보를 업데이트 하는 등의 세부 정보를 편집할 수 있습니다.

### <a name="to-edit-your-details"></a>세부 정보를 편집 하려면

1. **그룹** 페이지에서 편집 하려는 그룹을 선택 하 고 *&lt;group_name&gt;* 페이지에서 **세부 정보 편집** 을 선택 합니다.

    **세부 정보 편집** 상자가 나타나고 처음 그룹을 만들 때 추가한 정보를 업데이트할 수 있습니다.

2. 모든 변경을 수행 하 고 **업데이트**를 선택 합니다.

## <a name="add-or-remove-a-member"></a>구성원 추가 또는 제거

그룹에 대 한 구성원을 추가 하거나 제거할 수 있습니다.

### <a name="to-add-or-remove-a-member"></a>멤버를 추가 하거나 제거 하려면

1. 멤버를 추가 하려는 그룹을 선택 하 고 *&lt;group_name&gt;* 페이지에서를 **+** 선택 합니다.

    ![\+ 기호가 강조 표시 된 그룹 구성원 추가](media/my-apps-portal/my-apps-portal-add-member-link.png)

2. **멤버 추가** 상자에서 추가 하려는 멤버를 검색 한 다음 **추가**를 선택 합니다.

    ![추가할 새 멤버가 있는 멤버 추가 상자](media/my-apps-portal/my-apps-portal-add-member-page.png)

    조직의 앱에 대 한 액세스를 시작 하기 위해 새 구성원에 게 초대가 전송 됩니다.

3. 실수로 구성원을 추가 했거나 구성원이 조직을 떠난 경우  *&lt;&gt; group_name* 페이지에서 멤버 이름 옆의 **멤버 제거** 를 선택 하 여 멤버를 제거할 수 있습니다.

    ![제거 링크가 강조 표시 된 멤버를 제거 합니다.](media/my-apps-portal/my-apps-portal-remove-member-link.png)

## <a name="renew-an-office-365-group"></a>Office 365 그룹 갱신

조직에서 허용 하는 경우 Office 365 그룹을 갱신 하 여 만료 날짜를 연장할 수 있습니다.

### <a name="to-renew-a-group"></a>그룹을 갱신 하려면

1. 갱신 하려는 Office 365 그룹을 선택 하 고 **그룹 갱신**을 선택 합니다.

    ![Office 365 그룹 갱신, 만료 날짜 연장](media/my-apps-portal/my-apps-portal-renew-group-link.png)

2. **확인** 을 클릭 하 여 확인 메시지를 닫습니다.

    페이지를 새로 고치면 업데이트 된 **마지막 갱신** 및 **그룹 만료** 날짜가 표시 됩니다.

## <a name="delete-a-group"></a>그룹 삭제

언제 든 지 사용자 자신의 그룹을 삭제할 수 있습니다. 그러나 그룹을 실수로 삭제 하는 경우에는 그룹을 만들고 멤버를 다시 추가 해야 합니다.

### <a name="to-delete-the-group"></a>그룹을 삭제 하려면

1. 영구적으로 삭제 하려는 그룹을 선택 하 고  *&lt;&gt; group_name* 페이지에서 **그룹 삭제** 를 선택 합니다.

    ![그룹 삭제 링크가 강조 표시 된 < Group_name > 페이지](media/my-apps-portal/my-apps-portal-delete-group-link.png)

2. 확인 메시지에서 **예** 를 선택 합니다.

    그룹이 영구적으로 삭제 됩니다.

## <a name="join-an-existing-group"></a>기존 그룹에 가입

**그룹** 페이지에서 이미 존재 하는 그룹을 조인할 수 있습니다.

### <a name="to-join-or-leave-a-group"></a>그룹에 참가 하거나 그룹을 탈퇴 하려면

1. **그룹** 페이지의 **내가 속한 그룹** 에서 **조인 그룹** 을 선택 합니다.

    **조인 그룹** 페이지가 나타납니다.

    ![조인 그룹 단추가 강조 표시 된 조인 그룹 페이지](media/my-apps-portal/my-apps-portal-join-group-link.png)

2. **조인 그룹** 페이지에서 가입할 그룹의 이름을 선택 하 고 관련 그룹 정보를 표시 한 다음 그룹을 사용할 수 있는 경우 **조인 그룹**을 선택 합니다.

    그룹에서 멤버 자격을 승인 하기 위해 그룹 소유자가 필요한 경우 그룹에 조인 해야 하는 이유에 대 한 비즈니스 근거를 입력 하 고 **요청**을 선택 하 라는 메시지가 표시 됩니다. 그룹에 승인이 필요 하지 않은 경우에는 구성원으로 즉시 추가 되 고 그룹은 목록 **에** 있는 그룹에 표시 됩니다.

3. 그룹을 실수로 조인 했거나 더 이상 포함 하지 않아도 되는 경우 그룹 **가입** 페이지에서 그룹 이름을 선택 하 고 **그룹 나가기**를 선택할 수 있습니다.

    ![그룹 나가기 단추가 강조 표시 된 조인 그룹 페이지](media/my-apps-portal/my-apps-portal-leave-group-link.png)    

## <a name="next-steps"></a>다음 단계

- [내 앱 포털에서 앱에 액세스 하 고 앱을 사용](my-apps-portal-end-user-access.md)합니다.

- [프로필 정보를 변경](my-apps-portal-end-user-update-profile.md)합니다.

- 사용자 [고유의 액세스 검토를 수행](my-apps-portal-end-user-access-reviews.md)합니다.
