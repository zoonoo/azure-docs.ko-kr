<properties linkid="develop-java-how-to-twilio-sms-service" urlDisplayName="Twilio Voice/SMS Service" pageTitle="How to Use Twilio for Voice and SMS (Java) - Azure" metaKeywords="Twilio, Twilio API, phone calls, SMS message, TwiML responses, Azure Twilio Java" description="Learn how to make a phone call and send a SMS message with the Twilio API service on Azure. Code samples written in Java." metaCanonical="" services="" videoId="" scriptId="" documentationCenter="Java" title="How to Use Twilio for Voice and SMS Capabilities in Java" authors="MicrosoftHelp@twilio.com; robmcm" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="MicrosoftHelp@twilio.com; robmcm"></tags>

# Java에서 음성 및 SMS 기능을 위해 Twilio를 사용하는 방법(영문)

이 가이드에서는 Azure에서 Twilio API 서비스로 일반 프로그래밍 작업을 수행하는 방법을 보여 줍니다. 이 문서의 시나리오에서는 전화 통화를 걸고 SMS(Short Message Service) 메시지를 보냅니다. 응용 프로그램에서 음성 및 SMS 사용 방법과 Twilio에 대한 자세한 내용은 [다음 단계][] 섹션을 참조하십시오.

## 목차

-   [Twilio 정의][]
-   [Twilio 가격 책정][]
-   [개념][]
-   [Twilio 계정 만들기][]
-   [전화 번호 확인][]
-   [Java 응용 프로그램 만들기][]
-   [Twilio 라이브러리를 사용하도록 응용 프로그램 구성][]
-   [방법: 발신 전화 걸기][]
-   [방법: SMS 메시지 보내기][]
-   [방법: 고유한 웹 사이트에서 TwiML 응답 제공][]
-   [방법: 추가 Twilio 서비스 사용][]
-   [다음 단계][]

## <span id="WhatIs"></span></a>Twilio 정의

Twilio는 기존 웹 언어와 기술을 사용하여 음성 및 SMS 응용 프로그램을 빌드할 수 있게 해주는 전화 통신 웹 서비스 API입니다. Twilio는 타사 서비스입니다(Azure 기능 또는 Microsoft 제품 아님).

**Twilio 음성**을 통해 응용 프로그램에서 전화 통화를 걸고 받을 수 있습니다. **Twilio SMS**를 사용하면 응용 프로그램에서 SMS 메시지를 작성하고 받을 수 있습니다. **Twilio 클라이언트**를 사용하면 응용 프로그램에서 모바일 연결을 비롯한 기존 인터넷 연결을 통해 음성 통신을 사용할 수 있습니다.

## <span id="Pricing"></span></a>Twilio 가격 책정 및 특별 제공

Twilio 가격 책정 정보는 [Twilio 가격 책정][1]에서 확인할 수 있습니다. Azure 고객은 문자 1000개 또는 인바운드 1000분의 무료 크레딧을 제공하는 [특가 제공][]을 받을 수 있습니다. 이 제공에 등록하거나 추가 정보를 얻으려면 [][특가 제공]<http://ahoy.twilio.com/azure></a>를 방문하십시오.

## <span id="Concepts"></span></a>개념

Twilio API는 응용 프로그램에 대한 음성 및 SMS 기능을 제공하는 RESTful API입니다. 클라이언트 라이브러리는 다양한 언어로 사용할 수 있습니다. 목록에 대해서는 [Twilio API 라이브러리][](영문)를 참조하세요.

Twilio API의 핵심 요소는 Twilio 동사와 TwiML(Twilio Markup Language)입니다.

### <span id="Verbs"></span></a>Twilio 동사

API는 Twilio 동사를 활용합니다. 예를 들어 **\<Say\>** 동사는 Twilio에 통화 메시지를 음성으로 전달하도록 지시합니다.

다음은 Twilio 동사의 목록입니다.

