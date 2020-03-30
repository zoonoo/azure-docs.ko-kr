---
title: Azure Active Directory의 사용자 지정 관리자 역할 | 마이크로 소프트 문서
description: ID 관리를 위임하기 위한 사용자 지정 Azure AD 역할을 미리 봅슬입니다. Azure 포털, PowerShell 또는 그래프 API에서 Azure 역할을 관리합니다.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74024957"
---
# <a name="custom-administrator-roles-in-azure-active-directory-preview"></a>Azure Active Directory에서 사용자 지정 관리자 역할(미리 보기)

이 문서에서는 역할 기반 액세스 제어 및 리소스 범위를 사용하여 Azure Active Directory(Azure AD)에서 Azure AD 사용자 지정 역할을 이해하는 방법을 설명합니다. 사용자 지정 Azure AD 역할은 사용자 지정 역할을 만들고 구성할 수 있도록 [기본 제공 역할의](directory-assign-admin-roles.md)기본 사용 권한을 표시합니다. 이 방법을 사용하면 필요할 때마다 기본 제공 역할보다 더 세분화된 방식으로 액세스 권한을 부여할 수 있습니다. Azure AD 사용자 지정 역할의 첫 번째 릴리스에는 앱 등록 관리에 대한 권한을 할당하는 역할을 만드는 기능이 포함됩니다. 시간이 지남에 따라 엔터프라이즈 응용 프로그램, 사용자 및 장치와 같은 조직 리소스에 대한 추가 권한이 추가됩니다.  

또한 Azure AD 사용자 지정 역할은 보다 전통적인 조직 전체 할당 외에도 리소스별로 할당을 지원합니다. 이 방법을 사용하면 모든 리소스(모든 앱 등록)에 대한 액세스 권한을 부여하지 않고도 일부 리소스(예: 앱 등록 하나)를 관리할 수 있는 액세스 권한을 부여할 수 있습니다.

Azure AD 역할 기반 액세스 제어는 Azure AD의 공개 미리 보기 기능이며 유료 Azure AD 라이선스 계획에서 사용할 수 있습니다. 미리 보기에 대한 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관을](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)참조하십시오.

## <a name="understand-azure-ad-role-based-access-control"></a>Azure AD 역할 기반 액세스 제어 이해

사용자 지정 Azure AD 역할을 사용하여 권한을 부여하는 것은 사용자 지정 역할 정의를 만든 다음 역할 할당을 사용하여 할당하는 2단계 프로세스입니다. 사용자 지정 역할 정의는 미리 설정된 목록에서 추가하는 사용 권한의 모음입니다. 이러한 사용 권한은 기본 제공 역할에 사용되는 것과 동일한 사용 권한입니다.  

역할 정의를 만든 후에는 역할 할당을 만들어 사용자에게 할당할 수 있습니다. 역할 할당은 지정된 범위의 역할 정의에 대한 권한을 사용자에게 부여합니다. 이 2단계 프로세스를 사용하면 단일 역할 정의를 만들고 여러 범위에서 여러 번 할당할 수 있습니다. 범위는 역할 구성원이 액세스할 수 있는 Azure AD 리소스 집합을 정의합니다. 가장 일반적인 범위는 조직 전체(조직 전체) 범위입니다. 사용자 지정 역할은 조직 전체 범위에서 할당할 수 있으며, 이는 역할 구성원이 조직의 모든 리소스에 대한 역할 권한을 가지고 있다는 것을 의미합니다. 개체 범위에서 사용자 지정 역할을 할당할 수도 있습니다. 개체 범위의 예는 단일 응용 프로그램입니다. 조직의 모든 응용 프로그램에 대해 한 사용자에게 동일한 역할을 할당한 다음 Contoso 비용 보고서 앱의 범위만 있는 다른 사용자에게 할당할 수 있습니다.  

