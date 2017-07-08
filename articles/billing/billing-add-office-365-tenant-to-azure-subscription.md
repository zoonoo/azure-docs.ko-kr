---
title: "Azure 구독과 Office 365 테넌트 사용 | Microsoft Docs"
description: "Azure 구독에 Office 365 디렉터리(테넌트)를 추가하는 방법을 알아봅니다."
services: 
documentationcenter: 
author: JiangChen79
manager: jlian
editor: 
tags: billing,top-support-issue
ms.assetid: cc9c57c6-7bfd-4dea-9027-c75ef3737589
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: cjiang
ms.translationtype: Human Translation
ms.sourcegitcommit: 1cb57e5156dab976599ddfa9a58f26ca8ef1ee0e
ms.openlocfilehash: 42db903244e5642dbf273e71994d402f8aeda1e9
ms.contentlocale: ko-kr
ms.lasthandoff: 02/22/2017


---
# <a name="associate-an-office-365-tenant-to-an-azure-subscription"></a>Azure 구독에 Office 365 테넌트 연결
Azure 구독에서 Office 365 테넌트에 액세스할 수 있도록 별도 Azure 및 Office 365 구독을 연결합니다. 구독을 연결하려면 Azure 서비스 관리자 계정으로 Azure에 로그인하고 디렉터리를 추가한 다음 Azure Active Directory 테넌트에 Office 365 조직 계정을 추가합니다.

Azure Active Directory 인스턴스에서 사용자를 위한 Office 365 구독이 필요하거나 Office 365 계정이 있지만 Azure 계정이 없는 경우 [Office 365 계정을 사용하여 Azure에 등록](billing-use-existing-office-365-account-azure-subscription.md)을 참조하세요. 

