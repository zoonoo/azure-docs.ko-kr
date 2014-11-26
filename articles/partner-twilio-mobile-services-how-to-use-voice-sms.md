<properties linkid="develop-mobile-tutorials-twilio-for-voice-and-sms" pageTitle="Use Twilio for Voice and SMS Capabilities | Mobile Dev Center" metaKeywords="" description="Learn how to perform common tasks using the Twilio API with Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="How to use Twilio for voice and SMS capabilities from Mobile Services" authors="MicrosoftHelp@twilio.com" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="MicrosoftHelp@twilio.com" />

# 모바일 서비스에서 음성 및 SMS 기능을 위해 Twilio를 사용하는 방법

이 항목에서는 Azure 모바일 서비스와 함께 Twilio API를 사용하여 일반적인 작업을 수행하는 방법을 설명합니다. 이 자습서에서는 Twilio API를 사용하는 사용자 지정 API 스크립트를 만들어 전화 통화를 시작하고 SMS(Short Message Service) 메시지를 보내는 방법을 배웁니다.

## <span id="WhatIs"></span></a>Twilio 정의

Twilio는 개발자가 응용 프로그램에 음성, VoIP 및 메시징을 포함할 수 있도록 하면서 비즈니스 통신의 미래를 이끌고 있습니다. 개발자는 클라우드 기반 글로벌 환경에 필요한 모든 인프라를 가상화하고, Twilio 통신 API 플랫폼을 통해 이를 공개합니다. 덕분에 응용 프로그램을 간단히 빌드하고 확장할 수 있습니다. 종량제 가격의 유연성과 클라우드 안정성의 이점을 누리세요.

**Twilio 음성**을 통해 응용 프로그램에서 전화 통화를 걸고 받을 수 있습니다. **Twilio SMS**를 사용하면 응용 프로그램에서 SMS 메시지를 보내고 받을 수 있습니다. **Twilio 클라이언트**를 통해서는 전화, 태블릿 또는 브라우저에서 VoIP 통화를 하고 WebRTC를 지원할 수 있습니다.

## <span id="Pricing"></span></a>Twilio 가격 책정 및 특별 제공

Azure 고객은 문자 1000개 또는 인바운드 1000분의 무료 크레딧을 제공하는 [특가 제공][특가 제공]을 혜택을 받을 수 있습니다. 이 Twilio 크레딧은 모든 Twilio 사용량에 적용될 수 있습니다. 10달러의 크레딧은 전화 번호 및 메시지 또는 통화 대상의 위치에 따라 SMS 메시지를 1,000개 보내거나 최대 1000분간 인바운드 음성을 받을 수 있는 금액입니다. [ahoy.twilio.com/azure][특가 제공](영문)에서 이 Twilio 크레딧을 충전하고 시작하세요.

Twilio는 종량제 서비스입니다. 설정 수수료는 없으며 언제든 계정을 종료할 수 있습니다. [Twilio 가격 책정][Twilio 가격 책정](영문)에서 자세한 내용을 볼 수 있습니다.

## <span id="Concepts"></span></a>개념

Twilio API는 응용 프로그램에 대한 음성 및 SMS 기능을 제공하는 RESTful API입니다. 클라이언트 라이브러리는 다양한 언어로 사용할 수 있습니다. 목록에 대해서는 [Twilio API 라이브러리][Twilio API 라이브러리](영문)를 참조하세요. [.NET][.NET], [node.js][node.js], [Java][Java], [PHP][PHP], [Python][Python] 또는 [Ruby][Ruby]에서 작성된 Azure 응용 프로그램에서 Twilio를 사용에 대해 추가 자습서를 사용할 수 있습니다.

Twilio API의 핵심 요소는 Twilio 동사와 TwiML(Twilio Markup Language)입니다.

### <span id="Verbs"></span></a>Twilio 동사

API는 Twilio 동사를 활용합니다. 예를 들어 **\<Say\>** 동사는 Twilio에 통화 메시지를 음성으로 전달하도록 지시합니다.

