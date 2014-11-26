<properties pageTitle="How manage developer accounts in Azure API Management" metaKeywords="" description="Learn how to create or invite developers in Azure API Management" metaCanonical="" services="" documentationCenter="API Management" title="How manage developer accounts in Azure API Management" authors="sdanie" solutions="" manager="" editor="" />

<tags ms.service="api-management" ms.workload="mobile" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="sdanie" />

# Azure API 관리에서 개발자 계정을 관리하는 방법

API 관리(미리 보기)에서, 개발자는 API 관리 사용을 공개하는 API의 사용자입니다. 이 가이드에서는 개발자를 만들고, 개발자가 API 관리 인스턴스로 사용 가능하도록 설정된 API 및 제품을 사용할 수 있도록 초대하는 방법을 보여 줍니다.

## 항목 내용

- [새 개발자 만들기][새 개발자 만들기]
- [개발자 초대][개발자 초대]
- [개발자 계정 비활성화 또는 다시 활성화][개발자 계정 비활성화 또는 다시 활성화]
- [다음 단계][다음 단계]

## <a name="create-developer"></a> 새 개발자 만들기

새 개발자를 만들려면 Azure 포털에서 API 관리 서비스에 대한 **관리 콘솔**을 클릭합니다. API 관리의 관리용 포털이 열립니다.

>아직 API 관리 서비스 인스턴스를 만들지 않은 경우 [Azure API 관리 시작][Azure API 관리 시작] 자습서의 [API 관리 서비스 인스턴스 만들기][API 관리 서비스 인스턴스 만들기]를 참조하세요.

![API 관리 콘솔][API 관리 콘솔]

왼쪽의 **API 관리** 메뉴에서 **개발자**를 클릭한 다음 **사용자 추가**를 클릭합니다.

![개발자 만들기][개발자 만들기]

새 개발자에 대한 **전자 메일**, **암호** 및 **이름**을 입력하고 **저장**을 클릭합니다.

![개발자 만들기][1]

기본적으로 새로 만든 개발자 계정은 **활성**이며, **개발자** 그룹과 연결됩니다.

![새 개발자][새 개발자]

**활성** 상태의 개발자 계정은 구독하는 모든 API에 액세스하는 데 사용할 수 있습니다. 새로 만든 개발자와 추가 그룹을 연결하려면 [그룹과 개발자를 연결하는 방법][그룹과 개발자를 연결하는 방법]을 참조하세요.

## <a name="invite-developer"></a> 개발자 초대

개발자를 초대하려면 왼쪽의 **API 관리** 메뉴에서 **개발자**를 클릭한 다음 **사용자 초대**를 클릭합니다.

![개발자 초대][2]

개발자의 이름 및 전자 메일 주소를 입력하고 **초대**를 클릭합니다.

![개발자 초대][3]

확인 메시지가 표시되지만 새로 초대한 개발자는 해당 개발자가 초대를 허용할 때까지 목록에 나타나지 않습니다.

![초대 확인][초대 확인]

>개발자를 초대하면 전자 메일이 개발자에게 전송됩니다. 이 전자 메일은 템플릿을 사용하여 생성되며 사용자 지정 가능합니다. 자세한 내용은 [전자 메일 템플릿 구성][전자 메일 템플릿 구성]을 참조하세요.

초대가 허용되면 계정이 활성 상태가 됩니다.

## <a name="block-developer"></a> 개발자 계정 비활성화 또는 다시 활성화

기본적으로, 새로 만들거나 초대한 개발자 계정은 **활성** 상태가 됩니다. 개발자 계정을 비활성화하려면 **차단**을 클릭합니다. 차단된 개발자 계정을 다시 활성화하려면 **활성화**를 클릭합니다. 차단된 개발자 계정에서는 개발자 포털에 액세스하거나 어떤 API도 호출할 수 없습니다.

![개발자 차단][새 개발자]

## <a name="next-steps"></a> 다음 단계

개발자 계정이 생성된 후에는 계정과 역할을 연결하고 해당 계정으로 제품 및 API를 구독할 수 있습니다. 자세한 내용은 [그룹을 만들고 사용하는 방법][그룹을 만들고 사용하는 방법](영문)을 참조하세요.


[새 개발자 만들기]: #create-developer
[개발자 초대]: #invite-developer
[개발자 계정 비활성화 또는 다시 활성화]: #block-developer
[다음 단계]: #next-steps
[Azure API 관리 시작]: ../api-management-get-started
[API 관리 서비스 인스턴스 만들기]: ../api-management-get-started/#create-service-instance
[API 관리 콘솔]: ./media/api-management-howto-create-or-invite-developers/api-management-management-console.png
[개발자 만들기]: ./media/api-management-howto-create-or-invite-developers/api-management-create-developer.png
[1]: ./media/api-management-howto-create-or-invite-developers/api-management-add-new-user.png
[새 개발자]: ./media/api-management-howto-create-or-invite-developers/api-management-new-developer.png
[그룹과 개발자를 연결하는 방법]: ../api-management-howto-create-groups/#associate-group-developer
[2]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer.png
[3]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer-window.png
[초대 확인]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer-confirmation.png
[전자 메일 템플릿 구성]: ../api-management-howto-configure-notifications/#email-templates
[그룹을 만들고 사용하는 방법]: ../api-management-howto-create-groups
