<properties
	pageTitle="Site Recovery용 Hyper-V Capacity Planner 도구 실행 | Microsoft Azure"
	description="이 문서에는 Azure Site Recovery용 Hyper-V Capacity Planner 도구를 사용하는 방법에 대한 설명이 포함되어 있습니다."
	services="site-recovery"
	documentationCenter="na"
	authors="rayne-wiselman"
	manager="jwhit"
	editor="" />
<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery"
	ms.date="07/12/2016"
	ms.author="raynew" />

# Site Recovery용 Hyper-V Capacity Planner 도구 실행

Azure Site Recovery 배포의 일환으로 복제 및 대역폭 요구 사항을 파악해야 합니다. Site Recovery용 Hyper-V Capacity Planner 도구를 사용하면 Hyper-V 가상 컴퓨터 복제를 위한 복제 및 대역폭 요구 사항을 확인할 수 있습니다.


이 문서에서는 Hyper-V Capacity Planner 도구를 실행하는 방법을 설명합니다. 이 도구는 [Site Recovery에 대한 용량 계획](site-recovery-capacity-planner.md)에 설명된 용량 계획 도구 및 정보와 함께 사용해야 합니다.


## 시작하기 전에

기본 사이트의 Hyper-V 서버 또는 클러스터 노드에서 도구를 실행합니다. 도구를 실행하려면 Hyper-V 호스트 서버 항목이 필요합니다.

- 운영 체제: Windows Server® 2012 또는 Windows Server® 2012 R2
- 메모리: 20MB(최소)
- CPU: 5% 오버헤드(최소)
- 디스크 공간: 5MB(최소)

도구를 실행하기 전에 기본 사이트를 준비해야 합니다. 두 온-프레미스 사이트 간에 복제하며 대역폭을 확인하려는 경우 복제본 서버도 준비해야 합니다.


## 1단계: 기본 사이트 준비
1. 기본 사이트에서 복제하려는 모든 Hyper-V 가상 컴퓨터와, 이들이 위치한 Hyper-V 호스트/클러스터의 목록을 작성합니다. 도구는 여러 독립 실행형 호스트에 대해 각각 실행하거나, 단일 클러스터에 대해 실행할 수 있지만 둘 다 함께는 실행할 수 없습니다. 또한 각 운영 체제마다 개별적으로 실행해야 하므로 다음과 같이 Hyper-V 서버를 취합하고 확인해야 합니다.

  - Windows Server® 2012 독립 실행형 서버
  - Windows Server® 2012 클러스터
  - Windows Server® 2012 R2 독립 실행형 서버
  - Windows Server® 2012 R2 클러스터

3. 모든 Hyper-V 호스트 및 클러스터에서 WMI에 대해 원격 액세스를 활성화합니다. 각 서버/클러스터마다 이 명령을 실행하여 방화벽 규칙과 사용자 권한이 설정되었는지 확인합니다.

        netsh firewall set service RemoteAdmin enable

5. 다음과 같이 서버와 클러스터에서 성능 모니터링을 활성화합니다.

  - **고급 보안** 스냅인을 통해Windows 방화벽을 연 다음 **COM+ Network Access (DCOM-IN)** 인바운드 규칙과, **원격 이벤트 로그 관리 그룹**의 모든 규칙을 활성화합니다.

## 2단계: 복제본 서버(온-프레미스 간 복제)를 준비합니다.

Azure에 복제하는 경우 이 작업을 수행할 필요가 없습니다.

대역폭 확인을 위해 더미 VM을 복제할 수 있게 단일 Hyper-V 호스트를 복구 서버로 설정하는 것이 좋습니다. 이 단계는 생략할 수 있지만, 생략할 경우 대역폭을 측정할 수 없습니다.

1. 클러스터 노드를 복제본으로 사용하려면 Hyper-V 복제본 브로커를 구성합니다.

	- **서버 관리자**에서 **장애 조치 클러스터 관리자**를 엽니다.
	- 클러스터에 연결하고 클러스터 이름을 강조 표시한 다음 **작업** > **역할 구성**을 클릭하여 고가용성 마법사를 엽니다.
	- **역할 선택**에서 **Hyper-v 복제본 브로커**를 클릭합니다. 이 마법사에서 클러스터에 대한 연결점으로 사용할 **NetBIOS 이름** 및 **IP 주소**를 입력합니다(클라이언트 액세스 지점이라고 함). **Hyper-V 복제본 브로커**가 구성되며 이에 따라 생성된 클라이언트 액세스 지점 이름을 기억해 두어야 합니다.
	- Hyper-V 복제본 Broker 역할이 성공적으로 온라인 상태가 되었는지, 모든 클러스터 노드 사이에서 장애 조치할 수 있는지 확인합니다. 이렇게 하려면 해당 역할을 마우스 오른쪽 단추로 클릭하고 **이동**을 가리킨 다음 **노드 선택**을 클릭합니다. 노드 > **OK**를 선택합니다.
	- 인증서 기반 인증을 사용하는 경우 각 클러스터 노드와 클라이언트 액세스 지점에 모두 인증서가 설치되었는지 확인합니다.
