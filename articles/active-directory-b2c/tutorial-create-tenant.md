---
title: 자습서-Azure Active Directory B2C 테 넌 트 만들기
description: Azure Portal을 사용하여 Azure Active Directory B2C 테넌트를 만들어서 애플리케이션 등록을 준비하는 방법을 알아봅니다.
services: B2C
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: de29929c8fda476fe276f91d4a68ce4d632503fd
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71345218"
---
# <a name="tutorial-create-an-azure-active-directory-b2c-tenant"></a>자습서: Azure Active Directory B2C 테넌트 만들기

응용 프로그램은 Azure Active Directory B2C (Azure AD B2C)와 상호 작용 하기 전에 관리 하는 테 넌 트에 등록 되어야 합니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * Azure AD B2C 테넌트 만들기
> * 구독에 테넌트 연결
> * Azure AD B2C 테 넌 트를 포함 하는 디렉터리로 전환 합니다.
> * Azure Portal에서 Azure AD B2C 리소스를 *즐겨찾기로* 추가 합니다.

애플리케이션을 등록하는 방법은 다음 자습서에서 알아봅니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="create-an-azure-ad-b2c-tenant"></a>Azure AD B2C 테넌트 만들기

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. 구독을 포함 하는 디렉터리를 사용 하 고 있는지 확인 합니다.

    상단 메뉴에서 **디렉터리 + 구독** 필터를 선택 하 고 구독을 포함 하는 디렉터리를 선택 합니다. 이 디렉터리는 Azure AD B2C 테 넌 트를 포함 하는 디렉터리와 다릅니다.

    ![구독 테 넌 트가 선택 된 디렉터리 + 구독 필터](media/tutorial-create-tenant/portal-01-select-directory.png)

1. Azure Portal의 왼쪽 위 모서리에서 **리소스 만들기** 를 선택 합니다.
1. **Active Directory B2C**를 검색 하 고 선택한 다음 **만들기**를 선택 합니다.
1. **새 Azure AD B2C 테넌트 만들기**를 선택합니다.

    ![Azure Portal에서 선택한 새 Azure AD B2C 테 넌 트 만들기](media/tutorial-create-tenant/portal-02-create-tenant.png)

1. **조직 이름과** **초기 도메인 이름을**입력 합니다. **국가 또는 지역** (나중에 변경할 수 없음)을 선택 하 고 **만들기**를 선택 합니다.

    도메인 이름은 전체 테 넌 트 도메인 이름의 일부로 사용 됩니다. 이 예제에서 테 넌 트 이름은 *contosob2c.onmicrosoft.com*입니다.

    ![Azure Portal에서 예제 값을 사용 하 여 테 넌 트 폼 만들기](media/tutorial-create-tenant/portal-03-tenant-naming.png)

1. 테 넌 트 만들기가 완료 되 면 테 넌 트 만들기 페이지의 맨 위에 있는 **NEW B2C 테 넌 트를 만들거나 기존 테 넌 트에 연결** 링크를 선택 합니다.

    ![Azure Portal에 강조 표시 된 테 넌 트 링크 이동 경로 링크](media/tutorial-create-tenant/portal-04-select-link-sub-link.png)

1. **내 Azure 구독에 기존 Azure AD B2C 테 넌 트 연결을**선택 합니다.

   ![Azure Portal에서 기존 구독 선택 항목 연결](media/tutorial-create-tenant/portal-05-link-subscription.png)

1. 만든 **Azure AD B2C 테 넌 트** 를 선택 하 고 **구독**을 선택 합니다.

    **리소스 그룹**에 대해 **새로 만들기**를 선택합니다. 테 넌 트가 포함 될 리소스 그룹의 **이름을** 입력 하 고, **리소스 그룹 위치**를 선택한 다음, **만들기**를 선택 합니다.

    ![Azure Portal에서 구독 설정 양식 연결](media/tutorial-create-tenant/portal-06-link-subscription-settings.png)

## <a name="select-your-b2c-tenant-directory"></a>B2C 테 넌 트 디렉터리를 선택 합니다.

새 Azure AD B2C 테 넌 트 사용을 시작 하려면 테 넌 트가 포함 된 디렉터리로 전환 해야 합니다.

Azure Portal 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택 하 고 Azure AD B2C 테 넌 트가 포함 된 디렉터리를 선택 합니다.

처음에 새 Azure B2C 테 넌 트가 목록에 표시 되지 않으면 브라우저 창을 새로 고친 다음 맨 위 메뉴에서 **디렉터리 + 구독** 필터를 다시 선택 합니다.

![B2C 테 넌 트-Azure Portal에서 선택한 디렉터리를 포함 합니다.](media/tutorial-create-tenant/portal-07-select-tenant-directory.png)

## <a name="add-azure-ad-b2c-as-a-favorite-optional"></a>Azure AD B2C를 즐겨찾기로 추가 (선택 사항)

이 선택적 단계를 사용 하면 다음 및 모든 후속 자습서에서 Azure AD B2C 테 넌 트를 쉽게 선택할 수 있습니다.

테 넌 트로 작업할 때마다 **모든 서비스** 에서 "Azure AD B2C"을 검색 하는 대신 리소스를 즐겨찾기에 추가할 수 있습니다. 그런 다음 왼쪽 **즐겨찾기** 메뉴에서이를 선택 하 여 Azure AD B2C 테 넌 트를 빠르게 찾아볼 수 있습니다.

이 작업은 한 번만 수행 하면 됩니다. 이러한 단계를 수행 하기 전에 이전 섹션에서 [B2C 테 넌 트 디렉터리 선택](#select-your-b2c-tenant-directory)의 설명에 따라 Azure AD B2C 테 넌 트가 포함 된 디렉터리로 전환 했는지 확인 합니다.

1. [Azure Portal](https://portal.azure.com) 왼쪽 메뉴에서 **모든 서비스** 를 선택 합니다.
1. 검색 텍스트 상자에 *Azure AD B2C* 을 입력 합니다.
1. 즐겨찾기에 Azure AD B2C를 추가할 **별** 을 선택 합니다.
1. 이제 **즐겨찾기** 왼쪽 메뉴에 *Azure AD B2C* 표시 됩니다. 그런 다음, 다음 그림에 표시 된 것 처럼 원하는 경우 목록에서 더 위로 선택 하 여 끌 수 있습니다.

![Azure AD B2C를 즐겨찾기에 추가 하는 단계 Azure Portal](media/tutorial-create-tenant/portal-08-favorite-b2c.png)

## <a name="next-steps"></a>다음 단계

이 문서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * Azure AD B2C 테넌트 만들기
> * 구독에 테넌트 연결
> * Azure AD B2C 테 넌 트를 포함 하는 디렉터리로 전환 합니다.
> * Azure Portal에서 Azure AD B2C 리소스를 *즐겨찾기로* 추가 합니다.

다음으로 새 테 넌 트에 웹 응용 프로그램을 등록 하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [응용 프로그램을 등록 >](tutorial-register-applications.md)
