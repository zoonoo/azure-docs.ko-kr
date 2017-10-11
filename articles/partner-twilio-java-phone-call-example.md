---
title: "Twilio에서 전화를 거는 방법(Java) | Microsoft Docs"
description: "Azure의 Java 응용 프로그램에서 Twilio를 사용하여 웹 페이지에서 전화를 거는 방법에 대해 알아봅니다."
services: 
documentationcenter: java
author: devinrader
manager: twilio
editor: mollybos
ms.assetid: 0381789e-e775-41a0-a784-294275192b1d
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 11/25/2014
ms.author: microsofthelp@twilio.com
ms.openlocfilehash: 04ecb80a2a9e15b549b47138caf71c7e64bda500
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-make-a-phone-call-using-twilio-in-a-java-application-on-azure"></a>Azure의 Java 응용 프로그램에서 Twilio를 사용하여 전화를 거는 방법
다음 예제는 Azure에 호스트된 웹 페이지에서 Twilio를 사용하여 전화를 거는 방법을 보여 줍니다. 다음 스크린샷에 표시된 것처럼 응용 프로그램에서 사용자에게 전화 통화 값을 묻습니다.

![Twilio 및 Java를 사용하는 Azure 통화 양식][twilio_java]

이 항목에서 코드를 사용하려면 다음을 수행해야 합니다.

