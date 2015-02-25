<properties pageTitle="Azure에서 Linux 환경의 Cassandra 실행" description="Azure 가상 컴퓨터의 Linux에서 Cassandra 클러스터를 실행하는 방법에 대해 알아봅니다." services="virtual-machines" documentationCenter="nodejs" authors="hanuk" manager="timlt" editor=""/>

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="12/01/2014" ms.author="hanuk"/>





<h1><a id = ""></a>Azure에서 Linux 환경의 Cassandra 실행 및 Node.js에서 Cassandra에 액세스 </h1>
**작성자:** Hanu Kommalapati

## 목차##

- [개요] []
- [단일 지역 배포] []
- [단일 지역 Cassandra 클러스터 테스트] []
- [다중 지역 배포] []
- [다중 지역 Cassandra 클러스터 테스트] []
- [Node.js에서 Cassandra 클러스터 테스트] []
- [결론] []

##<a id="overview"> </a>개요 ##
Microsoft Azure는 운영 체제, 응용 프로그램 서버, 메시징 미들웨어뿐 아니라 상용 및 오픈 소스 모델의 SQL 및 NoSQL 데이터베이스를 포함하는 Microsoft 및 타사 소프트웨어를 실행하는 개방형 클라우드 플랫폼입니다. Azure를 비롯한 공용 클라우드에 복원 서비스를 빌드하려면 응용 프로그램 서버 및 저장소 계층 둘 다의 신중한 계획과 세밀한 아키텍처가 필요합니다. Cassandra의 분산 저장소 아키텍처는 클러스터 오류에 대한 내결함성이 있는 고가용성 시스템 빌드에 도움이 됩니다. Cassandra는 cassandra.apache.org에서 Apache Software Foundation에 의해 유지 관리되는 클라우드 규모의 NoSQL 데이터베이스입니다. Cassandra는 Java로 작성되었으므로 Windows 및 Linux 플랫폼에서 모두 실행됩니다. 

이 문서는 Microsoft Azure 가상 컴퓨터 및 가상 네트워크를 활용하여 Cassandra를 단일 및 다중 데이터 센터로 Ubuntu에 배포하는 과정을 보여 주는 데 중점을 둡니다. 프로덕션에 최적화된 작업을 위한 클러스터 배포는 필요한 복제, 데이터 일관성, 처리량 및 고가용성 요구 사항을 충족하기 위해 다중 디스크 노드 구성, 적절한 링 토폴로지 디자인 및 데이터 모델링이 필요하므로 이 문서의 범위를 벗어납니다. 

이 문서에서는 인프라 배포를 훨씬 용이하게 하는 Cassandra 클러스터 비교 Docker, Chef 또는 Puppet 빌드와 관련된 작업을 보여 주는 기본적인 접근 방법을 사용합니다.  

##<a id="depmodels"> </a>배포 모델 ##
Microsoft Azure 네트워킹을 사용하면 미세 조정된 네트워크 보안을 위해 액세스를 제한할 수 있는 격리된 개인 클러스터를 배포할 수 있습니다.  이 문서에서는 기본 수준의 Cassandra 배포를 보여 줄 것이므로 일관성 수준 및 처리량에 최적화된 저장소 디자인에는 중점을 두지 않습니다. 다음은 가상 클러스터에 대한 네트워킹 요구 사항 목록입니다.

- 외부 시스템은 Azure 내부나 외부에서 Cassandra 데이터베이스에 액세스할 수 없어야 합니다.
- Cassandra 클러스터는 쓰리프트 트래픽에 대한 부하 분산 장치 뒤에 있어야 합니다.
- 클러스터 가용성 향상을 위해 각 데이터 센터의 두 그룹에 Cassandra 노드를 배포해야 합니다. 
- 응용 프로그램 서버 팜만 데이터베이스에 직접 액세스할 수 있도록 클러스터를 잠가야 합니다.
- SSH 이외의 공용 네트워킹 끝점이 없어야 합니다.
- 각 Cassandra 노드에 고정된 내부 IP 주소가 필요합니다.

Cassandra는 작업의 분산 특성에 따라 단일 Azure 지역이나 여러 지역에 배포할 수 있습니다. 다중 지역 배포 모델을 활용하면 동일한 Cassandra 인프라를 통해 특정 지역에 더 가까운 곳에서 최종 사용자에게 서비스를 제공할 수 있습니다. Cassandra의 기본 제공 노드 복제는 여러 데이터 센터에서 발생한 다중 마스터 쓰기를 동기화하고 일관된 데이터 뷰를 응용 프로그램에 제공합니다. 또한 다중 지역 배포는 광범위한 Azure 서비스 중단 위험의 완화에도 도움이 될 수 있습니다. Cassandra의 조정 가능한 일관성 및 복제 토폴로지는 응용 프로그램의 다양한 RPO 요구를 충족하는 데 유용합니다. 


###<a id="oneregion"> </a>단일 지역 배포 ###
단일 지역 배포로 시작하여 다중 지역 모델을 만드는 방법을 알아보겠습니다. Azure 가상 네트워킹은 위에서 언급한 네트워크 보안 요구 사항을 충족할 수 있도록 격리된 서브넷을 만드는 데 사용됩니다.  단일 지역 배포를 만드는 방법에서 설명된 프로세스는 Ubuntu 14.04 LTS 및 Cassandra 2.08을 사용하지만 다른 Linux 변형에도 쉽게 이 프로세스를 채택할 수 있습니다. 다음은 단일 지역 배포의 몇 가지 시스템 특성입니다.  

