---
title: 음성 및 SMS에 Twilio 사용(Python) | Microsoft Docs
description: Azure에서 Twilio API 서비스를 사용하여 전화를 걸고 SMS 메시지를 보내는 방법에 대해 알아봅니다. 코드 샘플은 Python으로 작성되었습니다.
services: ''
documentationcenter: python
author: georgewallace
ms.assetid: 561bc75b-4ac4-40ba-bcba-48e901f27cc3
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 02/19/2015
ms.author: gwallace
ms.custom: devx-track-python
ms.openlocfilehash: 6d2f2b9cd145a2472ccc9ed24fd78f30107db710
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110463812"
---
# <a name="use-twilio-for-voice-and-sms-capabilities-in-python"></a>Python에서 음성 및 SMS 기능을 위해 Twilio를 사용하는 방법
이 문서에서는 Azure에서 Twilio API 서비스를 사용하여 일반 프로그래밍 작업을 수행하는 방법을 보여 줍니다. 이 문서의 시나리오는 전화를 걸고 SMS(Short Message Service) 메시지를 보내는 것을 포함합니다. 

애플리케이션에서 음성 및 SMS 사용 방법과 Twilio에 대한 자세한 내용은 [다음 단계](#NextSteps) 섹션을 참조하세요.

## <a name="what-is-twilio"></a><a id="WhatIs"></a>Twilio 정의
Twilio를 사용하면 개발자가 음성, VoIP(Voice over IP), 메시징을 애플리케이션에 포함할 수 있습니다. 개발자는 클라우드 기반 글로벌 환경에 필요한 모든 인프라를 가상화하고, Twilio API 플랫폼을 통해 이를 공개합니다. 덕분에 애플리케이션을 간단히 빌드하고 확장할 수 있습니다.

Twilio 구성 요소는 다음과 같습니다.

- **Twilio Voice**: 애플리케이션에서 전화를 걸고 받을 수 있습니다.
- **Twilio SMS**: 애플리케이션에서 문자 메시지를 보내고 받을 수 있습니다.
- **Twilio Client**: 전화, 태블릿, 브라우저에서 VoIP 통화를 할 수 있습니다. 실시간 통신을 위한 WebRTC 사양을 지원합니다.

## <a name="twilio-pricing-and-special-offers"></a><a id="Pricing"></a>Twilio 가격 책정 및 특별 제공
Azure 고객은 Twilio 계정을 업그레이드할 때 10달러의 Twilio 크레딧을 [특별 제공][special_offer]으로 받습니다. 이 크레딧은 모든 Twilio 사용에 적용할 수 있습니다. 10달러의 크레딧은 전화번호 및 메시지 또는 통화 대상의 위치에 따라 SMS 메시지를 최대 1,000개까지 보내거나 최대 1,000분의 인바운드 음성 통화 시간을 받을 수 있는 금액입니다.

Twilio는 종량제 서비스입니다. 설치 요금은 없으며 언제든 계정을 종료할 수 있습니다. [Twilio 가격 책정][twilio_pricing]에서 자세한 내용을 볼 수 있습니다.

## <a name="concepts"></a><a id="Concepts"></a>개념
Twilio API는 애플리케이션에 대한 음성 및 SMS 기능을 제공하는 RESTful API입니다. 클라이언트 라이브러리는 다양한 언어로 사용할 수 있습니다. 목록은 [Twilio API 라이브러리][twilio_libraries]를 참조하세요.

Twilio API의 핵심 요소는 Twilio 동사와 TwiML(Twilio Markup Language)입니다.

### <a name="twilio-verbs"></a><a id="Verbs"></a>Twilio 동사
API는 Twilio에 수행할 작업을 알려주는 다음과 같은 동사를 사용합니다.

* **&lt;Dial&gt;** : 발신자를 다른 전화에 연결합니다.
* **&lt;Gather&gt;** : 전화 키패드에 입력된 숫자를 수집합니다.
* **&lt;Hangup&gt;** : 통화를 끝냅니다.
* **&lt;Pause&gt;** : 지정된 시간(초) 동안 무음으로 대기합니다.
* **&lt;Play&gt;** : 오디오 파일을 재생합니다.
* **&lt;Queue&gt;** : 호출자 큐에 추가합니다.
* **&lt;Record&gt;** : 발신자의 음성을 녹음하고 녹음이 포함된 파일의 URL을 반환합니다.
* **&lt;Redirect&gt;** : 통화 또는 SMS에 대한 제어를 다른 URL의 TwiML로 전송합니다.
* **&lt;Reject&gt;** : 요금을 청구하지 않고 Twilio 번호의 수신 전화를 거부합니다.
* **&lt;Say&gt;** : 텍스트를 통화에 사용되는 음성으로 변환합니다.
* **&lt;Sms&gt;** : SMS 메시지를 보냅니다.

기타 동사 및 기능에 대해서는 [Twilio Markup Language 설명서][twiml]에서 알아보세요.

### <a name="twiml"></a><a id="TwiML"></a>TwiML
TwiML은 Twilio에 통화 또는 SMS 메시지 처리 방법을 알려 주는 Twilio 동사를 사용하는 XML 기반 명령어 집합입니다.

예를 들어, 다음 TwiML은 **Hello World** 텍스트를 음성으로 변환합니다.

```xml
<?xml version="1.0" encoding="UTF-8" ?>
  <Response>
    <Say>Hello World</Say>
  </Response>
```

애플리케이션에서 [Twilio API][twilio_api]를 호출할 때, API 매개 변수 중 하나는 TwiML 응답을 반환하는 URL입니다. 개발 목적의 경우, Twilio 제공 URL을 사용하여 애플리케이션에 사용되는 TwiML 응답을 제공할 수 있습니다. 또한 TwiML 응답을 생성하는 고유한 URL을 호스트할 수도 있고, 또는 `TwiMLResponse` 개체를 사용할 수도 있습니다.

## <a name="create-a-twilio-account"></a><a id="CreateAccount"></a>Twilio 계정 만들기
Twilio 계정을 사용할 준비가 되었다면 [Twilio 체험][try_twilio](영문)에서 등록하십시오. 무료 계정으로 시작했다가 나중에 계정을 업그레이드할 수 있습니다.

Twilio 계정을 등록하면 계정 보안 ID(SID) 및 인증 토큰을 받게 됩니다. Twilio API를 호출하려면 둘 다 필요합니다. 계정에 대한 무단 액세스를 방지하려면 인증 토큰을 안전하게 유지하십시오. 계정 SID 및 인증 토큰은 [Twilio 콘솔][twilio_console]의 **ACCOUNT SID** 및 **AUTH TOKEN** 필드에서 확인할 수 있습니다.

## <a name="create-a-python-application"></a><a id="create_app"></a>Python 애플리케이션 만들기
Twilio 서비스를 사용하여 Azure에서 실행되고 있는 Python 애플리케이션은 Twilio 서비스를 사용하는 다른 Python 애플리케이션과 차이가 없습니다. Twilio 서비스가 REST 기반이고 여러 가지 방법으로 Python에서 호출될 수 있기는 하지만, 이 문서에서는 [GitHub의 Python용 Twilio 라이브러리][twilio_python]와 Twilio 서비스를 사용하는 방법을 집중적으로 설명합니다. 이 라이브러리 사용에 대한 자세한 내용은 [Twilio Python 라이브러리 설명서][twilio_lib_docs]를 참조하세요.

먼저 새 Python 웹 애플리케이션의 호스트 역할을 할 [새 Azure Linux 가상 머신을 설정][azure_vm_setup]합니다. 가상 머신이 실행되면 공개 포트에 애플리케이션을 공개해야 합니다.

수신 규칙을 추가하려면 다음을 수행합니다.
  1. [네트워크 보안 그룹][azure_nsg] 페이지로 이동합니다.
  2. 가상 머신과 일치하는 네트워크 보안 그룹을 선택합니다.
  3. **포트 80** 에 대한 **발신 규칙** 을 추가합니다. 모든 주소에서의 수신 전화를 허용해야 합니다.

DNS 이름 레이블을 설정하려면 다음을 수행합니다.
  1. [공용 IP 주소][azure_ips] 페이지로 이동합니다.
  2. 가상 머신과 일치하는 공용 IP를 선택합니다.
  3. **구성** 섹션에서 **DNS 이름 레이블** 을 설정합니다. 이 예시의 경우 *\<your-domain-label\>.centralus.cloudapp.azure.com* 같이 표시됩니다.

SSH를 통해 가상 머신에 연결할 수 있으면 원하는 웹 프레임워크를 설치할 수 있습니다. Python에서 가장 잘 알려진 두 가지는 [Flask](http://flask.pocoo.org/)와 [Django](https://www.djangoproject.com)입니다. `pip install` 명령을 실행하여 둘 중 하나를 설치할 수 있습니다.

포트 80에서만 트래픽을 허용하도록 가상 머신을 구성했다는 점에 유의하세요. 따라서 이 포트를 사용하도록 애플리케이션을 구성해야 합니다.

## <a name="configure-your-application-to-use-the-twilio-library"></a><a id="configure_app"></a>Twilio 라이브러리를 사용하도록 애플리케이션 구성
다음과 같은 두 가지 방법으로 Python용 Twilio 라이브러리를 사용하도록 애플리케이션을 구성할 수 있습니다.

* 다음 명령을 사용하여 Python용 Twilio 라이브러리를 Pip 패키지로 설치합니다.
   
  `$ pip install twilio`

* [GitHub에서 Python용 Twilio 라이브러리][twilio_python]를 다운로드하고 다음과 같이 설치합니다.

  `$ python setup.py install`

Python용 Twilio 라이브러리를 설치한 후, Python 파일로 가져오기 할 수 있습니다.

`import twilio`

자세한 내용은 [Twilio GitHub 추가 정보](https://github.com/twilio/twilio-python/blob/master/README.md)를 참조하세요.

## <a name="make-an-outgoing-call"></a><a id="howto_make_call"></a>발신 전화 걸기
다음은 발신 전화를 거는 방법을 보여 줍니다. 이 코드는 Twilio 제공 사이트를 사용하여 TwiML 응답을 반환합니다. `from_number` 및 `to_number` 전화 번호에 대한 값을 대체합니다. 코드를 실행하기 전에 Twilio 계정용 `from_number` 전화번호를 확인해야 합니다.

```python
from urllib.parse import urlencode

# Import the Twilio Python Client.
from twilio.rest import TwilioRestClient

# Set your account ID and authentication token.
account_sid = "your_twilio_account_sid"
auth_token = "your_twilio_authentication_token"

# The number of the phone initiating the call.
# This should either be a Twilio number or a number that you've verified.
from_number = "NNNNNNNNNNN"

# The number of the phone receiving call.
to_number = "NNNNNNNNNNN"

# Use the Twilio-provided site for the TwiML response.
url = "https://twimlets.com/message?"

# The phone message text.
message = "Hello world."

# Initialize the Twilio client.
client = TwilioRestClient(account_sid, auth_token)

# Make the call.
call = client.calls.create(to=to_number,
                           from_=from_number,
                           url=url + urlencode({'Message': message}))
print(call.sid)
```

> [!IMPORTANT]
> 전화 번호는 '+' 및 국가 번호의 형식이어야 합니다. 예는 `+16175551212`(E. 164 형식)입니다. Twilio는 `(415) 555-1212` 또는 `415-555-1212`와 같이 형식이 지정되지 않은 미국 번호도 허용합니다.

이 코드는 Twilio 제공 사이트를 사용하여 TwiML 응답을 반환합니다. 이 사이트 대신 고유한 사이트를 사용하여 TwiML 응답을 제공할 수 있습니다. 자세한 내용은 [고유 웹 사이트에서 TwiML 응답 제공](#howto_provide_twiml_responses)을 참조하세요.

## <a name="send-an-sms-message"></a><a id="howto_send_sms"></a>SMS 메시지 보내기
다음은 `TwilioRestClient` 클래스를 사용하여 SMS 메시지를 보내는 방법을 보여 줍니다. Twilio은 SMS 메시지를 보낼 평가판 계정용 `from_number` 번호를 제공합니다. 코드를 실행하기 전에 Twilio 계정용 `to_number` 번호를 확인해야 합니다.

```python
# Import the Twilio Python Client.
from twilio.rest import TwilioRestClient

# Set your account ID and authentication token.
account_sid = "your_twilio_account_sid"
auth_token = "your_twilio_authentication_token"

from_number = "NNNNNNNNNNN"  # With a trial account, texts can only be sent from your Twilio number.
to_number = "NNNNNNNNNNN"
message = "Hello world."

# Initialize the Twilio client.
client = TwilioRestClient(account_sid, auth_token)

# Send the SMS message.
message = client.messages.create(to=to_number,
                                    from_=from_number,
                                    body=message)
```

## <a name="provide-twiml-responses-from-your-own-website"></a><a id="howto_provide_twiml_responses"></a>고유 웹 사이트에서 TwiML 응답 제공
애플리케이션에서 Twilio API로 호출을 시작하면 Twilio에서 TwiML 응답을 반환해야 하는 URL로 요청을 보냅니다. 위의 예시에서는 Twilio 제공 URL [https://twimlets.com/message][twimlet_message_url]를 사용합니다. 

> [!NOTE]
> TwiML은 Twilio에서 사용하도록 설계되었지만 브라우저에서도 TwiML을 볼 수 있습니다. 예를 들어, [https://twimlets.com/message][twimlet_message_url]을 선택하여 빈 `<Response>` 요소를 확인합니다. 또 다른 예로, [https://twimlets.com/message?Message%5B0%5D=Hello%20World][twimlet_message_url_hello_world]을 선택하여 `<Say>` 요소가 포함된 `<Response>` 요소를 확인합니다.

Twilio 제공 URL을 사용하지 않고 HTTP 응답을 반환하는 고유한 사이트를 만들 수 있습니다. XML 응답을 반환하는 사이트는 어떤 언어로든 만들 수 있습니다. 이 문서에서는 Python을 사용하여 TwiML를 만드는 것으로 가정합니다.

다음 예제에서는 호출 시 **Hello World** 라고 말하는 TwiML 응답이 출력됩니다.

Flask 사용:

```python
from flask import Response
@app.route("/")
def hello():
    xml = '<Response><Say>Hello world.</Say></Response>'
    return Response(xml, mimetype='text/xml')
```

Django 사용:

```python
from django.http import HttpResponse
def hello(request):
    xml = '<Response><Say>Hello world.</Say></Response>'
    return HttpResponse(xml, content_type='text/xml')
```

위의 예시와 같이 TwiML 응답은 단지 XML 문서입니다. Python용 Twilio 라이브러리에는 TwiML을 자동으로 생성하는 클래스가 포함되어 있습니다. 아래 예시에서는 위에 표시된 것과 동일한 응답을 생성하지만 Python용 Twilio 라이브러리의 `twiml` 모듈을 사용합니다.

```python
from twilio import twiml

response = twiml.Response()
response.say("Hello world.")
print(str(response))
```

TwiML에 대한 자세한 내용은 [TwiML 참조][twiml_reference]를 참조하세요.

Python 애플리케이션이 TwiML 응답을 제공하도록 설정된 후, 애플리케이션의 URL을 `client.calls.create` 메서드에 전달되는 URL로 사용합니다. 예를 들어, *MyTwiML* 이라는 웹 애플리케이션이 Azure 호스팅 서비스에 배포되어 있으면 다음 예시에 표시된 것처럼 URL을 웹후크로 사용할 수 있습니다.

```python
from twilio.rest import TwilioRestClient

account_sid = "your_twilio_account_sid"
auth_token = "your_twilio_authentication_token"
from_number = "NNNNNNNNNNN"
to_number = "NNNNNNNNNNN"
url = "http://your-domain-label.centralus.cloudapp.azure.com/MyTwiML/"

# Initialize the Twilio Client.
client = TwilioRestClient(account_sid, auth_token)

# Make the call.
call = client.calls.create(to=to_number,
                           from_=from_number,
                           url=url)
print(call.sid)
```

## <a name="use-additional-twilio-services"></a><a id="AdditionalServices"></a>추가 Twilio 서비스 사용
여기에서 보여 준 예시뿐만 아니라, Twilio는 Azure 애플리케이션에서 Twilio 기능을 활용할 수 있는 웹 기반 API를 제공합니다. 자세한 내용은 [Twilio API 설명서][twilio_api]를 참조하세요.

## <a name="next-steps"></a><a id="NextSteps"></a>다음 단계
Twilio 서비스에 관한 기본적인 사항들을 익혔으며 자세한 내용을 보려면 다음 링크를 따라가십시오.

* [Twilio 보안 지침][twilio_security_guidelines]
* [Twilio 사용 방법 가이드 및 예시 코드][twilio_howtos]
* [Twilio 빠른 시작 자습서][twilio_quickstarts]
* [Twilio on GitHub][twilio_on_github]
* [Twilio 지원 문의][twilio_support]

[special_offer]: https://ahoy.twilio.com/azure
[twilio_python]: https://github.com/twilio/twilio-python
[twilio_lib_docs]: https://www.twilio.com/docs/libraries/python
[twilio_github_readme]: https://github.com/twilio/twilio-python/blob/master/README.md

[twimlet_message_url]: https://twimlets.com/message
[twimlet_message_url_hello_world]: https://twimlets.com/message?Message%5B0%5D=Hello%20World
[twiml_reference]: https://www.twilio.com/docs/api/twiml
[twilio_pricing]: https://www.twilio.com/pricing

[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api]: https://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_console]:  https://www.twilio.com/console
[twilio_security_guidelines]: https://www.twilio.com/docs/security
[twilio_howtos]: https://www.twilio.com/docs/all
[twilio_on_github]: https://github.com/twilio
[twilio_support]: https://www.twilio.com/help/contact
[twilio_quickstarts]: https://www.twilio.com/docs/quickstart
[azure_ips]: ./virtual-network/virtual-network-public-ip-address.md
[azure_vm_setup]: ./virtual-machines/linux/quick-create-portal.md
[azure_nsg]: ./virtual-network/manage-network-security-group.md