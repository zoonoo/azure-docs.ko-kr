<properties 
	pageTitle="온-프레미스 VMWare 가상 컴퓨터 또는 물리적 서버와 Azure 간의 보호 설정" 
	description="Azure Site Recovery는 온-프레미스 VMWare 서버와 Azure 사이 및 물리적 온-프레미스 서버와 Azure 사이에 있는 가상 컴퓨터의 복제, 장애 조치(Failover) 및 복구를 조정합니다." 
	services="site-recovery" 
	documentationCenter="" 
	authors="raynew" 
	manager="jwhit" 
	editor="tysonn"/>

<tags 
	ms.service="site-recovery" 
	ms.workload="backup-recovery" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/26/2015" 
	ms.author="raynew"/>


# 온-프레미스 VMWare 가상 컴퓨터 또는 물리적 서버와 Azure 간의 보호 설정

Azure Site Recovery는 가상 컴퓨터와 물리적 서버의 복제, 장애 조치(Failover) 및 복구를 오케스트레이션하여 BCDR(비즈니스 연속성 및 재해 복구) 전략에 기여합니다. 가능한 배포 시나리오는 [Azure Site Recovery 개요](hyper-v-recovery-manager-overview.md)를 참조하세요.

이 연습에서는 다음과 같은 목적으로 사이트 복구를 배포하는 방법을 설명합니다.

- **온-프레미스 VMWare 가상 컴퓨터와 Azure 간 보호**
- **물리적 온-프레미스 Windows 및 Linux 서버와 Azure 간 보호**

비즈니스 이점은 다음과 같습니다.

- 물리적 Windows 또는 Linux 서버 보호.
- Azure Site Recovery 포털을 사용한 간단한 복제, 장애 조치(Failover) 및 복구.
- 인터넷, 사이트 간 VPN 연결 또는 Azure Express 경로를 통한 데이터 복제.   
- Azure에서 온-프레미스 VMWare 인프라로의 장애 복구(Failback)(복원). 
- VMWare 가상 컴퓨터의 간소화된 검색.
- 특정 작업을 실행하는 가상 컴퓨터와 물리적 서버를 일관된 데이터 지점으로 함께 복구할 수 있도록 하는 다중 VM 일관성.
- 여러 컴퓨터에 계층화된 작업의 간소화된 장애 조치(Failover) 및 복구를 위한 복구 계획.

이 기능은 현재 미리 보기로 제공됩니다. [미리 보기에 대한 보층 사용 약관]을 읽어보세요.(preview-supplemental-terms).

## 이 가이드 정보

이 가이드에는 개요 및 배포 필수 조건이 포함되어 있습니다. 모든 배포 구성 요소를 설정하고 가상 컴퓨터 및 서버에 대해 보호를 사용하도록 설정하는 방법을 안내합니다. 끝으로, 장애 조치(Failover)를 테스트하여 모두 예상대로 작동하는지 확인합니다.

