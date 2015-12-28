<properties
	pageTitle="Azure Site Recovery로 Active Directory 및 DNS 보호 | Microsoft Azure" 
	description="이 문서에서는 Azure Site Recovery를 사용하여 Active Directory에 대한 재해 복구 솔루션을 구현하는 방법에 대해 설명합니다." 
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
	ms.date="12/14/2015" 
	ms.author="pratshar"/>

# Azure Site Recovery로 Active Directory 및 DNS 보호

SharePoint, Dynamics AX 및 SAP와 같은 엔터프라이즈 응용 프로그램은 올바르게 작동하기 위해 Active Directory 및 DNS 인프라에 따라 달라집니다. 응용 프로그램에 대한 재해 복구 솔루션을 만들 때 다른 응용 프로그램 구성 요소 전에 Active Directory 및 DNS를 보호 및 복구하여 재해 발생 시 제대로 작동하도록 해야 합니다.

사이트 복구는 복제, 장애 조치(failover) 및 가상 컴퓨터의 복구를 오케스트레이션하여 재해 복구 기능을 제공하는 Azure 서비스입니다. 사이트 복구는 가상 컴퓨터 및 응용 프로그램을 일관적으로 보호하고 사설, 공용 또는 호스팅 서비스 공급자의 클라우드로 원활한 장애 조치(failover)를 수행하기 위해 다양한 복제 시나리오를 지원합니다.

사이트 복구를 사용하여 Active Directory에 대한 완전히 자동화된 재해 복구 계획을 만들 수 있습니다. 중단 발생 시, 어디에서나 몇 초 이내 장애 조치(failover)를 시작하고 몇 분 이내 Active Directory를 가동 및 실행할 수 있습니다. 기본 사이트에서 SharePoint 및 SAP와 같은 여러 응용 프로그램에 Active Directory를 배포하고 전체 사이트를 장애 조치하려면 먼저 복구 계획을 사용하는 Active Directory를 장애 조치한 후 응용 프로그램 특정 복구 계획을 사용하는 다른 응용 프로그램을 장애 조치할 수 있습니다.

이 문서는 Active Directory용 재해 복구 솔루션을 만들고 원클릭 복구 계획, 지원되는 구성 및 필수 구성 요소를 사용하여 계획된, 계획되지 않은, 테스트 장애 조치(failover)를 수행하는 방법을 자세히 설명합니다. 시작하기 전에 Active Directory와 Azure Site Recovery에 대해 잘 알고 있어야 합니다.

환경의 복잡도에 따라 두 가지 권장되는 옵션이 있습니다.

### 옵션 1

소량의 응용 프로그램 및 단일 도메인 컨트롤러가 있고 전체 사이트를 장애 조치하려는 경우, 사이트 복구를 사용하여 도메인 컨트롤러를 보조 사이트(Azure 또는 보조 사이트로 장애 조치하는지 여부에 관계없이)로 복제하는 것이 좋습니다. 테스트 장애 조치(failover)에 대해서도 동일한 복제 가상 컴퓨터를 사용할 수 있습니다.

### 옵션 2

환경에 많은 응용 프로그램과 둘 이상의 도메인 컨트롤러가 있는 경우 또는 동시에 적은 수의 응용 프로그램을 장애 조치(failover)하려는 경우 사이트 복구로 도메인 컨트롤러 가상 컴퓨터를 복제하는 것 이외에 대상 사이트(Azure 또는 보조 온-프레미스 데이터 센터)에 추가 도메인 컨트롤러를 설정하는 것이 좋습니다.

