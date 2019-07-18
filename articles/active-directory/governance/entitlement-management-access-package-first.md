---
title: 자습서 - Azure AD 권한 관리(미리 보기)에서 첫 번째 액세스 패키지 만들기 - Azure Active Directory
description: Azure Active Directory 권한 관리(미리 보기)에서 첫 번째 액세스 패키지를 만드는 방법에 대한 단계별 자습서입니다.
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.subservice: compliance
ms.date: 04/27/2019
ms.author: rolyon
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 354af736d5896214848205f41e429d9bf2c49863
ms.sourcegitcommit: 8a681ba0aaba07965a2adba84a8407282b5762b2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2019
ms.locfileid: "64873526"
---
# <a name="tutorial-create-your-first-access-package-in-azure-ad-entitlement-management-preview"></a>자습서: Azure AD 권한 관리(미리 보기)에서 첫 번째 액세스 패키지 만들기

> [!IMPORTANT]
> Azure AD(Azure Active Directory) 권한 관리는 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

직원에게 필요한 모든 리소스(예: 그룹, 애플리케이션 및 사이트)에 대한 액세스를 관리하는 것은 조직에 중요한 기능입니다. 생산성 향상에 필요한 올바른 수준의 액세스 권한을 직원에게 부여했다가 더 이상 필요가 없으면 액세스 권한을 제거하는 것이 좋습니다.

이 자습서에서는 Woodgrove Bank에 IT관리자로 근무한다고 가정합니다. 내부 사용자가 셀프 서비스를 요청할 수 있는 웹 프로젝트용 리소스 패키지를 생성하라는 요청을 받았습니다. 요청에는 승인이 필요하며 사용자의 액세스 권한은 30일 후에 만료됩니다. 이 자습서의 경우 웹 프로젝트 리소스는 단일 그룹의 멤버 자격뿐이지만, 그룹, 애플리케이션 또는 SharePoint Online 사이트의 컬렉션일 수도 있습니다.

![시나리오 개요](./media/entitlement-management-access-package-first/elm-scenario-overview.png)

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 그룹을 리소스로 사용하여 액세스 패키지 만들기
> * 승인자 지정
> * 내부 사용자가 액세스 패키지를 요청하는 방법 보여주기
> * 액세스 요청 승인

