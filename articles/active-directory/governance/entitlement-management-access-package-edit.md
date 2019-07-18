---
title: 편집 및 Azure AD 권한 관리 (미리 보기)-Azure Active Directory의에서 기존 액세스 패키지 관리
description: 편집 및 Azure Active Directory 권한 관리 (미리 보기)에서 기존 액세스 패키지를 관리 하는 방법을 알아봅니다.
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 05/16/2019
ms.author: rolyon
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 73c1717249abac30847f96073ee6daf7d98112d7
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190343"
---
# <a name="edit-and-manage-an-existing-access-package-in-azure-ad-entitlement-management-preview"></a>편집 및 Azure AD 권한 관리 (미리 보기)의 기존 액세스 패키지 관리

> [!IMPORTANT]
> Azure AD(Azure Active Directory) 권한 관리는 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

액세스 패키지를 자동으로 액세스 패키지의 수명에 대 한 액세스를 관리 하는 리소스 및 정책의 일회성 설치를 수행할 수 있도록 합니다. 액세스 패키지 관리자를 사용 하는 새 리소스에 대 한 사용자의 액세스를 프로 비전 또는 이전 리소스에서 액세스를 제거 하는 방법에 대 한 걱정 없이 언제 든 지 액세스 패키지에 리소스를 변경할 수 있습니다. 언제 든 지 정책을 업데이트할 수도 있습니다, 그리고 있지만 정책 변경 내용에만 영향을 새 액세스 합니다.

이 문서에는 편집 및 기존 액세스 패키지를 관리 하는 방법을 설명 합니다.

## <a name="add-resource-roles"></a>리소스 역할 추가

리소스 역할은 권한 리소스와 연결 된 컬렉션입니다. 사용자가 요청에 사용할 수 있는 리소스를 확인 하는 방법은 액세스 패키지에 리소스 역할을 추가 하는 것입니다. 그룹, 응용 프로그램 및 SharePoint 사이트에 대 한 리소스 역할을 추가할 수 있습니다.

**필수 역할:** 사용자 관리자, 카탈로그 소유자 또는 액세스 패키지 관리자

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽된 메뉴에서 클릭 **패키지에 액세스할** 액세스 패키지를 엽니다.

1. 왼쪽된 메뉴에서 클릭 **리소스 역할**입니다.

1. 클릭 **리소스 역할 추가** 패키지 페이지에 액세스 하려면 역할 추가 리소스를 열려고 합니다.

    ![패키지 액세스-리소스 역할 추가](./media/entitlement-management-access-package-edit/resource-roles-add.png)

1. 그룹, 응용 프로그램 또는 SharePoint 사이트를 추가 하려는 여부에 따라 다음 리소스 역할 섹션 중 하나의 단계를 수행 합니다.

### <a name="add-a-group-resource-role"></a>그룹 리소스 역할 추가

패키지를 액세스 할당 될 때 사용자 그룹에 자동으로 추가 하는 권한 관리를 사용할 수 있습니다. 

- 그룹 및 사용자는 액세스 패키지의 일부인 경우 사용자는 해당 그룹에 추가 하지 않은 경우 해당 액세스 패키지에 할당 됩니다.
- 사용자의 액세스 패키지 할당 만료 되 면 꺼내고 그룹에서 현재 그룹을 포함 하는 다른 액세스 패키지에 대 한 할당을 가져야 합니다.

모든 Office 365 그룹 또는 Azure AD 보안 그룹을 선택할 수 있습니다.  관리자는 카탈로그;에 모든 그룹을 추가할 수 있습니다. 카탈로그 소유자 그룹의 소유자 경우 카탈로그에 모든 그룹을 추가할 수 있습니다. 그룹을 선택 하는 경우 다음과 같은 Azure AD 제약 조건이 염두에 두십시오.

- 게스트를 포함 하 여 사용자를 그룹에 멤버로 추가 되 면 해당 그룹의 다른 모든 멤버를 볼 수 있습니다.
- Azure AD는 Azure AD Connect를 사용 하 여 Windows Server Active Directory에서 동기화 된 그룹의 멤버 자격을 변경할 수 없습니다.  
- 동적 그룹의 구성원을 추가 하거나 되므로 동적 그룹 구성원 자격 관리를 사용 하 여 사용 하기에 적합 하지 않습니다. 멤버를 제거 하 여 업데이트할 수 없습니다.

1. 에 **추가 리소스 역할이 패키지에 액세스할 수** 페이지에서 클릭 **그룹** 그룹 선택 창을 엽니다.

