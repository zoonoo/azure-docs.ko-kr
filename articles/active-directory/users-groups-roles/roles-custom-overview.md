---
title: Azure Active Directory |의 사용자 지정 관리자 역할 Microsoft Docs
description: Id 관리를 위임 하기 위한 사용자 지정 Azure AD 역할을 미리 봅니다. Azure Portal, PowerShell 또는 Graph API에서 Azure 역할을 관리 합니다.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: e5c7919dcc89e34831cb4cae7921b60b35eb4c69
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74024957"
---
# <a name="custom-administrator-roles-in-azure-active-directory-preview"></a>Azure Active Directory의 사용자 지정 관리자 역할 (미리 보기)

이 문서에서는 역할 기반 액세스 제어 및 리소스 범위를 사용 하 여 Azure Active Directory (Azure AD)에서 Azure AD 사용자 지정 역할을 이해 하는 방법을 설명 합니다. 사용자 지정 Azure AD 역할은 [기본 제공 역할](directory-assign-admin-roles.md)의 기본 사용 권한을 노출 하므로 사용자 지정 역할을 만들고 구성할 수 있습니다. 이 접근 방식을 사용 하면 필요에 따라 기본 제공 역할 보다 더 세분화 된 방식으로 액세스 권한을 부여할 수 있습니다. Azure AD 사용자 지정 역할의 첫 번째 릴리스에는 앱 등록을 관리 하는 권한을 할당 하는 역할을 만들 수 있는 기능이 포함 되어 있습니다. 시간이 지남에 따라 엔터프라이즈 응용 프로그램, 사용자, 장치 등의 조직 리소스에 대 한 추가 권한이 추가 됩니다.  

또한 Azure AD 사용자 지정 역할은 일반적인 조직 차원의 할당 외에도 리소스 단위로 할당을 지원 합니다. 이 접근 방식을 사용 하면 모든 리소스 (모든 앱 등록)에 대 한 액세스 권한을 부여 하지 않고도 일부 리소스 (예: 하나의 앱 등록)를 관리 하는 액세스 권한을 부여할 수 있습니다.

Azure AD 역할 기반 액세스 제어는 Azure AD의 공개 미리 보기 기능이 며 유료 Azure AD 라이선스 계획과 함께 사용할 수 있습니다. 미리 보기에 대한 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="understand-azure-ad-role-based-access-control"></a>Azure AD 역할 기반 액세스 제어 이해

사용자 지정 Azure AD 역할을 사용 하 여 권한을 부여 하는 작업은 사용자 지정 역할 정의를 만든 다음 역할 할당을 사용 하 여 할당 하는 두 단계로 이루어진 프로세스입니다. 사용자 지정 역할 정의는 미리 설정 된 목록에서 추가 하는 사용 권한 컬렉션입니다. 이러한 권한은 기본 제공 역할에 사용 되는 것과 동일한 권한입니다.  

역할 정의를 만든 후 역할 할당을 만들어 사용자에 게 할당할 수 있습니다. 역할 할당은 지정 된 범위에서 역할 정의의 사용 권한을 사용자에 게 부여 합니다. 이 2 단계 프로세스를 통해 단일 역할 정의를 만들고 여러 범위에서 여러 번 할당할 수 있습니다. 범위는 역할 구성원이 액세스할 수 있는 Azure AD 리소스 집합을 정의 합니다. 가장 일반적인 범위는 조직 전체 (조직 전체) 범위입니다. 조직 전체의 범위에서 사용자 지정 역할을 할당할 수 있습니다. 즉, 역할 구성원이 조직의 모든 리소스에 대 한 역할 권한을 가집니다. 사용자 지정 역할은 개체 범위에서 할당 될 수도 있습니다. 개체 범위의 예로는 단일 응용 프로그램이 있습니다. 조직의 모든 응용 프로그램에 대해 동일한 역할을 한 사용자에 게 할당 한 다음, Contoso Expense Reports 앱의 범위를 가진 다른 사용자에 게 할당할 수 있습니다.  