다음은 Twilio 동사의 목록입니다. 기타 동사 및 기능에 대해서는 [Twilio Markup Language 설명서][Twilio Markup Language 설명서](영문)에서 알아보세요.

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

Twilio 동사, 특성 및 TwiML에 대한 자세한 내용은 [TwiML][Twilio Markup Language 설명서](영문)을 참조하세요. Twilio API에 대한 자세한 내용은 [Twilio API][Twilio API](영문)를 참조하세요.

## <span id="CreateAccount"></span></a>Twilio 계정 만들기

Twilio 계정을 사용할 준비가 되었다면 [Twilio 체험][Twilio 체험](영문)에서 등록하세요. 무료 계정으로 시작했다가 나중에 계정을 업그레이드할 수 있습니다.

Twilio 계정을 등록하면 계정 ID 및 인증 토큰을 받게 됩니다. 둘 다 Twilio API 통화를 하는 데 필요합니다. 계정에 대한 무단 액세스를 방지하려면 인증 토큰을 안전하게 유지하세요. 계정 ID 및 인증 토큰은 [Twilio 계정 페이지][Twilio 계정 페이지](영문)의 **ACCOUNT SID** 및 **AUTH TOKEN**에서 각기 확인할 수 있습니다.

## <span id="VerifyPhoneNumbers"></span></a>전화 번호 확인

Twilio에서 계정에 대한 여러 전화 번호를 인증해야 합니다. 예를 들어 아웃바운드 통화를 하려는 경우 Twilio에서 전화 번호를 아웃바운드 발신자 번호로 확인해야 합니다. 마찬가지로, SMS 메시지를 받을 전화 번호를 원하는 경우 Twilio에서 수신 전화 번호를 확인해야 합니다. 전화 번호를 확인하는 방법에 대한 자세한 내용은 [번호 관리][번호 관리](영문)를 참조하세요. 아래 코드 중 일부는 Twilio에서 확인해야 하는 전화 번호를 사용합니다.

응용 프로그램의 기존 번호를 사용하지 않고 Twilio 전화 번호를 구매할 수도 있습니다. Twilio 전화 번호를 구매하는 방법에 대해서는 [Twilio 전화 번호 도움말][Twilio 전화 번호 도움말](영문)을 참조하세요.

## <span id="create_app"></span></a>모바일 서비스 만들기

Twilio 사용 응용 프로그램을 호스트하는 모바일 서비스도 다른 모바일 서비스와 동일합니다. 모바일 서비스 사용자 지정 API 스크립트에서 참조하기 위해 Twilio node.js 라이브러리를 추가하면 됩니다. 최초 모바일 서비스 만들기에 대한 내용은 [모바일 서비스 시작][모바일 서비스 시작](영문)을 참조하십시오.

## <span id="VerifyPhoneNumbers"></span></a>Twilio Node.js 라이브러리를 사용하도록 모바일 서비스 구성

Twilio는 Node.js 라이브러리를 제공하며, 이 라이브러리는 Twilio의 여러 가지 요소를 래핑함으로써 Twilio REST API 및 Twilio 클라이언트를 간단하고 쉽게 조작해서 TwiML 응답을 생성하는 방법을 제공합니다.

모바일 서비스에서 Twilio node.js 라이브러리를 사용하려면 모바일 서비스 npm 모듈 지원을 사용해야 합니다. 이는 원본 제어에서 스크립트를 저장함으로써 사용할 수 있습니다. [원본 제어에 스크립트 저장][원본 제어에 스크립트 저장](영문) 자습서에는 모바일 서비스에서 처음으로 원본 제어를 설정하고 Git 리포지토리에 서버 스크립트를 저장하는 방법이 나와 있습니다.

모바일 서비스에 대해 원본 제어를 설정했으면 모바일 서비스 대시보드에서 구성 탭을 열고 Git URL을 찾아 복사합니다.

이 URL을 브라우저에 붙여넣고 리포지토리 이름을 */DebugConsole/index.html*으로 바꿉니다.

예를 들면 다음과 같이 변경합니다.

    https://twilioSample.scm.azure-mobile.net/twilioSample.git

변경:

    https://twilioSample.scm.azure-mobile.net/DebugConsole

