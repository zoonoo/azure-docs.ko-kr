<properties title="How to use the SendGrid email service (PHP) - Azure" pageTitle="How to use the SendGrid email service (PHP) - Azure" metaKeywords="Azure SendGrid, Azure email service, Azure SendGrid PHP, Azure email PHP" description="Learn how send email with the SendGrid email service on Azure. Code samples written in PHP." documentationCenter="PHP" services="" manager="wpickett" editor="mollybos" authors="robmcm" scriptId="" videoId="" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="robmcm" />

# PHP에서 SendGrid 전자 메일 서비스를 사용하는 방법

이 가이드에서는 Azure에서 SendGrid 전자 메일 서비스로 일반 프로그래밍 작업을 수행하는 방법을 보여 줍니다. 이 샘플은 PHP로 작성됩니다.
**전자 메일 생성**, **전자 메일 보내기**, **첨부 파일 추가** 등의 시나리오를 다룹니다. SendGrid 및 전자 메일 보내기에 대한 자세한 내용은 [다음 단계][다음 단계] 섹션을 참조하세요.

## 목차

-   [SendGrid 전자 메일 서비스 정의][SendGrid 전자 메일 서비스 정의]
-   [SendGrid 계정 만들기][SendGrid 계정 만들기]
-   [PHP 응용 프로그램에서 SendGrid 사용][PHP 응용 프로그램에서 SendGrid 사용]
-   [방법: 전자 메일 보내기][방법: 전자 메일 보내기]
-   [방법: 첨부 파일 추가][방법: 첨부 파일 추가]
-   [방법: 필터를 사용하여 바닥글, 추적 및 분석을 사용하도록 설정][방법: 필터를 사용하여 바닥글, 추적 및 분석을 사용하도록 설정]
-   [다음 단계][다음 단계]

## <a name="bkmk_WhatIsSendGrid"> </a>SendGrid 전자 메일 서비스 정의

SendGrid는 사용자 지정 통합을 쉽게 만드는 유연한 API와 함께 신뢰할 만한
[트랜잭션 전자 메일 배달][트랜잭션 전자 메일 배달], 확장성 및 실시간 분석을 제공하는
[클라우드 기반 전자 메일 서비스][클라우드 기반 전자 메일 서비스]입니다. 일반적인 SendGrid 사용 시나리오는
다음과 같습니다.

-   고객에게 확인 메일 자동으로 보내기
-   월간 전자 전단 및 판촉 행사를 고객에게 보내기 위한
    분산 목록 관리
-   차단된 전자 메일, 고객 응답 같은 항목의
    실시간 메트릭 수집
-   경향을 식별하는 데 도움이 되도록 보고서 생성
-   고객 문의 전달
-   응용 프로그램의 전자 메일 알림

자세한 내용은 [][]<http://sendgrid.com></a>(영문)을 참조하세요.

## <a name="bkmk_CreateSendGrid"> </a>SendGrid 계정 만들기

