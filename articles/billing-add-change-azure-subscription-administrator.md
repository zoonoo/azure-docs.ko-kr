<properties
	pageTitle="Azure 공동 관리자, 서비스 관리자 및 계정 관리자를 추가 또는 변경하는 방법 | Microsoft Azure"
	description="Azure 공동 관리자, 서비스 관리자 및 계정 관리자를 추가 또는 변경하는 방법에 대해 설명합니다."
	services="billing"
	documentationCenter=""
	authors="genlin"
	manager="jarrettr"
	editor="meerak"
	tags="billing"
	/>

<tags
	ms.service="billing"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/20/2015"
	ms.author="genli"/>

# Azure 공동 관리자, 서비스 관리자 및 계정 관리자를 추가 또는 변경하는 방법
## 관리자 역할

Microsoft Azure에는 세 종류의 관리자 역할이 있습니다.

| 관리 역할 | 제한 | 설명
| ------------- | ------------- |---------------|
|계정 관리자 | Azure 계정당 1개 |Azure 구독을 등록했거나 구입했고, 계정 센터에 액세스하고 다양한 관리 작업을 수행할 권한이 부여된 사용자입니다. 이러한 관리 작업에는 구독 만들기, 구독 취소, 구독에 대한 대금 청구 변경 및 서비스 관리자 변경이 포함됩니다.
| 서비스 관리자 | Azure 구독당 1개 |할당된 계정에서 구독에 대한 Azure 관리 포털에 액세스할 수 있는 권한이 부여된 사용자입니다. 기본적으로 새 구독의 경우 계정 관리자가 서비스 관리자이기도 합니다.|
|공동 관리자|구독당 200개(서비스 관리자 외)|서비스 관리자와 동일한 액세스 권한이 있지만 Azure 디렉터리에 대한 구독의 연결을 변경할 수는 없는 사용자입니다.|

## 구독에 대한 공동 관리자 추가
1. [Azure 관리 포털](https://manage.windowsazure.com/)에 로그인합니다.
2. 탐색 창에서 **설정**> **관리자**> **추가**를 선택합니다.</br>![addcodmin](./media/billing-add-change-azure-subscription-administrator/addcoadmin.png)
3. 공동 관리자로 추가하려는 사람의 메일 주소를 입력한 다음 공동 관리자가 액세스하기를 원하는 구독을 선택합니다.</br> ![addcoadmin2](./media/billing-add-change-azure-subscription-administrator/addcoadmin2.png)</br>

다음 메일 주소를 공동 관리자로 추가할 수 있습니다.

* **Microsoft 계정**(이전의 Windows Live ID) </br> Microsoft 계정을 사용하여 Outlook(Hotmail), Skype(MSN), OneDrive, Windows Phone 및 Xbox LIVE와 같은 모든 소비자 지향 Microsoft 제품 및 클라우드 서비스에 로그인할 수 있습니다.
* **조직 계정**</br> 조직 계정은 Azure Active Directory에 만들어지는 계정입니다. 조직 계정 주소는 user@<your domain>.onmicrosoft.com 형식과 유사합니다.

**참고** * Microsoft 계정으로 로그인하는 경우 공동 관리자로는 다른 Microsoft 계정만 추가할 수 있습니다. 이는 조직 외부 계정에서 특정 계정(예: janedoe@contoso.com)이 유효한 계정인지 검색하는 것을 방지하는 보안 고려 사항입니다. * 조직 계정으로 로그인하는 경우 조직의 다른 조직 계정을 공동 관리자로 추가할 수 있습니다. 예를 들어 abby@contoso.com은 bob@contoso.com을 서비스 관리자 또는 공동 관리자로 추가할 수 있지만 john@notcontoso.com은 추가할 수 없습니다. 조직 계정으로 로그인한 사용자는 Microsoft 계정 사용자를 서비스 관리자 또는 공동 관리자로 계속 추가할 수 있습니다. * 이제 조직 계정으로 Azure에 로그인할 수 있습니다. 다음은 서비스 관리자 및 공동 관리자 계정 요구 사항에 대한 변경 사항입니다.

| 로그인 방법| Microsoft 계정을 공동 관리자 또는 서비스 관리자로 추가하나요? |같은 조직에 있는 조직 계정을 공동 관리자 또는 서비스 관리자로 추가하나요? |다른 조직에 있는 조직 계정을 공동 관리자 또는 서비스 관리자로 추가하나요?
| ------------- | ------------- |---------------|---------------|
|Microsoft 계정 |예|아니요|아니요|
|조직 계정|예|예|아니요|

## 구독에 대한 서비스 관리자 변경
계정 관리자만 구독에 대한 서비스 관리자를 변경할 수 있습니다.

1. 계정 관리자를 사용하여.[계정 관리 포털](https://account.windowsazure.com/subscriptions)에 로그온합니다.
2. 변경하려는 구독을 선택합니다.
3. 오른쪽에서 **구독 세부 정보 편집**을 클릭합니다. </br> ![editsub](./media/billing-add-change-azure-subscription-administrator/editsub.png)

4. **서비스 관리자** 상자에서 새 서비스 관리자의 메일 주소를 입력합니다. ![changeSA](./media/billing-add-change-azure-subscription-administrator/changeSA.png)

## 계정 관리자 변경(Azure 계정의 소유권을 다른 계정으로 이전)

Azure 계정의 소유권을 다른 계정으로 이전하려면 [Azure 구독 양도](../billing-subscription-transfer.md)를 참조하세요.

<!---HONumber=Nov15_HO1-->