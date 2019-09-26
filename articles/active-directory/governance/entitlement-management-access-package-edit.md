---
title: Azure AD 자격 관리 (미리 보기)에서 기존 액세스 패키지 편집 및 관리-Azure Active Directory
description: Azure Active Directory 자격 관리 (미리 보기)에서 기존 액세스 패키지를 편집 하 고 관리 하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 07/23/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6a575d9f90d166ba69b14e4507d9ed7a54fac574
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2019
ms.locfileid: "71291025"
---
# <a name="edit-and-manage-an-existing-access-package-in-azure-ad-entitlement-management-preview"></a>Azure AD 자격 관리 (미리 보기)에서 기존 액세스 패키지 편집 및 관리

> [!IMPORTANT]
> Azure AD(Azure Active Directory) 권한 관리는 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

액세스 패키지를 사용 하면 액세스 패키지의 수명 기간 동안 자동으로 액세스를 관리 하는 리소스 및 정책의 일회성 설치를 수행할 수 있습니다. 액세스 패키지 관리자는 새 리소스에 대 한 사용자의 액세스를 프로 비전 하거나 이전 리소스에서 액세스 권한을 제거 하는 것에 대해 걱정 하지 않고 언제 든 지 액세스 패키지의 리소스를 변경할 수 있습니다. 언제 든 지 정책을 업데이트할 수도 있지만 정책 변경 내용은 새 액세스에만 영향을 줍니다.

이 문서에서는 기존 액세스 패키지를 편집 하 고 관리 하는 방법을 설명 합니다.

## <a name="add-resource-roles"></a>리소스 역할 추가

리소스 역할은 리소스와 연결 된 사용 권한 컬렉션입니다. 사용자가 리소스를 요청할 수 있도록 하는 방법은 액세스 패키지에 리소스 역할을 추가 하는 것입니다. 그룹, 응용 프로그램 및 SharePoint 사이트에 대 한 리소스 역할을 추가할 수 있습니다.

**필수 역할:** 전역 관리자, 사용자 관리자, 카탈로그 소유자 또는 액세스 패키지 관리자

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽 메뉴에서 **액세스 패키지** 를 클릭 한 다음 액세스 패키지를 엽니다.

1. 왼쪽 메뉴에서 **리소스 역할**을 클릭 합니다.

1. **리소스 역할 추가** 를 클릭 하 여 패키지에 액세스할 수 있도록 리소스 역할 추가 페이지를 엽니다.

    ![액세스 패키지-리소스 역할 추가](./media/entitlement-management-access-package-edit/resource-roles-add.png)

1. 그룹, 응용 프로그램 또는 SharePoint 사이트를 추가할지 여부에 따라 다음 리소스 역할 섹션 중 하나의 단계를 수행 합니다.

### <a name="add-a-group-resource-role"></a>그룹 리소스 역할 추가

액세스 패키지가 할당 될 때 자격 관리에서 자동으로 그룹에 사용자를 추가할 수 있습니다. 

- 그룹이 액세스 패키지의 일부이 고 해당 액세스 패키지에 사용자가 할당 된 경우 해당 사용자는 아직 없는 경우 해당 그룹에 추가 됩니다.
- 사용자의 액세스 패키지 할당은 만료 될 때 해당 그룹이 포함 된 다른 액세스 패키지에 대 한 할당을 현재 보유 하 고 있지 않은 한 그룹에서 제거 됩니다.

모든 Office 365 그룹 또는 Azure AD 보안 그룹을 선택할 수 있습니다.  관리자는 카탈로그에 모든 그룹을 추가할 수 있습니다. 카탈로그 소유자는 그룹의 소유자 인 경우 카탈로그에 모든 그룹을 추가할 수 있습니다. 그룹을 선택할 때 다음 Azure AD 제약 조건을 염두에 두어야 합니다.