[WACOM.INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="bkmk_UsingSendGridfromPHP"> </a>PHP 응용 프로그램에서 SendGrid 사용

Azure PHP 응용 프로그램에서 SendGrid를 사용하기 위해 특별한
구성이 필요하지는 않습니다. SendGrid는 서비스이므로, 온-프레미스
응용 프로그램에서 액세스하는 것과 동일한 클라우드 응용 프로그램에서
액세스할 수 있습니다.

## <a name="bkmk_HowToSendEmail"> </a>방법: 전자 메일 보내기

SendGrid에서 제공하는 SMTP 또는 웹 API를 사용하여
전자 메일을 보낼 수 있습니다.

### SMTP API

SendGrid SMTP API를 사용하여 전자 메일을 보내려면 PHP 응용 프로그램에서 전자 메일을 보내기 위한 구성 요소 기반
라이브러리인 *Swift Mailer*를 사용합니다.
[][1]<http://swiftmailer.org/download></a>에서 *Swift Mailer* 라이브러리를
 다운로드할 수 있습니다. 라이브러리를 사용하여 전자 메일 보내기에는
<span class="auto-style2">Swift\_SmtpTransport</span>,
<span class="auto-style2">Swift\_Mailer</span> 및
<span class="auto-style2">Swift\_Message</span> 클래스의
인스턴스 생성, 적절한
속성 설정 및 <span class="auto-style2">Swift\_Mailer::send</span>
 메서드 호출이 포함됩니다.

    <?php
     include_once "lib/swift_required.php";
     /*
      * Create the body of the message (a plain-text and an HTML version).
      * $text is your plain-text email
      * $html is your html version of the email
      * If the reciever is able to view html emails then only the html
      * email will be displayed
      */ 
     $text = "Hi!\nHow are you?\n";
     $html = <<<EOM
           <html>
           <head></head>
           <body>
               <p>Hi!<br>
                   How are you?<br>
               </p>
           </body>
           </html>
           EOM;
     // This is your From email address
     $from = array('someone@example.com' => 'Name To Appear');
     // Email recipients
     $to = array(
           'john@contoso.com'=>'Destination 1 Name',
           'anna@contoso.com'=>'Destination 2 Name'
     );
     // Email subject
     $subject = 'Example PHP Email';

     // Login credentials
     $username = 'yoursendgridusername';
     $password = 'yourpassword';
     
     // Setup Swift mailer parameters
     $transport = Swift_SmtpTransport::newInstance('smtp.sendgrid.net', 587);
     $transport->setUsername($username);
     $transport->setPassword($password);
     $swift = Swift_Mailer::newInstance($transport);

     // Create a message (subject)
     $message = new Swift_Message($subject);

     // attach the body of the email
     $message->setFrom($from);
     $message->setBody($html, 'text/html');
     $message->setTo($to);
     $message->addPart($text, 'text/plain');
     
     // send message 
     if ($recipients = $swift->send($message, $failures))
     {
         // This will let us know how many users received this message
         echo 'Message sent out to '.$recipients.' users';
     }
     // something went wrong =(
     else
     {
         echo "Something went wrong - ";
         print_r($failures);
     }

### Web API

PHP의 [curl 함수][curl 함수]를 사용하여 SendGrid 웹 API를 사용하여 전자 메일을 보냅니다.

    <?php

     $url = 'http://sendgrid.com/';
     $user = 'USERNAME';
     $pass = 'PASSWORD'; 

     $params = array(
          'api_user' => $user,
          'api_key' => $pass,
          'to' => 'john@contoso.com',
          'subject' => 'testing from curl',
          'html' => 'testing body',
          'text' => 'testing body',
          'from' => 'anna@sendgrid.com',
       );
       
     $request = $url.'api/mail.send.json';
     
     // Generate curl request
     $session = curl_init($request);
     
     // Tell curl to use HTTP POST
     curl_setopt ($session, CURLOPT_POST, true);
     
     // Tell curl that this is the body of the POST
     curl_setopt ($session, CURLOPT_POSTFIELDS, $params);
     
     // Tell curl not to return headers, but do return the response
     curl_setopt($session, CURLOPT_HEADER, false);
     curl_setopt($session, CURLOPT_RETURNTRANSFER, true);
     
     // obtain response
     $response = curl_exec($session);
     curl_close($session);
     
     // print everything out
     print_r($response);

SendGrid의 웹 API는 REST API와 매우 유사하지만 대부분의 호출에서
GET 및 POST 동사를 상호 교환 가능한 방식으로 사용할 수 있으므로
진정한 의미에서 RESTful API는 아닙니다.

## <a name="bkmk_HowToAddAttachment"> </a>방법: 첨부 파일 추가

### SMTP API

SMTP API를 사용하여 첨부 파일을 보내는 프로세스에는 Swift Mailer를 사용하여
전자 메일을 보내기 위한 예제 스크립트에 대한 추가 코드 줄이 포함됩니다.

    <?php
     include_once "lib/swift_required.php";
     /*
      * Create the body of the message (a plain-text and an HTML version).
      * $text is your plain-text email
      * $html is your html version of the email
      * If the reciever is able to view html emails then only the html
      * email will be displayed
      */
     $text = "Hi!\nHow are you?\n";
      $html = <<<EOM
          <html>
          <head></head>
          <body>
             <p>Hi!<br>
                How are you?<br>
             </p>
          </body>
          </html>
     EOM;

     // This is your From email address
     $from = array('someone@example.com' => 'Name To Appear');
     
     // Email recipients
     $to = array(
          'john@contoso.com'=>'Destination 1 Name',
          'anna@contoso.com'=>'Destination 2 Name'
     );
     // Email subject
     $subject = 'Example PHP Email';
     
     // Login credentials
     $username = 'yoursendgridusername';
     $password = 'yourpassword';
     
     // Setup Swift mailer parameters
     $transport = Swift_SmtpTransport::newInstance('smtp.sendgrid.net', 587);
     $transport->setUsername($username);
     $transport->setPassword($password);
     $swift = Swift_Mailer::newInstance($transport);
     
     // Create a message (subject)
     $message = new Swift_Message($subject);
     
     // attach the body of the email
     $message->setFrom($from);
     $message->setBody($html, 'text/html');
     $message->setTo($to);
     $message->addPart($text, 'text/plain');
     $message->attach(Swift_Attachment::fromPath("path\to\file")->setFileName("file_name"));
     
     // send message 
     if ($recipients = $swift->send($message, $failures))
     {
          // This will let us know how many users received this message
          echo 'Message sent out to '.$recipients.' users';
     }
     // something went wrong =(
     else
     {
          echo "Something went wrong - "
          print_r($failures);
     }

추가 코드 줄은 다음과 같습니다.

     $message->attach(Swift_Attachment::fromPath("path\to\file")->setFileName('file_name'));

이 코드 줄은 <span class="auto-style2">Swift\_Message</span>
 개체에 첨부 메서드를 호출하고
<span class="auto-style2">Swift\_Attachment</span> 클래스의 정적
<span class="auto-style2">fromPath</span> 메서드를 사용하여
파일을 가져와서 메시지에 첨부합니다.

### Web API

웹 API를 사용한 첨부 파일 보내기는 웹 API를
사용하여 전자 메일 보내기와 매우 유사합니다. 그러나 다음 예제에서 매개 변수 배열은
이 요소를 포함해야 합니다.

     'files['.$fileName.']' => '@'.$filePath.'/'.$fileName

    <?php

     $url = 'http://sendgrid.com/';
     $user = 'USERNAME';
     $pass = 'PASSWORD';
     
     $fileName = 'myfile';
     $filePath = dirname(__FILE__);

     $params = array(
         'api_user' => $user,
         'api_key' => $pass,
         'to' =>'john@contoso.com',
         'subject' => 'test of file sends',
         'html' => '<p> the HTML </p>',
         'text' => 'the plain text',
         'from' => 'anna@sendgrid.com',
         'files['.$fileName.']' => '@'.$filePath.'/'.$fileName
     );
     
     print_r($params);
     
     $request = $url.'api/mail.send.json';
     
     // Generate curl request
     $session = curl_init($request);
     
     // Tell curl to use HTTP POST
     curl_setopt ($session, CURLOPT_POST, true);
     
     // Tell curl that this is the body of the POST
     curl_setopt ($session, CURLOPT_POSTFIELDS, $params);
     
     // Tell curl not to return headers, but do return the response
     curl_setopt($session, CURLOPT_HEADER, false);
     curl_setopt($session, CURLOPT_RETURNTRANSFER, true);
     
     // obtain response
     $response = curl_exec($session);
     curl_close($session);
     
     // print everything out
     print_r($response);

## <a name="bkmk_HowToUseFilters"> </a>방법: 필터를 사용하여 바닥글, 추적 및 분석을 사용하도록 설정

SendGrid는 필터 사용을 통해 추가 전자 메일 기능을
제공합니다. 클릭 추적, Google 분석, 구독 추적 등을 사용하도록 설정하는 것과
같이 특정 기능을 사용하도록 설정하기 위해 전자 메일 메시지에
추가할 수 있는 설정입니다.

필터는 filters 속성을 사용하여 메시지에 적용할 수 있습니다. 각 필터는
필터별 설정을 포함하는 해시에 의해 지정됩니다.
다음 예제에서는 바닥글 필터를 사용하도록 설정하고 전자 메일 메시지의
맨 아래에 추가할 텍스트 메시지를 지정합니다.

    <?php
     /*
      * This example is used for Swift Mailer V4
      */
     include "./lib/swift_required.php";
     include 'SmtpApiHeader.php';
     
     $hdr = new SmtpApiHeader();
     // The list of addresses this message will be sent to
     // [This list is used for sending multiple emails using just ONE request to 
     SendGrid]
     $toList = array('john@contoso.com', 'anna@contoso.com');
     
     // Specify the names of the recipients
     $nameList = array('Name 1', 'Name 2');
     
     // Used as an example of variable substitution
     $timeList = array('4 PM', '5 PM');
     
     // Set all of the above variables
     $hdr->addTo($toList);
     $hdr->addSubVal('-name-', $nameList);
     $hdr->addSubVal('-time-', $timeList);
     
     // Specify that this is an initial contact message
     $hdr->setCategory("initial");
     
     // You can optionally setup individual filters here, in this example, we have 
     enabled the footer filter
     $hdr->addFilterSetting('footer', 'enable', 1);
     $hdr->addFilterSetting('footer', "text/plain", "Thank you for your business");
     
     // The subject of your email
     $subject = 'Example SendGrid Email';
     
     // Where is this message coming from. For example, this message can be from support@yourcompany.com, info@yourcompany.com
     $from = array('someone@example.com' =&gt; 'Name Of Your Company');
     
     // If you do not specify a sender list above, you can specifiy the user here. If 
     // a sender list IS specified above, this email address becomes irrelevant.
     $to = array('john@contoso.com'=&gt;'Personal Name Of Recipient');
     
     # Create the body of the message (a plain-text and an HTML version). 
     # text is your plain-text email 
     # html is your html version of the email
     # if the receiver is able to view html emails then only the html
     # email will be displayed
     
     /*
     * Note the variable substitution here =)
     */
     $text = <<<EOM 
     Hello -name-,
     Thank you for your interest in our products. We have set up an appointment to call you at -time- EST to discuss your needs in more detail.
     Regards,
     Fred
     EOM;
     
     $html = <<<EOM
     < html> 
     <head></head>
     <body>
     <p>Hello -name-,<br>
     Thank you for your interest in our products. We have set up an appointment
     to call you at -time- EST to discuss your needs in more detail.
     
     Regards,
     
     Fred, How are you?<br>
     </p>
     </body>
     < /html>
     EOM;
     
     // Your SendGrid account credentials
     $username = 'sendgridusername@yourdomain.com';
     $password = 'example';
     
     // Create new swift connection and authenticate
     $transport = Swift_SmtpTransport::newInstance('smtp.sendgrid.net', 25);
     $transport ->setUsername($username);
     $transport ->setPassword($password);
     $swift = Swift_Mailer::newInstance($transport);
     
     // Create a message (subject)
     $message = new Swift_Message($subject);
     
     // add SMTPAPI header to the message
     // *****IMPORTANT NOTE*****
     // SendGrid's asJSON function escapes characters. If you are using Swift 
     Mailer's
     // PHP Mailer functions, the getTextHeader function will also escape characters.
     // This can cause the filter to be dropped.
     $headers = $message->getHeaders();
     $headers->addTextHeader('X-SMTPAPI', $hdr->asJSON());
     
     // attach the body of the email
     $message->setFrom($from);
     $message->setBody($html, 'text/html');
     $message->setTo($to);
     $message->addPart($text, 'text/plain');
     
     // send message
     if ($recipients = $swift->send($message, $failures))
     {
     // This will let us know how many users received this message
     // If we specify the names in the X-SMTPAPI header, then this will always be 1.
     echo 'Message sent out to '.$recipients.' users';
     }

     // something went wrong =(
     else
     {
     echo "Something went wrong - ";
     print_r($failures);
     }