메시지가 나타나면 서비스에 대해 원본 제어를 설정할 때 사용한 자격 증명을 입력합니다. 로그인하면 Azure 모바일 서비스 콘솔이 표시됩니다.

![모바일 서비스 콘솔][모바일 서비스 콘솔]

콘솔에서 디렉터리를 스크립트 폴더로 변경합니다.

    cd site\wwwroot\App_Data\config\scripts

api 폴더에서 다음 명령을 실행하는 Twilio 노드 모듈을 설치할 수 있습니다.

    npm install twilio

이제 사용자 지정 API 및 테이블 스크립트에서 Twilio 라이브러리를 참조하고 사용할 수 있습니다.

## <span id="howto_make_call"></span></a>방법: 발신 전화 걸기

다음 스크립트는 **makeCall** 함수를 사용하여 모바일 서비스에서 발신 전화를 시작하는 방법을 보여 줍니다. 또한 이 코드는 Twilio 제공 사이트를 사용하여 TwiML(Twilio Markup Language) 응답을 반환합니다. **From** 및 **To** 전화 번호의 값을 바꾸고, 코드를 실행하기 전에 Twilio 계정의 **From** 번호를 확인하세요.

    var twilio = require('twilio');

    exports.post = function(request, response) {

        var client = new twilio.RestClient('[ACCOUNT_SID]', 'AUTH_TOKEN');

        client.makeCall({
            to:'+16515556677', 
            from: '+14506667788',
            url: 'http://www.example.com/twiml.php' 

        }, function(err, responseData) {
            console.log(responseData.from); 
            response.send(200, '');
        });
    };

**client.makeCall** 함수에 전달된 매개 변수에 대한 자세한 내용은 [][]<http://www.twilio.com/docs/api/rest/making-calls></a>(영문)를 참조하십시오.

언급한 대로 이 코드는 Twilio 제공 사이트를 사용하여 TwiML 응답을 반환합니다. 이 사이트 대신 고유한 사이트를 사용하여 TwiML 응답을 제공할 수 있습니다. 자세한 내용은 [방법: 고유한 웹 사이트에서 TwiML 응답 제공][방법: 고유한 웹 사이트에서 TwiML 응답 제공]을 참조하세요.

## <span id="howto_send_sms"></span></a>방법: SMS 메시지 보내기

다음 코드는 **sendSms** 함수를 사용하여 SMS 메시지를 보내는 방법을 보여 줍니다. 평가판 계정이 SMS 메시지를 보낼 **From** 번호는 자동으로 입력됩니다. 코드를 실행하기 전에 Twilio 계정에 대해 **To** 번호를 확인해야 합니다.

    var twilio = require('twilio');

    exports.post = function(request, response) {

        var client = new twilio.RestClient('[ACCOUNT_SID]', 'AUTH_TOKEN');

        client.sendSms({
            to:'[]',
            from:'[]',
            body:'ahoy hoy! Testing Twilio and node.js'
        }, function(error, message) {

            // The "error" variable will contain error information, if any.
            // If the request was successful, this value will be "false"
            if (!error) {
                console.log('Success! The SID for this SMS message is: ' + message.sid);
                console.log('Message sent on: ' + message.dateCreated);
            }
            else {
                console.log('Oops! There was an error.');
            }
            response.send(200, { error : error } );
        });
    };

## <span id="howto_provide_twiml_responses"></span></a>방법: 고유한 웹 사이트에서 TwiML 응답 제공

응용 프로그램에서 Twilio API에 대한 호출을 시작하면(예: client.InitiateOutboundCall 메서드를 통해) Twilio에서 TwiML 응답을 반환해야 하는 URL로 요청을 보냅니다. 방법: 발신 전화 걸기에서는 Twilio 제공 URL인 <http://twimlets.com/message>를 사용하여 응답을 반환합니다.

