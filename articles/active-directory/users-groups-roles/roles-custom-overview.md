---
title: 사용자 지정 가능한 권한으로 Azure 관리자 역할 미리 보기-Azure Active Directory | Microsoft Docs
description: Id 관리를 위임 하기 위한 사용자 지정 Azure AD 역할을 미리 봅니다. Azure Portal, PowerShell 또는 Graph API에서 Azure 역할을 관리 합니다.
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 07/31/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 82638e3e102f7b8e39cd797960a11f3193132bc1
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/05/2019
ms.locfileid: "68779382"
---
# <a name="custom-administrator-roles-in-azure-active-directory-preview"></a>Azure Active Directory의 사용자 지정 관리자 역할 (미리 보기)

이 문서에서는 Azure Active Directory (Azure AD)에서 새 사용자 지정 RBAC (역할 기반 액세스 제어) 및 리소스 범위를 이해 하는 방법을 설명 합니다. 사용자 지정 RBAC 역할은 [기본 제공 역할](directory-assign-admin-roles.md) 의 기본 사용 권한을 표시 하므로 사용자 지정 역할을 만들고 구성할 수 있습니다. 리소스 범위를 사용 하면 모든 리소스 (모든 응용 프로그램)에 대 한 액세스를 제공 하지 않고 일부 리소스 (예: 응용 프로그램)를 관리 하는 사용자 지정 역할을 할당할 수 있습니다.

사용자 지정 RBAC 역할을 사용 하 여 권한을 부여 하는 작업은 두 단계로 진행 됩니다. 먼저 사용자 지정 역할 정의를 만들고 기본 설정 목록에서 해당 정의에 대 한 권한을 추가 합니다. 이러한 권한은 기본 제공 역할에 사용 되는 것과 동일한 권한입니다. 역할을 만들었으면 역할 할당을 만들어 사용자에 게 할당 합니다. 이 2 단계 프로세스를 통해 하나의 역할을 만들고 여러 범위에서 여러 번 할당할 수 있습니다. 사용자 지정 역할은 디렉터리 범위에서 할당 하거나 개체 범위에서 할당할 수 있습니다. 개체 범위의 예로는 단일 응용 프로그램이 있습니다. 이렇게 하면 디렉터리의 모든 응용 프로그램에 대해 Sally에 동일한 역할을 할당 한 다음 Contoso Expense Reports 앱만 Naveen 수 있습니다.

사용자 지정 RBAC 역할의 첫 번째 릴리스에는 앱 등록을 관리 하는 권한을 할당 하는 역할을 만들 수 있는 기능이 포함 되어 있습니다. 시간이 지남에 따라 엔터프라이즈 응용 프로그램, 사용자, 장치 등의 조직 리소스에 대 한 추가 권한이 추가 됩니다.

미리 보기 기능:

- 사용자 지정 역할을 만들고 관리 하 고 조직 전체 범위에서 사용자에 게 할당 하기 위한 포털 UI 업데이트
- 새 cmdlet을 사용 하 여 다음을 수행 하는 미리 보기 PowerShell 모듈입니다.
  - 사용자 지정 역할 만들기 및 관리
  - 조직 전체 또는 앱 별 등록 범위를 사용 하 여 사용자 지정 역할을 할당 합니다.
  - 조직 전체의 범위 (GA cmdlet을 사용 하는 패리티)에서 기본 제공 역할 할당
  - Azure AD Graph API 지원

Azure AD 역할 기반 액세스 제어는 Azure AD의 공개 미리 보기 기능이 며 유료 Azure AD 라이선스 계획과 함께 사용할 수 있습니다. 미리 보기에 대한 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="understand-azure-ad-role-based-access-control"></a>Azure AD 역할 기반 액세스 제어 이해

Azure AD 역할 기반 액세스 제어를 사용 하면 단일 유형의 Azure AD 리소스에 대해서만 허용 된 작업을 허용 하도록 사용자 지정 된 역할을 할당할 수 있습니다. Azure AD 역할 기반 액세스는 azure 역할 기반 액세스 제어 (azure 리소스 액세스에 대 한[AZURE RBAC](../../role-based-access-control/overview.md) )와 유사한 개념에 대해 작동 하지만 azure ad 역할 기반 액세스 제어는 Microsoft Graph을 기반으로 하며, azure rbac는 Azure Resource Manager를 기반으로 합니다. 그러나 두 시스템은 모두 역할 할당에 대 한 기능을 기반으로 합니다.

