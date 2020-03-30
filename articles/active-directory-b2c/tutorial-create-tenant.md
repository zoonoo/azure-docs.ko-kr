---
title: 자습서 - Azure Active Directory B2C 테넌트 만들기
description: Azure Portal을 사용하여 Azure Active Directory B2C 테넌트를 만들어서 애플리케이션 등록을 준비하는 방법을 알아봅니다.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c31f3c4c6688af7d2142180e8d9b7100965bad96
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186406"
---
# <a name="tutorial-create-an-azure-active-directory-b2c-tenant"></a>자습서: Azure Active Directory B2C 테넌트 만들기

응용 프로그램이 Azure Active Directory B2C(Azure AD B2C)와 상호 작용하려면 먼저 관리하는 테넌트에 등록해야 합니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * Azure AD B2C 테넌트 만들기
> * 구독에 테넌트 연결
> * Azure AD B2C 테넌트가 포함된 디렉터리로 전환
> * Azure 포털에서 Azure AD B2C 리소스를 **즐겨찾기로** 추가합니다.

애플리케이션을 등록하는 방법은 다음 자습서에서 알아봅니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="create-an-azure-ad-b2c-tenant"></a>Azure AD B2C 테넌트 만들기

1. [Azure 포털에](https://portal.azure.com/)로그인합니다. 구독 내에서 최소한 [기여자](../role-based-access-control/built-in-roles.md) 역할이 할당된 Azure 계정 또는 구독 내의 리소스 그룹으로 로그인합니다.

1. 구독이 포함된 디렉터리를 선택합니다.

    Azure 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택한 다음 구독이 포함된 디렉터리를 선택합니다. 이 디렉터리에는 Azure AD B2C 테넌트를 포함하는 디렉터리와 다릅니다.

    ![구독 테넌트, 구독 테넌트가 선택된 구독 처리장 + 구독 필터](media/tutorial-create-tenant/portal-01-pick-directory.png)

1. Azure Portal 메뉴 또는 **홈**페이지에서 **리소스 만들기**를 선택합니다.
1. Azure **Active Directory B2C를**검색한 다음 **을**선택합니다.
1. **새 Azure AD B2C 테넌트 만들기**를 선택합니다.

    ![Azure 포털에서 선택한 새 Azure AD B2C 테넌트 만들기](media/tutorial-create-tenant/portal-02-create-tenant.png)

1. 조직 **이름과** **초기 도메인 이름을**입력합니다. 국가 **또는 지역을** 선택한 다음(나중에 변경할 수 없음) **을 선택합니다.**

    도메인 이름은 전체 테넌트 도메인 이름의 일부로 사용됩니다. 이 예제에서 테넌트 이름은 *contosob2c.onmicrosoft.com.*

    ![Azure 포털의 예제 값으로 테넌트 양식 만들기](media/tutorial-create-tenant/portal-03-tenant-naming.png)

1. 테넌트 생성이 완료되면 **새 B2C 테넌트 만들기 또는 테넌트** 생성 페이지 상단의 기존 테넌트 링크링크를 선택합니다.

    ![Azure 포털에서 강조 표시된 테넌트 이동 경로 링크 링크](media/tutorial-create-tenant/portal-04-select-link-sub-link.png)

1. **기존 Azure AD B2C 테넌트를 내 Azure 구독에 연결합니다.**

   ![Azure 포털에서 기존 구독 선택 링크](media/tutorial-create-tenant/portal-05-link-subscription.png)

1. 만든 **Azure AD B2C 테넌트를** 선택한 다음 **구독**을 선택합니다.

    **리소스 그룹**에 대해 **새로 만들기**를 선택합니다. 테넌트를 포함하는 리소스 그룹에 대한 **이름을** 입력하고 **리소스 그룹 위치를**선택한 다음 **만들기를**선택합니다.

    ![Azure 포털에서 구독 설정 양식 링크](media/tutorial-create-tenant/portal-06-link-subscription-settings.png)

    청구를 위해 여러 Azure AD B2C 테넌을 단일 Azure 구독에 연결할 수 있습니다.

## <a name="select-your-b2c-tenant-directory"></a>B2C 테넌트 디렉터리 선택

새 Azure AD B2C 테넌트를 사용하려면 테넌트가 포함된 디렉터리로 전환해야 합니다.

Azure 포털의 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택한 다음 Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.

처음에 목록에 새 Azure B2C 테넌트가 표시되지 않으면 브라우저 창을 새로 고치고 상단 메뉴에서 **디렉터리 + 구독** 필터를 다시 선택합니다.

![Azure 포털에서 선택한 B2C 테넌트 포함 디렉터리](media/tutorial-create-tenant/portal-07-select-tenant-directory.png)

## <a name="add-azure-ad-b2c-as-a-favorite-optional"></a>Azure AD B2C를 즐겨찾기로 추가(선택 사항)

이 선택적 단계를 사용하면 다음 및 이후의 모든 자습서에서 Azure AD B2C 테넌트를 더 쉽게 선택할 수 있습니다.

테넌트와 함께 작업할 때마다 **모든 서비스에서** Azure *AD B2C를* 검색하는 대신 리소스를 즐겨찾기로 사용할 수 있습니다. 그런 다음 포털 메뉴의 **즐겨찾기** 섹션에서 선택하여 Azure AD B2C 테넌트로 빠르게 검색할 수 있습니다.

이 작업은 한 번만 수행하면 됩니다. 이러한 단계를 수행하기 전에 이전 섹션에 설명된 대로 Azure AD B2C 테넌트가 포함된 [Select your B2C tenant directory](#select-your-b2c-tenant-directory)디렉터리로 전환했는지 확인합니다.

1. [Azure 포털에](https://portal.azure.com)로그인합니다.
1. Azure 포털 메뉴에서 **모든 서비스를**선택합니다.
1. 모든 **서비스** 검색 상자에서 **Azure AD B2C를**검색하고 검색 결과 위로 마우스를 가져온 다음 도구 설명에서 별표 아이콘을 선택합니다. **Azure AD B2C는** 이제 **즐겨찾기**아래의 Azure 포털에 나타납니다.
1. 새 즐겨찾기의 위치를 변경하려면 Azure 포털 메뉴로 이동하여 **Azure AD B2C를**선택한 다음 원하는 위치로 끌어오십시오.

    ![Azure AD B2C, 즐겨찾기 메뉴, Microsoft Azure 포털](media/tutorial-create-tenant/portal-08-b2c-favorite.png)

## <a name="next-steps"></a>다음 단계

이 문서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * Azure AD B2C 테넌트 만들기
> * 구독에 테넌트 연결
> * Azure AD B2C 테넌트가 포함된 디렉터리로 전환
> * Azure 포털에서 Azure AD B2C 리소스를 **즐겨찾기로** 추가합니다.

다음으로 새 테넌트에 웹 응용 프로그램을 등록하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [응용 프로그램 등록 >](tutorial-register-applications.md)