<div class="dev-callout">
<b>참고</b>
<p>TwiML이 웹 서비스에 사용하도록 설계되었지만 브라우저에서도 TwiML을 볼 수 있습니다. 예를 들어 <a href="http://twimlets.com/message">twimlet_message_url</a>를 클릭하면 빈 &lt;Response&gt; 요소를 볼 수 있습니다. 또 다른 예로, <a href="http://twimlets.com/message?Message%5B0%5D=Hello%20World">twimlet_message_url_hello_world</a>를 클릭하면 &lt;Say&gt; 요소가 포함된 &lt;Response&gt; 요소를 볼 수 있습니다.</p>
</div>

Twilio 제공 URL을 사용하지 않고 HTTP 응답을 반환하는 고유한 URL 사이트를 만들 수 있습니다. HTTP 응답을 반환하는 사이트는 어떤 언어로든 만들 수 있습니다. 이 항목에서는 ASP.NET 제네릭 처리기에서 URL을 호스트한다고 가정합니다.

다음 스크립트에서는 호출 시 Hello World라고 말하는 TwiML 응답이 생성됩니다.

    var twilio = require('twilio');

    exports.post = function(request, response) {
        var resp = new twilio.TwimlResponse();
        resp.say({voice:'woman'}, 'ahoy hoy! Testing Twilio and node.js');
        response.set('Content-Type', 'text/xml');
        response.send(200, resp.toString());
    };

TwiML에 대한 자세한 내용은 [][1]<https://www.twilio.com/docs/api/twiml></a>(영문)을 참조하세요.

TwiML 응답을 제공하는 방법을 설정한 후에는 다음 코드 샘플에 표시된 것처럼 **client.makeCall** 메서드로 해당 URL을 전달할 수 있습니다.

    var twilio = require('twilio');

    exports.post = function(request, response) {

        var client = new twilio.RestClient('[ACCOUNT_SID]', 'AUTH_TOKEN');

        client.makeCall({
            to:'+16515556677', 
            from: '+14506667788',
            url: 'http://<your_mobile_service>.azure-mobile.net/api/makeCall' 

        }, function(err, responseData) {

            console.log(responseData.from);
            response.send(200, '');
        });
    };

[WACOM.INCLUDE [twilio_additional_services_and_next_steps](../includes/twilio_additional_services_and_next_steps.md)]

  [특가 제공]: http://ahoy.twilio.com/azure
  [Twilio 가격 책정]: http://www.twilio.com/pricing
  [Twilio API 라이브러리]: https://www.twilio.com/docs/libraries
  [.NET]: /ko-KR/develop/net/how-to-guides/twilio-voice-and-sms-service/
  [node.js]: /ko-KR/develop/nodejs/how-to-guides/twilio-voice-and-sms-service/
  [Java]: /ko-KR/develop/java/how-to-guides/twilio-voice-and-sms-service/
  [PHP]: /ko-KR/develop/php/how-to-guides/twilio-voice-and-sms-service/
  [Python]: /ko-KR/develop/python/how-to-guides/twilio-voice-and-sms-service/
  [Ruby]: /ko-KR/develop/ruby/how-to-guides/twilio-voice-and-sms-service/
  [Twilio Markup Language 설명서]: http://www.twilio.com/docs/api/twiml
  [Twilio API]: http://www.twilio.com/api
  [Twilio 체험]: https://www.twilio.com/try-twilio
  [Twilio 계정 페이지]: https://www.twilio.com/user/account
  [번호 관리]: https://www.twilio.com/user/account/phone-numbers/verified#
  [Twilio 전화 번호 도움말]: https://www.twilio.com/help/faq/phone-numbers
  [모바일 서비스 시작]: http://www.windowsazure.com/ko-KR/develop/mobile/tutorials/get-started/
  [원본 제어에 스크립트 저장]: http://www.windowsazure.com/ko-KR/develop/mobile/tutorials/store-scripts-in-source-control/
  [모바일 서비스 콘솔]: ./media/partner-twilio-mobile-services-how-to-use-voice-sms/twilio-kuduconsole.png
  []: http://www.twilio.com/docs/api/rest/making-calls
  [방법: 고유한 웹 사이트에서 TwiML 응답 제공]: #howto_provide_twiml_responses
  [1]: https://www.twilio.com/docs/api/twiml