-   **\<Dial\>**: 발신자를 다른 전화에 연결합니다.
-   **\<Gather\>**: 전화 키패드에 입력된 숫자를 수집합니다.
-   **\<Hangup\>**: 통화를 끝냅니다.
-   **\<Play\>**: 오디오 파일을 재생합니다.
-   **\<Pause\>**: 지정된 시간(초) 동안 무음으로 대기합니다.
-   **\<Record\>**: 발신자의 음성을 녹음하고 녹음이 포함된 파일의 URL을 반환합니다.
-   **\<Redirect\>**: 통화 또는 SMS에 대한 제어를 다른 URL의 TwiML로 전송합니다.
-   **\<Reject\>**: 요금을 청구하지 않고 Twilio 번호의 수신 전화를 거부합니다.
-   **\<Say\>**: 텍스트를 통화에 사용되는 음성으로 변환합니다.
-   **\<Sms\>**: SMS 메시지를 보냅니다.

### <span id="TwiML"></span></a>TwiML

TwiML은 Twilio에 통화 또는 SMS 처리 방법을 알려 주는 Twilio 동사를 사용하는 XML 기반 명령 집합입니다.

다음 예제 TwiML은 **Hello World** 텍스트를 음성으로 변환합니다.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

응용 프로그램에서 Twilio API를 호출할 때 API 매개 변수 중 하나는 TwiML 응답을 반환하는 URL입니다. 개발을 위해서 Twilio 제공 URL을 사용하여 응용 프로그램에 사용되는 TwiML 응답을 제공할 수 있습니다. 또한 TwiML 응답을 생성하는 고유한 URL을 호스트할 수도 있고, **TwiMLResponse** 개체를 사용할 수도 있습니다.

Twilio 동사, 특성 및 TwiML에 대한 자세한 내용은 [TwiML][](영문)을 참조하세요. Twilio API에 대한 자세한 내용은 [Twilio API][](영문)를 참조하세요.

## <span id="CreateAccount"></span></a>Twilio 계정 만들기

Twilio 계정을 사용할 준비가 되었다면 [Twilio 체험][](영문)에서 등록하세요. 무료 계정으로 시작했다가 나중에 계정을 업그레이드할 수 있습니다.

Twilio 계정을 등록하면 계정 ID 및 인증 토큰을 받게 됩니다. 둘 다 Twilio API 통화를 하는 데 필요합니다. 계정에 대한 무단 액세스를 방지하려면 인증 토큰을 안전하게 유지하세요. 계정 ID 및 인증 토큰은 [Twilio 계정 페이지][](영문)의 **ACCOUNT SID** 및 **AUTH TOKEN**에서 각기 확인할 수 있습니다.

## <span id="VerifyPhoneNumbers"></span></a>전화 번호 확인

Twilio에서 계정에 대한 여러 전화 번호를 인증해야 합니다. 예를 들어 아웃바운드 통화를 하려는 경우 Twilio에서 전화 번호를 아웃바운드 발신자 번호로 확인해야 합니다. 마찬가지로, SMS 메시지를 받을 전화 번호를 원하는 경우 Twilio에서 수신 전화 번호를 확인해야 합니다. 전화 번호를 확인하는 방법에 대한 자세한 내용은 [번호 관리][](영문)를 참조하십시오. 아래 코드 중 일부는 Twilio에서 확인해야 하는 전화 번호를 사용합니다.

응용 프로그램의 기존 번호를 사용하지 않고 Twilio 전화 번호를 구매할 수도 있습니다. Twilio 전화 번호를 구매하는 방법에 대해서는 [Twilio 전화 번호 도움말][](영문)을 참조하세요.

## <span id="create_app"></span></a>Java 응용 프로그램 만들기

1.  Twilio JAR을 다운로드하여 Java 빌드 경로 및 WAR 배포 어셈블리에 추가합니다. [][]<https://github.com/twilio/twilio-java></a>(영문)에서 GitHub 원본을 다운로드한 후 고유한 JAR을 만들거나 종속성 포함 여부에 관계없이 미리 빌드된 JAR을 다운로드할 수 있습니다.
2.  JDK의 **cacerts** keystore에 MD5 지문이 67:CB:9D:C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4인 Equifax Secure Certificate Authority 인증서가 포함되어 있는지 확인합니다(일련 번호는 35:DE:F4:CF이고 SHA1 지문은 D2:32:09:AD:23:D3:14:23:21:74:E4:0D:7F:9D:62:13:97:86:63:3A임). 이 인증서는 Twilio API를 사용할 때 호출되는 [][2]<https://api.twilio.com></a>(영문) 서비스에 대한 CA(인증 기관) 인증서입니다. JDK의 **cacerts** keystore에 올바른 CA 인증서가 포함되어 있는지 확인하는 방법에 대한 자세한 내용은 [Java CA 인증서 저장소에 인증서 추가][]를 참조하십시오.