1. 액세스 패키지에 포함 하려는 그룹을 선택 합니다.

    ![패키지 액세스-리소스 역할-그룹 선택 추가](./media/entitlement-management-access-package-edit/group-select.png)

1. **선택**을 클릭합니다.

1. 에 **역할** 목록에서 **소유자** 하거나 **멤버**합니다.

    일반적으로 멤버 역할을 선택 합니다. 소유자 역할을 선택 하면 사용자가 다른 멤버 / 소유자 추가 또는 제거를 허용 됩니다.

    ![패키지 액세스-리소스 역할 그룹에 대 한 추가](./media/entitlement-management-access-package-edit/group-role.png)

1. **추가**를 클릭합니다.

    모든 사용자 액세스 패키지에 기존 할당을 사용 하 여 자동으로 됩니다이 그룹의 구성원 추가 될 때.

### <a name="add-an-application-resource-role"></a>응용 프로그램 리소스 역할 추가

SaaS 응용 프로그램과 사용자가 액세스 패키지를 할당 하는 경우 Azure AD에 페더레이션 한 조직의 응용 프로그램을 포함 하 여 Azure AD 엔터프라이즈 응용 프로그램에 사용자 액세스를 자동으로 할당 하는 Azure AD를 사용할 수 있습니다. 페더레이션된 single sign-on을 통해 Azure AD와 통합 되는 응용 프로그램의 경우 Azure AD가 응용 프로그램에 할당 된 사용자에 대 한 페더레이션 토큰을 발급 합니다.

응용 프로그램에 여러 역할이 있을 수 있습니다. 파일에 해당 응용 프로그램에 역할이 둘 이상인 경우 액세스 패키지를 응용 프로그램을 추가 하는 경우 해당 사용자에 대 한 적절 한 역할을 지정 해야 합니다.  응용 프로그램을 개발 하는 경우 보다 어떻게 이러한 역할에 대 한 제공 하는 방법에 대 한 문서에서 응용 프로그램 읽을 수 있습니다 [SAML 토큰에서 발급 된 역할 클레임 구성](../develop/active-directory-enterprise-app-role-management.md)합니다.

응용 프로그램 역할을 사용 하면 액세스 패키지의 일부인 후:

- 사용자는 액세스 패키지에 할당 하면 사용자는 응용 프로그램 역할에 추가 됩니다 아직 없는 경우.
- 사용자의 액세스 패키지 할당 만료 되 면 해당 응용 프로그램 역할을 포함 하는 다른 액세스 패키지에 대 한 할당에 대 한 필요에 대 한 액세스는 응용 프로그램에서 제거 됩니다.

응용 프로그램을 선택 하는 경우 몇 가지 고려 사항은 다음과 같습니다.

- 응용 프로그램에도 해당 역할에 할당 된 그룹도 있습니다.  그러나는 액세스 패키지의 응용 프로그램 역할 대신 그룹을 추가 하도록 선택할 수 있습니다 다음 응용 프로그램이 표시 되지 않습니다 사용자에 게 내 액세스 포털에서 액세스 패키지의 일부로 합니다.

1. 에 **추가 리소스 역할이 패키지에 액세스할 수** 페이지에서 클릭 **응용 프로그램** 선택 된 응용 프로그램 창을 엽니다.

1. 액세스 패키지에 포함 하려는 응용 프로그램을 선택 합니다.

    ![패키지 액세스-리소스 역할-선택 된 응용 프로그램 추가](./media/entitlement-management-access-package-edit/application-select.png)

1. **선택**을 클릭합니다.

1. 에 **역할** 목록 응용 프로그램 역할을 선택 합니다.

    ![패키지 액세스-응용 프로그램에 대 한 리소스 역할 추가](./media/entitlement-management-access-package-edit/application-role.png)

1. **추가**를 클릭합니다.

    모든 사용자 액세스 패키지에 기존 할당을 사용 하 여 자동으로 제공 됩니다 액세스가 응용이 프로그램에 추가 될 때.

### <a name="add-a-sharepoint-site-resource-role"></a>SharePoint 사이트 리소스 역할 추가

Azure AD 자동으로 사용자 액세스를 할당할 수는 SharePoint Online 사이트 또는 SharePoint Online 사이트 모음 액세스 패키지를 할당 된 경우.

1. 에 **추가 리소스 역할이 패키지에 액세스할 수** 페이지에서 클릭 **SharePoint 사이트** 선택 SharePoint Online 사이트 창을 엽니다.

1. 액세스 패키지에 포함 하려면 SharePoint Online 사이트를 선택 합니다.

    ![패키지 액세스-리소스 역할-선택 SharePoint Online 사이트를 추가 합니다.](./media/entitlement-management-access-package-edit/sharepoint-site-select.png)

