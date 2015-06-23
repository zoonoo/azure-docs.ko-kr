<properties 
	pageTitle="SendGrid를 사용하여 메일 보내기 - Azure 모바일 서비스" 
	description="SendGrid 서비스를 사용하여 Azure 모바일 서비스 앱에서 전자 메일을 보내는 방법에 대해 알아봅니다." 
	services="mobile-services" 
	documentationCenter="" 
	authors="Erikre" 
	manager="sendgrid" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="04/24/2015" 
	ms.author="Erikre"/>


# SendGrid로 모바일 서비스에서 전자 메일 보내기

이 항목에서는 모바일 서비스에 전자 메일 기능을 어떻게 추가할 수 있는지 보여 줍니다. 이 자습서에서는 SendGrid를 사용해 전자 메일을 보내기 위해 서버 쪽 스크립트를 추가합니다. 이 작업을 완료하면 레코드가 삽입될 때마다 모바일 서비스에서 전자 메일을 전송합니다.

SendGrid는 사용자 지정 통합을 쉽게 만드는 유연한 API와 함께 신뢰할 만한 [트랜잭션 전자 메일 배달], 확장성 및 실시간 분석을 제공하는 [클라우드 기반 전자 메일 서비스]입니다. 자세한 내용은 <http://sendgrid.com>을 참조하세요.

이 자습서에서는 전자 메일 기능을 사용하도록 설정하는 다음 기본 단계를 단계별로 안내합니다.

1. [SendGrid 계정 만들기]
2. [메일을 보내기 위한 스크립트 추가]
3. [메일을 받기 위한 데이터 삽입]

이 자습서는 모바일 서비스 quickstart를 기반으로 합니다. 이 자습서를 시작하기 전에 먼저 [모바일 서비스 시작하기]를 완료해야 합니다.

## <a name="sign-up"></a>새 SendGrid 계정 만들기

[AZURE.INCLUDE [sendgrid-sign-up](../../includes/sendgrid-sign-up.md)]

## <a name="add-script"></a>메일을 보내는 새 스크립트 등록

1. [Azure 관리 포털]에 로그온하여 **모바일 서비스**를 클릭한 후 해당 모바일 서비스를 클릭합니다.

2. 관리 포털에서 **데이터** 탭을 클릭한 후 **TodoItem** 테이블을 클릭합니다.

	![][1]

3. **TodoItem**에서 **스크립트** 탭을 클릭하고 **삽입**을 선택합니다.
   
	![][2]

	**TodoItem** 테이블에 삽입 시 호출되는 함수가 표시됩니다.

4. insert 함수를 다음 코드로 바꿉니다.

        var SendGrid = require('sendgrid').SendGrid;
        
        function insert(item, user, request) {    
            request.execute({
                success: function() {
                    // After the record has been inserted, send the response immediately to the client
                    request.respond();
                    // Send the email in the background
                    sendEmail(item);
                }
            });

            function sendEmail(item) {
                var sendgrid = new SendGrid('**username**', '**password**');       
                
                sendgrid.send({
                    to: '**email-address**',
                    from: '**from-address**',
                    subject: 'New to-do item',
                    text: 'A new to-do was added: ' + item.text
                }, function(success, message) {
                    // If the email failed to send, log it as an error so we can investigate
                    if (!success) {
                        console.error(message);
                    }
                });
            }
        }

5. 위 스크립트에서 자리 표시자를 올바른 값, 즉

	- **_username_ 및 _password_**: [SendGrid 계정 만들기]에서 식별한 SendGrid 자격 증명입니다.

	- **_email-address_**: 메일을 받을 주소입니다. 실제 앱에서는 테이블을 사용하여 전자 메일 주소를 저장하고 검색합니다. 앱을 테스트할 경우, 자신의 전자 메일 주소를 사용하십시오.

	- **_from-address_**: 메일을 보내는 주소입니다. 해당 조직에 속하는 등록된 도메인 주소를 사용하십시오.

     >[AZURE.NOTE]등록된 도메인이 없는 경우, 모바일 서비스의 도메인을 *notifications@_your-mobile-service_.azure-mobile.net* 형식으로 대신 사용할 수 있습니다. 하지만 모바일 서비스 도메인으로 전송되는 메시지는 무시됩니다.

6. **저장** 단추를 클릭합니다. 이제 레코드가 **TodoItem** 테이블에 삽입될 때마다 전자 메일을 보내는 스크립트가 구성되었습니다.

## <a name="insert-data"></a>메일을 받기 위해 테스트 데이터 삽입

1. 클라이언트 앱 프로젝트에서 빠른 시작 응용 프로그램을 실행합니다. 

	이 항목에서는 빠른 시작의 Windows 스토어 버전을 보여 줍니다.

2. 앱에서 **Insert a TodoItem**에 텍스트를 입력하고 **Save**를 클릭합니다.

	![][3]

3. 아래의 알림에 표시된 것과 같은 전자 메일을 받게 됩니다.

	![][4]

	축하합니다. SendGrid를 사용하여 전자 메일을 보내도록 모바일 서비스가 성공적으로 구성되었습니다.

## <a name="nextsteps"> </a>다음 단계

SendGrid 전자 메일 서비스를 모바일 서비스와 함께 쉽게 사용할 수 있다는 것을 확인했습니다. SendGrid에 대해 자세히 알아보려면 다음 링크를 참조하십시오.

-   SendGrid API 설명서: <https://sendgrid.com/docs>
-   Azure 고객을 위한 SendGrid 특가 제공: <https://sendgrid.com/windowsazure.html>

<!-- Anchors. -->
[SendGrid 계정 만들기]: #sign-up
[메일을 보내기 위한 스크립트 추가]: #add-script
[메일을 받기 위한 데이터 삽입]: #insert-data

<!-- Images. -->
[1]: ./media/store-sendgrid-mobile-services-send-email-scripts/mobile-portal-data-tables.png
[2]: ./media/store-sendgrid-mobile-services-send-email-scripts/mobile-insert-script-push2.png
[3]: ./media/store-sendgrid-mobile-services-send-email-scripts/mobile-quickstart-push1.png
[4]: ./media/store-sendgrid-mobile-services-send-email-scripts/mobile-receive-email.png

<!-- URLs. -->
[모바일 서비스 시작하기]: /develop/mobile/tutorials/get-started
[sign up page]: https://sendgrid.com/windowsazure.html
[Multiple User Credentials page]: https://sendgrid.com/credentials
[Azure 관리 포털]: https://manage.windowsazure.com/
[트랜잭션 전자 메일 배달]: https://sendgrid.com/email-solutions
[클라우드 기반 전자 메일 서비스]: https://sendgrid.com/transactional-email


<!--HONumber=54--> 