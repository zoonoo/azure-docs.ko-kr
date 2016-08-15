<properties
	pageTitle="단일 Azure AD 테넌트를 Office 365 및 Azure 구독 전체에서 공유 | Microsoft Azure"
	description="Office 365 Azure AD 테넌트 및 그 사용자를 Azure 구독과 공유하는 방법 및 그 반대로 공유하는 방법에 대해 알아봅니다."
	services=""
	documentationCenter=""
	authors="jiangchen79"
	manager="mbaldwin"
	editor=""
	tags="billing,top-support-issue"/>

<tags
	ms.service="billing"
	ms.workload="na"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/20/2016"
	ms.author="cjiang"/>

# 기존 Office 365 계정을 Azure 구독에 사용하거나 그 반대로 사용하는 경우
시나리오: 이미 Office 365 구독이 있고 Azure 구독에 대해 준비되어 있지만, 기존 Office 365 사용자 계정을 Azure 구독에 사용하고 싶습니다. 또는, Azure 구독자인데 기존 Azure Active Directory의 사용자에 대한 Office 365 구독을 원합니다. 이 문서는 두 가지 모두를 쉽게 수행하는 방법을 보여줍니다.

> [AZURE.NOTE] 이 문서는 기업 계약(EA) 고객에게는 적용되지 않습니다.

## 빠른 지침

