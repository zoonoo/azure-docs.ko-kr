<properties
   pageTitle="Elasticsearch에 대한 성능 테스트 환경 만들기 | Microsoft Azure"
   description="Elasticsearch 클러스터의 성능을 테스트하기 위해 환경을 설정하는 방법입니다."
   services=""
   documentationCenter="na"
   authors="mabsimms"
   manager="marksou"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/18/2016"
   ms.author="masimms"/>
   
# Azure에서 Elasticsearch에 대한 성능 테스트 환경 만들기

이 문서는 [시리즈의 일부](guidance-elasticsearch.md)입니다.

이 문서에서는 Elasticsearch 클러스터의 성능을 테스트하기 위해 환경을 설정하는 방법을 설명합니다. [Azure에서 Elasticsearch에 대한 데이터 수집 성능 튜닝][]에 설명한 대로 데이터 수집 및 쿼리 워크로드의 성능을 테스트하는 데 이 구성을 사용합니다.

성능 테스트 프로세스는 목적을 위해 특별히 구성된 전용 VM(Elasticsearch 클러스터의 일부가 아님)의 집합을 사용하여 마스터/하위 구성에 설치된 플러그 인의 [표준 설정](http://jmeter-plugins.org/wiki/StandardSet/)으로 [Apache JMeter](http://jmeter.apache.org/)를 사용했습니다.

[PerfMon 서버 에이전트](http://jmeter-plugins.org/wiki/PerfMonAgent/)는 각 Elasticsearch 노드에 설치되었습니다. 다음 섹션에서는 JMeter를 사용하여 고유한 성능 테스트를 수행할 수 있도록 테스트 환경을 다시 만들기 위한 지침을 제공합니다. 이러한 지침은 Azure 가상 네트워크를 사용하여 연결된 노드로 Elasticsearch 클러스터를 이미 만들었다고 가정합니다.

또한 테스트 환경이 단일 Azure 리소스 그룹을 사용하여 관리되는 Azure VM의 집합으로 실행됩니다.

또한 [Marvel](https://www.elastic.co/products/marvel)은 Elasticsearch 클러스터의 내부 측면을 모니터링하고 보다 쉽게 분석할 수 있도록 설치되고 구성되었습니다. JMeter 통계가 성능에서 최고 또는 최저치를 보여주는 경우 Marvel를 통해 사용할 수 있는 정보는 변동의 원인을 확인하는 데 매우 유용할 수 있습니다.

다음 이미지에서는 전체 시스템의 구조를 보여줍니다.

![elasticsearch-arch](./media/guidance-elasticsearch/performance-structure.png)

다음 사항에 유의하세요.

- JMeter 마스터 VM은 Windows Server를 실행하여 JMeter 콘솔에 대한 GUI 환경을 제공합니다. JMeter 마스터 VM은 GUI(*jmeter* 응용 프로그램)를 제공하여 테스터가 테스트를 만들고 테스트를 실행하며 결과를 시각화하도록 합니다. 이 VM은 실제로 테스트를 구성하는 요청을 보내는 JMeter 서버 VM을 사용하여 조정합니다.

- JMeter 하위 VM은 Ubuntu Server(Linux)를 실행하며 이러한 VM에 대한 GUI 요구 사항은 없습니다. JMeter 서버 VM은 JMeter 서버 소프트웨어(*jmeter-server* 응용 프로그램)를 실행하여 Elasticsearch 클러스터에 요청을 전송합니다.

- 전용 마스터 노드를 사용했지만 전용 클라이언트 노드는 사용하지 않았습니다.

- 클러스터의 데이터 노드 수는 테스트된 시나리오에 따라 달라질 수 있습니다.

- Elasticsearch 클러스터의 모든 노드는 Marvel을 실행하여 런타임 시 성능을 측정하고 JMeter 서버 에이전트를 실행하여 이후 분석에 대한 모니터링 데이터를 수집합니다.

- 나중에 Elasticsearch 2.0.0 를 테스트할 경우 데이터 노드 중 하나는 Kibana를 실행하며 이는 Elasticsearch 2.0.0 이상에서 실행되는 Marvel 버전에서 필수적입니다.

## 가상 컴퓨터에 대한 Azure 리소스 그룹 만들기

JMeter 마스터는 성능 데이터를 수집하도록 Elasticsearch 클러스터의 노드 각각에 직접 연결할 수 있어야 합니다. JMeter VNet이 Elasticsearch 클러스터 VNet과 다른 경우 공용 IP 주소를 사용하여 각 Elasticsearch 노드를 구성합니다. Elasticsearch 구성에 이런 문제가 있으면 동일한 리소스 그룹을 사용하여 Elasticsearch 클러스터와 동일한 VNet에서 JMeter VM을 구현하는 것을 고려하며 이 경우에 이 첫 번째 절차를 생략할 수 있습니다.

먼저 [리소스 그룹을 만듭니다](../articles/resource-group-portal/#create-resource-group-and-resources). 이 문서에서는 리소스 그룹의 이름이 *JMeterPerformanceTest*라고 가정합니다. Elasticsearch 클러스터와 동일한 VNet에서 JMeter VM을 실행하려는 경우 리소스 그룹을 새로 만드는 대신 해당 클러스터와 동일한 리소스 그룹을 사용합니다.

## JMeter 마스터 가상 컴퓨터 만들기

다음으로 *Windows Server 2008 R2 SP1* 이미지를 사용하여 [Windows VM을 만듭니다](../articles/virtual-machines-windows-tutorial/). 충분한 코어 및 메모리를 가진 VM 크기를 선택하여 성능 테스트를 실행하는 것이 좋습니다. 2개 이상의 코어 및 3.5GB RAM이 있는 컴퓨터가 이상적입니다(A2 표준 이상).

<!-- TODO add info on why disabling diagnostics is positive -->

진단을 사용하지 않는 것이 좋습니다. 포털에서 VM을 만들 때 *진단*의 *모니터링* 섹션에 있는 *설정* 블레이드에서 수행합니다. 다른 설정을 기본값으로 둡니다.

VM 및 모든 관련된 리소스가 포털에서 [리소스 그룹을 검사](../articles/resource-group-portal/#browse-resource-groups)하여 성공적으로 만들어졌는지 확인합니다. 나열된 리소스는 동일한 이름을 가진 VM, 네트워크 보안 그룹 및 공용 IP 주소와 VM의 이름에 기반한 이름을 가진 네트워크 인터페이스 및 저장소 계정으로 구성되어야 합니다.

## JMeter 하위 가상 컴퓨터 만들기

이제 *Ubuntu Server 14.04 LTS* 이미지를 사용하여 [Linux VM을 만듭니다](../articles/virtual-machines-linux-tutorial-portal-rm/). JMeter 마스터 VM과 마찬가지로 충분한 코어 및 메모리를 가진 VM 크기를 선택하여 성능 테스트를 실행하는 것이 좋습니다. 2개 이상의 코어 및 최소 3.5GB RAM(표준 A2 이상)이 있는 컴퓨터가 이상적입니다.

마찬가지로 진단을 사용하지 않는 것이 좋습니다.

원하는 만큼 하위 VM을 만들 수 있습니다.

## JMeter 하위 VM에서 JMeter 서버 설치

JMeter 하위 VM이 Linux를 실행할 때 기본적으로 원격 데스크톱 연결(RDP)를 열어 여기에 연결할 수 없습니다. 대신, 각 VM에서 [PuTTY를 사용하여 명령줄 창을 열](../articles/virtual-machines-linux-how-to-log-on/) 수 있습니다.

하위 VM 중 하나에 연결하면 bash를 사용하여 JMeter을 설치합니다.

먼저, JMeter를 실행하는 데 필요한 Java Runtime Environment를 설치합니다.

```bash
sudo add-apt-repository ppa:webupd8team/java
sudo apt-get update
sudo apt-get install oracle-java8-installer
```

이제 zip 파일로 묶인 JMeter 소프트웨어를 다운로드합니다.

```bash
wget http://apache.mirror.anlx.net/jmeter/binaries/apache-jmeter-2.13.zip
```

압축 풀기를 명령을 설치한 다음 이를 사용하여 JMeter 소프트웨어를 확장합니다. 소프트웨어는 **apache-jmeter-2.13**라는 폴더에 복사됩니다.

```bash
sudo apt-get install unzip
unzip apache-jmeter-2.13.zip
```

JMeter 실행 파일을 보유한 *bin* 디렉터리를 변경하고 *jmeter-server* 및 *jmeter* 프로그램 실행 파일을 만듭니다.

```bash
cd apache-jmeter-2.13/bin
chmod u+x jmeter-server
chmod u+x jmeter
```

이제, 현재 폴더에 있는 `jmeter.properties` 파일을 편집해야 합니다(*vi* 또는 *vim*과 같은 가장 친숙한 텍스트 편집기 사용). 다음 줄을 찾습니다.

```yaml
...
client.rmi.localport=0
...
server.rmi.localport=4000
...
```

주석 처리를 제거(선행 # # 문자 제거)하고 아래와 같이 이 줄을 수정한 다음 파일을 저장하고 편집기를 닫습니다.

```yaml
...
client.rmi.localport=4441
...
server.rmi.localport=4440
```

이제, 다음 명령을 실행하여 4441 포트를 들어오는 TCP 트래픽에 대해 엽니다.(방금 이 포트에서 *jmeter-server*를 수신하도록 구성했습니다)

```bash
sudo iptables -A INPUT -m state --state NEW -m tcp -p tcp --dport 4441 -j ACCEPT
```

JMeter에 대한 플러그 인(이러한 플러그 인은 성능 모니터링 카운터를 제공)의 표준 컬렉션을 포함한 zip 파일을 다운로드하고 파일의 압축을 풉니다. 이 위치에 파일 압축을 풀면 정확한 폴더에 플러그 인을 배치합니다.

라이선스 파일을 바꿀지 묻는 메시지가 표시되면 (모두) A를 입력합니다.

```bash
wget http://jmeter-plugins.org/downloads/file/JMeterPlugins-Standard-1.3.0.zip
unzip JMeterPlugins-Standard-1.3.0.zip
```

`nohup`을 사용하여 백그라운드에서 JMeter 서버를 시작합니다. 원격 개체를 만들었고 명령을 수신하기 시작할 준비가 되었음을 나타내는 프로세스 ID 및 메시지를 표시하여 응답해야 합니다.

```bash
nohup bin/jmeter-server &
```

> [AZURE.NOTE] VM이 종료되면 JMeter 서버 프로그램도 종료됩니다. VM에 연결하고 수동으로 다시 시작해야 합니다. 또는 (*종료 0* 명령 전에) 다음 명령을 `/etc/rc.local` 파일에 추가하여 시작 시 자동으로 *jmeter-server* 명령을 실행하도록 시스템을 구성할 수 있습니다.

```bash
sudo -u <username> bash << eoc
cd /home/<username>/apache-jmeter-2.13/bin
nohup ./jmeter-server &
eoc
```

`<username>`을 로그인 이름으로 바꿉니다.

테스트가 진행 중인 동안 JMeter 서버의 진행률을 모니터링할 수 있도록 터미널 창을 열어두는 것이 유용할 수 있습니다.

각 JMeter 하위 VM에 대해 이러한 단계를 반복해야 합니다.

## Elasticsearch 노드에 JMeter 서버 에이전트 설치

이 절차에서는 Elasticsearch 노드에 대한 로그인 액세스 권한이 있다고 가정합니다. ARM 템플릿을 사용하여 클러스터를 만든 경우 [Azure 프로덕션 토폴로지](guidance-elasticsearch-running-on-azure.md#elasticsearch-topologies) 섹션에서 설명한 대로 점프 상자 VM을 통해 각 노드에 연결할 수 있습니다. 또한 PuTTY를 사용하여 점프 상자에 연결할 수 있습니다.

여기에서부터 *ssh* 명령을 사용하여 Elasticsearch 클러스터의 노드 각각에 로그인할 수 있습니다.

Elasticsearch 노드 중 하나에 관리자 권한으로 로그인합니다. bash 명령 프롬프트에서 다음 명령을 입력하여 JMeter 서버 에이전트를 보관하기 위한 폴더를 만들고 해당 폴더로 이동합니다.

```bash
mkdir server-agent
cd server-agent
```

다음 명령을 실행하여 *압축 풀기* 명령를 설치하고(아직 설치되지 않은 경우) JMeter 서버 에이전트 소프트웨어를 다운로드하며 압축을 풉니다.

```bash
sudo apt-get install unzip
wget http://jmeter-plugins.org/downloads/file/ServerAgent-2.2.1.zip
unzip ServerAgent-2.2.1.zip
```
 
다음 명령을 실행하여 방화벽을 구성하고 TCP 트래픽을 사용하도록 설정하여 4444 포트(JMeter 서버 에이전트에서 사용되는 포트)를 통과합니다.

```bash
sudo iptables -A INPUT -m state --state NEW -m tcp -p tcp --dport 4444 -j ACCEPT
```

다음 명령을 실행하여 백그라운드에서 JMeter 서버 에이전트를 시작합니다.

```bash
nohup ./startAgent.sh &
```

JMeter 서버 에이전트가 시작되고 4444 포트에서 수신 대기 중임을 나타내는 메시지로 응답해야 합니다. Enter 키를 눌러 명령 프롬프트 창을 가져오고 다음 명령을 실행합니다. `<nodename>`을 노드의 이름으로 바꿉니다. 노드의 이름을 정확히 알지 못하는 경우 `hostname` 명령을 실행하여 찾을 수 있습니다.

```bash
telnet <nodename> 4444
```

이 명령은 로컬 컴퓨터에서 4444 포트에 대한 텔넷 연결을 엽니다. 이 연결을 사용하여 JMeter 서버 에이전트가 올바르게 실행되는지 확인할 수 있습니다.

JMeter 서버 에이전트를 실행하지 않는 경우 응답을 수신합니다.

`*telnet: Unable to connect to remote host: Connection refused*.`

JMeter 서버 에이전트가 실행되고 포트 4444가 올바르게 구성된 경우 다음과 같은 응답이 표시되어야 합니다.

![](./media/guidance-elasticsearch/performance-telnet-server.png)

> [AZURE.NOTE] 텔넷 세션이 연결되면 어떤 종류의 프롬프트도 제공하지 않습니다.

텔넷 세션에서 다음 명령을 입력합니다.

``` 
test
```

JMeter 서버 에이전트를 구성하고 올바르게 수신 대기하는 경우 명령을 수신하고 *Yep* 메시지로 응답한다고 나타내야 합니다.

> [AZURE.NOTE] 다른 명령을 입력하여 성능 모니터링 데이터를 가져올 수 있습니다. 예를 들어 명령 `metric-single:cpu:idle`은 CPU가 유휴 상태인 경우(이것이 스냅숏) 현재 시간 비율을 제공합니다. 명령의 전체 목록은 [PerfMon 서버 에이전트](http://jmeter-plugins.org/wiki/PerfMonAgent/) 페이지를 방문합니다.

텔넷 세션에서 다음 명령을 입력하여 세션을 종료하고 bash 명령 프롬프트를 반환합니다.

``` 
exit
```

> [AZURE.NOTE] JMeter 하위 VM과 마찬가지로, 로그아웃하거나 이 컴퓨터가 종료되고 다시 시작한 경우 JMeter 서버 에이전트는 `startAgent.sh` 명령을 사용하여 수동으로 다시 시작해야 합니다. JMeter 서버 에이전트가 자동으로 시작되도록 하면 *종료 0* 명령 전에 `/etc/rc.local` 파일의 끝에 다음 명령을 추가합니다. `<username>`를 로그인 이름으로 바꿉니다.

```bash
sudo -u <username> bash << eoc
cd /home/<username>/server-agent
nohup ./startAgent.sh &
eoc
```

`<username>`을 로그인 이름으로 바꿉니다.

Elasticsearch 클러스터의 다른 모든 노드에 이 전체 프로세스를 반복할 수 있습니다. 또는 `scp` 명령을 사용하여 server-agent 폴더 및 모든 다른 노드에 대한 콘텐츠를 복사하고 `ssh` 명령을 사용하여 아래와 같이 JMeter 서버 에이전트를 시작할 수 있습니다. `<username>`을 사용자 이름으로 바꾸고 소프트웨어를 복사하고 실행하려는 노드의 이름으로 `<nodename>`를 바꿉니다(명령을 실행할 때 암호를 제공하라는 메시지가 표시될 수 있음).

```bash
scp -r ~/server-agent <username>@<nodename>:~
ssh <nodename> sudo iptables -A INPUT -m state --state NEW -m tcp -p tcp --dport 4444 -j ACCEPT
ssh <nodename> -n -f 'nohup ~/server-agent/startAgent.sh'
```

## JMeter 마스터 VM에 JMeter 설치 및 구성

Azure 포털에서 *리소스 그룹*을 클릭합니다. *리소스 그룹* 블레이드에서 JMeter 마스터 및 하위 VM을 포함하는 리소스 그룹을 클릭합니다. *리소스 그룹* 블레이드에서 JMeter 마스터 VM을 클릭합니다. 가상 컴퓨터 블레이드의 도구 모음에서 *연결*을 클릭합니다. 웹 브라우저에서 메시지가 표시되면 RDP 파일을 엽니다. Windows는 VM에 대한 원격 데스크톱 연결을 만듭니다. 메시지가 표시되면 VM에 대한 사용자 이름 및 암호를 입력합니다.

VM에서 Internet Explorer를 사용하여 [Windows용 Java 다운로드](http://www.java.com/en/download/ie_manual.jsp) 페이지로 이동합니다. 다음 지침을 수행하여 Java 설치 관리자를 다운로드하고 실행합니다.

웹 브라우저에서 [Apache JMeter 다운로드](http://jmeter.apache.org/download_jmeter.cgi) 페이지로 이동하여 가장 최근의 이진 파일을 포함하는 zip을 다운로드합니다. VM의 편리한 위치에 zip을 저장합니다.

[JMeter 플러그 인 사용자 지정](http://jmeter-plugins.org/) 사이트로 이동하여 플러그인의 표준 집합을 다운로드합니다. zip을 이전 단계에서 JMeter 다운로드와 동일한 폴더에 저장합니다.

Windows 탐색기에서 apache-jmeter-*xx* zip 파일(*xx*는 JMeter의 현재 버전)을 포함한 폴더로 이동한 다음 현재 폴더에 파일을 추출합니다(zip은 apache-jmeter-*xxx*라는 하위 폴더를 만듬).

JMeterPlugins-Standard-*yyy*.zip 파일에서 the apache-jmeter-*xxx*로 파일을 추출합니다(*yyy*는 플러그 인의 최신 버전). 그러면 JMeter에 대한 플러그 인을 올바른 폴더에 추가합니다(메시지가 표시되면 안전하게 lib 폴더를 병합하고 라이선스 및 추가 정보 파일을 덮어쓸 수 있음).

apache-jmeter-*xxx*/bin 폴더로 이동하고 메모장을 사용하여 jmeter.properties 파일을 편집합니다. `jmeter.properties` 파일에서 *원격 호스트 및 RMI 구성*이라고 레이블이 지정된 섹션을 찾습니다. 파일의 이 섹션에서 다음 줄을 찾습니다.

```yaml
remote_hosts=127.0.0.1
```

이 줄을 변경하고 IP 주소 127.0.0.1을 쉼표로 구분한 각 JMeter 하위 서버에 대한 IP 주소 또는 호스트 이름 목록으로 바꿉니다. 예:

```yaml
remote_hosts=JMeterSub1,JMeterSub2
```

다음 줄을 찾은 다음이 줄의 시작 부분에서 `#` 문자를 제거하고 다음에서 client.rmi.localport 설정의 값을 수정합니다.

```yaml
#client.rmi.localport=0
```

to:

```yaml
client.rmi.localport=4440
```

파일을 저장하고 메모장을 닫습니다.

Windows 도구 모음에서 *시작*, *관리 도구*, *고급 보안이 포함된 Windows 방화벽*을 차례로 클릭합니다. *고급 보안이 포함된 Windows 방화벽* 창의 왼쪽 창에서 *인바운드 규칙*을 마우스 오른쪽 단추로 클릭한 후 *새 규칙*을 클릭합니다.

*새 인바운드 규칙* *마법사*의 *규칙 형식* 페이지에서 *포트*를 선택한 후 *다음*을 클릭합니다. *프로토콜 및 포트* 페이지에서 *TCP*를 선택하고 *특정 로컬 포트*를 선택한 다음 텍스트 상자에 `4440-4444`를 입력하고 *다음*을 입력합니다. *작업* 화면에서 *연결 허용*을 선택하고 *다음*을 클릭합니다. *프로필* 페이지에서 모든 옵션을 선택하고 *다음*을 클릭합니다. *이름* 페이지의 *이름* 텍스트 상자 형식에서 *JMeter*를 입력하고 *마침*을 클릭합니다. *고급 보안이 포함된 Windows 방화벽* 창을 닫습니다.

Windows 탐색기의 apache-jmeter-*xx*/bin 폴더에서 *jmeter* Windows 배치 파일을 두 번 클릭하여 GUI를 시작합니다. 사용자 인터페이스가 표시되어야 합니다.

![](./media/guidance-elasticsearch/performance-image17.png)

메뉴 모음에서 *실행*을 클릭하고 *원격 시작*을 클릭한 다음 두 JMeter 하위 컴퓨터가 나열되어 있는지 확인합니다.

![](./media/guidance-elasticsearch/performance-image18.png)

이제 성능 테스트를 시작할 준비가 되었습니다.

## Marvel 설치 및 구성

클러스터를 구축할 때 MARVEL 및 KIBANA 매개 변수를 true로 설정하는 경우 Azure에 Elasticsearch 빠른 시작 템플릿은 적절한 버전의 Marvel을 자동으로 설치하고 구성합니다.

![](./media/guidance-elasticsearch/performance-image19.png)

설치를 수동으로 수행해야 하는 기존 클러스터에 Marvel을 추가하는 경우 다음 절차에 설명한 대로 이 과정은 사용하는 버전이 Elasticsearch 버전 1.7.x 또는 2.x인지에 따라 다릅니다.

### Elasticsearch 1.73 이전 버전을 사용하여 Marvel 설치

Elasticsearch 1.7.3 이전 버전을 사용하는 경우 클러스터의 *모든 노드에서* 다음 단계를 수행합니다.

- 노드에 로그인하고 Elasticsearch 홈 디렉터리로 이동합니다. Linux에서 일반적인 홈 디렉터리는 `/usr/share/elasticsearch`입니다.

-  다음 명령을 실행하여 Elasticsearch에 대한 Marvel 플러그 인을 다운로드하고 설치합니다.

```bash
sudo bin/plugin -i elasticsearch/marvel/latest
```

- 노드에서 Elasticsearch를 중지하고 다시 시작합니다.

```bash
sudo service elasticsearch restart
```

- Marvel이 제대로 설치되었는지 확인하려면 웹 브라우저를 열고 URL `http://<server>:9200/_plugin/marvel`로 이동합니다. `<server>`를 클러스터에 있는 Elasticsearch 서버의 이름 또는 IP 주소로 바꿉니다. 아래에 표시된 것과 비슷한 페이지가 나타나는지 확인합니다.

![](./media/guidance-elasticsearch/performance-image20.png)


### Elasticsearch 2.0.0 이후 버전을 사용하여 Marvel 설치

Elasticsearch 2.0.0 이후 버전을 사용하는 경우 클러스터의 *모든 노드에서* 다음 태스크를 수행합니다.

노드에 로그인하고 Elasticsearch 홈 디렉터리(일반적으로 `/usr/share/elasticsearch`)로 이동합니다. 다음 명령을 실행하여 Elasticsearch에 대한 Marvel 플러그 인을 다운로드하고 설치합니다.

```bash
sudo bin/plugin install license
sudo bin/plugin install marvel-agent
```

노드에서 Elasticsearch를 중지하고 다시 시작합니다.

```bash
sudo service elasticsearch restart
```

다음 절차에서는 `<kibana-version>`을 Elasticsearch 2.0.0 또는 Elasticsearch 2.0.1을 사용하는 경우 4.2.2로 바꾸고 Elasticsearch 2.1.0 이상을 사용하는 경우 4.3.1로 바꿉니다. `<marvel-version>`를 Elasticsearch 2.0.0 또는 Elasticsearch 2.0.1을 사용하는 경우 2.0.0으로 바꾸고 Elasticsearch 2.1.0 이상을 사용하는 경우 2.1.0으로 바꿉니다. 클러스터의 *한 노드에서* 다음 태스크를 수행합니다.

노드에 로그인하고 [Elasticsearch 다운로드 웹 사이트](https://www.elastic.co/downloads/past-releases)에서 사용 중인 Elasticsearch의 버전에 대한 Kibana의 적절한 빌드를 다운로드한 다음 패키지를 추출합니다.

```bash
wget https://download.elastic.co/kibana/kibana/kibana-<kibana-version>-linux-x64.tar.gz
tar xvzf kibana-<kibana-version>-linux-x64.tar.gz
```

5601 포트를 열어 들어오는 요청을 받습니다.

```bash
sudo iptables -A INPUT -m state --state NEW -m tcp -p tcp --dport 5601 -j ACCEPT
```

Kibana 구성 폴더(`kibana-<kibana-version>-linux-x64/config`)로 이동하고 `kibana.yml` 파일을 편집하며 다음 줄을 추가합니다. `<server>`을 클러스터에 있는 Elasticsearch 서버의 이름 또는 IP 주소로 바꿉니다.

```yaml
elasticsearch.url: "http://<server>:9200"
```

Kibana bin 폴더(`kibana-<kibana-version>-linux-x64/bin`)로 이동하고 다음 명령을 실행하여 Marvel 플러그 인을 Kibana로 통합합니다.

```bash
sudo ./kibana plugin --install elasticsearch/marvel/<marvel-version>
```

Kibana를 시작합니다.

```bash
sudo nohup ./kibana &
```

Marvel 설치를 확인하려면 웹 브라우저를 열고 URL `http://<server>:5601/app/marvel`로 이동합니다. `<server>`를 Kibana를 실행하는 서버의 이름 또는 IP 주소로 바꿉니다.

아래에 표시된 것과 비슷한 페이지가 나타나는지 확인합니다(클러스터의 이름이 이미지에 표시된 이름과 다를 수 있음).

![](./media/guidance-elasticsearch/performance-image21.png)

클러스터에 해당하는 링크를 클릭합니다(위의 그림에서 elasticsearch210). 아래에 보이는 것과 비슷한 페이지가 나타나야 합니다.

![](./media/guidance-elasticsearch/performance-image22.png)

[Azure에서 Elasticsearch에 대한 데이터 수집 성능 튜닝]: guidance-elasticsearch-tuning-data-ingestion-performance.md

<!---HONumber=AcomDC_0224_2016-->