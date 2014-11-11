<properties linkid="develop-ruby-how-to-twilio-sms-voice-service" urlDisplayName="Twilio Voice/SMS Service" pageTitle="How to Use Twilio for Voice and SMS (Ruby) - Azure" metaKeywords="Azure Ruby Twilio, Azure phone calls, Azure phone calls, Azure twilio, Azure SMS, Azure SMS, Azure voice calls, azure voice calls, Azure text messages, Azure text messages" description="Learn how to make a phone call and send a SMS message with the Twilio API service on Azure. Code samples written in Ruby." metaCanonical="" services="" documentationCenter="Ruby" title="How to Use Twilio for Voice and SMS Capabilities in PHP" authors="MicrosoftHelp@twilio.com" solutions="" manager="" editor="" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="ruby" ms.topic="article" ms.date="01/01/1900" ms.author="MicrosoftHelp@twilio.com" />

# Ruby에서 음성 및 SMS 기능을 위해 Twilio를 사용하는 방법

이 가이드에서는 Azure에서 Twilio API 서비스로 일반 프로그래밍 작업을 수행하는 방법을 보여 줍니다. 이 문서의 시나리오에서는 전화 통화를 걸고 SMS(Short Message Service) 메시지를 보냅니다. 응용 프로그램에서 음성 및 SMS 사용 방법과 Twilio에 대한 자세한 내용은 [다음 단계][다음 단계] 섹션을 참조하십시오.

## 목차

-   [Twilio 정의][Twilio 정의]
-   [Twilio 가격 책정][Twilio 가격 책정]
-   [개념][개념]
-   [Twilio 계정 만들기][Twilio 계정 만들기]
-   [Ruby Sinatra 응용 프로그램 만들기][Ruby Sinatra 응용 프로그램 만들기]
-   [Twilio 라이브러리를 사용하도록 응용 프로그램 구성][Twilio 라이브러리를 사용하도록 응용 프로그램 구성]
-   [방법: 발신 전화 걸기][방법: 발신 전화 걸기]
-   [방법: SMS 메시지 받기][방법: SMS 메시지 받기]
-   [방법: 추가 Twilio 서비스][방법: 추가 Twilio 서비스]
-   [다음 단계][다음 단계]

## <span id="WhatIs"></span></a>Twilio 정의

Twilio는 기존 웹 언어와 기술을 사용하여 음성 및 SMS 응용 프로그램을 빌드할 수 있게 해주는 전화 통신 웹 서비스 API입니다. Twilio는 타사 서비스입니다(Azure 기능 또는 Microsoft 제품 아님).

**Twilio 음성**을 통해 응용 프로그램에서 전화 통화를 걸고 받을 수 있습니다. **Twilio SMS**를 사용하면 응용 프로그램에서 SMS 메시지를 작성하고 받을 수 있습니다. **Twilio 클라이언트**를 사용하면 응용 프로그램에서 모바일 연결을 비롯한 기존 인터넷 연결을 통해 음성 통신을 사용할 수 있습니다.

## <span id="Pricing"></span></a>Twilio 가격 책정 및 특별 제공

Twilio 가격 책정 정보는 [Twilio 가격 책정][1]에서 확인할 수 있습니다. Azure 고객은 문자 1000개 또는 인바운드 1000분의 무료 크레딧을 제공하는 [특가 제공][특가 제공]을 받을 수 있습니다. 이 제공에 등록하거나 추가 정보를 얻으려면 [][특가 제공]<http://ahoy.twilio.com/azure></a>를 방문하십시오.

## <span id="Concepts"></span></a>개념

Twilio API는 응용 프로그램에 대한 음성 및 SMS 기능을 제공하는 RESTful API입니다. 클라이언트 라이브러리는 다양한 언어로 사용할 수 있습니다. 목록에 대해서는 [Twilio API 라이브러리][Twilio API 라이브러리](영문)를 참조하세요.