## <a name="bkmk_NextSteps"> </a>다음 단계

SendGrid 전자 메일 서비스에 관한 기본적인 사항들을 익혔으며 자세한 내용을 보려면
다음 링크를 따라가세요.

-   SendGrid 설명서: <https://sendgrid.com/docs>
-   Azure 고객을 위한 SendGrid 특가 제공: <http://sendgrid.com/azure.html>

  [다음 단계]: #bkmk_NextSteps
  [SendGrid 전자 메일 서비스 정의]: #bkmk_WhatIsSendGrid
  [SendGrid 계정 만들기]: #bkmk_CreateSendGrid
  [PHP 응용 프로그램에서 SendGrid 사용]: #bkmk_UsingSendGridfromPHP
  [방법: 전자 메일 보내기]: #bkmk_HowToSendEmail
  [방법: 첨부 파일 추가]: #bkmk_HowToAddAttachment
  [방법: 필터를 사용하여 바닥글, 추적 및 분석을 사용하도록 설정]: #bkmk_HowToUseFilters
  [트랜잭션 전자 메일 배달]: http://sendgrid.com/transactional-email
  [클라우드 기반 전자 메일 서비스]: http://sendgrid.com/solutions
  []: http://sendgrid.com
  [1]: http://swiftmailer.org/download
  [curl 함수]: http://php.net/curl