## <a name="before-you-begin"></a>시작하기 전에
* Azure 구독 서비스 관리자의 자격 증명이 있어야 합니다. 공동 관리자 계정은 이 문서의 단계 중 일부를 수행할 수 없습니다. 서비스 관리자를 변경하려면 [Azure 관리자 역할을 추가 또는 변경하는 방법](billing-add-change-azure-subscription-administrator.md#change-service-administrator-for-a-subscription)을 참조하세요.
* Office 365 테넌트 전역 관리자의 자격 증명이 있어야 합니다.
* 서비스 관리자의 전자 메일 주소가 Office 365 테넌트에 있지 않아야 합니다.
* 서비스 관리자의 전자 메일 주소가 Office 365 전역 관리자의 전자 메일 주소와 일치하지 않아야 합니다.
* Microsoft 계정이며 조직 계정인 전자 메일 주소를 사용하는 경우 Azure 구독의 서비스 관리자를 일시적으로 변경하여 다른 Microsoft 계정을 사용합니다. [Microsoft 계정 등록 페이지](https://signup.live.com/)에서 Microsoft 계정을 만들 수 있습니다.

## <a name="link-office-365-tenant-to-azure-subscription"></a>Azure 구독에 Office 365 테넌트 연결
Azure 구독에 Office 365 테넌트를 연결하려면 다음 단계를 따릅니다.

### <a name="step-1-add-office-365-tenant-to-your-azure-subscription"></a>1단계: Azure 구독에 Office 365 테넌트 추가

1. 서비스 관리자 자격 증명을 사용하여 [Azure 클래식 포털](https://manage.windowsazure.com/)에 로그인합니다.

    ![Azure 로그인의 스크린샷](./media/billing-add-office-365-tenant-to-azure-subscription/s313_azure-sign-in-service-admin.png)

2. 왼쪽 창에서 **ACTIVE DIRECTORY**를 선택합니다. Office 365 테넌트가 표시되지 않아야 합니다. 표시되는 경우 [2단계: Azure 구독과 연결된 디렉터리 변경](#Step2)으로 건너뜁니다.
   
   ![Active Directory 항목의 스크린샷](./media/billing-add-office-365-tenant-to-azure-subscription/s35-classic-portal-active-directory-entry.png)

3. **새로 만들기** > **디렉터리** > **사용자 지정 만들기**를 선택합니다.
   
    ![Azure Active Directory 사용자 지정 만들기의 스크린샷](./media/billing-add-office-365-tenant-to-azure-subscription/s37-aad-custom-create.png)
   
4. **디렉터리 추가** 페이지의 **디렉터리**아래에서 **기존 디렉터리 사용**을 선택합니다. 그런 다음 **로그아웃할 준비가 되었습니다**를 선택하고 **완료** ![complete-icon](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png)을 선택합니다.
   
    !["기존 디렉터리 사용"의 스크린샷](./media/billing-add-office-365-tenant-to-azure-subscription/s39_add-directory-use-existing.png)
   
5. 로그인한 후에 Office 365 테넌트 전역 관리자의 자격 증명으로 로그인합니다.
   
    ![Office 365 전역 관리자 로그인의 스크린샷](./media/billing-add-office-365-tenant-to-azure-subscription/s310_sign-in-global-admin-office-365.png)
   
6. **계속**을 선택합니다.
   
    ![확인의 스크린샷](./media/billing-add-office-365-tenant-to-azure-subscription/s311_use-contoso-directory-azure-verify.png)
   
7. **지금 로그아웃**을 선택합니다.
   
    ![로그아웃의 스크린샷](./media/billing-add-office-365-tenant-to-azure-subscription/s312_use-contoso-directory-azure-confirm-and-sign-out.png)
   
8. 서비스 관리자 자격 증명을 사용하여 [Azure 클래식 포털](https://manage.windowsazure.com/)에 로그인합니다.
   
    ![Azure 로그인의 스크린샷](./media/billing-add-office-365-tenant-to-azure-subscription/s313_azure-sign-in-service-admin.png)
   
9. 대시보드에서 Office 365 테넌트가 표시되어야 합니다.
   
    ![대시보드의 스크린샷](./media/billing-add-office-365-tenant-to-azure-subscription/s314_office-365-tenant-appear-in-azure.png)

### <a name="Step2"></a>2단계: Azure 구독과 연결된 디렉터리 변경
   
1. **설정**을 선택합니다.
   
    ![Azure 클래식 포털 설정 아이콘의 스크린샷](./media/billing-add-office-365-tenant-to-azure-subscription/s315_azure-classic-portal-settings-icon.png)
   
2. Azure 구독을 선택한 다음 **디렉터리 편집**을 선택합니다.

    ![Azure 구독 편집 디렉터리의 스크린샷](./media/billing-add-office-365-tenant-to-azure-subscription/s316_azure-subscription-edit-directory.png)
   
3. **다음** ![다음 아이콘](./media/billing-add-office-365-tenant-to-azure-subscription/s317_next-icon.png)을 선택합니다.
   
    !["연결된 디렉터리 변경"의 스크린샷](./media/billing-add-office-365-tenant-to-azure-subscription/s318_azure-change-associated-directory.png)
   
4. 영향을 받는 계정을 검토합니다. 또한 기존 리소스 그룹에 할당된 액세스 권한이 있는 모든 공동 관리자 및 [RBAC(역할 기반 액세스 제어)](../active-directory/role-based-access-control-configure.md) 사용자도 제거됩니다. 받은 경고에서는 공동 관리자를 제거하라는 메시지만을 언급합니다.
      
    ![제거할 공동 관리자 계정을 보여 주는 스크린샷](./media/billing-add-office-365-tenant-to-azure-subscription/s322_azure-confirm-directory-mapping.png)
   
    ![제거할 예제 사용자 계정을 보여 주는 스크린샷](./media/billing-add-office-365-tenant-to-azure-subscription/s325_assigned-users-removed-resource-groups.png)
   
5. **완료** ![complete-icon](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png)을 선택합니다.

### <a name="step-3-add-your-office-365-organizational-accounts-as-co-administrators-to-the-azure-active-directory-tenant"></a>3단계: Office 365 조직 계정을 Azure Active Directory 테넌트에 공동 관리자로 추가
   
1. **관리자** 탭을 선택한 다음 **추가**를 선택합니다.
   
    ![Azure 클래식 포털 설정 관리자 탭의 스크린샷](./media/billing-add-office-365-tenant-to-azure-subscription/s319_azure-classic-portal-settings-administrators.png)
   
2. Office 365 테넌트의 조직 계정을 입력하고 Azure 구독을 선택한 다음 **완료** ![complete-icon](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png)을 선택합니다.
   
    ![Azure 공동 관리자 추가 대화 상자의 스크린샷](./media/billing-add-office-365-tenant-to-azure-subscription/s320_azure-add-co-administrator.png)
   
3. **관리자** 탭으로 돌아갑니다. 공동 관리자 권한으로 표시된 조직 계정을 확인해야 합니다.
   
    ![관리자 탭의 스크린샷](./media/billing-add-office-365-tenant-to-azure-subscription/s321_azure-co-administrator-added.png)
4.  공동 관리자 계정을 사용하여 Azure에 대한 액세스를 테스트합니다.
   
    a. Azure 클래식 포털에서 로그아웃합니다.
   
    b. [Azure 포털](https://portal.azure.com/)을 엽니다.
   
    c. 공동 관리자의 자격 증명을 입력한 다음 **로그인**을 선택합니다.
   
    ![Azure 로그인 페이지의 스크린샷](./media/billing-add-office-365-tenant-to-azure-subscription/s324_azure-sign-in-with-co-admin.png)

## <a name="need-help-contact-support"></a>도움이 필요하세요? 지원에 문의하세요.
다른 도움이 필요한 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.