문제가 발생할 경우 [Azure 복구 서비스 포럼](http://go.microsoft.com/fwlink/?LinkId=313628)에 의문 사항을 게시하세요.

## 개요

이 다이어그램은 배포 구성 요소를 보여 줍니다.

![New vault](./media/site-recovery-vmware-to-azure/ASRVMWare_Arch.png)

### 배포 구성 요소

- **온-프레미스 컴퓨터** - 온-프레미스 사이트에 보호할 컴퓨터가 있습니다. VMWare 하이퍼바이저에서 실행되는 가상 컴퓨터나 Windows 또는 Linux를 실행하는 물리적 서버입니다.

- **온-프레미스 프로세스 서버** - 보호된 컴퓨터가 복제 데이터를 온-프레미스 프로세스 서버에 보냅니다. 프로세스 서버는 해당 데이터에 대해 다양한 작업을 수행합니다. Azure의 마스터 대상 서버로 전송하기 전에 데이터를 최적화합니다. 프로세스 서버에는 수신한 복제 데이터를 캐시할 디스크 기반 캐시가 있습니다. 또한 보호할 각 가상 컴퓨터나 물리적 서버에 설치되어야 하는 모바일 서비스의 푸시 설치를 처리하고 VMWare vCenter 서버의 자동 복구를 수행합니다. 프로세스 서버는 Windows Server 2012 R2를 실행하는 가상 서버 또는 물리적 서버입니다. 보호할 컴퓨터와 동일한 네트워크 및 LAN 세그먼트에 배치하는 것이 좋지만 보호된 컴퓨터에서 L3 네트워크를 볼 수 있다면 다른 네트워크에서 실행할 수도 있습니다. 배포 중에 프로세스 서버를 설정하고 구성 서버에 등록합니다.

- **Azure Site Recovery 자격 증명 모음** - 자격 증명 모음은 온-프레미스 사이트와 Azure 간의 데이터 복제본, 장애 조치(Failover) 및 복구를 조정하고 오케스트레이션합니다.

- **Azure 구성 서버** - 구성 서버는 Azure에서 보호된 컴퓨터, 프로세스 서버 및 마스터 대상 서버 간의 통신을 조정합니다. 복제를 설정하고 장애 조치(Failover) 발생 시 Azure에서 복구를 조정합니다. 구성 서버는 Azure 구독에 포함된 Azure 표준 A3 가상 컴퓨터에서 실행됩니다. 배포 중에 서버를 설정하고 Azure Site Recovery 자격 증명 모음에 등록합니다.

- **마스터 대상 서버** - Azure의 마스터 대상 서버는 Azure 저장소 계정으로 Blob 저장소에 만든 연결된 VHD를 사용하여 보호된 컴퓨터의 복제된 데이터를 유지합니다. Azure 가상 컴퓨터로, Windows Server 2012 R2 갤러리 이미지에 따라 Windows 서버로(Windows 컴퓨터 보호) 또는 OpenLogic CentOS 6.6 갤러리 이미지에 따라 Linux 서버로(Linux 컴퓨터 보호) 배포합니다. 표준 A3와 표준 D14의 두 가지 크기 조정 옵션을 사용할 수 있습니다. 서버는 구성 서버와 동일한 Azure 네트워크에 연결됩니다. 배포 중에 서버를 만들고 구성 서버에 등록합니다.

- **모바일 서비스** - 보호할 각 VMWare 가상 컴퓨터나 물리적 Windows/Linux 서버에 모바일 서비스를 설치합니다. 서비스가 복제 데이터를 프로세스 서버로 전송하고, 프로세스 서버는 다시 Azure의 마스터 대상 서버로 전송합니다. 프로세스 서버가 보호된 컴퓨터에 모바일 서비스를 자동으로 설치하거나, 내부 소프트웨어 배포 프로세스를 사용하여 수동으로 서비스를 배포할 수 있습니다.

- **데이터 통신 및 복제 채널** - 두 가지 옵션이 있습니다. 두 옵션에서 모두 보호된 컴퓨터에 인바운드 네트워크 포트를 열 필요가 없습니다. 모든 네트워크 통신은 온-프레미스 사이트에서 시작됩니다.
	- **인터넷 이용** - 보안 공용 인터넷 연결을 통해 보호된 온-프레미스 서버와 Azure의 데이터를 전달 및 복제합니다. 기본 옵션입니다.
	- **VPN/Express 경로** - VPN 연결을 통해 온-프레미스 서버와 Azure 간에 데이터를 전달 및 복제합니다. 사이트 간 VPN을 설정하거나 온-프레미스 사이트와 Azure 네트워크 간에 [Express 경로](expressroute) 연결을 설정해야 합니다. 

 
## 용량 계획

- 성능을 최적화하고 보호된 여러 컴퓨터를 일관된 데이터 지점으로 복구하는 다중 VM 일관성 기능을 이용하려면 가상 컴퓨터를 작업별 보호 그룹으로 수집하는 것이 좋습니다.
- 디스크 복제 시 디스크 크기를 미러링하는 VHD가 Azure Blob 저장소에 만들어지고 데이터 디스크로 마스터 대상 서버에 연결되기 때문에 여러 마스터 대상 서버에서 단일 컴퓨터를 보호할 수 없습니다. 단일 마스터 대상 서버로 여러 컴퓨터를 보호할 수는 있습니다.
- 마스터 대상 서버 가상 컴퓨터는 Azure 표준 A4 또는 D14일 수 있습니다.
	- 표준 A4 디스크를 사용하는 경우 각 가상 컴퓨터에 16개의 데이터 디스크(데이터 디스크당 최대 1023GB)를 추가할 수 있습니다.
	- 표준 D14 디스크를 사용하는 경우 각 가상 컴퓨터에 32개의 데이터 디스크(데이터 디스크당 최대 1023GB)를 추가할 수 있습니다.
- 마스터 대상 서버에 연결된 디스크 중 하나는 보존 드라이브로 예약됩니다. Azure Site Recovery를 통해 보존 기간을 정의하고 해당 기간 내 언제든지 보호된 컴퓨터를 복구할 수 있습니다. 보존 드라이브는 이 기간 동안 디스크 변경 내용의 업무 일지를 유지 관리합니다.  따라서 A4의 최대 디스크는 15개로 감소하고 D14의 경우 31개로 감소합니다. 
- 배포 크기를 조정하려면 프로세스 서버와 마스터 대상 서버를 여러 개 추가합니다. 기존 마스터 대상 서버에서 사용 가능한 디스크가 부족할 경우 두 번째 마스터 대상 서버를 배포해야 합니다. 보호된 컴퓨터의 데이터 변경 속도가 기존 프로세스 서버의 용량을 초과할 경우 추가 프로세스 서버를 배포해야 합니다. 프로세스 서버와 마스터 대상 서버는 일 대 일 매핑이 필요하지 않습니다. 첫 번째 프로세스 서버를 두 번째 마스터 대상 서버와 함께 배포할 수 있습니다.
프로세스 서버는 디스크 기반 캐시를 사용합니다. 캐시에 사용 가능한 공간이 C:/에 충분한지 확인합니다. 캐시 크기 조정은 보호 중인 컴퓨터의 데이터 변경 속도에 영향을 받습니다. 일반적으로 중간 크기 배포의 경우 600GB의 캐시 디렉터리 크기를 사용하는 것이 좋지만 다음 지침을 사용할 수 있습니다.

	![Cache guidelines](./media/site-recovery-vmware-to-azure/ASRVMWare_ProcessServerSize.png)


## 시작하기 전에

### Azure 필수 조건

- [Microsoft Azure](http://azure.microsoft.com/) 계정이 있어야 합니다. [무료 평가판](http://aka.ms/try-azure)으로 시작할 수 있습니다. 
- 복제된 데이터를 저장하려면 Azure 저장소 계정이 있어야 합니다. 계정에 대해 지역에서 복제 기능을 사용하도록 설정해야 합니다. 계정은 Azure Site Recovery 자격 증명 모음과 동일한 지역에 있고 동일한 구독과 연결되어야 합니다. 자세한 내용은 [Microsoft Azure 저장소 소개](http://go.microsoft.com/fwlink/?LinkId=398704)를 참조하세요.
- 구성 서버와 마스터 대상 서버를 배포할 Azure 가상 네트워크가 필요합니다. 이 네트워크는 Azure Site Recovery 자격 증명 모음과 동일한 구독 및 지역에 있어야 합니다.
- 모든 구성 요소를 배포하기에 충분한 Azure 리소스가 있는지 확인합니다. 자세한 내용은 다음을 참조하세요. [Azure 구독 제한](azure-subscription-service-limits).
- 보호할 컴퓨터가 Azure 가상 컴퓨터 요구 사항을 준수하는지 확인합니다. 

	- **디스크 수** - 하나의 보호된 서버에서 최대 31개의 디스크를 지원할 수 있습니다.
	- **디스크 크기** - 개별 디스크 용량은 1023GB 이하여야 합니다.
	- **클러스터링** - 클러스터형 서버는 지원되지 않습니다.
	- **부팅** - UEFI(Unified Extensible Firmware Interface)/EFI(Extensible Firmware Interface) 부팅은 지원되지 않습니다.
	- **볼륨** - Bitlocker 암호화된 볼륨은 지원되지 않습니다.
	- **서버 이름** - 이름에 1자에서 63자(문자, 숫자 및 하이픈)까지 사용할 수 있습니다. 이름은 문자나 숫자로 시작하고 문자나 숫자로 끝나야 합니다. 컴퓨터가 보호된 후 Azure 이름을 수정할 수 있습니다.	

	Azure 요구 사항에 대한 자세한 내용은 [가상 컴퓨터 지원](https://msdn.microsoft.com/library/azure/dn469078.aspx#BKMK_E2A)을 참조하세요.

### 시나리오 구성 요소 필수 조건

- 프로세스 서버:
	- 최신 업데이트를 설치한 Windows Server 2012 R2를 실행하는 물리적 컴퓨터나 가상 컴퓨터에 프로세스 서버를 배포할 수 있습니다. C:/에 설치합니다.
	- 서버에 8개 이상의 프로세스 코어와 64GB RAM이 필요하며 C:에서 300GB의 사용 가능한 공간이 권장됩니다.
	- 보호할 컴퓨터와 동일한 네트워크 및 서브넷에 서버를 배치하는 것이 좋습니다.
	- VMWare vCenter 서버의 자동 검색을 수행할 수 있도록 서버에 VMware vSphere CLI 5.1을 설치합니다.
- 구성 서버, 마스터 대상 서버, 프로세스 서버 및 장애 복구(Failback) 서버의 설치 경로에는 영문자만 사용해야 합니다. 예를 들어 Linux를 실행하는 마스터 대상 서버의 경로는 **/usr/local/ASR**이어야 합니다.

### VMWare 필수 조건

- VMware vSphere 하이퍼바이저를 관리하는 VMWare vCenter 서버. 최신 업데이트를 설치한 vCenter 버전 5.0 이상을 실행해야 합니다. 
- 보호할 VMWare 가상 컴퓨터가 포함된 하나 이상의 vSphere 하이퍼바이저. 하이퍼바이저는 최신 업데이트를 설치한 ESX/ESXi 버전 5.0 이상을 실행해야 합니다.
- vCenter 서버를 통해 검색된 VMWare 가상 컴퓨터에 VMware 도구가 설치되고 실행되어야 합니다.

### 보호된 Windows 컴퓨터 필수 조건

Windows를 실행하는 보호된 물리적 서버 또는 VMWare 가상 컴퓨터에는 다음이 있어야 합니다.

- 지원되는 64비트 운영 체제: Windows Server 2012 R2, Windows Server 2012 또는 SP1 이상을 설치한 Windows Server 2008 R2.
- 호스트 이름, 마운트 지점, 장치 이름, Windows 시스템 경로(예: C:\Windows)에는 영어만 사용해야 합니다.
- 운영 체제는 C:\ 드라이브에 설치해야 합니다.
- Windows 서버에 대한 일반 저장소 옵션이 지원됩니다.
- 보호된 컴퓨터의 방화벽 규칙에서 Azure의 구성 서버와 마스터 대상 서버에 연결할 수 있도록 허용해야 합니다. 
- 장애 조치(Failover) 후에 원격 데스크톱을 사용하여 Azure의 Windows 가상 컴퓨터에 연결하려는 경우 온-프레미스 컴퓨터에 대해 원격 데스크톱이 사용하도록 설정되었는지 확인합니다. VPN을 통해 연결하지 않는 경우 방화벽 규칙에서 인터넷을 통한 원격 데스크톱 연결을 허용해야 합니다.

### 보호된 Linux 컴퓨터 필수 조건

Linux를 실행하는 보호된 물리적 서버 또는 VMWare 가상 컴퓨터에는 다음이 있어야 합니다.

- 지원되는 운영 체제: Centos 6.4, 6.5, 6.6(32비트 또는 64비트), Red Hat 호환 커널 또는 UEK3(Unbreakable Enterprise Kern Release 3)을 실행하는 Oracle Enterprise Linux  6.4, 6.5(32비트 또는 64비트), SUSE Linux Enterprise Server 11 SP3(32비트 또는 64비트)
- 호스트 이름, 마운트 지점, 장치 이름 및 Linux 시스템 경로와 파일 이름(예: /etc/; /usr)에는 영어만 사용해야 합니다.
- 다음 저장소가 있는 온-프레미스 컴퓨터에 대해 보호를 사용하도록 설정할 수 있습니다.
	- 파일 시스템: EXT3, ETX4, ReiserFS, XFS
	- 다중 경로 소프트웨어: 장치 매퍼 - 다중 경로
	- 볼륨 관리자: LVM2
	- HP CCISS 컨트롤러 저장소가 있는 물리적 서버는 지원되지 않습니다.
- 보호된 컴퓨터의 방화벽 규칙에서 Azure의 구성 서버와 마스터 대상 서버에 연결할 수 있도록 허용해야 합니다.
- 장애 조치(Failover) 후에 보안 셸 클라이언트(ssh)를 사용하여 Linux를 실행하는 Azure 가상 컴퓨터에 연결하려는 경우 보호된 컴퓨터의 보안 셸 서비스가 시스템 부팅 시 자동으로 시작되도록 설정되었는지, 그리고 방화벽 규칙에서 ssh 연결을 허용하는지 확인합니다.  

### 타사 필수 조건

이 시나리오의 일부 배포 구성 요소는 제대로 작동하기 위해 타사 소프트웨어를 사용합니다. 전체 목록은 <a href="#thirdparty">타사 소프트웨어 통지 및 정보</a>를 참조하세요. 

## 1단계: 자격 증명 모음 만들기

1. [관리 포털](https://portal.azure.com)에 로그인합니다.


2. **데이터 서비스** > **복구 서비스**를 확장하고 **사이트 복구 자격 증명 모음**을 클릭합니다.


3. **새로 만들기** > **빠른 생성**을 클릭합니다.
	
4. **이름**에 자격 증명 모음을 식별하기 위한 이름을 입력합니다.

5. **지역**에서 자격 증명 모음에 대한 지리적 지역을 선택합니다. 지원되는 지역을 확인하려면 [Azure Site Recovery 가격 정보](href="http://go.microsoft.com/fwlink/?LinkId=389880)에서 지리적 가용성을 참조하세요.

6. **자격 증명 모음 만들기**를 클릭합니다. 

	![New vault](./media/site-recovery-vmware-to-azure/ASRVMWare_CreateVault.png)

상태 표시줄을 점검하여 자격 증명 모음이 성공적으로 만들어졌는지 확인합니다. 자격 증명 모음은 기본 **복구 서비스** 페이지에서 **활성**으로 나열됩니다.

## 2단계: 구성 서버 배포


1. **복구 서비스** 페이지에서 자격 증명 모음을 클릭하여 빠른 시작 페이지를 엽니다. 빠른 시작은 언제든지 아이콘을 사용하여 열 수도 있습니다.

	![Quick Start Icon](./media/site-recovery-vmware-to-azure/ASRVMWare_QuickStartIcon.png)

2. 드롭다운 목록에서 **VMWare/물리적 서버가 있는 온-프레미스 사이트와 Azure 간**을 선택합니다.
3. **대상 리소스 준비**에서 **구성 서버 배포**를 클릭합니다.

	![Deploy configuration server](./media/site-recovery-vmware-to-azure/ASRVMWare_DeployCS.png)

4. 서버에 연결하기 위한 구성 서버 정보 및 자격 증명을 지정합니다. 서버를 배치할 Azure 네트워크를 선택합니다. 서버에 할당할 내부 IP 주소 및 서브넷을 지정합니다. **확인**을 클릭하면 Azure Site Recovery Windows Server 2012 R2 갤러리 이미지를 기반으로 하는 표준 A3 가상 컴퓨터가 구성 서버 구독에 만들어집니다. 새 클라우드 서비스에 예약된 공용 IP 주소를 사용하는 첫 번째 인스턴스로 만들어집니다.

	![Configuration server settings](./media/site-recovery-vmware-to-azure/ASRVMWare_CSDetails.png)

5. **작업** 탭에서 진행률을 모니터링할 수 있습니다.

	![Monitor progress](./media/site-recovery-vmware-to-azure/ASRVMWare_MonitorConfigServer.png)

8.  구성 서버가 배포된 후 Azure 포털의 **가상 컴퓨터** 페이지에서 할당된 공용 IP 주소를 확인하고 적어둡니다. 그런 다음 **끝점** 탭에서 개인 포트 443에 매핑된 공용 HTTPS 포트를 확인하고 적어둡니다. 이 정보는 나중에 마스터 대상 서버와 프로세스 서버를 구성 서버에 등록할 때 필요합니다. 4개의 공용 끝점을 사용하여 구성 서버가 배포됩니다.

	- 443: 구성 요소 서버와 Azure 간의 통신을 조정하는 데 사용되는 HTTPS 채널입니다.
	- 9443: 장애 복구(Failback) 도구 및 장애 복구(Failback) 통신에 사용됩니다.
	- 원격 데스크톱
	- PowerShell


## 3단계: 자격 증명 모음에 구성 서버 등록

1. **대상 리소스 준비**에서 **등록 키 다운로드**를 클릭합니다. 키 파일이 자동으로 생성됩니다. 이 파일은 생성된 날로부터 5일간 유효합니다. 파일을 구성 서버에 복사합니다.
2. 가상 컴퓨터의 **대시보드** 페이지에서 **연결**을 클릭합니다. 다운로드한 RDP 파일을 사용하여 원격 데스크톱을 통해 구성 서버에 로그온합니다.  처음 로그온하는 경우 Azure Site Recovery 설치 및 등록 마법사가 자동으로 실행됩니다.

	![Registration](./media/site-recovery-vmware-to-azure/ASRVMWareRegister1.png)

3. 마법사 지침을 따릅니다. MySQL Server를 다운로드하여 설치하고 자격 증명을 지정해야 합니다. **Azure Site Recovery 등록** 페이지에서 서버에 복사한 키 파일을 찾습니다.

	![Registration key](./media/site-recovery-vmware-to-azure/ASRVMWareRegister2.png)

4. 등록이 완료되면 암호가 생성됩니다. 암호를 안전한 위치에 복사합니다. 프로세스 서버와 마스터 대상 서버를 구성 서버에 인증 및 등록하려면 암호가 필요합니다. 암호는 구성 서버 통신에서 채널 무결성을 보장하는 데에도 사용됩니다. 암호를 다시 생성할 수 있지만 새 암호를 사용하여 마스터 대상 서버와 프로세스 서버를 다시 등록해야 합니다.

	![Passphrase](./media/site-recovery-vmware-to-azure/ASRVMWareRegister2.png)

등록하면 자격 증명 모음의 **구성 서버** 페이지에 구성 서버가 나열됩니다.

## 4단계: VPN 연결 설정
 
인터넷을 통해 또는 VPN 연결이나 Express 경로 연결을 사용하여 구성 서버에 연결할 수 있습니다. 인터넷 연결은 서버의 공용 가상 IP 주소와 함께 가상 컴퓨터의 끝점을 사용합니다. VPN은 끝점 개인 포트와 함께 서버의 내부 IP 주소를 사용합니다.
 
다음과 같이 서버에 대한 VPN 연결을 구성할 수 있습니다.

1. 사이트 간 연결이나 Azure Express 경로 연결이 설정되지 않은 경우 다음 항목에서 자세히 알아볼 수 있습니다.

	- [Express 경로 또는 VPN - 내게 적합한 연결](http://azure.microsoft.com/blog/2014/06/10/expressroute-or-virtual-network-vpn-whats-right-for-me/) 
	- [Azure 가상 컴퓨터에 대한 사이트 간 연결 구성](https://msdn.microsoft.com/library/azure/dn133795.aspx)
	- [Express 경로 구성](https://msdn.microsoft.com/library/azure/dn606306.aspx) 
	
2. 자격 증명 모음에서 **서버** > **구성 서버** > 구성 서버 > **구성**을 클릭합니다.
3. **연결 설정**에서 **연결 유형**을 **VPN**으로 설정합니다. VPN이 설정되어 있고 온-프레미스 사이트에서 인터넷에 액세스할 수 없는 경우 VPN 옵션을 선택해야 합니다. 그렇지 않으면 프로세스 서버가 공용 끝점에서 복제 데이터를 마스터 대상 서버로 보낼 수 없습니다.

	![Enable VPN](./media/site-recovery-vmware-to-azure/ASRVMWare_EnableVPN.png)

## 5단계: 마스터 대상 서버 배포

1. **대상 리소스 준비**에서 **마스터 대상 서버 배포**를 클릭합니다.
2. 마스터 대상 서버 정보와 자격 증명을 지정합니다. 이 서버는 서버를 등록한 구성 서버와 동일한 Azure 네트워크에 배포됩니다. 클릭하여 완료하면 Windows 또는 Linux 갤러리 이미지를 사용하여 Azure 가상 컴퓨터가 만들어집니다. 

	![Target server settings](./media/site-recovery-vmware-to-azure/ASRVMWare_TSDetails.png)

3. Windows 마스터 서버 가상 컴퓨터는 다음과 같은 공용 TCP 끝점을 사용하여 만들어집니다.

	- 사용자 지정: 공용 포트를 사용하여 인터넷을 통해 복제 데이터를 보냅니다. 개인 포트 9443을 사용하여 프로세스 서버가 VPN을 통해 데이터를 마스터 대상 서버로 보냅니다.
	- 사용자 지정1: 개인 포트 9080을 사용하여 프로세스 서버가 VPN을 통해 데이터를 대상 서버로 보냅니다.
	- PowerShell: 개인 포트: 5986
	- 원격 데스크톱: 개인 포트: 3389

4. Linux 마스터 서버 가상 컴퓨터는 다음과 같은 끝점을 사용하여 만들어집니다.

	- 사용자 지정: 공용 포트를 사용하여 인터넷을 통해 복제 데이터를 보냅니다. 개인 포트 9443을 사용하여 프로세스 서버가 VPN을 통해 데이터를 마스터 대상 서버로 보냅니다.
	- 사용자 지정1: 개인 포트 9080을 사용하여 프로세스 서버가 VPN을 통해 데이터를 마스터 대상 서버로 보냅니다.
	- SSH: 개인 포트 22

5. **가상 컴퓨터**에서 가상 컴퓨터가 시작될 때까지 기다립니다. 

	- Windows를 사용하여 서버를 구성한 경우 원격 데스크톱 정보를 적어둡니다.
	- Linux로 구성했으며 VPN을 통해 연결하는 경우 가상 컴퓨터의 내부 IP 주소를 적어둡니다. 인터넷을 통해 연결하는 경우 공용 IP 주소를 적어둡니다.
6.  서버에 로그온하여 설치를 완료하고 구성 서버에 등록합니다. Windows를 실행하는 경우 다음을 수행합니다.

	1. 가상 컴퓨터에 대한 원격 데스크톱 연결을 시작합니다. 처음 로그온하는 경우 PowerShell 창에서 스크립트가 실행됩니다. 창을 닫지 마세요. 완료되면 호스트 에이전트 구성 도구가 자동으로 열려 서버를 등록합니다.
	2. **호스트 에이전트 구성**에서 구성 서버의 내부 IP 주소와 포트 443을 지정합니다. 가상 컴퓨터가 구성 서버와 동일한 Azure 네트워크에 연결되어 있으므로 VPN 모드를 통해 연결하지 않는 경우에도 내부 주소와 개인 포트 443을 사용할 수 있습니다. **HTTPS 사용**을 설정된 상태로 둡니다. 앞에서 적어둔 구성 서버의 암호를 입력합니다. **확인**을 클릭하여 서버를 등록합니다. 페이지의 NAT 옵션은 무시해도 됩니다. 해당 옵션은 사용되지 않습니다.

	![Windows master target server](./media/site-recovery-vmware-to-azure/ASRVMWare_TSRegister.png)

6. Linux를 실행하는 경우 다음을 수행합니다.
	1. sftp 클라이언트를 사용하여 [서버 설치 프로그램 tar 파일](http://go.microsoft.com/fwlink/?LinkID=529757&clcid=0x409)을 가상 컴퓨터에 복사합니다. 또는 로그온한 다음 wget을 사용하여 빠른 시작 페이지의 링크에서 파일을 다운로드할 수 있습니다. 
	2. 보안 셸 클라이언트를 사용하여 서버에 로그온합니다. VPN을 통해 Azure 네트워크에 연결되어 있는 경우 내부 IP 주소를 사용합니다. 그렇지 않으면 외부 IP 주소와 SSH 공용 끝점을 사용합니다.
	3. 다음을 실행하여 GZip 압축된 설치 프로그램에서 파일을 추출합니다. **tar -xvzf Microsoft-ASR_UA_8.2.0.0_RHEL6-64***"   

		![Linux master target server](./media/site-recovery-vmware-to-azure/ASRVMWare_TSLinuxTar.png)

	4. tar 파일의 내용을 추출한 디렉터리에 있는지 확인하고 "**sudo ./install.sh**" 명령을 실행합니다. **2. 마스터 대상** 옵션을 선택합니다. 다른 옵션은 기본 설정을 그대로 둡니다.

		![Register target server](./media/site-recovery-vmware-to-azure/ASRVMWare_TSLinux.png)

	5. 설치가 완료되면 명령줄 **호스트 구성 인터페이스**가 나타납니다. 창의 크기를 조정하지 마세요. 
	6. 화살표 키를 사용하여 **전역**을 선택하고 Enter 키를 누릅니다.
	7. **IP 주소 입력**에 구성 서버의 내부 주소와 포트 22를 입력합니다.
	8.  앞에서 적어둔 구성 서버의 암호를 지정하고 Enter 키를 누릅니다. **종료**를 선택하여 설치를 완료합니다. PuTTY 클라이언트를 사용하여 ssh를 통해 가상 컴퓨터에 연결하는 경우 Shift+Insert로 붙여넣을 수 있습니다. 
	9.  오른쪽 화살표 키를 사용하여 종료하고 Enter 키를 누릅니다.

		![Master target server settings](./media/site-recovery-vmware-to-azure/ASRVMWare_TSLinux2.png)

7. 몇 분(5-10) 동안 기다린 후 **서버** > **구성 서버** 페이지의 **서버 정보** 탭에서 마스터 대상 서버가 등록된 것으로 나열되는지 확인합니다. Linux를 실행하며 등록되지 않은 경우 /usr/local/ASR/Vx/bin/hostconfigcli에서 호스트 구성 도구를 다시 실행합니다. 수퍼 사용자 권한으로 chmod를 실행하여 액세스 권한을 설정해야 합니다.

	![Verify target server](./media/site-recovery-vmware-to-azure/ASRVMWare_TSList.png)

## 6단계: 온-프레미스 프로세스 서버 배포

1. 빠른 시작 > **프로세스 서버 온-프레미스 설치** > **프로세스 서버 다운로드 및 설치**를 클릭합니다.

	![Install process server](./media/site-recovery-vmware-to-azure/ASRVMWare_PSDeploy.png)

2. 프로세스 서버를 설치할 서버에 다운로드한 zip 파일을 복사합니다. zip 파일에는 다음 두 개의 설치 파일이 들어 있습니다.

	- Microsoft-ASR_CX_TP_8.2.0.0_Windows*
	- Microsoft-ASR_CX_8.2.0.0_Windows*

3. 보관 파일의 압축을 풀고 설치 파일을 서버의 한 위치에 복사합니다.
4. **Microsoft-ASR_CX_TP_8.2.0.0_Windows*** 설치 파일을 실행하고 지침을 따릅니다. 이렇게 하면 배포에 필요한 타사 구성 요소가 설치됩니다.
5. 그런 다음 **Microsoft-ASR_CX_8.2.0.0_Windows***를 실행합니다.
6. **서버 모드** 페이지에서 **프로세스 서버**를 선택합니다.
7.	VPN을 통해 연결하는 경우 **구성 서버 정보**에서 구성 서버의 내부 IP 주소를 지정하고 포트에 443을 지정합니다. 그렇지 않으면 공용 가상 IP 주소 및 매핑된 공용 HTTP 끝점을 지정합니다.
8.	자동 푸시를 사용하여 서비스를 설치할 때 검증을 사용하지 않도록 설정하려면 **모바일 서비스 소프트웨어 서명 검증**을 선택 취소합니다. 서명 검증을 수행하려면 프로세스 서버에서 인터넷에 연결할 수 있어야 합니다.
9.	구성 서버의 암호를 입력합니다.

	![Register configuration server](./media/site-recovery-vmware-to-azure/ASRVMWare_CSRegister.png)

8. 서버 설치를 완료합니다. vCenter 서버를 검색할 수 있으려면 서버에 VMware vSphere CLI 5.1을 설치해야 합니다.

	![Register process server](./media/site-recovery-vmware-to-azure/ASRVMWare_PSRegister2.png)

자격 증명 모음 > **구성 서버** > **서버 정보**에서 프로세스 서버가 성공적으로 등록되었는지 확인합니다.

![Validate process server](./media/site-recovery-vmware-to-azure/ASRVMWare_ProcessServerRegister.png)

프로세스 서버를 등록할 때 모바일 서비스에 대해 서명 검증을 사용하지 않도록 설정하지 않은 경우 나중에 다음과 같이 설정할 수 있습니다.

1. 관리자 권한으로 프로세스 서버에 로그온한 다음 편집을 위해 C:\pushinstallsvc\pushinstaller.conf 파일을 엽니다. **[PushInstaller.transport]** 섹션 아래에 다음 줄을 추가합니다. **SignatureVerificationChecks="0"**. 파일을 저장하고 닫습니다.
1. InMage PushInstall 서비스를 다시 시작합니다.


## 7단계: vCenter 서버 추가

1. **서버** > **구성 서버** 탭에서 구성 서버를 선택하고 vCenter 서버를 클릭하여 추가합니다.

	![Select vCenter server](./media/site-recovery-vmware-to-azure/ASRVMWare_AddVCenter.png)

2. vCenter 서버의 세부 정보를 지정하고 서버 검색에 사용할 프로세스 서버를 선택합니다.  프로세스 서버는 vCenter 서버와 동일한 네트워크에 있어야 하며 VMware vSphere CLI 5.1이 설치되어 있어야 합니다.
3. 검색이 완료되면 vCenter 서버가 구성 서버 아래에 나열됩니다.
	
	![vCenter server settings](./media/site-recovery-vmware-to-azure/ASRVMWare_AddVCenter2.png)


## 8단계: 보호 그룹 만들기

1. **보호된 항목** > **보호 그룹**을 열고 보호 그룹을 클릭하여 추가합니다.

	![Create protection group](./media/site-recovery-vmware-to-azure/ASRVMWare_CreatePG1.png)

2. **보호 그룹 설정 지정** 페이지에서 그룹의 이름을 지정하고 그룹을 만들 구성 서버를 선택합니다.

	![Protection group settings](./media/site-recovery-vmware-to-azure/ASRVMWare_CreatePG2.png)

3. **복제 설정 지정** 페이지에서 그룹의 모든 컴퓨터에 사용할 복제 설정을 구성합니다.   

	![Protection group replication](./media/site-recovery-vmware-to-azure/ASRVMWare_CreatePG3.png)

4. 설정:
	- **다중 VM 일관성**: 이 설정을 켜면 보호 그룹의 컴퓨터에 응용 프로그램 일치 공유 복구 지점이 만들어집니다. 이 설정은 보호 그룹의 모든 컴퓨터에서 동일한 작업을 실행하는 경우에 가장 적합합니다. 모든 컴퓨터가 동일한 데이터 지점으로 복구됩니다. Windows 서버에만 사용할 수 있습니다. 
	- **RPO 임계값**: 연속 데이터 보호 복제 RPO가 구성된 RPO 임계값을 초과하면 경고가 생성됩니다.
	- **복구 지점 보존**: 보존 기간을 지정합니다. 보호된 컴퓨터를 이 기간 내의 모든 지점으로 복구할 수 있습니다.
	- **응용 프로그램 일치 스냅숏 빈도**: 응용 프로그램 일치 스냅숏이 포함된 복구 지점을 만드는 방법을 지정합니다.

**보호된 항목** 페이지에서 생성 시 보호 그룹을 모니터링할 수 있습니다. 

## 9단계: 모바일 서비스 푸시

보호 그룹에 컴퓨터를 추가하면 프로세스 서버가 자동으로 각 컴퓨터에 모바일 서비스를 푸시 및 설치합니다. Windows를 실행하는 보호된 컴퓨터에 대해 이 자동 푸시 메커니즘을 사용하려는 경우 각 컴퓨터에서 다음을 수행해야 합니다.

1. **파일 및 프린터 공유**와 **Windows Management Instrumentation**을 허용하도록 Windows 방화벽을 구성합니다. 도메인에 속하는 컴퓨터의 경우 GPO를 사용하여 방화벽 정책을 구성할 수 있습니다.
2. 푸시 설치에 사용되는 계정은 보호할 컴퓨터의 Administrators 그룹에 속해야 합니다. 이러한 자격 증명은 푸시 설치에만 사용됩니다. 모바일 서비스에 저장되지 않으며 서비스가 보호된 후에는 삭제됩니다. 보호 그룹에 컴퓨터를 추가할 때 이 자격 증명을 제공합니다.

	![Mobility credentials](./media/site-recovery-vmware-to-azure/ASRVMWare_PushCredentials.png)

3. 관리자 계정이 도메인 계정이 아닌 경우 로컬 컴퓨터에서 원격 사용자 계정 제어를 사용하지 않도록 설정해야 합니다. 이렇게 하려면 HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System에서 LocalAccountTokenFilterPolicy 항목을 만들고(없는 경우) DWORD 값에 할당합니다. 

Linux를 실행하는 컴퓨터를 보호하려는 경우 다음을 수행해야 합니다.

1. 계정이 원본 Linux 서버의 루트 사용자인지 확인합니다.
1. 보호할 컴퓨터에 최신 openssh, openssh-server, openssl 패키지를 설치합니다.
1. ssh 포트 22를 사용하도록 설정합니다.
2. sshd 구성 파일에서 Sftp 하위 시스템 및 암호 인증을 사용하도록 설정합니다.
	1. 루트 사용자 계정으로 로그인합니다.
	2. /etc/ssh/sshd_config 파일에서 "PasswordAuthentication"으로 시작하는 줄을 찾습니다. 줄의 주석 처리를 제거하고 값을 "no"에서 "yes"로 변경합니다. 

		![Linux mobility](./media/site-recovery-vmware-to-azure/ASRVMWare_LinuxPushMobility1.png)

	4. "Subsystem"으로 시작하는 줄을 찾아서 주석 처리를 제거합니다. 

		![Linux push mobility](./media/site-recovery-vmware-to-azure/ASRVMWare_LinuxPushMobility1.png)

## 10단계: 보호 그룹에 컴퓨터 추가

1. **보호된 항목** > **보호 그룹** > **컴퓨터** 탭을 열고 검색된 vCenter 서버가 관리하는 가상 컴퓨터 또는 물리적 컴퓨터를 추가합니다. 특정 응용 프로그램을 실행하는 컴퓨터를 동일한 그룹에 추가하도록 보호 그룹이 작업을 미러링하는 것이 좋습니다.

	![Add machines](./media/site-recovery-vmware-to-azure/ASRVMWare_PushCredentials..png)

2. **가상 컴퓨터 추가**의 **가상 컴퓨터 선택** 페이지에서 V-Center 서버를 선택한 다음 해당 서버의 컴퓨터를 선택합니다.

	![Add V-Center server](./media/site-recovery-vmware-to-azure/ASRVMWare_SelectVMs.png)

3. 보호 그룹에 컴퓨터를 추가하면 온-프레미스 프로세스 서버에서 모바일 서비스가 자동으로 설치됩니다. 자동 푸시 메커니즘이 작동하려면 이전 단계에서 설명한 대로 보호된 컴퓨터를 설정했는지 확인합니다.
4. **가상 컴퓨터 선택**에서 보호할 컴퓨터를 관리하는 vCenter 서버를 선택한 다음 가상 컴퓨터를 선택합니다.

4. 복제에 사용할 서버와 저장소를 선택합니다. 

	![vCenter server](./media/site-recovery-vmware-to-azure/ASRVMWare_MachinesResources.png)

5. 원본 서버에 대한 사용자 자격 증명을 제공합니다. 이 자격 증명은 원본 컴퓨터에 모바일 서비스를 자동으로 설치하는 데 필요합니다. Windows 서버의 경우 원본 서버에서 계정에 관리자 권한이 있어야 합니다. Linux의 경우 서버에서 계정에 수퍼 사용자 권한이 있어야 합니다.

	![Linux credentials](./media/site-recovery-vmware-to-azure/ASRVMWare_VMMobilityInstall.png)

6. 확인 표시를 클릭하여 보호 그룹에 컴퓨터 추가를 완료하고 각 컴퓨터에 대해 초기 복제를 시작합니다. **작업** 페이지에서 상태를 모니터링할 수 있습니다.

	![Add V-Center server](./media/site-recovery-vmware-to-azure/ASRVMWare_PGJobs.png)

7. 또한 **보호된 항목** > <보호 그룹> > **가상 컴퓨터**를 클릭하여 보호 상태를 모니터링합니다. 초기 복제가 완료되고 컴퓨터가 데이터를 동기화하면 **보호됨** 상태로 표시됩니다.

	![Virtual machine jobs](./media/site-recovery-vmware-to-azure/ASRVMWare_PGJobs.png)

## 11단계: 보호된 컴퓨터 속성 설정

1. 컴퓨터가 **보호됨** 상태이면 해당 장애 조치(Failover) 속성을 구성할 수 있습니다. 보호 그룹 정보에서 컴퓨터를 선택하고 **구성** 탭을 엽니다.
2. 장애 조치(Failover) 후에 Azure에서 컴퓨터에 지정될 이름과 Azure 크기를 수정할 수 있습니다. 장애 조치(Failover) 후에 컴퓨터가 연결되는 Azure 네트워크를 선택할 수도 있습니다. 다음 사항에 유의하세요.

	- Azure 컴퓨터의 이름은 필수 조건에서 설명한 Azure 요구 사항을 준수해야 합니다.
	- 기본적으로 Azure의 복제된 가상 컴퓨터는 Azure 네트워크에 연결되지 않습니다. 복제된 가상 컴퓨터가 통신할 수 있게 하려면 해당 컴퓨터에 대해 동일한 Azure 네트워크를 설정해야 합니다.

	![Set virtual machine properties](./media/site-recovery-vmware-to-azure/ASRVMWare_VMProperties.png)

## 12단계: 장애 조치(Failover) 실행

1. **복구 계획** 페이지에서 복구 계획을 추가합니다. 계획의 세부 정보를 지정하고 대상으로 **Azure**를 선택합니다.

	![Configure recovery plan](./media/site-recovery-vmware-to-azure/ASRVMWare_RP1.png)

2. **가상 컴퓨터 선택**에서 보호 그룹을 선택한 다음 그룹에서 복구 계획에 추가할 컴퓨터를 선택합니다.

	![Add virtual machines](./media/site-recovery-vmware-to-azure/ASRVMWare_RP2.png)

3. 필요한 경우 계획을 사용자 지정하여 그룹을 만들고 복구 계획의 컴퓨터가 장애 조치(Failover)되는 순서를 지정할 수 있습니다. 수동 작업 및 스크립트에 대한 프롬프트를 추가할 수도 있습니다.

	![Customize recovery plan](./media/site-recovery-vmware-to-azure/ASRVMWare_RP2.png)

5. **복구 계획** 페이지에서 계획을 선택하고 **테스트 장애 조치(Failover)**를 클릭합니다.
6. **장애 조치(Failover) 확인**에서 장애 조치(Failover) 방향(Azure로)을 확인하고 장애 조치(Failover)할 복구 지점을 선택합니다. 
7. 장애 조치(Failover) 작업이 완료될 때까지 기다린 다음 장애 조치(Failover)가 예상대로 작동했는지, 그리고 복제된 가상 컴퓨터가 Azure에서 성공적으로 시작되는지 확인합니다.

<a name="thirdparty"></a><h2>타사 소프트웨어 통지 및 정보</h2>

Do Not Translate or Localize

The software and firmware running in the Microsoft product or service is based on or incorporates material from the projects listed below (collectively, "Third Party Code").  Microsoft is the not original author of the Third Party Code.  The original copyright notice and license, under which Microsoft received such Third Party Code, are set forth below.

The information in Section A is regarding Third Party Code components from the projects listed below. Such licenses and information are provided for informational purposes only.  This Third Party Code is being relicensed to you by Microsoft under Microsoft's software licensing terms for the Microsoft product or service.  

The information in Section B is regarding Third Party Code components that are being made available to you by Microsoft under the original licensing terms. 

The complete file may be found on the [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=530254)  h. Microsoft reserves all rights not expressly granted herein, whether by implication, estoppel or otherwise.


<!--HONumber=49-->