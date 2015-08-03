<properties 
	pageTitle="Oracle 가상 컴퓨터 이미지에 대한 기타 고려 사항" 
	description="Microsoft Azure 내에서 Oracle 가상 컴퓨터를 배포하기 전 추가 고려 사항에 대해 알아봅니다." 
	services="virtual-machines" 
	authors="bbenz" 
	documentationCenter=""/>

<tags 
	ms.service="virtual-machines" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="infrastructure-services" 
	ms.date="06/22/2015" 
	ms.author="bbenz" />

#Oracle 가상 컴퓨터 이미지에 대한 기타 고려 사항
이 문서는 운영 체제로 Windows Server와 함께 Microsoft에 의해 제공되는 Azure에서의 Oracle 가상 컴퓨터에 대한 고려 상황을 다룹니다.

-  Oracle 데이터베이스 가상컴퓨터 이미지
-  Oracle Weblogic Server 가상 컴퓨터 이미지
-  Oracle JDK 가상 컴퓨터 이미지

##Oracle 데이터베이스 가상 컴퓨터 이미지
### 클러스터링(RAC)은 지원되지 않습니다.

현재 Azure는 Oracle 데이터베이스 클러스터링을 지원하지 않습니다. 독립 실행형 Oracle 데이터베이스 인스턴스만 가능합니다. 그 이유는 Azure가 여러 VM 인스턴스 간의 읽기/쓰기 방식에서 현재 가상 디스크 공유를 지원하지 않기 때문입니다. UDP 멀티캐스트도 지원되지 않습니다.

### 고정 내부 IP 주소 없음