>[AZURE.NOTE]옵션-2를 구현하더라도 테스트 장애 조치(Failover)를 수행하기 위해서는 사이트 복구를 사용하여 계속 도메인 컨트롤러를 복제해야 합니다. 자세한 내용은 [테스트 장애 조치 시 고려 사항](#considerations-for-test-failover)을 확인하세요.


다음 섹션은 사이트 복구에서 도메인 컨트롤러에 대해 보호를 설정하는 방법과 Azure에서 도메인 컨트롤러를 설정하는 방법에 대해 설명합니다.


## 필수 조건

- Active Directory 및 DNS 서버의 온-프레미스 배포
- Microsoft Azure 구독에서 Azure Site Recovery 서비스 자격 증명 모음 
- Azure에 복제 중인 경우 VM에서 Azure Virtual Machine Readiness Assessment 도구를 실행하여 Azure VM 및 Azure Site Recovery 서비스와 호환되는지 확인


## 사이트 복구를 사용하여 보호 사용


### 가상 컴퓨터 보호

사이트 복구에서 도메인 컨트롤러/DNS 가상 컴퓨터의 보호를 설정합니다. 가상 컴퓨터 유형(Hyper-V 또는 VMware)에 따라 사이트 복구 설정을 구성합니다. 15분의 크래시 일관성 복제 빈도를 사용하는 것이 좋습니다.

###가상 컴퓨터 네트워크 설정 구성

도메인 컨트롤러/DNS 가상 컴퓨터에 대해 장애 조치(failover) 후 VM에 올바른 네트워크에 연결되도록 사이트 복구에서 네트워크 설정을 구성합니다. 예를 들어 Hyper-V VM을 Azure로 복제하는 경우 VMM 클라우드 또는 보호 그룹에서 VM을 선택하여 아래와 같이 네트워크 설정을 구성합니다.

![VM 네트워크 설정](./media/site-recovery-active-directory/VM-Network-Settings.png)

## Active Directory 복제로 Active Directory 보호 

### 사이트-사이트 보호

보조 사이트에 도메인 컨트롤러를 만들고 서버를 도메인 컨트롤러 역할로 승격할 때 기본 사이트에 사용 중인 도메인과 동일한 이름을 지정합니다. **Active Directory 사이트 및 서비스** 스냅인을 사용하여 사이트가 추가된 사이트 링크 개체에서 설정을 구성할 수 있습니다. 사이트 링크에서 설정을 구성하면 둘 이상의 사이트에서 복제가 실행되는 시기와 빈도를 관리할 수 있습니다. 자세한 내용은 [사이트 간 복제 일정 예약](https://technet.microsoft.com/library/cc731862.aspx)을 참조하세요.

###사이트-Azure 보호

다음 지침을 따라 [Azure 가상 네트워크에서 도메인 컨트롤러](../virtual-network/virtual-networks-install-replica-active-directory-domain-controller.md)를 만듭니다. 서버를 도메인 컨트롤러 역할로 승격할 때 기본 사이트에 사용된 도메인과 동일한 이름을 지정합니다.

그런 다음 Azure에서 DNS 서버를 사용하도록 [가상 네트워크에 대한 DNS 서버를 재구성](../virtual-network/virtual-networks-install-replica-active-directory-domain-controller.md#reconfigure-dns-server-for-the-virtual-network)합니다.
  
![Azure 네트워크](./media/site-recovery-active-directory/azure-network.png)

## 테스트 장애 조치 시 고려 사항

테스트 장애 조치(failover)는 프로덕션 워크로드에 영향을 미치지 않도록 프로덕션 네트워크에서 격리된 네트워크에서 발생합니다.

또한 대부분의 응용 프로그램은 작동하는 도메인 컨트롤러 및 DNS가 있어야 작동하므로 응용 프로그램을 장애 조치하기 전에 테스트 장애 조치에 사용할 격리된 네트워크에 도메인 컨트롤러를 만들어야 합니다. 이 작업을 수행하는 가장 쉬운 방법은 사이트 복구를 사용하여 도메인 컨트롤러/DNS 가상 컴퓨터에서 보호를 사용하도록 설정하고 응용 프로그램 복구 계획의 테스트 장애 조치(failover)를 실행하기 전에 가상 컴퓨터의 테스트 장애 조치를 실행하는 것입니다. 그 방법은 다음과 같습니다.

1. 사이트 복구에서 도메인 컨트롤러/DNS 가상 컴퓨터에 대한 보호를 설정합니다.
2. 격리된 네트워크를 만듭니다. Azure에서 생성되는 모든 가상 네트워크는 기본적으로 다른 네트워크에서 격리됩니다. 이 네트워크의 IP 범위를 프로덕션 네트워크와 동일하게 설정하는 것이 좋습니다. 이 네트워크에서 사이트-사이트 연결을 사용하지 마십시오.
3. DNS 가상 컴퓨터를 가져올 것으로 예상되는 IP 주소로 만든 네트워크에서 DNS IP 주소를 제공합니다. Azure로 복제 중인 경우 VM 속성의 **대상 IP** 설정에서 장애 조치(failover)로 사용할 VM의 IP 주소를 제공합니다. 다른 온-프레미스에 복제 중이고 DHCP를 사용하는 경우 지침을 따라 [테스트 장애 조치(failover)의 DNS 및 DHCP를 설정](site-recovery-failover.md#prepare-dhcp)합니다. 

>[AZURE.NOTE]테스트 장애 조치(failover) 중에 가상 컴퓨터에 할당된 IP 주소는 이 IP 주소가 테스트 장애 조치(failover) 네트워크에서 사용할 수 있는 경우, 계획되거나 계획되지 않은 장애 조치(failover) 시 얻게 되는 IP 주소와 동일합니다. 그렇지 않다면 가상 컴퓨터는 테스트 장애 조치 네트워크에서 사용할 수 있는 다른 IP 주소를 수신합니다.

4. 도메인 컨트롤러 가상 컴퓨터에서 격리된 네트워크에서 해당 가상 컴퓨터의 테스트 장애 조치(failover)를 실행합니다. 
5. 응용 프로그램 복구 계획용 테스트 장애 조치(Failover)를 실행합니다.
6. 테스트가 완료되면 사이트 복구 포털의 **작업** 탭에서 도메인 컨트롤러 가상 컴퓨터 작업의 테스트 장애 조치(failover) 및 복구 계획을 '완료'로 표시합니다. 

### 다른 컴퓨터에서 DNS 및 도메인 컨트롤러
 
DNS가 도메인 컨트롤러와 같은 가상 컴퓨터에 없는 경우 테스트 장애 조치(failover)를 위한 DNS VM를 만들어야 합니다. 동일한 VM에 있는 경우는 이 섹션을 건너뛸 수 있습니다.

새 DNS 서버를 사용하고 모든 필요한 영역을 만들 수 있습니다. 예를 들어, Active Directory 도메인이 contoso.com인 경우 이름이 contoso.com인 DNS 영역을 만들 수 있습니다. 다음과 같이 Active Directory에 해당하는 항목을 DNS에서 업데이트해야 합니다.

1. 복구 계획의 다른 가상 컴퓨터를 생성하기 전에 이러한 설정이 준비되었는지 확인합니다.

	- 영역 이름은 포리스트 루트 이름 영역을 따서 지어야 합니다.
	- 영역 파일을 백업해야 합니다.
	- 보안 및 비보안 업데이트에 대한 영역을 활성화해야 합니다.
	- 도메인 컨트롤러 가상 컴퓨터의 확인자는 DNS 가상 컴퓨터의 IP 주소를 가리켜야 합니다.

2. 도메인 컨트롤러 가상 컴퓨터 디렉터리에서 다음 명령을 실행합니다.

	`nltest /dsregdns`

3. DNS 서버에서 영역을 추가하고 비보안 업데이트를 허용하고 DNS에 이에 대한 항목을 추가합니다.

	    dnscmd /zoneadd contoso.com  /Primary 
	    dnscmd /recordadd contoso.com  contoso.com. SOA %computername%.contoso.com. hostmaster. 1 15 10 1 1 
	    dnscmd /recordadd contoso.com %computername%  A <IP_OF_DNS_VM> 
	    dnscmd /config contoso.com /allowupdate 1


## 다음 단계

Azure Site Recovery로 엔터프라이즈 워크로드를 보호하는 방법에 대해 자세히 알아보려면 [어떤 워크로드를 보호할 수 있습니까?](../site-recovery/site-recovery-workload.md)를 읽어보세요.

<!---HONumber=AcomDC_1217_2015-->