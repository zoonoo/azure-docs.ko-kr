<properties
   pageTitle="논리 앱에서 Office 365 커넥터 사용 | Microsoft Azure 앱 서비스"
   description="Office 365 커넥터 또는 API 앱을 만들어서 구성하고 Azure 앱 서비스의 논리 앱에서 사용하는 방법"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="anuragdalmia"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="03/16/2016"
   ms.author="sameerch"/>


# Office 365 커넥터 시작 및 논리 앱에 추가
>[AZURE.NOTE] 이 문서 버전은 논리 앱 2014-12-01-미리 보기 스키마 버전에 적용됩니다. 2015-08-01-preview 스키마 버전을 보려면 [Office 365 Outlook API](../connectors/connectors-create-api-office365-outlook.md) 또는 [Office 365 사용자 API](../connectors/connectors-create-api-office365-users.md)를 클릭합니다.

Office 365 계정에 연결하여 메일을 보내거나 받고 일정 및 연락처를 관리합니다. 메일 보내기, 받기 및 가져오기, 일정에서 이벤트 만들기 및 삭제, 연락처 만들기, 업데이트, 가져오기 및 삭제 등 여러 가지 동작을 수행할 있습니다.

논리 앱은 다양한 데이터 원본을 기반으로 트리거하고 흐름의 일부로 데이터를 가져오고 처리하기 위한 커넥터를 제공할 수 있습니다. Office 365 커넥터를 비즈니스 워크플로에 추가하고 논리 앱 내에서 이 워크플로의 일부로 데이터를 처리할 수 있습니다.

**기본 동작**

- 새 메일(트리거)
- 메일 보내기
- 메일에 회신
- 이벤트 보내기
- 연락처 추가

## O365 커넥터 API 앱 만들기
커넥터는 논리 앱 내에서 만들거나 Azure 마켓플레이스에서 직접 만들 수 있습니다. 마켓플레이스에서 커넥터를 만들려면

1. Azure 시작 보드에서 **마켓플레이스**를 선택합니다.
2. "Office 365 커넥터"를 검색하여 선택하고 **만들기**를 선택합니다.
3.	호스팅 계획, 리소스 그룹에 대한 세부 정보를 제공하고 API 앱의 이름을 선택하여 Office 365 커넥터를 구성합니다. ![][21]


## 논리 앱 만들기
메일이 수신되면(판매 문의 메일 ID - 예: sales@contoso.com) 트리거되는 간단한 논리 앱을 만듭니다. 그런 후에 이 논리 앱은 이벤트를 만들고 보낸 사람의 세부 정보가 포함된 연락처를 추가하며 개인 계정으로 메일을 보내고 마지막으로 승인이 포함된 회신을 보냅니다.

1.	Azure 포털에 로그인하고 '새 파일 ->웹 + 모바일 -> 논리 앱'을 클릭합니다. ![][1]

2.	'논리 앱 만들기' 페이지에서 이름, 앱 서비스 계획 및 위치 등의 필요한 세부 정보를 입력합니다. ![][2]

3.	'트리거 및 동작'을 클릭하면 논리 앱 편집기가 열립니다. ![][3]

4.	흐름에 추가하기 위해서 갤러리의 '이 리소스 그룹의 API 앱' 섹션에서 Office 365 트리거를 선택합니다. ![][4]

6.	Office 365에 연결하려면 계정에 액세스할 수 있는 권한을 논리 앱에 부여해야 합니다. '권한 부여'를 클릭하여 Office 365 로그인 자격 증명을 제공합니다. ![][5]

7.	Office 365 로그인 페이지로 리디렉션되며 Office 365 계정 자격 증명으로 인증할 수 있습니다. ![][6] ![][7]

8.	권한 부여가 완료되면 Office 365 트리거가 표시됩니다. ![][8]

9.	'New Email(새 메일)' 트리거를 선택하면 입력 매개 변수가 표시됩니다.


10.	'분'에 대한 트리거 빈도를 변경하고 ✓를 클릭합니다. ![][9]

11. Office 365 'New Email(새 메일)' 트리거가 구성되고 출력 매개 변수도 표시되는 것을 확인할 수 있습니다. ![][10]

12.	갤러리의 'Recently Used(최근에 사용한 항목)' 섹션에서 'Office 365 Connector'를 선택하면 새 'Office 365' 동작이 추가됩니다.

13.	동작 목록에서 'Send Event(이벤트 보내기)'를 선택하면 'Send Event(이벤트 보내기)' 동작의 입력 매개 변수가 표시됩니다. ![][11]

14.	이벤트 세부 정보를 지정하고 ✓를 클릭합니다. ![][12]

15.	갤러리의 'Recently Used(최근에 사용한 항목)' 섹션에서 'Office 365 Connector'를 선택하면 새 'Office 365' 동작이 추가됩니다.

16.	동작 목록에서 'Add Contact(연락처 추가)'를 선택하면 'Add Contact(연락처 추가)' 동작의 입력 매개 변수가 표시됩니다. ![][13]