Azure AD 기본 제공 및 사용자 지정 역할 [은 azure 역할 기반 액세스 제어](../../role-based-access-control/overview.md)와 유사한 개념에 대해 작동 합니다. [이러한 두 역할 기반 액세스 제어 시스템의 차이점](../../role-based-access-control/rbac-and-directory-admin-roles.md) 은 azure RBAC는 Azure 리소스 관리를 사용 하 여 가상 머신 또는 저장소와 같은 azure 리소스에 대 한 액세스를 제어 하 고 azure ad 사용자 지정 역할은 Graph API를 사용 하 여 azure ad 리소스에 대 한 액세스를 제어 한다는 것입니다 두 시스템 모두 역할 정의 및 역할 할당의 개념을 활용 합니다.

### <a name="role-assignments"></a>역할 할당

역할 할당은 특정 범위의 사용자에 게 역할 정의를 연결 하 여 Azure AD 리소스 액세스 권한을 부여 하는 개체입니다. 역할 할당을 만들어서 액세스 권한을 부여하고, 역할 할당을 제거하여 액세스 권한을 취소합니다. 코어에서 역할 할당은 다음과 같은 세 가지 요소로 구성 됩니다.

- 사용자 (Azure Active Directory의 사용자 프로필이 있는 개인)
- 역할 정의
- 리소스 범위

Azure Portal, Azure AD PowerShell 또는 Graph API를 사용 하 여 [역할 할당을 만들](roles-create-custom.md) 수 있습니다. [사용자 지정 역할에 대 한 할당을 볼](roles-view-assignments.md#view-the-assignments-of-a-role)수도 있습니다.

다음 다이어그램은 역할 할당의 예를 보여줍니다. 이 예에서는 Contoso Widget Builder 앱 등록 범위에서 Chris 녹색에 앱 등록 관리자 사용자 지정 역할이 할당 되었습니다. 할당은이 특정 앱 등록에 대해서만 앱 등록 관리자 역할의 권한을 부여 합니다.

![역할 할당은 사용 권한이 적용 되는 방식 이며 세 부분으로 구성 되어 있습니다.](./media/roles-custom-overview/rbac-overview.png)

### <a name="security-principal"></a>보안 주체

보안 주체는 Azure AD 리소스에 대 한 액세스 권한을 할당할 사용자를 나타냅니다. *사용자* 는 Azure Active Directory 사용자 프로필이 있는 개인입니다.

### <a name="role"></a>역할

역할 정의 또는 역할은 사용 권한 컬렉션입니다. 역할 정의는 만들기, 읽기, 업데이트 및 삭제와 같은 Azure AD 리소스에서 수행할 수 있는 작업을 나열 합니다. Azure AD에는 두 가지 유형의 역할이 있습니다.

- Microsoft에서 만든 기본 제공 역할은 변경할 수 없습니다.
- 조직에서 만들고 관리 하는 사용자 지정 역할

### <a name="scope"></a>범위

범위는 역할 할당의 일부로 특정 Azure AD 리소스에 대해 허용 되는 작업을 제한 하는 것입니다. 역할을 할당할 때 특정 리소스에 대 한 관리자의 액세스를 제한 하는 범위를 지정할 수 있습니다. 예를 들어 개발자에 게 사용자 지정 역할을 부여 하 고 특정 응용 프로그램 등록만 관리 하려는 경우 특정 응용 프로그램 등록을 역할 할당의 범위로 포함할 수 있습니다.

  > [!Note]
  > 사용자 지정 역할은 디렉터리 범위 및 리소스 범위에서 할당할 수 있습니다. 아직 관리 단위 범위에서 할당할 수 없습니다.
  > 기본 제공 역할은 디렉터리 범위, 경우에 따라 관리 단위 범위에서 할당할 수 있습니다. 아직 Azure AD 리소스 범위에서 할당할 수 없습니다.

## <a name="required-license-plan"></a>필요한 라이선스 계획

[!INCLUDE [License requirement for using custom roles in Azure AD](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>다음 단계

- [Azure Portal, AZURE AD PowerShell 및 Graph API](roles-create-custom.md) 를 사용 하 여 사용자 지정 역할 할당을 만듭니다.
- [사용자 지정 역할에 대 한 할당 보기](roles-view-assignments.md#view-assignments-of-single-application-scope)