- 게스트를 비롯 한 사용자가 그룹에 멤버로 추가 되 면 해당 그룹의 다른 모든 멤버를 볼 수 있습니다.
- Azure AD는 Azure AD Connect를 사용 하 여 Windows Server Active Directory에서 동기화 되었거나 메일 그룹으로 Exchange Online에서 만든 그룹의 구성원을 변경할 수 없습니다.  
- 동적 그룹의 멤버 자격은 멤버를 추가 하거나 제거 하 여 업데이트할 수 없으므로 동적 그룹 멤버 자격은 자격 관리와 함께 사용 하기에 적합 하지 않습니다.

1. **액세스 패키지에 리소스 역할 추가** 페이지에서 **그룹** 을 클릭 하 여 그룹 선택 창을 엽니다.

1. 액세스 패키지에 포함 하려는 그룹을 선택 합니다.

    ![액세스 패키지-리소스 역할 추가-그룹 선택](./media/entitlement-management-access-package-edit/group-select.png)

1. **선택**을 클릭합니다.

1. **역할** 목록에서 **소유자** 또는 **멤버**를 선택 합니다.

    일반적으로 멤버 역할을 선택 합니다. 소유자 역할을 선택 하면 사용자가 다른 멤버나 소유자를 추가 하거나 제거할 수 있습니다.

    ![액세스 패키지-그룹에 대 한 리소스 역할 추가](./media/entitlement-management-access-package-edit/group-role.png)

1. **추가**를 클릭합니다.

    액세스 패키지에 대 한 기존 할당을 사용 하는 모든 사용자는 추가 될 때 자동으로이 그룹의 구성원이 됩니다.

### <a name="add-an-application-resource-role"></a>응용 프로그램 리소스 역할 추가

사용자에 게 액세스 패키지가 할당 된 경우 azure ad가 azure ad에 페더레이션된 SaaS 응용 프로그램 및 조직의 응용 프로그램을 포함 하 여 Azure AD 엔터프라이즈 응용 프로그램에 대 한 액세스 권한을 자동으로 할당 하도록 할 수 있습니다. 페더레이션된 Single Sign-On를 통해 Azure AD와 통합 되는 응용 프로그램의 경우 Azure AD는 응용 프로그램에 할당 된 사용자에 대 한 페더레이션 토큰을 발급 합니다.

응용 프로그램에는 여러 역할이 있을 수 있습니다. 응용 프로그램을 액세스 패키지에 추가 하는 경우 해당 응용 프로그램에 둘 이상의 역할이 있는 경우 해당 사용자에 대 한 적절 한 역할을 지정 해야 합니다.  응용 프로그램을 개발 하는 경우 [SAML 토큰에서 발급 된 역할 클레임을 구성](../develop/active-directory-enterprise-app-role-management.md)하는 방법에 대 한 문서에서 응용 프로그램에 이러한 역할이 제공 되는 방법에 대해 자세히 알아볼 수 있습니다.

응용 프로그램 역할이 액세스 패키지의 일부인 경우:

- 사용자에 게 해당 액세스 패키지를 할당 하면 해당 사용자는 아직 없는 경우 해당 응용 프로그램 역할에 추가 됩니다.
- 사용자의 액세스 패키지 할당이 만료 되 면 해당 응용 프로그램 역할을 포함 하는 다른 액세스 패키지에 할당 된 경우를 제외 하 고 응용 프로그램에서 해당 액세스 권한이 제거 됩니다.

응용 프로그램을 선택할 때 고려해 야 할 사항은 다음과 같습니다.

- 응용 프로그램에도 해당 역할에 할당 된 그룹이 있을 수 있습니다.  액세스 패키지의 응용 프로그램 역할 대신 그룹을 추가 하도록 선택할 수 있지만 응용 프로그램은 사용자에 게 내 액세스 포털에서 액세스 패키지의 일부로 표시 되지 않습니다.

1. **액세스 패키지에 리소스 역할 추가** 페이지에서 **응용** 프로그램을 클릭 하 여 응용 프로그램 선택 창을 엽니다.

1. 액세스 패키지에 포함 하려는 응용 프로그램을 선택 합니다.

    ![액세스 패키지-리소스 역할 추가-응용 프로그램 선택](./media/entitlement-management-access-package-edit/application-select.png)

