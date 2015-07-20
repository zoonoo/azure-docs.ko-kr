<properties 
	pageTitle=".NET 서비스 버스 API와 함께 AMQP 1.0을 사용하는 방법 - Azure" 
	description="Azure .NET Service Bus API와 함께 AMQP(Advanced Message Queuing Protocol) 1.0을 사용하는 방법에 대해 알아봅니다." 
	services="service-bus" 
	documentationCenter=".net" 
	authors="sethmanheim" 
	manager="timlt" 
	editor="mattshel"/>

<tags 
	ms.service="service-bus" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="07/02/2015" 
	ms.author="sethm"/>

# 서비스 버스 .NET API와 함께 AMQP 1.0을 사용하는 방법

AMQP(Advanced Message Queuing Protocol) 1.0은 효율성과 안정성이 뛰어난 유선 수준 메시징 프로토콜로, 이를 통해 여러 플랫폼 간에 상호 운용되는 강력한 메시징 응용 프로그램을 만들 수 있습니다.

서비스 버스에서 AMQP 1.0이 지원되므로 효율적인 이진 프로토콜을 사용하여 다양한 플랫폼에서 큐 및 게시/구독 조정된 메시징 기능을 이용할 수 있습니다. 뿐만 아니라 여러 언어, 프레임워크 및 운영 체제가 혼합되어 사용된 구성 요소로 이루어진 응용 프로그램을 만들 수 있습니다.

이 방법 가이드에서는 서비스 버스 .NET API를 사용하여 .NET 응용 프로그램에서 서비스 버스 조정된 메시징 기능(큐 및 게시/구독 항목)을 사용하는 방법에 대해 설명합니다. 표준 JMS(Java Message Service) API를 사용하여 동일한 작업을 수행하는 방법을 설명하는 동반 방법 가이드도 제공됩니다. AMQP 1.0을 사용한 플랫폼 간 메시징에 대해 알아보려면 이 두 가지 가이드를 함께 사용할 수 있습니다.

## 서비스 버스 시작