- Office 365 구독이 있는 상태에서 Azure에 등록하려면, **조직 계정으로 로그인** 옵션을 사용하고 Office 365 계정으로 Azure 등록을 진행합니다. [자세한 단계](#s1)를 참조하세요.
- Azure 구독이 있는 상태에서 Office 365 구독을 확보하려면, Office 365에 Azure 계정으로 로그인하여 등록 절차를 진행합니다. 완료되면, Azure 구독이 속해있는 Azure Active Directory에 Office 365 구독이 추가됩니다. [자세한 단계](#s2)를 참조하세요.

>[AZURE.NOTE] Office 365 구독을 확보하려면, 로그인에 사용한 계정이 Azure AD 테넌트 내 전역 관리자 또는 대금 청구 관리자 디렉터리 역할의 구성원이어야 합니다. [Azure Active Directory의 역할을 파악하는 방법을 알아봅니다.](#how-to-know-your-role-in-your-azure-active-directory)

계정에 구독을 추가하면 어떻게 되는지 이해하려면, 이 문서의 뒤쪽에서 [배경 정보](#background-information)를 참조하세요.

## 자세한 단계
<a id="s1"></a>
### 시나리오 1: Office 365 사용자가 Azure 구입을 계획하는 경우
이 시나리오에서 Kelley Wall은 Office 365 구독이 있는 사용자이며 Azure 구독을 계획 중이라고 가정합니다. Jane과 Tricia라는 활성 사용자가 두 명 더 있습니다. Kelley의 계정은 admin@contoso.onmicrosoft.com입니다.

![office365-users-admin-center.png](./media/billing-use-existing-office-365-account-azure-subscription/1-office365-users-admin-center.png)

Azure에 등록하려면, 다음 단계를 수행하세요.

1. [Azure.com](https://azure.microsoft.com/)에서 Azure에 등록합니다. **무료 평가판**을 클릭합니다. 다음 페이지에서 **지금 시작**을 클릭합니다.

	![Azure 무료 평가판 등록](./media/billing-use-existing-office-365-account-azure-subscription/2-azure-signup-try-free.png)

2. **조직 계정을 사용하여 로그인**을 클릭합니다.

	![Azure에 로그인](./media/billing-use-existing-office-365-account-azure-subscription/3-sign-in-to-azure.png)

3. Office 365 계정으로 로그인합니다. 이 경우에는 Kelley의 Office 365 계정입니다.

	![조직 계정으로 로그인](./media/billing-use-existing-office-365-account-azure-subscription/4-sign-in-with-org-account.png)

4. 정보를 입력하고 등록 절차를 완료합니다.

	![Azure 등록 정보 입력](./media/billing-use-existing-office-365-account-azure-subscription/5-azure-sign-up-fill-information.png)

5. **내 서비스 관리 시작**을 클릭하고 계속 진행하세요.

	![Azure 내 서비스 관리 시작](./media/billing-use-existing-office-365-account-azure-subscription/6-azure-start-managing-my-service.png)

이제 모든 설정이 완료되었습니다. Azure 포털에, 동일한 사용자가 동일한 디렉터리에 표시됩니다. 이를 확인하려면 다음 단계를 수행하세요.

1. 위 화면에서 **내 서비스 관리 시작**을 클릭합니다.
2. **찾아보기**를 클릭한 다음 **Active Directory**를 클릭합니다.

	![Azure 포털 찾아보기](./media/billing-use-existing-office-365-account-azure-subscription/7-azure-portal-browse-ad.png)

3. **사용자**를 클릭합니다.

	![Azure 포털 AD 사용자 탭](./media/billing-use-existing-office-365-account-azure-subscription/8-azure-portal-ad-users-tab.png)

4. Kelley를 포함한 모든 사용자가 예상대로 나열됩니다.

	![Azure 포털 AD 사용자](./media/billing-use-existing-office-365-account-azure-subscription/9-azure-portal-ad-users.png)

<a id="s2"></a>
### 시나리오 2: Azure 사용자가 Office 365 구입을 계획하는 경우

이 시나리오에서 Kelley Wall은 admin@contoso.onmicrosoft.com 계정으로 Azure 구독을 소유하는 사용자입니다. Kelley는 Office 365를 구독하려고 하며, Azure에 이미 가지고 있는 디렉터리를 사용하고자 합니다.

>[AZURE.NOTE] Office 365 구독을 확보하려면, 로그인에 사용한 계정이 Azure AD 테넌트 내 전역 관리자 또는 대금 청구 관리자 디렉터리 역할의 구성원이어야 합니다. [Azure Active Directory의 역할을 파악하는 방법을 알아봅니다.](#how-to-know-your-role-in-your-azure-active-directory)

![Azure 포털 설정 구독](./media/billing-use-existing-office-365-account-azure-subscription/10-azure-portal-settings-subscription.png)

![Azure 포털 ADS 사용자](./media/billing-use-existing-office-365-account-azure-subscription/11-azure-portal-ads-users.png)

Office 365를 구독하려면, 다음 단계를 수행하세요.

1. [Office 365 제품 페이지](https://products.office.com/business)로 이동하여 적합한 요금제를 선택합니다.
2. 요금제를 선택하면 다음 페이지가 표시됩니다. 양식에 정보를 입력하지 마세요. 페이지의 상단 오른쪽에 있는 **로그인**을 클릭합니다.

	![Office 365 평가판 페이지](./media/billing-use-existing-office-365-account-azure-subscription/12-office-365-trial-page.png)

3. 사용자 계정의 자격 증명으로 로그인합니다. 이 경우에는 Kelley의 계정입니다.

	![Office 365 로그인](./media/billing-use-existing-office-365-account-azure-subscription/13-office-365-sign-in.png)

4. **지금 시도**를 클릭합니다.

	![Office 365 주문 확인](./media/billing-use-existing-office-365-account-azure-subscription/14-office-365-confirm-your-order.png)

5. 주문 접수 페이지에서 **계속**을 클릭합니다.

	![Office 365 주문 접수](./media/billing-use-existing-office-365-account-azure-subscription/15-office-365-order-receipt.png)

이제 모든 설정이 완료되었습니다. Office 365 관리 센터에, Contoso 디렉터리와 같은 사용자가 활성 사용자로 표시됩니다. 이를 확인하려면 다음 단계를 수행하세요.

1. Office 365 관리 센터를 엽니다.
2. **사용자**를 확장한 다음 **활성 사용자**를 클릭합니다.

	![Office 365 관리 센터 사용자](./media/billing-use-existing-office-365-account-azure-subscription/16-office-365-admin-center-users.png)

### Azure Active Directory에서 나의 역할을 파악하는 방법

1. [Azure 포털](https://portal.azure.com/)에 로그인합니다.
2. **찾아보기**를 클릭한 다음 **Active Directory**를 클릭합니다.

	![Azure 포털 찾아보기](./media/billing-use-existing-office-365-account-azure-subscription/7-azure-portal-browse-ad.png)

3. **사용자**를 클릭합니다.

	![Azure 포털 기본 AD 사용자](./media/billing-use-existing-office-365-account-azure-subscription/17-azure-portal-default-ad-users.png)

4. 사용자를 클릭합니다. 이 예제에서는 Kelley Wall입니다.
5. **조직 내 역할** 필드에 주목합니다.

	![Azure 포털 사용자 ID](./media/billing-use-existing-office-365-account-azure-subscription/18-azure-portal-user-identity.png)

## 배경 정보
Office 365 및 Azure는 Azure Active Directory(AAD) 서비스를 사용하여 사용자와 구독을 관리합니다. Azure 디렉터리는 여러분이 사용자와 구독을 그룹화하는 컨테이너로 간주할 수 있습니다. Microsoft Azure 및 Office 365 구독에 대해 동일한 사용자 계정을 사용하려면, 구독을 동일한 디렉터리 내에 만들어야 합니다.

- 구독은 디렉터리 하위에 생성되며 그 반대로는 생성되지 않습니다.
- 사용자는 디렉터리에 속하며, 그 반대로는 속하지 않습니다.
- 구독은 구독을 생성하는 사용자의 디렉터리 내에 배치됩니다. 결과적으로, Office 365 구독은 Azure 구독 계정을 사용하여 Office 365 구독을 만들 때 Azure 구독과 동일한 계정에 연결됩니다.

![배경 정보](./media/billing-use-existing-office-365-account-azure-subscription/19-background-information.png)

자세한 내용은 [Azure 구독과 Azure Active Directory의 연관 관계](./active-directory/active-directory-how-subscriptions-associated-directory.md)를 참조하세요.

**참고:**

- Azure 구독은 디렉터리 내 개별 사용자가 소유합니다.
- Office 365 구독은 디렉터리 자체에 의해 소유됩니다. 디렉터리 내 사용자는 필수 권한이 있으면, 이러한 구독을 조정할 수 있습니다.

##다음 단계
과거에 Azure 및 Office 365 구독을 모두 별도로 획득하고 Azure 구독에서 Office 365 테넌트에 액세스할 수 있도록 하려는 관련된 시나리오가 있습니다. 이 작업을 수행하는 방법을 알아보려면 [Azure 구독과 Office 365 테넌트 연결](billing-add-office-365-tenant-to-azure-subscription.md)을 참조하세요.

<!---HONumber=AcomDC_0803_2016-->