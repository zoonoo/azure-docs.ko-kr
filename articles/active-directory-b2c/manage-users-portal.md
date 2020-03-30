---
title: Azure 포털에서 Azure AD B2C 소비자 사용자 계정을 삭제하기 & 만들기
description: Azure 포털을 사용하여 Azure AD B2C 디렉터리에서 소비자 사용자를 만들고 삭제하는 방법을 알아봅니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/09/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c8fad8b5bd2dccba36c0c6888712fa806f3cbfc8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78187222"
---
# <a name="use-the-azure-portal-to-create-and-delete-consumer-users-in-azure-ad-b2c"></a>Azure 포털을 사용하여 Azure AD B2C에서 소비자 사용자를 만들고 삭제합니다.

Azure Active Directory B2C(Azure AD B2C) 디렉터리에서 소비자 계정을 수동으로 만들려는 시나리오가 있을 수 있습니다. Azure AD B2C 디렉터리에서 소비자 계정은 사용자가 응용 프로그램 중 하나를 사용하도록 등록할 때 가장 일반적으로 만들어지지만 프로그래밍 방식으로 만들 수 있으며 Azure Portal을 사용하여 만들 수 있습니다. 이 문서에서는 사용자 만들기 및 삭제의 Azure 포털 방법에 대해 중점합니다.

사용자를 추가하거나 삭제하려면 계정에 사용자 *관리자* 또는 *글로벌 관리자* 역할이 할당되어야 합니다.

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="types-of-user-accounts"></a>사용자 계정의 유형

[Azure AD B2C의 사용자 계정 개요에](user-overview.md)설명된 대로 Azure AD B2C 디렉터리에서 만들 수 있는 세 가지 유형의 사용자 계정이 있습니다.

* Work
* Guest
* 소비자

이 문서에서는 Azure Portal의 **소비자 계정** 작업에 중점을 둡니다. 저작물 및 게스트 계정 만들기 및 삭제에 대한 자세한 내용은 Azure Active Directory 를 [사용하여 사용자 추가 또는 삭제를](../active-directory/fundamentals/add-users-azure-active-directory.md)참조하세요.

## <a name="create-a-consumer-user"></a>소비자 사용자 만들기

1. [Azure 포털에](https://portal.azure.com)로그인합니다.
1. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택한 다음, Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.
1. 왼쪽 메뉴에서 **Azure AD B2C**를 선택합니다. 또는 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
1. **관리**에서 **사용자**를 선택합니다.
1. **새 사용자**를 선택합니다.
1. **Azure AD B2C 사용자 만들기를**선택합니다.
1. 로그인 **방법을** 선택하고 새 사용자의 **전자 메일** 주소 또는 사용자 **이름을** 입력합니다. 여기에서 선택하는 로그인 방법은 Azure AD B2C 테넌트의 *로컬 계정* ID 공급자에 대해 지정한 설정과 일치해야 합니다(Azure AD B2C 테넌트의**ID 공급자** **관리** > 참조).
1. 사용자의 **이름을** 입력합니다. 일반적으로 사용자의 전체 이름(지정 및 성)입니다.
1. (선택 사항) 사용자가 **로그인하는** 기능을 지연하려는 경우 로그인을 차단할 수 있습니다. 나중에 Azure 포털에서 사용자 **프로필을** 편집하여 로그인을 활성화할 수 있습니다.
1. **암호 자동 생성을** 선택하거나 **암호를 만들 수 있습니다.**
1. 사용자의 **이름과** **성을**지정합니다.
1. **만들기**를 선택합니다.

**차단 로그인을**선택하지 않은 경우 사용자는 이제 지정한 로그인 방법(이메일 또는 사용자 이름)을 사용하여 로그인할 수 있습니다.

## <a name="delete-a-consumer-user"></a>소비자 사용자 삭제

1. Azure AD B2C 디렉터리에서 **사용자를**선택한 다음 삭제할 사용자를 선택합니다.
1. **삭제를**선택한 다음 **예를** 클릭하여 삭제를 확인합니다.

삭제 후 처음 30일 이내에 사용자를 복원하거나 사용자를 영구적으로 삭제하는 자세한 내용은 [Azure Active Directory를 사용하여 최근에 삭제된 사용자 복원 또는 제거를](../active-directory/fundamentals/active-directory-users-restore.md)참조하십시오.

## <a name="next-steps"></a>다음 단계

자동화된 사용자 관리 시나리오(예: 다른 ID 공급자에서 Azure AD B2C 디렉터리로 사용자 마이그레이션)의 경우 [Azure AD B2C: 사용자 마이그레이션](user-migration.md)을 참조하십시오.
