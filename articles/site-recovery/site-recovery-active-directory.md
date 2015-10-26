<properties
	pageTitle="Active Directory에 대한 ASR 지침 | Microsoft Azure" 
	description="이 문서는 Azure Site Recovery를 사용하여 AD의 재해 복구 솔루션을 만들고 원클릭 복구 계획, 지원되는 구성, 필수 구성 요소를 사용하여 계획된/계획되지 않은/테스트 장애 조치(failover)를 수행하는 방법을 자세히 설명합니다." 
	services="site-recovery" 
	documentationCenter="" 
	authors="prateek9us" 
	manager="abhiag" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery" 
	ms.date="10/12/2015" 
	ms.author="pratshar"/>

#ASR을 사용하는 Active Directory 및 DNS용 자동 DR 솔루션


SharePoint, Dynamics AX 및 SAP와 같은 모든 엔터프라이즈 응용 프로그램은 올바르게 작동하기 위해 AD 및 DNS 인프라에 따라 달라집니다. 이러한 응용 프로그램에 대한 재해 복구(DR) 솔루션을 만드는 동안 응용 프로그램의 다른 구성 요소에 중단이 발생하기 전에 AD를 보호하고 복구하는 것이 중요합니다.

Azure Site Recovery는 복제, 장애 조치(failover), 가상 컴퓨터를 오케스트레이션하여 재해 복구 기능을 제공하는 Azure 기반 서비스입니다. Azure Site Recovery는 가상 컴퓨터 및 응용 프로그램을 일관적으로 복제, 보호하고 사설/공용 또는 호스팅 서비스 공급자의 클라우드로 원활한 장애 조치(failover)를 수행하기 위해 다양한 복제 기술을 지원합니다.

Azure 사이트 복구를 사용하여 AD에 대한 완전히 자동화된 재해 복구 계획을 만들 수 있습니다. 중단 시 어디에서나 몇 초 이내 장애 조치(failover)를 시작하고 몇 분 이내 AD를 가동할 수 있습니다. 기본 사이트에 SharePoint 및 SAP와 같은 여러 응용 프로그램에 대한 AD가 있고 전체 사이트 장애 조치(failover)가 가능하도록 결정하는 경우 먼저 ASR을 사용하여 AD를 장애 조치(failover)한 다음 응용 프로그램 특정 복구 계획을 사용하여 다른 응용 프로그램을 장애 조치(failover)할 수 있습니다.

이 문서는 AD용 재해 복구 솔루션을 만들고 원클릭 복구 계획, 지원되는 구성 및 필수 구성 요소를 사용하여 계획된/계획되지 않은/테스트 장애 조치(failover)를 수행하는 방법을 자세히 설명합니다. 독자는 AD 및 Azure Site Recovery를 잘 이해하고 있어야 합니다. AD 보호를 위해 고객의 온-프레미스 환경의 복잡성을 기준으로 두 가지 옵션을 제안합니다.

####옵션 1

환경에 적은 응용 프로그램과 단일 도메인 컨트롤러가 있고 전체 사이트에 함께 장애 조치(failover)하려는 경우 ASR 복제를 사용하여 도메인 컨트롤러 컴퓨터를 보조 사이트로 복제하는 것이 좋습니다(사이트-사이트 및 사이트-Azure에 적용 가능). 테스트 장애 조치(failover)에 동일한 복제 가상 머신을 사용할 수 있습니다.

####옵션 2
환경에 많은 응용 프로그램과 둘 이상의 도메인 컨트롤러가 있는 경우 또는 동시에 적은 수의 응용 프로그램을 장애 조치(failover)하려는 경우 도메인 컨트롤러 가상 컴퓨터(테스트 장애 조치(failover)에 사용)에서 ASR 복제를 사용하도록 설정하는 것 이외에 DR 사이트(보조 온-프레미스 사이트 또는 Azure)에 추가 도메인 컨트롤러를 설정하는 것이 좋습니다.

