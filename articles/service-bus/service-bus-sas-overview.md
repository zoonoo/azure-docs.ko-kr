<properties
   pageTitle="공유 액세스 서명 개요 | Microsoft Azure"
   description="공유 액세스 서명의 정의, 그 작동 방법 및 노드, PHP, C#에서 공유 액세스 서명을 사용하는 방법에 대해 설명합니다."
   services="service-bus,event-hubs"
   documentationCenter="na"
   authors="djrosanova"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-bus"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/04/2015"
   ms.author="darosa"/>

# 공유 액세스 서명

*공유 액세스 서명*(SAS)은 이벤트 허브, 조정된 메시징(큐 및 토픽) 및 릴레이된 메시징을 포함한 서비스 버스에 대한 기본 보안 메커니즘입니다. 이 문서에서는 공유 액세스 서명, 그 작동 방법 및 플랫폼과 상관 없는 방식으로 사용하는 방법에 대해 설명합니다.

## SAS 개요

공유 액세스 서명은 SHA-256 보안 해시 또는 URI에 따른 인증 메커니즘입니다. SAS는 모든 서비스 버스 서비스에서 사용되는 매우 강력한 메커니즘입니다. 실제 사용 시, SAS에는 *공유 액세스 정책*과 *공유 액세스 서명*(*토큰*이라고 부름)의 두 구성 요소가 있습니다.

서비스 버스를 통한 공유 액세스 서명에 대한 자세한 내용은 [서비스 버스를 사용한 공유 액세스 서명 인증](service-bus-shared-access-signature-authentication.md)에서 확인할 수 있습니다.

## 공유 액세스 정책

SAS에 대해 잘 이해하기 위해서는 정책부터 시작하는 것이 중요합니다. 모든 정책은 세 가지 정보 즉, **이름**, **범위** 및 **권한**을 결정해야 합니다. **이름**은 해당 범위 내에서 고유한 이름입니다. 범위는 아주 쉬운데, 해당 리소스의 URI를 말합니다. 서비스 버스 네임스페이스의 경우, 범위는 **`https://<yournamespace>.servicebus.windows.net/`** 등과 같이 정규화된 도메인 이름(FQDN)입니다.

정책에서 사용 가능한 권한은 대부분 설명이 따로 필요 없습니다.

  + 보내기
  + 수신 대기
  + 관리

정책을 만든 후 *기본 키*와 *보조 키*가 할당됩니다. 이들은 강력한 암호화 키입니다. 잃어 버리거나 누출되지 않도록 하세요. 항상 [Azure 클래식 포털][]에서 사용할 수 있습니다. 생성된 키 중 하나를 사용할 수 있으며 언제든지 다시 생성할 수 있습니다. 그러나 정책에서 기본 키를 다시 생성하거나 변경할 경우, 만든 공유 액세스 서명은 무효화됩니다.

서비스 버스 네임스페이스를 만들 때 **RootManageSharedAccessKey**라는 전체 네임스페이스에 대해 정책이 자동으로 만들어지며 이 정책은 모든 사용 권한을 갖습니다. **root**로 로그온하지 않아야 합니다. 따라서 특별한 이유가 없으면 이 정책을 사용하지 않도록 합니다. 포털의 해당 네임스페이스에 대한 **구성** 탭에서 추가 정책을 만들 수 있습니다. 서비스 버스의 단일 트리 수준(네임스페이스, 큐, 이벤트 허브 등)에는 최대 12개의 정책까지만 연결할 수 있습니다.

## 공유 액세스 서명(토큰)

정책 자체는 서비스 버스에 대한 액세스 토큰이 아니며 기본 또는 보조 키 중 하나를 사용하여 액세스 토큰이 생성되는 개체입니다. 토큰은 다음 형식으로 문자열을 신중하게 선별하여 생성됩니다.

```
SharedAccessSignature sig=<signature-string>&se=<expiry>&skn=<keyName>&sr=<URL-encoded-resourceURI>
```

여기서 `signature-string`은 토큰 범위(이전 섹션에서 설명한 대로 **범위**)의 SHA-256 해시로, CRLF가 첨부되어 있고 만료 시간(Epoch 이후 초 단위: 1970년 1월 1일에 `00:00:00 UTC`)이 있습니다.

해시는 다음 의사 코드와 유사하며 32바이트를 반환합니다.

```
SHA-256('https://<yournamespace>.servicebus.windows.net/'+'\n'+ 1438205742)
```

