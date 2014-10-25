<properties title="How to Use Twilio for Voice and SMS (PHP) - Azure" pageTitle="How to Use Twilio for Voice and SMS (PHP) - Azure" metaKeywords="Azure PHP Twilio, Azure phone calls, Azure phone calls, Azure twilio, Azure SMS, Azure SMS, Azure voice calls, azure voice calls, Azure text messages, Azure text messages" description="Learn how to make a phone call and send a SMS message with the Twilio API service on Azure. Code samples written in PHP." documentationCenter="PHP" services="" authors="MicrosoftHelp@twilio.com; robmcm" manager="wpickett" editor="mollybos" videoId="" scriptId="" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="MicrosoftHelp@twilio.com; robmcm"></tags>

# PHP에서 음성 및 SMS 기능을 위해 Twilio를 사용하는 방법

이 가이드에서는 Azure에서 Twilio API 서비스로 일반 프로그래밍 작업을 수행하는 방법을 보여 줍니다. 이 문서의 시나리오에서는 전화 통화를 걸고 SMS(Short Message Service) 메시지를 보냅니다. 응용 프로그램에서 음성 및 SMS 사용 방법과 Twilio에 대한 자세한 내용은 [다음 단계][] 섹션을 참조하십시오.

## 목차

-   [Twilio 정의][]
-   [Twilio 가격 책정][]
-   [개념][]
-   [Twilio 계정 만들기][]
-   [전화 번호 확인][]
-   [PHP 응용 프로그램 만들기][]
-   [Twilio 라이브러리를 사용하도록 응용 프로그램 구성][]
-   [방법: 발신 전화 걸기][]
-   [방법: SMS 메시지 보내기][]
-   [방법: 고유한 웹 사이트에서 TwiML 응답 제공][]

## <span id="WhatIs"></span></a>Twilio 정의

Twilio는 개발자가 응용 프로그램에 음성, VoIP 및 메시징을 포함할 수 있도록 하면서 비즈니스 통신의 미래를 이끌고 있습니다. 개발자는 클라우드 기반 글로벌 환경에 필요한 모든 인프라를 가상화하고, Twilio 통신 API 플랫폼을 통해 이를 공개합니다. 덕분에 응용 프로그램을 간단히 빌드하고 확장할 수 있습니다. 종량제 가격의 유연성과 클라우드 안정성의 이점을 누리세요.

**Twilio 음성**을 통해 응용 프로그램에서 전화 통화를 걸고 받을 수 있습니다. **Twilio SMS**를 사용하면 응용 프로그램에서 문자 메시지를 보내고 받을 수 있습니다. **Twilio 클라이언트**를 통해서는 전화, 태블릿 또는 브라우저에서 VoIP 통화를 하고 WebRTC를 지원할 수 있습니다.

## <span id="Pricing"></span></a>Twilio 가격 책정 및 특별 제공