1. **선택**을 클릭합니다.

1. **역할** 목록에서 응용 프로그램 역할을 선택 합니다.

    ![액세스 패키지-응용 프로그램에 대 한 리소스 역할 추가](./media/entitlement-management-access-package-edit/application-role.png)

1. **추가**를 클릭합니다.

    액세스 패키지에 대 한 기존 할당을 사용 하는 모든 사용자에 게는 추가 될 때이 응용 프로그램에 대 한 액세스 권한이 자동으로 부여 됩니다.

### <a name="add-a-sharepoint-site-resource-role"></a>SharePoint 사이트 리소스 역할 추가

Azure AD는 액세스 패키지에 할당 된 경우 사용자에 게 SharePoint Online 사이트 또는 SharePoint Online 사이트 모음에 대 한 액세스 권한을 자동으로 할당할 수 있습니다.

1. **액세스 패키지에 리소스 역할 추가** 페이지에서 **sharepoint 사이트** 를 클릭 하 여 sharepoint Online 사이트 선택 창을 엽니다.

1. 액세스 패키지에 포함 하려는 SharePoint Online 사이트를 선택 합니다.

    ![액세스 패키지-리소스 역할 추가-SharePoint Online 사이트 선택](./media/entitlement-management-access-package-edit/sharepoint-site-select.png)

1. **선택**을 클릭합니다.

1. **역할** 목록에서 SharePoint Online 사이트 역할을 선택 합니다.

    ![액세스 패키지-SharePoint Online 사이트에 대 한 리소스 역할 추가](./media/entitlement-management-access-package-edit/sharepoint-site-role.png)

1. **추가**를 클릭합니다.

    액세스 패키지에 대 한 기존 할당을 사용 하는 모든 사용자에 게는이 SharePoint Online 사이트를 추가할 때 해당 사이트에 대 한 액세스 권한이 자동으로 부여 됩니다.

## <a name="remove-resource-roles"></a>리소스 역할 제거

**필수 역할:** 전역 관리자, 사용자 관리자, 카탈로그 소유자 또는 액세스 패키지 관리자

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽 메뉴에서 **액세스 패키지** 를 클릭 한 다음 액세스 패키지를 엽니다.

1. 왼쪽 메뉴에서 **리소스 역할**을 클릭 합니다.

1. 리소스 역할 목록에서 제거 하려는 리소스 역할을 찾습니다.

1. 줄임표 ( **...** )를 클릭 한 다음 **리소스 역할 제거**를 클릭 합니다.

    액세스 패키지에 대 한 기존 할당을 가진 사용자는 제거 될 때이 리소스 역할에 대 한 액세스 권한이 자동으로 취소 됩니다.

## <a name="add-a-new-policy"></a>새 정책 추가

액세스 패키지를 요청할 수 있는 사용자를 지정 하는 방법은 정책을 만드는 것입니다. 서로 다른 승인 및 만료 설정을 사용 하 여 다른 사용자 집합에 할당을 부여할 수 있도록 허용 하려면 단일 액세스 패키지에 대해 여러 정책을 만들 수 있습니다. 단일 정책을 사용 하 여 내부 및 외부 사용자를 동일한 액세스 패키지에 할당할 수는 없습니다. 그러나 동일한 액세스 패키지에 두 개의 정책을 만들 수 있습니다. 하나는 내부 사용자를 위한 것이 고 다른 하나는 외부 사용자를 위한 것입니다. 사용자에 게 적용 되는 정책이 여러 개 있는 경우 요청 시 사용자에 게 할당 하려는 정책을 선택 하 라는 메시지가 표시 됩니다.

다음 다이어그램에서는 기존 액세스 패키지에 대 한 정책을 만드는 개략적인 프로세스를 보여 줍니다.

![정책 프로세스 만들기](./media/entitlement-management-access-package-edit/policy-process.png)

**필수 역할:** 전역 관리자, 사용자 관리자, 카탈로그 소유자 또는 액세스 패키지 관리자

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽 메뉴에서 **액세스 패키지** 를 클릭 한 다음 액세스 패키지를 엽니다.