Java용 Twilio 클라이언트 라이브러리 사용에 대한 자세한 지침은 [Azure의 Java 응용 프로그램에서 Twilio를 사용하여 전화를 거는 방법][]을 참조하십시오.

## <span id="configure_app"></span></a>Twilio 라이브러리를 사용하도록 응용 프로그램 구성

코드 내에서 응용 프로그램에 사용할 Twilio 패키지 또는 클래스의 원본 파일 맨 위에 **import** 문을 추가할 수 있습니다.

Java 원본 파일의 경우

    import com.twilio.*;
    import com.twilio.sdk.*;
    import com.twilio.sdk.resource.factory.*;
    import com.twilio.sdk.resource.instance.*;

JSP(Java Server Page) 원본 파일의 경우

    import="com.twilio.*"
    import="com.twilio.sdk.*"
    import="com.twilio.sdk.resource.factory.*"
    import="com.twilio.sdk.resource.instance.*"

사용할 Twilio 패키지 또는 클래스에 따라 **import** 문이 달라질 수 있습니다.

## <span id="howto_make_call"></span></a>방법: 발신 전화 걸기

다음은 **CallFactory** 클래스를 사용하여 발신 전화를 거는 방법을 보여 줍니다. 또한 이 코드는 Twilio 제공 사이트를 사용하여 TwiML(Twilio Markup Language) 응답을 반환합니다. **From** 및 **To** 전화 번호의 값을 바꾸고, 코드를 실행하기 전에 Twilio 계정의 **From** 번호를 확인하세요.

    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    String accountSID = "your_twilio_account";
    String authToken = "your_twilio_authentication_token";

    // Create an instance of the Twilio client.
    TwilioRestClient client;
    client = new TwilioRestClient(accountSID, authToken);

    // Retrieve the account, used later to create an instance of the CallFactory.
    Account account = client.getAccount();

    // Use the Twilio-provided site for the TwiML response.
    String Url="http://twimlets.com/message";
    Url = Url + "?Message%5B0%5D=Hello%20World";

    // Place the call From, To and URL values into a hash map. 
    HashMap<String, String> params = new HashMap<String, String>();
    params.put("From", "NNNNNNNNNN"); // Use your own value for the second parameter.
    params.put("To", "NNNNNNNNNN");   // Use your own value for the second parameter.
    params.put("Url", Url);

    // Create an instance of the CallFactory class.
    CallFactory callFactory = account.getCallFactory();

    // Make the call.
    Call call = callFactory.create(params);

**CallFactory.create** 메서드에 전달된 매개 변수에 대한 자세한 내용은 [][3]<http://www.twilio.com/docs/api/rest/making-calls></a>를 참조하십시오.

언급한 대로 이 코드는 Twilio 제공 사이트를 사용하여 TwiML 응답을 반환합니다. 고유한 사이트를 대신 사용하여 TwiML 응답을 제공할 수 있습니다. 자세한 내용은 [Azure의 Java 응용 프로그램에서 TwiML 응답을 제공하는 방법][방법: 고유한 웹 사이트에서 TwiML 응답 제공]을 참조하십시오.

## <span id="howto_send_sms"></span></a>방법: SMS 메시지 보내기

