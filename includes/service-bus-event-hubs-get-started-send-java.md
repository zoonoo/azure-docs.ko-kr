## 이벤트 허브에 메시지 보내기

이벤트 허브에 대한 Java 클라이언트 라이브러리는 [Maven 중앙 리포지토리](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22)의 Maven 프로젝트에 사용할 수 있으며 Maven 프로젝트 파일 내의 다음 종속성 선언을 사용하여 참조할 수 있습니다.

``` XML
<dependency>
	<groupId>com.microsoft.azure</groupId>
	<artifactId>azure-eventhubs</artifactId>
	<version>{VERSION}</version>
</dependency>
```
 
다양한 유형의 빌드 환경을 위해, [Maven 중앙 리포지토리](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22) 또는 [GitHub의 릴리스 배포 지점](https://github.com/Azure/azure-event-hubs/releases)에서 최근에 릴리스된 JAR 파일을 명시적으로 가져올 수 있습니다.

단순 이벤트 게시자의 경우 이벤트 허브 클라이언트 클래스에 대한 *com.microsoft.azure.eventhubs* 패키지와 유틸리티 클래스(예: Azure 서비스 버스 메시징 클라이언트와 공유되는 일반적인 예외)에 대한 *com.microsoft.azure.servicebus* 패키지를 가져옵니다.

다음 샘플에서는 먼저 즐겨 찾는 Java 개발 환경에서 콘솔/셸 응용 프로그램에 대한 새 Maven 프로젝트를 만듭니다. 이 클래스를 ```Send```라고 합니다.

``` Java

import java.io.IOException;
import java.nio.charset.*;
import java.util.*;
import java.util.concurrent.ExecutionException;

import com.microsoft.azure.eventhubs.*;
import com.microsoft.azure.servicebus.*;

public class Send
{
	public static void main(String[] args) 
			throws ServiceBusException, ExecutionException, InterruptedException, IOException
	{
```

네임스페이스 및 이벤트 허브 이름을 이벤트 허브를 만들 때 사용한 값으로 바꿉니다.

``` Java
	final String namespaceName = "----ServiceBusNamespaceName-----";
	final String eventHubName = "----EventHubName-----";
	final String sasKeyName = "-----SharedAccessSignatureKeyName-----";
	final String sasKey = "---SharedAccessSignatureKey----";
	ConnectionStringBuilder connStr = new ConnectionStringBuilder(namespaceName, eventHubName, sasKeyName, sasKey);
```

그런 후 문자열을 UTF-8 바이트 인코딩으로 전환하여 단일 이벤트를 만듭니다. 그런 다음 연결 문자열에서 새 이벤트 허브 클라이언트 인스턴스를 만들고 해당 메시지를 보냅니다.

``` Java 
				
	byte[] payloadBytes = "Test AMQP message from JMS".getBytes("UTF-8");
	EventData sendEvent = new EventData(payloadBytes);
	
	EventHubClient ehClient = EventHubClient.createFromConnectionStringSync(connStr.toString());
	ehClient.sendSync(sendEvent);
	}
}

``` 

<!---HONumber=AcomDC_0907_2016-->