2.  복제본 서버를 활성화합니다.

	- 클러스터에 대해 오류 클러스터 관리자를 열고 클러스터에 연결한 다음 **역할** > 역할 선택 > **복제 설정** > **이 클러스터를 복제 서버로 사용**을 클릭합니다. 클러스터를 복제본으로 사용할 경우 Hyper-V 복제본 브로커 역할이 기본 사이트의 클러스터에도 있어야 합니다.
	- 독립 실행형 서버의 경우 Hyper-V 관리자를 엽니다. **작업** 창에서 사용하려는 서버에 대한 **Hyper-V 설정**을 클릭하고 **복제 구성**에서 **이 컴퓨터를 복제본 서버로 사용**을 클릭합니다.
3. 인증을 설정합니다.

	- **인증 및 포트**에서 기본 서버의 인증 방법과 인증 포트를 선택합니다. 인증서를 사용할 경우 **인증서 선택**을 클릭하여 하나를 선택합니다. 기본 및 복구 Hyper-V 호스트가 동일한 도메인이나 신뢰할 수 있는 도메인에 있는 경우 Kerberos를 사용합니다. 서로 다른 도메인 또는 작업 그룹 배포의 경우 인증서를 사용합니다.
	- **권한 부여 및 저장소** 섹션에서 **모든** 인증된(기본) 서버가 복제 데이터를 이 복제본 서버로 보낼 수 있게 허용합니다. **OK** 또는 **적용**을 클릭합니다.

	![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image1.png)

	- **netsh http show servicestate**를 실행하여 지정한 프로토콜/포트에 대해 수신기가 실행 중인지 확인합니다.
4. 방화벽을 설정합니다. Hyper-V 설치 중에, 기본 포트(443의 HTTPS, 80의 Kerberos)에서 트래픽을 허용하는 방화벽 규칙이 생성됩니다. 이러한 규칙을 다음과 같이 활성화합니다.

		- Certificate authentication on cluster (443): **Get-ClusterNode | ForEach-Object {Invoke-command -computername \$\_.name -scriptblock {Enable-Netfirewallrule -displayname "Hyper-V Replica HTTPS Listener (TCP-In)"}}**
		- Kerberos authentication on cluster (80): **Get-ClusterNode | ForEach-Object {Invoke-command -computername \$\_.name -scriptblock {Enable-Netfirewallrule -displayname "Hyper-V Replica HTTP Listener (TCP-In)"}}**
		- Certificate authentication on standalone server: **Enable-Netfirewallrule -displayname "Hyper-V Replica HTTPS Listener (TCP-In)"**
		- Kerberos authentication on standalone server: **Enable-Netfirewallrule -displayname "Hyper-V Replica HTTP Listener (TCP-In)"**

## 3단계: 용량 플래너 도구 실행

기본 사이트를 준비하고 복구 서버를 설정한 후 도구를 실행할 수 있습니다.