Azure는 각 가상 컴퓨터에 내부 IP 주소를 할당합니다. VM이 가상 네트워크의 일부가 아닌경우에, VM의 IP 주소는 동적이며 VM이 다시 시작된 후 변경될 수 있습니다. Oracle 데이터베이스는 정적 IP 주소가 필요하기 때문에 문제가 발생할 수 있습니다. 이 문제를 방지하려면, Azure 가상 네트워크에 VM을 추가하는 것이 좋습니다. 자세한 내용은 [가상 네트워크](http://azure.microsoft.com/documentation/services/virtual-network/) 및 [Azure에서 가상 네트워크 만들기](create-virtual-network.md)를 참조하십시오.

### 연결된 디스크 구성 옵션

VM의 운영 체제(OS) 디스크 또는 데이터 디스크로 알려진 연결된 디스크에 데이터 파일을 배치할 수 있습니다. 연결된 디스크는 OS 디스크 보다 더 나은 성능과 유연성을 제공할 수 있습니다. OS 디스크는 10GB 이하의 데이터베이스에 대해서만 사용하는 것이 좋습니다.

연결된 디스크는 Azure Blob 저장소 서비스에 의존합니다. 각 디스크는 이론적으로 최대 초당 500 입력/출력 작업(IOPS)을 할 수 있습니다. 연결된 디스크의 성능은 초기에 최적화되지 않을 수 있으며, IOPS 성능은 약 60-90분의 연속적인 “굽기" 기간 후 크게 향상될 수 있습니다. 디스크가 이후에 유휴 상태로 남아 있으면, IOPS 성능은 다른 굽기 연속 작업 때까지 저하될 수 있습니다. 간단히 말해, 더 많은 디스크 활동은, 최적화된 IOPS 성능에 접근할 가능성이 높습니다.

VM에 단일 디스크를 연결하고 데이터 파일을 해당 디스크에 저장하는 가장 단순한 접근법이지만 이 접근법은 성능에 있어서도 가장 제한을 받습니다. 대신, 여러개의 연결된 디스크를 사용하여 데이터베이스를 분산시킨 후, Oracle 자동 저장소 관리(ASM)를 사용하면 효과적으로 IOPS 성능 향상을 개선할 수 있습니다. 자세한 내용은 [Oracle 자동 저장소 개요](http://www.oracle.com/technetwork/database/index-100339.html)를 참조하십시오. 여러 디스크의 운영 체제 수준 스트라이프를 사용할 수도 있지만, 그러한 접근법은 IOPS 성능 향상에 있어 알려진 바가 없기 때문에 권장되지 않습니다.

읽기 작업의 성능 또는 데이터베이스에 대한 쓰기 작업에 우선 순위를 지정하려는 여부에 따라 여러 디스크를 연결하기 위한 두 가지 방법을 고려해야 합니다.

- **자체적으로 Oracle ASM**은 쓰기 작업 성능이 향상될 가능성이 높지만, Windows Server 2012 저장소 풀을 사용하는 방법과 대비해 볼 때, 읽기 작업에 대한 IOPS는 더 낮습니다. 다음 그림은 이 정렬을 논리적으로 보여줍니다.![](media/virtual-machines-miscellaneous-considerations-oracle-virtual-machine-images/image2.png)

- **Windows Server 2012 저장소 풀과 Oracle ASM**은 데이터베이스가 주로 읽기 작업을 수행하거나, 사용자가 쓰기 작업에 비해 읽기 작업 성능에 가치를 두는 경우, 읽기 작업에서 더 나은 IOPS 성능을 나타낼 가능성이 높습니다. Windows Server 2012 운영 체제에 기초한 이미지가 요구됩니다. 저장소 풀에 대한 자세한 내용은 [독립 실행형 서버에서 저장소 공간 배포](http://technet.microsoft.com/library/jj822938.aspx)를 참조하십시오. 이 정렬에서, 연결된 디스크의 두개의 하위 집합 중 처음 것은, 두 개의 저장소 풀 볼륨에 물리적 디스크로 “스트라이프" 처리된 것이며, 두 번째 것은 ASM 디스크 그룹에 추가된 볼륨입니다. 다음 그림은 이 정렬을 논리적으로 보여줍니다.

	![](media/virtual-machines-miscellaneous-considerations-oracle-virtual-machine-images/image3.png)

>[AZURE.IMPORTANT]상황별로 쓰기 성능과 읽기 성능간의 트레이드-오프를 평가합니다. 이러한 접근법을 사용할 때 실제 결과는 달라질 수 있습니다.

### 높은 가용성 및 재해 복구 고려 사항

Azure 가상 컴퓨터에서 Oracle 데이터베이스를 사용할 때, 사용자는 모든 가동 중지 시간을 방지하기 위해 높은 가용성과 재해 복구 솔루션을 구현하는 일을 담당합니다. 또한 사용자 고유의 데이터와 응용 프로그램 백업을 담당해야 합니다.

Azure에서 Oracle Database Enterprise Edition에 대한 높은 가용성 및 재해 복구는 두 대의 가상 컴퓨터(VM)에서 [데이터 가드, 활성 데이터 가드](http://www.oracle.com/technetwork/articles/oem/dataguardoverview-083155.html), 또는 [Oracle 골든 게이트](http://www.oracle.com/technetwork/middleware/goldengate)를 사용하여 수행할 수 있습니다. 두 대의 가상 컴퓨터는영구적인 개인 IP 주소를 통해 서로 액세스 할 수 있도록 [클라우드 서비스](cloud-services-connect-virtual-machine.md) 및 동일한 [가상 네트워크](http://azure.microsoft.com/documentation/services/virtual-network/)에 위치해야 합니다. 또한, Azure이 개별 장애 도메인 및 업그레이드 도메인에 VM을 배치할 수 있도록 동일한 [가용성 집합](manage-availability-virtual-machines.md)에 VM을 배치하는 것이 좋습니다. 참고 동일한 클라우드 서비스에서 오직 VM만 동일한 가용성 집합에 참여할 수 있습니다. 각 VM은 최소 2GB의 메모리 및 5GB의 디스크 공간이 있어야 합니다.

Oracle 데이터 가드로, 가상 컴퓨터에서 주 데이터베이스, 또 다른 가상 컴퓨터에서 보조(대기) 데이터 베이스에서 고가용성을 얻을 수 있으며, 양 데이터베이스 간에 단방향 복제를 설정할 수 있습니다. 결과는 해당 데이터 베이스의 복사본에 대한 읽기 엑세스입니다. Oracle 골든 게이트로, 두 개의 데이터베이스 간의 양방향 복제를 구성할 수 있습니다. 이러한 도구를 사용하여 사용자의 데이터 베이스에 대한 고가용성 솔루션을 설정하는 방법을 알아보려면 Oracle 웹사이트에서 [활성 데이터 가드](http://www.oracle.com/technetwork/database/features/availability/data-guard-documentation-152848.html) 및 [골든 게이트](http://docs.oracle.com/goldengate/1212/gg-winux/index.html)문서 를 참조하십시오. 데이터 베이스의 복사본에 대한 읽기-쓰기 액세스 권한이 필요한 경우, [Oracle 활성 데이터 가드](http://www.oracle.com/uk/products/database/options/active-data-guard/overview/index.html)를 사용할 수 있습니다.

##Oracle Weblogic Server 가상 컴퓨터 이미지

-  **클러스터링은 Enterprise Edition에서만 지원됩니다.** WeblLogic 서버(특별히, 운영 체제로 Windows Server를 사용하는)의 Microsoft 라이선스 이미지를 사용하는 경우, WebLogic Server의 Enterprise Edition을 사용할 때만 WebLogic 클러스터링을 사용할 수 잇는 라이선스가 부여됩니다. WebLogic Server Standard Edition으로 클러스터링을 사용하지 마십시오.

-  **연결 시간 제한:** 사용자의 응용 프로그램이 다른 Azure 클라우드 서비스(예: 데이터베이스 계층 서비스)의 공용 끝접에 대한 연결에 의존하는 경우, Azure는 비활성 4분후에 이러한 열려있는 연결을 닫을 수 있습니다. 이는 연결이 제한 이상으로 비활성되어 더이상 유효하지 않을 수 있기 때문에 연결 풀에 의존하는 기능 및 응용 프로그램에 영향을 줄 수 있습니다. 사용자의 응용 프로그램에 영향을 미치는 경우 연결 풀에 대한 “유지" 로직을 사용하도록 설정하는 것이 좋습니다.

	끝점이 사용자의 Azure 클라우드 서비스 배포(예컨대 WebLogic 가상 컴퓨터와 같은 *동일한* 클라우드 서비스 내에서 독립 실행형 데이터베이스 가상 컴퓨터)에 대해 *내부*인 경우, 해당 연결은 직접적이며 Azure 부하 분산 장치에 의존하지 않으므로, 연결 제한 시간이 적용되지 않습니다.

-  **UDP 멀티 캐스트는 지원되지 않습니다.** Azure는 UDP 유니캐스트를 지원하지만 멀티 캐스팅 및 브로드캐스팅은 지원하지 않습니다. WebLogic 서버는 Azure의 UDP 유니캐스 기능에 의존할 수 있습니다. UDP 유니캐스트에 의존하는 최상의 결과를 위해, WebLogic 클러스터 크기를 정적으로 유지하거나 클러스터에 포함된 서버는 10개 이하로 유지하는 것이 좋습니다.

-  **WebLogic Server는 T3 액세스(예: Enterprise JavaBeans를 사용할 때)에 대해 공용 및 개인 포트가 동일한 것으로 예상합니다.** **SLWLS**로 명명된 클라우드 서비스에서 두 대 또는 그 이상으로 구성된 WebLogic Server 클러스터 상에서 실행되고 있는 서비스 계층(EJB)에서 다중 계층 시나리오를 고려해 보세요. 다른 클라우드 서비스에 있는 클라이언트 계층은 서비스 계층에서 EJB 호출을 시도하는 간단한 Java 프로그램을 실행합니다. 서비스 계층을 부하 분산하는 작업이 필요하므로, 부하 분산된 공용 끝점은 WebLogic Server 클러스터에 있는 가상 컴퓨터에 대해 생성될 필요가 있습니다. 끝점에 대해 지정한 개인 포트가 공용 포트와 다른 경우(예: 7006:7008), 다음과 같은 오류가 발생합니다.

		[java] javax.naming.CommunicationException [Root exception is java.net.ConnectException: t3://example.cloudapp.net:7006:

		Bootstrap to: example.cloudapp.net/138.91.142.178:7006' over: 't3' got an error or timed out]

	이는 모든 원격 T3 액세스에 대해, WebLogic Server가 부하 분산 장치 포트 및 WebLogic 관리 서버가 동일한 것으로 간주하기 때문입니다. 위와 같은 경우, 클라이언트는 포트 7006에 액세스하고 관리 서버는 7008(개인포트)에서 수신합니다. 이 제한은 T3 액세스에만 적용가능하며 HTTP에는 적용할 수 없는 것에 유의해 주세요.

	이 문제를 방지하려면, 다음 해결 방법중 하나를 사용합니다.

	-  T3 액세스 전용 부하 분산 끝점에 대해 동일한 개인 및 공용 포트 번호를 사용합니다.

	-  WebLogic Server를 시작하는 경우 다음 JVM 매개변수를 포함합니다.

			-Dweblogic.rjvm.enableprotocolswitch=true

관련된 정보는, KB 자료 문서 <http://support.oracle.com>.의 **860340.1**를 참조하십시오.

-  **동적 클러스터링 및 부하 분산 제한** WebLogic 서버에서 동적 클러스터를 사용하고 Azure에서 단일, 공용 부하 분산 끝점을 통해 노출한다고 가정해 보세요. 이 작업은 각 관리 서버(범위에서 동적으로 할당되지 않은)에 대해 고정된 포트 번호를 사용하고 관리자가 추적하는 서버(가상 컴퓨터 하나당 하나 이상의 관리 서버를 둘 수 없습니다)보다 더 많은 관리 서버를 시작하지 않는 한 수행될 수 있습니다. 사용자의 구성이 VM 개수 보다 많은 WebLogic 서버가 시작되고 있는 결과를 낳게 되면(즉, 여러 개의 WebLogic server 인스턴스가 동일한 VM을 공유), 주어진 포트 번호에 바인딩하는 인스턴스 서버는 WebLogic 서버 중 하나 이상이 될 수 없으므로, 해당 VM에서 나머지는 실패합니다. 

	반면에, 관리자 서버를 구성하여 자체 관리 서버에 고유한 포트 번호를 자동으로 설정하면, Azure가 단일 공용 포트에서 여러개의 개인 포트에 이르는 맵핑을 지원하지 않으므로, 작업 분산이 가능하지 않으며 이 구성이 필요할 수 있습니다.

-  **단일 VM에서 다중 WebLogic 인스턴스** 배포 요구사항에 따라, VM이 충분히 큰 경우, 동일한 가상 컴퓨터 내에서 WebLogic Server의 다중 인스턴스를 실행하는 옵션을 고려해볼 수 있습니다. 예를 들면, 2개의 코어를 갖고 있는 중간 크기의 가상 컴퓨터에서, WebLogic Server의 2개의 인스턴스 실행을 선택할 수 있습니다. 주의: 그러나 하나가 망가지면 전체가 망가지는 오류를 막는 것이 여전히 권장됩니다. 이러한 사례로서, 단지 하나의 VM을 사용하면서 WebLogic Server의 여러 인스턴스를 실행하고 있는 경우를 들 수 있습니다. 두 개의 VM을 사용하는 것이 더 나은 접근법이며, 각 VM은 여러 WebLogic 서버 인스턴스를 실행할 수 있습니다. 각 WebLogic Sever 인스턴스는 여전히 동일한 클러스터의 일부입니다. 주의: 그러나 Azure 부하 분산 장치가 고유한 VM들 간에 부하가 분산된 서버를 요구하므로, 동일한 VM 내의 WebLogic 서버 배포로 노출되는 부하 분산 끝점을 로드하기 위해 Azure를 사용하는 것은 현재 불가능합니다.

##Oracle JDK 가상 컴퓨터 이미지

-  **JDK 6 및7 최신 업데이트** Java의 최신 공용 지원 버전(현재 Java 8) 사용이 권장되지만, Azure 또한 JDK 6 및 7 이미지를 사용할 수 있게 합니다. 이는 JDK 8으로 아직 업그레이드 될 준비가 되지 않은 레거시 응용 프로그램을 위한 것입니다. 이전 JDK에 대한 업데이트가 일반 대중에게 더 이상 제공되지 않지만, Oracle과 Microsoft 사이의 파트너 관계로 인하여 Azure에 의해 제공되는 JDK 6 및 7 이미지들은 Oracle이 지원하는 고객중 오직 선택 그룹의 고객들을 위한 가장 최신의 비-공용 업데이트를 포함합니다. JDK 이미지의 최신 버전은 JDK 6 및 7의 업데이트된 릴리스 시간이 지난 후 제공될 것입니다.

	이 JDK 6 및 7 이미지들 내에서 JDK를 사용할 수 있으며, 파생된 가상 컴퓨터 및 이미지는 오직 Azure 내에서만 사용할 수 있습니다.

-  **64비트 JDK.** Azure가 제공하는 Oracle WebLogic Sever 가상 컴퓨터 이미지 및 Oracle JDK 가상 컴퓨터 이미지는 64비트 버전 Windows Server와 JDK 모두를 포함합니다.

##추가 리소스
[Azure용 Oracle 가상 컴퓨터 이미지 ](virtual-machines-oracle-list-oracle-virtual-machine-images.md)

<!---HONumber=July15_HO4-->