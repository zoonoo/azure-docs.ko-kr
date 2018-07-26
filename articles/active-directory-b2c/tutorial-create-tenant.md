---
title: 자습서 - Azure Active Directory B2C 테넌트 만들기 | Microsoft Docs
description: Azure Portal을 사용하여 Azure Active Directory B2C 테넌트를 만들어서 응용 프로그램 등록을 준비하는 방법을 알아봅니다.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: conceptual
ms.date: 06/19/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: cc48cd3eb40d93c26a68caf843a89f7bbfb46c6c
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/25/2018
ms.locfileid: "39236896"
---
# <a name="tutorial-create-an-azure-active-directory-b2c-tenant"></a>자습서: Azure Active Directory B2C 테넌트 만들기

응용 프로그램이 Azure AD(Azure Active Directory) B2C와 상호 작용하려면 먼저 사용자가 관리하는 테넌트에 등록되어야 합니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * Azure AD B2C 테넌트 만들기
> * 구독에 테넌트 연결

응용 프로그램을 등록하는 방법은 다음 자습서에서 알아봅니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[Azure Portal](https://portal.azure.com/)에 로그인합니다.

## <a name="create-an-azure-ad-b2c-tenant"></a>Azure AD B2C 테넌트 만들기

1. Azure Portal의 왼쪽 위 모서리에서 **리소스 만들기**를 선택합니다.
2. Azure Marketplace 리소스 목록 위에 있는 검색 상자에서 **Active Directory B2C**를 검색하고 선택한 다음, **만들기**를 클릭합니다.
3. **새 Azure AD B2C 테넌트 만들기**를 선택하고 테넌트 이름에 사용된 조직 이름과 초기 도메인 이름을 입력한 다음, **만들기**를 클릭합니다. 테넌트의 국가는 나중에 변경할 수 없으니 확인하십시오.

    ![테넌트 만들기](./media/tutorial-create-tenant/create-tenant.png)

    이 예에서 테넌트 이름은 contoso0522Tenant.onmicrosoft.com입니다.

새 테넌트 관리를 시작하려면 **새 디렉터리를 관리 하려면 여기 클릭하십시오**에서 **여기**라는 단어를 클릭합니다. 구독을 새 테넌트에 연결해야 한다고 표시되는 **문제 해결** 메시지가 표시됩니다. 

## <a name="link-your-tenant-to-your-subscription"></a>구독에 테넌트 연결

모든 기능을 활성화하고 사용 요금을 지불하기 위해 Azure 구독에 Azure AD B2C테넌트를 연결해야 합니다. 테넌트를 구독에 연결하지 않으면 응용 프로그램이 제대로 작동하지 않습니다.

1. Azure Portal의 오른쪽 상단 모서리에서 디렉터리를 전환하여 새 테넌트에 연결할 구독이 포함된 디렉터리를 사용하고 있는지 확인합니다.

    ![디렉터리 전환](./media/tutorial-create-tenant/switch-directories.png)

    그런 다음, 구독이 포함된 디렉터리를 선택합니다.

    ![디렉터리 선택](./media/tutorial-create-tenant/select-directory.png)

2. Azure Portal의 왼쪽 위 모서리에서 **리소스 만들기**를 선택합니다.
3. Azure Marketplace 리소스 목록 위에 있는 검색 상자에서 **Active Directory B2C**를 검색하고 선택한 다음, **만들기**를 클릭합니다.
4. **내 Azure 구독에 기존 Azure AD B2C 테넌트 연결**을 선택하고, 만든 테넌트를 선택하고, 구독을 선택하고, 리소스 그룹 이름에 *myContosoTenantRG*를 입력하고, 위치를 수락한 다음, **만들기**를 클릭합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * Azure AD B2C 테넌트 만들기
> * 구독에 테넌트 연결

> [!div class="nextstepaction"]
> [웹 응용 프로그램이 계정을 인증하도록 설정](active-directory-b2c-tutorials-web-app.md)