Azure 고객은 Twilio 계정을 업그레이드할 경우 10달러의 무료 Twilio 크레딧을 제공하는 [특가 제공](http://www.twilio.com/azure) 혜택을 받을 수 있습니다. 이 Twilio 크레딧은 모든 Twilio 사용량에 적용될 수 있습니다. 10달러의 크레딧은 전화 번호 및 메시지 또는 통화 대상의 위치에 따라 SMS 메시지를 1,000개 보내거나 최대 1000분간 인바운드 음성을 받을 수 있는 금액입니다. 에서 이 Twilio 크레딧을 충전하고 [][]<http://ahoy.twilio.com/azure></a>(영문)

Twilio는 종량제 서비스입니다. 설정 수수료는 없으며 언제든 계정을 종료할 수 있습니다. [Twilio 가격 책정][1](영문)에서 자세한 내용을 볼 수 있습니다.

## <span id="Concepts"></span></a>개념

Twilio API는 응용 프로그램에 대한 음성 및 SMS 기능을 제공하는 RESTful API입니다. 클라이언트 라이브러리는 다양한 언어로 사용할 수 있습니다. 목록에 대해서는 [Twilio API 라이브러리][](영문)를 참조하세요.

Twilio API의 핵심 요소는 Twilio 동사와 TwiML(Twilio Markup Language)입니다.

### <span id="Verbs"></span></a>Twilio 동사

API는 Twilio 동사를 활용합니다. 예를 들어 **\<Say\>** 동사는 Twilio에 통화 메시지를 음성으로 전달하도록 지시합니다.

다음은 Twilio 동사의 목록입니다. [Twilio Markup Language 설명서](http://www.twilio.com/docs/api/twiml)을 통해 다른 동사 및 기능에 대해 알아보세요.

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

Twilio에서 계정에 대한 여러 전화 번호를 인증해야 합니다. 예를 들어, 발신자 번호에 대해 기존 전화 번호를 사용하여 아웃바운드 통화를 하려는 경우 Twilio에서 전화 번호를 확인해야 합니다. 마찬가지로, 업그레이드할 때까지는 전화 번호로 SMS 메시지를 보내려는 경우 Twilio에서 전화 번호를 확인해야 합니다. 업그레이드한 후에는 번호를 확인하지 않고 어떤 번호로도 SMS 메시지를 보낼 수 있습니다. 전화 번호를 확인하는 방법에 대한 자세한 내용은 [번호 관리][](영문)를 참조하십시오. 아래 코드 중 일부는 Twilio에서 확인해야 하는 전화 번호를 사용합니다.

응용 프로그램의 기존 번호를 사용하지 않고 Twilio 전화 번호를 구매할 수도 있습니다. Twilio 전화 번호를 구매하는 방법에 대해서는 [Twilio 전화 번호 도움말][](영문)을 참조하세요.

## <span id="create_app"></span></a>PHP 응용 프로그램 만들기

Twilio 서비스를 사용하고 Azure에서 실행되고 있는 PHP 응용 프로그램은 Twilio 서비스를 사용하는 다른 PHP 응용 프로그램과 차이가 없습니다. Twilio 서비스가 REST 기반이고 여러 가지 방법으로 PHP에서 호출될 수 있기는 하지만, 이 문서에서는 [Github의 PHP용 Twilio 라이브러리][](영문)와 Twilio 서비스를 사용하는 방법을 집중적으로 설명합니다. PHP용 Twilio 라이브러리 사용에 대한 자세한 내용은 [][2]<http://readthedocs.org/docs/twilio-php/en/latest/index.html></a>(영문)을 참조하십시오.

Twilio/PHP 응용 프로그램을 빌드하여 Azure에 배포하는 방법에 대한 자세한 지침은 [Azure의 PHP 응용 프로그램에서 Twilio를 사용하여 전화 통화를 거는 방법][](영문)에서 제공합니다.

## <span id="configure_app"></span></a>Twilio 라이브러리를 사용하도록 응용 프로그램 구성

다음과 같은 두 가지 방법으로 PHP용 Twilio 라이브러리를 사용하도록 응용 프로그램을 구성할 수 있습니다.

1.  Github의 PHP용 Twilio 라이브러리([][Github의 PHP용 Twilio 라이브러리]<https://github.com/twilio/twilio-php></a>(영문))를 다운로드하고 응용 프로그램에 **Services** 디렉터리를 추가합니다.

    또는

2.  PHP용 Twilio 라이브러리를 PEAR 패키지로 설치합니다. 다음 명령을 사용하여 설치할 수 있습니다.

        $ pear channel-discover twilio.github.com/pear
        $ pear install twilio/Services_Twilio

PHP용 Twilio 라이브러리를 설치하고 나면 PHP 파일의 맨 위에 **require\_once** 문을 추가하여 라이브러리를 참조하도록 할 수 있습니다.

        require_once 'Services/Twilio.php';

자세한 내용은 [][3]<https://github.com/twilio/twilio-php/blob/master/README.md></a>(영문)를 참조하십시오.

## <span id="howto_make_call"></span></a>방법: 발신 전화 걸기

다음은 **Services\_Twilio** 클래스를 사용하여 발신 전화를 거는 방법을 보여 줍니다. 또한 이 코드는 Twilio 제공 사이트를 사용하여 TwiML(Twilio Markup Language) 응답을 반환합니다. **From** 및 **To** 전화 번호의 값을 바꾸고, 코드를 실행하기 전에 Twilio 계정의 **From** 번호를 확인하세요.

    // Include the Twilio PHP library.
    require_once 'Services/Twilio.php';

    // Library version.
    $version = "2010-04-01";

    // Set your account ID and authentication token.
    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";

    // The number of the phone initiating the the call.
    // (Must be previously validated with Twilio.)
    $from_number = "NNNNNNNNNNN";

    // The number of the phone receiving call.
    $to_number = "NNNNNNNNNNN";

    // Use the Twilio-provided site for the TwiML response.
    $url = "http://twimlets.com/message";

    // The phone message text.
    $message = "Hello world.";

    // Create the call client.
    $client = new Services_Twilio($sid, $token, $version);

    //Make the call.
    try
    {
        $call = $client->account->calls->create(
            $from_number, 
            $to_number,
            $url.'?Message='.urlencode($message)
        );
    }
    catch (Exception $e) 
    {
        echo 'Error: ' . $e->getMessage();
    }

언급한 대로 이 코드는 Twilio 제공 사이트를 사용하여 TwiML 응답을 반환합니다. 고유한 사이트를 대신 사용하여 TwiML 응답을 제공할 수 있습니다. 자세한 내용은 [고유한 웹 사이트에서 TwiML 응답을 제공하는 방법][방법: 고유한 웹 사이트에서 TwiML 응답 제공]을 참조하십시오.

-   **참고**: SSL 인증서 유효성 검사 오류 문제를 해결하려면 [][4]<http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html></a>(영문)을 참조하십시오.

## <span id="howto_send_sms"></span></a>방법: SMS 메시지 보내기

다음은 **Services\_Twilio** 클래스를 사용하여 SMS 메시지를 보내는 방법을 보여 줍니다. 평가판 계정이 SMS 메시지를 보낼 **From** 번호는 자동으로 입력됩니다. 코드를 실행하기 전에 Twilio 계정에 대한 **To** 번호를 확인해야 합니다.

    // Include the Twilio PHP library.
    require_once 'Services/Twilio.php';

    // Library version.
    $version = "2010-04-01";

    // Set your account ID and authentication token.
    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";


    $from_number = "NNNNNNNNNNN"; // With trial account, texts can only be sent from your Twilio number.
    $to_number = "NNNNNNNNNNN";
    $message = "Hello world.";

    // Create the call client.
    $client = new Services_Twilio($sid, $token, $version);

    // Send the SMS message.
    try
    {
        $client->account->sms_messages->create($from_number, $to_number, $message);
    }
    catch (Exception $e) 
    {
        echo 'Error: ' . $e->getMessage();
    }

## <span id="howto_provide_twiml_responses"></span></a>방법: 고유한 웹 사이트에서 TwiML 응답 제공

응용 프로그램에서 Twilio API 호출을 시작하면 Twilio에서 TwiML 응답을 반환해야 하는 URL로 요청을 보냅니다. 위 예제에서는 Twilio 제공 URL인 [][5]<http://twimlets.com/message></a>를 사용합니다. TwiML은 Twilio에서 사용되도록 설계되었지만 브라우저에서도 TwiML을 볼 수 있습니다. 예를 들어 [][5]<http://twimlets.com/message></a>를 클릭하면 빈 `<Response>` 요소가 표시됩니다. 또 다른 예로, [][6]<http://twimlets.com/message?Message%5B0%5D=Hello%20World></a>를 클릭하면 `<Say>` 요소를 포함하는 `<Response>` 요소가 표시됩니다.

Twilio 제공 URL을 사용하지 않고 HTTP 응답을 반환하는 고유한 사이트를 만들 수 있습니다. XML 응답을 반환하는 모든 언어로 사이트를 만들 수 있습니다. 이 항목에서는 PHP를 사용하여 TwiML을 만든다고 가정합니다.

다음 PHP 페이지에서는 호출 시 **Hello World**라고 말하는 TwiML 응답이 생성됩니다.

    <?php    
        header("content-type: text/xml");    
        echo "<?xml version=\"1.0\" encoding=\"UTF-8\"?>\n";
    ?>
    <Response>    
        <Say>Hello world.</Say>
    </Response>

위의 예제와 같이 TwiML 응답은 단지 XML 문서입니다. PHP용 Twilio 라이브러리에는 TwiML을 자동으로 생성하는 클래스가 포함되어 있습니다. 아래의 예제에서는 위에 표시된 것과 동일한 응답을 생성하지만 PHP용 Twilio 라이브러리의 **Services\_Twilio\_Twiml** 클래스를 사용합니다.

    require_once('Services/Twilio.php');

    $response = new Services_Twilio_Twiml();
    $response->say("Hello world.");
    print $response;

TwiML에 대한 자세한 내용은 [][7]<https://www.twilio.com/docs/api/twiml></a>(영문)을 참조하세요.

PHP 페이지가 TwiML 응답을 제공하도록 설정된 경우 `Services_Twilio->account->calls->create` 메서드에 전달되는 URL로 PHP 페이지의 URL을 사용합니다. 예를 들어 **MyTwiML**이라는 웹 응용 프로그램이 Azure 호스팅 서비스에 배포되어 있고 PHP 페이지의 이름이 **mytwiml.php**인 경우 다음 예와 같이 URL을 **Services\_Twilio-\>account-\>calls-\>create**에 전달할 수 있습니다.

    require_once 'Services/Twilio.php';

    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";
    $from_number = "NNNNNNNNNNN";
    $to_number = "NNNNNNNNNNN";
    $url = "http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.php";

    // The phone message text.
    $message = "Hello world.";

    $client = new Services_Twilio($sid, $token, "2010-04-01");

    try
    {
        $call = $client->account->calls->create(
            $from_number, 
            $to_number,
            $url.'?Message='.urlencode($message)
        );
    }
    catch (Exception $e) 
    {
        echo 'Error: ' . $e->getMessage();
    }

Azure에서 PHP와 함께 Twilio를 사용하는 방법에 대한 자세한 내용은 [Azure의 PHP 응용 프로그램에서 Twilio를 사용하여 전화 통화를 거는 방법][](영문)을 참조하십시오.

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
  [PHP 응용 프로그램 만들기]: #create_app
  [Twilio 라이브러리를 사용하도록 응용 프로그램 구성]: #configure_app
  [방법: 발신 전화 걸기]: #howto_make_call
  [방법: SMS 메시지 보내기]: #howto_send_sms
  [방법: 고유한 웹 사이트에서 TwiML 응답 제공]: #howto_provide_twiml_responses
  []: http://ahoy.twilio.com/azure
  [1]: http://www.twilio.com/pricing
  [Twilio API 라이브러리]: https://www.twilio.com/docs/libraries
  [TwiML]: http://www.twilio.com/docs/api/twiml
  [Twilio API]: http://www.twilio.com/api
  [Twilio 체험]: https://www.twilio.com/try-twilio
  [Twilio 계정 페이지]: https://www.twilio.com/user/account
  [번호 관리]: https://www.twilio.com/user/account/phone-numbers/verified#
  [Twilio 전화 번호 도움말]: https://www.twilio.com/help/faq/phone-numbers
  [Github의 PHP용 Twilio 라이브러리]: https://github.com/twilio/twilio-php
  [2]: http://readthedocs.org/docs/twilio-php/en/latest/index.html
  [Azure의 PHP 응용 프로그램에서 Twilio를 사용하여 전화 통화를 거는 방법]: ../partner-twilio-php-make-phone-call
  [3]: https://github.com/twilio/twilio-php/blob/master/README.md
  [4]: http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html
  [5]: http://twimlets.com/message
  [6]: http://twimlets.com/message?Message%5B0%5D=Hello%20World
  [7]: https://www.twilio.com/docs/api/twiml
  [Twilio 보안 지침(영문)]: http://www.twilio.com/docs/security
  [Twilio 방법 및 예제 코드(영문)]: http://www.twilio.com/docs/howto
  [Twilio 빠른 시작 자습서(영문)]: http://www.twilio.com/docs/quickstart
  [Twilio on GitHub(영문)]: https://github.com/twilio
  [Twilio 지원 문의(영문)]: http://www.twilio.com/help/contact