이 가이드에서는 사용자가 "queue1"이라는 큐가 포함된 서비스 버스 네임스페이스를 이미 가지고 있다고 가정합니다. 가지고 있지 않은 사용자는 [Azure 관리 포털](http://manage.windowsazure.com)을 사용하여 네임스페이스와 큐를 만들 수 있습니다. 서비스 버스 네임스페이스와 큐를 만드는 방법에 대한 자세한 내용은 "[서비스 버스 큐를 사용하는 방법](https://azure.microsoft.com/develop/net/how-to-guides/service-bus-queues/)"이라는 제목의 방법 가이드를 참조하십시오.

## 서비스 버스 SDK 다운로드

AMQP 1.0 지원은 서비스 버스 SDK 버전 2.1 이상에서 이용할 수 있습니다. NuGet([http://nuget.org/packages/WindowsAzure.ServiceBus/](http://nuget.org/packages/WindowsAzure.ServiceBus/))에서 최신 SDK를 다운로드할 수 있습니다.

## .NET 응용 프로그램 코딩

기본적으로 서비스 버스 .NET 클라이언트 라이브러리는 전용 SOAP 기반 프로토콜을 사용하여 서비스 버스 서비스와 통신합니다. 기본 프로토콜 대신 AMQP 1.0을 사용하려면 다음 섹션에서 설명한 대로 서비스 버스 연결 문자열에서 이를 명시적으로 구성해야 합니다. AMQP 1.0을 사용하는 경우 이러한 변경 사항 외에는 응용 프로그램 코드가 기본적으로 변경되지 않습니다.

현재 릴리스에는 AMQP 사용 시 지원되지 않는 몇 가지 API 기능이 있습니다. 지원되지 않는 이러한 기능은 뒤에 나오는 "지원되지 않는 기능 및 제한" 섹션에 나열되어 있습니다. AMQP를 사용하는 경우 몇 가지 고급 구성 설정도 다른 의미를 가집니다. 이 짧은 방법 가이드에서는 이러한 설정이 사용되지 않지만, 자세한 내용은 [서비스 버스 AMQP 1.0 개발자 가이드](http://msdn.microsoft.com/library/jj841071.aspx)에서 알아볼 수 있습니다.

### App.config를 통해 구성

응용 프로그램에서는 App.config 구성 파일을 사용하여 설정을 저장하는 것이 바람직합니다. 서비스 버스 응용 프로그램의 경우 App.config를 사용하여 서비스 버스 **ConnectionString**을 저장할 수 있습니다. 또한 이 샘플 응용 프로그램은 App.config를 사용하여 자체에서 사용하는 서비스 버스 메시징 엔터티의 이름을 저장합니다.

샘플 App.config 파일은 다음과 같습니다.

	<?xml version="1.0" encoding="utf-8" ?>
	<configuration>
	  	<appSettings>
		    <add key="Microsoft.ServiceBus.ConnectionString"
        	     value="Endpoint=sb://[namespace].servicebus.windows.net;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp" />
	    	<add key="EntityName" value="queue1" />
  		</appSettings>
	</configuration>

### 서비스 버스 연결 문자열 구성

**Microsoft.ServiceBus.ConnectionString** 설정의 값은 서비스 버스에 대한 연결을 구성하는 데 사용되는 서비스 버스 연결 문자열입니다. 형식은 다음과 같습니다.

	Endpoint=sb://[namespace].servicebus.windows.net;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp

여기서 [namespace] 및 [SAS key]는 Azure 관리 포털에서 가져옵니다. 자세한 내용은 [서비스 버스 큐를 사용하는 방법]을 참조하세요.

AMQP를 사용하면 연결 문자열이 ";TransportType=Amqp"로 추가됩니다. 이는 AMQP 1.0을 사용하여 서비스 버스에 연결하도록 클라이언트 라이브러리에 알려줍니다.

### 엔터티 이름 구성

이 샘플 응용 프로그램은 응용 프로그램이 메시지를 교환하는 큐의 이름을 구성하기 위해 App.config 파일의 **appSettings** 섹션에 있는 `EntityName` 설정을 사용합니다.

### 서비스 버스 큐를 사용하는 간단한 .NET 응용 프로그램

다음은 서비스 버스 큐와 메시지를 보내고 받는 예제입니다.

	// SimpleSenderReceiver.cs
	
	using System;
	using System.Configuration;
	using System.Threading;
	using Microsoft.ServiceBus.Messaging;
	
	namespace SimpleSenderReceiver
	{
	    class SimpleSenderReceiver
	    {
	        private static string connectionString;
	        private static string entityName;
	        private static Boolean runReceiver = true;
	        private MessagingFactory factory;
	        private MessageSender sender;
	        private MessageReceiver receiver;
	        private MessageListener messageListener;
	        private Thread listenerThread;
	
	        static void Main(string[] args)
	        {
	            try
	            {
	                if ((args.Length > 0) && args[0].ToLower().Equals("sendonly"))
	                    runReceiver = false;
	                
	                string ConnectionStringKey = "Microsoft.ServiceBus.ConnectionString";
	                string entityNameKey = "EntityName";
	                entityName = ConfigurationManager.AppSettings[entityNameKey];
	                connectionString = ConfigurationManager.AppSettings[ConnectionStringKey];
	                SimpleSenderReceiver simpleSenderReceiver = new SimpleSenderReceiver();
	
	                Console.WriteLine("Press [enter] to send a message. " +
	                    "Type 'exit' + [enter] to quit.");
	
	                while (true)
	                {
	                    string s = Console.ReadLine();
	                    if (s.ToLower().Equals("exit"))
	                    {
	                        simpleSenderReceiver.Close();
	                        System.Environment.Exit(0);
	                    }
	                    else
	                        simpleSenderReceiver.SendMessage();
	                }
	            }
	            catch (Exception e)
	            {
	                Console.WriteLine("Caught exception: " + e.Message);
	            }
	        }
	
	        public SimpleSenderReceiver()
	        {
	            factory = MessagingFactory.CreateFromConnectionString(connectionString);
	            sender = factory.CreateMessageSender(entityName);
	
	            if (runReceiver)
	            {
	                receiver = factory.CreateMessageReceiver(entityName);
	                messageListener = new MessageListener(receiver);
	                listenerThread = new Thread(messageListener.Listen);
	                listenerThread.Start();
	            }
	        }
	
	        public void Close()
	        {
	            messageListener.RequestStop();
	            listenerThread.Join();
	            factory.Close();
	        }
	
	        private void SendMessage()
	        {
	            BrokeredMessage message = 
	                new BrokeredMessage("Test AMQP message from .NET");
	            sender.Send(message);
	            Console.WriteLine("Sent message with MessageID = " 
	                + message.MessageId);
	        }
	
	    }
	
	    public class MessageListener
	    {
	        private MessageReceiver messageReceiver;
	        public MessageListener(MessageReceiver receiver)
	        {
	            messageReceiver = receiver;
	        }
	
	        public void Listen()
	        {
	            while (!_shouldStop)
	            {
	                try
	                {
	                    BrokeredMessage message = 
	                        messageReceiver.Receive(new TimeSpan(0, 0, 10));
	                    if (message != null)
	                    {
	                        Console.WriteLine("Received message with MessageID = " + 
	                            message.MessageId);
	                        message.Complete();
	                    }
	                }
	                catch (Exception e)
	                {
	                    Console.WriteLine("Caught exception: " + e.Message);
	                    break;
	                }
	            }
	        }
	
	        public void RequestStop()
	        {
	            _shouldStop = true;
	        }
	
	        private volatile bool _shouldStop;
	    }
	}

### 응용 프로그램 실행

응용 프로그램을 실행하면 양식의 출력이 생성됩니다.

	> SimpleSenderReceiver.exe
	Press [enter] to send a message. Type 'exit' + [enter] to quit.
	
	Sent message with MessageID = fb7f5d3733704e4ba4bd55f759d9d7cf
	Received message with MessageID = fb7f5d3733704e4ba4bd55f759d9d7cf
	
	Sent message with MessageID = d00e2e088f06416da7956b58310f7a06
	Received message with MessageID = d00e2e088f06416da7956b58310f7a06
	
	Received message with MessageID = f27f79ec124548c196fd0db8544bca49
	Sent message with MessageID = f27f79ec124548c196fd0db8544bca49
	exit

## JMS와 .NET 간의 크로스 플랫폼 메시징

이 가이드에서는 .NET을 사용하여 서비스 버스로 메시지를 보내는 방법 및 .NET을 사용하여 이러한 메시지를 받는 방법에 대해 설명했습니다. 그러나 AMQP 1.0의 주요 이점 중 하나는 다른 언어로 작성된 구성 요소로 응용 프로그램을 빌드하여 안정적이며 완전히 신뢰할 수 있는 상태로 메시지를 교환할 수 있다는 것입니다.

위에서 설명한 샘플 .NET 응용 프로그램 및 동반 가이드([서비스 버스 및 AMQP 1.0과 함께 JMS(Java Message Service) API를 사용하는 방법](service-bus-java-how-to-use-jms-api-amqp.md))에서 제공하는 유사한 Java 응용 프로그램을 사용하면 .NET과 Java 간에 메시지를 교환할 수 있습니다.

서비스 버스 및 AMQP 1.0을 사용하는 플랫폼 간 메시징의 세부 정보를 알아보려면 [서비스 버스 AMQP 1.0 개발자 가이드](http://msdn.microsoft.com/library/azure/jj841071.aspx)를 참조하십시오.

### JMS에서 .NET으로

JMS에서 .NET으로의 메시징을 시연하려면:

* 명령줄 인수 없이 .NET 샘플 응용 프로그램을 시작합니다.
* "sendonly" 명령줄 인수로 Java 샘플 응용 프로그램을 시작합니다. 이 모드에서는 응용 프로그램이 큐에서 메시지를 받지 않고 보내기만 합니다.
* Java 응용 프로그램 콘솔에서 **Enter** 키를 몇 번 누릅니다. 그러면 메시지가 전송됩니다.
* .NET 응용 프로그램에서 이러한 메시지를 수신합니다.

### JMS 응용 프로그램의 출력

	> java SimpleSenderReceiver sendonly
	Press [enter] to send a message. Type 'exit' + [enter] to quit.
	Sent message with JMSMessageID = ID:4364096528752411591
	Sent message with JMSMessageID = ID:459252991689389983
	Sent message with JMSMessageID = ID:1565011046230456854
	exit

### .NET 응용 프로그램의 출력

	> SimpleSenderReceiver.exe	
	Press [enter] to send a message. Type 'exit' + [enter] to quit.
	Received message with MessageID = 4364096528752411591
	Received message with MessageID = 459252991689389983
	Received message with MessageID = 1565011046230456854
	exit

## .NET에서 JMS로

.NET에서 JMS로의 메시징을 시연하려면:

* "sendonly" 명령줄 인수로 .NET 샘플 응용 프로그램을 시작합니다. 이 모드에서는 응용 프로그램이 큐에서 메시지를 받지 않고 보내기만 합니다.
* 명령줄 인수 없이 Java 샘플 응용 프로그램을 시작합니다.
* .NET 응용 프로그램 콘솔에서 **Enter** 키를 몇 번 누릅니다. 그러면 메시지가 전송됩니다.
* Java 응용 프로그램에서 이러한 메시지를 수신합니다.

#### .NET 응용 프로그램의 출력

	> SimpleSenderReceiver.exe sendonly
	Press [enter] to send a message. Type 'exit' + [enter] to quit.
	Sent message with MessageID = d64e681a310a48a1ae0ce7b017bf1cf3	
	Sent message with MessageID = 98a39664995b4f74b32e2a0ecccc46bb
	Sent message with MessageID = acbca67f03c346de9b7893026f97ddeb
	exit


#### JMS 응용 프로그램의 출력

	> java SimpleSenderReceiver	
	Press [enter] to send a message. Type 'exit' + [enter] to quit.
	Received message with JMSMessageID = ID:d64e681a310a48a1ae0ce7b017bf1cf3
	Received message with JMSMessageID = ID:98a39664995b4f74b32e2a0ecccc46bb
	Received message with JMSMessageID = ID:acbca67f03c346de9b7893026f97ddeb
	exit

## 지원되지 않는 기능 및 제한

.NET 서비스 버스 API의 다음 기능은 현재 AMQP 사용 시 지원되지 않습니다.

* 트랜잭션
* 전송 대상을 통해 보내기
* 메시지 시퀀스 번호로 받기
* 메시지 및 세션 탐색
* 세션 상태
* 일괄 처리 기반 API
* 확장된 받기
* 구독 규칙의 런타임 조작
* 세션 잠금 갱신
* 몇 가지 사소한 동작의 차이

자세한 내용은 [서비스 버스 AMQP 1.0 개발자 가이드](http://msdn.microsoft.com/library/azure/jj841071.aspx)를 참조하십시오. 이 항목에는 지원되지 않는 API의 자세한 목록이 포함되어 있습니다.

## 요약

이 방법 가이드에서는 AMQP 1.0과 서비스 버스 .NET API를 사용하여 .NET 응용 프로그램에서 서비스 버스 조정된 메시징 기능(큐 및 게시/구독 항목)에 액세스하는 방법에 대해 설명했습니다.

Java, C, Python, PHP 등의 다른 언어에서도 서비스 버스 AMQP 1.0을 사용할 수 있습니다. 이러한 언어로 빌드한 구성 요소는 서비스 버스의 AMQP 1.0을 사용하여 안정적이며 완전히 신뢰할 수 있는 상태로 메시지를 교환할 수 있습니다. 자세한 내용은 [서비스 버스 AMQP 1.0 개발자 가이드](http://msdn.microsoft.com/library/azure/jj841071.aspx)를 참조하세요.

## 다음 단계

* [Azure 서비스 버스의 AMQP 1.0 지원](service-bus-amqp-overview.md)
* [서비스 버스 및 AMQP 1.0과 함께 JMS(Java Message Service) API를 사용하는 방법](service-bus-java-how-to-use-jms-api-amqp.md)
* [서비스 버스 AMQP 1.0 개발자 가이드](http://msdn.microsoft.com/library/azure/jj841071.aspx)
* [서비스 버스 큐를 사용하는 방법](service-bus-dotnet-how-to-use-queues.md)
 

<!---HONumber=July15_HO2-->