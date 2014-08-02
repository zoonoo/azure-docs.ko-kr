<properties linkid="dev-net-how-to-sendgrid-email-service" urlDisplayName="SendGrid Email Service" pageTitle="How to use the SendGrid email service (.NET) - Azure" metaKeywords="Azure SendGrid, Azure email service, Azure SendGrid .NET, Azure email .NET, Azure SendGrid C#, Azure email C#" description="Learn how send email with the SendGrid email service on Azure. Code samples written in C# and use the .NET API." metaCanonical="" services="" documentationCenter=".NET" title="How to Send Email Using SendGrid with Azure" authors="" solutions="" manager="" editor="" />

Azure에서 SendGrid를 사용하여 전자 메일을 보내는 방법
=====================================================

이 가이드에서는 Azure에서 SendGrid 전자 메일 서비스로 일반 프로그래밍 작업을 수행하는 방법을 보여 줍니다. 샘플은 C\#으로 작성되었으며 .NET API를 사용합니다. **전자 메일 작성**, **전자 메일 발송**, **첨부 파일 추가**, **필터 사용** 등의 시나리오를 다룹니다. SendGrid 및 전자 메일 보내기에 대한 자세한 내용은 [다음 단계](#nextsteps) 섹션을 참조하십시오.

목차
----

