## 이벤트 허브에 메시지 보내기
이 섹션에서는 이벤트 허브로 이벤트를 보내는 Java 콘솔 응용 프로그램을 작성합니다. 여기서는 [Apache Qpid 프로젝트](http://qpid.apache.org/)(영문)의 JMS AMQP 공급자를 사용합니다. 이는 [여기](../articles/service-bus/service-bus-java-how-to-use-jms-api-amqp.md)에 나와 있는 AMQP와 함께 서비스 버스 큐와 토픽을 사용하는 방법과 유사합니다. 자세한 내용은 [Qpid JMS 설명서](http://qpid.apache.org/releases/qpid-0.30/programming/book/QpidJMS.html) 및 [Java Messaging Service](http://www.oracle.com/technetwork/java/jms/index.html)를 참조하세요.

1. Eclipse에서 [Azure Toolkit for Eclipse](https://msdn.microsoft.com/library/azure/hh690946.aspx)를 설치합니다. 여기에는 Qpid JMS AMQP 클라이언트 라이브러리가 포함되어 있습니다.

2. Eclipse에서 **Sender**라는 Java 프로젝트를 새로 만듭니다.

3. Eclipse 패키지 탐색기에서 **Sender** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **속성**을 선택합니다. 대화 상자의 왼쪽 창에서 **Java 빌드 경로**를 클릭한 후, **라이브러리** 탭을 클릭하고 **라이브러리 추가** 단추를 클릭합니다. **Package for Apache Qpid Client Libraries for JMS(MS Open Tech 제공)**를 선택하고 **다음**, **마침**을 차례로 클릭합니다.

	![][8]

4. **servicebus.properties**라는 파일을 **Sender** 프로젝트의 루트에 만들고, 다음 콘텐츠를 포함합니다. 다음 값을 대체해야 합니다.
	- 이벤트 허브 이름
	- 네임스페이스 이름(일반적으로 `{event hub name}-ns`)
	- URL로 인코딩된 **SendRule** 키(이벤트 허브를 만들 때 이 키를 기록해 둠) [여기](http://www.w3schools.com/tags/ref_urlencode.asp)(영문)에서 URL로 인코드할 수 있습니다.

			# servicebus.properties - sample JNDI configuration

			# Register a ConnectionFactory in JNDI using the form:
			# connectionfactory.[jndi_name] = [ConnectionURL]
			connectionfactory.SBCF = amqps://SendRule:{Send Rule key}@{namespace name}.servicebus.windows.net/?sync-publish=false

			# Register some queues in JNDI using the form
			# queue.[jndi_name] = [physical_name]
			# topic.[jndi_name] = [physical_name]
			queue.EventHub = {event hub name}

5. **Sender**라는 클래스를 새로 만듭니다. 다음 `import` 문을 추가합니다.

		import java.io.BufferedReader;
		import java.io.IOException;
		import java.io.InputStreamReader;
		import java.io.UnsupportedEncodingException;
		import java.util.Hashtable;

		import javax.jms.BytesMessage;
		import javax.jms.Connection;
		import javax.jms.ConnectionFactory;
		import javax.jms.Destination;
		import javax.jms.JMSException;
		import javax.jms.MessageProducer;
		import javax.jms.Session;
		import javax.naming.Context;
		import javax.naming.InitialContext;
		import javax.naming.NamingException;

6. 그리고 다음 코드를 추가합니다.

		public static void main(String[] args) throws NamingException,
				JMSException, IOException, InterruptedException {
			// Configure JNDI environment
			Hashtable<String, String> env = new Hashtable<String, String>();
			env.put(Context.INITIAL_CONTEXT_FACTORY,
					"org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory");
			env.put(Context.PROVIDER_URL, "servicebus.properties");
			Context context = new InitialContext(env);

			ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");

			Destination queue = (Destination) context.lookup("EventHub");

			// Create Connection
			Connection connection = cf.createConnection();

			// Create sender-side Session and MessageProducer
			Session sendSession = connection.createSession(false,
					Session.AUTO_ACKNOWLEDGE);
			MessageProducer sender = sendSession.createProducer(queue);

			System.out.println("Press Ctrl-C to stop the sender process");
			System.out.println("Press Enter to start now");
			BufferedReader commandLine = new java.io.BufferedReader(
					new InputStreamReader(System.in));
			commandLine.readLine();

			while (true) {
				sendBytesMessage(sendSession, sender);
				Thread.sleep(200);
			}
		}

		private static void sendBytesMessage(Session sendSession, MessageProducer sender) throws JMSException, UnsupportedEncodingException {
	        BytesMessage message = sendSession.createBytesMessage();
	        message.writeBytes("Test AMQP message from JMS".getBytes("UTF-8"));
	        sender.send(message);
	        System.out.println("Sent message");
	    }



<!-- Links -->
[Azure Management Portal]: https://manage.windowsazure.com/


<!-- Images -->
[8]: ./media/service-bus-event-hubs-getstarted/create-sender-java1.png

<!---HONumber=July15_HO1-->