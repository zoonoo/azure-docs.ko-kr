---
title: 자습서 - Azure Active Directory B2C 테넌트 만들기 | Microsoft Docs
description: Azure Portal을 사용하여 Azure Active Directory B2C 테넌트를 만들어서 애플리케이션 등록을 준비하는 방법을 알아봅니다.
services: B2C
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: b8878eccb079bf78c45ff9c1e4040659d109b1ab
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60360200"
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
2. 구독을 포함하는 디렉터리를 사용하려면 위쪽 메뉴에서 **디렉터리 및 구독 필터**를 클릭하고 구독이 포함된 디렉터리를 선택합니다. 이 디렉터리는 Azure AD B2C 테넌트가 포함될 디렉터리와는 다릅니다.

    ![구독 디렉터리로 전환](./media/tutorial-create-tenant/switch-directory-subscription.png)

3. Azure Portal의 왼쪽 위 모서리에서 **리소스 만들기**를 선택합니다.
4. **Active Directory B2C**를 검색하여 선택하고 **만들기**를 클릭합니다.
5. **새 Azure AD B2C 테넌트 만들기**를 선택하고 테넌트 이름에 사용되는 조직 이름과 초기 도메인 이름을 입력한 다음 국가를 선택하고(나중에 변경할 수 없음) **만들기**를 클릭합니다.

    ![테넌트 만들기](./media/tutorial-create-tenant/create-tenant.png)

    이 예제에서 테넌트 이름은 contoso0926Tenant.onmicrosoft.com입니다.

6. **새 B2C 테넌트 만들기 또는 기존 테넌트에 연결** 페이지에서 **내 Azure 구독에 기존 Azure AD B2C 테넌트 연결**을 선택하고 만든 테넌트를 선택한 다음, 구독을 선택하고 **새로 만들기**를 클릭합니다.
7. 테넌트를 포함할 리소스 그룹의 이름을 입력한 다음, 위치를 선택하고 **만들기**를 클릭합니다.
8. 새 테넌트 사용을 시작하려면 위쪽 메뉴에서 **디렉터리 및 구독 필터**를 클릭하고 테넌트가 포함된 디렉터리를 선택하여 Azure AD B2C 테넌트를 포함하는 디렉터리를 사용하도록 지정합니다.

    ![테넌트 디렉터리로 전환](./media/tutorial-create-tenant/switch-directories.png)

## <a name="next-steps"></a>다음 단계

이 문서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * Azure AD B2C 테넌트 만들기
> * 구독에 테넌트 연결

> [!div class="nextstepaction"]
> [애플리케이션 등록](tutorial-register-applications.md)
