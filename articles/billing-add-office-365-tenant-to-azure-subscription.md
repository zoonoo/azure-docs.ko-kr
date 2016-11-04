---
title: Azure 구독과 Office 365 테넌트 사용 | Microsoft Docs
description: 연결할 Azure 구독에 Office 365 디렉터리(테넌트)를 추가하는 방법을 알아봅니다.
services: ''
documentationcenter: ''
author: JiangChen79
manager: mbaldwin
editor: ''
tags: billing,top-support-issue

ms.service: billing
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 09/16/2016
ms.author: cjiang

---
# <a name="associate-an-office-365-tenant-with-an-azure-subscription"></a>Azure 구독과 Office 365 테넌트 연결
과거에 Azure 및 Office 365 구독을 모두 별도로 획득한 경우 이제 Azure 구독에서 Office 365 테넌트에 액세스할 수 있도록 합니다. 작업을 수행하기 쉽습니다. 이 문서는 방법을 안내합니다.

> [!NOTE]
> 이 문서는 기업 계약(EA) 고객에게는 적용되지 않습니다.
> 
> 

## <a name="quick-guidance"></a>빠른 지침
Azure 구독과 Office 365 테넌트를 연결하려면 Azure 계정을 사용하여 Office 365 테넌트를 추가하고 Office 365 테넌트와 Azure 구독을 연결합니다.

## <a name="detailed-steps"></a>자세한 단계
이 시나리오에서 Kelley Wall은 kelley.wall@outlook.com. 계정으로 Azure 구독을 소유하는 사용자입니다. Kelley는 kelley.wall@contoso.onmicrosoft.com. 계정에서 Office 365도 구독합니다. 이제 Kelley는 Azure 구독으로 Office 365 테넌트에 액세스하려고 합니다.

![Azure Active Directory 상태의 스크린샷](./media/billing-add-office-365-tenant-to-azure-subscription/s31_msa-aad-status.png)

![Office 365 관리 센터 활성 사용자의 스크린샷](./media/billing-add-office-365-tenant-to-azure-subscription/s32_office-365-user.png)

### <a name="prerequisites"></a>필수 조건
연결을 제대로 작동하게 하려면 다음 필수 조건이 필요합니다.