비해시 값이**SharedAccessSignature** 문자열에 있으므로 수신자는 동일한 결과를 반환하도록 동일한 매개 변수로 해시를 계산할 수 있습니다. URI는 범위를 지정하며 키 이름은 해시를 계산하는 데 사용할 정책을 식별합니다. 이것은 보안의 관점에서 중요합니다. 서명이 수신자(서비스 버스)에서 계산하는 것과 일치하지 않으면 액세스가 거부됩니다. 이제 발신자에게 키에 대한 액세스 권한이 있음을 알 수 있으므로 정책에 지정된 권한을 부여해야 합니다.

## 정책에서 서명 생성

실제로 코드에서 이 작업을 어떻게 해야 할까요? 몇 가지를 살펴보겠습니다.

### NodeJS

```
function createSharedAccessToken(uri, saName, saKey) { 
    if (!uri || !saName || !saKey) { 
            throw "Missing required parameter"; 
        } 
    var encoded = encodeURIComponent(uri); 
    var now = new Date(); 
    var week = 60*60*24*7;
    var ttl = Math.round(now.getTime() / 1000) + week;
    var signature = encoded + '\n' + ttl; 
    var signatureUTF8 = utf8.encode(signature); 
    var hash = crypto.createHmac('sha256', saKey).update(signatureUTF8).digest('base64'); 
    return 'SharedAccessSignature sr=' + encoded + '&sig=' +  
        encodeURIComponent(hash) + '&se=' + ttl + '&skn=' + saName; 
}
``` 

### Java

```
private static String GetSASToken(String resourceUri, String keyName, String key)
  {
      long epoch = System.currentTimeMillis()/1000L;
      int week = 60*60*24*7;
      String expiry = Long.toString(epoch + week);

      String sasToken = null;
      try {
          String stringToSign = URLEncoder.encode(resourceUri, "UTF-8") + "\n" + expiry;
          String signature = getHMAC256(key, stringToSign);
          sasToken = "SharedAccessSignature sr=" + URLEncoder.encode(resourceUri, "UTF-8") +"&sig=" +
                  URLEncoder.encode(signature, "UTF-8") + "&se=" + expiry + "&skn=" + keyName;
      } catch (UnsupportedEncodingException e) {

          e.printStackTrace();
      }

      return sasToken;
  }


public static String getHMAC256(String key, String input) {
    Mac sha256_HMAC = null;
    String hash = null;
    try {
        sha256_HMAC = Mac.getInstance("HmacSHA256");
        SecretKeySpec secret_key = new SecretKeySpec(key.getBytes(), "HmacSHA256");
        sha256_HMAC.init(secret_key);
        Encoder encoder = Base64.getEncoder();

        hash = new String(encoder.encode(sha256_HMAC.doFinal(input.getBytes("UTF-8"))));

    } catch (InvalidKeyException e) {
        e.printStackTrace();
    } catch (NoSuchAlgorithmException e) {
        e.printStackTrace();
   } catch (IllegalStateException e) {
        e.printStackTrace();
    } catch (UnsupportedEncodingException e) {
        e.printStackTrace();
    }

    return hash;
}
```

### PHP

```
function generateSasToken($uri, $sasKeyName, $sasKeyValue) 
{ 
$targetUri = strtolower(rawurlencode(strtolower($uri))); 
$expires = time(); 	
$expiresInMins = 60; 
$week = 60*60*24*7;
$expires = $expires + $week; 
$toSign = $targetUri . "\n" . $expires; 
$signature = rawurlencode(base64_encode(hash_hmac('sha256', 			
 $toSign, $sasKeyValue, TRUE))); 

$token = "SharedAccessSignature sr=" . $targetUri . "&sig=" . $signature . "&se=" . $expires . 		"&skn=" . $sasKeyName; 
return $token; 
}
```
 
### C&#35;

```
private static string createToken(string resourceUri, string keyName, string key)
{
    TimeSpan sinceEpoch = DateTime.UtcNow - new DateTime(1970, 1, 1);
    var week = 60 * 60 * 24 * 7;
    var expiry = Convert.ToString((int)sinceEpoch.TotalSeconds + week);
    string stringToSign = HttpUtility.UrlEncode(resourceUri) + "\n" + expiry;
    HMACSHA256 hmac = new HMACSHA256(Encoding.UTF8.GetBytes(key));
    var signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(stringToSign)));
    var sasToken = String.Format(CultureInfo.InvariantCulture, "SharedAccessSignature sr={0}&sig={1}&se={2}&skn={3}", HttpUtility.UrlEncode(resourceUri), HttpUtility.UrlEncode(signature), expiry, keyName);
    return sasToken;
}
```

