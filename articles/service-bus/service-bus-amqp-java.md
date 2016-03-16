<properties 
   pageTitle="AMQP 1.0을 사용한 서비스 버스 및 Java | Microsoft Azure"
   description="AMQP를 사용하여 Java에서 서비스 버스 사용."
   services="service-bus"
   documentationCenter="na"
   authors="sethmanheim"
   manager="timlt"
   editor="tysonn" /> 
<tags 
   ms.service="service-bus"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="01/26/2016"
   ms.author="sethm" />

# AMQP 1.0을 사용하여 Java에서 서비스 버스 사용

[AZURE.INCLUDE [service-bus-selector-amqp](../../includes/service-bus-selector-amqp.md)]

JMS(Java Message Service)는 Java 플랫폼의 메시지 지향 미들웨어 작업에 대한 표준 API입니다. Microsoft Azure 서비스 버스는 Apache Qpid 프로젝트에 의해 개발된 JMS 클라이언트 라이브러리 기반 AMQP 1.0과 함께 테스트되었습니다. 이 라이브러리는 전체 JMS 1.1 API를 지원하며 모든 AMQP 1.0 호환 메시징 서비스와 함께 사용할 수 있습니다. 이 시나리오는 [Windows Serve용 서비스 버스](https://msdn.microsoft.com/library/dn282144.aspx)(온-프레미스 서비스 버스)에서도 지원됩니다. 자세한 내용은 [Windows Server용 서비스 버스의 AMQP][]를 참조하세요.

## Apache Qpid AMQP 1.0 JMS 클라이언트 라이브러리 다운로드

최신 버전의 Apache Qpid JMS AMQP 1.0 클라이언트 라이브러리를 다운로드할 위치에 대한 자세한 내용은 [http://people.apache.org/~rgodfrey/qpid-java-amqp-1-0-client-jms.html](http://people.apache.org/~rgodfrey/qpid-java-amqp-1-0-client-jms.html)을 참조하세요.

서비스 버스를 사용하여 JMS 응용 프로그램을 빌드 및 실행할 때 Apache Qpid JMS AMQP 1.0 배포 보관에 포함된 다음 JAR 파일 4개를 Java CLASSPATH에 추가해야 합니다.

-   geronimo-jms\_1.1\_spec-[version].jar

-   qpid-amqp-1-0-client-[version].jar

-   qpid-amqp-1-0-client-jms-[version].jar

-   qpid-amqp-1-0-common-[version].jar

## JMS에서 서비스 버스 큐, 토픽 및 구독 작업

### JNDI(Java Naming and Directory Interface)

JMS는 JNDI(Java Naming and Directory Interface)를 사용하여 논리적 이름과 물리적 이름 간에 구분을 만듭니다. JNDI를 사용하여 두 유형의 JMS 개체인 **ConnectionFactory**와 **Destination**을 확인합니다. JNDI는 다양한 디렉터리 서비스를 연결할 수 있는 공급자 모델을 사용하여 이름 확인 책임을 처리합니다. Apache Qpid JMS AMQP 1.0 라이브러리에는 텍스트 파일을 사용하여 구성된 간단한 속성 파일 기반 JNDI 공급자가 포함되어 있습니다.

Qpid 속성 파일 JNDI 공급자는 다음과 같은 형식의 속성 파일을 사용하여 구성됩니다.

```
# servicebus.properties – sample JNDI configuration

# Register a ConnectionFactory in JNDI using the form:
# connectionfactory.[jndi_name] = [ConnectionURL]
connectionfactory.SBCONNECTIONFACTORY = amqps://[username]:[password]@[namespace].servicebus.windows.net

# Register some queues in JNDI using the form
# queue.[jndi_name] = [physical_name]
# topic.[jndi_name] = [physical_name]
topic.TOPIC = topic1
queue.QUEUE = queue1
```

#### 연결 팩터리 구성

Qpid 속성 파일 JNDI 공급자에서 **ConnectionFactory**를 정의하는 데 사용되는 항목의 형식은 다음과 같습니다.

```
connectionfactory.[jndi_name] = [ConnectionURL]
```

여기서 `[jndi\_name]` 및 `[ConnectionURL]`은(는) 다음과 같은 의미가 있습니다.

| 이름 | 의미 | | | | |
|-----------------|--------------------------------------------------------------------------------------------------------------------------------------------|---|---|---|---|
| `[jndi\_name]` | 연결 팩터리의 논리적 이름입니다. 이 이름은 JNDI `IntialContext.lookup()` 메서드를 사용하여 Java 응용 프로그램에서 확인됩니다. | | | | |
| `[ConnectionURL]` | AMQP 브로커에 필요한 정보를 JMS 라이브러리에 제공하는 URL입니다. | | | | |

연결 URL의 형식은 다음과 같습니다.

```
amqps://[username]:[password]@[namespace].servicebus.windows.net
```

여기서 `[namespace]`, `[username]` 및 `[password]`은(는) 다음과 같은 의미가 있습니다.

| 이름 | 의미 | | | | |
|---------------|--------------------------------------------------------------------------------|---|---|---|---|
| `[namespace]` | [Azure 클래식 포털][]에서 얻은 서비스 버스 네임스페이스입니다. | | | | |
| `[username]` | [Azure 클래식 포털][]에서 얻은 서비스 버스 발급자 이름입니다. | | | | |
| `[password]` | [Azure 클래식 포털][]에서 얻은 URL 인코딩된 형식의 서비스 버스 발급자 키입니다. | | | | |

> [AZURE.NOTE] 수동으로 암호를 URL 인코딩해야 합니다. 유용한 URL 인코딩 유틸리티는 [http://www.w3schools.com/tags/ref\_urlencode.asp](http://www.w3schools.com/tags/ref_urlencode.asp)에서 사용할 수 있습니다.

예를 들어 포털에서 얻은 정보가 다음과 같다고 가정합니다.

| 네임스페이스: | test.servicebus.windows.net |
|--------------|----------------------------------------------|
| 발급자 이름: | owner |
| 발급자 키: | abcdefg |

이 경우 `SBCONNECTIONFACTORY`라는 **ConnectionFactory** 개체를 정의하기 위해 구성 문자열이 다음과 같이 표시됩니다.

```
connectionfactory.SBCONNECTIONFACTORY = amqps://owner:abcdefg@test.servicebus.windows.net
```

#### destination 구성

Qpid 속성 파일 JNDI 공급자에서 destination을 정의하는 항목의 형식은 다음과 같습니다.

```
queue.[jndi_name] = [physical_name]
topic.[jndi_name] = [physical_name]
```

여기서 `[jndi\_name]` 및 `[physical\_name]`은 다음과 같은 의미가 있습니다.

| 이름 | 의미 |
|-------------------|------------------------------------------------------------------------------------------------------------------------------------------|
| `[jndi\_name]` | destination의 논리적 이름입니다. 이 이름은 JNDI `IntialContext.lookup()` 메서드를 사용하여 Java 응용 프로그램에서 확인됩니다. |
| `[physical\name]` | 응용 프로그램이 메시지를 보내거나 받는 서비스 버스 엔터티의 이름입니다. |

다음 사항에 유의하세요.

- `[physical\name]` 값은 서비스 버스 큐 또는 토픽이 될 수 있습니다.
- 서비스 버스 토픽 구독에서 받는 경우 JNDI에 지정된 물리적 이름은 토픽 이름이어야 합니다. 구독 이름은 JMS 응용 프로그램 코드에서 지속형 구독을 만들 때 제공됩니다.
- 서비스 버스 토픽 구독을 JMS 큐로 처리하는 것도 가능합니다. 이 방법의 장점은 다음과 같습니다. 동일한 수신자 코드를 큐와 토픽 구독에 대해 사용할 수 있으며 모든 주소 정보(항목 및 구독 이름)는 속성 파일에서 구체화됩니다.
- 서비스 버스 토픽 구독을 JMS 큐로 처리하려면 속성 파일의 항목이 양식 `queue.[jndi\_name] = [topic\_name]/Subscriptions/[subscription\_name]`이어야 합니다.|

"topic1"이라는 서비스 버스 토픽에 매핑하는 “TOPIC”이라는 논리 JMS 대상을 정의하려면 속성 파일의 항목은 다음과 같아야 합니다.

```
topic.TOPIC = topic1
```

### JMS를 사용하여 메시지 전송

다음 코드는 서비스 버스 토픽에 메시지를 전송하는 방법을 보여 줍니다. `SBCONNECTIONFACTORY` 및 `TOPIC`은 이전 섹션에서 설명한대로 **servicebus.properties** 구성 파일에서 정의된 것으로 간주됩니다.

```
Hashtable<String, String> env = new Hashtable<String, String>(); 
env.put(Context.INITIAL_CONTEXT_FACTORY, 
        "org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory"); 
env.put(Context.PROVIDER_URL, "servicebus.properties"); 
 
InitialContext context = new InitialContext(env); 
 
ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCONNECTIONFACTORY");
Topic topic = (Topic) context.lookup("TOPIC");
Connection connection = cf.createConnection();
Session session = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);
MessageProducer producer = session.createProducer(topic);
TextMessage message = session.createTextMessage("This is a text string"); 
producer.send(message);
```

### JMS를 사용하여 메시지 수신

다음 코드는 서비스 버스 토픽 구독에서 메시지를 수신하는 `how`을 보여 줍니다. `SBCONNECTIONFACTORY` 및 TOPIC은 이전 섹션에서 설명한대로 **servicebus.properties** 구성 파일에서 정의된 것으로 간주됩니다. 또한 구독 이름은 `subscription1`로 간주됩니다.

```
Hashtable<String, String> env = new Hashtable<String, String>(); 
env.put(Context.INITIAL_CONTEXT_FACTORY, 
        "org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory"); 
env.put(Context.PROVIDER_URL, "servicebus.properties"); 
 
InitialContext context = new InitialContext(env);

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCONNECTIONFACTORY");
Topic topic = (Topic) context.lookup("TOPIC");
Connection connection = cf.createConnection();
Session session = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);
TopicSubscriber subscriber = session.createDurableSubscriber(topic, "subscription1");
connection.start();
Message message = messageConsumer.receive();
```

### 강력한 응용 프로그램을 구축하기 위한 지침

JMS 사양은 이러한 예외를 처리하도록 API 메서드 및 응용 프로그램 코드의 예외 계약을 작성하는 방법을 정의합니다. 예외 처리와 관련된 다른 고려할 사항은 다음과 같습니다.

-   **connection.setExceptionListener**를 사용하여 JMS 연결로 **ExceptionListener**를 등록합니다. 이렇게 하면 클라이언트에게 문제를 비동기적으로 알려줍니다. 이 알림은 메시지를 사용하는 연결은 연결 실패를 알 수 있는 다른 방법이 없으므로 특히 중요합니다. 기본 AMQP 연결, 세션 또는 링크에 문제가 있을 경우 **ExceptionListener**가 호출됩니다. 이 경우 응용 프로그램은 **JMS 연결**, **세션**, **MessageProducer** 및 **MessageConsumer** 개체를 처음부터 다시 만들어야 합니다.

-   메시지가 **MessageProducer**에서 서비스 버스 엔터티로 성공적으로 전송되었는지 확인하려면 응용 프로그램이 **qpid.sync\_publish** 시스템 속성 집합으로 구성되었는지 확인합니다. 응용 프로그램을 시작할 때 명령줄에서 설정된 **-Dqpid.sync\_publish=true** Java VM 옵션으로 프로그램을 시작하여 실행할 수 있습니다. 이 옵션 설정은 서비스 버스에서 메시지 수락했다는 확인을 수신할 때까지 전송 호출로부터 반환되지 않도록 라이브러리를 구성합니다. 전송 작업 중에 문제가 발생하는 경우 **JMSException**이 발생합니다. 다음 두 가지 가능한 원인이 있습니다.
	1. 서비스 버스가 전송되는 특정 메시지를 거부하는 방식으로 인해 문제가 발생한 경우 **MessageRejectedException** 예외가 발생합니다. 이 오류는 일시적이거나 메시지의 문제로 인해 발생됩니다. 일부 백오프 논리로 작업을 다시 시도하는 것이 좋습니다. 문제가 지속되면 메시지는 로컬로 로그온한 오류와 함께 중단되어야 합니다. 이 경우 **JMS 연결**, **세션** 또는 **MessageProducer** 개체를 다시 만들 필요가 없습니다. 
	2. 서비스 버스가 AMQP 링크를 닫는 것으로 인해 문제가 발생한 경우 **InvalidDestinationException** 예외가 발생합니다. 이는 일시적인 문제 또는 삭제되는 메시지 엔터티로 인한 문제일 수 있습니다. 두 경우 모두 **JMS 연결**, **세션** 및 **MessageProducer** 개체를 다시 만들어야 합니다. 오류 상태가 일시적이었다면 이 작업은 결국 성공적으로 수행될 것입니다. 엔터티가 삭제된 경우 오류가 영구적이 됩니다.

## .NET과 JMS 간의 메시징

### 메시지 본문

JMS는 다섯 가지 다른 메시지 유형 **BytesMessage**, **MapMessage**, **ObjectMessage**, **StreamMessage** 및 **TextMessage**를 정의합니다. 서비스 버스 .NET API에는 단일 메시지 유형, [BrokeredMessage][]가 있습니다.

#### JMS에서 서비스 버스 .NET API

다음 섹션에서는 .NET에서 각 JMS 메시지 형식의 메시지를 사용하는 방법을 보여 줍니다. **ObjectMessage**의 본문이 .NET 응용 프로그램에서 해석할 수 없는 Java 프로그래밍 언어의 직렬화 가능 개체를 포함하므로 **ObjectMessage** 예는 포함되지 않습니다.

##### BytesMessage

다음 코드는 서비스 버스 .NET API를 사용하여 **BytesMessage** 개체의 본문을 사용하는 방법을 보여 줍니다.

```
Stream stream = message.GetBody<Stream>();
int streamLength = (int)stream.Length;

byte[] byteArray = new byte[streamLength];
stream.Read(byteArray, 0, streamLength);

Console.WriteLine("Length = " + streamLength);
for (int i = 0; i < stream.Length; i++)
{
  Console.Write("[" + (sbyte) byteArray[i] + "]");
}
```

##### MapMessage

다음 코드는 서비스 버스 .NET API를 사용하여 **MapMessage** 개체의 본문을 사용하는 방법을 보여 줍니다. 이 코드는 각 요소의 이름 및 값을 표시하는 맵 요소를 통해 반복합니다.

```
Dictionary<String, Object> dictionary = message.GetBody<Dictionary<String, Object>>();

foreach (String mapItemName in dictionary.Keys)
{
  Object mapItemValue = null;
  if (dictionary.TryGetValue(mapItemName, out mapItemValue))
  {
    Console.WriteLine(mapItemName + ":" + mapItemValue);
  }
}
```

##### StreamMessage

다음 코드는 서비스 버스 .NET API를 사용하여 **StreamMessage** 개체의 본문을 사용하는 방법을 보여 줍니다. 이 코드는 해당 유형과 함께 스트림에서 각 항목을 나열합니다.

```
List<Object> list = message.GetBody<List<Object>>();

foreach (Object item in list)
{
  Console.WriteLine(item + " (" + item.GetType() + ")");
}
```

##### TextMessage

다음 코드는 서비스 버스 .NET API를 사용하여 **TextMessage** 개체의 본문을 사용하는 방법을 보여 줍니다. 이 코드는 메시지의 본문에 포함 된 텍스트 문자열을 표시합니다.

```
Console.WriteLine("Text: " + message.GetBody<String>());
```

#### 서비스 버스 .NET API에서 JMS

다음 섹션은 .NET 응용 프로그램에서 다른 각 JMS 메시지 유형의 JMS에서 수신된 메시지를 만드는 방법을 보여 줍니다. **ObjectMessage**의 본문이 .NET 응용 프로그램에서 해석할 수 없는 Java 프로그래밍 언어의 직렬화 가능 개체를 포함하므로 **ObjectMessage** 예는 포함되지 않습니다.

##### BytesMessage

다음 코드는 JMS 클라이언트에서 **BytesMessage**로 수신된 .NET의 [BrokeredMessage][] 개체를 만드는 방법을 보여 줍니다.

```
byte[] bytes = { 33, 12, 45, 33, 12, 45, 33, 12, 45, 33, 12, 45 };
message = new BrokeredMessage(bytes);
```

##### StreamMessage

다음 코드는 JMS 클라이언트에서 **StreamMessage**로 수신된 .NET의 [BrokeredMessage][] 개체를 만드는 방법을 보여 줍니다.

```
List<Object> list = new List<Object>();
list.Add("String 1");
list.Add("String 2");
list.Add("String 3");
list.Add((double)3.14159);
message = new BrokeredMessage(list);
```

##### TextMessage

다음 코드는 서비스 버스 .NET API를 사용하여 **TextMessage**의 본문을 사용하는 방법을 보여 줍니다. 이 코드는 메시지의 본문에 포함 된 텍스트 문자열을 표시합니다.

```
message = new BrokeredMessage("this is a text string");
```

### 응용 프로그램 속성

####JMS에서 서비스 버스 .NET API

JMS 메시지는 다음과 같은 형식의 응용 프로그램 속성을 지원합니다. **boolean**, **byte**, **short**, **int**, **long**, **float**, **double** 및 **String** 다음 Java 코드는 이러한 각 형식의 속성을 사용하여 메시지에서 속성을 설정하는 방법을 보여 줍니다.

```
message.setBooleanProperty("TestBoolean", true); 
message.setByteProperty("TestByte", (byte) 33); 
message.setDoubleProperty("TestDouble", 3.14159D); 
message.setFloatProperty("TestFloat", 3.13159F); 
message.setIntProperty("TestInt", 100); 
message.setStringProperty("TestString", "Service Bus");
```

서비스 버스 .NET API에서 메시지 응용 프로그램 속성은 [BrokeredMessage][]의 **속성** 컬렉션에 전달됩니다. 다음 코드는 JMS 클라이언트로부터 받은 메시지의 응용 프로그램 속성을 읽는 방법을 보여 줍니다.

```
if (message.Properties.Keys.Count > 0)
{
  foreach (string name in message.Properties.Keys)
  {
    Object value = message.Properties[name];
    Console.WriteLine(name + ": " + value + " (" + value.GetType() + ")" );
  }
  Console.WriteLine();
}
```

다음 표는 JMS 속성 형식을 .NET 속성 형식으로 매핑하는 방법을 보여 줍니다.

| JMS 속성 형식 | .NET 속성 형식 |
|-------------------|--------------------|
| Byte | sbyte |
| Integer | int |
| Float | float |
| Double | double |
| Boolean | bool |
| String | string |

[BrokeredMessage][] 형식은 다음과 같은 형식의 응용 프로그램 속성을 지원합니다. **byte**, **sbyte**, **char**, **short**, **ushort**, **int**, **uint**, **long**, **ulong**, **float**, **double**, **decimal**, **bool**, **Guid**, **string**, **Uri**, **DateTime**, **DateTimeOffset** 및 **TimeSpan** 다음 .NET 코드는 이러한 각 형식의 속성을 사용하여 [BrokeredMessage][] 개체에서 속성을 설정하는 방법을 보여 줍니다.

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

다음 Java 코드는 서비스 버스 .NET 클라이언트부터 받은 메시지의 응용 프로그램 속성을 읽는 방법을 보여 줍니다.

```
Enumeration propertyNames = message.getPropertyNames(); 
while (propertyNames.hasMoreElements()) 
{ 
  String name = (String) propertyNames.nextElement(); 
  Object value = message.getObjectProperty(name); 
  System.out.println(name + ": " + value + " (" + value.getClass() + ")"); 
}
```

다음 표는 .NET 속성 형식을 JMS 속성 형식으로 매핑하는 방법을 보여 줍니다.

| .NET 속성 형식 | JMS 속성 형식 | 참고 |
|--------------------|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| byte | UnsignedByte | - | 
| sbyte | Byte | - | 
| char | Character | - | 
| short | Short | - | 
| ushort | UnsignedShort | - | 
| int | Integer | - | 
| uint | UnsignedInteger | - |
| long | Long | - |
| ulong | UnsignedLong | - |
| float | Float | - |
| double | Double | - |
| decimal | BigDecimal | - |
| bool | Boolean | - |
| Guid | UUID | - |
| string | String | - |
| DateTime | Date | - |
| DateTimeOffset | DescribedType | AMQP 형식으로 매핑된 DateTimeOffset.UtcTicks:<type name=”datetime-offset” class=restricted source=”long”> <descriptor name=”com.microsoft:datetime-offset” /></type> |
| TimeSpan | DescribedType | AMQP 형식으로 매핑된 Timespan.Ticks:<type name=”timespan” class=restricted source=”long”> <descriptor name=”com.microsoft:timespan” /></type> |
| Uri | DescribedType | AMQP 형식으로 매핑된 Uri.AbsoluteUri:<type name=”uri” class=restricted source=”string”> <descriptor name=”com.microsoft:uri” /></type> |

### 표준 헤더

다음 표는 JMS 표준 헤더 및 [BrokeredMessage][] 표준 속성을 AMQP 1.0을 사용하여 매핑하는 방법을 보여 줍니다.

#### JMS에서 서비스 버스 .NET API

| JMS | 서비스 버스 .NET | 참고 |
|------------------|--------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| JMSCorrelationID | Message.CorrelationID | - |
| JMSDeliveryMode | 현재 사용할 수 없음 | 서비스 버스는 지정된 것과 관계 없이 지속적 메시지만 지원합니다(예: DeliveryMode.PERSISTENT). |
| JMSDestination | Message.To | - |
| JMSExpiration | Message. TimeToLive | Conversion |
| JMSMessageID | Message.MessageID | 기본적으로 JMSMessageID는 AMQP 메시지에서 이진 형식으로 인코딩됩니다. 이진 메시지 ID를 받으면 .NET 클라이언트 라이브러리는 바이트의 유니코드 값 기반 문자열 표현으로 변환합니다. JMS 라이브러리를 전환하여 문자열 메시지 ID를 사용하려면 "binary-messageid=false" 문자열을 JNDI ConnectionURL의 쿼리 매개 변수에 추가합니다. 예: “amqps://[username]:[password]@[namespace].servicebus.windows.net? binary-messageid=false”. |
| JMSPriority | 현재 사용 불가능 | 서비스 버스는 메시지 우선 순위를 제공하지 않습니다. |
| JMSRedelivered | 현재 사용 불가능 | - |
| JMSReplyTo | Message. ReplyTo | - |
| JMSTimestamp | Message.EnqueuedTimeUtc | Conversion |
| JMSType | Message.Properties[“jms-type”] | - |

#### 서비스 버스 .NET API에서 JMS

| 서비스 버스 .NET | JMS | 참고 |
|-------------------------|------------------|-------------------------|
| ContentType | - | 현재 사용 불가능 |
| CorrelationId | JMSCorrelationID | - |
| EnqueuedTimeUtc | JMSTimestamp | Conversion |
| Label | n/a | 현재 사용 불가능 |
| MessageId | JMSMessageID | - |
| ReplyTo | JMSReplyTo | - |
| ReplyToSessionId | n/a | 현재 사용 불가능 |
| ScheduledEnqueueTimeUtc | n/a | 현재 사용 불가능 |
| SessionId | n/a | 현재 사용 불가능 |
| TimeToLive | JMSExpiration | Conversion |
| To | JMSDestination | - |

## 지원되지 않는 기능 및 제한

서비스 버스와 함께 JMS over AMQP 1.0을 사용하는 경우 다음과 같은 제한 사항이 있습니다.

-   세션당 하나의 **MessageProducer** 또는 **MessageConsumer**만 허용됩니다. 응용 프로그램에서 **MessageProducers** 또는 **MessageConsumers** 개체를 여러 개 만들어야 하는 경우 각 항목에 대한 전용 세션을 만듭니다.

-   휘발성 토픽 구독은 현재 지원되지 않습니다.

-   **MessageSelector** 개체는 지원되지 않습니다.

-   임시 대상인 **TemporaryQueue** 또는 **TemporaryTopic**과 이러한 대상을 사용하는 **QueueRequestor** 및 **TopicRequestor** API는 지원되지 않습니다.

-   트랜잭션 처리된 세션은 지원되지 않습니다.

-   분산된 트랜잭션은 지원되지 않습니다.

## 다음 단계

자세히 알아볼 준비가 되셨습니까? 다음 링크를 방문하세요.

- [서비스 버스 AMQP 개요]
- [Windows Server용 서비스 버스의 AMQP]

[Windows Server용 서비스 버스의 AMQP]: https://msdn.microsoft.com/library/dn574799.aspx
[BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx

[서비스 버스 AMQP 개요]: service-bus-amqp-overview.md
[Azure 클래식 포털]: http://manage.windowsazure.com

<!---HONumber=AcomDC_0128_2016-->