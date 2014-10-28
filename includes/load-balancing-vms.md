<properties title="Load Balancing for Azure Infrastructure Services" pageTitle="Load Balancing for Azure Infrastructure Services" description="Describes the facilities to perform load balancing with Traffic Manager and load balancer." metaKeywords="" services="virtual-machines" solutions="" documentationCenter="" authors="josephd" videoId="" scriptId="" manager="timlt" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="josephd"></tags>

# Azure 인프라 서비스를 위한 부하 분산

Azure 인프라 서비스에 사용할 수 있는 두 가지 수준의 부하 분산이 있습니다.

-   **DNS 수준**: 서로 다른 데이터 센터에 위치한 서로 다른 클라우드 서비스, 서로 다른 데이터 센터에 위치한 서로 다른 Azure 웹 사이트 또는 외부 끝점으로의 트래픽 부하를 분산합니다. 트래픽 관리자 및 라운드 로빈 부하 분산 방법으로 수행됩니다.
-   **네트워크 수준**: 클라우드 서비스의 서로 다른 컴퓨터에 수신되는 인터넷 트래픽 부하를 분산하거나 클라우드 서비스 또는 가상 네트워크에 있는 가상 컴퓨터 간의 트래픽 부하를 분산합니다. Azure 부하 분산 장치를 통해 수행됩니다.

## 클라우드 서비스 및 웹 사이트를 위한 트래픽 관리자 부하 분산

Azure 트래픽 관리자를 사용하여 끝점으로의 사용자 트래픽 분산을 제어할 수 있습니다. 여기에는 클라우드 서비스, 웹 사이트, 외부 사이트 및 기타 트래픽 관리자 프로필이 포함될 수 있습니다. 트래픽 관리자는 인터넷 리소스의 도메인 이름에 대한 DNS(Domain Name System) 쿼리에 지능형 정책 엔진을 적용하여 작동합니다. 클라우드 서비스 또는 웹 사이트는 전 세계의 여러 데이터 센터에서 실행될 수 있습니다.

REST 또는 Windows PowerShell을 사용하여 외부 끝점 또는 트래픽 관리자 프로필을 끝점으로 구성할 수 있습니다.

Azure 트래픽 관리자는 세 가지 부하 분산 방법을 사용하여 트래픽을 분산합니다.

-   **장애 조치(Failover)**: 모든 트래픽에 대해 기본 끝점을 사용하되 기본을 사용할 수 없는 경우에 대비하여 백업을 제공하려면 이 방법을 선택하십시오.
-   **성능**: 끝점이 서로 다른 지역에 있고 요청하는 클라이언트가 "가장 가까운"(즉, 지연 시간이 가장 짧은) 끝점을 사용하도록 하려면 이 방법을 선택하십시오.
-   **라운드 로빈:** 같은 데이터 센터에 있는 클라우드 서비스 집합 간에 또는 서로 다른 데이터 센터에 있는 클라우드 서비스나 웹 사이트 간에 부하를 분산하려면 이 방법을 선택하십시오.

자세한 내용은 [트래픽 관리자 부하 분산 방법][트래픽 관리자 부하 분산 방법](영문)을 참조하십시오.

다음 그림은 서로 다른 클라우드 서비스 간에 트래픽을 분산하기 위한 라운드 로빈 부하 분산 방법의 예를 보여 줍니다.

![부하 분산][부하 분산]

기본 프로세스는 다음과 같습니다.

1.  인터넷 클라이언트가 웹 서비스에 해당하는 도메인 이름을 쿼리합니다.
2.  DNS가 이름 쿼리 요청을 트래픽 관리자에게 전달합니다.
3.  트래픽 관리자가 라운드 로빈 목록에 있는 클라우드 서비스의 DNS 이름을 반환합니다. 인터넷 클라이언트의 DNS 서버가 이름을 IP 주소로 풀어서 인터넷 클라이언트에 보냅니다.
4.  선택된 클라우드 서비스에 인터넷 클라이언트가 연결합니다.

자세한 내용은 [트래픽 관리자][트래픽 관리자](영문)를 참조하십시오.