[SendGrid 전자 메일 서비스 정의](#whatis)
 [SendGrid 계정 만들기](#createaccount)
 [SendGrid .NET 클래스 라이브러리 참조](#reference)
 [방법: 전자 메일 만들기](#createemail)
 [방법: 전자 메일 보내기](#sendemail)
 [방법: 첨부 파일 추가](#addattachment)
 [방법: 필터를 사용하여 바닥글, 추적 및 분석을 사용하도록 설정](#usefilters)
 [방법: 추가 SendGrid 서비스 사용](#useservices)
 [다음 단계](#nextsteps)

SendGrid 전자 메일 서비스 정의SendGrid SendGrid 전자 메일 서비스 정의
---------------------------------------------------------------------

SendGrid는 사용자 지정 통합을 쉽게 만드는 유연한 API와 함께 신뢰할 만한 [트랜잭션 전자 메일 배달](http://sendgrid.com/transactional-email), 확장성 및 실시간 분석을 제공하는 [클라우드 기반 전자 메일 서비스](http://sendgrid.com/solutions)입니다. 일반적인 SendGrid 사용 시나리오는 다음과 같습니다.

-   고객에게 확인 메일 자동 발송
-   고객에게 매달 전자 전단지 및 특가 제공 메일을 보내기 위한 메일 그룹 관리
-   차단된 전자 메일과 같은 작업에 대한 실시간 메트릭 및 고객 응답 수집
-   동향을 파악하는 데 도움이 되는 보고서 작성
-   고객 질문 전달

자세한 내용은 <http://sendgrid.com>(영문)을 참조하십시오.

SendGrid 계정 만들기SendGrid 계정 만들기
----------------------------------------

[WACOM.INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

SendGrid .NET 클래스 라이브러리 참조SendGrid .NET 클래스 라이브러리 참조
------------------------------------------------------------------------

SendGrid NuGet 패키지는 SendGrid API를 가져오고 응용 프로그램에서 종속성을 모두 갖도록 구성하는 가장 쉬운 방법입니다. NuGet은 Microsoft Visual Studio 2012에 포함된 Visual Studio 확장 프로그램으로서 라이브러리 및 도구를 쉽게 설치하고 업데이트할 수 있습니다.
**참고**

Visual Studio 2012보다 이전 버전의 Visual Studio를 실행 중인 경우 NuGet을 설치하려면 <http://www.nuget.org>로 이동하여 **Install NuGet** 단추를 클릭합니다.

응용 프로그램에서 SendGrid NuGet 패키지를 설치하려면 다음을 수행합니다.

1.  **솔루션 탐색기**에서 **참조**를 마우스 오른쪽 단추로 클릭한 후 **NuGet 패키지 관리**를 클릭합니다.

2.  **NuGet 패키지 관리** 대화 상자의 왼쪽 창에서 **온라인**을 클릭합니다.

3.  **SendGrid**를 검색하고 결과 목록에서 **SendGrid** 항목을 선택합니다.

    ![SendGrid NuGet 패키지](./media/sendgrid-dotnet-how-to-send-email/sendgrid01.png)

4.  **설치**를 클릭하여 설치를 완료한 후 이 대화 상자를 닫습니다.

SendGrid의 .NET 클래스 라이브러리는 **SendGridMail**이라고 합니다. 다음 네임스페이스가 포함되어 있습니다.

-   **SendGridMail** - 전자 메일 항목 작성 및 작업
-   **SendGridMail.Transport** - **SMTP** 프로토콜과 **Web/REST**를 사용한 HTTP 1.1 프로토콜 중 하나를 사용하여 전자 메일 발송

프로그래밍 방식으로 SendGrid 전자 메일 서비스에 액세스하려는 C\# 파일의 맨 위에 다음과 같은 코드 네임스페이스 선언을 추가합니다. **System.Net**과 **System.Net.Mail**은 포함되는 .NET Framework 네임스페이스이며, 형식을 포함하므로 일반적으로 SendGrid API와 함께 사용됩니다.

    using System.Net;
    using System.Net.Mail;
    using SendGridMail;
    using SendGridMail.Transport;

방법: 전자 메일 만들기방법: 전자 메일 만들기
--------------------------------------------

정적 **SendGrid.GetInstance** 메서드를 사용하여 형식이 **SendGrid**인 전자 메일 메시지를 만듭니다. 일단 메시지가 만들어지면 **SendGrid** 속성 및 메서드를 사용하여 전자 메일 보낸 사람, 전자 메일 받는 사람, 전자 메일 제목 및 본문을 포함한 값을 설정할 수 있습니다.

다음 예에서는 완전히 채워진 전자 메일 개체를 만드는 방법을 보여 줍니다.

    // Setup the email properties.
    var from = new MailAddress("john@contoso.com");
    var to   = new MailAddress[] { new MailAddress("jeff@contoso.com") };
    var cc   = new MailAddress[] { new MailAddress("anna@contoso.com") };
    var bcc  = new MailAddress[] { new MailAddress("peter@contoso.com") };
    var subject = "Testing the SendGrid Library";
    var html    = "<p>Hello World!</p>";
    var text = "Hello World plain text!";
    var transport = SendGridMail.TransportType.SMTP;

    // Create an email, passing in the the eight properties as arguments.
    SendGrid myMessage = SendGrid.GetInstance(from, to, cc, bcc, subject, html, text, transport);

다음 예에서는 빈 전자 메일 개체를 만드는 방법을 보여 줍니다.

    // Create the email object first, then add the properties.
    SendGrid myMessage = SendGrid.GetInstance();
     
    // Add the message properties.
    MailAddress sender = new MailAddress(@"John Smith <john@contoso.com>");
    myMessage.From = sender;
     
    // Add multiple addresses to the To field.
    List<String> recipients = new List<String>
    {
        @"Jeff Smith <jeff@contoso.com>",
        @"Anna Lidman <anna@contoso.com>",
        @"Peter Saddow <peter@contoso.com>"
    };
     
    foreach (string recipient in recipients)
    {
        myMessage.AddTo(recipient);
    }
     
    // Add a message body in HTML format.
    myMessage.Html = "<p>Hello World!</p>";

    // Add the subject.
    myMessage.Subject = "Testing the SendGrid Library";

**SendGrid** 형식에서 지원하는 모든 속성 및 메서드에 대한 자세한 내용은 GitHub에서 [sendgrid-csharp](https://github.com/sendgrid/sendgrid-csharp)를 참조하십시오.

방법: 전자 메일 보내기방법: 전자 메일 보내기
--------------------------------------------

전자 메일 메시지를 만든 후에는 SMTP 또는 SendGrid에서 제공하는 Web API를 사용하여 해당 메시지를 보낼 수 있습니다. 각 API의 장단점에 대한 자세한 내용은 SendGrid 설명서에서 [SMTP vs. Web API](http://docs.sendgrid.com/documentation/get-started/integrate/examples/smtp-vs-rest/)를 참조하십시오.

어느 프로토콜로든 전자 메일을 보내려면 SendGrid 계정 자격 증명(사용자 이름 및 암호)을 제공해야 합니다. 다음 코드에서는 **NetworkCredential** 개체에서 자격 증명을 래핑하는 방법을 보여 줍니다.

    // Create network credentials to access your SendGrid account.
    var username = "your_sendgrid_username";
    var pswd = "your_sendgrid_password";

    var credentials = new NetworkCredential(username, pswd);

전자 메일 메시지를 보내려면 SMTP 프로토콜을 사용하는 **SMTP** 클래스나 SendGrid Web API를 호출하는 **REST** 전송 클래스에서 **Deliver** 메서드를 사용합니다. 다음 예에서는 SMTP와 Web API를 사용하여 메시지를 보내는 방법을 보여 줍니다.

### SMTP

    // Create the email object first, then add the properties.
    SendGrid myMessage = SendGrid.GetInstance();
    myMessage.AddTo("anna@contoso.com");
    myMessage.From = new MailAddress("john@contoso.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    // Create credentials, specifying your user name and password.
    var credentials = new NetworkCredential("username", "password");

    // Create an SMTP transport for sending email.
    var transportSMTP = SMTP.GetInstance(credentials);

    // Send the email.
    transportSMTP.Deliver(myMessage);

### Web API

    // Create the email object first, then add the properties.
    SendGrid myMessage = SendGrid.GetInstance();
    myMessage.AddTo("anna@contoso.com");
    myMessage.From = new MailAddress("john@contoso.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    // Create credentials, specifying your user name and password.
    var credentials = new NetworkCredential("username", "password");

    // Create a REST transport for sending email.
    var transportREST = Web.GetInstance(credentials);

    // Send the email.
    transportREST.Deliver(myMessage);

방법: 첨부 파일 추가방법: 첨부 파일 추가
----------------------------------------

**AddAttachment** 메서드를 호출하고 첨부할 파일의 이름과 경로를 지정하여 첨부 파일을 메시지에 추가할 수 있습니다. 첨부할 파일마다 이 메서드를 한 번씩 호출하여 여러 개의 첨부 파일을 포함할 수 있습니다. 다음 예에서는 메시지에 첨부 파일을 추가하는 방법을 보여 줍니다.

    SendGrid myMessage = SendGrid.GetInstance();
    myMessage.AddTo("anna@contoso.com");
    myMessage.From = new MailAddress("john@contoso.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    myMessage.AddAttachment(@"C:\file1.txt");

방법: 필터를 사용하여 바닥글, 추적 및 분석을 사용하도록 설정방법: 필터를 사용하여 바닥글, 추적 및 분석을 사용하도록 설정
------------------------------------------------------------------------------------------------------------------------

SendGrid는 필터 사용을 통해 추가 전자 메일 기능을 제공합니다. 클릭 추적, Google 분석, 구독 추적 등과 같은 특정 기능을 사용할 수 있도록 전자 메일 메시지에 추가할 수 있는 설정입니다. 전체 필터 목록은 [필터 설정](http://docs.sendgrid.com/documentation/api/smtp-api/filter-settings/)(영문)을 참조하십시오.

**SendGrid** 클래스의 일부로 구현되는 메서드를 사용하여 필터를 **SendGrid** 전자 메일 메시지에 적용할 수 있습니다. 우선 **InitalizeFilters** 메서드를 호출하여 사용 가능한 필터 목록을 초기화해야 전자 메일 메시지에서 필터를 사용할 수 있습니다.

다음 예에서는 바닥글 및 클릭 추적 필터를 보여 줍니다.

### 바닥글

    // Create the email object first, then add the properties.
    SendGrid myMessage = SendGrid.GetInstance();
    myMessage.AddTo("anna@contoso.com");
    myMessage.From = new MailAddress("john@contoso.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    myMessage.InitializeFilters();
    // Add a footer to the message.
    myMessage.EnableFooter("PLAIN TEXT FOOTER", "<p><em>HTML FOOTER</em></p>");

### 클릭 추적

    // Create the email object first, then add the properties.
    SendGrid myMessage = SendGrid.GetInstance();
    myMessage.AddTo("anna@contoso.com");
    myMessage.From = new MailAddress("john@contoso.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Html = "<p><a href=\"http://www.windowsazure.com\">Hello World Link!</a></p>";
    myMessage.Text = "Hello World!";

    myMessage.InitializeFilters();
    // true indicates that links in plain text portions of the email 
    // should also be overwritten for link tracking purposes. 
    myMessage.EnableClickTracking(true);


방법: 추가 SendGrid 서비스 사용방법: 추가 SendGrid 서비스 사용
--------------------------------------------------------------

SendGrid는 Azure 응용 프로그램에서 추가 SendGrid 기능을 활용하는 데 사용할 수 있는 웹 기반 API를 제공합니다. 자세한 내용은 [SendGrid API 설명서](http://docs.sendgrid.com/documentation/api/)(영문)를 참조하십시오.

다음 단계다음 단계
------------------

SendGrid 전자 메일 서비스에 관한 기본적인 사항들을 익혔으며 자세한 내용을 보려면 다음 링크를 따라가십시오.

-   SendGrid C\# 라이브러리 리포지토리: [sendgrid-csharp](https://github.com/sendgrid/sendgrid-csharp)
-   SendGrid API 설명서: <http://docs.sendgrid.com/documentation/api/>
-   Azure 고객을 위한 SendGrid 특가 제공: <http://sendgrid.com>