### <span id="TwiML"></span></a>TwiML

TwiML은 Twilio에 통화 또는 SMS 처리 방법을 알려 주는 XML 기반 명령 집합입니다.

다음 예제 TwiML은 **Hello World** 텍스트를 음성으로 변환합니다.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

모든 TwiML 문서는 `<Response>`를 루트 요소로 포함하고 있습니다. 거기서 Twilio 동사를 사용하여 응용 프로그램의 동작을 정의합니다.

### <span id="Verbs"></span></a>TwiML 동사

Twilio 동사는 Twilio에 **수행할 작업**을 알려 주는 XML 태그입니다. 예를 들어 **\<Say\>** 동사는 Twilio에 통화 메시지를 음성으로 전달하도록 지시합니다.

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

Twilio 동사, 특성 및 TwiML에 대한 자세한 내용은 [TwiML][TwiML](영문)을 참조하세요. Twilio API에 대한 자세한 내용은 [Twilio API][Twilio API](영문)를 참조하세요.

## <span id="CreateAccount"></span></a>Twilio 계정 만들기

Twilio 계정을 사용할 준비가 되었다면 [Twilio 체험][Twilio 체험](영문)에서 등록하세요. 무료 계정으로 시작했다가 나중에 계정을 업그레이드할 수 있습니다.

Twilio 계정을 등록할 때 응용 프로그램의 무료 전화 번호를 받습니다. 계정 SID 및 인증 토큰도 받게 됩니다. 둘 다 Twilio API 통화를 하는 데 필요합니다. 계정에 대한 무단 액세스를 방지하려면 인증 토큰을 안전하게 유지하세요. 계정 SID 및 인증 토큰은 [Twilio 계정 페이지][Twilio 계정 페이지](영문)의 **ACCOUNT SID** 및 **AUTH TOKEN** 필드에서 각기 확인할 수 있습니다.

### <span id="VerifyPhoneNumbers"></span></a>전화 번호 확인

Twilio에서 제공한 번호 외에 응용 프로그램에서 사용하기 위해 제어하는 번호(즉, 휴대폰 또는 집 전화 번호)도 확인할 수 있습니다.

전화 번호를 확인하는 방법에 대한 자세한 내용은 [번호 관리][번호 관리](영문)를 참조하십시오.

## <span id="create_app"></span></a>Ruby 응용 프로그램 만들기

Twilio 서비스를 사용하고 Azure에서 실행되고 있는 Ruby 응용 프로그램은 Twilio 서비스를 사용하는 다른 Ruby 응용 프로그램과 차이가 없습니다. Twilio 서비스가 RESTful이고 여러 가지 방법으로 Ruby에서 호출될 수 있기는 하지만, 이 문서에서는 [Ruby용 Twilio 도우미 라이브러리][Ruby용 Twilio 도우미 라이브러리](영문)와 Twilio 서비스를 사용하는 방법을 집중적으로 설명합니다.

먼저, 새 Ruby 웹 응용 프로그램의 호스트 역할을 할 [새 Azure Linux VM을 설정합니다][새 Azure Linux VM을 설정합니다](영문). Rails 앱을 만드는 것과 관련된 단계는 무시하고 VM 설정만 수행하십시오. 외부 포트 80과 내부 포트 5000으로 끝점을 만들어야 합니다.

아래 예제에서는 매우 간단한 Ruby용 웹 프레임워크인 [Sinatra][Sinatra]를 사용합니다. 그러나 Ruby on Rails를 비롯한 다른 웹 프레임워크와 함께 Ruby용 Twilio 도우미 라이브러리를 사용할 수도 있습니다.

새 VM에 SSH를 설치하고 새 앱용 디렉터리를 만듭니다. 해당 디렉터리 내에 Gemfile이라는 파일을 만들고 다음 코드를 이 파일에 복사합니다.

    source 'https://rubygems.org'
    gem 'sinatra'
    gem 'thin'