1. Microsoft 다운로드 센터에서 도구를 [다운로드](https://www.microsoft.com/download/details.aspx?id=39057)합니다.
2. 기본 서버 중 하나로부터 도구를 실행합니다(또는 기본 클러스터로부터 노드 중 하나). .exe 파일을 마우스 오른쪽 단추로 클릭한 다음 **관리자 권한으로 실행**을 선택합니다.
3. **시작하기 전에** 데이터를 수집하려는 기간을 지정합니다. 데이터가 대표성이 있도록 운영 시간 중에 도구를 실행하는 것이 좋습니다. 네트워크 연결의 유효성만 검사하려는 경우 1분 간만 데이터를 수집할 수 있습니다.

	![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image2.png)

4. **기본 사이트 세부 정보**에서 독립 실행형 호스트에 대한 서버 이름이나 FQDN을 지정하거나, 클러스터에 대해 클러슽터의 클라이언트 액세스 포인트 FQDN, 클러스터 이름 또는 모든 노드를 지정하고 **다음**을 클릭합니다. 도구가 자동으로 자신이 실행 중인 서버의 이름을 검색합니다. 도구가 지정한 서버에 대해 모니터링 가능한 VM을 선택합니다.

	![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image3.png)

5. **복제본 사이트 세부 정보**에서, Azure에 복제하거나 보조 데이터센터에 복제하며 복제본 서버를 설정하지 않은 경우 **복제본 사이트 관련 테스트 건너뛰기**를 선택합니다. 보조 데이터센터에 복제하며 독립 실행형 서버나 클러스터의 클라이언트 액세스 포인트의 FQDN을 **서버 이름 또는 Hyper-V 복제본 브로커 CAP**에서 설정한 경우.

	![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image4.png)

6. **확장된 복제본 세부 정보**에서 **확장된 복제본 사이트 관련 테스트 건너뛰기**를 활성화합니다. 이 항목은 사이트 복구에서 지원되지 않습니다.
7. **복제할 VM 선택**에서 **기본 사이트 세부 정보** 페이지에서 사용자가 지정한 설정에 따라 도구가 서버나 클러스터에 연결하고 기본 서버에서 실행 중인 VM과 디스크를 표시합니다. 이미 복제에 대해 활성화되었거나 실행되고 있지 않은 VM은 표시되지 않습니다. 메트릭을 수집할 VM을 선택합니다. VHD를 선택하면 VM에 대한 데이터도 자동으로 수집됩니다.
9. 복제본 서버나 클러스터를 구성한 경우 **네트워크 정보**에서 기본 서버와 복제본 사이트 간에 사용할 적합한 WAN 대역폭을 지정하고, 인증서 인증을 구성한 경우 인증서를 선택합니다.

	![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image5.png)

10. **요약**에서 설정을 확인하고 **다음**을 클릭하여 메트릭 수집을 시작합니다. 도구 진행률 및 상태가 **용량 계산** 페이지에 표시됩니다. 도구가 실행을 마쳤을 때 **보고서 보기**를 클릭하면 출력으로 이동할 수 있습니다. 기본적으로 보고서와 로그는 **%systemdrive%\\Users\\Public\\Documents\\Capacity Planner**에 저장됩니다.

	![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image6.png)


## 4단계: 결과 해석
다음은 중요한 메트릭입니다. 여기에 나열되지 않은 메트릭은 사이트 복구와 관련이 없는 것이므로 무시할 수 있습니다.

### 온-프레미스 간 복제
  - 기본 호스트의 계산, 메모리에서 복제의 영향
  - 기본, 복구 호스트의 저장소 디스크 공간, IOPS에 대한 복제의 영향
  - 델타 복제(Mbps)에 필요한 총 대역폭
  - 기본 호스트와 복구 호스트(Mbps) 사이에 관찰된 네트워크 대역폭
  - 두 호스트/클러스터 사이에서 활성 병렬 전송의 이상적인 개수에 대한 제안

### 온-프레미스와 Azure 간 복제
  - 기본 호스트의 계산, 메모리에서 복제의 영향
  - 기본 호스트의 저장소 디스크 공간, IOPS에 대한 복제의 영향
  - 델타 복제(Mbps)에 필요한 총 대역폭

## 추가 리소스

- 도구에 대한 자세한 정보는 도구 다운로드에 포함된 문서를 확인하세요.
- Keith Mayer의 [TechNet 블로그](http://blogs.technet.com/b/keithmayer/archive/2014/02/27/guided-hands-on-lab-capacity-planner-for-windows-server-2012-hyper-v-replica.aspx)에서 제공하는 도구 안내를 확인하세요.
- 온-프레미스 간 Hyper-V 복제를 위한 성능 테스트 [결과 보기](site-recovery-performance-and-scaling-testing-on-premises-to-on-premises.md)



## 다음 단계

용량 계획을 마친 후에는 Site Recovery 배포를 시작할 수 있습니다.

- [VMM 클라우드의 Hyper-V VM을 Azure로 복제](site-recovery-vmm-to-azure.md)
- [Hyper-V VM(VMM 없음)을 Azure로 복제](site-recovery-hyper-v-site-to-azure.md)
- [VMM 사이트 간에 Hyper-V VM 복제](site-recovery-vmm-to-vmm.md)
- [SAN을 통해 VMM 사이트 간에 Hyper-V VM 복제](site-recovery-vmm-san.md)
- [단일 VMM 서버에서 Hyper-V VM 복제](site-recovery-single-vmm.md)

<!---HONumber=AcomDC_0720_2016-->