## 공유 액세스 서명 사용(HTTP 수준에서)
 
서비스 버스의 모든 엔터티에 대해 공유 액세스 서명을 만드는 방법을 알았으므로 HTTP POST를 수행할 준비가 되었습니다.

```
POST https://<yournamespace>.servicebus.windows.net/<yourentity>/messages
Content-Type: application/json
Authorization: SharedAccessSignature sr=https%3A%2F%2F<yournamespace>.servicebus.windows.net%2F<yourentity>&sig=<yoursignature from code above>&se=1438205742&skn=KeyName
ContentType: application/atom+xml;type=entry;charset=utf-8
``` 
	
이 방식은 모든 항목에 대해 작동한다는 것을 기억하세요. 큐, 토픽, 구독, 이벤트 허브 또는 릴레이에 대해 SAS를 만들 수 있습니다. 이벤트 허브에 대한 게시자별 ID를 사용하는 경우 `/publishers/< publisherid>`를 추가하면 됩니다.

발신자 또는 클라이언트에게 SAS 토큰을 제공하면 직접 키를 가질 수 없으며 키를 얻기 위해 해시를 되돌릴 수도 없습니다. 예를 들어 사용자가 액세스할 수 있는 항목 및 액세스 기간을 제어할 수 있습니다. 정책에서 기본 키를 다시 생성하거나 변경할 경우, 만든 공유 액세스 서명이 무효화되므로 주의해야 합니다.

## 공유 액세스 서명 사용(AMQP 수준에서)

이전 섹션에서는 HTTP POST 요청과 함께 SAS 토큰을 사용하여 데이터를 서비스 버스에 보내는 방법을 살펴봤습니다. 아시다시피, 서비스 버스는 많은 시나리오에서 성능상의 이유로 사용하는 기본 및 선호 프로토콜인 AMQP(고급 메시지 큐 프로토콜) 프로토콜을 사용하여 액세스할 수 있습니다. AMQP와 함께 SAS 토큰을 사용하는 방법은 2013년 이후 초안 상태이지만 현재 Azure에서 충분한 지원을 받고 있는 문서 [AMQP 클레임 기반 보안 버전 1.0](https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc)에 설명되어 있습니다.

서비스 버스에 데이터의 전송을 시작하기 전에 게시자는 AMQP 메시지 안에 있는 SAS 토큰을 **"$cbs"**(모든 SAS 토큰을 얻고 유효성을 검사하기 위해 서비스에서 사용하는 "특별" 큐)라는 이름의 정의된 AMQP 노드에 전송해야 합니다. 게시자는 AMQP 메시지 내부에 있는 **"ReplyTo"** 필드를 지정해야 합니다. 이는 서비스가 토큰 유효성 검사 결과와 함께 게시자에게 응답하는 노드입니다(게시자와 서비스 간의 간단한 요청/응답 패턴). 이 회신 노드는 "즉시" 생성되며 AMQP 1.0 사양에 설명된 것처럼 “원격 노드 동적 생성”에 대해 얘기합니다. SAS 토큰이 유효한지 확인한 후 게시자는 이제 데이터를 서비스에 보내기 시작할 수 있습니다.

다음 단계는 C&#35;에서 개발 중인 공식 서비스 버스 SDK(예: WinRT, .Net Compact Framework, .Net Micro Framework 및 Mono에서)를 사용할 수 없는 경우에 유용한 [AMQP.Net Lite](http://amqpnetlite.codeplex.com) 라이브러리를 사용하여 AMQP 프로토콜과 함께 SAS 토큰을 보내는 방법을 알려줍니다. 물론 이 라이브러리는 클레임 기반 보안이 HTTP 수준에서 작동하는 방식을 볼 때처럼 AMQP 수준에서 작동하는 방식을 이해하는 데 유용합니다(“권한 부여" 헤더 내에서 전송되는 HTTP POST 요청 및 SAS 토큰과 함께). 그러나 염려하지 마세요. AMQP에 대한 깊은 지식이 없어도 모든 다른 플랫폼에 대해 [Azure SB Lite](http://azuresblite.codeplex.com) 라이브러리 또는 .Net Framework 응용 프로그램과 함께 공식 서비스 버스 SDK를 사용할 수 있습니다(위 참조).