명령줄에서 `bundle install`을 실행합니다. 그러면 위의 종속성이 설치됩니다. 이제 `web.rb`라는 파일을 만듭니다. 이 파일은 웹 앱 코드가 있는 곳입니다. 다음 코드를 이 파일에 붙여넣습니다.

    require 'sinatra'

    get '/' do
        "Hello Monkey!"
    end

이때 `ruby web.rb -p 5000` 명령을 실행할 수 있어야 합니다. 그러면 포트 5000에서 소형 웹 서버가 스핀업합니다. 브라우저에서 Azure VM에 대해 설정한 URL을 방문하여 이 앱으로 이동할 수 있어야 합니다. 브라우저에서 웹 앱에 연결할 수 있으면 Twilio 앱 빌드를 시작할 준비가 된 것입니다.

## <span id="configure_app"></span></a>Twilio를 사용하도록 응용 프로그램 구성

다음 줄을 포함하도록 `Gemfile`을 업데이트하여 Twilio 라이브러리를 사용하도록 웹 앱을 구성할 수 있습니다.

    gem 'twilio-ruby'

명령줄에서 `bundle install`을 실행합니다. 이제 `web.rb`를 열고 맨 위에 다음 줄을 포함합니다.

    require 'twilio-ruby'

이제 웹 앱에서 Ruby용 Twilio 도우미 라이브러리를 사용할 준비가 모두 완료되었습니다.

## <span id="howto_make_call"></span></a>방법: 발신 전화 걸기

다음은 발신 전화를 거는 방법을 보여 줍니다. 핵심 개념에는 Ruby용 Twilio 도우미 라이브러리를 사용하여 REST API 호출을 만드는 작업과 TwiML 렌더링 작업이 포함됩니다. **From** 및 **To** 전화 번호의 값을 바꾸고, 코드를 실행하기 전에 Twilio 계정의 **From** 번호를 확인하세요.

다음 함수를 `web.md`에 추가합니다.

    # Set your account ID and authentication token.
    sid = "your_twilio_account_sid";
    token = "your_twilio_authentication_token";

    # The number of the phone initiating the the call.
    # This should either be a Twilio number or a number that you've verified
    from = "NNNNNNNNNNN";

    # The number of the phone receiving call.
    to = "NNNNNNNNNNN";

    # Use the Twilio-provided site for the TwiML response.
    url = "http://yourdomain.cloudapp.net/voice_url";
      
    get '/make_call' do
      # Create the call client.
      client = Twilio::REST::Client.new(sid, token);
      
      # Make the call
      client.account.calls.create(to: to, from: from, url: url)
    end

    post '/voice_url' do
      "<Response>
         <Say>Hello Monkey!</Say>
       </Response>"
    end

브라우저에서 `http://yourdomain.cloudapp.net/make_call`을 열면 Twilio API에 대한 호출이 트리거되어 전화 통화가 걸립니다. `client.account.calls.create`의 첫 번째 두 매개 변수 즉, 통화의 `from`(걸려오는) 번호 및 `to`(걸리는 대상) 번호는 따로 설명이 필요 없습니다.

세 번째 매개 변수(`url`)는 전화가 연결된 후 수행할 작업에 대한 지침을 받기 위해 Twilio가 요청하는 URL입니다. 이 경우 간단한 TwiML 문서를 반환하고 `<Say>` 동사를 사용하여 텍스트를 음성으로 변환하고 전화를 받는 사람에게 "Hello Monkey"라고 말하는 URL(`http://yourdomain.cloudapp.net`)을 설정했습니다.

## <span id="howto_recieve_sms"></span></a>방법: SMS 메시지 받기

앞의 예제에서는 **발신** 전화 통화를 시작했습니다. 이번에는 등록하는 동안 Twilio가 제공한 전화 번호를 사용하여 **수신** SMS 메시지를 처리합니다.