* Azure 구독 서비스 관리자의 자격 증명이 필요합니다. 공동 관리자는 단계의 하위 집합을 실행할 수 없습니다.
* Office 365 테넌트 전역 관리자의 자격 증명이 필요합니다.
* 서비스 관리자의 전자 메일 주소가 Office 365 테넌트에 포함되지 않아야 합니다.
* 서비스 관리자의 전자 메일 주소가 Office 365 전역 관리자의 전자 메일 주소와 일치하지 않아야 합니다.
* Microsoft 계정이며 조직 계정인 전자 메일 주소를 현재 사용 중인 경우 Azure 구독의 서비스 관리자를 일시적으로 변경하여 다른 Microsoft 계정을 사용합니다. [Microsoft 계정 등록 페이지](https://signup.live.com/)에서 새 Microsoft 계정을 만들 수 있습니다.

서비스 관리자를 변경하려면 다음 단계를 수행합니다.

1. [계정 관리 포털](https://account.windowsazure.com/subscriptions)에 로그인합니다.
2. 변경하려는 구독을 선택합니다.
3. **구독 세부 사항 편집**을 선택합니다.
   
    !["구독 세부 정보 편집"이 강조 표시된 Azure 구독 정보의 스크린샷](./media/billing-add-office-365-tenant-to-azure-subscription/s33_azure-edit-subscription-details.png)
4. **서비스 관리자** 상자에서 새 서비스 관리자의 메일 주소를 입력합니다.
   
    !["구독 편집" 대화 상자의 스크린샷](./media/billing-add-office-365-tenant-to-azure-subscription/s34_change-subscription-service-admin.png)

### <a name="associate-the-office-365-tenant-with-the-azure-subscription"></a>Azure 구독과 Office 365 테넌트 연결
Azure 구독과 Office 365 테넌트를 연결하려면 다음 단계를 따릅니다.

1. 서비스 관리자 자격 증명을 사용하여 [계정 관리 포털](https://account.windowsazure.com/subscriptions)에 로그인합니다.
2. 왼쪽 창에서 **ACTIVE DIRECTORY**를 선택합니다.
   
   ![Active Directory 항목의 스크린샷](./media/billing-add-office-365-tenant-to-azure-subscription/s35-classic-portal-active-directory-entry.png)
   
   > [!NOTE]
   > Office 365 테넌트가 나타나지 않아야 합니다. 표시되는 경우 다음 단계를 건너뜁니다.
   > 
   > 
   
   ![Azure Active Directory 기본 디렉터리의 스크린샷](./media/billing-add-office-365-tenant-to-azure-subscription/s36-aad-tenant-default.png)
3. Azure 구독에 Office 365 테넌트를 추가합니다.
   
    a. **새로 만들기** > **디렉터리** > **사용자 지정 만들기**를 선택합니다.
   
    ![Azure Active Directory 사용자 지정 만들기의 스크린샷](./media/billing-add-office-365-tenant-to-azure-subscription/s37-aad-custom-create.png)
   
    b. **디렉터리 추가** 페이지의 **디렉터리**아래에서 **기존 디렉터리 사용**을 선택합니다. 그런 다음 **로그아웃할 준비가 되었습니다**를 선택하고 **완료** ![complete-icon](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png)을 선택합니다.
   
    !["기존 디렉터리 사용"의 스크린샷](./media/billing-add-office-365-tenant-to-azure-subscription/s39_add-directory-use-existing.png)
   
    c. 로그인한 후에 Office 365 테넌트 전역 관리자의 자격 증명으로 로그인합니다.
   
    ![Office 365 전역 관리자 로그인의 스크린샷](./media/billing-add-office-365-tenant-to-azure-subscription/s310_sign-in-global-admin-office-365.png)
   
    d. **계속**을 선택합니다.
   
    ![확인의 스크린샷](./media/billing-add-office-365-tenant-to-azure-subscription/s311_use-contoso-directory-azure-verify.png)
   
    e. **지금 로그아웃**을 선택합니다.
   
    ![로그아웃의 스크린샷](./media/billing-add-office-365-tenant-to-azure-subscription/s312_use-contoso-directory-azure-confirm-and-sign-out.png)
   
    f. 서비스 관리자 자격 증명을 사용하여 [계정 관리 포털](https://account.windowsazure.com/subscriptions)에 로그인합니다.
   
    ![Azure 로그인의 스크린샷](./media/billing-add-office-365-tenant-to-azure-subscription/s313_azure-sign-in-service-admin.png)
   
    g. 대시보드에서 Office 365 테넌트가 표시되어야 합니다.
   
    ![대시보드의 스크린샷](./media/billing-add-office-365-tenant-to-azure-subscription/s314_office-365-tenant-appear-in-azure.png)
4. 먼저 Azure 구독과 연결된 디렉터리를 변경합니다.
   
    a. **설정**을 선택합니다.
   
    ![Azure 클래식 포털 설정 아이콘의 스크린샷](./media/billing-add-office-365-tenant-to-azure-subscription/s315_azure-classic-portal-settings-icon.png)
   
    b. Azure 구독을 선택한 다음 **디렉터리 편집**을 선택합니다.
    ![Azure 구독 편집 디렉터리의 스크린샷](./media/billing-add-office-365-tenant-to-azure-subscription/s316_azure-subscription-edit-directory.png)
   
    c. **다음** ![next-icon](./media/billing-add-office-365-tenant-to-azure-subscription/s317_next-icon.png)을 선택합니다.
   
    !["연결된 디렉터리 변경"의 스크린샷](./media/billing-add-office-365-tenant-to-azure-subscription/s318_azure-change-associated-directory.png)
   
   > [!WARNING]
   > 모든 공동 관리자를 제거하라는 경고를 받게됩니다.
   > 
   > 
   
    ![azure-confirm-directory-mapping](./media/billing-add-office-365-tenant-to-azure-subscription/s322_azure-confirm-directory-mapping.png)
   
   > [!WARNING]
   > 또한 기존 리소스 그룹에 할당된 액세스 권한이 있는 모든 [RBAC(역할 기반 액세스 제어)](active-directory/role-based-access-control-configure.md) 사용자도 제거됩니다. 그러나 받은 경고에서는 공동 관리자를 제거하라는 메시지만을 언급합니다.
   > 
   > 
   
    ![assigned-users-removed-resource-groups](./media/billing-add-office-365-tenant-to-azure-subscription/s325_assigned-users-removed-resource-groups.png)
   
    d. **완료** ![complete-icon](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png)을 선택합니다.
5. 이제 Office 365 조직 계정을 Azure Active Directory 테넌트에 공동 관리자로 추가할 수 있습니다.
   
    a. **관리자** 탭을 선택한 다음 **추가**를 선택합니다.
   
    ![Azure 클래식 포털 설정 관리자 탭의 스크린샷](./media/billing-add-office-365-tenant-to-azure-subscription/s319_azure-classic-portal-settings-administrators.png)
   
    b. Office 365 테넌트의 조직 계정을 입력하고 Azure 구독을 선택한 다음 **완료** ![complete-icon](./media/billing-add-office-365-tenant-to-azure-subscription/s38_complete-icon.png)을 선택합니다.
   
    ![Azure 공동 관리자 추가 대화 상자의 스크린샷](./media/billing-add-office-365-tenant-to-azure-subscription/s320_azure-add-co-administrator.png)
   
    c. **관리자** 탭으로 돌아갑니다. 공동 관리자 권한으로 표시된 조직 계정을 확인해야 합니다.
   
    ![관리자 탭의 스크린샷](./media/billing-add-office-365-tenant-to-azure-subscription/s321_azure-co-administrator-added.png)
6. 다음으로 공동 관리자와 액세스를 테스트할 수 있습니다.
   
    a. 계정 관리 포털에서 로그아웃합니다.
   
    b. [계정 관리 포털](https://account.windowsazure.com/subscriptions) 또는 [Azure 포털](https://portal.azure.com/)을 엽니다.
   
    c. Azure 로그인 페이지가 **조직 계정으로 로그인**하는 링크를 포함하는 경우 링크를 선택합니다. 그렇지 않은 경우, 이 단계를 건너뜁니다.
   
    ![Azure 로그인 페이지의 스크린샷](./media/billing-add-office-365-tenant-to-azure-subscription/3-sign-in-to-azure.png)
   
    d. 공동 관리자의 자격 증명을 입력한 다음 **로그인**을 선택합니다.
   
    ![Azure 로그인 페이지의 스크린샷](./media/billing-add-office-365-tenant-to-azure-subscription/s324_azure-sign-in-with-co-admin.png)

## <a name="next-steps"></a>다음 단계
관련된 시나리오에는 다음이 포함됩니다.

* 이미 Office 365 구독이 있고 Azure 구독에 대해 준비되어 있지만, 기존 Office 365 사용자 계정을 Azure 구독에 사용하고 싶습니다.
* Azure 구독자인데 기존 Azure Active Directory 인스턴스의 사용자에 대한 Office 365 구독을 원합니다.

이러한 작업을 수행하는 방법을 알아보려면 [기존 Office 365 계정을 Azure 구독에 사용하거나 그 반대로 사용하는 경우](billing-use-existing-office-365-account-azure-subscription.md)를 참조하세요.

<!--HONumber=Oct16_HO2-->


