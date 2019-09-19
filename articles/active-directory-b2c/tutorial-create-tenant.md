---
title: 자습서-Azure Active Directory B2C 테 넌 트 만들기
description: Azure Portal을 사용하여 Azure Active Directory B2C 테넌트를 만들어서 애플리케이션 등록을 준비하는 방법을 알아봅니다.
services: B2C
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 06/07/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 57aed26fe2f2f03dca347836f21d1cad14a07642
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71063342"
---
# <a name="tutorial-create-an-azure-active-directory-b2c-tenant"></a>자습서: Azure Active Directory B2C 테넌트 만들기

응용 프로그램은 Azure Active Directory B2C (Azure AD B2C)와 상호 작용 하기 전에 관리 하는 테 넌 트에 등록 되어야 합니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * Azure AD B2C 테넌트 만들기
> * 구독에 테넌트 연결

애플리케이션을 등록하는 방법은 다음 자습서에서 알아봅니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="create-an-azure-ad-b2c-tenant"></a>Azure AD B2C 테넌트 만들기

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. 구독을 포함 하는 디렉터리를 사용 하 고 있는지 확인 합니다. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택 하 고 구독을 포함 하는 디렉터리를 선택 합니다. 이 디렉터리는 Azure AD B2C 테 넌 트를 포함 하는 디렉터리와 다릅니다.

    ![구독 테 넌 트가 선택 된 디렉터리 및 구독 필터](./media/tutorial-create-tenant/switch-directory-subscription.PNG)

3. Azure Portal의 왼쪽 위 모서리에서 **리소스 만들기**를 선택합니다.
4. **Active Directory B2C**를 검색하여 선택하고 **만들기**를 클릭합니다.
5. **새 Azure AD B2C 테 넌 트 만들기** 를 선택 하 고 조직 이름과 초기 도메인 이름을 입력 합니다. 국가/지역 (나중에 변경할 수 없음)을 선택 하 고 **만들기**를 클릭 합니다.

    초기 도메인 이름은 테 넌 트 이름의 일부로 사용 됩니다. 이 예제에서 테 넌 트 이름은 *contoso0926Tenant.onmicrosoft.com*입니다.

    ![Azure Portal의 B2C 테 넌 트 만들기 페이지](./media/tutorial-create-tenant/create-tenant.PNG)

6. **새 B2C 테 넌 트 만들기 또는 기존 테 넌 트에 연결** 페이지에서 **기존 Azure AD B2C 테 넌 트를 내 Azure 구독에 연결**을 선택 합니다.

    만든 테 넌 트를 선택 하 고 구독을 선택 합니다.

    리소스 그룹에 대해 **새로 만들기**를 선택 합니다. 테넌트를 포함할 리소스 그룹의 이름을 입력한 다음, 위치를 선택하고 **만들기**를 클릭합니다.
1. 새 테 넌 트 사용을 시작 하려면 맨 위 메뉴에서 **디렉터리 + 구독** 필터를 선택 하 고이를 포함 하는 디렉터리를 선택 하 여 Azure AD B2C 테 넌 트를 포함 하는 디렉터리를 사용 하 고 있는지 확인 하세요.

    처음에 새 Azure B2C 테 넌 트가 목록에 표시 되지 않으면 브라우저 창을 새로 고친 다음 맨 위 메뉴에서 **디렉터리 + 구독** 필터를 다시 선택 합니다.

    ![B2C 테 넌 트가 선택 된 디렉터리 및 구독 필터](./media/tutorial-create-tenant/switch-directories.PNG)

## <a name="next-steps"></a>다음 단계

이 문서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * Azure AD B2C 테넌트 만들기
> * 구독에 테넌트 연결

다음으로 새 테 넌 트에 웹 응용 프로그램을 등록 하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [응용 프로그램을 등록 >](tutorial-register-applications.md)
