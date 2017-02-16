---
title: "Office 365 계정을 사용하여 Azure에 등록 | Microsoft Docs"
description: "Office 365 Azure AD 테넌트 및 그 사용자를 Azure 구독과 공유하는 방법 및 그 반대로 공유하는 방법에 대해 알아봅니다."
services: 
documentationcenter: 
author: JiangChen79
manager: vikdesai
editor: 
tags: billing,top-support-issue
ms.assetid: 129cdf7a-2165-483d-83e4-8f11f0fa7f8b
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 12/16/2016
ms.author: cjiang
translationtype: Human Translation
ms.sourcegitcommit: 71240b7bb978ca0831a4e23cafa64a96d64dc3cd
ms.openlocfilehash: 85db551a80ce6995965aec642f08b3b87248d21b


---
# <a name="use-an-existing-office-365-account-with-your-azure-subscription-or-vice-versa"></a>기존 Office 365 계정을 Azure 구독에 사용하거나 그 반대로 사용하는 경우
시나리오: 이미 Office 365 구독이 있고 Azure 구독에 대해 준비되어 있지만, 기존 Office 365 사용자 계정을 Azure 구독에 사용하고 싶습니다. 또는, Azure 구독자인데 기존 Azure Active Directory의 사용자에 대한 Office 365 구독을 원합니다. 이 문서는 두 가지 모두를 쉽게 수행하는 방법을 보여줍니다.

> [!NOTE]
> 이 문서는 기업 계약(EA) 고객에게는 적용되지 않습니다. 이 문서에서 언제든지 도움말이 필요한 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.
>
>

