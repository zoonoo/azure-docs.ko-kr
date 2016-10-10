<properties 
    pageTitle="AMQP 1.0을 사용한 서비스 버스 및 PHP | Microsoft Azure"
    description="AMQP를 사용하여 PHP에서 서비스 버스 사용."
    services="service-bus-messaging"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" /> 
<tags 
    ms.service="service-bus-messaging"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="05/06/2016"
    ms.author="sethm" />

# AMQP 1.0을 사용하여 PHP에서 서비스 버스 사용

[AZURE.INCLUDE [service-bus-selector-amqp](../../includes/service-bus-selector-amqp.md)]

Proton-PHP는 Proton-C로 바인딩된 PHP 언어로 Proton-PHP는 C에서 구현된 엔진에 대한 래퍼로 구현됩니다.

## Proton 클라이언트 라이브러리 다운로드

[http://qpid.apache.org/download.html](http://qpid.apache.org/download.html)에서 Proton-C 및 연관된 바인딩(PHP 포함)을 다운로드할 수 있습니다 . 다운로드는 소스 코드 양식입니다. 코드를 작성하려면 다운로드한 패키지에 포함된 지침을 따릅니다.

> [AZURE.IMPORTANT] 이 문서 작성 시 Proton-C의 SSL 지원은 Linux 운영 체제에서만 사용할 수 있습니다. Azure 서비스 버스는 SSL을 사용하므로 이 경우 Proton-C(및 언어 바인딩)는 Linux에서 서비스 버스에 액세스하는데만 사용될 수 있습니다. Windows에서 SSL과 함께 Proton-C를 사용하도록 설정하는 작업은 진행 중이므로 업데이트를 자주 확인하세요.

## PHP에서 Service Bus 큐, 토픽 및 구독 작업

다음 코드는 서비스 버스 메시징 엔터티에서 메시지를 전송하고 수신하는 방법을 보여 줍니다.

### Proton-PHP를 사용하여 메시지 전송

다음 코드는 서비스 버스 메시징 엔터티에 메시지를 전송하는 방법을 보여 줍니다.

```
$messenger = new Messenger();
$message = new Message();
$message->address = "amqps://[username]:[password]@[namespace].servicebus.windows.net/[entity]";

$message->body = "This is a text string";
$messenger->put($message);
$messenger->send();
```

### Proton-PHP를 사용하여 메시지 수신

다음 코드는 서비스 버스 메시징 엔터티에서 메시지를 수신하는 방법을 보여 줍니다.

```
$messenger = new Messenger();
$address = "amqps://[username]:[password]@[namespace].servicebus.windows.net/[entity]";
$messenger->subscribe($address);

$messenger->start();
$messenger->recv(1);

if($messenger->incoming())
{
   $message = new Message();
   $messenger->get($message);      
}

$messenger->stop();
```

## .NET과 Proton-PHP 간의 메시징

### 응용 프로그램 속성

#### ProtonPHP에서 서비스 버스 .NET API

Proton-PHP 메시지는 다음과 같은 형식의 응용 프로그램 속성을 지원합니다. **integer**, **double**, **Boolean**, **string** 및 **object** 다음 PHP 코드는 이러한 각 형식의 속성을 사용하여 메시지에서 속성을 설정하는 방법을 보여 줍니다.

```
$message->properties["TestInt"] = 1;    
$message->properties["TestDouble"] = 1.5;      
$message->properties["TestBoolean"] = False;
$message->properties["TestString"] = "Service Bus";    
$message->properties["TestObject"] = new UUID("1234123412341234");   
```

서비스 버스 .NET API에서 메시지 응용 프로그램 속성은 [BrokeredMessage][]의 **속성** 컬렉션에 전달됩니다. 다음 코드는 PHP 클라이언트로부터 받은 메시지의 응용 프로그램 속성을 읽는 방법을 보여 줍니다.

```
if (message.Properties.Keys.Count > 0)
{
  foreach (string name in message.Properties.Keys)
  {
    Object value = message.Properties[name];
    Console.WriteLine(name + ": " + value + " (" + value.GetType() + ")" );
  }
  Console.WriteLine();
}if (message.Properties.Keys.Count > 0)
{
foreach (string name in message.Properties.Keys)
{
  Object value = message.Properties[name];
  Console.WriteLine(name + ": " + value + " (" + value.GetType() + ")" );
}
Console.WriteLine();
}
```

다음 표는 PHP 속성 형식을 .NET 속성 형식으로 매핑합니다.

| PHP 속성 형식 | .NET 속성 형식 |
|-------------------|--------------------|
| integer | int |
| double | double |
| boolean | bool |
| string | string |
| object | Object |

#### 서비스 버스 .NET API에서 PHP

[BrokeredMessage][] 형식은 다음과 같은 형식의 응용 프로그램 속성을 지원합니다. **byte**, **sbyte**, **char**, **short**, **ushort**, **int**, **uint**, **long**, **ulong**, **float**, **double**, **decimal**, **bool**, **Guid**, **string**, **Uri**, **DateTime**, **DateTimeOffset** 및 **TimeSpan** 다음 .NET 코드는 이러한 각 형식의 속성을 사용하여 [BrokeredMessage][]에서 속성을 설정하는 방법을 보여 줍니다.

```
message.Properties["TestByte"] = (byte)128;
message.Properties["TestSbyte"] = (sbyte)-22;
message.Properties["TestChar"] = (char) 'X';
message.Properties["TestShort"] = (short)-12345;
message.Properties["TestUshort"] = (ushort)12345;
message.Properties["TestInt"] = (int)-100;
message.Properties["TestUint"] = (uint)100;
message.Properties["TestLong"] = (long)-12345;
message.Properties["TestUlong"] = (ulong)12345;
message.Properties["TestFloat"] = (float)3.14159;
message.Properties["TestDouble"] = (double)3.14159;
message.Properties["TestDecimal"] = (decimal)3.14159;
message.Properties["TestBoolean"] = true;
message.Properties["TestGuid"] = Guid.NewGuid();
message.Properties["TestString"] = "Service Bus";
message.Properties["TestUri"] = new Uri("http://www.bing.com");
message.Properties["TestDateTime"] = DateTime.Now;
message.Properties["TestDateTimeOffSet"] = DateTimeOffset.Now;
message.Properties["TestTimeSpan"] = TimeSpan.FromMinutes(60);
```

다음 PHP 코드는 서비스 버스 .NET 클라이언트부터 받은 메시지의 응용 프로그램 속성을 읽는 방법을 보여 줍니다.

```
if ($message->properties != null)
{
  foreach($message->properties as $key => $value)
  {
    printf("-- %s : %s (%s) \n", $key, $value, gettype($value));                       
  }         
}
```

다음 표는 .NET 속성 형식을 PHP 속성 형식으로 매핑합니다.

| .NET 속성 형식 | PHP 속성 형식 | 참고 사항 |
|--------------------|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| byte | integer | - | | sbyte | integer | - | | char | Char | Proton-PHP class | | short | integer | - | | ushort | integer | - | | int | integer | - | | uint | Integer | - | | long | integer | - | | ulong | integer | - | | float | double | - | | double | double | - | | decimal | string | Proton에서 Decimal은 현재 지원되지 않습니다. | | bool | boolean | - | | Guid | UUID | Proton-PHP class | | string | string | - | | DateTime | integer | - | | DateTimeOffset | DescribedType | AMQP 형식으로 매핑된 DateTimeOffset.UtcTicks:<type name="datetime-offset" class=restricted source="long"> <descriptor name="com.microsoft:datetime-offset" /></type> | | TimeSpan | DescribedType | AMQP 형식으로 매핑된 Timespan.Ticks:<type name="timespan" class=restricted source="long"> <descriptor name="com.microsoft:timespan" /></type> | | Uri | DescribedType | AMQP 형식으로 매핑된 Uri.AbsoluteUri:<type name="uri" class=restricted source="string"> <descriptor name="com.microsoft:uri" /></type> |

### 표준 속성

다음 표는 Proton-PHP 표준 메시지 속성과 [BrokeredMessage][] 표준 메시지 속성 간의 매핑을 보여 줍니다.

| Proton-PHP | 서비스 버스 .NET | 참고 사항 |
|----------------------|--------------------------|----------------------------------------------------------|
| 지속성 | 해당 없음 | 서비스 버스는 지속적 메시지만 지원합니다. |
| 우선 순위 | 해당 없음 | 서비스 버스는 단일 메시지 우선 순위만 지원합니다. |
| Ttl | Message.TimeToLive | 변환, Proton-PHP TTL이 밀리초 단위로 정의됩니다. |
| first\_acquirer | - | - | | delivery\_count | - | - | | Id | Message.Id | - | | user\_id | - | - | | Address | Message.To | - | | Subject | Message.Label | - | | reply\_to | Message.ReplyTo | - | | correlation\_id | Message.CorrelationId | - | | content\_type | Message.ContentType | - | | content\_encoding | n/a | - | | expiry\_time | Message.ExpiresAtUTC | - | | creation\_time | n/a | - | | group\_id | Message.SessionId | - | | group\_sequence | - | - | | reply\_to\_group\_id | Message.ReplyToSessionId | - | | Format | n/a | -

#### 서비스 버스 .NET API에서 Proton-PHP

| 서비스 버스 .NET | Proton-PHP | 참고 |
|-------------------------|--------------------------------------------------------|--------------------------------------------------------|
| ContentType | Message->content\_type | - | | CorrelationId | Message->correlation\_id | - | | EnqueuedTimeUtc | Message->annotations[x-opt-enqueued-time] | - | | Label | Message->subject | - | | MessageId | Message->id | - | | ReplyTo | Message->reply\_to | - | | ReplyToSessionId | Message->reply\_to\_group\_id | - | | ScheduledEnqueueTimeUtc | Message->annotations ["x-opt-scheduled-enqueue-time"] | - | | SessionId | Message->group\_id | - | | TimeToLive | Message->ttl | 변환, Proton-PHP TTL은 밀리초 단위로 정의됩니다. | | To | Message->address | - |

## 다음 단계

자세히 알아볼 준비가 되셨습니까? 다음 링크를 방문하세요.

- [서비스 버스 AMQP 개요]
- [Windows Server용 서비스 버스의 AMQP]


[BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx
[Windows Server용 서비스 버스의 AMQP]: https://msdn.microsoft.com/library/dn574799.aspx
[서비스 버스 AMQP 개요]: service-bus-amqp-overview.md

<!---HONumber=AcomDC_0928_2016-->