## 가상 컴퓨터를 위한 Azure 부하 분산

같은 클라우드 서비스나 가상 네트워크에 있는 가상 컴퓨터는 전용 IP 주소를 사용하여 서로 간에 직접 통신할 수 있습니다. 클라우드 서비스 또는 가상 네트워크 외부에 있는 컴퓨터와 서비스는 구성된 끝점을 통해서만 클라우드 서비스 또는 가상 네트워크에 있는 가상 컴퓨터와 통신할 수 있습니다. 끝점은 공용 IP 주소 및 포트를 Azure 클라우드 서비스 내 가상 컴퓨터 또는 웹 역할의 전용 IP 주소 및 포트에 매핑하는 것입니다.

Azure 부하 분산 장치는 부하 분산 집합이라고 하는 구성에서 특정 유형의 수신 트래픽을 여러 가상 컴퓨터나 서비스에 임의로 분산합니다. 예를 들어 웹 요청 트래픽의 부하를 여러 웹 서버 또는 웹 역할에 분배할 수 있습니다.

다음 그림은 공용 및 개인 TCP 포트 80에서 세 대의 가상 컴퓨터 사이에 공유되는 표준(암호화되지 않음) 웹 트래픽의 부하 분산 끝점을 보여 줍니다. 이 세 대의 가상 컴퓨터는 부하 분산 집합에 속합니다.

![부하 분산][1]

자세한 내용은 [Azure 부하 분산 장치][Azure 부하 분산 장치](영문)를 참조하십시오. 부하 분산 집합을 만드는 과정은 [부하 분산 집합 구성][부하 분산 집합 구성](영문)을 참조하십시오.

Azure는 클라우드 서비스 또는 가상 네트워크 내에서 부하를 분산할 수도 있습니다. 이것을 내부 부하 분산이라고 하며 다음과 같은 방법으로 사용될 수 있습니다.

-   다중 계층 응용 프로그램의 여러 계층에서 서버 간에 부하를 분산합니다(예: 웹 계층과 데이터베이스 계층 사이).
-   추가적인 부하 분산 장치 하드웨어 또는 소프트웨어 없이도 Azure에서 호스트되는 LOB(기간 업무) 응용 프로그램의 부하를 분산합니다.
-   트래픽 부하가 분산되는 컴퓨터 집합에 온-프레미스 서버를 포함합니다.

Azure 부하 분산과 마찬가지로 내부 부하 분산 집합을 구성함으로써 내부 부하 분산을 수월하게 만들 수 있습니다.

다음 그림은 교차 프레미스 가상 네트워크에서 세 개의 가상 컴퓨터 간에 공유되는 LOB(기간 업무) 응용 프로그램의 내부 부하 분산 끝점 예를 보여 줍니다.

![부하 분산][2]

자세한 내용은 [내부 부하 분산][내부 부하 분산](영문)을 참조하십시오. 부하 분산 집합을 만드는 과정은 [내부 부하 분산 집합 구성][내부 부하 분산 집합 구성](영문)을 참조하십시오.

<!-- LINKS -->

  [트래픽 관리자 부하 분산 방법]: http://msdn.microsoft.com/ko-kr/library/azure/dn339010.aspx
  [부하 분산]: ./media/load-balancing-vms/TMSummary.png
  [트래픽 관리자]: http://msdn.microsoft.com/ko-kr/library/azure/hh745750.aspx
  [1]: ./media/load-balancing-vms/LoadBalancing.png
  [Azure 부하 분산 장치]: http://msdn.microsoft.com/ko-kr/library/azure/dn655058.aspx
  [부하 분산 집합 구성]: http://msdn.microsoft.com/ko-kr/library/azure/dn655055.aspx
  [2]: ./media/load-balancing-vms/LOBServers.png
  [내부 부하 분산]: http://msdn.microsoft.com/ko-kr/library/azure/dn690121.aspx
  [내부 부하 분산 집합 구성]: http://msdn.microsoft.com/ko-kr/library/azure/dn690125.aspx
