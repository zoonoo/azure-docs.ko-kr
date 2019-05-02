---
title: Azure AD 권한 관리 (미리 보기)-Azure Active Directory에서에서 새 액세스 패키지 만들기
description: Azure Active Directory 권한 관리 (미리 보기)에서 공유 하려는 리소스의 새 액세스 패키지를 만드는 방법에 알아봅니다.
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
ms.date: 04/24/2019
ms.author: rolyon
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ad6570a3f30e40e4074502a8ce85bf739f58d3f
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2019
ms.locfileid: "64866434"
---
# <a name="create-a-new-access-package-in-azure-ad-entitlement-management-preview"></a>Azure AD 권한 관리 (미리 보기)에서 새 액세스 패키지 만들기

> [!IMPORTANT]
> Azure Active Directory (Azure AD) 권한 관리는 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

액세스 패키지를 자동으로 액세스 패키지의 수명에 대 한 액세스를 관리 하는 리소스 및 정책의 일회성 설치를 수행할 수 있도록 합니다. 이 문서에는 새 액세스 패키지를 만드는 방법을 설명 합니다.

## <a name="overview"></a>개요

모든 액세스 패키지 카탈로그 라는 컨테이너에 넣어야 합니다. 카탈로그는 액세스 패키지에 추가할 수 있는 어떤 리소스를 정의 합니다. 카탈로그를 지정 하지 않으면 일반 카탈로그로 액세스 패키지 해질 수 있습니다. 현재 기존 액세스 패키지를 다른 카탈로그를 이동할 수 없습니다.

모든 액세스 패키지에는 하나 이상의 정책이 있어야 합니다. 정책은은 액세스 패키지와도 승인 및 만료 설정을 요청할 수 있는 사용자를 지정 합니다. 새 액세스 패키지를 만든 경우 직접 할당만 관리자에 대 한 디렉터리에 없는 사용자에 대 한 디렉터리에서 사용자에 대 한 초기 정책을 만들 수 있습니다 또는 나중에 정책을 만들 수 있습니다.

다음 다이어그램은 새 액세스 패키지를 만드는 대략적인 프로세스를 보여 줍니다.

![액세스 패키지 프로세스 만들기](./media/entitlement-management-access-package-create/access-package-process.png)

## <a name="start-new-access-package"></a>새 액세스 패키지를 시작 합니다.

**필수 구성 요소 역할:** 카탈로그 소유자 또는 사용자 관리자

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. 클릭 **Azure Active Directory** 을 클릭 한 다음 **Identity 거 버 넌 스**합니다.

1. 왼쪽된 메뉴에서 클릭 **패키지에 액세스할**합니다.

    ![Azure portal에서 자격 관리](./media/entitlement-management-shared/elm-access-packages.png)

1. 클릭 **새 액세스 패키지**합니다.

## <a name="basics"></a>기본 사항

에 **기본 사항** 탭 액세스 패키지 이름을 지정 하는 카탈로그에 대 한 액세스 패키지를 만들려면를 지정 합니다.

1. 표시 이름 및 액세스 패키지에 대 한 설명을 입력 합니다. 사용자는 액세스 패키지에 대 한 요청을 제출할 때이 정보를 표시 됩니다.

1. 에 **카탈로그** 드롭 다운 목록에서 액세스를 만들려는 카탈로그에 패키지 합니다. 예를 들어, 요청할 수 있는 모든 마케팅 리소스를 관리 하는 카탈로그 소유자를 해야 합니다. 이 경우 마케팅 카탈로그를 선택할 수 있습니다.

    카탈로그에만 나타납니다에서 액세스 패키지를 만들 수 있는 권한이 있습니다. 기존 카탈로그에 대 한 액세스 패키지를 만들려면 하나 이상의 사용자 관리자, 카탈로그 소유자 또는 패키지 관리자 액세스 해야 합니다.

    ![패키지 액세스-기본 사항](./media/entitlement-management-access-package-create/basics.png)

    새 카탈로그에 대 한 액세스 패키지를 생성 하려는 경우 클릭 **새로 만들기**합니다. 카탈로그 이름 및 설명을 입력 한 다음 클릭 **만들기**합니다.

    만들려는 액세스 패키지 하 고 그 안에 포함 된 모든 리소스는 새 카탈로그에 추가 됩니다. 또한 카탈로그의 첫 번째 소유자 됩니다 자동으로 됩니다. 추가 카탈로그 소유자를 추가할 수 있습니다.

    새 카탈로그를 만들려면 하나 이상의 사용자 관리자 또는 카탈로그를 만든 여야 합니다.