1. **정책** 을 클릭 한 다음 **정책 추가**를 클릭 합니다.

1. 정책에 대 한 이름 및 설명을 입력 합니다.

    ![이름 및 설명을 사용 하 여 정책 만들기](./media/entitlement-management-access-package-edit/policy-name-description.png)

1. **액세스를 요청할 수 있는 사용자**의 선택에 따라 다음 정책 섹션 중 하나의 단계를 수행 합니다.

[!INCLUDE [Entitlement management policy](../../../includes/active-directory-entitlement-management-policy.md)]

## <a name="edit-an-existing-policy"></a>기존 정책 편집

언제 든 지 정책을 편집할 수 있습니다. 정책에 대 한 만료 날짜를 변경 하는 경우 이미 보류 중인 승인 또는 승인 된 상태에 있는 요청에 대 한 만료 날짜는 변경 되지 않습니다.

**필수 역할:** 전역 관리자, 사용자 관리자, 카탈로그 소유자 또는 액세스 패키지 관리자

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽 메뉴에서 **액세스 패키지** 를 클릭 한 다음 액세스 패키지를 엽니다.

1. **정책** 을 클릭 한 다음 편집 하려는 정책을 클릭 합니다.

    **정책 세부 정보** 창이 페이지 아래쪽에 열립니다.

    ![액세스 패키지-정책 정보 창](./media/entitlement-management-access-package-edit/policy-details.png)

1. 정책을 편집 하려면 **편집** 을 클릭 합니다.

    ![액세스 패키지-정책 편집](./media/entitlement-management-access-package-edit/policy-edit.png)

1. 완료 되 면 **업데이트**를 클릭 합니다.

## <a name="directly-assign-a-user"></a>사용자를 직접 할당

경우에 따라 사용자가 액세스 패키지를 요청 하는 프로세스를 진행할 필요가 없도록 특정 사용자를 액세스 패키지에 직접 할당할 수 있습니다. 사용자를 직접 할당 하려면 액세스 패키지에 관리자 직접 할당을 허용 하는 정책이 있어야 합니다.

**필수 역할:** 전역 관리자, 사용자 관리자, 카탈로그 소유자 또는 액세스 패키지 관리자

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽 메뉴에서 **액세스 패키지** 를 클릭 한 다음 액세스 패키지를 엽니다.

1. 왼쪽 메뉴에서 **할당**을 클릭 합니다.

1. **새 할당** 을 클릭 하 여 액세스 패키지에 사용자 추가를 엽니다.

    ![할당-액세스 패키지에 사용자 추가](./media/entitlement-management-access-package-edit/assignments-add-user.png)

1. **사용자 추가** 를 클릭 하 여 액세스 패키지를 할당 하려는 사용자를 선택 합니다.