다음은 **SmsFactory** 클래스를 사용하여 SMS 메시지를 보내는 방법을 보여 줍니다. 체험 계정이 SMS 메시지를 보낼 **From** 번호 **4155992671**은 자동으로 입력됩니다. 코드를 실행하기 전에 Twilio 계정에 대한 **To** 번호를 확인해야 합니다.

    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    String accountSID = "your_twilio_account";
    String authToken = "your_twilio_authentication_token";

    // Create an instance of the Twilio client.
    TwilioRestClient client;
    client = new TwilioRestClient(accountSID, authToken);

    // Retrieve the account, used later to create an instance of the SmsFactory.
    Account account = client.getAccount();

    // Send an SMS message.
    // Place the call From, To and Body values into a hash map. 
    HashMap<String, String> smsParams = new HashMap<String, String>();
    smsParams.put("From", "4155992671"); // The second parameter is a phone number provided
                                         // by Twilio for trial accounts.
    smsParams.put("To", "NNNNNNNNNN");   // Use your own value for the second parameter.
    smsParams.put("Body", "This is my SMS message.");

    // Create an instance of the SmsFactory class.
    SmsFactory smsFactory = account.getSmsFactory();

    // Send the message.
    Sms sms = smsFactory.create(smsParams);

**SmsFactory.create** 메서드에 전달된 매개 변수에 대한 자세한 내용은 [][4]<http://www.twilio.com/docs/api/rest/sending-sms></a>를 참조하십시오.

## <span id="howto_provide_twiml_responses"></span></a>방법: 고유한 웹 사이트에서 TwiML 응답 제공

응용 프로그램에서 Twilio API 호출을 시작하면(예: **CallFactory.create** 메서드를 통해) Twilio에서 TwiML 응답을 반환해야 하는 URL로 요청을 보냅니다. 위 예제에서는 Twilio 제공 URL인 [][5]<http://twimlets.com/message></a>를 사용합니다. TwiML은 웹 서비스에서 사용되도록 설계되었지만 브라우저에서도 TwiML을 볼 수 있습니다. 예를 들어 [][5]<http://twimlets.com/message></a>를 클릭하면 빈 **\<Response\>** 요소가 표시됩니다. 또 다른 예로, [][6]<http://twimlets.com/message?Message%5B0%5D=Hello%20World></a>를 클릭하면 **\<Say\>** 요소를 포함하는 **\<Response\>** 요소가 표시됩니다.

Twilio 제공 URL을 사용하지 않고 HTTP 응답을 반환하는 고유한 URL 사이트를 만들 수 있습니다. HTTP 응답을 반환하는 모든 언어로 사이트를 만들 수 있습니다. 이 항목에서는 JSP 페이지에 URL을 호스트한다고 가정합니다.

다음 JSP 페이지에서는 호출 시 **Hello World**라고 말하는 TwiML 응답이 생성됩니다.

    <%@ page contentType="text/xml" %>
    <Response> 
        <Say>Hello World</Say>
    </Response>

다음 JSP 페이지에서는 일부 텍스트를 말하고 여러 일시 중지를 포함하며 Twilio API 버전 및 Azure 역할 이름에 대한 정보를 말하는 TwiML 응답이 생성됩니다.

    <%@ page contentType="text/xml" %>
    <Response> 
        <Say>Hello from Azure</Say>
        <Pause></Pause>
        <Say>The Twilio API version is <%= request.getParameter("ApiVersion") %>.</Say>
        <Say>The Azure role name is <%= System.getenv("RoleName") %>.</Say>
        <Pause></Pause>
        <Say>Good bye.</Say>
    </Response>

**ApiVersion** 매개 변수는 Twilio 음성 요청(SMS 요청 아님)에 사용할 수 있습니다. Twilio 음성 및 SMS 요청에 사용 가능한 요청 매개 변수를 보려면 각각 <https://www.twilio.com/docs/api/twiml/twilio_request> 및 <https://www.twilio.com/docs/api/twiml/sms/twilio_request>를 참조하세요. **RoleName** 환경 변수는 Azure 배포의 일부로 사용할 수 있습니다. **System.getenv**에서 선택될 수 있도록 사용자 지정 환경 변수를 추가하려면 [기타 역할 구성 설정][]의 환경 변수 섹션을 참조하십시오.

JSP 페이지가 TwiML 응답을 제공하도록 설정된 경우 **CallFactory.create** 메서드에 전달되는 URL로 JSP 페이지의 URL을 사용합니다. 예를 들어 MyTwiML이라는 웹 응용 프로그램이 Azure 호스팅 서비스에 배포되어 있고 JSP 페이지의 이름이 mytwiml.jsp인 경우 다음과 같이 URL을 **CallFactory.create**에 전달할 수 있습니다.

    // Place the call From, To and URL values into a hash map. 
    HashMap<String, String> params = new HashMap<String, String>();
    params.put("From", "NNNNNNNNNN");
    params.put("To", "NNNNNNNNNN");
    params.put("Url", "http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.jsp");

    CallFactory callFactory = account.getCallFactory();
    Call call = callFactory.create(params);