### C&#35;

```
/// <summary>
/// Send Claim Based Security (CBS) token
/// </summary>
/// <param name="shareAccessSignature">Shared access signature (token) to send</param>
private bool PutCbsToken(Connection connection, string sasToken)
{
    bool result = true;
    Session session = new Session(connection);

    string cbsClientAddress = "cbs-client-reply-to";
    var cbsSender = new SenderLink(session, "cbs-sender", "$cbs");
    var cbsReceiver = new ReceiverLink(session, cbsClientAddress, "$cbs");

    // construct the put-token message
    var request = new Message(sasToken);
    request.Properties = new Properties();
    request.Properties.MessageId = Guid.NewGuid().ToString();
    request.Properties.ReplyTo = cbsClientAddress;
    request.ApplicationProperties = new ApplicationProperties();
    request.ApplicationProperties["operation"] = "put-token";
    request.ApplicationProperties["type"] = "servicebus.windows.net:sastoken";
    request.ApplicationProperties["name"] = Fx.Format("amqp://{0}/{1}", sbNamespace, entity);
    cbsSender.Send(request);

    // receive the response
    var response = cbsReceiver.Receive();
    if (response == null || response.Properties == null || response.ApplicationProperties == null)
    {
        result = false;
    }
    else
    {
        int statusCode = (int)response.ApplicationProperties["status-code"];
        if (statusCode != (int)HttpStatusCode.Accepted && statusCode != (int)HttpStatusCode.OK)
        {
            result = false;
        }
    }

    // the sender/receiver may be kept open for refreshing tokens
    cbsSender.Close();
    cbsReceiver.Close();
    session.Close();

    return result;
}
```

위의 *PutCbsToken()* 메서드는 서비스에 대한 TCP 연결 및 전송할 SAS 토큰인 *sasToken* 매개 변수를 나타내는 *연결*(AMQP.Net Lite 라이브러리에서 제공하는 대로 AMQP 연결 클래스 인스턴스)을 수신합니다. 참고: 연결이 **EXTERNAL로 설정된 SASL 인증 메커니즘**(SAS 토큰을 보낼 필요가 없을 때 사용하는 사용자 이름 및 암호를 가진 기본 PLAIN이 아님)으로 생성된다는 사실이 중요합니다.

그런 다음 게시자는 SAS 토큰을 보내고 서비스로부터 회신(토큰 유효성 검사 결과)을 받기 위한 2개의 AMQP 링크를 만듭니다.

AMQP 메시지는 간단한 메시지보다 정보가 많고 속성이 많으므로 다소 복잡합니다. SAS 토큰은 메시지의 본문으로 배치됩니다(해당 생성자를 사용하여). **"ReplyTo"** 속성은 수신기 링크에 대한 유효성 검사 결과를 받기 위한 노드 이름으로 설정됩니다(원하는 대로 이름을 변경할 수 있으며 서비스에서 동적으로 생성합니다). 마지막 세 응용 프로그램/사용자 지정 속성은 서비스에서 실행하는 작업의 종류를 이해하는 데 사용합니다. CBS 초안 사양에서 설명한 것처럼 이들은 **토큰의 형식**("servicebus.windows.net:sastoken")인 **작업 이름**("put-token")이 되고 마지막으로는 토큰이 적용되는 **청중의 "이름"**이어야 합니다(전체 엔터티).

보낸 사람 링크에서 SAS 토큰을 보낸 후 게시자는 수신자 링크에서 회신을 읽어야 합니다. 회신은 HTTP 상태 코드와 동일한 값을 포함할 수 있는 **"status-code"**라는 이름의 응용 프로그램 속성을 가진 간단한 AMQP 메시지입니다.

## 다음 단계

이러한 SAS 토큰으로 수행할 수 있는 작업에 대한 자세한 내용은 [서비스 버스 REST API 참조](https://msdn.microsoft.com/library/azure/hh780717.aspx)를 참조하세요.

서비스 버스 인증에 대한 자세한 내용은 [서비스 버스 인증 및 권한 부여](service-bus-authentication-and-authorization.md)를 참조하세요.

C# 및 Java Script에서 SAS의 자세한 예는 [이 블로그 게시물](http://developers.de/blogs/damir_dobric/archive/2013/10/17/how-to-create-shared-access-signature-for-service-bus.aspx)에 나와 있습니다.

[Azure 클래식 포털]: http://manage.windowsazure.com

<!---HONumber=AcomDC_1203_2015-->