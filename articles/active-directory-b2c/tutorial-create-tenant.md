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
ms.openlocfilehash: 711b9152f9f3fa1b3573e39d1950f18b628c268a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67056325"
---
# <a name="tutorial-create-an-azure-active-directory-b2c-tenant"></a>자습서: Azure Active Directory B2C 테넌트 만들기

애플리케이션이 Azure AD(Azure Active Directory) B2C와 상호 작용하려면 먼저 사용자가 관리하는 테넌트에 등록되어야 합니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * Azure AD B2C 테넌트 만들기
> * 구독에 테넌트 연결

애플리케이션을 등록하는 방법은 다음 자습서에서 알아봅니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="create-an-azure-ad-b2c-tenant"></a>Azure AD B2C 테넌트 만들기

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. 구독을 포함 하는 디렉터리 사용 했는지 확인 합니다. 클릭 합니다 **디렉터리 및 구독 필터** 위쪽 메뉴에서 구독을 포함 하는 디렉터리를 선택 합니다. 이 디렉터리는 Azure AD B2C 테 넌 트를 포함 하는 것과 다릅니다.

    ![구독 디렉터리로 전환](./media/tutorial-create-tenant/switch-directory-subscription.PNG)

3. Azure Portal의 왼쪽 위 모서리에서 **리소스 만들기**를 선택합니다.
4. **Active Directory B2C**를 검색하여 선택하고 **만들기**를 클릭합니다.
5. 선택 **새 Azure AD B2C 테 넌 트 만들기** 된 조직 이름 및 초기 도메인 이름을 입력 합니다. 국가/지역 (변경할 수 없습니다 나중)를 선택한 다음 클릭 **만들기**합니다.

    초기 도메인 이름은 테 넌 트 이름 부분으로 사용 됩니다. 이 예제에서는 테 넌 트 이름은 *contoso0926Tenant.onmicrosoft.com*:

    ![테넌트 만들기](./media/tutorial-create-tenant/create-tenant.PNG)

6. 에 **만들기 새 B2C 테 넌 트 또는 기존 테 넌 트에 연결할** 페이지에서 **내 Azure 구독에 연결 된 기존 Azure AD B2C 테 넌 트**합니다.

    만든 테 넌 트를 선택 하 고 구독을 선택 합니다.

    리소스 그룹 선택 **새로 만들기**합니다. 테넌트를 포함할 리소스 그룹의 이름을 입력한 다음, 위치를 선택하고 **만들기**를 클릭합니다.
1. 새 테 넌 트 사용을 시작 하려면 클릭 하 여 Azure AD B2C 테 넌 트를 포함 하는 디렉터리 사용 했는지 확인 합니다 **디렉터리 및 구독 필터** 최상위 메뉴 및 포함 된 디렉터리를 선택 합니다.

    처음에 새 Azure B2C 테 넌 트 목록에 보이지 않으면 경우 브라우저 창을 새로 고침을 선택 합니다 **디렉터리 및 구독 필터** 최상위 메뉴에서 다시 합니다.

    ![테넌트 디렉터리로 전환](./media/tutorial-create-tenant/switch-directories.PNG)

## <a name="next-steps"></a>다음 단계

이 문서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * Azure AD B2C 테넌트 만들기
> * 구독에 테넌트 연결

다음으로, 새 테 넌 트에서 웹 응용 프로그램을 등록 하는 방법에 알아봅니다.

> [!div class="nextstepaction"]
> [응용 프로그램 등록 >](tutorial-register-applications.md)