### <a name="role-assignments"></a>역할 할당

Azure AD 역할 기반 액세스 제어를 사용 하 여 액세스를 제어 하는 방법은 권한을 적용 하는 데 사용 되는 **역할 할당**을 만드는 것입니다. 역할 할당은 다음과 같은 세 가지 요소로 구성 됩니다.

- 보안 주체
- 역할 정의
- 리소스 범위

역할 할당을 만들어서 액세스 권한을 부여하고, 역할 할당을 제거하여 액세스 권한을 취소합니다. Azure Portal, Azure AD PowerShell 및 Graph API를 사용 하 여 [역할 할당을 만들](roles-create-custom.md) 수 있습니다. [사용자 지정 역할에 대 한 할당을 별도로 볼](roles-view-assignments.md#view-the-assignments-of-a-role-with-single-application-scope-using-the-azure-ad-portal-preview)수 있습니다.

다음 다이어그램은 역할 할당의 예를 보여줍니다. 이 예제에서는 SalesForce 응용 프로그램의 범위에서 [응용 프로그램 관리자](directory-assign-admin-roles.md#application-administrator) 역할이 Chris 녹색에 할당 되었습니다. 다른 역할 할당의 일부가 아닌 경우 Chris는 다른 응용 프로그램을 관리할 수 있는 권한이 없습니다.

![역할 할당은 사용 권한이 적용 되는 방식 이며 세 부분으로 구성 되어 있습니다.](./media/roles-custom-overview/rbac-overview.png)

### <a name="security-principal"></a>보안 주체

보안 주체는 Azure AD 리소스에 대 한 액세스 권한을 할당할 사용자 또는 서비스 주체를 나타냅니다. *사용자* 는 Azure Active Directory 사용자 프로필이 있는 개인입니다. *서비스 주체* 는 응용 프로그램이 나 서비스에서 특정 Azure AD 리소스에 액세스 하는 데 사용 하는 보안 id입니다.

보안 주체는 사용자 이름 및 암호 또는 인증서를 나타내지만 사용자가 아닌 응용 프로그램 또는 서비스에 대 한 사용자 id와 비슷합니다.

### <a name="role"></a>역할

역할 정의 또는 역할은 사용 권한 컬렉션입니다. 역할 정의는 만들기, 읽기, 업데이트 및 삭제와 같은 Azure AD 리소스에서 수행할 수 있는 작업을 나열 합니다. Azure AD에는 두 가지 유형의 역할이 있습니다.

- Microsoft에서 만든 기본 제공 역할은 변경할 수 없습니다. 전역 관리자 기본 제공 역할에는 모든 Azure AD 리소스에 대 한 모든 권한이 있습니다.
- 조직에서 만들고 관리 하는 사용자 지정 역할

### <a name="scope"></a>범위

범위는 특정 Azure AD 리소스에 대해 허용 되는 작업의 제한입니다. 역할을 할당할 때 관리자가 허용 하는 작업을 특정 리소스로 제한 하는 범위를 지정할 수 있습니다. 예를 들어 개발자에 게 사용자 지정 역할을 부여 하 고 특정 응용 프로그램 등록을 관리 하려는 경우 특정 응용 프로그램 등록을 역할 할당의 범위로 포함할 수 있습니다.

  > [!Note]
  > 사용자 지정 역할은 디렉터리 범위 및 리소스 범위에서 할당할 수 있습니다. 아직 관리 단위 범위에서 할당할 수 없습니다.
  > 기본 제공 역할은 디렉터리 범위에서 할당 될 수 있으며 일부 경우에는 관리 단위 범위에 할당할 수 있습니다. 아직 개체 범위에서 할당할 수 없습니다.

## <a name="required-license-plan"></a>필요한 라이선스 계획

[!INCLUDE [License requirement for using custom roles in Azure AD](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>다음 단계

- [Azure Portal, AZURE AD PowerShell 및 Graph API](roles-create-custom.md) 를 사용 하 여 사용자 지정 역할 할당을 만듭니다.
- [사용자 지정 역할에 대 한 할당 보기](roles-view-assignments.md#view-the-assignments-of-a-role-with-single-application-scope-using-the-azure-ad-portal-preview)