Azure AD 기본 제공 및 사용자 지정 역할은 [Azure 역할 기반 액세스 제어와](../../role-based-access-control/overview.md)유사한 개념에서 작동합니다. [이러한 두 역할 기반 액세스 제어 시스템의 차이점은](../../role-based-access-control/rbac-and-directory-admin-roles.md) Azure RBAC가 Azure 리소스 관리를 사용하여 가상 시스템 또는 저장소와 같은 Azure 리소스에 대한 액세스를 제어하고 Azure AD 사용자 지정 역할이 그래프 API를 사용하여 Azure AD 리소스에 대한 액세스를 제어한다는 것입니다. 두 시스템 모두 역할 정의 및 역할 할당의 개념을 활용합니다.

### <a name="role-assignments"></a>역할 할당

역할 할당은 Azure AD 리소스 액세스를 부여하기 위해 특정 범위의 사용자에게 역할 정의를 연결하는 개체입니다. 역할 할당을 만들어서 액세스 권한을 부여하고, 역할 할당을 제거하여 액세스 권한을 취소합니다. 역할 할당의 핵심은 다음 세 가지 요소로 구성됩니다.

- 사용자(Azure Active Directory에 사용자 프로필이 있는 개인)
- 역할 정의
- 리소스 범위

Azure 포털, Azure AD PowerShell 또는 그래프 API를 사용하여 [역할 할당을 만들](roles-create-custom.md) 수 있습니다. [사용자 지정 역할에 대한 할당을 볼](roles-view-assignments.md#view-the-assignments-of-a-role)수도 있습니다.

다음 다이어그램은 역할 할당의 예를 보여줍니다. 이 예제에서 Chris Green은 Contoso Widget Builder 앱 등록 범위에서 앱 등록 관리자 사용자 지정 역할을 할당받았습니다. 할당은 Chris에게 이 특정 앱 등록에 대해서만 앱 등록 관리자 역할의 권한을 부여합니다.

![역할 할당은 사용 권한이 적용되는 방법이며 세 부분으로 구성됩니다.](./media/roles-custom-overview/rbac-overview.png)

### <a name="security-principal"></a>보안 주체

보안 주체는 Azure AD 리소스에 대한 액세스 권한을 할당받을 사용자를 나타냅니다. *사용자는* Azure Active Directory에 사용자 프로필이 있는 개인입니다.

### <a name="role"></a>역할

역할 정의 또는 역할은 사용 권한의 컬렉션입니다. 역할 정의에는 만들기, 읽기, 업데이트 및 삭제와 같은 Azure AD 리소스에서 수행할 수 있는 작업이 나열됩니다. Azure AD에는 두 가지 유형의 역할이 있습니다.

- 변경할 수 없는 Microsoft에서 만든 기본 제공 역할입니다.
- 조직에서 만들고 관리하는 사용자 지정 역할입니다.

### <a name="scope"></a>Scope

범위는 역할 할당의 일부로 특정 Azure AD 리소스에 허용되는 작업의 제한입니다. 역할을 할당할 때 특정 리소스에 대한 관리자의 액세스를 제한하는 범위를 지정할 수 있습니다. 예를 들어 개발자에게 사용자 지정 역할을 부여하지만 특정 응용 프로그램 등록을 관리하려는 경우 특정 응용 프로그램 등록을 역할 할당의 범위로 포함할 수 있습니다.

  > [!Note]
  > 사용자 지정 역할은 디렉터리 범위 및 리소스 범위에서 할당할 수 있습니다. 관리 단위 범위에서 아직 할당할 수 없습니다.
  > 기본 제공 역할은 디렉터리 범위에서 할당할 수 있으며 경우에 따라 관리 단위 범위에서 할당할 수 있습니다. Azure AD 리소스 범위에서 아직 할당할 수 없습니다.

## <a name="required-license-plan"></a>필수 라이센스 플랜

[!INCLUDE [License requirement for using custom roles in Azure AD](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>다음 단계

- [Azure 포털, Azure AD PowerShell 및 그래프 API를](roles-create-custom.md) 사용하여 사용자 지정 역할 할당 만들기
- [사용자 지정 역할에 대한 할당 보기](roles-view-assignments.md#view-assignments-of-single-application-scope)