1. **선택**을 클릭합니다.

1. 에 **역할** 목록에서 SharePoint Online 사이트 역할을 선택 합니다.

    ![패키지 액세스-SharePoint Online 사이트에 대 한 리소스 역할 추가](./media/entitlement-management-access-package-edit/sharepoint-site-role.png)

1. **추가**를 클릭합니다.

    모든 사용자 액세스 패키지에 기존 할당을 사용 하 여 자동으로 제공 됩니다 액세스가 SharePoint Online 사이트에 추가 될 때.

## <a name="remove-resource-roles"></a>리소스 역할 제거

**필수 역할:** 사용자 관리자, 카탈로그 소유자 또는 액세스 패키지 관리자

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽된 메뉴에서 클릭 **패키지에 액세스할** 액세스 패키지를 엽니다.

1. 왼쪽된 메뉴에서 클릭 **리소스 역할**입니다.

1. 리소스 역할의 목록에서 제거 하려는 리소스 역할을 찾습니다.

1. 줄임표 ( **...** )을 클릭 한 다음 **제거 리소스 역할**입니다.

    기존 할당 액세스 패키지에 있는 모든 사용자를 제거할 때이 리소스 역할에 취소에 대 한 액세스를 자동으로 포함 됩니다.

## <a name="add-a-new-policy"></a>새 정책 추가

액세스 패키지를 요청할 수 있는 사용자를 지정 하는 방법은 정책을 만드는 것입니다. 다른 사용자 집단에 서로 다른 승인 및 만료 설정을 사용 하 여 할당을 부여할 수 있도록 하려는 경우 단일 액세스 패키지에 대해 여러 정책을 만들 수 있습니다. 단일 정책에는 내부 및 외부 사용자 액세스는 동일한 패키지를 할당 하려면 사용할 수 없습니다. 그러나 두 정책을 같은 액세스 패키지-에 만들 수 있습니다 내부 사용자와 외부 사용자에 대 한 합니다. 여러 정책이 사용자에 게 적용 되는 경우에 할당 하려는 정책을 선택 하는 요청 시 요청 합니다.

다음 다이어그램에서는 기존 액세스 패키지에 대 한 정책을 만드는, 대략적인 프로세스를 보여 줍니다.

![정책 프로세스 만들기](./media/entitlement-management-access-package-edit/policy-process.png)

**필수 역할:** 사용자 관리자, 카탈로그 소유자 또는 액세스 패키지 관리자

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽된 메뉴에서 클릭 **패키지에 액세스할** 액세스 패키지를 엽니다.

1. 클릭 **정책을** 차례로 **정책 추가**합니다.

1. 이름 및 정책에 대 한 설명을 입력 합니다.

    ![이름 및 설명을 사용 하 여 정책 만들기](./media/entitlement-management-access-package-edit/policy-name-description.png)

1. 에 대 한 선택에 따라 **액세스 권한을 요청 하는 사용자**, 다음 정책 섹션 중 하나의 단계를 수행 합니다.

[!INCLUDE [Entitlement management policy](../../../includes/active-directory-entitlement-management-policy.md)]

## <a name="edit-an-existing-policy"></a>기존 정책 편집

언제 든 지 정책을 편집할 수 있습니다. 정책에 대 한 만료 날짜를 변경 하는 경우 보류 중인 승인이 이미 있는 또는 상태를 승인 하는 요청에 대 한 만료 날짜는 변경 되지 않습니다.

**필수 역할:** 사용자 관리자, 카탈로그 소유자 또는 액세스 패키지 관리자

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽된 메뉴에서 클릭 **패키지에 액세스할** 액세스 패키지를 엽니다.

1. 클릭 **정책을** 및 정책을 편집 하려면 클릭 합니다.

    합니다 **정책 세부 정보** 페이지의 맨 아래에 있는 창이 열립니다.

    ![패키지 액세스-정책 세부 정보 창](./media/entitlement-management-access-package-edit/policy-details.png)

1. 클릭 **편집** 정책을 편집할 수 있습니다.

    ![패키지 액세스-정책 편집](./media/entitlement-management-access-package-edit/policy-edit.png)

1. 완료 되 면, 클릭 **업데이트**합니다.

## <a name="directly-assign-a-user"></a>직접 사용자 할당

경우에 따라 직접 사용자 액세스 패키지 요청 프로세스를 통해 이동 하지 액세스 패키지에 특정 사용자를 할당 하는 것이 좋습니다. 사용자에 직접 할당 하려면 액세스 패키지 관리자 직접 할당을 허용 하는 정책이 있어야 합니다.