TwiML로 응답하는 또 다른 옵션은 **com.twilio.sdk.verbs** 패키지에 포함된 **TwiMLResponse** 클래스를 사용하는 것입니다.

Azure에서 Java와 함께 Twilio를 사용하는 방법에 대한 자세한 내용은 [Azure의 Java 웹 응용 프로그램에서 Twilio를 사용하여 전화를 거는 방법][Azure의 Java 응용 프로그램에서 Twilio를 사용하여 전화를 거는 방법]을 참조하십시오.

## <span id="AdditionalServices"></span></a>방법: 추가 Twilio 서비스 사용

여기에서 보여 준 예뿐만 아니라 Twilio는 Azure 응용 프로그램에서 Twilio 기능을 활용할 수 있는 웹 기반 API를 제공합니다. 자세한 내용은 [Twilio API 설명서][Twilio API](영문)를 참조하세요.

## <span id="NextSteps"></span></a>다음 단계

Twilio 서비스에 관한 기본적인 사항들을 익혔으며 자세한 내용을 보려면 다음 링크를 따라가세요.

-   [Twilio 보안 지침(영문)][]
-   [Twilio 방법 및 예제 코드(영문)][]
-   [Twilio 빠른 시작 자습서(영문)][]
-   [Twilio on GitHub(영문)][]
-   [Twilio 지원 문의(영문)][]

  [다음 단계]: #NextSteps
  [Twilio 정의]: #WhatIs
  [Twilio 가격 책정]: #Pricing
  [개념]: #Concepts
  [Twilio 계정 만들기]: #CreateAccount
  [전화 번호 확인]: #VerifyPhoneNumbers
  [Java 응용 프로그램 만들기]: #create_app
  [Twilio 라이브러리를 사용하도록 응용 프로그램 구성]: #configure_app
  [방법: 발신 전화 걸기]: #howto_make_call
  [방법: SMS 메시지 보내기]: #howto_send_sms
  [방법: 고유한 웹 사이트에서 TwiML 응답 제공]: #howto_provide_twiml_responses
  [방법: 추가 Twilio 서비스 사용]: #AdditionalServices
  [1]: http://www.twilio.com/pricing
  [특가 제공]: http://ahoy.twilio.com/azure
  [Twilio API 라이브러리]: https://www.twilio.com/docs/libraries
  [TwiML]: http://www.twilio.com/docs/api/twiml
  [Twilio API]: http://www.twilio.com/api
  [Twilio 체험]: https://www.twilio.com/try-twilio
  [Twilio 계정 페이지]: https://www.twilio.com/user/account
  [번호 관리]: https://www.twilio.com/user/account/phone-numbers/verified#
  [Twilio 전화 번호 도움말]: https://www.twilio.com/help/faq/phone-numbers
  []: https://github.com/twilio/twilio-java
  [2]: https://api.twilio.com
  [Java CA 인증서 저장소에 인증서 추가]: ../java-add-certificate-ca-store
  [Azure의 Java 응용 프로그램에서 Twilio를 사용하여 전화를 거는 방법]: ../partner-twilio-java-phone-call-example
  [3]: http://www.twilio.com/docs/api/rest/making-calls
  [4]: http://www.twilio.com/docs/api/rest/sending-sms
  [5]: http://twimlets.com/message
  [6]: http://twimlets.com/message?Message%5B0%5D=Hello%20World
  [기타 역할 구성 설정]: http://msdn.microsoft.com/ko-KR/library/windowsazure/hh690945.aspx
  [Twilio 보안 지침(영문)]: http://www.twilio.com/docs/security
  [Twilio 방법 및 예제 코드(영문)]: http://www.twilio.com/docs/howto
  [Twilio 빠른 시작 자습서(영문)]: http://www.twilio.com/docs/quickstart
  [Twilio on GitHub(영문)]: https://github.com/twilio
  [Twilio 지원 문의(영문)]: http://www.twilio.com/help/contact