## <a name="quick-guidance"></a>빠른 지침
* Office 365 구독이 있는 상태에서 Azure에 등록하려면, **조직 계정으로 로그인** 옵션을 사용합니다. 그런 다음 Office 365 계정으로 Azure 등록 프로세스를 진행합니다. [이 문서의 뒷부분에 나오는 자세한 단계](#s1)를 참조하세요.
* Azure 구독이 있는 상태에서 Office 365 구독을 확보하려면, Office 365에 Azure 계정으로 로그인합니다. 그런 다음 등록 단계를 진행합니다. 등록이 완료된 후 Azure 구독이 속해있는 Azure Active Directory 인스턴스에 Office 365 구독이 추가됩니다. 자세한 내용은 [이 문서의 뒷부분에 나오는 자세한 단계](#s2) 섹션을 참조하세요.

> [!NOTE]
> Office 365 구독을 확보하려면, 등록에 사용한 계정이 Azure Active Directory 테넌트 내 전역 관리자 또는 대금 청구 관리자 디렉터리 역할의 구성원이어야 합니다. [Azure Active Directory의 역할을 확인하는 방법을 알아봅니다](#how-to-know-your-role-in-your-azure-active-directory).
>
>

계정에 구독을 추가하면 어떻게 되는지 이해하려면, 이 문서 뒷쪽에 나오는 배경 정보를 참조하세요.

## <a name="detailed-steps"></a>자세한 단계
<a id="s1"></a>

### <a name="scenario-1-office-365-users-who-plan-to-buy-azure"></a>시나리오 1: Azure를 구입하려는 Office 365 사용자
이 시나리오에서 Kelley Wall은 Office 365 구독이 있는 사용자이며 Azure 구독을 계획 중이라고 가정합니다. Jane과 Tricia라는 활성 사용자가 두 명 더 있습니다. Kelley의 계정은 admin@contoso.onmicrosoft.com입니다.

![Office 365 사용자 관리 센터](./media/billing-use-existing-office-365-account-azure-subscription/1-office365-users-admin-center.png)

Azure에 등록하려면, 다음 단계를 수행하세요.

1. [Azure.com](https://azure.microsoft.com/)에서 Azure에 등록합니다. **무료 계정**을 클릭합니다. 다음 페이지에서 **무료 시작**을 클릭합니다.

    ![Azure를 무료로 사용해 봅니다.](./media/billing-use-existing-office-365-account-azure-subscription/2-azure-signup-try-free.png)
2. Office 365 계정으로 로그인합니다. 이 경우에는 Kelley의 Office 365 계정입니다.

    ![Office 365 계정으로 로그인합니다.](./media/billing-use-existing-office-365-account-azure-subscription/4-sign-in-with-org-account.png)
3. 정보를 입력하고 등록 절차를 완료합니다.

    ![정보를 입력하고 등록을 완료합니다.](./media/billing-use-existing-office-365-account-azure-subscription/5-azure-sign-up-fill-information.png)

    ![내 서비스 관리 시작을 클릭합니다.](./media/billing-use-existing-office-365-account-azure-subscription/6-azure-start-managing-my-service.png)

이제 모든 설정을 완료했습니다. Azure 포털에서 동일한 사용자가 표시됩니다. 이를 확인하려면 다음 단계를 수행하세요.

1. 이전에 표시된 화면에서 **내 서비스 관리 시작**을 클릭합니다.
2. **찾아보기**를 클릭한 다음 **Active Directory**를 클릭합니다.

    ![찾아보기를 클릭한 다음 Active Directory를 클릭합니다.](./media/billing-use-existing-office-365-account-azure-subscription/7-azure-portal-browse-ad.png)
3. **사용자**를 클릭합니다.

    ![사용자 탭](./media/billing-use-existing-office-365-account-azure-subscription/8-azure-portal-ad-users-tab.png)
4. Kelley를 포함한 모든 사용자가 예상대로 나열됩니다.

    ![사용자 목록](./media/billing-use-existing-office-365-account-azure-subscription/9-azure-portal-ad-users.png)

<a id="s2"></a>

### <a name="scenario-2-azure-users-who-plan-to-buy-office-365"></a>시나리오 2: Office 365를 구입하려는 Azure 사용자
이 시나리오에서 Kelley Wall은 admin@contoso.onmicrosoft.com 계정으로 Azure 구독을 소유하는 사용자입니다. Kelley는 Office 365를 구독하려고 하며, Azure에 이미 가지고 있는 디렉터리를 사용하고자 합니다.

> [!NOTE]
> Office 365 구독을 확보하려면, 로그인에 사용한 계정이 Azure Active Directory 테넌트 내 전역 관리자 또는 대금 청구 관리자 디렉터리 역할의 구성원이어야 합니다. [Azure Active Directory의 역할을 파악하는 방법을 알아봅니다](#how-to-know-your-role-in-your-azure-active-directory).
>
>

![Azure 포털 구독 설정](./media/billing-use-existing-office-365-account-azure-subscription/10-azure-portal-settings-subscription.png)

![Azure 포털 Active Directory 사용자](./media/billing-use-existing-office-365-account-azure-subscription/11-azure-portal-ads-users.png)

Office 365를 구독하려면, 다음 단계를 수행하세요.

1. [Office 365 제품 페이지](https://products.office.com/business)로 이동하여 적합한 요금제를 선택합니다.
2. 요금제를 선택하면 다음 페이지가 표시됩니다. 양식에 정보를 입력하지 마세요. 페이지의 오른쪽 위에서 **로그인**을 클릭합니다.

    ![Office 365 평가판 페이지](./media/billing-use-existing-office-365-account-azure-subscription/12-office-365-trial-page.png)
3. 계정 자격 증명으로 로그인합니다. 이 예에서는 Kelley의 계정입니다.

    ![Office 365 로그인](./media/billing-use-existing-office-365-account-azure-subscription/13-office-365-sign-in.png)
4. **지금 시도**를 클릭합니다.

    ![Office 365에 대한 주문을 확인합니다.](./media/billing-use-existing-office-365-account-azure-subscription/14-office-365-confirm-your-order.png)
5. 주문 접수 페이지에서 **계속**을 클릭합니다.

    ![Office 365 주문 접수](./media/billing-use-existing-office-365-account-azure-subscription/15-office-365-order-receipt.png)

이제 모든 설정을 완료했습니다. Office 365 관리 센터에, Contoso 디렉터리와 같은 사용자가 활성 사용자로 표시됩니다. 이를 확인하려면 다음 단계를 수행하세요.

1. Office 365 관리 센터를 엽니다.
2. **사용자**를 확장한 다음 **활성 사용자**를 클릭합니다.

    ![Office 365 관리 센터 사용자](./media/billing-use-existing-office-365-account-azure-subscription/16-office-365-admin-center-users.png)

### <a name="how-to-know-your-role-in-your-azure-active-directory"></a>Azure Active Directory에서 나의 역할을 파악하는 방법
1. [Azure 포털](https://portal.azure.com/)에 로그인합니다.
2. **찾아보기**를 클릭한 다음 **Active Directory**를 클릭합니다.

    ![Azure 포털의 Active Directory](./media/billing-use-existing-office-365-account-azure-subscription/7-azure-portal-browse-ad.png)
3. **사용자**를 클릭합니다.

    ![Azure 포털 기본 Active Directory 사용자](./media/billing-use-existing-office-365-account-azure-subscription/17-azure-portal-default-ad-users.png)
4. 사용자를 클릭합니다. 이 예제에서 사용자는 Kelley Wall입니다.

    **조직 역할** 필드에 주목합니다.

    ![Azure 포털 사용자 ID](./media/billing-use-existing-office-365-account-azure-subscription/18-azure-portal-user-identity.png)

## <a name="background-information-about-azure-and-office-365-subscriptions"></a>Azure 및 Office 365 구독에 대한 배경 정보
Office 365 및 Azure는 Azure Active Directory 서비스를 사용하여 사용자와 구독을 관리합니다. Azure 디렉터리는 사용자와 구독을 그룹화할 수 있는 컨테이너로 간주됩니다. Azure 및 Office 365 구독에 대해 동일한 사용자 계정을 사용하려면 구독을 동일한 디렉터리 내에 만들어야 합니다. 다음 사항을 주의하세요.

* 구독은 디렉터리 하위에 생성되며 그 반대로는 생성되지 않습니다.
* 사용자는 디렉터리에 속하며, 그 반대로는 속하지 않습니다.
* 구독은 구독을 생성하는 사용자의 디렉터리 내에 배치됩니다. 결과적으로, Office 365 구독은 Azure 구독 계정을 사용하여 Office 365 구독을 만들 때 Azure 구독과 동일한 계정에 연결됩니다.

![배경 정보](./media/billing-use-existing-office-365-account-azure-subscription/19-background-information.png)

자세한 내용은 [Azure 구독과 Azure Active Directory의 연관 관계](active-directory/active-directory-how-subscriptions-associated-directory.md)를 참조하세요.

> [!NOTE]
> Azure 구독은 디렉터리 내 개별 사용자가 소유합니다.
>
> [!NOTE]
> Office 365 구독은 디렉터리 자체에 의해 소유됩니다. 디렉터리 내 사용자에게 필수 사용 권한이 있으면 이러한 구독으로 작업할 수 있습니다.
>
>

## <a name="next-steps"></a>다음 단계
과거에 Azure 및 Office 365 구독을 모두 별도로 획득한 경우 Azure 구독에서 Office 365 테넌트에 액세스할 수 있도록 합니다. [Azure 구독과 Office 365 테넌트 연결](billing-add-office-365-tenant-to-azure-subscription.md)을 참조하세요.

> [!NOTE]
> 다른 질문이 있는 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.
>
>



<!--HONumber=Jan17_HO4-->


