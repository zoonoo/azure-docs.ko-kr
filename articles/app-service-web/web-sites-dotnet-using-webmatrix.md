<properties 
	pageTitle="Microsoft WebMatrix로 웹 앱 개발 및 배포" 
	description="Microsoft WebMatrix를 사용하여 ASP.NET 웹 응용 프로그램을 개발하고 Azure 앱 서비스 웹 앱에 배포하는 방법에 대해 알아봅니다." 
	services="app-service\web" 
	documentationCenter=".net" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="05/15/2015" 
	ms.author="tomfitz"/>


# Microsoft WebMatrix로 웹 앱 개발 및 배포

## 개요

이 가이드에서는 Microsoft WebMatrix를 사용하여 .NET 웹 사이트를 만들고 [Azure 앱 서비스](http://go.microsoft.com/fwlink/?LinkId=529714) 웹 앱에 배포하는 방법을 설명합니다. WebMatrix 사이트 템플릿에서 샘플 응용 프로그램을 사용합니다.

다음 내용을 배웁니다.

* WebMatrix에서 Azure에 로그인하는 방법
* WebMatrix에서 기본 제공 템플릿을 사용하여 사이트를 만드는 방법 
* WebMatrix에서 Azure로 사용자 지정된 웹 사이트를 직접 배포하는 방법

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE]Azure 계정을 등록하기 전에 Azure 앱 서비스를 시작하려면 [앱 서비스 평가](http://go.microsoft.com/fwlink/?LinkId=523751)로 이동합니다. 앱 서비스에서 단기 스타터 웹 앱을 즉시 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.

## Azure에 로그인

1. WebMatrix를 시작합니다.
2. 이번에 처음으로 WebMatrix 3을 사용하는 경우 Azure에 로그인하라는 메시지가 표시됩니다. 또는 **로그인** 단추를 클릭하고 **계정 추가**를 선택할 수 있습니다. Microsoft 계정을 사용하여 **로그인**하도록 선택합니다.

	![계정 추가][addaccount]

3. Azure 계정을 등록한 경우 Microsoft 계정을 사용하여 로그인할 수 있습니다.

	![Azure에 로그인][signin]


## Azure용 기본 제공 템플릿을 사용하여 사이트 만들기

1. 시작 화면에서 **새로 만들기** 단추를 클릭하고 **템플릿 갤러리**를 선택하여 템플릿 갤러리에서 새 사이트를 만듭니다.

	![템플릿 갤러리에서 새 사이트 만들기][sitefromtemplate]

2. 템플릿 갤러리는 로컬로 또는 Azure에서 실행할 수 있는 사용 가능한 템플릿 목록을 보여 줍니다. 템플릿 목록에서 **제과**를 선택하고 **사이트 이름**으로 **bakerysample**을 입력하고 **다음**을 클릭합니다.

	![템플릿에서 사이트 만들기][sitefromtemplatedetails]

3. Azure에 로그인한 경우 로컬 사이트에 대한 Azure 앱 서비스 웹 앱을 만들 수 있는 옵션이 제공됩니다. 고유한 이름을 선택하고 웹 앱 인스턴스를 만들 데이터 센터를 선택합니다.

	![Azure에서 사이트 만들기][sitefromtemplateazure]

	WebMatrix에서 로컬 사이트 만들고 Azure에서 웹 앱 인스턴스를 만들고 나면 WebMatrix IDE가 표시됩니다.

	![WebMatrix IDE][howtowebmatrixide]

## 메일 설정

제과 샘플에는 주문된 항목이 포함된 메일 메시지를 보내는 시뮬레이트된 주문 양식이 포함되어 있습니다. Azure의 SendGrid 메일 서비스를 사용하여 사이트에서 메일을 보냅니다.

1. [Azure에서 SendGrid 계정을 사용하여 메일을 보내는 방법][sendgridexample] 자습서의 단계에 따라 SendGrid 계정을 설정하고 연결 정보를 검색합니다. 전체 자습서를 수행할 필요는 없으며 연결 정보를 가져오는 부분까지만 수행하면 됩니다.

2. WebMatrix 프로젝트에 SendGrid NuGet 패키지를 추가합니다. 먼저 NuGet 단추를 클릭합니다.

    ![SendGrid 추가][addsendgrid]

    SendGrid를 검색하고 설치합니다.

    ![SendGrid 설치][installsendgrid]

    패키지 설치를 마치면 SendGrid 어셈블리가 bin에 추가되었는지 확인합니다.

    ![추가된 SendGrid][binsendgrid]

3. 파일 이름을 두 번 클릭하여 *Order.cshtml* 페이지를 엽니다.

	![][modify2]

4. 파일 맨 위에 다음 코드를 추가합니다.

        @using SendGrid;
        @using System.Net.Mail;

4. //SMTP Configuration for Hotmail이 표시된 주석을 찾고 WebMail을 사용하기 위해 모든 코드를 삭제하거나 주석 처리합니다.

        /*
        //SMTP Configuration for Hotmail
        WebMail.SmtpServer = "smtp.live.com";
        WebMail.SmtpPort = 25;
        WebMail.EnableSsl = true;

        //Enter your Hotmail credentials for UserName/Password and a "From" address for the e-mail
        WebMail.UserName = "";
        WebMail.Password = "";
        WebMail.From = "";

        if (WebMail.UserName.IsEmpty() || WebMail.Password.IsEmpty() || WebMail.From.IsEmpty()) {
            Response.Redirect("~/OrderSuccess?NoEmail=1");
        } 
        else {
            try {
                WebMail.Send(to: customerEmail, subject: "Fourth Coffee - New Order", body: body);
                Response.Redirect("~/OrderSuccess");
            } catch {
                ModelState.AddFormError("There was an error and your order could not be processed at this time");
            }
        }*/


5. WebMail 대신 SendGrid를 사용하여 메일을 보내는 코드를 추가합니다. 이전 단계에서 삭제한 코드 대신 다음 코드를 추가합니다. NetworkCredential를 만들 때 SendGrid 사용자 이름 및 암호를 추가해야 합니다.

		 if (email.IsEmpty()) {
            Response.Redirect("~/OrderSuccess?NoEmail=1");
        }
        else {
            // Create the email object first, then add the properties.
            SendGridMessage myMessage = new SendGridMessage();
            myMessage.AddTo(email);
            myMessage.From = new MailAddress("FourthCoffee@example.com", "Fourth Coffee");
            myMessage.Subject = "Fourth Coffee - New Order";
            myMessage.Text = body;

            // Create credentials, specifying your user name and password.
            var credentials = new NetworkCredential("[your user name]", "[your password]");

            // Create an Web transport for sending email.
            var transportWeb = new Web(credentials);

            // Send the email.
            try {
                transportWeb.DeliverAsync(myMessage);
                Response.Redirect("~/OrderSuccess");
            } catch {
                ModelState.AddFormError("There was an error and your order could not be processed at this time");
            }
        }


6. WebMatrix 리본에서 **실행**을 클릭하여 사이트를 테스트합니다.

	![][modify4]

7. 제품 중 하나에 대해 **지금 주문**을 클릭하고 주문을 자신에게 보냅니다.

8. 전자 메일을 확인해서 주문 확인을 받았는지 확인합니다. 전자 메일을 보내는 데 문제가 있는 경우 ASP.NET 웹 페이지(Razor) 가이드의 [전자 메일 보내기 관련 문제][sendmailissues](영문)를 참조하십시오.
 

## WebMatrix에서 Azure로 사용자 지정된 웹 사이트 배포

1. WebMatrix의 **홈** 리본에서 **게시**를 클릭하여 웹 사이트의 **게시 미리 보기** 대화 상자를 표시합니다.

	![WebMatrix 게시 미리 보기][howtopublishpreview]

2. bakery.sdf 옆에 있는 확인란을 클릭하여 선택한 후 **계속**을 클릭합니다. 게시가 완료되면 Azure 앱 서비스의 업데이트된 웹 앱의 URL이 WebMatrix IDE 아래쪽에 표시됩니다.

	![게시 완료][publishcomplete]

3. 링크를 클릭하여 브라우저에서 웹 사이트(Azure에서 웹 앱 인스턴스)를 엽니다.

	![제과 샘플 사이트][bakerysample]

	**찾아보기** > **웹 앱**을 클릭하여 구독을 위한 모든 웹 앱 인스턴스를 표시한 다음 웹 앱을 선택하여 웹 앱 인스턴스에 대한 URL을 [Azure 포털](https://portal.azure.com)에서도 찾을 수 있습니다. 웹 앱의 URL은 웹 앱의 블레이드를 표시합니다.

## 웹 사이트를 수정하고 웹 앱에 다시 게시

WebMatrix를 사용하여 사이트를 수정하고 웹 앱 인스턴스에 다시 게시할 수 있습니다. 다음 절차에서는 주문이 선물임을 나타내는 확인란을 추가합니다.

1. *Order.cshtml* 페이지를 엽니다.

2. "shiping" 클래스 양식 정의를 찾습니다. &lt;li&gt; 블록 바로 뒤에 다음 코드를 삽입합니다.
		
		<li class="gift">
		    <div class="fieldcontainer" data-role="fieldcontain">
		        <label for="isGift">This is a gift</label>           
		        <div>@Html.CheckBox("isGift")</div>
		    </div>
		</li>

	![][modify5]

3. 파일에서 "var shipping = Request["orderShipping"];" 줄을 찾아서 바로 뒤에 다음 코드 줄을 삽입합니다.

		var gift = Request["isGift"];

4. 다음 코드 조각에서 배송 주소가 비어 있지 않은지 유효성을 검사하는 코드 블록 바로 뒤에 다음을 삽입합니다.

		if(gift != null) {
			body += "This is a gift." + "<br/>";
		}

	*order.cshtml* 파일은 다음 이미지와 유사하게 나타납니다.

	![][modify6]

5. 파일을 저장하고 사이트를 로컬로 실행하고 테스트 주문을 자신에게 보냅니다. 새 확인란을 테스트했는지 확인하십시오.

	![][modify7]

6. **홈** 리본에서 **게시**를 클릭하여 사이트를 다시 게시합니다.

7. **게시 미리 보기** 대화 상자에서 Order.cshtml을 선택했는지 확인하고 계속을 클릭합니다.

8. 링크를 클릭하여 브라우저에서 웹 사이트를 열고 웹 앱 인스턴스에서 업데이트를 테스트합니다.

## 다음 단계

* [WebMatrix 웹 사이트](http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200106398)

## 변경된 내용
* 웹 사이트에서 앱 서비스로의 변경에 대한 지침은 [Azure 앱 서비스와 이 서비스가 기존 Azure 서비스에 미치는 영향](http://go.microsoft.com/fwlink/?LinkId=529714)을 참조하세요.
* 이전 포털에서 새 포털로의 변경에 대한 지침은 [미리 보기 포털 탐색에 대한 참조](http://go.microsoft.com/fwlink/?LinkId=529715)를 참조하세요.




[howtowebmatrixide]: ./media/web-sites-dotnet-using-webmatrix/howtowebmatrixide2.png
[howtopublishpreview]: ./media/web-sites-dotnet-using-webmatrix/howtopublishpreview.png
[addsendgrid]: ./media/web-sites-dotnet-using-webmatrix/addsendgridpackage.png
[installsendgrid]: ./media/web-sites-dotnet-using-webmatrix/installsendgrid.png
[binsendgrid]: ./media/web-sites-dotnet-using-webmatrix/sendgridbin.png

[publishcomplete]: ./media/web-sites-dotnet-using-webmatrix/howtopublished2.png
[bakerysample]: ./media/web-sites-dotnet-using-webmatrix/howtobakerysamplesite.png
[addaccount]: ./media/web-sites-dotnet-using-webmatrix/webmatrix-add-account.png
[signin]: ./media/web-sites-dotnet-using-webmatrix/webmatrix-sign-in.png
[sitefromtemplate]: ./media/web-sites-dotnet-using-webmatrix/webmatrix-site-from-template.png
[sitefromtemplatedetails]: ./media/web-sites-dotnet-using-webmatrix/webmatrix-site-from-template-details.png
[sitefromtemplateazure]: ./media/web-sites-dotnet-using-webmatrix/webmatrix-site-from-template-azure.png

[modify1]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-1.png
[modify2]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-2.png
[modify3]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-3.png
[modify4]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-4.png
[modify5]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-5.png
[modify6]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-6.png
[modify7]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-7.png



[sendmailissues]: http://go.microsoft.com/fwlink/?LinkId=253001#email
[sendgridexample]: http://azure.microsoft.com/documentation/articles/sendgrid-dotnet-how-to-send-email/
 

<!---HONumber=July15_HO4-->