>[AZURE.NOTE]옵션 2를 선택하는 경우에도 테스트 장애 조치(failover)를 수행하려면 도메인 컨트롤러에 대해 ASR 복제를 설정해야 합니다. 자세한 내용은 [테스트 장애 조치(failover) 시 고려 사항](#considerations-for-test-failover) 섹션을 참조하세요.


아래 섹션은 ASR에서 도메인 컨트롤러에 대해 보호를 설정하는 방법과 Azure에서 도메인 컨트롤러를 설정하는 방법에 대해 설명합니다.


##필수 조건

Azure Site Recovery를 사용하여 AD에 재해 복구를 구현하려면 다음의 필수 조건을 완료해야 합니다.

- Active Directory 및 DNS 서버의 온-프레미스 배포
- Microsoft Azure 구독에서 Azure Site Recovery 서비스 자격 증명 모음 생성 
- Azure가 복구 사이트인 경우 VM에서 Azure Virtual Machine Readiness Assessment 도구를 실행하여 Azure VM 및 Azure Site Recovery 서비스와 호환되는지 확인


##ASR을 사용하여 도메인 컨트롤러/DNS에 대한 보호 사용


###VM 보호
ASR에서 도메인 컨트롤러/DNS에 대한 보호를 사용하도록 설정합니다. VM이 Hyper-V 또는 VMware에 배포되었는지 여부를 기준으로 관련 Azure Site Recovery 구성을 수행합니다. 충돌 일치 주기는 15분으로 구성하는 것이 좋습니다.

###VM 네트워크 설정 구성
- 도메인 컨트롤러/DNS 가상 컴퓨터에 대해 장애 조치(failover) 후 VM에 올바른 네트워크에 연결되도록 ASR에서 네트워크 설정을 구성합니다. 
- 예를 들어 DR 사이트로 Azure를 사용하는 경우 'VMM 클라우드' 또는 '보호 그룹'에서 VM을 선택하여 아래 스냅샷과 같이 네트워크 설정을 구성합니다.
- 
![VM 네트워크 설정](./media/site-recovery-active-directory/VM-Network-Settings.png)

##AD 복제를 사용하여 AD에 대한 보호 사용
###사이트-사이트 시나리오

보조(DR) 사이트에 도메인 컨트롤러를 만들고 서버를 도메인 컨트롤러 역할로 승격하면서 기본 사이트에 사용 중인 도메인과 동일한 이름을 지정합니다. Active Directory 사이트 및 서비스 스냅인을 사용하여 사이트가 추가된 사이트 링크 개체에서 설정을 구성할 수 있습니다. 사이트 링크에서 설정을 구성하면 둘 이상의 사이트에서 복제가 실행되는 시기와 빈도를 관리할 수 있습니다. 자세한 내용은 ['사이트 간 복제 일정 예약'](https://technet.microsoft.com/ko-KR/library/cc731862.aspx)을 참조하세요.

###사이트-Azure 시나리오
다음 지침을 따라 [Azure 가상 네트워크에서 도메인 컨트롤러](../virtual-network/virtual-networks-install-replica-active-directory-domain-controller.md)를 만듭니다. 서버를 도메인 컨트롤러 역할로 승격하면서 기본 사이트에서 사용하는 도메인과 동일한 이름을 지정합니다.

그런 다음 [가상 네트워크의 dns 서버가 Azure의 DNS 서버를 사용하도록 재구성](../virtual-network/virtual-networks-install-replica-active-directory-domain-controller.md#reconfigure-dns-server-for-the-virtual-network)해야 합니다.
  
![Azure 네트워크](./media/site-recovery-active-directory/azure-network.png)

##테스트 장애 조치(failover) 시 고려 사항
테스트 장애 조치(failover)는 프로덕션 워크로드에 영향을 미치지 않도록 프로덕션 네트워크에서 격리된 네트워크에서 수행합니다. 대부분의 응용 프로그램은 도메인 컨트롤러와 DNS 서버가 있어야 작동할 수 있습니다. 따라서 응용 프로그램을 장애 조치(failover)하기 전에 테스트 장애 조치(failover)에 사용할 격리된 네트워크에서 도메인 컨트롤러를 만들어야 합니다. 가장 쉬운 방법은 가장 먼저 ASR을 사용하여 도메인 컨트롤러/DNS 가상 컴퓨터에서 보호를 사용하도록 설정하고 응용 프로그램 복구 계획의 테스트 장애 조치(failover)를 트리거하기 전에 도메인 컨트롤러 가상 컴퓨터의 테스트 장애 조치(failover)를 트리거하는 것입니다. 다음은 테스트 장애 조치(failover)를 수행하는 단계별 지침입니다.

1. 다른 가상 컴퓨터와 마찬가지로, 도메인 컨트롤러/DNS 가상 컴퓨터에 보호를 사용하도록 설정합니다.
2. 격리된 네트워크를 만듭니다. Azure에서 생성되는 모든 가상 네트워크는 기본적으로 다른 네트워크에서 격리됩니다. 이 네트워크의 IP 범위를 프로덕션 네트워크와 동일하게 설정하는 것이 좋습니다. 이 네트워크에서 사이트-사이트 연결을 사용하지 마십시오.
3. DNS VM이 가져와야 할 IP로 위 단계에서 만든 네트워크의 DNS IP를 입력합니다. Azure를 DR 사이트로 사용하는 경우 VM 속성의 '대상 IP' 설정에서 장애 조치(failover)로 사용할 VM의 IP를 입력할 수 있습니다. DR 사이트가 온-프레미스이고 DHCP를 사용하는 경우 이 문서의 지침을 따라 [테스트 장애 조치(failover)의 DNS 및 DHCP를 설정](site-recovery-failover.md#prepare-dhcp)합니다. 

>[AZURE.NOTE]테스트 장애 조치(failover)에서 가상 컴퓨터에 제공된 IP는 이 IP가 테스트 장애 조치(failover) 네트워크에서 사용할 수 있다는 점을 감안할 때, 계획되거나 계획되지 않은 장애 조치(failover) 시 얻게 되는 IP와 동일합니다. 동일한 IP를 테스트 장애 조치(failover) 네트워크에서 사용할 수 없는 경우 가상 컴퓨터는 테스트 장애 조치(failover) 네트워크에서 사용 가능한 다른 몇 가지의 IP를 받게 됩니다.

4. 도메인 컨트롤러 가상 컴퓨터로 간 다음 격리된 네트워크에서 해당 가상 컴퓨터의 테스트 장애 조치(failover)를 수행합니다. 
5. 응용 프로그램의 복구 계획에 대해 테스트 장애 조치(failover)를 수행합니다.
6. 테스트가 완료되면 ASR의 작업 탭에서 도메인 컨트롤러 가상 컴퓨터 작업의 테스트 장애 조치(failover) 및 복구 계획을 '완료'로 표시합니다. 

###DNS가 도메인 컨트롤러와 다른 가상 컴퓨터에 있는 경우 
DNS가 도메인 컨트롤러와 다른 가상 컴퓨터에 있는 경우 테스트 장애 조치(failover)에 대해 DNS를 만들어야 합니다. 동일한 VM에 있는 경우는 이 섹션을 건너뛸 수 있습니다. 새 DNS 서버를 사용하고 모든 필요한 영역을 만들 수 있습니다. 예를 들어, Active Directory 도메인이 contoso.com인 경우 이름이 contoso.com인 영역을 만들 수 있습니다. Active Directory에 해당하는 항목을 DNS에서 업데이트해야 합니다. 다음과 같이 수행합니다.

- 복구 계획의 다른 가상 컴퓨터를 생성하기 전에 다음 설정이 준비되었는지 확인합니다.
	- 영역 이름은 포리스트 루트 이름 영역을 따서 지어야 합니다.
	- 영역 파일을 백업해야 합니다.
	- 보안 및 비보안 업데이트에 대한 영역을 활성화해야 합니다.
	- 도메인 컨트롤러 가상 컴퓨터의 확인자는 DNS 가상 컴퓨터의 IP 주소를 가리켜야 합니다.
- 도메인 컨트롤러 가상 컴퓨터 디렉터리에서 nltest /dsregdns 명령을 실행합니다.

- **영역 추가** - 다음의 샘플 스크립트를 사용하여 DNS 서버에서 영역을 추가하고 비보안 업데이트를 허용하고 DNS에 자체에 대한 항목을 추가합니다.

	    dnscmd /zoneadd contoso.com  /Primary 
	    dnscmd /recordadd contoso.com  contoso.com. SOA %computername%.contoso.com. hostmaster. 1 15 10 1 1 
	    dnscmd /recordadd contoso.com %computername%  A <IP_OF_DNS_VM> 
	    dnscmd /config contoso.com /allowupdate 1


##요약
Azure 사이트 복구를 사용하여 AD에 대한 완전히 자동화된 재해 복구 계획을 만들 수 있습니다. 중단 시 어디에서나 몇 초 이내 장애 조치(failover)를 시작하고 몇 분 이내 AD를 가동할 수 있습니다. 기본 사이트에 SharePoint 및 SAP와 같은 여러 응용 프로그램에 대한 AD가 있고 전체 사이트 장애 조치(failover)가 가능하도록 결정하는 경우 우선 ASR을 사용하여 AD를 장애 조치(failover)한 다음 응용 프로그램별 복구 계획을 사용하여 다른 응용 프로그램을 장애 조치(failover)할 수 있습니다.


ASR을 사용하여 다양한 워크로드를 보호하는 방법에 대한 자세한 내용은 [사이트 복구 워크로드 지침](../site-recovery/site-recovery-workload.md)을 참조하세요.

<!---HONumber=Oct15_HO3-->