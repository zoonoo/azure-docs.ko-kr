---
title: 리소스 정리 및 테넌트 삭제 - Azure Active Directory B2C
description: Azure AD B2C 테넌트를 삭제하는 방법을 설명하는 단계입니다. 모든 테넌트 리소스를 삭제한 다음, 테넌트를 삭제하는 방법을 알아봅니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/23/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5935847c4abdf3bc9da60937096a19d44fb2d09d
ms.sourcegitcommit: ad921e1cde8fb973f39c31d0b3f7f3c77495600f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/25/2021
ms.locfileid: "107982033"
---
# <a name="clean-up-resources-and-delete-the-tenant"></a>리소스 정리 및 테넌트 삭제

Azure AD B2C 자습서를 완료하면 테스트나 학습에 사용된 테넌트를 삭제할 수 있습니다. 테넌트를 삭제하려면 먼저 모든 테넌트 리소스를 삭제해야 합니다. 이 문서에서는 다음을 수행합니다.

> [!div class="checklist"]
> * **테넌트 삭제** 옵션을 사용하여 정리 작업 식별
> * 테넌트 리소스(사용자 흐름, ID 공급자, 애플리케이션, 사용자) 삭제
> * 테넌트 삭제

## <a name="identify-cleanup-tasks"></a>정리 작업 식별

1. 전역 관리자나 구독 관리자 역할로 [Azure Portal](https://portal.azure.com/)에 로그인합니다. Azure에 등록하는 데 사용한 같은 Microsoft 계정이나 동일한 회사 또는 학교 계정을 사용합니다.

2. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택한 다음, Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.

3. **Azure Active Directory** 서비스를 선택합니다.

4. **관리** 에서 **속성** 을 선택합니다.

5. **Azure 리소스에 대한 액세스 관리** 에서 **예** 를 클릭한 후 **저장** 을 클릭합니다.

6. Azure Portal에서 로그아웃한 다음, 다시 로그인하여 액세스를 새로 고칩니다. 다시 상단 메뉴에서 **디렉터리 + 구독** 필터를 사용하여 Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.

7. **Azure Active Directory** 서비스를 선택합니다.

8. **개요** 페이지에서 **테넌트 삭제** 를 선택합니다. **필수 작업** 열에 테넌트를 삭제하려면 먼저 제거해야 하는 리소스가 표시됩니다.

   ![테넌트 작업 삭제](media/tutorial-delete-tenant/delete-tenant-tasks.png)

## <a name="delete-tenant-resources"></a>테넌트 리소스 삭제

이전 섹션에서 확인 페이지를 열었으면 **필수 작업** 열에 있는 링크를 사용하여 해당 리소스를 제거할 수 있는 Azure Portal 페이지를 엽니다. 또는 다음 단계를 수행하여 Azure AD B2C 서비스 내에서 테넌트 리소스를 제거할 수 있습니다.

1. 전역 관리자나 구독 관리자 역할로 [Azure Portal](https://portal.azure.com/)에 로그인합니다. Azure에 등록하는 데 사용한 같은 Microsoft 계정이나 동일한 회사 또는 학교 계정을 사용합니다.

2. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택한 다음, Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.

3. **Azure AD B2C** 서비스를 선택합니다. 또는 검색 상자를 사용하여 **Azure AD B2C** 를 찾고 선택합니다.

4. **관리** 에서 **사용자** 를 선택하여 현재 로그인한 관리자 계정을 *제외한* 모든 사용자를 삭제합니다. **모든 사용자** 페이지에서 각 사용자 옆에 있는 확인란을 선택합니다(현재 로그인한 관리자 계정 제외). **삭제** 를 선택한 다음, 메시지가 표시되면 **예** 를 선택합니다.

   ![사용자 삭제](media/tutorial-delete-tenant/delete-users.png)
   
5. **관리** 에서 **앱 등록** 을 선택하여 앱 등록과 *b2c-extensions-app* 을 삭제합니다. **모든 애플리케이션** 탭을 선택합니다. 애플리케이션을 선택한 다음, **삭제** 를 선택합니다. **b2c-extensions-app** 애플리케이션을 포함하여 모든 애플리케이션에 반복합니다.

   ![애플리케이션 삭제](media/tutorial-delete-tenant/delete-applications.png)

6. **관리** 에서 **ID 공급자** 를 선택하여 구성한 모든 ID 공급자를 삭제합니다. 구성한 ID 공급자를 선택한 다음, **제거** 를 선택합니다.

   ![ID 공급자 삭제](media/tutorial-delete-tenant/identity-providers.png)

8. **정책** 에서 **사용자 흐름** 을 선택하여 사용자 흐름을 삭제합니다. 각 사용자 흐름 옆에 있는 줄임표(...)를 선택한 다음, **삭제** 를 선택합니다.

   ![사용자 흐름 삭제](media/tutorial-delete-tenant/user-flow.png)

9. **정책** 에서 **Identity Experience Framework** 를 선택한 다음, **정책 키** 를 선택하여 정책 키를 삭제합니다. 각 정책 키 옆에 있는 줄임표(...)를 선택한 다음, **삭제** 를 선택합니다.

10. **정책** 에서 **Identity Experience Framework** 를 선택하고 **사용자 지정 정책** 을 선택한 다음, 모든 정책을 삭제하여 사용자 지정 정책을 삭제합니다.

## <a name="delete-the-tenant"></a>테넌트 삭제

1. 전역 관리자나 구독 관리자 역할로 [Azure Portal](https://portal.azure.com/)에 로그인합니다. Azure에 등록하는 데 사용한 같은 Microsoft 계정이나 동일한 회사 또는 학교 계정을 사용합니다.

2. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택한 다음, Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.

3. **Azure Active Directory** 서비스를 선택합니다.

4. 아직 자신에게 액세스 관리 권한을 부여하지 않았으면 다음을 수행합니다.

   * **관리** 에서 **속성** 을 선택합니다.
   * **Azure 리소스에 대한 액세스 관리** 에서 **예** 를 클릭한 후 **저장** 을 클릭합니다.
   * Azure Portal에서 로그아웃한 다음, 다시 로그인하여 액세스를 새로 고치고 **Azure Active Directory** 서비스를 선택합니다.

5. **개요** 페이지에서 **테넌트 삭제** 를 선택합니다.

   ![테넌트 삭제](media/tutorial-delete-tenant/delete-tenant.png)

6. 화면 지시에 따라 프로세스를 완료합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 테넌트 리소스 삭제
> * 테넌트 삭제

다음으로 Azure AD B2C [사용자 흐름과 사용자 지정 정책](user-flow-overview.md)을 사용하여 시작하는 방법에 대해 자세히 알아봅니다.
