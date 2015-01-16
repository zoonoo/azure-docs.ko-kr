<properties title="How to make a phone call from Twilio (PHP) - Azure" pageTitle="Twilio에서 전화를 거는 방법(PHP) - Azure" metaKeywords="Azure PHP Twilio, Azure Twilio, Azure 전화 통화, Azure twilio, Azure SMS, Azure SMS, Azure 음성 통화, azure 음성 통화, Azure 문자 메시지, Azure 문자 메시지, PHP twilio Azure" description="Azure에서 Twilio API 서비스를 사용하여 전화를 걸고 SMS 메시지를 보내는 방법에 대해 알아봅니다. 샘플은 PHP 응용 프로그램용입니다." documentationCenter="PHP" services="" solutions="" videoId="" scriptId="" authors="MicrosoftHelp@twilio.com; robmcm" manager="twilio" editor="mollybos" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="MicrosoftHelp@twilio.com; robmcm" />

# Azure의 PHP 응용 프로그램에서 Twilio를 사용하여 전화를 거는 방법 

다음 예제는 Azure에 호스트된 PHP 웹 페이지에서 Twilio를 사용하여 전화를 거는 방법을 보여 줍니다. 다음 스크린샷에 표시된 것처럼 응용 프로그램에서 사용자에게 전화 통화 값을 묻습니다.

![Azure Call Form Using Twilio and PHP][twilio_php]

이 항목에서 코드를 사용하려면 다음을 수행해야 합니다.

