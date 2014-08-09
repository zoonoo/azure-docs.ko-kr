<properties linkid="services-linux-cassandra-with-linux" urlDisplayName="Cassandra with Linux" pageTitle="Run Cassandra with Linux on Azure" metaKeywords="" description="Explains how to run a Cassandra cluster on Linux in Azure Virtual Machines." metaCanonical="" services="virtual-machines" documentationCenter="Node.js" title="Running Cassandra with Linux on Azure and Accessing it from Node.js" authors="" solutions="" manager="" editor="" />

Azure에서 Linux 환경의 Cassandra 실행 및 Node.js에서 Cassandra에 액세스
=======================================================================

**작성자:** Hanu Kommalapati

목차
----

-   [개요](#overview)
-   [Cassandra 배포 개요](#schematic)
-   [복합 배포](#composite)
-   [Azure 가상 컴퓨터 배포](#deployment)
-   [작업 1: Linux 클러스터 배포](#task1)
-   [작업 2: 각 가상 컴퓨터에서 Cassandra 설정](#task2)
-   [작업 3: Node.js에서 Cassandra 클러스터에 액세스](#task3)
-   [결론](#conclusion)

개요
----

Azure는 Azure 테이블 저장소를 통해 NoSQL 데이터베이스 서비스를 제공하여 스키마 없이 비즈니스 개체를 저장할 수 있게 합니다. 이 서비스는 Node.JS, .NET, Java 외에 HTTP 및 REST를 사용할 수 있는 모든 언어에서 사용 가능합니다. 그러나 Cassandra, Couchbase 등의 인기 있는 NoSQL 데이터베이스는 Azure PaaS의 상태 비저장 클라우드 서비스 모델로 인해 Azure PaaS에서 실행될 수 없었습니다. 이제는 이러한 NoSQL 데이터베이스도 Azure 가상 컴퓨터 덕분에 코드베이스의 변경 없이 Azure에서 실행될 수 있습니다. 이 문서는 가상 컴퓨터에서 Cassandra 클러스터를 실행하고 Node.js에서 클러스터에 액세스하는 방법을 보여 주기 위해 작성되었습니다. 다중 데이터 센터 Cassandra 클러스터 및 연관된 백업과 복구 전략을 검토해야 하는 실제 프로덕션 운영을 위한 Cassandra 배포에 대해서는 여기에서 다루지 않습니다. 이번 실습에서는 Ubuntu 12.04 버전의 Linux 및 Cassandra 1.0.10이 사용되며, 실습 과정은 Linux 배포판에 따라 조정될 수 있습니다.

Cassandra 배포 개요
-------------------

Azure 가상 컴퓨터에는 [Cassandra](http://wiki.apache.org/cassandra/) 같은 NoSQL 데이터베이스를 사설 클라우드 환경에서 실행하듯이 손쉽게 Microsoft 공용 클라우드에서 실행할 수 있게 하는 기능이 있습니다. Azure 가상 컴퓨터 인프라의 고유한 가상 네트워크 구성을 제외하고 다른 점은 없습니다. 이 문서를 작성하는 시점에는 Cassandra를 Azure에서 관리 서비스로 이용할 수 없기 때문에 이 문서에서는 Cassandra 클러스터를 가상 컴퓨터에 설정하고 가상 컴퓨터 내에 호스트되는 다른 Linux 인스턴스에서 클러스터를 액세스하는 방법을 검토합니다. 이 문서에 있는 Node.js 코드 조각은 PaaS에 의해 호스트되는 웹 응용 프로그램 또는 웹 서비스에서 사용될 수도 있습니다. Azure의 주요 장점 중 하나는 최상의 PaaS 및 IaaS를 활용할 수 있는 복합 응용 프로그램 모델을 허용한다는 것입니다.

Cassandra 응용 프로그램 환경에 적합한 배포 모델로 자체 포함 가상 컴퓨터 배포 및 복합 배포 두 가지가 있습니다. 복합 배포에서는 PaaS에 의해 호스트되는 Azure 웹 응용 프로그램 또는 웹 서비스가 Thrift 인터페이스를 사용하여 부하 분산 장치를 통해 가상 컴퓨터에 의해 호스트되는 Cassandra 클러스터를 이용하게 됩니다. 키 공간 오류로 인해 각 Cassandra 노드가 다른 피어 노드로 요청을 위임할 경우에도 부하 분산 장치는 요청에 대해 항목별 부하 분산이 가능하도록 합니다. 또한 부하 분산 장치는 방화벽으로 보호되는 샌드박스를 만들어 데이터에 대한 제어 수준을 높여 줍니다.

복합 배포
---------

복합 배포의 목적은 PaaS 활용을 극대화하면서 가상 컴퓨터의 사용 공간을 절대적으로 최소화하여 가상 컴퓨터의 인프라 관리에 따르는 오버헤드를 줄이는 데 있습니다. 서버 관리 오버헤드를 고려하여 상태 저장 동작을 필요로 하는 구성 요소만 배포합니다. 이러한 구성 요소는 출시 기간, 소스 코드에 대한 가시성 부족, OS에 대한 저수준 액세스 등의 다양한 이유 때문에 쉽게 수정할 수 없습니다.

![복합 배포 다이어그램](./media/virtual-machines-linux-nodejs-running-cassandra/cassandra-linux1.png)

Azure 가상 컴퓨터 배포
----------------------

![가상 컴퓨터 배포](./media/virtual-machines-linux-nodejs-running-cassandra/cassandra-linux2.png)

위 다이어그램에서 4 노드 Cassandra 클러스터는 가상 컴퓨터 내에 배포되어 있고, 가상 컴퓨터는 Thrift 트래픽을 허용하도록 구성된 부하 분산 장치 뒤에 위치합니다. Azure에 의해 호스트되는 PaaS 응용 프로그램은 언어별 Thrift 라이브러리를 사용하여 클러스터에 액세스합니다. 이 라이브러리는 Java, C\#, Node.js, Python, C++ 등의 언어에서 사용할 수 있습니다. 두 번째 다이어그램에 나와 있는 자체 포함 가상 컴퓨터 배포에서는 가상 컴퓨터에 호스트되는 다른 클라우드 서비스 내에서 실행되는 응용 프로그램이 데이터를 이용합니다.

작업 1: Linux 클러스터 배포
---------------------------

가상 컴퓨터 미리 보기 릴리스 중에 Linux VM이 동일한 가상 네트워크의 일부가 되도록 하려면 모든 컴퓨터가 동일한 클라우드 서비스에 배포되어야 합니다. 클러스터를 만들기 위한 일반적인 시퀀스는 다음과 같습니다.

![클러스터 만들기 시퀀스 다이어그램](./media/virtual-machines-linux-nodejs-running-cassandra/cassandra-linux4.png)

**1단계: SSH 키 쌍 생성**

Azure는 프로비전 시간에 PEM 또는 DER 인코딩된 X509 공개 키를 필요로 합니다. [Azure에서 Linux 환경의 SSH를 사용하는 방법](http://www.windowsazure.com/ko-kr/manage/linux/how-to-guides/ssh-into-linux/)(영문)에 설명된 지침에 따라 공개/개인 키 쌍을 생성합니다. Windows 또는 Linux에서 putty.exe를 SSH 클라이언트로 사용할 계획이면 PEM 인코딩된 RSA 개인 키를 puttygen.exe를 사용하여 PPK 형식으로 변환해야 합니다. 이에 대한 지침은 [Azure에서의 Linux VM 배포를 위한 SSH 키 쌍 생성](http://blogs.msdn.com/b/hanuk/archive/2012/06/07/generating-ssh-key-pair-for-linux-vm-deployment-on-windows-azure.aspx)(영문)에서 확인할 수 있습니다.

**2단계: Ubuntu VM 만들기**

첫 번째 Ubuntu VM을 만들려면 Azure 미리 보기 포털에 로그인하고 **새로 만들기**, **가상 컴퓨터**, **갤러리에서**, **Unbuntu Server 12.xx**를 차례로 클릭한 후, 오른쪽 화살표를 클릭합니다. Linux VM을 만드는 방법을 설명하는 자습서는 [Linux를 실행하는 가상 컴퓨터 만들기](http://www.windowsazure.com/ko-kr/manage/linux/tutorials/virtual-machine-from-gallery/)(영문)를 참조하십시오.

그런 다음, VM 구성 화면에서 다음 정보를 입력합니다.

<table>
	<tr>
		<th>필드 이름</th>
		<th>필드 값</th>
		<th>설명</th>
	</tr>
	<tr>
		<td>가상 컴퓨터 이</td>
		<td>hk-cas1</td>
		<td>VM의 호스트 이름입니다.</td>
	</tr>
	<tr>
		<td>새 사용자 이름</td>
		<td>localadmin</td>
		<td>"admin"은 Ubuntu 12.xx에서 예약된 사용자 이름입니다.</td>
	</tr>
	<tr>
		<td>새 암호</td>
		<td><i>강력한 암호</i></td>
		<td></td>
	</tr>
	<tr>
		<td>암호 확인</td>
		<td><i>강력한 암호</i></td>
		<td></td>
	</tr>
	<tr>
		<td>크기</td>
		<td>작음</td>
		<td>IO 요구 사항을 기준으로 VM을 선택합니다. </td>
	</tr>
	<tr>
		<td>Secure using SSH Key for Authentication</td>
		<td>확인란을 클릭합니다.</td>
		<td>SSH 키를 사용하여 보안을 유지하려면 선택합니다.</td>
	</tr>
	<tr>
		<td>인증서</td>
		<td><i>공개 키 인증서의 파일 이름</i></td>
		<td>OpenSSL 또는 다른 도구를 사용하여 생성된 DER 또는 PEM 인코딩된 SSH 공개 키</td>
	</tr>
</table>


VM 모드 화면에서 다음 정보를 입력합니다.

<table>
	<tr>
		<th>필드 이름</th>
		<th>필드 값</th>
		<th>설명</th>
	</tr>
	<tr>
		<td>Standalone Virtual VM</td>
		<td>라디오 단추를 "선택"합니다.</td>
		<td>이 필드는 후속 VM을 위해 첫 번째 VM에 대해 적용되며, 여기서는 "Connect to Existing VM" 옵션을 사용합니다.</td>
	</tr>
	<tr>
		<td>DNS 이름</td>
		<td><i>고유 이름</i>.cloudapp.net</td>
		<td>컴퓨터에 알 수 없는 부하 분산 장치 이름을 지정합니다.</td>
	</tr>
	<tr>
		<td>저장소 계정</td>
		<td><i>기본 저장소 계정</i></td>
		<td>직접 만든 기본 저장소 계정을 사용합니다.</td>
	</tr>
	<tr>
		<td>지역/선호도 그룹/가상 네트워크</td>
		<td>미국 서부</td>
		<td>웹 응용 프로그램이 Cassandra 클러스터에 액세스하는 지역을 선택합니다.</td>
	</tr>
</table>

Cassandra 클러스터의 일부가 될 모든 가상 컴퓨터에 대해 위의 프로세스를 반복합니다. 이때 모든 컴퓨터는 동일한 네트워크의 일부가 되고 서로에게 Ping 신호를 보낼 수 있습니다. Ping이 작동하지 않으면 VM의 방화벽(예: iptables) 구성을 점검하여 ICMP가 허용되는지 확인합니다. 네트워크 연결이 성공적으로 테스트된 후에는 ICMP가 사용되지 않도록 설정하여 공격 벡터를 줄여야 합니다.

**3단계: 부하 분산형 Thrift 끝점 추가**

1단계 및 2단계 후에는 각 VM에 SSH 끝점이 이미 정의되어 있습니다. 이번에는 부하 분산형 Thrift 끝점을 공용 포트 9160으로 추가합니다. 시퀀스는 다음과 같습니다.

a. 첫 번째 VM의 세부 정보 보기에서 "끝점 추가"를 클릭합니다.

b. "가상 컴퓨터에 끝점 추가" 화면에서 "끝점 추가" 라디오 단추를 선택합니다.

c. 오른쪽 화살표를 클릭합니다.

d. "Specify endpoint details" 화면에서 다음을 입력합니다.

<table>
	<tr>
		<th >필드 이름</th>
		<th>필드 값</th>
		<th>설명</th>
	</tr>
	<tr>
		<td>이름</td>
		<td>cassandra</td>
		<td>고유한 끝점 이름이면 됩니다.</td>
	</tr>
	<tr>
		<td>프로토콜</td>
		<td>TCP</td>
		<td></td>
	</tr>
	<tr>
		<td>공용 포트</td>
		<td>9160</td>
		<td>기본 Thrift 포트입니다. </td>
	</tr>
	<tr>
		<td>개인 포트</td>
		<td>9160</td>
		<td>cassandra.yaml에서 이 값을 변경하지 않은 경우</td>
	</tr>
</table>

위의 단계를 끝내면 첫 번째 VM에서 LOAD BALANCED 필드가 "아니요"인 cassandra 끝점을 표시합니다. 이 끝점을 후속 VM에 추가하면 이 필드가 "예"로 변경되므로, 현 시점에서는 이 값을 무시해도 됩니다.

e. 이제 두 번째 VM을 선택하고 위의 프로세스를 반복하여 끝점을 추가합니다. "Load-balance traffic on an existing endpoint"를 선택하고 드롭다운 상자에서 "cassandra-960"을 사용해야 한다는 점을 제외하면 위의 프로세스와 차이가 없습니다. 이 단계에서 두 VM 모두에 매핑된 끝점의 LOAD BALANCED 상태는 "아니요"에서 "예"로 바뀌게 됩니다.

클러스터의 후속 노드에 대해 "e"를 반복합니다.

VM이 준비되었으므로 이제는 각 VM에서 Cassandra를 설정할 차례입니다. Cassandra가 여러 Linux 배포판에서 표준 구성 요소가 아니기 때문에 수동 배포 프로세스를 따르도록 하겠습니다.

[수동으로 각 VM에 소프트웨어를 설치한다는 점에 유의하십시오. 완전하게 작동하는 Cassandra VM을 설정하고 이 VM을 기본 이미지로 캡처한 후 이 이미지로부터 추가적인 인스턴스를 만드는 방법으로 프로세스의 처리 속도가 향상될 수는 있습니다. Linux 이미지 캡처에 대한 지침은 [Linux를 실행하는 가상 컴퓨터의 이미지를 캡처하는 방법](https://www.windowsazure.com/en-us/manage/linux/how-to-guides/capture-an-image/)(영문)에 나와 있습니다.]

작업 2: 각 가상 컴퓨터에서 Cassandra 설정
-----------------------------------------

**1단계: 필수 구성 요소 설치**

Cassandra는 Java Virtual Machine을 필요로 하기 때문에 Ubuntu를 비롯하여 Debian에서 파생된 배포판에 대해 다음 명령을 사용해 최신 JRE를 설치합니다.

    sudo add-apt-repository ppa:webupd8team/java
    sudo apt-get update
    sudo apt-get install oracle-java7-installer

**2단계: Cassandra 설치**

1.  SSH를 사용하여 Linux(Ubuntu) VM 인스턴스에 로그인합니다.

2.  wget을 사용하여 Cassandra 비트를 apache-cassandra-bin.tar.gz로 미러 사이트(http://cassandra.apache.org/download/)[http://cassandra.apache.org/download/]에서 추천한 사이트)에서 "\~/downloads" 디렉터리로 다운로드합니다. 버전을 알 수 없도록 하기 위해 다운로드한 파일에 버전 번호가 포함되어 있지 않습니다.

3.  다음 명령을 실행하여 tar ball을 기본 로그인 디렉터리에 압축 해제합니다.

        tar -zxvf downloads/apache-cassandra-bin.tar.gz

    위의 명령은 아카이브를 apache-cassandra- [버전] 디렉터리로 확장합니다.

4.  로그 및 데이터를 보관할 기본 디렉터리 2개를 다음과 같이 만듭니다.

         $ sudo chown -R /var/lib/cassandra
         $ sudo chown -R /var/log/cassandra

5.  Cassandra를 실행할 사용자 ID에게 쓰기 권한을 부여합니다.

        a.   sudo chown -R <user>:<group> /var/lib/cassandra
        b.  sudo chown -R <user>:<group> /var/log/cassandra
        To use current user context, replace the <user> and <group> with $USER and $GROUP

6.  다음 명령을 사용하여 apache-cassandra-[버전]/bin 디렉터리에서 Cassandra를 시작합니다.

        $ ./cassandra

위의 명령에 따라 Cassandra 노드는 백그라운드 프로세스로 시작됩니다. 포그라운드 모드로 프로세스를 시작하려면 -cassandra "f"를 사용합니다.

로그에 mx4j 오류가 표시될 수도 있습니다. mx4j가 없어도 Cassandra는 정상적으로 작동하지만, Cassandra 설치를 관리하려면 mx4j가 필요합니다. 다음 단계를 계속하기 전에 Cassandra 프로세스를 중단합니다.

**3단계: mx4j 설치**

    a)   Download mx4j: wget [http://sourceforge.net/projects/mx4j/files/MX4J%20Binary/3.0.2/mx4j-3.0.2.tar.gz/download](http://sourceforge.net/projects/mx4j/files/MX4J%20Binary/3.0.2/mx4j-3.0.2.tar.gz/download) -O mx4j.tar.gz
    b)  tar -zxvf mx4j.tar.gz
    c)  cp mx4j-23.0.2/lib/*.jar ~/apache-cassandra-<version>/lib
    d)  rm -rf mx4j-23.0.2
    e)  rm mx4j.tar.gz

이 단계에서 Cassandra 프로세스를 다시 시작합니다.

**4단계: Cassandra 설치 테스트**

thrift 클라이언트를 사용하여 연결하기 위해 Cassandra의 bin 디렉터리에서 다음 명령을 실행합니다.

    cassandra-cli -h localhost -p 9160

**5단계: 외부 연결이 가능하도록 Cassandra 설정**

기본적으로 Cassandra는 루프 백 주소만 수신 대기하도록 설정되기 때문에 외부 연결을 위해서는 다음과 같이 변경해야 합니다.

"conf/cassandra.yaml"을 편집하여 **listen\_address** 및 **rpc\_address**를 서버의 IP 주소 또는 호스트 이름으로 변경함으로써 현재 노드가 다른 노드 및 외부의 부하 분산 장치에게 표시될 수 있게 합니다.

클러스터의 모든 노드에 대해 1단계부터 5단계까지 반복합니다.

모든 개별 VM이 준비되고 필수적인 소프트웨어도 함께 준비되었으므로 이제는 시드 구성을 통해 노드 간 통신을 설정할 차례입니다. 다중 노드 클러스터 구성에 대한 자세한 내용은 <http://wiki.apache.org/cassandra/MultinodeCluster>에서 제공하는 정보를 참조하십시오.

**6단계: 다중 노드 클러스터 설정**

cassandra.yaml을 편집하여 모든 VM에서 다음 속성을 변경합니다.

**a) cluster\_name**

기본 클러스터 이름은 "Test Cluster"로 설정됩니다. 실제 적용 사례에 맞춰 이 이름을 변경합니다(예: "AppStore"). 설치 중에 테스트를 위해 "Test Cluster"라는 이름으로 클러스터를 이미 시작한 경우, 클러스터 이름 불일치 오류가 발생합니다. 이 오류를 해결하려면 /var/lib/cassandra/data/system 디렉터리에 있는 모든 파일을 삭제합니다.

**b) seeds**

여기에서 지정한 IP 주소는 링 토폴로지를 파악하기 위해 새 노드에 사용됩니다. 가장 신뢰할 수 있는 노드를 쉼표로 구분된 형식, 즉 "*host1*,*host2*" 형식을 사용하여 시드로 설정합니다. 예를 들어, "hk-ub1,hk-ub2"와 같이 설정합니다.

시드 서버가 제공하는 기본 토큰은 본 실습에서 중요한 부분이 아니기 때문에 제공된 그대로 사용할 것입니다. 최적의 토큰 생성에 대해서는 <http://wiki.apache.org/cassandra/GettingStarted>에서 제공하는 python 스크립트를 참조하십시오.

모든 노드에서 Cassandra를 다시 시작하여 위의 변경 내용을 적용합니다.

**7단계: 다중 노드 클러스터 테스트**

Cassandra의 bin 디렉터리에 설치되는 Nodetool은 클러스터 작업에 유용합니다. 여기서는 nodetool을 사용하여 다음과 같은 명령 형식으로 클러스터 설정을 확인합니다.

    $ bin/nodetool -h <hostname> -p 7199 ring

구성이 올바르면 3개 노드 클러스터에 대해 아래와 같은 정보가 표시됩니다.

<table>
	<tr>
		<td>주소</td>
		<td>DC</td>
		<td>랙</td>
		<td>가동 상태</td>
		<td>시스템 상태</td>
		<td>부하</td>
		<td>소유 비율</td>
		<td>토큰</td>
	</tr>
	<tr>
		<td></td>
		<td></td>	
		<td></td>	
		<td></td>	
		<td></td>	
		<td></td>	
		<td></td>	
		<td>149463697837832744402916220269706844972</td>	
	</tr>
	<tr>
		<td>10.26.196.68</td>
		<td>datacenter1</td>	
		<td>rack1</td>	
		<td>가동</td>	
		<td>정상</td>	
		<td>15.69 KB</td>	
		<td>25.98%</td>	
		<td>114445918355431753244435008039926455424</td>	
	</tr>
	<tr>
		<td>10.26.198.81</td>
		<td>datacenter1</td>	
		<td>rack1</td>	
		<td>가동</td>	
		<td>정상</td>	
		<td>15.69 KB</td>	
		<td>53.44%</td>	
		<td>70239176883275351288292106998553981501</td>	
	</tr>
	<tr>
		<td>10.26.198.84</td>
		<td>datacenter1</td>	
		<td>rack1</td>	
		<td>가동</td>	
		<td>정상</td>	
		<td>18.35 KB</td>	
		<td>25.98%</td>	
		<td>149463697837832744402916220269706844972</td>	
	</tr>
</table>


이 단계에서 클러스터는 "Linux 클러스터 배포" 작업 중에 만든 클라우드 서비스 URL(첫 번째 VM을 만드는 중에 지정된 DNS 이름)을 통해 Thrift 클라이언트에게 서비스를 제공할 수 있게 준비됩니다.

작업 3: Node.js에서 Cassandra 클러스터에 액세스
-----------------------------------------------

이전 작업에서 설명된 프로세스에 따라 Azure에 Linux VM을 만듭니다. 이 VM은 Cassandra 클러스터에 액세스하기 위한 클라이언트로 사용되기 때문에 독립 실행형 VM이어야 합니다. Node.js, NPM 및 [cassandra-client](https://github.com/racker/node-cassandra-client)를 github로부터 설치한 후, 이 VM에서 Cassandra 클러스터에 연결합니다.

**1단계: Node.js 및 NPM 설치**

a) 필수 조건 설치

    sudo apt-get install g++ libssl-dev apache2-utils make

b) GitHub의 소스를 사용하여 컴파일하고 설치합니다. repo를 복제하려면 먼저 git core 런타임을 설치해야 합니다.

    sudo apt-get install git-core

c) 노드 repo 복제

    git clone git://github.com/joyent/node.git

d) 위의 명령으로 "node"라는 이름의 디렉터리가 만들어집니다. 다음 명령 시퀀스를 실행하여 node.js를 컴파일 및 설치합니다.

    cd node
    ./configure
    make
    sudo make install

e) 다음 명령을 실행하여 stable 바이너리로부터 NPM을 설치합니다.

    curl http://npmjs.org/install.sh | sh

**2단계: cassandra-client 패키지 설치**

    npm cassandra-client 

**3단계: Cassandra 저장소 준비**

Cassandra 저장소는 RDBMS 용어의 데이터베이스 및 테이블 구조와 유사한 KEYSPACE 및 COLUMNFAMILY라는 개념을 사용합니다. KEYSAPCE는 COLUMNFAMILY 정의 집합을 포함합니다. 아래의 중첩된 그림에 나온 대로 각 COLUMNFAMILY는 행 집합을 포함하고, 각 행은 여러 개의 열을 포함합니다.

![행 및 열](./media/virtual-machines-linux-nodejs-running-cassandra/cassandra-linux3.png)

위의 데이터 구조를 만들고 쿼리하는 node.js 액세스의 예를 보여 주기 위해 이전에 배포한 Cassandra 클러스터를 사용할 것입니다. 고객 데이터 저장을 위해 클러스터를 준비하는 기본적인 작업을 수행하는 간단한 node.js 스크립트를 만듭니다. 이 스크립트에 나와 있는 기법은 node.js 웹 응용 프로그램 또는 웹 서비스에서 쉽게 사용될 수 있습니다. 아래의 코드 조각은 작동 방식을 보여 주기 위한 것일 뿐이며 실제 솔루션에 사용할 경우에는 개선의 여지(예: 보안, 로깅, 확장성)가 매우 많다는 점에 유의하십시오.

필요한 변수를 스크립트 범위에서 정의하여 cassandra-client 모듈의 PooledConnection 및 자주 사용되는 keyspace 이름과 keyspace 연결 매개 변수를 포함하겠습니다.

    casdemo.js: 
    var pooledCon = require('cassandra-client').PooledConnection;
    var ksName = "custsupport_ks";
    var ksConOptions = { hosts: ['<azure_svc_name>.cloudapp.net:9160'], 
                         keyspace: ksName, use_bigints: false };

고객 데이터를 저장할 수 있게 준비하려면 먼저 다음 스크립트 예제를 사용하여 KEYSPACE를 만들어야 합니다.

    casdemo.js: 
    function createKeyspace(callback){
       var cql = 'CREATE KEYSPACE ' + ksName + ' WITH 
       strategy_class=SimpleStrategy AND strategy_options:replication_factor=1';
       var sysConOptions = { hosts: ['<azure_svc_name>.cloudapp.net:9160'],  
                             keyspace: 'system', use_bigints: false };
       var con = new pooledCon(sysConOptions);
       con.execute(cql,[],function(err) {
       if (err) {
         console.log("Failed to create Keyspace: " + ksName);
         console.log(err);
       }
       else {
         console.log("Created Keyspace: " + ksName);
         callback(ksConOptions, populateCustomerData);
       }
       });
       con.shutdown();
    } 

createKeysapce 함수는 콜백 함수를 인수로 받아들입니다. 열 패밀리를 만들려면 KEYSPACE가 반드시 필요하기 때문에 COLUMNFAMILY 만들기 함수를 실행하기 위해 콜백 함수를 인수로 받습니다. 응용 프로그램 KEYSPACE 정의를 위해서는 "system" KEYSPACE에 연결해야 합니다. [CQL(Cassandra 쿼리 언어)](http://cassandra.apache.org/doc/cql/CQL.html)(영문)은 이러한 코드 조각을 통한 클러스터와의 상호 작용에서 일관되게 사용됩니다. 위의 스크립트에서 작성된 CQL에는 매개 변수 마커가 없기 때문에 PooledConnection.execute() 메서드가 실행될 때 빈 매개 변수 컬렉션("[]")을 사용합니다.

키 공간이 성공적으로 만들어지면 다음 코드 조각에 있는 createColumnFamily() 함수가 실행되어 필요한 COLUMNFAMILY 정의를 생성합니다.

	casdemo.js: 
	//Creates COLUMNFAMILY
	function createColumnFamily(ksConOptions, callback){
	  var params = ['customers_cf','custid','varint','custname',
	                'text','custaddress','text'];
	  var cql = 'CREATE COLUMNFAMILY ? (? ? PRIMARY KEY,? ?, ? ?)';
	var con =  new pooledCon(ksConOptions);
	  con.execute(cql,params,function(err) {
	      if (err) {
	         console.log("Failed to create column family: " + params[0]);
	         console.log(err);
	      }
	      else {
	         console.log("Created column family: " + params[0]);
	         callback();
	      }
	  });
	  con.shutdown();
	} 

매개 변수가 있는 CQL 템플릿은 params 개체와 결합되어 COLUMNFAMILY 만들기를 위한 올바른 CQL을 생성합니다. COLUMNFAMILY가 성공적으로 만들어진 후, 제공된 콜백 함수(여기서는 populateCustomerData())가 비동기 호출 체인의 일부로서 호출됩니다.

	casdemo.js: 
	//populate Data
	function populateCustomerData() {
	   var params = ['John','Infinity Dr, TX', 1];
	   updateCustomer(ksConOptions,params);
	
	   params = ['Tom','Fermat Ln, WA', 2];
	   updateCustomer(ksConOptions,params);
	}
	
	//update will also insert the record if none exists
	function updateCustomer(ksConOptions,params)
	{
	  var cql = 'UPDATE customers_cf SET custname=?,custaddress=? where 
	             custid=?';
	  var con = new pooledCon(ksConOptions);
	  con.execute(cql,params,function(err) {
	      if (err) console.log(err);
	      else console.log("Inserted customer : " + params[0]);
	  });
	  con.shutdown();
	}

populateCustomerData()는 행 2개를 customers\_cf라는 COLUMNFAMILY에 삽입합니다. Cassandra 쿼리 언어에서 UPDATE는 삽입하려는 레코드가 INSERT CQL 문을 중복 호출하는 프로세스에 아직 없는 경우 해당 레코드를 삽입합니다.

지금까지 콜백 체인은 createKeyspace()부터 createColumnFamily()를 거쳐 populateCustomerData()까지 이어졌습니다. 이제는 다음 코드 조각을 통해 코드를 실행할 차례입니다.

    casdemo.js:
    var pooledCon = require('cassandra-client').PooledConnection;
    var ksName = "custsupport_ks";
    var ksConOptions = { hosts: ['<azure_svc_name>.cloudapp.net:9160'], 
                         keyspace: ksName, use_bigints: false };

    createKeyspace(createColumnFamily);
    //rest of the not shown

셸 프롬프트에서 다음 명령을 실행하여 스크립트를 실행합니다.

    //the following command will create the KEYSPACE, COLUMNFAMILY and //inserts two customer records
    $ node casdemo.js

readCustomer() 메서드는 Azure에 의해 호스트되는 클러스터에 액세스하고, CQL 쿼리를 실행한 후에 검색된 JSON 조각을 표시합니다.

    casdemo.js: 
    //read the two rows inserted above
    function readCustomer(ksConOptions)
    {
      var cql = 'SELECT * FROM customers_cf WHERE custid IN (1,2)';
      var con = new pooledCon(ksConOptions);
      con.execute(cql,[],function(err,rows) {
          if (err) 
             console.log(err);
          else 
             for (var i=0; i<rows.length; i++)
                console.log(JSON.stringify(rows[i]));
        });
       con.shutdown();
    } 

아래와 같이 casdemo.js를 수정하여 위의 함수를 추가하고 이전에 호출한 createKeyspace() 메서드를 주석 처리한 후에 함수를 호출합니다.

    casdemo.js: 
    var pooledCon = require('cassandra-client').PooledConnection;
    var ksName = "custsupport_ks";
    var ksConOptions = { hosts: ['<azure_svc_name>.cloudapp.net:9160'], 
                         keyspace: ksName, use_bigints: false };

    //createKeyspace(createColumnFamily);
    readCustomer(ksConOptions)
    //rest of the code below not shown

결론
----

Azure 가상 컴퓨터에는 Linux(Microsoft 파트너가 제공한 이미지) 및 Windows 가상 컴퓨터를 만들어 아무런 변경 없이 기존 서버 제품 및 응용 프로그램을 마이그레이션할 수 있는 기능이 있습니다. 이 문서에서 다룬 Cassandra NoSQL 데이터베이스 서버도 이와 같이 마이그레이션할 수 있습니다. 이 문서에서 설정한 Cassandra 클러스터는 Windows 및 Linux OS 환경에서 Azure에 의해 호스트되는 클라우드 서비스, 타사 공용 클라우드 및 사설 클라우드로부터 액세스될 수 있습니다. 이 문서에서는 클라이언트로 node.js를 다루었지만 .NET, Java 및 기타 언어 환경에서도 Cassandra에 액세스할 수 있습니다.