1. **다음**을 클릭합니다.

## <a name="resource-roles"></a>리소스 역할

에 **리소스 역할** 액세스 패키지에 포함할 리소스를 선택 하면 탭 합니다.

1. 추가 하려는 리소스 유형을 클릭 (**그룹**하십시오 **응용 프로그램**, 또는 **SharePoint 사이트**).

1. 표시 되는 선택 창에서 목록에서 하나 이상의 리소스를 선택 합니다.

    ![리소스 역할-액세스 패키지](./media/entitlement-management-access-package-create/resource-roles.png)

    일반 카탈로그 또는 새 카탈로그 액세스 패키지를 만드는 경우 사용자가 소유한 디렉터리에서 모든 리소스를 선택할 수 됩니다. 사용자 관리자 이거나 작성자 카탈로그 있어야 합니다.

    기존 카탈로그에 액세스 패키지를 만들려는 경우이 소유 하지 않고도 카탈로그에 이미 있는 모든 리소스를 선택할 수 있습니다.

    사용자 관리자 또는 카탈로그 소유자 인 경우 아직 카탈로그에 없는 소유 하는 리소스를 선택 하는 추가 옵션도 있습니다. 선택한 카탈로그에 현재 리소스를 선택 하는 경우 이러한 리소스를 사용 하 여 액세스 패키지 빌드를 다른 카탈로그 관리자에 대 한 카탈로그도 추가 됩니다. 현재 선택한 카탈로그, 확인 된 리소스를 선택 하려는 경우는 **스탬프만** 선택 pan의 맨 위에 있는 확인란입니다.

1. 리소스를 선택한 후는 **역할** 목록 역할을 할당할 사용자 리소스를 선택 합니다.

    ![패키지 액세스-리소스 역할 선택](./media/entitlement-management-access-package-create/resource-roles-role.png)

1. **다음**을 클릭합니다.

## <a name="policy"></a>정책

에 **정책** 액세스 패키지와도 승인 및 만료 설정을 요청할 수 있는 사용자 지정 하려면 첫 번째 정책을 만든 탭 합니다. 나중에 추가 사용자 그룹을 자체 승인 및 만료 설정을 사용 하 여 액세스 패키지를 요청할 수 있도록 자세한 정책을 만들 수 있습니다. 나중에 정책을 만들 수도 있습니다.

1. 설정 된 **첫 번째 정책 만들기** 토글을 **이제** 또는 **나중에**입니다.

    ![패키지 액세스-정책](./media/entitlement-management-access-package-create/policy.png)

1. 선택 하는 경우 **나중**를 아래로 이동 합니다 [검토 + 만들기](#review--create) 액세스 패키지를 만드는 섹션입니다.

1. 선택 하는 경우 **이제**, 다음 정책 섹션 중 하나의 단계를 수행 합니다.

[!INCLUDE [Entitlement management policy](../../../includes/active-directory-entitlement-management-policy.md)]

## <a name="review--create"></a>검토 + 만들기

에 **검토 + 만들기** 탭, 설정 및 유효성 검사 오류에 대 한 확인을 검토할 수 있습니다.

1. 액세스 패키지의 설정을 검토 합니다.

    ![패키지 액세스-정책 지원 정책 설정](./media/entitlement-management-access-package-create/review-create.png)

1. 클릭 **만들기** 액세스 패키지를 만듭니다.

    새 액세스 패키지 액세스 패키지 목록에 나타납니다.

## <a name="next-steps"></a>다음 단계

- [편집 및 기존 액세스 패키지 관리](entitlement-management-access-package-edit.md)
- [만들기 및 카탈로그 관리](entitlement-management-catalog-create.md)