1. **정책 선택** 목록에서 [없음 (관리자 직접 할당에만 해당)](#policy-none-administrator-direct-assignments-only) 설정이 있는 정책을 선택 합니다.

    이 액세스 패키지에 이러한 유형의 정책이 없으면 **새 정책 만들기** 를 클릭 하 여 추가할 수 있습니다.

1. 선택한 사용자의 할당을 시작할 날짜와 시간을 설정 하 고 종료 합니다. 종료 날짜를 제공 하지 않으면 정책의 만료 설정이 사용 됩니다.

1. 필요에 따라 레코드 유지를 위한 직접 할당에 대 한 근거를 제공 합니다.

1. **추가** 를 클릭 하 여 선택한 사용자를 액세스 패키지에 직접 할당 합니다.

    몇 분 후에 **새로 고침** 을 클릭 하 여 할당 목록에서 사용자를 확인 합니다.

## <a name="view-who-has-an-assignment"></a>할당 된 사용자 보기

**필수 역할:** 전역 관리자, 사용자 관리자, 카탈로그 소유자 또는 액세스 패키지 관리자

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽 메뉴에서 **액세스 패키지** 를 클릭 한 다음 액세스 패키지를 엽니다.

1. 활성 할당 목록을 보려면 **할당** 을 클릭 합니다.

1. 특정 할당을 클릭 하 여 추가 세부 정보를 확인 합니다.

1. 모든 리소스 역할이 제대로 프로 비전 되지 않은 할당 목록을 보려면 필터 상태를 클릭 하 고 **배달**중을 선택 합니다.

    **요청** 페이지에서 사용자의 해당 요청을 찾아 배달 오류에 대 한 추가 세부 정보를 볼 수 있습니다.

1. 만료 된 할당을 보려면 필터 상태를 클릭 하 고 **만료 됨**을 선택 합니다.

1. 필터링 된 목록의 CSV 파일을 다운로드 하려면 **다운로드**를 클릭 합니다.

## <a name="view-requests"></a>요청 보기

**필수 역할:** 전역 관리자, 사용자 관리자, 카탈로그 소유자 또는 액세스 패키지 관리자

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽 메뉴에서 **액세스 패키지** 를 클릭 한 다음 액세스 패키지를 엽니다.

1. **요청**을 클릭 합니다.

1. 특정 요청을 클릭 하면 추가 세부 정보를 볼 수 있습니다.

## <a name="view-a-requests-delivery-errors"></a>요청의 배달 오류 보기

**필수 역할:** 전역 관리자, 사용자 관리자, 카탈로그 소유자 또는 액세스 패키지 관리자

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽 메뉴에서 **액세스 패키지** 를 클릭 한 다음 액세스 패키지를 엽니다.

1. **요청**을 클릭 합니다.

1. 보려는 요청을 선택 합니다.

    요청에 배달 오류가 있으면 요청 **상태가 배달 되지 않음이 고 하위** 상태는 부분적으로 **전달**됩니다.

    배달 오류가 있으면 요청 세부 정보 창에 배달 오류가 발생 한 것입니다.

1. 개수를 클릭 하면 요청의 모든 배달 오류가 표시 됩니다.

## <a name="cancel-a-pending-request"></a>보류 중인 요청 취소

아직 배달 되지 않은 보류 중인 요청만 취소할 수 있습니다.

**필수 역할:** 전역 관리자, 사용자 관리자, 카탈로그 소유자 또는 액세스 패키지 관리자

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽 메뉴에서 **액세스 패키지** 를 클릭 한 다음 액세스 패키지를 엽니다.

1. **요청**을 클릭 합니다.

1. 취소할 요청을 클릭 합니다.

1. 요청 정보 창에서 **요청 취소**를 클릭 합니다.

## <a name="copy-my-access-portal-link"></a>내 액세스 포털 링크 복사

디렉터리의 대부분의 사용자는 내 액세스 포털에 로그인 하 여 요청할 수 있는 액세스 패키지의 목록을 자동으로 볼 수 있습니다. 그러나 디렉터리에 아직 없는 외부 비즈니스 파트너 사용자의 경우 액세스 패키지를 요청 하는 데 사용할 수 있는 링크를 전송 해야 합니다. 

내부 비즈니스 파트너에 게 보낼 때 전체 내 액세스 포털 링크를 복사 하는 것이 중요 합니다. 이렇게 하면 파트너가 디렉터리 포털에 액세스 하 여 요청을 수행할 수 있습니다. 

이 링크는 "myaccess"로 시작 하 고, 디렉터리 힌트를 포함 하 고, 액세스 패키지 id로 끝납니다. 링크에 다음이 모두 포함 되어 있는지 확인 합니다.

 `https://myaccess.microsoft.com/@<directory_hint>#/access-packages/<access_package_id>`

외부 사용자가 액세스 패키지를 사용 하도록 설정 하 고 외부 사용자의 디렉터리에 대 한 정책이 있는 경우 외부 사용자는 내 액세스 포털 링크를 사용 하 여 액세스 패키지를 요청할 수 있습니다.

**필수 역할:** 전역 관리자, 사용자 관리자, 카탈로그 소유자 또는 액세스 패키지 관리자

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽 메뉴에서 **액세스 패키지** 를 클릭 한 다음 액세스 패키지를 엽니다.

1. 개요 페이지에서 **내 액세스 포털 링크**를 복사 합니다.

    ![액세스 패키지 개요 - 내 액세스 포털 링크](./media/entitlement-management-shared/my-access-portal-link.png)

1. 전자 메일을 보내거나 외부 비즈니스 파트너에 게 링크를 보냅니다. 사용자와의 링크를 공유 하 여 액세스 패키지를 요청할 수 있습니다.

## <a name="change-the-hidden-setting"></a>숨겨진 설정 변경

액세스 패키지는 기본적으로 검색 가능 합니다. 즉, 정책을 통해 사용자가 액세스 패키지를 요청할 수 있는 경우 내 액세스 포털에 나열 된 액세스 패키지가 자동으로 표시 됩니다.

**필수 역할:** 전역 관리자, 사용자 관리자, 카탈로그 소유자 또는 액세스 패키지 관리자

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽 메뉴에서 **액세스 패키지** 를 클릭 한 다음 액세스 패키지를 엽니다.

1. 개요 페이지에서 **편집**을 클릭 합니다.

1. **숨겨진** 설정을 설정 합니다.

    **아니요**로 설정 하면 액세스 패키지가 사용자의 내 액세스 포털에 나열 됩니다.

    **예**로 설정 되 면 액세스 패키지가 사용자의 내 액세스 포털에 나열 되지 않습니다. 사용자가 액세스 패키지를 볼 수 있는 유일한 방법은 액세스 패키지에 대 한 직접 **내 액세스 포털 링크가** 있는 경우입니다.

## <a name="delete"></a>삭제

활성 사용자 할당이 없는 경우에만 액세스 패키지를 삭제할 수 있습니다.

**필수 역할:** 전역 관리자, 사용자 관리자, 카탈로그 소유자 또는 액세스 패키지 관리자

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽 메뉴에서 **액세스 패키지** 를 클릭 한 다음 액세스 패키지를 엽니다.

1. 왼쪽 메뉴에서 **할당** 을 클릭 하 고 모든 사용자에 대 한 액세스 권한 제거를 클릭 합니다.

1. 왼쪽 메뉴에서 **개요** 를 클릭 한 다음 **삭제**를 클릭 합니다.

1. 삭제 메시지가 표시 되 면 **예**를 클릭 합니다.

## <a name="when-are-changes-applied"></a>변경 내용이 적용 되는 경우

자격 관리에서 Azure AD는 매일 여러 번 액세스 패키지의 할당 및 리소스에 대 한 대량 변경을 처리 합니다. 따라서 액세스를 만들거나 액세스 패키지의 리소스 역할을 변경 하는 경우 Azure AD에서 변경 내용을 적용 하는 데 최대 24 시간이 걸릴 수 있으며, 이러한 변경 내용을 다른 Microsoft Online Services 또는 연결 된 SaaS 응용 프로그램에 전파 하는 데 걸리는 시간이 길어집니다. server 변경으로 인해 몇 가지 개체만 영향을 받는 경우 Azure AD에서 변경 내용을 적용 하는 데 몇 분 정도 걸릴 수 있습니다. 그 후에는 다른 Azure AD 구성 요소에서 해당 변경 내용을 검색 하 고 SaaS 응용 프로그램을 업데이트 합니다. 변경이 수천 개의 개체에 영향을 주는 경우에는 변경 내용이 더 오래 걸립니다. 예를 들어 응용 프로그램 2 개 및 100 사용자 할당을 사용 하는 액세스 패키지가 있고 액세스 패키지에 SharePoint 사이트 역할을 추가 하기로 결정 한 경우 모든 사용자가 해당 SharePoint 사이트 역할에 포함 될 때까지 지연이 있을 수 있습니다. Azure AD 감사 로그, Azure AD 프로 비전 로그 및 SharePoint 사이트 감사 로그를 통해 진행률을 모니터링할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [카탈로그 소유자 또는 액세스 패키지 관리자 추가](entitlement-management-delegate.md#add-a-catalog-owner-or-an-access-package-manager)
- [요청 프로세스 및 전자 메일 알림](entitlement-management-process.md)
