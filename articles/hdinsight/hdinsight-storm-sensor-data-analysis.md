<properties
   pageTitle="Apache Storm 및 HBase를 사용하여 센서 데이터 분석 | Microsoft Azure"
   description="가상 네트워크를 사용하여 Apache Storm에 연결하는 방법을 알아봅니다. HBase와 함께 Storm을 사용하여 이벤트 허브에서 센서 데이터를 처리하고 D3.js를 통해 이를 시각화합니다."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="java"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/23/2015"
   ms.author="larryfr"/>

# HDInsight(Hadoop)에서 Apache Storm, 이벤트 허브 및 HBase를 사용하여 센서 데이터 분석

HDInsight의 Apache Storm을 사용하여 Azure 이벤트 허브에서 센서 데이터를 처리하고, D3.js를 통해 이를 시각화하는 방법에 대해 알아봅니다. 또한 이 문서에서는 Azure 가상 네트워크를 사용하여 HDInsight의 Storm을 HDInsight의 HBase와 연결하고 토폴로지의 데이터를 HBase에 저장하는 방법에 대해서도 설명합니다.

## 필수 조건

* Azure 구독. [Azure 무료 평가판](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.

* [HDInsight의 Apache Storm 클러스터](../hdinsight-storm-getting-started.md)

* [Node.js](http://nodejs.org/): 웹 대시보드에 사용되며 센서 데이터를 이벤트 허브를 보냅니다.

* [Java 및 JDK 1.7](http://www.oracle.com/technetwork/java/javase/downloads/index.html)

* [Maven](http://maven.apache.org/what-is-maven.html)

* [Git](http://git-scm.com/)

> [AZURE.NOTE]Java, JDK, Maven 및 Git는 [Chocolatey NuGet](http://chocolatey.org/) 패키지 관리자를 통해서도 제공됩니다.

## 아키텍처

![아키텍처 다이어그램](./media/hdinsight-storm-sensor-data-analysis/devicesarchitecture.png)

이 예제는 다음 구성 요소로 구성됩니다.

* **Azure 이벤트 허브**: 센서에서 수집된 데이터를 제공합니다. 이 예제에서는 모조 데이터를 생성하는 응용 프로그램이 제공됩니다.

* **HDInsight의 Storm**: 이벤트 허브의 데이터를 실시간으로 처리합니다.

* **HDInsight의 HBase**(선택 사항): 영구 NoSQL 데이터 저장소를 제공합니다.

* **Azure 가상 네트워크 서비스**(선택 사항, HBase를 사용하는 경우 필수): HDInsight의 Storm 클러스터와 HDInsight의 HBase 클러스터 간 보안 통신을 설정합니다.

* **대시보드 웹 사이트**: 실시간으로 데이터 차트를 작성하는 예제 대시보드입니다.

	* 웹 사이트는 Node.js로 구현되므로 모든 클라이언트 운영 체제에서 테스트용으로 실행하거나 Azure 웹 사이트에 배포할 수 있습니다.

	* [Socket.io](http://socket.io/)는 Storm 토폴로지와 웹 사이트 간의 실시간 통신에 사용됩니다.

		> [AZURE.NOTE]이 구현 세부 정보입니다. 원시 WebSocket 또는 SignalR과 같은 모든 통신 프레임워크를 사용할 수 있습니다.

	* [D3.js](http://d3js.org/)는 웹 사이트로 전송되는 데이터의 그래프를 작성하는 데 사용됩니다.

이 토폴로지는 HDInsight의 Storm 클러스터에서 제공되는 **com.microsoft.eventhubs.spout.EventHubSpout** 클래스를 사용하여 이벤트 허브에서 데이터를 읽습니다. 웹 사이트와의 통신은 [socket.io-client.java](https://github.com/nkzawa/socket.io-client.java)를 사용하여 수행됩니다.

필요에 따라 HBase와의 통신은 Storm의 일부로 제공되는 [org.apache.storm.hbase.bolt.HBaseBolt](https://storm.apache.org/javadoc/apidocs/org/apache/storm/hbase/bolt/class-use/HBaseBolt.html) 클래스를 사용하여 수행됩니다.

다음은 토폴로지의 다이어그램입니다.

![토폴로지 다이어그램](./media/hdinsight-storm-sensor-data-analysis/sensoranalysis.png)

> [AZURE.NOTE]이는 토폴로지의 매우 단순화된 보기입니다. 런타임에 읽고 있는 이벤트 허브의 각 파티션에 대해 각 구성 요소의 인스턴스가 만들어집니다. 이러한 인스턴스는 클러스터의 노드에 분산되며 노드 간에 다음과 같이 데이터가 라우팅됩니다.
>
> * Spout에서 파서로 전송되는 데이터는 부하 분산됩니다.
> * 파서에서 대시보드 및 HBase(사용하는 경우)로 전송되는 데이터는 장치 ID별로 그룹화되므로 같은 장치의 메시지는 항상 동일한 구성 요소로 흐릅니다.

### 구성 요소

* **EventHub Spout**: 이 Spout는 GitHub에서 [HDInsight Storm 예제](https://github.com/hdinsight/hdinsight-storm-examples)의 일부로 제공됩니다.

* **ParserBolt.java**: Spout에서 내보내는 데이터는 원시 JSON이며, 한 번에 둘 이상의 이벤트를 내보낼 때도 있습니다. 이 Bolt는 Spout에서 내보낸 데이터를 읽어 여러 필드가 포함된 튜플로 새 스트림에 내보내는 방법을 보여 줍니다.

* **DashboardBolt.java**: Java용 Socket.io 클라이언트 라이브러리를 사용하여 웹 대시보드로 데이터를 실시간으로 보내는 방법을 보여 줍니다.

## 환경 준비

이 예제를 사용하려면 먼저 Storm 토폴로지에서 읽을 Azure 이벤트 허브를 만들어야 합니다. 또한 이벤트 허브에서 데이터를 읽는 데 사용되는 구성 요소는 클러스터에서만 사용할 수 있기 때문에 HDInsight의 Storm 토폴로지를 만들어야 합니다.

> [AZURE.NOTE]최종적으로 이벤트 허브 Spout는 Maven에서 사용할 수 있습니다.

### 이벤트 허브 구성

이벤트 허브는 이 예제의 데이터 원본입니다. 다음 단계에 따라 새 이벤트 허브를 만듭니다.

1. [Azure 포털](https://manage.windowsazure.com)에서 **새로 만들기 | 서비스 버스 | 이벤트 허브 | 사용자 지정 만들기**를 선택합니다.

2. **새 이벤트 허브 추가** 대화 상자에서 **이벤트 허브 이름**을 입력하고 허브를 만들 **하위 지역**을 선택한 다음 새 네임스페이스를 만들거나 기존 네임스페이스를 선택합니다. 마지막으로 화살표를 클릭하여 계속합니다.

2. **이벤트 허브 구성** 대화 상자에서 **파티션 개수** 및 **메시지 보존** 값을 입력합니다. 이 예에서는 파티션 개수로 10을, 메시지 보존으로는 1을 사용합니다.

3. 이벤트 허브가 만들어지면 네임스페이스를 선택하고 **이벤트 허브**를 선택합니다. 마지막으로 앞에서 만든 이벤트 허브를 선택합니다.

4. **구성**을 선택하고 다음 정보를 사용하여 새 액세스 정책 두 개를 만듭니다.

	<table>
<tr><th>이름</th><th>권한</th></tr>
<tr><td>장치</td><td>보내기</td></tr>
<tr><td>Storm</td><td>수신 대기</td></tr>
</table>권한을 만든 후 페이지 아래쪽의 **저장** 아이콘을 선택합니다. 그러면 이 허브로 메시지를 보내고 이 허브에서 메시지를 읽는 데 사용할 공유 액세스 정책이 만들어집니다.

5. 정책을 저장한 후 페이지 아래쪽의 **공유 액세스 키 생성기**를 사용하여 **장치** 및 **Storm** 정책의 키를 검색합니다. 이러한 키는 나중에 사용되므로 저장합니다.

### HDInsight의 Storm 클러스터 만들기

1. [Azure 포털](https://manage.windowsazure.com/)에 로그인합니다.

2. 왼쪽 창에서 **HDInsight**를 클릭하고 페이지 왼쪽 아래에서 **+새로 만들기**를 클릭합니다.

3. 두 번째 열에서 HDInsight 아이콘을 클릭하고 **사용자 지정**을 선택합니다.

4. **클러스터 세부 정보** 페이지에서 새 클러스터의 이름을 입력하고 **클러스터 유형**으로 **Storm**을 선택합니다. 화살표를 클릭하여 계속합니다.

5. 이 클러스터에 사용할 **데이터 노드**의 수로 1을 입력합니다.

	> [AZURE.NOTE]이 문서에서 사용하는 클러스터의 비용을 최소화하려면 **클러스터 크기**를 1로 줄이고 클러스터를 다 사용한 후에는 삭제합니다.

6. 관리자의 **사용자 이름** 및 **암호**를 입력하고 화살표를 클릭하여 계속합니다.

4. **저장소 계정**에서는 **새 저장소 만들기** 또는 기존 저장소 계정을 선택합니다. 그런 다음 사용할 **계정 이름** 및 **기본 컨테이너**를 선택하거나 입력합니다. 왼쪽 아래에 있는 확인 표시 아이콘을 선택하여 Storm 클러스터를 만듭니다.

## EventHubSpout 다운로드 및 설치

1. [HDInsight Storm 예제 프로젝트](https://github.com/hdinsight/hdinsight-storm-examples/)를 다운로드합니다. 다운로드한 후 **lib/eventhubs/eventhubs-storm-spout-0.9-jar-with-dependencies.jar** 파일을 찾습니다.

2. 명령 프롬프트에서 다음 명령을 사용하여 **eventhubs-storm-spout-0.9-jar-with-dependencies.jar** 파일을 로컬 Maven 저장소에 설치합니다. 그러면 이후 단계에서 해당 패키지를 Storm 프로젝트에 참조로 쉽게 추가할 수 있습니다.

		mvn install:install-file -Dfile=target/eventhubs-storm-spout-0.9-jar-with-dependencies.jar -DgroupId=com.microsoft.eventhubs -DartifactId=eventhubs-storm-spout -Dversion=0.9 -Dpackaging=jar

## 프로젝트 다운로드 및 구성

다음을 사용하여 GitHub에서 프로젝트를 다운로드합니다.

	git clone https://github.com/Blackmist/hdinsight-eventhub-example

명령이 완료되면 다음 디렉터리 구조가 생성됩니다.

	hdinsight-eventhub-example/
		TemperatureMonitor/ - this is the Java topology
			conf/
				Config.properties
				hbase-site.xml
			src/
			test/
			dashboard/ - this is the node.js web dashboard
			SendEvents/ - utilities to send fake sensor data

> [AZURE.NOTE]이 문서에서는 이 샘플에 포함된 코드에 대해 자세히 알아보지 않습니다. 그러나 코드는 완전히 주석 처리되어 있습니다.

**Config.properties** 파일을 열고 이전에 이벤트 허브를 만들 때 사용한 정보를 추가합니다. 이 정보를 추가한 후 파일을 저장합니다.

	eventhubspout.username = storm

	eventhubspout.password = <the key of the 'storm' policy>

	eventhubspout.namespace = <the event hub namespace>

	eventhubspout.entitypath = <the event hub name>

	eventhubspout.partitions.count = <the number of partitions for the event hub>

	## if not provided, will use storm's zookeeper settings
	## zookeeper.connectionstring=localhost:2181

	eventhubspout.checkpoint.interval = 10

	eventhub.receiver.credits = 1024

## 로컬로 컴파일 및 테스트

테스트하기 전에 대시보드를 시작하여 토폴로지의 출력을 확인하고 이벤트 허브에 저장할 데이터를 생성해야 합니다.

### 웹 응용 프로그램 시작

1. 새 명령 프롬프트 또는 터미널을 열고 디렉터리를 **hdinsight-eventhub-example/dashboard**로 변경한 후 다음 명령을 사용하여 웹 응용 프로그램에 필요한 종속성을 설치합니다.

		npm install

2. 다음 명령을 사용하여 웹 응용 프로그램을 시작합니다.

		node server.js

	다음과 유사한 메시지가 표시됩니다.

		Server listening at port 3000

2. 웹 브라우저를 열고 ****http://localhost:3000/**을 주소로 입력합니다. 다음과 유사한 결과가 표시됩니다.

	![웹 대시보드](./media/hdinsight-storm-sensor-data-analysis/emptydashboard.png)

	이 명령 프롬프트 또는 터미널을 계속 열어 두세요. 테스트 후 Ctrl+C를 사용하여 웹 서버를 중지합니다.

### 데이터 생성 시작

> [AZURE.NOTE]이 섹션의 단계에서는 모든 플랫폼에서 실행할 수 있도록 Node.js를 사용합니다. 다른 언어 예제는 **SendEvents** 디렉터리를 참조하세요.


1. 새 명령 프롬프트 또는 터미널을 열고 디렉터리를 **hdinsight-eventhub-example/SendEvents/nodejs**로 변경한 후 다음 명령을 사용하여 응용 프로그램에 필요한 종속성을 설치합니다.

		npm install

2. 텍스트 편집기에서 **app.js** 파일을 열고 이전에 가져온 이벤트 허브 정보를 추가합니다.

		// ServiceBus Namespace
		var namespace = 'servicebusnamespace';
		// Event Hub Name
		var hubname ='eventhubname';
		// Shared access Policy name and key (from Event Hub configuration)
		var my_key_name = 'devices';
		var my_key = 'key';

2. 다음 명령을 사용하여 이벤트 허브에 새 항목을 삽입합니다.

		node app.js

	이벤트 허브로 전송된 데이터가 포함된 여러 줄의 출력이 표시됩니다. 이러한 출력은 다음과 유사합니다.

		{"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":0,"Temperature":7}
		{"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":1,"Temperature":39}
		{"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":2,"Temperature":86}
		{"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":3,"Temperature":29}
		{"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":4,"Temperature":30}
		{"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":5,"Temperature":5}
		{"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":6,"Temperature":24}
		{"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":7,"Temperature":40}
		{"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":8,"Temperature":43}
		{"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":9,"Temperature":84}

### 토폴로지 시작

2. 다음 명령을 사용하여 로컬로 토폴로지를 시작합니다.

	mvn compile exec:java -Dstorm.topology=com.microsoft.examples.Temperature

	그러면 토폴로지가 시작되고 이벤트 허브에서 파일을 읽은 후 Azure 웹 사이트에서 실행되는 대시보드로 파일을 보냅니다. 웹 대시보드에 다음과 유사한 줄이 표시됩니다.

	![데이터가 표시된 대시보드](./media/hdinsight-storm-sensor-data-analysis/datadashboard.png)

3. 대시보드가 실행 중인 동안 이전 단계의 `node app.js` 명령을 사용하여 새 데이터를 대시보드로 보냅니다. 온도 값은 임의로 생성되기 때문에 새 값을 표시하도록 그래프가 업데이트됩니다.

3. 제대로 작동하는지 확인하고 Ctrl+C를 입력하여 토폴로지를 중지합니다. SendEvent 앱을 중지하려면 창을 선택하고 아무 키나 누릅니다. Ctrl+C를 사용하여 웹 서버를 중지할 수도 있습니다.

## 토폴로지를 패키지하여 HDInsight에 배포

개발 환경에서 다음 단계를 수행하여 HDInsight Storm 클러스터에서 Temperature 토폴로지를 실행합니다.

### 웹 사이트 대시보드 게시

1. 대시보드를 Azure 웹 사이트에 배포하려면 [Node.js 웹 사이트 빌드 및 Azure에 배포](../web-sites-nodejs-develop-deploy-mac.md) 단계를 따릅니다 웹 사이트의 URL(**mywebsite.azurewebsites.net**과 유사함)을 기록해 둡니다.

2. 웹 사이트가 만들어지면 Azure 포털에서 해당 사이트로 이동하여 **구성** 탭을 선택합니다. **웹 소켓**을 사용하도록 설정하고 페이지 아래쪽에서 **저장**을 클릭합니다.

2. **hdinsight-eventhub-example\\TemperatureMonitor\\src\\main\\java\\com\\microsoft\\examples\\bolts\\DashboardBolt.java**를 열고 다음 줄을 게시된 대시보드의 URL을 가리키도록 변경합니다.

		socket = IO.socket("http://mywebsite.azurewebsites.net");

3. **DashboardBolt.java** 파일을 저장합니다.

### 토폴로지 패키지 및 배포

1. 다음 명령을 사용하여 프로젝트에서 JAR 패키지를 만듭니다.

		mvn package

	그러면 프로젝트의 **target** 디렉터리에 **TemperatureMonitor-1.0-SNAPSHOT.jar** 파일이 만들어집니다.

2. [Storm 토폴로지 배포 및 관리](hdinsight-storm-deploy-monitor-topology.md)의 단계에 따라 **Storm 대시보드**를 사용하여 HDInsight의 Storm 클러스터에서 토폴로지를 업로드하고 시작합니다.

3. 토폴로지가 시작되면 브라우저에서 Azure에 게시한 웹 사이트를 열고 `node app.js` 명령을 사용하여 이벤트 허브로 데이터를 보냅니다. 정보를 표시하기 위해 웹 대시보드가 업데이트됩니다.

	![dashboard](./media/hdinsight-storm-sensor-data-analysis/datadashboard.png)

## 선택 사항: HBase 사용

Storm과 HBase를 함께 사용하려면 Azure 가상 네트워크를 만든 다음 해당 네트워크 내에 Storm 및 HBase 클러스터를 만들어야 합니다.

### Azure 가상 네트워크 만들기(선택 사항)

이 예제에서 HBase를 사용하려면 HDInsight의 Storm 클러스터와 HDInsight의 HBase 클러스터를 포함할 Azure 가상 네트워크를 만들어야 합니다.

1. [Azure 포털](https://manage.windowsazure.com)에 로그인합니다.

2. 페이지 아래쪽에서 **+새로 만들기** >**네트워크 서비스** > **가상 네트워크** > **빠른 생성**을 클릭합니다.

3. 다음 값을 입력하거나 선택합니다.

	- **이름**: 가상 네트워크의 이름입니다.

	- **주소 공간**: 클러스터의 모든 노드에 대한 주소를 제공하기에 충분히 큰 가상 네트워크의 주소 공간을 선택합니다. 그렇지 않으면 프로비전에 실패합니다.

	- **최대 VM 수**: 최대 가상 컴퓨터 수 중 하나를 선택합니다.

	- **위치**: 위치는 만들 HBase 클러스터와 동일해야 합니다.

	- **DNS 서버**: 이 문서에서는 Azure에서 제공하는 내부 DNS 서버를 사용하므로 **없음**을 선택하면 됩니다. 사용자 지정 DNS 서버가 포함된 고급 네트워킹 구성도 지원됩니다. 자세한 지침은 [이름 확인(DNS)](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)을 참조하세요.

4. **가상 네트워크 만들기**를 클릭합니다. 새 가상 네트워크 이름이 목록에 나타납니다. 상태 열에 **생성됨**이 표시될 때까지 기다립니다.

5. 기본 창에서 방금 만든 가상 네트워크를 클릭합니다.

6. 페이지 맨 위에서 **대시보드**를 클릭합니다.

7. **간략 상태** 아래에서 **가상 네트워크 ID**를 적어 둡니다. 이 ID는 Storm 및 HBase 클러스터를 프로비전할 때 필요합니다.

8. 페이지 위쪽에서 **구성**을 클릭합니다.

9. 페이지 아래쪽에 표시된 기본 서브넷 이름은 **Subnet-1**입니다. **서브넷 추가** 단추를 사용하여 **Subnet-2**를 추가합니다. 이러한 서브넷에 Storm 및 HBase 클러스터가 포함됩니다.

	> [AZURE.NOTE]이 문서에서는 노드가 하나뿐인 클러스터를 사용합니다. 다중 노드 클러스터를 만드는 경우에는 클러스터에 사용할 서브넷의 **CIDR(주소 수)**을 확인해야 합니다. 주소 수는 작업자 노드 수에 7(게이트웨이: 2, 헤드 노드: 2, Zookeeper: 3)을 더한 값보다 커야 합니다. 예를 들어 10개 노드 HBase 클러스터가 필요한 경우 서브넷의 주소 수는 17(10+7)보다 커야 합니다. 그렇지 않으면 배포에 실패합니다.
	>
	> 각 클러스터에 단일 서브넷을 지정하는 것이 좋습니다.

11. 페이지 맨 아래에서 **저장**을 클릭합니다.

### 가상 네트워크에서 Storm 및 HBase 클러스터 만들기

1. [Azure 포털](https://manage.windowsazure.com/)에 로그인합니다.

2. 왼쪽 창에서 **HDInsight**를 클릭하고 페이지 왼쪽 아래에서 **+새로 만들기**를 클릭합니다.

3. 두 번째 열에서 HDInsight 아이콘을 클릭하고 **사용자 지정**을 선택합니다.

4. **클러스터 세부 정보** 페이지에서 새 클러스터의 이름을 입력하고 **클러스터 유형**으로 **Storm**을 선택합니다. 화살표를 클릭하여 계속합니다.

5. 이 클러스터에 사용할 **데이터 노드**의 수로 1을 입력합니다. **하위 지역/가상 네트워크**에서 앞에서 만든 Azure 가상 네트워크를 선택합니다. **가상 네트워크 서브넷**으로는 **Subnet-1**을 선택합니다.

	> [AZURE.NOTE]이 문서에서 사용하는 클러스터의 비용을 최소화하려면 **클러스터 크기**를 1로 줄이고 클러스터를 다 사용한 후에는 삭제합니다.

6. 관리자의 **사용자 이름** 및 **암호**를 입력하고 화살표를 클릭하여 계속합니다.

4. **저장소 계정**에서는 **새 저장소 만들기** 또는 기존 저장소 계정을 선택합니다. 그런 다음 사용할 **계정 이름** 및 **기본 컨테이너**를 선택하거나 입력합니다. 왼쪽 아래에 있는 확인 표시 아이콘을 선택하여 Storm 클러스터를 만듭니다.

5. 이러한 단계를 반복하여 새 **HBase** 클러스터를 만듭니다. 다음은 주요 차이점입니다.

	* **클러스터 유형**: **HBase** 선택

	* **가상 네트워크 서브넷**: **Subnet-2** 선택

	* **저장소 계정**: Storm 클러스터에 사용했던 것과는 다른 컨테이너를 사용해야 합니다.

### HBase DNS 접미사 검색

Storm 클러스터에서 HBase에 쓰려면 HBase 클러스터의 FQDN(정규화된 도메인 이름)을 사용해야 합니다. 다음 명령을 사용하여 이 정보를 검색합니다.

	curl -u <username>:<password> -k https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/hbase/components/hbrest

반환되는 JSON 데이터에서 **"host\_name"** 항목을 찾습니다. 이 항목에는 클러스터의 노드에 대한 FQDN이 포함되어 있습니다. 예를 들면 다음과 같습니다.

	...
	"host_name": "wordkernode0.<clustername>.b1.cloudapp.net
	...

클러스터 이름으로 시작하는 도메인 이름 부분이 DNS 접미사입니다(예: **mycluster.b1.cloudapp.net**).

### HBase bolt 사용

1. **hdinsight-eventhub-example\\TemperatureMonitor\\conf\\hbase-site.xml**을 열고 다음 줄의 `suffix` 항목을 앞에서 가져온 HBase 클러스터의 DNS 접미사로 바꿉니다. 변경을 완료한 후 파일을 저장합니다.

		<value>zookeeper0.suffix,zookeeper1.suffix,zookeeper2.suffix</value>

	이는 HBase bolt에서 HBase 클러스터와 통신하는 데 사용됩니다.

1. 텍스트 편집기에서 **hdinsight-eventhub-example\\TemperatureMonitor\\src\\main\\java\\com\\microsoft\\examples\\bolts**를 열고 시작 부분에서 `//`를 제거하여 다음 줄의 주석 처리를 제거합니다. 변경을 완료한 후 파일을 저장합니다.

		topologyBuilder.setBolt("HBase", new HBaseBolt("SensorData", mapper).withConfigKey("hbase.conf"), spoutConfig.getPartitionCount())
    	  .fieldsGrouping("Parser", "hbasestream", new Fields("deviceid")).setNumTasks(spoutConfig.getPartitionCount());

	그러면 HBase Bolt가 사용하도록 설정됩니다.

	> [AZURE.NOTE]로컬로 테스트할 때까지 아니라 Storm 클러스터에 배포할 때만 HBase Bolt를 사용하도록 설정해야 합니다.

### HBase 및 Storm 데이터

토폴로지를 실행하기 전에 데이터를 허용하도록 HBase를 준비해야 합니다.

1. 원격 데스크톱을 사용하여 HBase 클러스터에 연결합니다.

2. 데스크톱에서 HDInsight 명령줄을 시작하고 다음 명령을 입력합니다.

    cd %HBASE\_HOME% bin\\hbase shell

3. HBase 셸에서 다음 명령을 입력하여 센서 데이터를 저장할 테이블을 만듭니다.

    create 'SensorData', 'cf'

4. 다음 명령을 입력하여 테이블에 데이터가 없음을 확인합니다.

    scan 'SensorData'

Storm 클러스터에서 토폴로지를 시작하고 데이터를 처리한 경우 다시 `scan 'SensorData'` 명령을 사용하여 데이터가 HBase에 삽입되었는지 확인할 수 있습니다.


## 다음 단계

지금까지 Storm을 사용하여 이벤트 허브에서 데이터를 읽고, SignalR 및 D3.js를 사용하여 외부 대시보드에 Storm의 정보를 표시하는 방법에 대해 알아보았습니다. 또한 선택적 단계를 사용한 경우 가상 네트워크에서 HDInsight를 구성하는 방법 및 HBase Bolt를 사용하여 Storm 토폴로지와 HBase 간에 통신하는 방법도 배웠습니다.

* HDinsight의 Storm 토폴로지에 대한 추가 예제는 다음을 참조하세요.

    * [HDInsight의 Storm에 대한 예제 토폴로지](hdinsight-storm-example-topology.md)

* Apache Storm에 대한 자세한 내용은 [Apache Storm](https://storm.incubator.apache.org/) 사이트를 참조하세요.

* HDInsight의 HBase에 대한 자세한 내용은 [HDInsight HBase 개요](hdinsight-hbase-overview.md)를 참조하세요.

* Socket.io에 대한 자세한 내용은 [socket.io](http://socket.io/) 사이트를 참조하세요.

* D3.js에 대한 자세한 내용은 [D3.js - 데이터 기반 문서](http://d3js.org/)를 참조하세요.

* Java에서 토폴로지를 만들기에 대한 자세한 내용은 [HDInsight의 Apache Storm용 Java 토폴로지 개발](hdinsight-storm-develop-java-topology.md)을 참조하세요.

* .NET에서 토폴로지를 만들기에 대한 자세한 내용은 [Visual Studio를 사용하여 HDInsight의 Apache Storm용 C# 토폴로지 개발](hdinsight-storm-develop-csharp-visual-studio-topology.md)을 참조하세요.

[azure-portal]: https://manage.windowsazure.com/

<!---HONumber=Sept15_HO4-->