1. Twilio 계정 및 인증 토큰을 받습니다. Twilio를 시작하려면 [http://www.twilio.com/pricing][twilio_pricing]에서 가격을 평가합니다. 에 등록할 수 있습니다 [https://www.twilio.com/try-twilio][try_twilio]합니다. Twilio에서 제공 하는 API에 대 한 정보를 참조 하십시오. [http://www.twilio.com/api][twilio_api]합니다.
2. Twilio JAR를 가져옵니다. [https://github.com/twilio/twilio-java][twilio_java_github]에서 GitHub 원본을 다운로드한 후 고유한 JAR을 만들거나 종속성 포함 여부에 관계없이 미리 빌드된 JAR을 다운로드할 수 있습니다.
   이 항목의 코드는 미리 빌드된 TwilioJava-3.3.8-with-dependencies JAR을 사용하여 작성되었습니다.
3. Java 빌드 경로에 JAR을 추가합니다.
4. Eclipse를 사용하여 이 Java 응용 프로그램을 만드는 경우, Eclipse의 배포 어셈블리 기능을 사용하여 응용 프로그램 배포 파일(WAR)에 Twilio JAR을 포함시킵니다. Eclipse를 사용하지 않고 이 Java 응용 프로그램을 만드는 경우, 같은 Azure 역할 내에 Twilio JAR이 Java 응용 프로그램으로 포함되어 있으며 응용 프로그램의 클래스 경로에 추가되어 있는지 확인합니다.
5. cacerts keystore에 MD5 지문이 67:CB:9D:C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4인 Equifax Secure Certificate Authority 인증서가 포함되어 있는지 확인합니다(일련 번호는 35:DE:F4:CF이고 SHA1 지문은 D2:32:09:AD:23:D3:14:23:21:74:E4:0D:7F:9D:62:13:97:86:63:3A임). 이 인증서는 Twilio API를 사용할 때 호출되는 [https://api.twilio.com][twilio_api_service] 서비스에 대한 CA(인증 기관) 인증서입니다. 이 CA 인증서를 JDK의 cacert 저장소에 추가 하는 방법에 대 한 정보를 참조 하십시오. [Java CA 인증서 저장소에 인증서 추가][add_ca_cert]합니다.

또한 경험에 대 한 정보를 [Eclipse 용 Azure 도구 키트는 Hello World 응용 프로그램 사용 하 여 만드는][azure_java_eclipse_hello_world], 나는 경우 Azure에서 Java 응용 프로그램을 호스트 하는 것에 대 한 다른 기술로 Eclipse를 사용 하지는 것이 좋습니다.

## <a name="create-a-web-form-for-making-a-call"></a>전화 걸기 웹 양식 만들기
다음 코드는 전화를 걸기 위해 웹 양식을 만들고 사용자 데이터를 검색하는 방법을 보여 줍니다. 이 예제에서는 **TwilioCloud**라는 새 동적 웹 프로젝트가 생성되고 **callform.jsp**가 JSP 파일로 추가되었습니다.

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
        pageEncoding="ISO-8859-1" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Automated call form</title>
    </head>
    <body>
     <p>Fill in all fields and click <b>Make this call</b>.</p>
     <br/>
      <form action="makecall.jsp" method="post">
       <table>
         <tr>
           <td>To:</td>
           <td><input type="text" size=50 name="callTo" value="" />
           </td>
         </tr>
         <tr>
           <td>From:</td>
           <td><input type="text" size=50 name="callFrom" value="" />
           </td>
         </tr>
         <tr>
           <td>Call message:</td>
           <td><input type="text" size=400 name="callText" value="Hello. This is the call text. Good bye." />
           </td>
         </tr>
         <tr>
           <td colspan=2><input type="submit" value="Make this call" />
           </td>
         </tr>
       </table>
     </form>
     <br/>
    </body>
    </html>

## <a name="create-the-code-to-make-the-call"></a>코드를 만들어 전화 걸기
사용자가 callform.jsp에 의해 표시되는 양식을 다 작성하면 호출되는 다음 코드는 통화 메시지를 만들고 통화를 생성합니다. 이 예제에서는 JSP 파일이 **makecall.jsp**로 이름이 지정되고 **TwilioCloud** 프로젝트에 추가되었습니다. (아래 코드에서 **accountSID** 및 **authToken**에 할당된 자리 표시자 값 대신 Twilio 계정 및 인증 토큰을 사용하십시오.)

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
    import="java.util.*"
    import="com.twilio.*"
    import="com.twilio.sdk.*"
    import="com.twilio.sdk.resource.factory.*"
    import="com.twilio.sdk.resource.instance.*"
    pageEncoding="ISO-8859-1" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Call processing happens here</title>
    </head>
    <body>
        <b>This is my make call page.</b><p/>
     <%
    try 
    {
         // Use your account SID and authentication token instead
         // of the placeholders shown here.
         String accountSID = "your_twilio_account";
         String authToken = "your_twilio_authentication_token";

         // Instantiate an instance of the Twilio client.     
         TwilioRestClient client;
         client = new TwilioRestClient(accountSID, authToken);

         // Retrieve the account, used later to retrieve the CallFactory.
         Account account = client.getAccount();

         // Display the client endpoint. 
         out.println("<p>Using Twilio endpoint " + client.getEndpoint() + ".</p>");

         // Display the API version.
         String APIVERSION = TwilioRestClient.DEFAULT_VERSION;
         out.println("<p>Twilio client API version is " + APIVERSION + ".</p>");

         // Retrieve the values entered by the user.
         String callTo = request.getParameter("callTo");  
         // The Outgoing Caller ID, used for the From parameter,
         // must have previously been verified with Twilio.
         String callFrom = request.getParameter("callFrom");
         String userText = request.getParameter("callText");

         // Replace spaces in the user's text with '%20', 
         // to make the text suitable for a URL.
         userText = userText.replace(" ", "%20");

         // Create a URL using the Twilio message and the user-entered text.
         String Url="http://twimlets.com/message";
         Url = Url + "?Message%5B0%5D=" + userText;

         // Display the message URL.
         out.println("<p>");
         out.println("The URL is " + Url);
         out.println("</p>");

         // Place the call From, To and URL values into a hash map. 
         HashMap<String, String> params = new HashMap<String, String>();
         params.put("From", callFrom);
         params.put("To", callTo);
         params.put("Url", Url);

         CallFactory callFactory = account.getCallFactory();
         Call call = callFactory.create(params);
         out.println("<p>Call status: " + call.getStatus()  + "</p>"); 
    } 
    catch (TwilioRestException e) 
    {
        out.println("<p>TwilioRestException encountered: " + e.getMessage() + "</p>");
        out.println("<p>StackTrace: " + e.getStackTrace().toString() + "</p>");
    }
    catch (Exception e) 
    {
        out.println("<p>Exception encountered: " + e.getMessage() + "");
        out.println("<p>StackTrace: " + e.getStackTrace().toString() + "</p>");
    }
    %>
    </body>
    </html>

전화 걸기와 함께, makecall.jsp는 Twilio 끝점, API 버전 및 통화 상태를 표시합니다. 다음 스크린샷에 예가 있습니다.

![Twilio 및 Java를 사용하는 Azure 통화 응답][twilio_java_response]

## <a name="run-the-application"></a>응용 프로그램 실행
다음은; 응용 프로그램을 실행 하는 개략적인 단계 다음이 단계를 찾을 수 있습니다 세부 [Eclipse 용 Azure 도구 키트는 Hello World 응용 프로그램 사용 하 여 만드는][azure_java_eclipse_hello_world]합니다.

1. TwilioCloud WAR을 Azure **approot** 폴더로 내보냅니다. 
2. **startup.cmd** 를 수정하여 TwilioCloud WAR의 압축을 풉니다.
3. 계산 에뮬레이터에 대해 응용 프로그램을 컴파일합니다.
4. 계산 에뮬레이터에서 배포를 시작합니다.
5. 브라우저를 열고 **http://localhost:8080/TwilioCloud/callform.jsp**를 실행합니다.
6. 양식에 값을 입력하고 **Make this call**을 클릭한 다음 makecall.jsp의 결과를 확인합니다.

Azure에 배포할 준비가 되면 클라우드에 배포에 대해 다시 컴파일하고 Azure에 배포한 다음 브라우저에서 http://*your_hosted_name*.cloudapp.net/TwilioCloud/callform.jsp를 실행합니다(*your_hosted_name*의 값 대체).

## <a name="next-steps"></a>다음 단계
이 코드는 Azure의 Java에서 Twilio를 사용하는 기본 기능을 보여 줍니다. Azure를 프로덕션에 배포하기 전에 더 많은 오류 처리 또는 기타 기능을 추가할 수 있습니다. 예를 들면 다음과 같습니다.

* 웹 양식을 사용하는 대신, Azure 저장소 Blob 또는 SQL 데이터베이스를 사용하여 전화 번호 및 통화 텍스트를 저장할 수 있습니다. Java에서 Azure 저장소 blob을 사용 하는 방법에 대 한 정보를 참조 하십시오. [Java에서 Blob 저장소 서비스를 사용 하는 방법을][howto_blob_storage_java]합니다. Java에서 SQL 데이터베이스를 사용 하는 방법에 대 한 정보를 참조 하십시오. [Java에서 SQL 데이터베이스를 사용 하 여][howto_sql_azure_java]합니다.
* makecall.jsp에서 값을 하드 코딩하는 대신, **RoleEnvironment.getConfigurationSettings** 를 사용하여 배포 구성 설정에서 Twilio 계정 ID 및 인증 토큰을 검색할 수 있습니다. 에 대 한 내용은 **RoleEnvironment** 클래스를 참조 하십시오. [JSP에서 Azure 서비스 런타임 라이브러리를 사용 하 여] [ azure_runtime_jsp] 및 에Azure서비스런타임패키지설명서[http://dl.windowsazure.com/javadoc][azure_javadoc]합니다.
* Makecall.jsp 코드 Twilio 제공 URL 할당 [http://twimlets.com/message][twimlet_message_url]을 **Url** 변수입니다. 이 URL은 Twilio에 통화를 진행하는 방법을 알리는 TwiML(Twilio Markup Language) 응답을 제공합니다. 예를 들어, 반환되는 TwiML에는 통화 수신자에게 말하는 텍스트에 나타나는 **&lt;Say&gt;** 동사가 포함될 수 있습니다. Twilio 제공 URL을 사용 하는 대신 Twilio의 요청에 응답 하도록 사용자 고유의 서비스를 작성할 수 있습니다. 자세한 내용은 참조 [음성 및 SMS 기능 Java에서 사용 하 여 Twilio 하는 방법을][howto_twilio_voice_sms_java]합니다. TwiML에 대 한 자세한 정보를 찾을 수 있습니다 [http://www.twilio.com/docs/api/twiml][twiml], 및에 대 한 자세한 내용은  **&lt;의견을 언급 해서도&gt;**  및 다른 Twilio 동사를 찾을 수 있습니다 [http://www.twilio.com/docs/api/twiml/say][twilio_say]합니다.
* [https://www.twilio.com/docs/security][twilio_docs_security]에서 Twilio 보안 지침을 읽으세요.

Twilio에 대한 자세한 내용은 [https://www.twilio.com/docs][twilio_docs]를 참조하세요.

## <a name="see-also"></a>참고 항목
* [음성 및 SMS 기능 Java에서 위해 Twilio를 사용 하는 방법][howto_twilio_voice_sms_java]
* [Java CA 인증서 저장소에 인증서 추가][add_ca_cert]

[twilio_pricing]: http://www.twilio.com/pricing
[try_twilio]: http://www.twilio.com/try-twilio
[twilio_api]: http://www.twilio.com/api
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_java_github]: http://github.com/twilio/twilio-java
[twimlet_message_url]: http://twimlets.com/message
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api_service]: http://api.twilio.com
[add_ca_cert]: java-add-certificate-ca-store.md
[azure_java_eclipse_hello_world]: http://msdn.microsoft.com/library/windowsazure/hh690944.aspx
[howto_twilio_voice_sms_java]: partner-twilio-java-how-to-use-voice-sms.md
[howto_blob_storage_java]: http://www.windowsazure.com/develop/java/how-to-guides/blob-storage/
[howto_sql_azure_java]: http://msdn.microsoft.com/library/windowsazure/hh749029.aspx
[azure_runtime_jsp]: http://msdn.microsoft.com/library/windowsazure/hh690948.aspx
[azure_javadoc]: http://dl.windowsazure.com/javadoc
[twilio_docs_security]: http://www.twilio.com/docs/security
[twilio_docs]: http://www.twilio.com/docs
[twilio_say]: http://www.twilio.com/docs/api/twiml/say
[twilio_java]: ./media/partner-twilio-java-phone-call-example/WA_TwilioJavaCallForm.jpg
[twilio_java_response]: ./media/partner-twilio-java-phone-call-example/WA_TwilioJavaMakeCall.jpg
