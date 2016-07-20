<properties
	pageTitle="내 Azure 구독 관리를 위해 로그인할 수 없음 | Microsoft Azure"
	description="몇 가지 일반적인 Azure 구독 로그인 문제에 대한 문제 해결 정보를 설명합니다."
	services="billing"
	documentationCenter=""
	authors="genlin"
	manager="msmbaldwin"
	editor="na"
	tags="billing"
	/>

<tags
	ms.service="billing"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/30/2016"
	ms.author="genli"/>

# 내 Azure 구독 관리를 위해 로그인할 수 없음

> [AZURE.NOTE] 이 문서의 어디에서든 도움이 필요한 경우 [MSDN Azure 및 스택 오버플로 포럼](https://azure.microsoft.com/support/forums/)에서 Azure 전문가에게 문의할 수 있습니다. 또는 [Azure 지원 사이트](http://go.microsoft.com/fwlink/?linkid=544831&clcid=0x409)에 Azure 지원 인시던트를 제출할 수도 있습니다. Azure 지원을 사용하는 방법에 대한 자세한 내용은 [Microsoft Azure 지원 FAQ](https://azure.microsoft.com/support/faq/)를 참조하세요.

이 문서는 로그인 문제의 몇 가지 일반적인 원인을 해결하는 데 도움이 됩니다.

## 액세스하려는 포털은 무엇인가요?

계정 관리자는 [계정 센터](https://account.windowsazure.com/)에만 액세스할 수 있는 반면 SA(서비스 관리자) 및 CA(공동 관리자)는 [Azure 포털](https://portal.azure.com) 또는 [Azure 클래식 포털](https://manage.windowsazure.com/)에 대한 액세스 권한이 있습니다.

Azure 관리자 역할에 대한 자세한 내용은 [Azure 관리자 역할을 추가 또는 변경하는 방법](billing-add-change-azure-subscription-administrator.md)을 참조하세요.

## Microsoft 계정 또는 조직 계정에 연결된 구독인가요?

Microsoft 계정은 사용하는 메일 주소이며 암호와 함께 사용하여 Outlook, Hotmail, MSN 또는 OneDrive와 같은 Windows Live 프로그램 또는 서비스에 로그인합니다. 회사 메일을 비롯하여 사용하는 모든 메일 주소로 Microsoft 계정을 설정할 수 있습니다. 자세한 내용은 [www.microsoft.com/account](http://www.microsoft.com/account)를 참조하세요.

계정이 조직 계정에 연결된 경우 아래와 같이 올바른 로그인 옵션을 선택하세요. 조직 계정 사용에 대한 자세한 내용은 [조직으로 Azure에 등록](./active-directory/sign-up-organization.md)을 참조하세요.

![로그인 페이지](./media/billing-cannot-login-subscription/signin.png)

## 공동 관리자: 다른 계정을 관리하는 데 올바른 계정 유형을 사용 중인가요?

- Microsoft 계정으로 로그인하는 경우 다른 Microsoft 계정만 공동 관리자로 추가할 수 있습니다. 이는 조직 외부 계정이 특정 계정(예: janedoe@contoso.com)이 유효한 계정인지 탐색하는 것을 방지하기 위한 보안 요구 사항입니다.
- 조직 계정으로 로그인하는 경우 해당 조직의 다른 조직 계정을 공동 관리자로 추가할 수 있습니다. 예를 들어 abby@contoso.com은 bob@contoso.com을 서비스 관리자 또는 공동 관리자로 추가할 수 있지만 john@notcontoso.com은 추가할 수 없습니다. 또한 조직 계정을 사용하여 로그인한 사용자는 Microsoft 계정 사용자를 서비스 관리자 또는 공동 관리자로 추가할 수 있습니다.

이제 조직 계정으로 Azure에 로그인할 수 있으므로 SA(서비스 관리자) 및 CA(공동 관리자) 계정 요구 사항이 다음과 같이 변경되었습니다.

| 로그인 방법| Microsoft 계정을 공동 관리자 또는 서비스 관리자로 추가하나요? |같은 조직에 있는 조직 계정을 공동 관리자 또는 서비스 관리자로 추가하나요? |다른 조직에 있는 조직 계정을 공동 관리자 또는 서비스 관리자로 추가하나요?
| ------------- | ------------- |---------------|---------------|
|Microsoft 계정 |예|아니요|아니요|
|조직 계정|예|예|아니요|

## 인터넷 브라우저에 문제가 있나요?

IE InPrivate 브라우징 모드를 사용하고 다른 브라우저도 사용하여 캐시/쿠키를 삭제합니다.

<!---HONumber=AcomDC_0706_2016-->