먼저, [Twilio 대시보드][Twilio 계정 페이지](영문)에 로그인합니다. 위쪽 탐색 표시줄에서 "Numbers(번호)"를 클릭한 후 제공받은 Twilio 번호를 클릭합니다. 구성할 수 있는 URL 두 개가 표시됩니다. 하나는 음성 요청 URL이고 다른 하나는 SMS 요청 URL입니다. 이러한 URL은 전화 통화가 걸리거나 SMS가 사용자의 번호로 전송될 때마다 Twilio가 호출하는 URL입니다. 이러한 URL을 "웹 후크"라고도 합니다.

수신 SMS 메시지를 처리할 것이므로, URL을 `http://yourdomain.cloudapp.net/sms_url`로 업데이트합니다. 계속해서 페이지 아래쪽에 있는 Save Changes(변경 내용 저장)를 클릭합니다. 이제 다시 `web.rb`에서 이를 처리하기 위한 응용 프로그램을 프로그래밍합니다.

    post '/sms_url' do
      "<Response>
         <Sms>Hey, thanks for the ping! Twilio and Azure rock!</Sms>
       </Response>"
    end

변경한 후 웹 앱을 다시 시작해야 합니다. 이제 휴대폰을 사용하여 SMS를 Twilio 번호로 보냅니다. 즉시 "Hey, thanks for the ping! Twilio and Azure rock!"이라는 SMS 응답을 받게 됩니다.

## <span id="additional_services"></span></a>방법: 추가 Twilio 서비스 사용

여기에서 보여 준 예뿐만 아니라 Twilio는 Azure 응용 프로그램에서 Twilio 기능을 활용할 수 있는 웹 기반 API를 제공합니다. 자세한 내용은 [Twilio API 설명서][Twilio API](영문)를 참조하세요.

### <span id="NextSteps"></span></a>다음 단계

Twilio 서비스에 관한 기본적인 사항들을 익혔으며 자세한 내용을 보려면 다음 링크를 따라가세요.

-   [Twilio 보안 지침(영문)][Twilio 보안 지침(영문)]
-   [Twilio 사용 방법 및 예제 코드(영문)][Twilio 사용 방법 및 예제 코드(영문)]
-   [Twilio 빠른 시작 자습서(영문)][Twilio 빠른 시작 자습서(영문)]
-   [Twilio on GitHub(영문)][Twilio on GitHub(영문)]
-   [Twilio 지원 문의(영문)][Twilio 지원 문의(영문)]

  [다음 단계]: #NextSteps
  [Twilio 정의]: #WhatIs
  [Twilio 가격 책정]: #Pricing
  [개념]: #Concepts
  [Twilio 계정 만들기]: #CreateAccount
  [Ruby Sinatra 응용 프로그램 만들기]: #create_app
  [Twilio 라이브러리를 사용하도록 응용 프로그램 구성]: #configure_app
  [방법: 발신 전화 걸기]: #howto_make_call
  [방법: SMS 메시지 받기]: #howto_recieve_sms
  [방법: 추가 Twilio 서비스]: #additional_services
  [1]: http://www.twilio.com/pricing
  [특가 제공]: http://ahoy.twilio.com/azure
  [Twilio API 라이브러리]: https://www.twilio.com/docs/libraries
  [TwiML]: http://www.twilio.com/docs/api/twiml
  [Twilio API]: http://www.twilio.com/api
  [Twilio 체험]: https://www.twilio.com/try-twilio
  [Twilio 계정 페이지]: https://www.twilio.com/user/account
  [번호 관리]: https://www.twilio.com/user/account/phone-numbers/verified#
  [Ruby용 Twilio 도우미 라이브러리]: https://www.twilio.com/docs/ruby/install
  [새 Azure Linux VM을 설정합니다]: http://www.windowsazure.com/ko-KR/develop/ruby/tutorials/web-app-with-linux-vm/
  [Sinatra]: http://www.sinatrarb.com/