**필수 역할:** 사용자 관리자, 카탈로그 소유자 또는 액세스 패키지 관리자

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽된 메뉴에서 클릭 **패키지에 액세스할** 액세스 패키지를 엽니다.

1. 왼쪽된 메뉴에서 클릭 **할당**합니다.

1. 클릭 **새 할당** 액세스 패키지에 사용자 추가 열려고 합니다.

    ![할당-추가 액세스 패키지에는 사용자](./media/entitlement-management-access-package-edit/assignments-add-user.png)

1. 클릭 **사용자 추가** 액세스 패키지를 할당 하려는 사용자를 선택할 수 있습니다.

1. 에 **정책을 선택** 목록에서 선택 된 정책 합니다 [없음 (관리자가 직접 할당만)](#policy-none-administrator-direct-assignments-only) 설정.

    이 액세스 패키지에이 유형의 정책이 없는 경우 클릭할 수 있습니다 **새 정책 만들기** 하나를 추가 합니다.

1. 시작 및 종료 선택된 된 사용자의 할당을 원하는 날짜 및 시간을 설정 합니다. 종료 날짜를 지정 하지 않은 경우 만료 정책 사용 됩니다.

1. 필요에 따라 기록 보관에 직접 할당 하기 위한 근거를 제공 합니다.

1. 클릭 **추가** 직접 액세스 패키지에 선택한 사용자를 할당 합니다.

    몇 분 후 클릭 **새로 고침** 할당 목록에서 사용자를 확인 합니다.

## <a name="view-who-has-an-assignment"></a>할당 권한이 있는 사용자 보기

**필수 역할:** 사용자 관리자, 카탈로그 소유자 또는 액세스 패키지 관리자

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽된 메뉴에서 클릭 **패키지에 액세스할** 액세스 패키지를 엽니다.

1. 클릭 **할당** 활성 할당 목록을 확인 합니다.

1. 자세한 내용을 참조 하는 특정 할당을 클릭 합니다.

1. 제대로 프로 비전 하는 모든 리소스 역할에 할당의 목록을 보려면 필터 상태를 클릭 하 고 선택 **Delivering**합니다.

    에 해당 하는 사용자의 요청을 배치 하 여 배달 오류에서 추가 세부 정보를 볼 수 있습니다 합니다 **요청** 페이지입니다.

1. 만료 된 할당을 확인 하려면 필터 상태를 클릭 하 고 선택 **만료 됨**합니다.

1. 필터링 된 목록의 CSV 파일을 다운로드 하려면 **다운로드**합니다.

## <a name="view-requests"></a>요청 보기

**필수 역할:** 사용자 관리자, 카탈로그 소유자 또는 액세스 패키지 관리자

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽된 메뉴에서 클릭 **패키지에 액세스할** 액세스 패키지를 엽니다.

1. 클릭 **요청**합니다.

1. 추가 세부 정보를 보려면 특정 요청을 클릭 합니다.

## <a name="view-a-requests-delivery-errors"></a>요청의 배달 오류 보기

**필수 역할:** 사용자 관리자, 카탈로그 소유자 또는 액세스 패키지 관리자

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽된 메뉴에서 클릭 **패키지에 액세스할** 액세스 패키지를 엽니다.

1. 클릭 **요청**합니다.

1. 확인 하려는 요청을 선택 합니다.

    요청에 모든 배달 오류가 발생 하는 경우 요청 상태 됩니다 **Undelivered** 0x{0:x16}은 됩니다 **부분적으로 배달**합니다.

    요청 세부 정보 창에서 모든 배달 오류가 있는 경우 배달 오류 수가 됩니다.

1. 모든 요청의 배달 오류를 볼 수를 클릭 합니다.

## <a name="cancel-a-pending-request"></a>보류 중인 요청 취소

아직 제공 되지 않았습니다 하는 보류 중인 요청을 취소할 수 있습니다.

**필수 역할:** 사용자 관리자, 카탈로그 소유자 또는 액세스 패키지 관리자

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽된 메뉴에서 클릭 **패키지에 액세스할** 액세스 패키지를 엽니다.

1. 클릭 **요청**합니다.

1. 요청을 취소 하려면 클릭

1. 요청 세부 정보 창에서 클릭 **취소 요청**합니다.

## <a name="copy-my-access-portal-link"></a>내 액세스 포털 링크 복사

디렉터리에서 대부분의 사용자는 내 액세스 포털에 로그인 하 고 자동으로 요청할 수 있습니다 액세스 패키지 목록을 보려면 수 있습니다. 그러나 아직 디렉터리에 없는 외부 비즈니스 파트너 사용자에 대 한 액세스 패키지를 요청 하는 데 사용할 수 있는 링크를 보낼 해야 됩니다. 으로 외부 사용자에 대 한 액세스 패키지를 사용 하 고 외부 사용자의 디렉터리에 대 한 정책이 있는, 외부 사용자 액세스 패키지를 요청 하려면 내 액세스 포털 링크를 사용할 수 있습니다.

**필수 역할:** 사용자 관리자, 카탈로그 소유자 또는 액세스 패키지 관리자

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽된 메뉴에서 클릭 **패키지에 액세스할** 액세스 패키지를 엽니다.

1. 개요 페이지에 복사 합니다 **내 액세스 포털 링크**합니다.

    ![액세스 패키지 개요 - 내 액세스 포털 링크](./media/entitlement-management-shared/my-access-portal-link.png)

1. 전자 메일 또는 외부 비즈니스 파트너에 링크를 보냅니다. 패키지 액세스를 요청 하려면 해당 사용자를 사용 하 여 링크를 공유할 수 있습니다.

## <a name="change-the-hidden-setting"></a>Hidden 설정 변경

액세스 패키지 기본적으로 검색할 수 있습니다. 이 정책에서는 사용자 액세스 패키지를 요청 하는 경우 자동으로 표시 될 액세스 패키지의 내 액세스 포털에 나열 된 것을 의미 합니다.

**필수 역할:** 사용자 관리자, 카탈로그 소유자 또는 액세스 패키지 관리자

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽된 메뉴에서 클릭 **패키지에 액세스할** 액세스 패키지를 엽니다.

1. 개요 페이지에서 클릭 **편집**합니다.

1. 설정 된 **Hidden** 설정 합니다.

    경우로 **No**, 액세스 패키지가 사용자의 내 액세스 포털에 나열 됩니다.

    경우 설정 **예**, 사용자의 내 액세스 포털에서 액세스 패키지 나열 되지 것입니다. 사용자 액세스 패키지를 볼 수는 유일한 방법은 직접 있는지 **내 액세스 포털 링크** 액세스 패키지 합니다.

## <a name="delete"></a>삭제

액세스 패키지를 현재 사용자 할당이 있는 경우에 삭제할 수 있습니다.

**필수 역할:** 사용자 관리자, 카탈로그 소유자 또는 액세스 패키지 관리자

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽된 메뉴에서 클릭 **패키지에 액세스할** 액세스 패키지를 엽니다.

1. 왼쪽된 메뉴에서 클릭 **할당** 및 모든 사용자에 대 한 액세스를 제거 합니다.

1. 왼쪽된 메뉴에서 클릭 **개요** 을 클릭 한 다음 **삭제**합니다.

1. Delete 메시지가 나타나면 클릭 **예**합니다.

## <a name="when-are-changes-applied"></a>변경 내용은 적용 하는 경우

권한 관리에서 하루에 여러 번 Azure AD 할당 및 액세스 패키지의 리소스에 대 한 대량 변경 내용이 처리 됩니다. 을 할당 하거나 액세스 패키지의 리소스 역할을 변경 하는 경우 해당 변경 내용을 Azure AD에서 수행 하려면 최대 24 시간이 걸릴 수 있습니다, 따라서 시간 및 다른 Microsoft Online Services 이러한 변경 내용을 전파 하는 데 걸리는 또는 SaaS 응용 프로그램 연결 s입니다. 몇 가지 개체에 영향을 주는 변경 내용을 변경 가능성이 걸립니다는 다른 Azure AD 구성 요소는 다음 검색 변경 하 고 SaaS 응용 프로그램을 업데이트 하는 Azure AD에서 적용 되려면 몇 분 정도입니다. 수천 개의 개체에 영향을 주는 변경 내용에 변경 내용을 더 오래 걸립니다. 예를 들어 2 응용 프로그램 및 100 사용자 할당을 사용 하 여 액세스 패키지를 있고 액세스 패키지에는 SharePoint 사이트 역할을 추가 하려는 경우 있을 수 있습니다 지연 해당 SharePoint 사이트 역할의 일부인 모든 사용자가 될 때까지. Azure AD 감사 로그, Azure AD 프로 비전 로그 및 SharePoint 사이트 감사 로그를 통해 진행률을 모니터링할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [카탈로그 소유자 또는 액세스 패키지 관리자를 추가 합니다.](entitlement-management-delegate.md#add-a-catalog-owner-or-an-access-package-manager)
- [프로세스 및 메일 알림 요청](entitlement-management-process.md)