Azure AD Premium P2 또는 Enterprise Mobility + Security E5 라이선스가 없으면, 무료 [Enterprise Mobility + Security E5 평가판](https://signup.microsoft.com/Signup?OfferId=87dd2714-d452-48a0-a809-d2f58c4f68b7&ali=1)을 만드세요.

## <a name="prerequisites"></a>필수 조건

Azure AD 권한 관리(미리 보기)를 사용하려면 다음 라이선스 중 하나가 있어야 합니다.

- Azure AD Premium P2
- EMS(Enterprise Mobility + Security) E5 라이선스

## <a name="step-1-set-up-users-and-group"></a>1단계: 사용자 및 그룹 설정

리소스 디렉터리에는 공유할 리소스가 하나 이상 있습니다. 이 단계에서는 Woodgrove Bank 디렉터리에 **엔지니어링 그룹**이라는 그룹을 만듭니다. 이것이 권한 관리에 대한 대상 리소스입니다. 내부 요청자도 설정합니다.

**필수 역할:** 전역 관리자 또는 사용자 관리자

![사용자 및 그룹 만들기](./media/entitlement-management-access-package-first/elm-users-groups.png)

1. 전역 관리자 또는 사용자 관리자로 [Azure Portal](https://portal.azure.com)에 로그인합니다.  

1. 왼쪽 탐색 메뉴에서 **Azure Active Directory**를 클릭합니다.

1. 다음 두 가지 사용자를 만들거나 구성합니다. 이 이름을 사용하거나 다른 이름을 사용할 수 있습니다. **Admin1**은 현재 로그인되어 있는 사용자일 수 있습니다.

    | Name | 디렉터리 역할 | 설명 |
    | --- | --- | --- |
    | **Admin1** | 전역 관리자<br/>또는<br/>제한된 관리자(사용자 관리자) | 관리자 및 승인자 |
    | **Requestor1** | 사용자 | 내부 요청자 |

    이 자습서에서는 관리자와 승인자가 같은 사람이지만 일반적으로 한 명 이상을 승인자로 지정합니다.

1. 멤버 자격 유형이 **할당됨**인 **엔지니어링 그룹**이라는 Azure AD 보안 그룹을 만듭니다.

    이 그룹은 권한 관리에 대한 대상 리소스가 됩니다. 그룹은 시작 시 멤버가 비어 있어야 합니다.

## <a name="step-2-create-an-access-package"></a>2단계: 액세스 패키지 만들기

액세스 패키지(*access package*)는 사용자가 프로젝트 작업을 하거나 자신의 업무를 수행할 때 필요한 모든 리소스의 번들입니다. 액세스 패키지는 카탈로그(*catalogs*)라는 컨테이너에 정의됩니다. 이 단계에서는 **일반** 카탈로그에 **웹 프로젝트 액세스 패키지**를 만듭니다.

**필수 역할:** 전역 관리자 또는 사용자 관리자

![액세스 패키지 만들기](./media/entitlement-management-access-package-first/elm-access-package.png)

1. Azure Portal의 왼쪽 탐색에서 **Azure Active Directory**를 클릭합니다.

1. 왼쪽 메뉴에서 **Identity Governance**를 클릭합니다.

1. 왼쪽 메뉴에서 **액세스 패키지**를 클릭합니다.  **액세스 거부됨**이 보이면, 해당 디렉터리에 Azure AD Premium P2 라이선스가 있는지 확인합니다.

1. **새 액세스 패키지**를 클릭합니다.

    ![Azure Portal에서 권한 관리](./media/entitlement-management-access-package-first/access-packages-list.png)

1. **기본** 탭에서 이름에 **웹 프로젝트 액세스 패키지**를 입력하고 설명에 **엔지니어링 웹 프로젝트용 액세스 패키**를 입력합니다.

1. **카탈로그** 드롭다운 목록은 **일반**으로 둡니다.

    ![새 액세스 패키지 - 기본 탭](./media/entitlement-management-access-package-first/basics.png)

1. **다음**을 클릭하여 **리소스 역할** 탭을 엽니다.

    이 탭에서 액세스 패키지에 포함할 사용 권한을 선택합니다.

1. **그룹**을 클릭합니다.

1. 그룹 선택 창에서 앞서 만든 **엔지니어링 그룹** 그룹을 찾아서 선택합니다.

    기본적으로 **일반** 카탈로그 안팎의 그룹을 볼 수 있습니다. **일반** 카탈로그 외부의 그룹을 선택하면 **일반** 카탈로그에 추가됩니다.

    ![새 액세스 패키지 - 리소스 역할 탭](./media/entitlement-management-access-package-first/resource-roles-select-groups.png)

1. **선택**을 클릭하여 그룹을 목록에 추가합니다.

1. **역할** 드롭다운 목록에서 **멤버**를 선택합니다.

    ![새 액세스 패키지 - 리소스 역할 탭](./media/entitlement-management-access-package-first/resource-roles.png)

1. **다음**을 클릭하여 **정책** 탭을 엽니다.

1. **첫 번째 정책 만들기** 토글을 **나중에**로 설정합니다.

    정책은 다음 섹션에서 만들게 됩니다.

    ![새 액세스 패키지 - 정책 탭](./media/entitlement-management-access-package-first/policy.png)

1. **다음**을 클릭하여 **리뷰 + 만들기** 탭을 엽니다.

    ![새 액세스 패키지 - 리뷰 + 만들기 탭](./media/entitlement-management-access-package-first/review-create.png)

1. 액세스 패키지 설정을 검토한 다음, **만들기**를 클릭합니다.

    카탈로그가 아직 활성화되지 않아서 액세스 패키지가 사용자에게 표시되지 않는다는 메시지가 보일 수 있습니다.

    ![새 액세스 패키지 - 표시 안 됨 메시지](./media/entitlement-management-access-package-first/not-visible.png)

1. **확인**을 클릭합니다.

    잠시 후 액세스 패키지를 성공적으로 만들었다는 알림이 표시됩니다.

## <a name="step-3-create-a-policy"></a>3단계: 정책 만들기

정책(*policy*)은 액세스 패키지에 액세스하기 위한 규칙 또는 보호책을 정의합니다. 이 단계에서는 리소스 디렉터리의 특정 사용자가 액세스 패키지를 요청할 수 있는 정책을 만듭니다. 요청을 승인해야 한다고 지정하고 누가 승인자가 될지도 지정합니다.

![액세스 패키지 정책 만들기](./media/entitlement-management-access-package-first/elm-access-package-policy.png)

**필수 역할:** 전역 관리자 또는 사용자 관리자

1. **웹 프로젝트 액세스 패키지**의 왼쪽 메뉴에서 **정책**을 클릭합니다.

    ![액세스 패키지 정책 목록](./media/entitlement-management-access-package-first/policies-list.png)

1. **정책 추가**를 클릭하여 정책 만들기를 엽니다.

1. 이름에 **내부 요청자 정책**을 입력하고 설명에 **이 디렉터리의 사용자가 웹 프로젝트 리소스에 대한 액세스를 요청하도록 허용**을 입력합니다.

1. **Users who can request access**(액세스를 요청할 수 있는 사용자) 섹션에서 **For users in your directory**(디렉터리에 있는 사용자의 경우) 클릭합니다.

    ![정책 만들기](./media/entitlement-management-access-package-first/policy-create.png)

1. **사용자 및 그룹 선택** 섹션까지 아래로 스크롤하여 **사용자 및 그룹 추가**를 클릭합니다.

1. 사용자 및 그룹 선택 창에서 앞서 만든 **Requestor1** 사용자를 선택한 다음, **선택**을 클릭합니다.

1. **요청** 섹션에서 **승인 필요**를 **예**로 설정합니다.

1. **승인자 선택** 섹션에서 **승인자 추가**를 클릭합니다.

1. 승인자 선택 창에서 앞서 만든 **Admin1**을 선택한 다음, **선택**을 클릭합니다.

    이 자습서에서는 관리자와 승인자가 같은 사람이지만 일반적으로 또 다른 사람을 승인자로 지정할 수 있습니다.

1. **만료** 섹션에서 **Access package expires**(액세스 패키지 만료)를 **일 수**로 설정합니다.

1. **Access expires after**(다음 기간 후 액세스 만료)를 **30**일로 설정합니다

1. **정책 설정**을 **예**로 설정합니다.

    ![정책 설정 만들기](./media/entitlement-management-access-package-first/policy-create-settings.png)

1. **만들기**를 클릭하여 **내부 요청자 정책**을 만듭니다.

1. 웹 프로젝트 액세스 패키지의 왼쪽 메뉴에서 **개요**를 클릭합니다.

1. **내 액세스 포털 링크**를 복사합니다.

    이 링크는 다음 단계에서 사용됩니다.

    ![액세스 패키지 개요 - 내 액세스 포털 링크](./media/entitlement-management-shared/my-access-portal-link.png)

## <a name="step-4-request-access"></a>4단계: 액세스 요청

이 단계에서는 **내부 요청자**로 단계를 수행하면서 액세스 패키지에 대한 액세스 권한을 요청합니다. 요청자는 내 액세스 포털이라는 사이트를 사용하여 요청을 제출합니다. 내 액세스 포털에서는 요청자가 액세스 패키지에 대한 요청을 제출할 수 있고, 이미 액세스 권한이 있는 액세스 패키지를 확인하고 요청 기록을 볼 수 있습니다.

**필수 역할:** 내부 요청자

1. Azure Portal에서 로그아웃합니다.

1. 새 브라우저 창에서, 이전 단계에서 복사한 내 액세스 포털 링크로 이동합니다.

1. 내 액세스 포털에 **Requestor1**으로 로그인합니다.

    **웹 프로젝트 액세스 패키지**가 보입니다.

1. 필요한 경우, **설명** 열에서 화살표를 클릭하면 액세스 패키지에 대한 세부 정보를 볼 수 있습니다.

    ![내 액세스 포털 - 액세스 패키지](./media/entitlement-management-shared/my-access-access-packages.png)

1. 확인 표시를 클릭하여 패키지를 선택합니다.

1. **액세스 요청**을 클릭하여 액세스 요청 창을 엽니다.

1. **비즈니스 타당성** 상자에 **웹 프로젝트 작업**를 근거로 입력합니다.

1. **특정 기간의 요청입니까?** 토글을 **예**로 설정합니다.

1. **시작 날짜**는 오늘로, **종료 날짜**는 내일로 설정합니다.

    ![내 액세스 포털 - 액세스 요청](./media/entitlement-management-shared/my-access-request-access.png)

1. **제출**을 클릭합니다.

1. 왼쪽 메뉴에서 **요청 기록**을 클릭하여 요청이 제출되었는지 확인합니다.

## <a name="step-5-approve-access-request"></a>5단계: 액세스 요청 승인

이 단계에서는 **승인자** 사용자로 로그인하여 내부 요청자에 대한 액세스 요청을 승인합니다. 승인자는 요청자가 요청을 제출하는 데 사용한 것과 동일한 내 액세스 포털을 사용합니다. 내 액세스 포털을 사용하면 승인자가 보류 중인 승인을 보고 요청을 승인하거나 거부할 수 있습니다.

**필수 역할:** 승인자

1. 내 액세스 포털에서 로그아웃합니다.

1. [내 액세스 포털](https://myaccess.microsoft.com)에 **Admin1**로 로그인합니다.

1. 왼쪽 메뉴에서 **승인**을 클릭합니다.

1. **보류 중** 탭에서 **Requestor1**을 찾습니다.

    Requestor1의 요청이 보이지 않으면 몇 분 후에 다시 시도합니다.

1. **보기** 링크를 클릭하여 액세스 요청 창을 엽니다.

1. **승인**을 클릭합니다.

1. **이유** 상자에 **웹 프로젝트에 대한 액세스 승인**을 이유로 입력합니다.

    ![내 액세스 포털 - 액세스 요청](./media/entitlement-management-shared/my-access-approve-request.png)

1. **제출**을 클릭하여 결정을 제출합니다.

    성공적으로 승인되었다는 메시지가 보입니다.

## <a name="step-6-validate-that-access-has-been-assigned"></a>6단계: 액세스가 할당되었는지 확인

액세스 요청을 승인했으니, 이 단계에서는, **내부 요청자**에게 액세스 패키지가 할당되었으며, 이제 **엔지니어링 그룹**이라는 그룹의 멤버라는 것을 확인합니다.

**필수 역할:** 전역 관리자 또는 사용자 관리자

1. 내 액세스 포털에서 로그아웃합니다.

1. **Admin1**로 [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽 메뉴에서 **액세스 패키지**를 클릭합니다.

1. **웹 프로젝트 액세스 패키지**를 찾아서 클릭합니다.

1. 왼쪽 메뉴에서 **요청**을 클릭합니다.

    Requestor1과 상태가 **배달됨**인 내부 요청자 정책이 표시됩니다.

1. 요청을 클릭하여 요청 세부 정보를 봅니다.

    ![액세스 패키지 - 요청 세부 정보](./media/entitlement-management-access-package-first/request-details.png)

1. 왼쪽 탐색 메뉴에서 **Azure Active Directory**를 클릭합니다.

1. **그룹**을 클릭하고 **엔지니어링 그룹**이라는 그룹을 엽니다.

1. **구성원**을 클릭합니다.

    **Requestor1**이 멤버로 나열된 것이 보입니다.

    ![엔지니어링 그룹 멤버](./media/entitlement-management-access-package-first/group-members.png)

## <a name="step-7-clean-up-resources"></a>7단계: 리소스 정리

이 단계에서는 변경한 내용을 제거하고 **웹 프로젝트 액세스 패키지**라는 액세스 패키지를 삭제합니다.

**필수 역할:**  전역 관리자 또는 사용자 관리자

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. **웹 프로젝트 액세스 패키지**를 엽니다.

1. **할당**을 클릭합니다.

1. **Requestor1**에서 줄임표( **...** )를 클릭한 다음, **액세스 권한 제거**를 클릭합니다.

    상태가 배달됨에서 만료됨으로 변경됩니다.

1. **정책**을 클릭합니다.

1. **내부 요청자 정책**에서 줄임표( **...** )를 클릭한 다음, **삭제**를 클릭합니다.

1. **리소스 역할**을 클릭합니다.

1. **엔지니어링 그룹**에 대해 줄임표( **...** )를 클릭한 다음, **리소스 역할 제거**를 클릭합니다.

1. 액세스 패키지 목록을 엽니다.

1. **웹 프로젝트 액세스 프로젝트**에 대해 줄임표( **...** )를 클릭한 다음, **삭제**를 클릭합니다.

1. Azure Active Directory에서 직접 만든 사용자(예: **Requestor1**과 **Admin1**)를 삭제합니다.

1. **엔지니어링 그룹**이라는 그룹을 삭제합니다.

## <a name="next-steps"></a>다음 단계

다음 문서로 이동하면 권한 관리의 일반적인 시나리오 단계에 대해 알아볼 수 있습니다.
> [!div class="nextstepaction"]
> [일반적인 시나리오](entitlement-management-scenarios.md)