17.	'Email Address(메일 주소)' 필드 옆의 '+'를 클릭하고 트리거에서 'From(보낸 사람)' 출력 필드 값을 선택합니다. ![][14]

18. ✓를 클릭하면 동작 구성이 완료됩니다. ![][15]

19.	갤러리의 'Recently Used(최근에 사용한 항목)' 섹션에서 'Office 365 Connector'를 선택하면 새 'Office 365' 동작이 추가됩니다.


20.	동작 목록에서 'Send Email(메일 보내기)'을 선택하면 'Send Email(메일 보내기)' 동작의 입력 매개 변수가 표시됩니다. ![][19]

21.	메일을 보내는 데 필요한 세부 정보를 입력합니다. 아래와 같은 내용을 입력하여 메시지를 작성할 수 있습니다. ‘메일 보내기' 동작이 구성되면 ✓를 클릭합니다.

		Body - @concat('You got a new sales enquiry from',triggers().output.body.From)

	![][20]
22.	갤러리의 'Recently Used(최근에 사용한 항목)' 섹션에서 'Office 365 Connector'를 선택하면 새 'Office 365' 동작이 추가됩니다.


23.	동작 목록에서 'Reply To(회신)'를 선택하면 'Reply To(회신)' 동작의 입력 매개 변수가 표시됩니다. ![][16]

24.	'From(보낸 사람)' 필드 옆의 '+'를 클릭하고 트리거의 출력 메시지 ID에서 값을 선택한 후 ✓를 클릭합니다. ![][17]

25. 논리 앱 편집기 화면에서 확인을 클릭한 다음 '만들기'를 클릭합니다. 만들기를 완료하는 데 30초 정도 걸립니다.

26. 트리거를 사용하여 구성한 계정에 메일을 보내면 Office 365 메일 계정에 개인 메일 계정의 메일, 일정 이벤트 및 연락처가 표시되어야 합니다. 또한 판매 문의에 곧 응답하겠다고 알리는 회신이 수신되어야 합니다.

## 커넥터의 추가 기능
이제 커넥터를 만들었으므로 논리 앱을 사용하여 비즈니스 워크플로에 추가할 수 있습니다. [논리 앱 정의](app-service-logic-what-are-logic-apps.md)를 참조하세요.

> [AZURE.NOTE] Azure 계정을 등록하기 전에 Azure 논리 앱을 시작하려는 경우 [논리 앱 평가](https://tryappservice.azure.com/?appservice=logic)로 이동합니다. 앱 서비스에서 즉시 단기 스타터 논리 앱을 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.

[커넥터 및 API 앱 참조](http://go.microsoft.com/fwlink/p/?LinkId=529766)의 Swagger REST API 참조를 봅니다.

커넥터의 성능 통계를 검토하고 보안을 제어할 수 있습니다. [기본 제공 API 앱 및 커넥터 관리 및 모니터링](app-service-logic-monitor-your-connectors.md)을 참조하세요.

<!--Image references-->
[1]: ./media/app-service-logic-connector-office365/1_New_Logic_App.png
[2]: ./media/app-service-logic-connector-office365/2_Logic_App_Settings.png
[3]: ./media/app-service-logic-connector-office365/3_Logic_App_Editor.png
[4]: ./media/app-service-logic-connector-office365/4_Select_Office365_Gallery.png
[5]: ./media/app-service-logic-connector-office365/5_Office365_Authorize.png
[6]: ./media/app-service-logic-connector-office365/6_Office365_Login.png
[7]: ./media/app-service-logic-connector-office365/7_Office365_User_Consent.png
[8]: ./media/app-service-logic-connector-office365/8_Office365_Trigger.png
[9]: ./media/app-service-logic-connector-office365/9_Office365_Trigger_Settings.png
[10]: ./media/app-service-logic-connector-office365/10_Office365_Trigger_Configured.png
[11]: ./media/app-service-logic-connector-office365/11_Office365_Actions_List.png
[12]: ./media/app-service-logic-connector-office365/12_Office365_Create_Event_Inputs.png
[13]: ./media/app-service-logic-connector-office365/13_Office365_Add_Contact_Inputs.png
[14]: ./media/app-service-logic-connector-office365/14_Office365_Add_Contact_Email_FromTrigger.png
[15]: ./media/app-service-logic-connector-office365/15_Office365_Add_Contacts_Configured.png
[16]: ./media/app-service-logic-connector-office365/16_Office365_Reply_To_Inputs.png
[17]: ./media/app-service-logic-connector-office365/17_Office365_Reply_To_MessageId.png
[18]: ./media/app-service-logic-connector-office365/18_Office365_Reply_To_Configured.png
[19]: ./media/app-service-logic-connector-office365/19_Office365_Send_Inputs.png
[20]: ./media/app-service-logic-connector-office365/20_Office365_Send_Configured.png
[21]: ./media/app-service-logic-connector-office365/21-create-new-o365-api-app.png

<!---HONumber=AcomDC_0803_2016-->