**고가용성:** 그림 1에 표시된 Cassandra 노드는 고가용성을 위해 여러 장애 도메인에 간에 노드가 배포되도록 두 개의 가용성 집합에 배포됩니다. 각 가용성 집합이 주석으로 추가된 VM은 2개의 장애 도메인에 매핑됩니다. Microsoft Azure는 장애 도메인의 개념을 사용하여 계획되지 않은 중단 시간(예: 하드웨어 또는 소프트웨어 오류)을 관리하는 반면, 예약된 중단 시간 관리에는 업그레이드 도메인의 개념(예: 호스트 또는 게스트 OS 패치/업그레이드, 응용 프로그램 업그레이드)이 사용됩니다. 고가용성 실현에서 장애 도메인과 업그레이드 도메인의 역할에 대해서는 [Azure 응용 프로그램에 대한 재해 복구 및 고가용성](http://msdn.microsoft.com/ko-kr/library/dn251004.aspx)을 참조하세요.

![Single region deployment](./media/virtual-machines-linux-nodejs-running-cassandra/cassandra-linux1.png)

그림 1: 단일 지역 배포

이 문서를 작성할 당시, Azure는 특정 장애 도메인에 대한 VM 그룹의 명시적 매핑을 허용하지 않습니다. 따라서 그림 1에 표시된 배포 모델에서도 통계상 모든 가상 컴퓨터가 4개가 아닌 2개의 장애 도메인에 매핑될 수 있습니다. 

**쓰리프트 트래픽의 부하 분산:** 웹 서버 내부의 쓰리프트 클라이언트 라이브러리는 내부 부하 분산 장치를 통해 클러스터에 연결합니다. 이 경우 Cassandra 클러스터를 호스트하는 클라우드 서비스 컨텍스트의 "data" 서브넷(그림 1 참조)에 내부 부하 분산 장치를 추가해야 합니다. 내부 부하 분산 장치가 정의되고 나면 이전에 정의한 내부 부하 분산 장치 이름으로 부하 분산 집합에 주석을 지정하여 각 노드에 부하 분산 끝점을 추가해야 합니다. 자세한 내용은 [Azure 내부 부하 분산](http://msdn.microsoft.com/ko-kr/library/azure/dn690121.aspx)을 참조하세요.

**클러스터 시드:** 새 노드는 시드 노드와 통신하여 클러스터의 토폴로지를 검색하므로 가장 가용성이 큰 노드를 시드에 대해 선택하는 것이 중요합니다. 단일 실패 지점을 방지하기 위해 각 가용성 집합에서 하나의 노드가 시드 노드로 지정됩니다.

**복제 계수 및 일관성 수준:** Cassandra의 기본 제공 고가용성 및 데이터 지속성은 복제 계수(RF - 클러스터에 저장되는 각 행의 복사본 수) 및 일관성 수준(호출자에게 결과를 반환하기 전에 읽거나 쓸 복제본 수)으로 구분됩니다. 복제 계수는 KEYSPACE(관계형 데이터베이스와 유사) 생성 중 지정되는 반면 일관성 수준은 CRUD 쿼리를 실행하는 동안 지정됩니다. 일관성에 대한 자세한 내용과 쿼럼 계산 공식은 [일관성에 대한 구성](http://www.datastax.com/documentation/cassandra/2.0/cassandra/dml/dml_config_consistency_c.html)의 Cassandra 설명서를 참조하세요.

Cassandra는 두 가지 유형의 데이터 무결성 모델, 즉 일관성과 최종 일관성을 지원합니다. 복제 계수 및 일관성 수준이 결합되어 쓰기 작업이 완료되는 즉시 데이터가 일치하는지 또는 최종적으로 일치하는지를 결정합니다. 예를 들어 QUORUM을 일관성 수준으로 지정하면 항상 데이터 일관성이 유지되는 반면 QUORUM 실현에 필요한 쓸 복제본 수보다 낮은 일관성 수준(예: ONE)에서는 데이터가 최종적으로 일치합니다. 

복제 계수가 3이고 읽기/쓰기 일관성 수준이 QUORUM(일관성을 위해 2개 노드를 읽거나 씀)인 위에 표시된 8 노드 클러스터는 응용 프로그램에서 실패를 감지하기 전에 복제 그룹당 최대 1개 노드의 이론적 손실을 감당할 수 있습니다. 이 경우 모든 주요 공간에서 읽기/쓰기 요청의 균형이 잘 조정되어 있다고 가정합니다.  다음은 배포된 클러스터에 사용할 매개 변수입니다. 

단일 지역 Cassandra 클러스터 구성:

<table>
<tr>

<th>클러스터 매개 변수</th><th>값</th><th>설명</th></tr>
<tr><td>노드 수(N) </td><td>8</td><td>클러스터의 총 노드 수</td></tr>
<tr><td>복제 계수(RF)</td><td>	3 </td><td>	지정된 행의 복제본 수 </td></tr>
<tr><td>일관성 수준(쓰기)</td><td>	QUORUM[(RF/2) +1) = 2] [공식 결과는 버림됨] </td><td> 응답이 호출자에게 전송되기 전에 최대 2개의 복제본에 씁니다. 세 번째 복제본은 최종 일관성 방식으로 작성됩니다. </td></tr>
<tr><td>일관성 수준(읽기)	</td><td>QUORUM[(RF/2) +1 = 2] [공식 결과는 버림됨]</td><td>	호출자에게 응답을 보내기 전에 2개의 복제본을 읽습니다.</td></tr>
<tr><td>복제 전략 </td><td>	NetworkTopologyStrategy[자세한 내용은 Cassandra 설명서의 [데이터 복제](http://www.datastax.com/documentation/cassandra/2.0/cassandra/architecture/architectureDataDistributeReplication_c.html) 참조]</td><td>	배포 토폴로지를 이해하고 모든 복제본이 동일한 랙에 배포되지 않도록 노드에 복제본을 배치합니다.</td></tr>
<tr><td>Snitch	</td><td>GossipingPropertyFileSnitch[자세한 내용은 Cassandra 설명서의 [Snitch](http://www.datastax.com/documentation/cassandra/2.0/cassandra/architecture/architectureSnitchesAbout_c.html) 참조]</td><td>	NetworkTopologyStrategy는 snitch 개념을 사용하여 토폴로지를 파악합니다. GossipingPropertyFileSnitch를 사용하면 데이터 센터 및 랙에 대한 각 노드의 매핑을 보다 잘 제어할 수 있습니다. 클러스터는 가십을 사용하여 이 정보를 전파합니다. PropertyFileSnitch에 비해 동적 IP 설정이 훨씬 간단합니다. </td></tr>
</TABLE>

**Cassandra 클러스터에 대한 Azure 고려 사항:** Microsoft Azure 가상 컴퓨터 기능은 디스크 지속성을 위해 Azure Blob 저장소를 사용합니다. Azure 저장소는 높은 내구성을 위해 각 디스크의 복제본을 3개 저장합니다. 즉, Cassandra 테이블에 삽입된 각 데이터 행이 3개의 복제본에 이미 저장되어 있으므로 RF(복제 계수)가 1인 경우에도 데이터 일관성이 이미 유지됩니다. 복제 계수가 1일 경우의 주요 문제는 하나의 Cassandra 노드가 실패해도 응용 프로그램에서 가동 중지 시간이 발생한다는 것입니다. 그러나 Azure 패브릭 컨트롤러에서 인식되는 문제(예: 하드웨어, 시스템 소프트웨어 오류)로 인해 노드가 다운된 경우 동일한 저장소 드라이브를 사용하여 새 노드를 대신 프로비전합니다. 이전 노드를 대체할 새 노드를 프로비전하는 데 몇 분 정도 걸릴 수 있습니다. 마찬가지로 게스트 OS 변경, Cassandra 업그레이드 및 응용 프로그램 변경과 같은 계획된 유지 관리 활동의 경우 Azure 패브릭 컨트롤러에서 클러스터 노드에 대해 롤링 업그레이드를 수행합니다. 롤링 업그레이드도 한 번에 몇 개의 노드를 다운시킬 수 있으므로 클러스터의 몇몇 파티션에서 잠시 가동 중지 시간이 발생할 수 있습니다. 그러나 데이터는 기본 제공 Azure 저장소 중복성 때문에 손실되지 않습니다.

Azure에 배포된 시스템에 고가용성(예: 8.76시간/년과 동등한 약 99.9, 자세한 내용은 [고가용성](http://en.wikipedia.org/wiki/High_availability) 참조))이 필요하지 않은 경우 RF=1 및 일관성 수준=ONE으로 실행할 수 있습니다.  고가용성 요구 사항이 있는 응용 프로그램의 경우 RF=3 및 일관성 수준=QUORUM은 복제본 한 개당 노드 한 개의 가동 중지 시간을 감당합니다. 기존 배포(예: 온-프레미스)의 RF=1은 디스크 오류 등의 문제로 인한 데이터 손실 때문에 사용할 수 없습니다.   

## 다중 지역 배포 ##
위에서 설명한 Cassandra의 데이터 센터 인식 복제 및 일관성 모델은 외부 도구를 사용할 필요 없이 바로 다중 지역 배포를 도와줍니다. 이것이 다중 마스터 쓰기를 위한 데이터베이스 미러링 설정이 매우 복잡할 수 있는 기존 관계형 데이터베이스와의 차이점입니다. 다중 지역 설정의 Cassandra는 다음을 비롯한 사용 시나리오에서 유용할 수 있습니다. 

**근접 기반 배포:** 테넌트 사용자와 지역 간의 명확한 매핑을 사용하는 다중 테넌트 응용 프로그램은 다중 지역 클러스터의 낮은 대기 시간을 활용할 수 있습니다. 예를 들어 교육 기관의 학습 관리 시스템에서 미국 동부 및 미국 서부 지역에 분산 클러스터를 배포하여 해당 캠퍼스에 대한 트랜잭션과 분석을 처리할 수 있습니다. 데이터는 읽기 및 쓰기 시 로컬에서 일치할 수 있고 최종적으로 두 지역에서 모두 일치할 수 있습니다. 미디어 배포, 전자 상거래 및 지역적으로 집중된 사용자 기반에 서비스를 제공하는 모든 항목과 같은 다른 예제도 이 배포 모델의 좋은 사용 사례입니다.

**고가용성:** 중복성은 소프트웨어 및 하드웨어의 고가용성 실현에 중요한 요소입니다. 자세한 내용은 Microsoft Azure에서 신뢰할 수 있는 클라우드 시스템 빌드를 참조하세요. Microsoft Azure에서 진정한 중복성을 실현하는 신뢰할 수 있는 방법은 다중 지역 클러스터를 배포하는 것뿐입니다. 능동-능동 또는 능동-수동 모드로 응용 프로그램을 배포할 수 있으며, 지역 중 하나가 다운되면 Azure 트래픽 관리자가 활성 지역으로 트래픽을 리디렉션할 수 있습니다. 단일 지역 배포에서는 가용성이 99.9일 경우 다음 공식에 따라 2개 지역 배포를 통해 99.9999의 가용성을 실현할 수 있습니다. (1-(1-0.999) * (1-0.999))*100). 자세한 내용은 위의 문서를 참조하세요.

**재해 복구:** 제대로 디자인된 다중 지역 Cassandra 클러스터는 심각한 센터 중단 문제를 극복할 수 있습니다. 한 지역이 다운되면 다른 지역에 배포된 응용 프로그램에서 최종 사용자에게 서비스를 제공할 수 있습니다. 다른 비즈니스 연속성 구현과 마찬가지로 응용 프로그램에서는 비동기 파이프라인의 데이터에서 발생하는 약간의 데이터 손실을 감당해야 합니다. 그러나 Cassandra는 기존의 데이터베이스 복구 프로세스에 걸리는 시간보다 훨씬 신속하게 복구합니다. 그림 2에서는 각 지역에 8개 노드가 있는 일반적인 다중 지역 배포 모델을 보여 줍니다. 두 지역은 대칭의 동일성을 위해 상대 지역의 미러 이미지입니다. 실제 디자인은 작업 유형(예: 트랜잭션 또는 분석), RPO, RTO, 데이터 일관성 및 가용성 요구 사항에 따라 달라집니다.

![Multi region deployment](./media/virtual-machines-linux-nodejs-running-cassandra/cassandra-linux2.png)

그림 2: 다중 지역 Cassandra 배포

### 네트워크 통합 ###
두 지역에 있는 개인 네트워크에 배포된 가상 컴퓨터 집합은 VPN 터널을 사용하여 서로 통신합니다. VPN 터널은 네트워크 배포 프로세스 중 프로비전된 두 개의 소프트웨어 게이트웨이를 연결합니다. 두 지역은 "web" 및 "data" 서브넷 측면에서 네트워크 아키텍처가 유사합니다. Azure 네트워킹을 사용하면 필요한 개수만큼 서브넷을 만들고 네트워크 보안에 필요한 경우 ACL을 적용할 수 있습니다. 디자인하는 동안 클러스터 토폴로지 데이터 센터 간 통신 대기 시간과 네트워크 트래픽의 경제적 영향을 고려해야 합니다. 

### 다중 데이터 센터 배포에 대한 데이터 일관성 ###
분산 배포에서는 클러스터 토폴로지가 처리량 및 고가용성에 미치는 영향에 주의해야 합니다. 할당량이 모든 데이터 센터의 가용성에 의존하지 않도록 RF 및 일관성 수준을 선택해야 합니다. 
높은 일관성이 필요한 시스템의 경우 일관성 수준(읽기 및 쓰기)을 LOCAL_QUORUM으로 설정하면 데이터가 원격 데이터 센터에 비동기적으로 복제되는 동안 로컬 읽기 및 쓰기가 로컬 노드에서 충족됩니다.  표 2에는 문서의 뒷부분에서 설명하는 다중 지역 클러스터의 구성 세부 정보가 요약되어 있습니다. 

**2개 지역 Cassandra 클러스터 구성**

<table>
<tr><th>클러스터 매개 변수 </th><th>값	</th><th>설명 </th></tr>
<tr><td>노드 수(N) </td><td>	8 + 8	</td><td> 클러스터의 총 노드 수 </td></tr>
<tr><td>복제 계수(RF)</td><td>	3 	</td><td>지정된 행의 복제본 수 </td></tr>
<tr><td>일관성 수준(쓰기)	</td><td>LOCAL_QUORUM [(sum(RF)/2) +1) = 4] [공식 결과는 버림됨]	</td><td>2개 노드는 첫 번째 데이터 센터에 동기적으로 기록됩니다. 할당량에 필요한 추가 2개 노드는 두 번째 데이터 센터에 비동기적으로 기록됩니다. </td></tr>
<tr><td>일관성 수준(읽기)</td><td>	LOCAL_QUORUM [((RF/2) +1) = 2] [공식 결과는 버림됨]	</td><td>읽기 요청은 한 지역에서만 충족됩니다. 응답이 클라이언트로 다시 전송되기 전에 2개 노드를 읽습니다.  </td></tr>
<tr><td>복제 전략 </td><td>	NetworkTopologyStrategy[자세한 내용은 Cassandra 설명서의 [데이터 복제](http://www.datastax.com/documentation/cassandra/2.0/cassandra/architecture/architectureDataDistributeReplication_c.html) 참조] </td><td>	배포 토폴로지를 이해하고 모든 복제본이 동일한 랙에 배포되지 않도록 노드에 복제본을 배치합니다.  </td></tr>
<tr><td>Snitch</td><td> GossipingPropertyFileSnitch[자세한 내용은 Cassandra 설명서의 [Snitch](http://www.datastax.com/documentation/cassandra/2.0/cassandra/architecture/architectureSnitchesAbout_c.html) 참조] </td><td>NetworkTopologyStrategy는 snitch 개념을 사용하여 토폴로지를 파악합니다. GossipingPropertyFileSnitch를 사용하면 데이터 센터 및 랙에 대한 각 노드의 매핑을 보다 잘 제어할 수 있습니다. 클러스터는 가십을 사용하여 이 정보를 전파합니다. PropertyFileSnitch에 비해 동적 IP 설정이 훨씬 간단합니다. </td></tr> 
</table> 

##소프트웨어 구성##
배포 중에 다음 소프트웨어 버전이 사용됩니다.

<table>
<tr><th>소프트웨어</th><th>원본</th><th>버전</th></tr>
<tr><td>JRE	</td><td>[JRE 8](http://www.oracle.com/technetwork/java/javase/downloads/server-jre8-downloads-2133154.html) </td><td>8U5</td></tr>
<tr><td>JNA	</td><td>[JNA](https://github.com/twall/jna) </td><td> 3.2.7</td></tr>
<tr><td>Cassandra</td><td>[Apache Cassandra 2.0.8](http://www.apache.org/dist/cassandra/2.0.8/apache-cassandra-2.0.8-bin.tar.gz)</td><td> 2.0.8</td></tr>
<tr><td>Ubuntu	</td><td>[Mcrosoft Azure Portal](http://azure.microsoft.com) </td><td>14.04 LTS</td></tr>
</table>

JRE를 다운로드하려면 Oracle 라이선스를 수동으로 승인해야 하므로 배포를 간소화하려면 클러스터 배포 전에 만들려는 Ubuntu 템플릿 이미지에 나중에 업로드할 필수 소프트웨어를 데스크톱에 모두 다운로드합니다. 

로컬 데스크톱의 잘 알려진 downloads 디렉터리(예: Windows의 %TEMP%/downloads 또는 Linux/Mac의 ~/downloads)에 위 소프트웨어를 다운로드합니다. 

### UBUNTU VM 만들기 ###
이 프로세스 단계에서는 여러 개의 Cassandra 노드를 프로비전하는 데 이미지를 다시 사용할 수 있도록 필수 조건 소프트웨어로 Ubuntu 이미지를 만듭니다.  
####1단계: SSH 키 쌍 생성####
Azure는 프로비전 시간에 PEM 또는 DER 인코딩된 X509 공개 키를 필요로 합니다. Azure에서 Linux와 함께 SSH를 사용하는 방법에 설명된 지침에 따라 공개/개인 키 쌍을 생성합니다. Windows 또는 Linux에서 putty.exe를 SSH 클라이언트로 사용할 계획이면 PEM 인코딩된 RSA 개인 키를 puttygen.exe를 사용하여 PPK 형식으로 변환해야 합니다. 이에 대한 지침은 위의 웹 페이지를 참조하세요. 

####2단계: Ubuntu 템플릿 VM 만들기####
템플릿 VM을 만들려면 azure.microsoft.com 포털에 로그인하고 다음 시퀀스를 사용합니다. 새로 만들기, 계산, 가상 컴퓨터, 갤러리에서, UBUNTU, Ubuntu Server 14.04 LTS를 차례로 클릭한 다음 오른쪽 화살표를 클릭합니다. Linux VM을 만드는 방법을 설명하는 자습서는 Linux를 실행하는 가상 컴퓨터 만들기를 참조하세요.

"가상 컴퓨터 구성" 화면 #1에서 다음 정보를 입력합니다. 

<table>
<tr><th>필드 이름              </td><td>       필드 값               </td><td>         설명                </td><tr>
<tr><td>버전 릴리스 날짜    </td><td> 드롭다운에서 날짜 선택</td><td></td><tr>
<tr><td>가상 컴퓨터 이름    </td><td> cass-template	               </td><td> VM의 호스트 이름입니다. </td><tr>
<tr><td>계층	                 </td><td> 표준	                       </td><td> 기본값을 그대로 둡니다.              </td><tr>
<tr><td>크기	                 </td><td> A1                              </td><td>IO 요구에 따라 VM을 선택합니다. 여기서는 기본값을 그대로 둡니다. </td><tr>
<tr><td> 새 사용자 이름	         </td><td> localadmin	                   </td><td> "admin"은 Ubuntu 12.xx 이상에서 예약된 사용자 이름입니다.</td><tr>
<tr><td> 인증	     </td><td> 확인란을 클릭합니다.                 </td><td>SSH 키를 사용하여 보안을 유지하려면 선택합니다. </td><tr>
<tr><td> 인증서	         </td><td> 공개 키 인증서의 파일 이름 </td><td> 이전에 생성된 공개 키를 사용합니다.</td><tr>
<tr><td> 새 암호	</td><td> 강력한 암호 </td><td> </td><tr>
<tr><td> 암호 확인	</td><td> 강력한 암호 </td><td></td><tr>
</table>

"가상 컴퓨터 구성" 화면 #2에서 다음 정보를 입력합니다. 

<table>
<tr><th>필드 이름             </th><th> 필드 값	                   </th><th> 설명                                 </th></tr>
<tr><td> 클라우드 서비스	</td><td> 새 클라우드 서비스 만들기	</td><td>클라우드 서비스는 가상 컴퓨터와 같은 계산 리소스의 컨테이너입니다.</td></tr>
<tr><td> 클라우드 서비스 DNS 이름	</td><td>ubuntu-template.cloudapp.net	</td><td>컴퓨터에 알 수 없는 부하 분산 장치 이름을 지정합니다.</td></tr>
<tr><td> 지역/선호도 그룹/가상 네트워크 </td><td>	미국 서부	</td><td> 웹 응용 프로그램이 Cassandra 클러스터에 액세스하는 지역을 선택합니다.</td></tr>
<tr><td>저장소 계정 </td><td>	기본값 사용	</td><td>기본 저장소 계정이나 특정 지역에 미리 생성된 저장소 계정을 사용합니다.</td></tr>
<tr><td>가용성 집합 </td><td>	없음 </td><td>	비워 둡니다.</td></tr>
<tr><td>끝점	</td><td>기본값 사용 </td><td>	기본 SSH 구성을 사용합니다. </td></tr>
</table>

오른쪽 화살표를 클릭하고 화면 #3의 기본값을 그대로 둔 다음 "확인" 단추를 클릭하여 VM 프로비저닝 프로세스를 완료합니다. 몇 분 후에 이름이 "ubuntu-template"인 VM이 "실행 중" 상태가 되어야 합니다. 

###필수 소프트웨어 설치###
####1단계: tarball 업로드 ####
scp 또는 pscp를 사용하여 다음 명령 형식으로 이전에 다운로드한 소프트웨어를 ~/downloads 디렉터리로 복사합니다. 

#####pscp server-jre-8u5-linux-x64.tar.gz localadmin@hk-cas-template.cloudapp.net:/home/localadmin/downloads/server-jre-8u5-linux-x64.tar.gz #####

JRE 및 Cassandra 비트에 대해 위 명령을 반복합니다. 

####2단계: 디렉터리 구조 준비 및 보관 추출####
VM에 로그인한 다음 아래 bash 스크립트를 사용하여 슈퍼 사용자로 디렉터리 구조를 만들고 소프트웨어를 추출합니다.

	#!/bin/bash
	CASS_INSTALL_DIR="/opt/cassandra"
	JRE_INSTALL_DIR="/opt/java"
	CASS_DATA_DIR="/var/lib/cassandra"
	CASS_LOG_DIR="/var/log/cassandra"
	DOWNLOADS_DIR="~/downloads"
	JRE_TARBALL="server-jre-8u5-linux-x64.tar.gz"
	CASS_TARBALL="apache-cassandra-2.0.8-bin.tar.gz"
	SVC_USER="localadmin"
	
	RESET_ERROR=1
	MKDIR_ERROR=2
	
	reset_installation ()
	{
	   rm -rf $CASS_INSTALL_DIR 2> /dev/null
	   rm -rf $JRE_INSTALL_DIR 2> /dev/null
	   rm -rf $CASS_DATA_DIR 2> /dev/null
	   rm -rf $CASS_LOG_DIR 2> /dev/null
	}
	make_dir ()
	{
	   if [ -z "$1" ]
	   then
	      echo "make_dir: invalid directory name"
	      exit $MKDIR_ERROR
	   fi
	   
	   if [ -d "$1" ]
	   then
	      echo "make_dir: directory already exists"
	      exit $MKDIR_ERROR
	   fi
	
	   mkdir $1 2>/dev/null
	   if [ $? != 0 ]
	   then
	      echo "directory creation failed"
	      exit $MKDIR_ERROR
	   fi
	}
	
	unzip()
	{
	   if [ $# == 2 ]
	   then
	      tar xzf $1 -C $2
	   else
	      echo "archive error"
	   fi
	   
	}
	
	if [ -n "$1" ]
	then
	   SVC_USER=$1
	fi
	
	reset_installation 
	make_dir $CASS_INSTALL_DIR
	make_dir $JRE_INSTALL_DIR
	make_dir $CASS_DATA_DIR
	make_dir $CASS_LOG_DIR
	
	#unzip JRE and Cassandra 
	unzip $HOME/downloads/$JRE_TARBALL $JRE_INSTALL_DIR
	unzip $HOME/downloads/$CASS_TARBALL $CASS_INSTALL_DIR
	
	#Change the ownership to the service credentials
	
	chown -R $SVC_USER:$GROUP $CASS_DATA_DIR
	chown -R $SVC_USER:$GROUP $CASS_LOG_DIR
	echo "edit /etc/profile to add JRE to the PATH"
	echo "installation is complete"


이 스크립트를 vim 창에 붙여 넣는 경우 다음 명령을 사용하여 캐리지 리턴('\r')을 제거해야 합니다.

	tr -d '\r' <infile.sh >outfile.sh

####3단계: etc/profile 편집####
다음 내용을 끝에 추가합니다. 

	JAVA_HOME=/opt/java/jdk1.8.0_05 
	CASS_HOME= /opt/cassandra/apache-cassandra-2.0.8
	PATH=$PATH:$HOME/bin:$JAVA_HOME/bin:$CASS_HOME/bin
	export JAVA_HOME
	export CASS_HOME
	export PATH

####4단계: 프로덕션 시스템용 JNA 설치####
다음 명령 시퀀스를 사용합니다. 
다음 명령은 jna-3.2.7.jar 및 jna-platform-3.2.7.jar을 /usr/share.java 디렉터리에 설치합니다.
sudo apt-get install libjna-java 

Cassandra 시작 스크립트에서 이러한 jar을 찾을 수 있도록 $CASS_HOME/lib 디렉터리에 바로 가기 링크를 만듭니다.

	ln -s /usr/share/java/jna-3.2.7.jar $CASS_HOME/lib/jna.jar

	ln -s /usr/share/java/jna-platrom-3.2.7.jar $CASS_HOME/lib/jna-platform.jar

####5단계: cassandra.yaml 구성####
모든 가상 컴퓨터에 필요한 구성을 반영하도록 각 VM에서 cassandra.yaml을 편집합니다.[실제 프로비저닝 중에는 이 내용이 조정됨]. 

<table>
<tr><th>필드 이름   </th><th> 값  </th><th>	설명 </th></tr>
<tr><td>cluster_name </td><td>	"CustomerService"	</td><td> 배포를 반영하는 이름을 사용합니다.</td></tr> 
<tr><td>listen_address	</td><td>[비워 둠]	</td><td> "localhost"를 삭제합니다. </td></tr>
<tr><td>rpc_addres   </td><td>[비워 둠]	</td><td> "localhost"를 삭제합니다. </td></tr>
<tr><td>seeds	</td><td>"10.1.2.4, 10.1.2.6, 10.1.2.8"	</td><td>시드로 지정된 모든 IP 주소 목록입니다.</td></tr>
<tr><td>endpoint_snitch </td><td> org.apache.cassandra.locator.GossipingPropertyFileSnitch </td><td> NetworkTopologyStrateg에서 VM의 랙과 데이터 센터를 유추하는 데 사용됩니다.</td></tr>
</table>

####6단계: VM 이미지 캡처####
이전에 만든 호스트 이름(hk-cas-template.cloudapp.net) 및 SSH 개인 키를 사용하여 가상 컴퓨터에 로그인합니다. 명령 ssh 또는 putty.exe를 사용하여 로그인하는 방법에 대한 자세한 내용은 Azure에서 Linux와 함께 SSH를 사용하는 방법을 참조하세요. 

다음 작업 시퀀스를 실행하여 이미지를 캡처합니다.
#####1. 프로비전 해제#####
"sudo waagent -deprovision+user" 명령을 사용하여 가상 컴퓨터 인스턴스 특정 정보를 제거합니다. 이미지 캡처 프로세스에 대한 자세한 내용은 템플릿으로 사용할 [Linux 가상 컴퓨터를 캡처하는 방법](http://azure.microsoft.com/ko-kr/documentation/articles/virtual-machines-linux-capture-image/)을 참조하세요. 

#####2: VM 종료#####
가상 컴퓨터를 강조 표시하고 아래쪽 명령 모음에서 종료 링크를 클릭합니다.

#####3: 이미지 캡처#####
가상 컴퓨터를 강조 표시하고 아래쪽 명령 모음에서 캡처 링크를 클릭합니다. 다음 화면에서 이미지 이름(예: hk-cas-2-08-ub-14-04-2014071) 및 적절한 이미지 설명을 제공하고 "확인" 표시를 클릭하여 캡처 프로세스를 마칩니다.

이 작업은 몇 초 정도 걸리며, 이미지 갤러리의 내 이미지 섹션에서 해당 이미지를 사용할 수 있습니다. 이미지가 성공적으로 캡처되면 원본 VM이 자동으로 삭제됩니다. 

##단일 지역 배포 프로세스##
**1단계: 가상 네트워크 만들기**
관리 포털에 로그인한 다음 표에 나열된 특성을 사용하여 가상 네트워크를 만듭니다. 프로세스의 자세한 단계는 [관리 포털에서 클라우드 전용 가상 네트워크 구성](http://msdn.microsoft.com/ko-kr/library/azure/dn631643.aspx)을 참조하세요.      

<table>
<tr><th>VM 특성 이름</th><th>값</th><th>설명</th></tr>
<tr><td>이름</td><td>vnet-cass-west-us</td><td></td></tr>	
<tr><td>지역</td><td>미국 서부</td><td></td></tr>	
<tr><td>DNS 서버	</td><td>없음</td><td>DNS 서버를 사용하지 않으므로 이 특성을 무시합니다.</td></tr>
<tr><td>지점 및 사이트 간 VPN 구성</td><td>없음</td><td> 이 특성을 무시합니다.</td></tr>
<tr><td>사이트 간 VPN 구성</td><td>None</td><td> 이 특성을 무시합니다.</td></tr>
<tr><td>주소 공간</td><td>10.1.0.0/16</td><td></td></tr>	
<tr><td>시작 IP</td><td>10.1.0.0</td><td></td></tr>	
<tr><td>CIDR </td><td>/16 (65531)</td><td></td></tr>
</table>

다음 서브넷을 추가합니다. 

<table>
<tr><th>이름</th><th>시작 IP</th><th>CIDR</th><th>설명</th></tr>
<tr><td>web</td><td>10.1.1.0</td><td>/24 (251)</td><td>웹 팜에 대한 서브넷입니다.</td></tr>
<tr><td>data</td><td>10.1.2.0</td><td>/24 (251)</td><td>데이터베이스 노드에 대한 서브넷입니다.</td></tr>
</table>

Data 및 Web 서브넷은 이 문서를 범위를 벗어난 네트워크 보안 그룹을 통해 보호할 수 있습니다.  

**2단계: 가상 컴퓨터 프로비전** 
이전에 만든 이미지를 사용하여 클라우드 서버 "hk-c-svc-west"에 다음 가상 컴퓨터를 만들고 아래와 같이 해당 서브넷에 바인딩합니다. 

<table>
<tr><th>컴퓨터 이름    </th><th>서브넷	</th><th>IP 주소	</th><th>가용성 집합</th><th>DC/랙</th><th>시드 여부</th></tr>
<tr><td>hk-c1-west-us	</td><td>data	</td><td>10.1.2.4	</td><td>hk-c-aset-1	</td><td>dc =WESTUS rack =rack1 </td><td>예</td></tr>
<tr><td>hk-c2-west-us	</td><td>data	</td><td>10.1.2.5	</td><td>hk-c-aset-1	</td><td>dc =WESTUS rack =rack1	</td><td>아니요 </td></tr>
<tr><td>hk-c3-west-us	</td><td>data	</td><td>10.1.2.6	</td><td>hk-c-aset-1	</td><td>dc =WESTUS rack =rack2	</td><td>예</td></tr>
<tr><td>hk-c4-west-us	</td><td>data	</td><td>10.1.2.7	</td><td>hk-c-aset-1	</td><td>dc =WESTUS rack =rack2	</td><td>아니요 </td></tr>
<tr><td>hk-c5-west-us	</td><td>data	</td><td>10.1.2.8	</td><td>hk-c-aset-2	</td><td>dc =WESTUS rack =rack3	</td><td>예</td></tr>
<tr><td>hk-c6-west-us	</td><td>data	</td><td>10.1.2.9	</td><td>hk-c-aset-2	</td><td>dc =WESTUS rack =rack3	</td><td>아니요 </td></tr>
<tr><td>hk-c7-west-us	</td><td>data	</td><td>10.1.2.10	</td><td>hk-c-aset-2	</td><td>dc =WESTUS rack =rack4	</td><td>예</td></tr>
<tr><td>hk-c8-west-us	</td><td>data	</td><td>10.1.2.11	</td><td>hk-c-aset-2	</td><td>dc =WESTUS rack =rack4	</td><td>아니요 </td></tr>
<tr><td>hk-w1-west-us	</td><td>web	</td><td>10.1.1.4	</td><td>hk-w-aset-1	</td><td>                       </td><td>해당 없음</td></tr>
<tr><td>hk-w2-west-us	</td><td>web	</td><td>10.1.1.5	</td><td>hk-w-aset-1	</td><td>                       </td><td>해당 없음</td></tr>
</table>

위의 VM 목록을 만들려면 다음 프로세스가 필요합니다.

1.  특정 지역에 빈 클라우드 서비스를 만듭니다.
2.	이전에 캡처된 이미지에서 VM을 만든 다음 이전에 만든 가상 네트워크에 연결합니다. 모든 VM에 대해 이 단계를 반복합니다.
3.	클라우드 서비스에 내부 부하 분산 장치를 추가하고 "data" 서브넷에 연결합니다.
4.	이전에 만든 각 VM에 대해 이전에 만든 내부 부하 분산 장치에 연결된 부하 분산 집합을 통해 쓰리프트 트래픽에 대한 부하 분산 끝점을 추가합니다.

위 프로세스는 Azure 관리 포털을 사용하여 실행할 수 있습니다. Windows 컴퓨터를 사용하고(Windows 컴퓨터에 대한 액세스 권한이 없는 경우 Azure에서 VM 사용) 다음 PowerShell 스크립트를 사용하여 VM 8개를 모두 자동으로 프로비전합니다.

**List1: 가상 컴퓨터 프로비전을 위한 PowerShell 스크립트**
		
		#Tested with Azure Powershell - November 2014	
		#This powershell script deployes a number of VMs from an existing image inside an Azure region
		#Import your Azure subscription into the current Powershell session before proceeding
		#The process: 1. create Azure Storage account, 2. create virtual network, 3.create the VM template, 2. crate a list of VMs from the template
		
		#fundamental variables - change these to reflect your subscription
		$country="us"; $region="west"; $vnetName = "your_vnet_name";$storageAccount="your_storage_account"
		$numVMs=8;$prefix = "hk-cass";$ilbIP="your_ilb_ip"
		$subscriptionName = "Azure_subscription_name"; 
		$vmSize="ExtraSmall"; $imageName="your_linux_image_name"
		$ilbName="ThriftInternalLB"; $thriftEndPoint="ThriftEndPoint"
		
		#generated variables
		$serviceName = "$prefix-svc-$region-$country"; $azureRegion = "$region $country"
		
		$vmNames = @()
		for ($i=0; $i -lt $numVMs; $i++)
		{
		   $vmNames+=("$prefix-vm"+($i+1) + "-$region-$country" );
		}
		
		#select an Azure subscription already imported into Powershell session
		Select-AzureSubscription -SubscriptionName $subscriptionName -Current
		Set-AzureSubscription -SubscriptionName $subscriptionName -CurrentStorageAccountName $storageAccount
		
		#create an empty cloud service
		New-AzureService -ServiceName $serviceName -Label "hkcass$region" -Location $azureRegion
		Write-Host "Created $serviceName"
		
		$VMList= @()   # stores the list of azure vm configuration objects
		#create the list of VMs
		foreach($vmName in $vmNames)
		{
		   $VMList += New-AzureVMConfig -Name $vmName -InstanceSize ExtraSmall -ImageName $imageName |
		   Add-AzureProvisioningConfig -Linux -LinuxUser "localadmin" -Password "Local123" |
		   Set-AzureSubnet "data"
		}
		
		New-AzureVM -ServiceName $serviceName -VNetName $vnetName -VMs $VMList
		
		#Create internal load balancer
		Add-AzureInternalLoadBalancer -ServiceName $serviceName -InternalLoadBalancerName $ilbName -SubnetName "data" -StaticVNetIPAddress "$ilbIP"
		Write-Host "Created $ilbName"
		#Add add the thrift endpoint to the internal load balancer for all the VMs
		foreach($vmName in $vmNames)
		{
		    Get-AzureVM -ServiceName $serviceName -Name $vmName |
		        Add-AzureEndpoint -Name $thriftEndPoint -LBSetName "ThriftLBSet" -Protocol tcp -LocalPort 9160 -PublicPort 9160 -ProbePort 9160 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -InternalLoadBalancerName $ilbName | 
		        Update-AzureVM 
		
		    Write-Host "created $vmName"     
		}

**3단계: 각 VM에서 Cassandra 구성**

VM에 로그인하고 다음을 수행합니다. 

* $CASS_HOME/conf/cassandra-rackdc.properties를 편집하여 데이터 센터 및 랙 속성을 지정합니다.
      
       dc =EASTUS, rack =rack1

* cassandra.yaml을 편집하여 시드 노드를 아래와 같이 구성합니다.
     
       Seeds: "10.1.2.4,10.1.2.6,10.1.2.8,10.1.2.10"

**4단계: VM 시작 및 클러스터 테스트**

노드 중 하나(예: hk-c1-west-us)에 로그인하고 다음 명령을 실행하여 클러스터의 상태를 확인합니다. 
       
       nodetool -h 10.1.2.4 -p 7199 status 

8 노드 클러스터의 경우 아래와 유사한 표시가 나타나야 합니다. 

<table>
<tr><th>상태</th></th>주소	</th><th>부하	</th><th>토큰	</th><th>소유 비율 </th><th>호스트 ID	</th><th>랙</th></tr>
<tr><th>UN	</td><td>10.1.2.4 	</td><td>87.81 KB	</td><td>256	</td><td>38.0%	</td><td>Guid(제거됨)</td><td>rack1</td></tr>
<tr><th>UN	</td><td>10.1.2.5 	</td><td>41.08 KB	</td><td>256	</td><td>68.9%	</td><td>Guid(제거됨)</td><td>rack1</td></tr>
<tr><th>UN	</td><td>10.1.2.6 	</td><td>55.29 KB	</td><td>256	</td><td>68.8%	</td><td>Guid(제거됨)</td><td>rack2</td></tr>
<tr><th>UN	</td><td>10.1.2.7 	</td><td>55.29 KB	</td><td>256	</td><td>68.8%	</td><td>Guid(제거됨)</td><td>rack2</td></tr>
<tr><th>UN	</td><td>10.1.2.8 	</td><td>55.29 KB	</td><td>256	</td><td>68.8%	</td><td>Guid(제거됨)</td><td>rack3</td></tr>
<tr><th>UN	</td><td>10.1.2.9 	</td><td>55.29 KB	</td><td>256	</td><td>68.8%	</td><td>Guid(제거됨)</td><td>rack3</td></tr>
<tr><th>UN	</td><td>10.1.2.10 	</td><td>55.29 KB	</td><td>256	</td><td>68.8%	</td><td>Guid(제거됨)</td><td>rack4</td></tr>
<tr><th>UN	</td><td>10.1.2.11 	</td><td>55.29 KB	</td><td>256	</td><td>68.8%	</td><td>Guid(제거됨)</td><td>rack4</td></tr>
</table>

##<a id="testone"> </a>단일 지역 클러스터 테스트##
클러스터를 테스트하려면 다음 단계를 사용합니다.

1.    Powershell 명령 Get-AzureInternalLoadbalancer commandlet을 사용하여 내부 부하 분산 장치의 IP 주소를 가져옵니다(예: 10.1.2.101). 명령 구문은 다음과 같습니다. Get-AzureLoadbalancer -ServiceName "hk-c-svc-west-us"[IP 주소와 함께 내부 부하 분산 장치의 세부 정보 표시]
2.	Putty 또는 ssh를 사용하여 웹 팜 VM(예: hk-w1-west-us)에 로그인합니다.
3.	$CASS_HOME/bin/cqlsh 10.1.2.101 9160을 실행합니다. 
4.	다음 CQL 명령을 사용하여 클러스터가 작동하는지 확인합니다.

		CREATE KEYSPACE customers_ks WITH REPLICATION = { 'class' : 'SimpleStrategy', 'replication_factor' : 3 };	
		USE customers_ks;
		CREATE TABLE Customers(customer_id int PRIMARY KEY, firstname text, lastname text);
		INSERT INTO Customers(customer_id, firstname, lastname) VALUES(1, 'John', 'Doe');
		INSERT INTO Customers(customer_id, firstname, lastname) VALUES (2, 'Jane', 'Doe');
		
		SELECT * FROM Customers;

아래와 같은 표시가 나타나야 합니다.

<table>
  <tr><th> customer_id </th><th> firstname </th><th> lastname </th></tr>
  <tr><td> 1 </td><td> John </td><td> Doe </td></tr>
  <tr><td> 2 </td><td> Jane </td><td> Doe </td></tr>
</table>

4단계에서 만든 keyspace는 replication_factor 3으로 SimpleStrategy를 사용합니다. SimpleStrategy는 단일 데이터 세터 배포에 권장되고 NetworkTopologyStrategy는 다중 데이터 세터 배포에 권장됩니다. replication_factor가 3이면 노드 오류에 대한 내결함성이 제공됩니다. 

##<a id="tworegion"> </a>다중 지역 배포 프로세스##
완료된 단일 지역 배포를 활용하며 두 번째 지역 설치를 위해 동일한 프로세스를 반복합니다. 단일 지역 배포와 다중 지역 배포 간의 주요 차이점은 지역 간 통신을 위한 VPN 터널 설정입니다. 네트워크 설치에서 시작하여 VM을 프로비전하고 Cassandra를 구성하겠습니다. 

###1단계: 두 번째 지역에 가상 네트워크 만들기###
관리 포털에 로그인한 다음 표에 나열된 특성을 사용하여 가상 네트워크를 만듭니다. 프로세스의 자세한 단계는 [관리 포털에서 클라우드 전용 가상 네트워크 구성](http://msdn.microsoft.com/ko-kr/library/azure/dn631643.aspx)을 참조하세요.      

<table>
<tr><th>특성 이름    </th><th>값	</th><th>설명</th></tr>
<tr><td>이름	</td><td>vnet-cass-east-us</td><td></td></tr>	
<tr><td>지역	</td><td>미국 동부</td><td></td></tr>	
<tr><td>DNS 서버		</td><td></td><td>DNS 서버를 사용하지 않으므로 이 특성을 무시합니다.</td></tr>
<tr><td>지점 및 사이트 간 VPN 구성</td><td></td><td>		이 특성을 무시합니다.</td></tr>
<tr><td>사이트 간 VPN 구성</td><td></td><td>		이 특성을 무시합니다.</td></tr>
<tr><td>주소 공간	</td><td>10.2.0.0/16</td><td></td></tr>	
<tr><td>시작 IP	</td><td>10.2.0.0	</td><td></td></tr>
<tr><td>CIDR	</td><td>/16 (65531)</td><td></td></tr>
</table>	

다음 서브넷을 추가합니다. 
<table>
<tr><th>이름    </th><th>시작 IP	</th><th>CIDR	</th><th>설명</th></tr>
<tr><td>web	</td><td>10.2.1.0	</td><td>/24 (251)	</td><td>웹 팜에 대한 서브넷입니다.</td></tr>
<tr><td>data	</td><td>10.2.2.0	</td><td>/24 (251)	</td><td>데이터베이스 노드에 대한 서브넷입니다.</td></tr>
</table>


###2단계: 로컬 네트워크 만들기###
Azure 가상 네트워킹의 로컬 네트워크는 개인 클라우드 또는 다른 Azure 지역을 비롯한 원격 사이트에 매핑되는 프록시 주소 공간입니다. 이 프록시 주소 공간은 네트워크를 올바른 네트워킹 대상에 라우팅하기 위해 원격 게이트웨이에 바인딩됩니다. VNET 간 연결 설정에 대한 자세한 내용은 [VNet 연결 구성](http://msdn.microsoft.com/ko-kr/library/azure/dn690122.aspx)을 참조하세요. 

다음 세부 정보당 두 개의 로컬 네트워크를 만듭니다.

<table>
<tr><th>네트워크 이름          </th><th>VPN 게이트웨이 주소	</th><th>주소 공간	</th><th>설명</th></tr>
<tr><td>hk-lnet-map-to-east-us</td><td>	23.1.1.1	</td><td>10.2.0.0/16	</td><td>로컬 네트워크를 만드는 동안 자리 표시자 게이트웨이 주소를 제공합니다. 게이트웨이를 만들고 나면 실제 게이트웨이 주소가 채워집니다. 주소 공간이 해당 원격 VNET과 정확히 일치하는지 확인합니다. 여기서는 미국 동부 지역에 생성된 VNET입니다.</td></tr>
<tr><td>hk-lnet-map-to-west-us	</td><td>23.2.2.2	</td><td>10.1.0.0/16	</td><td>로컬 네트워크를 만드는 동안 자리 표시자 게이트웨이 주소를 제공합니다. 게이트웨이를 만들고 나면 실제 게이트웨이 주소가 채워집니다. 주소 공간이 해당 원격 VNET과 정확히 일치하는지 확인합니다. 여기서는 미국 서부 지역에 생성된 VNET입니다.</td></tr>
</table>


###3단계: "로컬" 네트워크를 해당 VNET에 매핑###
서비스 관리 포털에서 다음 세부 정보당 각 vnet을 선택하고 "구성"을 클릭한 다음 "로컬 네트워크에 연결", 로컬 네트워크를 차례로 선택합니다. 

<table>
<tr><th>가상 네트워크 </th><th>로컬 네트워크</th></tr>
<tr><td>hk-vnet-west-us	</td><td>hk-lnet-map-to-east-us</td></tr>
<tr><td>hk-vnet-east-us	</td><td>hk-lnet-map-to-west-us</td></tr>
</table>

###4단계: VNET1 및 VNET2에 게이트웨이 만들기###
두 가상 네트워크의 대시보드에서 게이트웨이 만들기를 클릭합니다. 그러면 VPN 게이트웨이 프로비저닝 프로세스가 트리거됩니다. 몇 분 후에 각 가상 네트워크의 대시보드에 실제 게이트웨이 주소가 표시되어야 합니다. 
###5단계: "로컬" 네트워크를 해당 "게이트웨이" 주소로 업데이트###
두 로컬 네트워크를 편집하여 자리 표시자 게이트웨이 IP 주소를 방금 프로비전한 게이트웨이의 실제 IP 주소로 바꿉니다. 다음 매핑을 사용합니다. 

<table>
<tr><th>로컬 네트워크    </th><th>가상 네트워크 게이트웨이</th></tr>
<tr><td>hk-lnet-map-to-east-us </td><td>hk-vnet-west-us의 게이트웨이</td></tr>
<tr><td>hk-lnet-map-to-west-us </td><td>hk-vnet-east-us의 게이트웨이</td></tr>
</table>

###6단계: 공유 키 업데이트###
다음 Powershell 스크립트를 사용하여 각 VPN 게이트웨이의 IPSec 키를 업데이트합니다[두 게이트웨이에 대해 모두 sake 키 사용]. 
Set-AzureVNetGatewayKey -VNetName hk-vnet-east-us -LocalNetworkSiteName hk-lnet-map-to-west-us -SharedKey D9E76BKK
Set-AzureVNetGatewayKey -VNetName hk-vnet-west-us -LocalNetworkSiteName hk-lnet-map-to-east-us -SharedKey D9E76BKK 

###6단계: VNET 간 연결 설정###
Azure 서비스 관리 포털에서 두 가상 네트워크의 "대시보드" 메뉴를 사용하여 게이트웨이 간 연결을 설정합니다. 아래쪽 도구 모음의 "연결" 메뉴 항목을 사용합니다. 몇 분 후에 대시보드에 연결 정보가 그래픽으로 표시되어야 합니다.

###7단계: 지역 #2에 가상 컴퓨터 만들기 ###
동일한 단계를 수행하여 지역 #1 배포에서 설명한 대로 Ubuntu 이미지를 만들거나 이미지 VHD 파일을 지역 #2에 있는 Azure 저장소 계정에 복사하여 이미지를 만듭니다. 이 이미지를 사용하고 새 클라우드 서비스 hk-c-svc-east-us에 다음 가상 컴퓨터 목록을 만듭니다. 

<table>
<tr></th>컴퓨터 이름 </th><th>서브넷</th><th>IP 주소</th><th>가용성 집합</th><th>DC/랙</th><th>시드 여부</th></tr>
<tr><td>hk-c1-east-us	</td><td>data	</td><td>10.2.2.4	</td><td>hk-c-aset-1	</td><td>dc =EASTUS rack =rack1	</td><td>예</td></tr>
<tr><td>hk-c2-east-us	</td><td>data	</td><td>10.2.2.5	</td><td>hk-c-aset-1	</td><td>dc =EASTUS rack =rack1	</td><td>아니요 </td></tr>
<tr><td>hk-c3-east-us	</td><td>data	</td><td>10.2.2.6	</td><td>hk-c-aset-1	</td><td>dc =EASTUS rack =rack2	</td><td>예</td></tr>
<tr><td>hk-c5-east-us	</td><td>data	</td><td>10.2.2.8	</td><td>hk-c-aset-2	</td><td>dc =EASTUS rack =rack3	</td><td>예</td></tr>
<tr><td>hk-c6-east-us	</td><td>data	</td><td>10.2.2.9	</td><td>hk-c-aset-2	</td><td>dc =EASTUS rack =rack3	</td><td>아니요 </td></tr>
<tr><td>hk-c7-east-us  </td><td>data	</td><td>10.2.2.10	</td><td>hk-c-aset-2	</td><td>dc =EASTUS rack =rack4	</td><td>예</td></tr>
<tr><td>hk-c8-east-us	</td><td>data	</td><td>10.2.2.11	</td><td>hk-c-aset-2	</td><td>dc =EASTUS rack =rack4	</td><td>아니요 </td></tr>
<tr><td>hk-w1-east-us	</td><td>web	</td><td>10.2.1.4	</td><td>hk-w-aset-1	</td><td>	해당 없음</td><td>해당 없음</td></tr>
<tr><td>hk-w2-east-us	</td><td>web	</td><td>10.2.1.5	</td><td>hk-w-aset-1	</td><td>	해당 없음</td><td>해당 없음</td></tr>
</table>

지역 #1과 동일한 지침을 따르되 10.2.xxx.xxx 주소 공간을 사용합니다. 
###8단계: 각 VM에서 Cassandra 구성###
VM에 로그인하고 다음을 수행합니다. 

1. $CASS_HOME/conf/cassandra-rackdc.properties를 편집하여 데이터 센터 및 랙 속성을 다음 형식으로 지정합니다.
    dc =EASTUS
    rack =rack1
2. cassandra.yaml을 편집하여 시드 노드를 구성합니다. 
    Seeds: "10.1.2.4,10.1.2.6,10.1.2.8,10.1.2.10,10.2.2.4,10.2.2.6,10.2.2.8,10.2.2.10"
###9단계: Cassandra 시작###
각 VM에 로그인하고 다음 명령을 실행하여 Cassandra를 백그라운드에서 시작합니다.
$CASS_HOME/bin/cassandra

##<a id="testtwo"> </a>다중 지역 클러스터 테스트##
지금까지 각 Azure 지역에 8개 노드씩, 16개 노드에 Cassandra가 배포되었습니다. 이러한 노드는 공통 클러스터 이름 및 시드 노드 구성으로 인해 동일한 클러스터에 있습니다. 클러스터를 테스트하려면 다음 프로세스를 사용합니다.
###1단계: PowerShell을 사용하여 두 지역에 대한 내부 부하 분산 장치 IP 가져오기### 
- Get-AzureInternalLoadbalancer -ServiceName "hk-c-svc-west-us"
- Get-AzureInternalLoadbalancer -ServiceName "hk-c-svc-east-us"  

    표시되는 IP 주소를 확인합니다(예: 서부 - 10.1.2.101, 동부 - 10.2.2.101).

###2단계: hk-w1-west-us에 로그인한 후 서부 지역에서 다음 실행###
1.    $CASS_HOME/bin/cqlsh 10.1.2.101 9160을 실행합니다. 
2.	다음 CQL 명령을 실행합니다.

		CREATE KEYSPACE customers_ks
		WITH REPLICATION = { 'class' : 'NetworkToplogyStrategy', 'WESTUS' : 3, 'EASTUS' : 3};
		USE customers_ks;
		CREATE TABLE Customers(customer_id int PRIMARY KEY, firstname text, lastname text);
		INSERT INTO Customers(customer_id, firstname, lastname) VALUES(1, 'John', 'Doe');
		INSERT INTO Customers(customer_id, firstname, lastname) VALUES (2, 'Jane', 'Doe');
		SELECT * FROM Customers;

아래와 같은 표시가 나타나야 합니다.

<table>
<tr><th>customer_id </th><th>firstname</th><th>Lastname</th></tr>
<tr><td>1</td><td>John</td><td>Doe</td></tr>
<tr><td>2</td><td>Jane</td><td>Doe</td></tr>
</table>

###3단계: hk-w1-east-us에 로그인한 후 동부 지역에서 다음 실행:###
1.    $CASS_HOME/bin/cqlsh 10.2.2.101 9160을 실행합니다. 
2.	다음 CQL 명령을 실행합니다.

		USE customers_ks;
		CREATE TABLE Customers(customer_id int PRIMARY KEY, firstname text, lastname text);
		INSERT INTO Customers(customer_id, firstname, lastname) VALUES(1, 'John', 'Doe');
		INSERT INTO Customers(customer_id, firstname, lastname) VALUES (2, 'Jane', 'Doe');
		SELECT * FROM Customers;

서부 지역과 동일한 표시가 나타나야 합니다.

<table>
<tr><th>customer_id </th><th>firstname</th><th>Lastname</th></tr>
<tr><td>1</td><td>John</td><td>Doe</td></tr>
<tr><td>2</td><td>Jane</td><td>Doe</td></tr>
</table>

삽입을 몇 개 더 실행하고 클러스터의 west-us 부분에 복제되는지 확인합니다. 

##<a id="testnode"> </a>Node.js에서 Cassandra 클러스터 테스트##
이전에 "web" 계층에 만든 Linux VM 중 하나에서 간단한 Node.js 스크립트를 실행하여 이전에 삽입한 데이터를 읽습니다. 

**1단계: Node.js 및 Cassandra 클라이언트 설치**

1. Node.js 및 npm을 설치합니다.
2. npm을 사용하여 노드 패키지 "cassandra-client"를 설치합니다.
3. 검색된 데이터의 json 문자열을 표시하는 셸 프롬프트에서 다음 스크립트를 실행합니다. 

		var pooledCon = require('cassandra-client').PooledConnection;
		var ksName = "custsupport_ks";
		var cfName = "customers_cf";
		var hostList = ['internal_loadbalancer_ip:9160'];
		var ksConOptions = { hosts: hostList,
		                     keyspace: ksName, use_bigints: false };
		
		function createKeyspace(callback){
		   var cql = 'CREATE KEYSPACE ' + ksName + ' WITH strategy_class=SimpleStrategy AND strategy_options:replication_factor=1';
		   var sysConOptions = { hosts: hostList,  
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
		  var cql = 'UPDATE customers_cf SET custname=?,custaddress=? where custid=?';
		  var con = new pooledCon(ksConOptions);
		  con.execute(cql,params,function(err) {
		      if (err) console.log(err);
		      else console.log("Inserted customer : " + params[0]);
		  });
		  con.shutdown();
		}
		
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
		
		//exectue the code
		createKeyspace(createColumnFamily);
		readCustomer(ksConOptions)


##<a id="conclusion"> </a>결론##
Microsoft Azure는 이 연습에서 알 수 있듯이 Microsoft 및 오픈 소스 소프트웨어를 둘 다 실행할 수 있게 해주는 유연한 플랫폼입니다. 여러 장애 도메인에 클러스터 노드를 분산하여 단일 데이터 센터에 고가용성 Cassandra 클러스터를 배포할 수 있습니다. 재해 증명 시스템을 위해 지역적으로 떨어진 여러 Azure 지역에 Cassandra 클러스터를 배포할 수도 있습니다. Azure와 Cassandra를 함께 사용하면 오늘날의 인터넷 규모 서비스에 필요한 고확장성, 고가용성 및 재해 복구 가능한 클라우드 서비스를 생성할 수 있습니다.  

[개요]: #overview
[단일 지역 배포]: #oneregion
[단일 지역 Cassandra 클러스터 테스트]: #testone
[다중 지역 배포]: #tworegion
[다중 지역 Cassandra 클러스터 테스트]: #testtwo
[Node.js에서 Cassandra 클러스터 테스트]: #testnode
[결론]: #conclusion

##참조##
- [http://cassandra.apache.org](http://cassandra.apache.org)
- [http://www.datastax.com](http://www.datastax.com) 
- [http://www.nodejs.org](http://www.nodejs.org) 

<!--HONumber=42-->