1. Twilio 계정 및 인증 토큰을 받습니다. Twilio를 시작하려면 [http://www.twilio.com/pricing][twilio_pricing](영문)에서 가격을 평가합니다. [https://www.twilio.com/try-twilio][try_twilio](영문)에서 체험 계정을 등록할 수 있습니다. Twilio에서 제공하는 API에 대한 내용은 [http://www.twilio.com/api][twilio_api](영문)를 참조하세요.
2. Twilio에서 아웃바운드 발신자 번호로 전화 번호를 확인합니다. 전화 번호를 확인하는 방법에 대한 내용은 [https://www.twilio.com/user/account/phone-numbers/verified#][verify_phone](영문)을 참조하세요. 기존 번호를 사용하지 않고 Twilio 전화 번호를 구매할 수도 있습니다.
이 예제에서는 **callform.php**(나중에 설명)의 **From** 값으로 확인된 전화 번호를 사용합니다.
3. PHP용 Twilio 라이브러리를 가져옵니다. Github([https://github.com/twilio/twilio-php][twilio_php_github])에서 다운로드하거나 PEAR 패키지로 설치할 수 있습니다. 자세한 내용은 [https://github.com/twilio/twilio-php/blob/master/README.md][twilio_github_readme](영문)를 참조하세요.
4. PHP용 Azure SDK를 설치합니다. SDK에 대한 개요 및 설치 지침은 [PHP용 Azure SDK 설정][setup_php_sdk](영문)을 참조하세요.

## 전화 걸기 웹 양식 만들기

다음 HTML 코드는 전화를 걸기 위해 사용자 데이터를 검색하는 웹 페이지(**callform.html**)를 빌드하는 방법을 보여 줍니다.

    <html>
	<head>
		<title>Automated call form</title>
	</head>
	<body>
	<h1>Automated Call Form</h1>
 	<p>Fill in all fields and click <b>Make this call</b>.</p>
  	<form action="makecall.php" method="post">
   	<table>
     	<tr>
       		<td>To:</td>
       		<td><input type="text" size=50 name="callTo" value=""></td>
     	</tr>
     	<tr>
       		<td>From:</td>
       		<td><input type="text" size=50 name="callFrom" value=""></td>
     	</tr>
     	<tr>
       		<td>Call message:</td>
       		<td><input type="text" size=100 name="callText" value="Hello. This is the call text. Good bye." /></td>
     	</tr>
     	<tr>
       		<td colspan=2><input type="submit" value="Make this call"></td>
     	</tr>
   	</table>
 	</form>
 	<br/>
	</body>
	</html>

## 코드를 만들어 전화 걸기
다음 코드는 사용자가 **callform.html**에 의해 표시된 양식을 제출할 때 호출되는 웹 페이지(**makecall.php**)를 빌드하는 방법을 보여 줍니다. 아래 표시된 코드는 통화 메시지를 만들고 통화를 생성합니다. (아래 코드에서 **$sid** 및 **$token**에 할당된 자리 표시자 값 대신 Twilio 계정 및 인증 토큰을 사용하세요.)

    <html>
	<head><title>Making call...</title></head>
	<body>
	<p>Your call is being made.</p>

	<?php
	require_once 'Services/Twilio.php';

	$sid = "your_account_sid";
	$token = "your_authentication_token";

	$from_number = $_POST['callFrom']; // Calls must be made from a registered Twilio number.
	$to_number = $_POST['callTo'];
	$message = $_POST['callText'];

	$client = new Services_Twilio($sid, $token, "2010-04-01");

	$call = $client->account->calls->create(
		$from_number, 
		$to_number,
  		'http://twimlets.com/message?Message='.urlencode($message)
	);

	echo "Call status: ".$call->status."<br />";
	echo "URI resource: ".$call->uri."<br />";
	?>
	</body>
	</html>

전화 걸기와 함께, **makecall.php**는 몇몇 통화 메타데이터(예는 아래 스크린샷에 표시됨)를 표시합니다. 통화 메타데이터에 대한 자세한 내용은 [https://www.twilio.com/docs/api/rest/call#instance-properties][twilio_call_properties](영문)를 참조하세요.

![Azure Call Response Using Twilio and PHP][twilio_php_response]

## 응용 프로그램 실행
다음 단계는 Azure 웹 사이트에 응용 프로그램을 배포하는 것입니다. 다음 문서에는 웹 사이트 생성 및 Git, FTP 또는 WebMatrix를 사용하여 코드 배포에 대한 정보가 들어 있습니다(각 문서의 일부 정보는 관련 없음).

* [PHP-MySQL Azure 웹 사이트 만들기 및 Git를 사용하여 배포(영문)][website-git]
* [PHP-MySQL Azure 웹 사이트 만들기 및 FTP를 사용하여 배포][website-ftp]
* [PHP-MySQL Azure 웹 사이트 만들기 및 WebMatrix를 사용하여 배포(영문)][website-webmatrix]

## 다음 단계
이 코드는 Azure의 PHP에서 Twilio를 사용하는 기본 기능을 보여 줍니다. Azure를 프로덕션에 배포하기 전에 더 많은 오류 처리 또는 기타 기능을 추가할 수 있습니다. 예를 들면 다음과 같습니다.

* 웹 양식을 사용하는 대신, Azure 저장소 Blob 또는 SQL 데이터베이스를 사용하여 전화 번호 및 통화 텍스트를 저장할 수 있습니다. PHP에서 Azure 저장소 Blob 사용에 대한 내용은 [PHP 응용 프로그램에서 Azure 저장소 사용][howto_blob_storage_php](영문)을 참조하세요. PHP에서 SQL 데이터베이스 사용에 대한 내용은 [PHP 응용 프로그램에서 SQL 데이터베이스 사용][howto_sql_azure_php](영문)을 참조하세요.
* **makecall.php** 코드는 Twilio 제공 URL([http://twimlets.com/message][twimlet_message_url])을 사용하여 Twilio에 통화를 진행하는 방법을 알리는 TwiML(Twilio Markup Language) 응답을 제공합니다. 예를 들어, 반환되는 TwiML에는 통화 수신자에게 말하는 텍스트에 나타나는 `<Say>` 동사가 포함될 수 있습니다. Twilio 제공 URL을 사용하는 대신, 고유한 서비스를 빌드하여 Twilio의 요청에 응답할 수 있습니다. 자세한 내용은 [PHP에서 음성 및 SMS 기능을 위해 Twilio를 사용하는 방법][howto_twilio_voice_sms_php]을 참조하세요. TwiML에 대한 자세한 내용은 [http://www.twilio.com/docs/api/twiml][twiml](영문)에서 찾을 수 있으며, `<Say>` 및 기타 Twilio 동사에 대한 자세한 내용은 [http://www.twilio.com/docs/api/twiml/say][twilio_say](영문)에서 찾을 수 있습니다.
* [https://www.twilio.com/docs/security][twilio_docs_security](영문)에서 Twilio 보안 지침을 읽으세요.

Twilio에 대한 자세한 내용은 [https://www.twilio.com/docs][twilio_docs](영문)를 참조하세요

## 참고 항목
* [PHP에서 음성 및 SMS 기능을 위해 Twilio를 사용하는 방법](../partner-twilio-php-how-to-use-voice-sms)

[twilio_pricing]: http://www.twilio.com/pricing
[try_twilio]: http://www.twilio.com/try-twilio
[twilio_api]: http://www.twilio.com/api
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_php]: https://github.com/twilio/twilio-php
[twilio_github_readme]: https://github.com/twilio/twilio-php/blob/master/README.md
[setup_php_sdk]: http://azurephp.interoperabilitybridges.com/articles/setup-the-windows-azure-sdk-for-php
[twimlet_message_url]: http://twimlets.com/message
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api_service]: http://api.twilio.com
[build_php_azure_app]: http://azurephp.interoperabilitybridges.com/articles/build-and-deploy-a-windows-azure-php-application
[howto_twilio_voice_sms_php]: ../partner-twilio-php-how-to-use-voice-sms
[howto_blob_storage_php]: http://azure.microsoft.com/ko-kr/documentation/articles/storage-php-how-to-use-blobs/
[howto_sql_azure_php]: http://azure.microsoft.com/ko-kr/documentation/articles/sql-database-php-how-to-use/
[twilio_call_properties]: https://www.twilio.com/docs/api/rest/call#instance-properties
[twilio_docs_security]: http://www.twilio.com/docs/security
[twilio_docs]: http://www.twilio.com/docs
[twilio_say]: http://www.twilio.com/docs/api/twiml/say
[ssl_validation]: http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html
[twilio_php]: ./media/partner-twilio-php-make-phone-call/WA_TwilioPHPCallForm.jpg
[twilio_php_response]: ./media/partner-twilio-php-make-phone-call/WA_TwilioPHPMakeCall.jpg
[website-git]: https://www.windowsazure.com/ko-kr/develop/php/tutorials/website-w-mysql-and-git/
[website-ftp]: https://www.windowsazure.com/ko-kr/develop/php/tutorials/website-w-mysql-and-ftp/
[website-webmatrix]: https://www.windowsazure.com/ko-kr/develop/php/tutorials/website-w-mysql-and-webmatrix/
[twilio_php_github]: https://github.com/twilio/twilio-php
