<properties 
	pageTitle="Azure API 관리에서 알림 및 메일 템플릿을 구성하는 방법" 
	description="Azure API 관리에서 알림 및 메일 템플릿을 구성하는 방법에 대해 알아봅니다." 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/16/2015" 
	ms.author="sdanie"/>

# Azure API 관리에서 알림 및 메일 템플릿을 구성하는 방법

API 관리는 특정 이벤트에 대한 알림을 구성하는 기능과 API 관리 인스턴스의 관리자 및 개발자와 의사를 전달하는 데 사용되는 메일 템플릿을 구성하는 기능을 제공합니다. 이 항목에서는 사용 가능한 이벤트에 대한 알림을 구성하는 방법을 보여 주고 이러한 이벤트에 사용된 메일 템플릿 구성에 대한 개요를 제공합니다.

## <a name="publisher-notifications"> </a>게시자 알림 구성

알림을 구성하려면 Azure 포털에서 API 관리 서비스에 대해 **관리**를 클릭합니다. API 관리 게시자 포털로 이동됩니다.

![게시자 포털][api-management-management-console]

>아직 API 관리 서비스 인스턴스를 만들지 않은 경우 [Azure API 관리 시작][] 자습서의 [API 관리 서비스 인스턴스 만들기][]를 참조하세요.

왼쪽의 **API 관리** 메뉴에서 **알림**을 클릭하여 사용 가능한 알림을 봅니다.

![게시자 알림][api-management-publisher-notifications]

알림에 대해 다음 이벤트 목록을 구성할 수 있습니다.

-	**구독 요청(승인 필요)** - 지정된 메일 받는 사람 및 사용자가 승인이 필요한 API 제품의 구독 요청에 대한 메일 알림을 받습니다.
-	**새 구독** - 지정된 메일 받는 사람 및 사용자가 새 API 제품 구독에 대한 메일 알림을 받습니다.
-	**응용 프로그램 갤러리 요청** - 새 응용 프로그램이 응용 프로그램 갤러리에 제출되면 지정된 메일 받는 사람 및 사용자가 메일 알림을 받습니다.
-	**BCC** - 지정된 메일 받는 사람 및 사용자가 개발자에게 전송된 모든 메일의 숨은 참조 복사본을 받습니다.
-	**새 문제 및 설명** - 개발자 포털에서 새 문제 또는 설명이 제출되면 지정된 메일 받는 사람 및 사용자가 메일 알림을 받습니다.
-	**Close account message(계정 종료 메시지)** - 계정이 종료되면 지정된 메일 받는 사람 및 사용자가 메일 알림을 받습니다.
-	**구독 할당량 한도 근접** - 구독 사용량이 사용 할당량에 근접하면 다음 메일 받는 사람 및 사용자가 메일 알림을 받습니다.

각 이벤트에 대해 메일 주소 입력란을 사용하여 메일 받는 사람을 지정하거나 목록에서 사용자를 선택할 수 있습니다.

알릴 메일 주소를 지정하려면 메일 주소 입력란에 입력합니다. 메일 주소를 여러 개 사용하는 경우 쉼표를 사용하여 구분하세요.

![알림 받는 사람][api-management-email-addresses]

알릴 사용자를 지정하려면 **받는 사람 추가**를 클릭하고, 알릴 사용자 옆에 있는 확인란을 선택하고, **확인**을 클릭합니다.

>관리자만 목록에 표시됩니다.

알림 받는 사람을 구성한 후 **저장**을 클릭하여 업데이트된 알림 받는 사람을 적용합니다.

>**게시자 알림** 탭 외부로 이동하면 저장되지 않은 변경 내용이 있는 경우 API 관리 포털에서 사용자에게 알립니다.

## <a name="email-templates"> </a>메일 템플릿 구성

API 관리는 서비스를 관리 및 사용하는 과정에서 전송된 메일 메시지에 대한 메일 템플릿을 제공합니다. 다음 메일 템플릿이 제공됩니다.

-	응용 프로그램 갤러리 제출 승인
-	개발자 인사 편지
-	개발자 할당량 한도 근접 알림
-	사용자 초대
-	문제에 새 설명 추가
-	새 문제 수신
-	새 구독 활성화
-	구독 갱신 확인
-	구독 요청 거부
-	구독 요청 수신

이러한 템플릿은 필요에 따라 수정할 수 있습니다.

API 관리 인스턴스의 메일 템플릿을 보고 구성하려면 왼쪽 **API 관리** 메뉴에서 **알림**을 클릭하고 **메일 템플릿** 탭을 선택합니다.

![메일 템플릿][api-management-email-templates]

특정 템플릿을 보거나 수정하려면 **템플릿** 드롭다운 목록에서 선택합니다.

![메일 템플릿 목록][api-management-email-templates-list]

각 메일 템플릿의 제목은 일반 텍스트이며 본문 정의는 HTML 형식입니다. 각 항목은 필요에 따라 사용자 지정할 수 있습니다.

![메일 템플릿 편집기][api-management-email-template]

**매개 변수** 목록에는 제목 또는 본문에 삽입되는 매개 변수 목록이 포함되며, 이러한 매개 변수는 메일이 전송될 때 지정된 값으로 바뀝니다. 매개 변수를 삽입하려면 매개 변수를 넣을 위치에 커서를 놓고 매개 변수 이름 왼쪽 화살표를 클릭합니다.

**미리 보기** 또는 **테스트 보내기**를 클릭하여 메일의 모양을 보거나 테스트 메일을 보냅니다.

>테스트를 미리 보거나 보낼 때에는 매개 변수가 실제 값으로 바뀌지 않습니다.
>
>메일 템플릿의 변경 내용을 저장하려면 **저장**을 클릭하고, 변경 내용을 취소하려면 **취소**를 클릭합니다.



[api-management-management-console]: ./media/api-management-howto-configure-notifications/api-management-management-console.png
[api-management-publisher-notifications]: ./media/api-management-howto-configure-notifications/api-management-publisher-notifications.png
[api-management-email-addresses]: ./media/api-management-howto-configure-notifications/api-management-email-addresses.png


[api-management-email-templates]: ./media/api-management-howto-configure-notifications/api-management-email-templates.png
[api-management-email-templates-list]: ./media/api-management-howto-configure-notifications/api-management-email-templates-list.png
[api-management-email-template]: ./media/api-management-howto-configure-notifications/api-management-email-template.png


[Configure publisher notifications]: #publisher-notifications
[Configure email templates]: #email-templates

[How to create and use groups]: api-management-howto-create-groups.md
[How to associate groups with developers]: api-management-howto-create-groups.md#associate-group-developer

[API 관리 서비스 인스턴스 만들기]: api-management-get-started.md
[Azure API 관리 시작]: api-management-get-started.md#create-service-instance

<!---HONumber=July15_HO4-->