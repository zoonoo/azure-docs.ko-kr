<properties
   pageTitle="Azure 마켓플레이스를 위해 가상 컴퓨터 이미지 만들기 | Microsoft Azure"
   description="Azure 마켓플레이스에서 다른 사용자가 구입할 수 있도록 가상 컴퓨터 이미지를 만드는 방법에 대한 자세한 지침입니다."
   services="Azure Marketplace"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
   ms.service="marketplace-publishing"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="Azure"
   ms.workload="na"
   ms.date="10/09/2015"
   ms.author="hascipio; v-divte"/>

# Azure 마켓플레이스에 대한 가상 컴퓨터 이미지 만들기 가이드

이 문서의 **2단계**에서는 Azure 마켓플레이스에 배포할 SKU의 기초가 되는 VHD를 준비하는 과정을 안내합니다. Linux 기반 SKU를 제공할지 Windows 기반 SKU를 제공할지 여부에 따라 프로세스는 달라집니다. 이 섹션에서는 두 시나리오 모두에 대해 설명합니다. 이 프로세스는 [계정 만들기 및 등록][link-acct-creation]과 함께 병렬로 수행할 수 있습니다.

## 1\. 제품 및 SKU 정의

이 섹션에서는 제품과 제품 아래의 SKU를 정의합니다.

제품은 모든 SKUS의 "부모"입니다. 제품을 여러 개 보유할 수 있습니다. 제품을 구성하는 방법은 게시자가 결정합니다. 제품은 스테이징으로 푸시될 때 모든 SKU와 함께 푸시됩니다. SKU 식별자는 URL에 표시되므로 신중하게 고려하세요.

- Azure.com – http://azure.microsoft.com/marketplace/partners/{PartnerNamespace}/{OfferIdentifier}-{SKUidentifier}

- Azure Preview 포털 - https://portal.azure.com/#gallery/{PublisherNamespace}.{OfferIdentifier}{SKUIDdentifier}

SKU는 VM 이미지에 대한 상업용 이름입니다. VM 이미지에는 OS 디스크 하나와 0개 이상의 데이터 디스크가 포함되어 있습니다. 가상 컴퓨터에 대한 완벽한 저장소 프로필입니다. 디스크당 VHD 하나가 필요합니다. 데이터 디스크가 비어 있는 경우에도 VHD를 만들어야 합니다.

사용 중인 운영 체제에 상관없이 SKU에 필요한 최소 개수의 데이터 디스크만 추가합니다. 최종 사용자는 배포 시 이미지의 일부인 디스크를 제거할 수 없지만, 필요한 경우 배포 중이나 이후에 언제든지 디스크를 추가할 수 있습니다.

### 1\.1 제품 추가

1. 판매자 계정을 사용하여 [게시 포털][link-pubportal]에 로그인합니다.
2. 게시 포털의 **가상 컴퓨터** 탭으로 이동합니다. 표시된 입력 필드에 제품 이름을 입력하고 만듭니다.
3. 제품 이름은 일반적으로 Azure 마켓플레이스에 판매할 계획인 제품/서비스의 이름입니다.

### 1\.2 SKU 정의
제품을 추가한 후 SKU를 정의/식별해야 합니다. 게시자는 제품을 여러 개 보유할 수 있으며 각 제품에는 그 아래 여러 개의 SKU를 포함할 수 있습니다. 제품은 스테이징으로 푸시될 때 모든 SKU와 함께 푸시됩니다.

1. **SKU를 추가합니다.** URL에 사용할 식별자가 필요합니다. 이 식별자는 게시 프로필 내에서 공유해야 하지만, 식별자가 다른 게시자와 충돌할 위험은 없습니다.

> [AZURE.NOTE]제품 및 SKU 식별자는 마켓플레이스의 제품 URL에 표시됩니다.

2. **SKU에 대한 요약 설명을 추가합니다.** 이 설명은 UX에서 사용자에게 표시되므로 읽기 쉽게 입력하는 것이 좋습니다. 이 정보는 "스테이징으로 푸시"할 때까지 잠글 필요가 없습니다. 그때까지 자유롭게 편집할 수 있습니다.
3. Windows 기반 SKU를 사용할 경우 제안된 링크를 따라 Windows Server의 승인된 버전을 습득하세요.

## 2\. Azure 호환 VHD 만들기(Linux 기반)
다음 섹션에서는 Microsoft Azure 마켓플레이스에 대한 Linux 기반 VM 이미지를 만드는 모범 사례를 중심으로 다룹니다. 단계별 연습은 [Linux 운영 체제가 포함된 가상 하드 디스크 만들기 및 업로드][link-azure-vm-1] 설명서를 참조하세요.

> [AZURE.TIP]다음 단계의 대부분(예: 에이전트 설치, 커널 부팅 매개 변수)은 Microsoft Azure 이미지 갤러리에서 제공되는 Linux 이미지에 대해 이미 수행한 작업입니다. 따라서 이러한 이미지 중 하나를 토대로 시작하면 Azure 미인식 Linux 이미지를 구성하는 것보다 시간을 절약할 수 있습니다.

### 2\.1 올바른 VHD 크기 선택
게시된 SKU(VM 이미지)는 SKU에 대한 디스크 수를 지원하는 모든 VM 크기에서 작동하도록 설계되어야 합니다. 권장 크기에 대한 지침을 제공할 수 있지만, 이러한 지침은 권장 사항이지 강제 사항은 아닙니다.

1. Linux OS VHD: VM 이미지의 Linux OS VHD는 30GB - 50GB 고정 형식 VHD로 만들어져야 합니다. 30GB 미만일 수 없습니다. 물리적 크기가 VHD 크기보다 작은 경우 VHD가 스파스여야 합니다. 50GB를 초과하는 Linux VHD는 사례별로 고려됩니다. 다른 형식으로 된 VHD가 이미 있는 경우 [Convert-VHD PowerShell cmdlet을 사용하여 형식을 변경할 수 있습니다.][link-technet-1]
2. 데이터 디스크 VHD: 데이터 디스크는 1TB 정도 될 수 있습니다. 데이터 디스크 VHD는 고정 형식 VHD로 작성되어야 하지만 스파스일 수도 있습니다. 디스크 크기를 결정할 때는 최종 사용자가 이미지 내에서 VHD 크기를 조정할 수 없다는 점을 고려하세요.

### 2\.2 최신 Azure Linux 에이전트가 설치되어 있는지 확인
OS VHD를 준비할 경우 RPM 또는 Deb 패키지를 사용하여 최신 [Azure Linux 에이전트][link-azure-vm-2]가 설치되어 있는지 확인합니다. 보통 패키지 이름은 walinuxagent 또는 WALinuxAgent이지만 배포 시 확실한 이름을 확인하세요. 에이전트는 Azure에서 Linux IaaS 배포를 구축하는 데 필요한 핵심 기능(예: VM 프로비전 및 네트워킹 기능)을 제공합니다.

에이전트를 다양한 방법으로 구성할 수 있지만 일반 에이전트 구성을 사용하여 호환성을 최대화하는 것이 좋습니다. 에이전트를 수동으로 설치할 수 있지만, 가능한 경우 배포에서 미리 구성된 패키지를 사용하는 것이 좋습니다.

[GitHub 리포지토리][link-github-waagent]에서 에이전트를 수동으로 설치하려면 먼저 'waagent' 파일을 /usr/sbin에 복사하고 다음 명령을 루트로 실행합니다.

    # chmod 755 /usr/sbin/waagent
    # /usr/sbin/waagent -install

에이전트 구성 파일은 /etc/waagent.conf에 있습니다.

### 2\.3 필요한 라이브러리가 포함되어 있는지 확인
Azure Linux 에이전트 이외에 다음 라이브러리도 포함해야 합니다.

1. [Linux Integration Services][link-intsvc] 버전 3.0 이상을 커널에 사용하도록 설정해야 합니다. [Linux 커널 요구 사항](../virtual-machines/virtual-machines-linux-create-upload-vhd-generic/#linux-kernel-requirements)을 참조하세요.
2. Azure I/O 안정성을 위한 [커널 패치](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/scsi/storvsc_drv.c?id=5c1b10ab7f93d24f29b5630286e323d1c5802d5c)(최신 커널에 대해 필요하지 않은 경우도 있지만 확인해야 함)
3. [Python][link-python] 2.6 이상
4. Python] pyasn1 패키지(아직 설치되지 않은 경우)
5. [OpenSSL][link-openssl](v1.0 이상 권장)

### 2\.4 디스크 파티션 설정
논리 볼륨 관리자를 사용하지 **않는** 것이 좋습니다. OS 디스크에 대해 단일 루트 파티션을 만듭니다. OS 또는 데이터 디스크에서 스왑 파티션을 사용하지 마세요. 스왑 파티션은 /etc/fstab에 탑재되지 않은 경우에도 제거하는 것이 좋습니다. 필요한 경우 Linux 에이전트를 사용하여 로컬 리소스 디스크(/dev/sdb)에 스왑 파티션을 만들 수 있습니다.

### 2\.5 필요한 커널 부팅 줄 매개 변수 추가
또한 다음 매개 변수를 커널 부팅 줄에 추가해야 합니다.

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300

그러면 Azure 지원에서 필요 시 직렬 콘솔 출력을 고객에게 제공할 수 있습니다. 또한 클라우드 저장소에서 OS 디스크를 탑재하는 데 적절한 제한 시간을 제공합니다. SKU가 최종 고객이 가상 컴퓨터에 직접 SSH로 연결하지 못하도록 차단하는 경우에도 직렬 콘솔 출력을 사용해야 합니다.

### 2\.6 기본적으로 SSH 서버 포함
최종 사용자에 대해 SSH를 사용하는 것이 좋습니다. SSH 서버를 사용하도록 설정한 경우 sshd config에 SSH keep alive를 ClientAliveInterval 180 옵션과 함께 추가합니다. 180을 사용하는 것이 좋지만 30 - 235 범위가 허용됩니다. 응용 프로그램에 따라 최종 사용자에게 가상 컴퓨터에 대한 직접 SSH를 허용하지 않는 것이 좋은 경우도 있습니다. SSH가 명시적으로 차단된 경우 ClientAliveInterval을 설정할 필요가 없습니다.

### 2\.7 네트워킹 요구 사항 충족
Azure 호환 Linux VM 이미지에 대한 네트워킹 요구 사항은 다음과 같습니다.

- 대부분의 경우 NetworkManager를 사용하지 않도록 설정하는 것이 가장 좋습니다. 단, CentOS 7.x 기반 시스템(및 파생 버전)에서는 NetworkManager를 사용하도록 설정해야 합니다.
- 네트워킹 구성은 ifup/ifdown 스크립트를 통해 제어할 수 있어야 합니다. Linux 에이전트는 이러한 명령을 사용하여 프로비전 중에 네트워킹을 다시 시작할 수 있습니다.
- 사용자 지정 네트워크 구성이 없어야 합니다. 마지막 단계로 resolv.conf 파일을 삭제해야 합니다. 일반적으로 프로비전 해제의 일부로 수행됩니다([Azure Linux 에이전트 사용자 가이드](../virtual-machines/virtual-machines-linux-agent-user-guide/) 참조). 다음 명령을 사용하여 이 단계를 수동으로 수행할 수도 있습니다.

        rm /etc/resolv.conf

- 네트워크 장치가 부팅 시 시작되고 DHCP를 사용해야 합니다.
- IPv6는 Azure에서 지원되지 않습니다. 이 속성은 사용하도록 설정해도 작동되지 않습니다.

### 2\.8 보안 모범 사례가 충족되는지 확인
Azure 마켓플레이스의 SKU는 보안에 대한 모범 사례를 따라야 합니다. 여기에는 다음이 포함됩니다.

- 배포에 대한 모든 보안 패치를 설치합니다.
- 배포 보안 지침을 따릅니다.
- 프로비전 인스턴스 전체에서 동일하게 유지되는 기본 계정을 만들지 마세요.
- bash 기록 항목을 지웁니다.
- iptables(방화벽) 소프트웨어를 포함하되 규칙을 사용하도록 설정하지 마세요. 그러면 고객에게 원활한 기본 환경이 제공됩니다. 추가 구성을 위해 VM 방화벽을 사용하려는 고객은 해당 요구 사항을 충족하도록 iptables 규칙을 구성할 수 있습니다.

### 2\.9 이미지 일반화
Azure 마켓플레이스의 모든 이미지는 일반적으로 재사용 가능해야 합니다. 즉, 특정 구성 항목에 대한 이미지를 제거해야 합니다. Linux에서 이렇게 하려면 OS VHD를 프로비저닝 해제해야 합니다.

Linux의 프로비저닝 해제 명령은 다음과 같습니다.

        # waagent -deprovision

이 명령은 다음 작업을 자동으로 수행합니다.

- /etc/resolv.conf에서 nameserver 구성 제거
- 캐시된 DHCP 클라이언트 임대 제거
- 호스트 이름을 localhost.localdomain으로 다시 설정

또한 다음 작업을 완료하도록 구성 파일(/etc/waagent.conf)을 설정하는 것이 좋습니다.

- 구성 파일에서 Provisioning.RegenerateSshHostKeyPair를 'y'로 설정하여 모든 SSH 호스트 키 제거
- 구성 파일에서 Provisioning.DeleteRootPassword를 'y'로 설정하여 /etc/shadow에서 '루트' 암호 제거 구성 파일의 내용에 대한 설명서는 Agent Github 리포지토리 페이지([https://github.com/Azure/WALinuxAgent](https://github.com/Azure/WALinuxAgent) 및 아래로 스크롤)에서 추가 정보 파일의 "구성" 섹션을 참조하세요.  

이제 Linux VM의 일반화를 완료했습니다. Azure 포털, 명령줄 또는 VM 내에서 VM을 종료합니다. 종료가 완료되면 3.4단계를 계속합니다.

## 3\. Azure 호환 VHD 만들기(Windows 기반)
다음 섹션에서는 Microsoft Azure 마켓플레이스에 대해 Windows Server 기반 SKU를 만드는 단계를 중심으로 다룹니다.

### 3\.1 올바른 기본 VHD를 사용 중인지 확인
VM 이미지용 OS VHD는 Microsoft Azure 승인 기본 이미지를 기반으로 하고 Windows Server 또는 SQL Server를 포함해야 합니다.

시작하려면 [Microsoft Azure 포털][link-azure-portal]에 있는 다음 이미지 중 하나에서 VM을 만듭니다.

- Windows Server([2012 R2 Datacenter][link-datactr-2012-r2], [2012 Datacenter][link-datactr-2012], [2008 R2 SP1][link-datactr-2008-r2])
- SQL Server 2014([Enterprise][link-sql-2014-ent], [Standard][link-sql-2014-std], [Web][link-sql-2014-web])
- SQL Server 2012 SP2([Enterprise][link-sql-2012-ent], [Standard][link-sql-2012-std], [Web][link-sql-2012-web])

이러한 링크는 게시 포털의 SKU 페이지 아래에도 있습니다.

> [AZURE.TIP]최신 Azure 관리 포털 또는 PowerShell을 사용 중인 경우 2014년 9월 8일 이후에 게시된 Windows Server 이미지가 승인됩니다.


### 3\.2 Windows VM 만들기
Microsoft Azure 포털에서 승인된 기본 이미지를 기반으로 VM을 간단히 만들 수 있습니다. 프로세스 개요는 다음과 같습니다.

1. 기본 이미지 페이지에서 **VM 만들기**를 선택합니다. 그러면 새 [Microsoft Azure 포털][link-azure-portal]로 이동합니다.

    ![drawing][img-acom-1]

2. 사용할 Azure 구독에 대한 Microsoft 계정(MSA) 및 암호를 사용하여 포털에 로그인합니다.
3. 프롬프트에 따라 선택한 기본 이미지를 사용하여 VM을 만듭니다. 최소한 VM에 대한 호스트 이름(컴퓨터 이름), 사용자 이름(등록된 관리 사용자) 및 암호를 제공해야 합니다.

    ![drawing][img-portal-vm-create]

4. 배포할 VM의 크기를 선택합니다.

    a. 온-프레미스에서 VHD를 개발하려는 경우 크기는 중요하지 않습니다. 더 작은 VM 중 하나를 사용하는 것이 좋습니다.

    b. Azure에서 이미지를 개발하려는 경우 선택된 이미지에 대한 권장 VM 크기 중 하나를 사용하는 것이 좋습니다.

    c. 가격 책정에 대한 자세한 내용은 포털에 표시되는 권장 가격 책정 계층 선택기를 참조하세요. 게시자가 제공한 세 개의 권장 크기를 제공합니다. 이 경우 게시자는 Microsoft입니다.

    ![drawing][img-portal-vm-size]

5. 속성을 설정합니다.

    a. 빠른 배포를 위해 **선택적 구성** 및 **리소스 그룹**에서 속성에 대한 기본값을 그대로 사용할 수 있습니다.

    b. 원하는 경우 저장소 계정에서 OS VHD를 저장할 저장소 계정을 선택할 수 있습니다.

    c. 원하는 경우 리소스 그룹에서 VM을 배치할 논리 그룹을 선택할 수 있습니다.
6. 배포할 **위치**를 선택합니다.

    a. 온-프레미스에서 VHD를 개발하려면 나중에 이미지를 Azure에 업로드할 것이므로 위치는 중요하지 않습니다.

    b. Azure에서 이미지를 개발하려면 처음부터 미국 기반 Microsoft Azure 지역 중 하나를 사용하는 것이 좋습니다. 그러면 개발자가 인증을 위해 이미지를 제출할 때 Microsoft에서 자동으로 수행되는 VHD 복사 프로세스가 단축됩니다.

    ![drawing][img-portal-vm-location]

7. **만들기**를 클릭합니다. VM이 배포됩니다. 몇 분 이내에 배포되고 SKU에 대한 이미지 만들기를 시작할 수 있습니다.

### 3\.3 클라우드에서 VHD 개발
RDP(원격 데스크톱 프로토콜)를 사용하여 클라우드에서 VHD를 개발하는 것이 좋습니다. 프로비전 중에 지정한 사용자 이름과 암호를 사용하여 RDP에 연결합니다.

> [AZURE.IMPORTANT]\: 온-프레미스에서 VHD를 개발하는 경우 [온-프레미스에 가상 컴퓨터 이미지 만들기](marketplace-publishing-vm-image-creation-on-premise.md)를 참조하세요. **클라우드에서 개발 중인 경우에는 VHD를 다운로드할 필요가 없습니다.**


**[Microsoft Azure 포털][link-azure-portal]을 사용하여 RDP를 통해 연결**

1. **찾아보기**를 선택한 다음 **VM**을 선택합니다.
2. VM 블레이드가 열립니다. 연결하려는 VM이 실행 중인지 확인하고 배포된 VM 목록에서 해당 VM을 선택합니다.
3. 선택된 VM을 설명하는 블레이드가 열립니다. 맨 위에 있는 **연결**을 클릭합니다.
4. 프로비전할 때 지정한 사용자 이름과 암호를 입력하라는 메시지가 표시됩니다.

**PowerShell을 사용하여 RDP를 통해 연결**

원격 데스크톱 파일을 로컬 컴퓨터에 다운로드하려면 [Get-AzureRemoteDesktopFile cmdlet][link-technet-2]을 사용합니다. 이 cmdlet을 사용하려면 서비스 이름과 VM 이름을 알아야 합니다. [Microsoft Azure 포털][link-azure-portal]에서 VM을 만든 경우 VM 속성에서 이 정보를 찾을 수 있습니다.

1. Microsoft Azure 포털에서 **찾아보기**를 선택한 다음 **VM**을 선택합니다.
2. **가상 컴퓨터** 블레이드가 열립니다. VM 목록에서 배포한 VM을 선택합니다.
3. 선택된 VM을 설명하는 블레이드가 열립니다.
4. **속성**을 클릭합니다.
5. 도메인 이름의 첫 부분은 서비스 이름입니다. 호스트 이름은 VM 이름입니다.

    ![drawing][img-portal-vm-rdp]

6. 만든 VM에 대한 RDP 파일을 관리자의 로컬 데스크톱으로 다운로드하기 위한 cmdlet은 다음과 같습니다.

        Get‐AzureRemoteDesktopFile ‐ServiceName “baseimagevm‐6820cq00” ‐Name “BaseImageVM” –LocalPath “C:\Users\Administrator\Desktop\BaseImageVM.rdp”

RDP에 대한 자세한 내용은 MSDN의 [RDP 또는 SSH를 사용하여 Azure VM에 연결](http://msdn.microsoft.com/library/azure/dn535788.aspx)(영문) 문서를 참조하세요.

**VM 구성 및 SKU 만들기**

OS VHD를 다운로드한 후 Hyper­V를 사용하고 SKU 만들기를 시작하도록 VM을 구성합니다. 세부 단계는 TechNet에서 [Hyper­V 설치 및 VM 구성](http://technet.microsoft.com/library/hh846766.aspx) 링크를 참조하세요.

### 3\.4 올바른 VHD 크기 선택
VM 이미지의 Windows OS VHD는 128GB 고정 형식 VHD로 만들어져야 합니다.

물리적 크기가 128GB보다 작은 경우 VHD가 스파스여야 합니다. 이미 제공된 기본 Windows 및 SQL Server 이미지는 이러한 요구 사항을 충족해야 합니다. 가져온 VHD의 형식 또는 크기를 변경하지 마세요.

데이터 디스크는 1TB이하여야 합니다. 디스크 크기를 결정할 때 최종 사용자가 배포 시에 이미지 내의 VHD 크기를 변경할 수 있음에 유의하세요. 데이터 디스크 VHD는 고정 형식 VHD로 작성되어야 하지만 스파스일 수도 있습니다. 데이터 디스크는 비어 있거나 데이터를 포함할 수 있습니다.


### 3\.5 최신 Windows 패치 설치
기본 이미지에는 게시된 날짜까지의 최신 패치가 포함되어 있습니다. 만든 OS VHD를 게시하기 전에 Windows 업데이트가 실행되었고 모든 최신의 '필수' 및 '중요' 보안 패치를 설치했는지 확인하세요.

### 3\.6 필요 시 추가 구성 수행 및 작업 예약
추가 구성이 필요한 경우 시작 시 실행하도록 예약된 작업을 사용하여 배포된 VM을 최종적으로 변경하는 것이 좋습니다.

- 작업이 성공적으로 실행된 후 해당 작업을 자동으로 삭제하는 것이 모범 사례입니다.
- C:\\ 또는 D:\\ 드라이브는 항상 존재하므로 반드시 이 두 드라이브 중 하나를 사용하도록 구성해야 합니다. C:\\는 OS 디스크이고 D:\\는 임시 로컬 디스크입니다.

### 3\.7 이미지 일반화
Azure 마켓플레이스의 모든 이미지는 일반적으로 다시 사용할 수 있어야 합니다. 즉, OS VHD를 일반화해야 합니다.

- Windows의 경우 이미지에 "sysprep"을 실행해야 하므로 'sysprep' 명령을 지원하지 않도록 구성해서는 안 됩니다.
- %windir%\\System32\\Sysprep 디렉터리에서 아래 명령을 실행할 수 있습니다.

        sysprep.exe /generalize /oobe /sshutdown

  운영 체제에 sysprep를 실행하는 방법은 MSDN 문서, [Windows Server VHD를 만들어서 Azure에 업로드](../virtual-machines/virtual-machines-create-upload-vhd-windows-server/)의 단계를 참조하세요.

## 4\. VHD에서 VM 배포
VHD, 일반화된 OS VHD 및 0개 이상의 데이터 디스크 VHD가 Azure 저장소 계정에 업로드된 경우 해당 항목을 테스트할 사용자 VM 이미지로 등록할 수 있습니다. OS VHD는 일반화되므로 VHD URL을 제공하여 VM을 직접 배포할 수 없습니다.

VM 이미지에 대해 자세히 알아보려면 다음 블로그 게시물을 참조하세요.

- [VM 이미지](https://azure.microsoft.com/blog/vm-image-blog-post/)
- [VM 이미지 PowerShell 방법](https://azure.microsoft.com/blog/vm-image-powershell-how-to-blog-post/)
- [Azure에서 VM 이미지 정보](https://msdn.microsoft.com/library/azure/dn790290.aspx)

### 4\.1 사용자 VM 이미지 만들기
SKU에서 사용자 VM 이미지를 만들어 여러 VM을 배포하려면 [VM 이미지 Rest API 만들기](http://msdn.microsoft.com/library/azure/dn775054.aspx)를 사용하여 VHD를 VM 이미지로 등록해야 합니다.

Invoke-WebRequest cmdlet을 사용하여 PowerShell에서 VM 이미지를 만들 수 있습니다. 아래 PowerShell 스크립트는 OS 디스크와 데이터 디스크 하나를 포함하는 VM 이미지를 만드는 방법을 보여 줍니다. PowerShell 세션은 이미 설정되어 있고 구독 집합이어야 합니다.

        # Image Parameters to Specify
        $ImageName=’ENTER-YOUR-OWN-IMAGE-NAME-HERE’
        $Label='ENTER-YOUR-LABEL-HERE'
        $Description='DESCRIBE YOUR IMAGE HERE’
        $osCaching='ReadWrite' $os = 'Windows'
        $state = 'Generalized'
        $osMediaLink = 'https://mystorageaccount.blob.core.windows.net/vhds/myosvhd.vhd'
        $dataCaching='None'
        $lun='1'
        $dataMediaLink='http://mystorageaccount.blob.core.windows.net/vhds/mydatavhd.vhd'
        # Subscription Related Properties
        $SrvMngtEndPoint='https://management.core.windows.net'
        $subscription = Get-AzureSubscription -Current -ExtendedDetails
        $certificate = $subscription.Certificate
        $SubId = $subscription.SubscriptionId
        $body =  
        "<VMImage xmlns=`"http://schemas.microsoft.com/windowsazure`" xmlns:i=`"http://www.w3.org/2001/XMLSchema-instance`">" + Name>" + $ImageName + "</Name>" +
        "<Label>" + $Label + "</Label>" +
        "<Description>" + $Description + "</Description>" + "<OSDiskConfiguration>" +
        "<HostCaching>" + $osCaching + "</HostCaching>" +
        "<OSState>" + $state + "</OSState>" +
        "<OS>" + $os + "</OS>" +
        "<MediaLink>" + $osMediaLink + "</MediaLink>" +
        "</OSDiskConfiguration>" +
        "<DataDiskConfigurations>" +
        "<DataDiskConfiguration>" +
        "<HostCaching>" + $dataCaching + "</HostCaching>" +
        "<Lun>" + $lun + "</Lun>" +
        "<MediaLink>" + $dataMediaLink + "</MediaLink>" +
        "</DataDiskConfiguration>" +
        "</DataDiskConfigurations>" +
        "</VMImage>"
        $uri = $SrvMngtEndPoint + "/" + $SubId + "/" + "services/vmimages" $headers = @{"x-ms-version"="2014-06-01"}
        $response = Invoke-WebRequest -Uri $uri -ContentType "application/xml" -Body
        $body -Certificate $certificate -Headers $headers -Method POST
        if ($response.StatusCode -ge 200 -and $response.StatusCode -lt 300)
        {
        echo "Accepted"
        } else {
        echo "Not Accepted" }
        $opId = $response.Headers.'x-ms-request-id'
        $uri2 = $SrvMngtEndPoint + "/" + $SubId + "/" + "operations" + "/" + $opId $response2 = Invoke-WebRequest -Uri $uri2 -ContentType "application/xml" -
        Certificate $certificate -Headers $headers -Method GET
        $response2.RawContent


이 스크립트를 실행하여 ImageName 매개 변수에 제공된 이름 myVMIMage를 사용하여 사용자 VM 이미지를 만듭니다. 이 이미지는 OS 디스크 하나와 데이터 디스크 하나로 구성됩니다.

이 API는 비동기 작업이며 202를 허용하여 응답합니다. VM 이미지가 만들어졌는지 확인하려면 작업 상태를 쿼리해야 합니다. 반환 응답에서 x-ms-request-id는 작업 ID입니다. 이 id는 아래 $opId에서 설정해야 합니다.

        $opId = #Fill In With Operation ID
        $uri2 = $SrvMngtEndPoint + "/" + $SubId + "/" + "operations" + "/" + "opId"
        $response2 = Invoke‐WebRequest ‐Uri $uri2 ‐ContentType "application/xml" ‐Certificate $certificate ‐Headers $headers ‐Method GET

OS VHD 및 추가 빈 데이터 디스크(이 디스크에 대한 VHD를 만들지 않음)에서 VM 이미지를 만들려면 VM 이미지 만들기 API에서 다음 스크립트를 사용합니다.

        # Image Parameters to Specify
        $ImageName=’myVMImage’
        $Label='IMAGE_LABEL'
        $Description='My VM Image to Test’
        $osCaching='ReadWrite'
        $os = 'Windows'
        $state = 'Generalized'
        $osMediaLink = 'http://mystorageaccount.blob.core.windows.net/containername/myOSvhd.vhd'
        $dataCaching='None'
        $lun='1'
        $emptyDiskSize= 32
        # Subscription Related Properties
        $SrvMngtEndPoint='https://management.core.windows.net'
        $subscription = Get‐AzureSubscription –Current ‐ExtendedDetails
        $certificate = $subscription.Certificate
        $SubId = $subscription.SubscriptionId
        $body =
        "<VMImage xmlns="http://schemas.microsoft.com/windowsazure" xmlns:i="http://www.w3.org/2001/XMLSchema‐instance">" +
        "<Name>" + $ImageName + "</Name>" +
        "<Label>" + $Label + "</Label>" +
        "<Description>" + $Description + "</Description>" +
        "<OSDiskConfiguration>" +
        "<HostCaching>" + $osCaching + "</HostCaching>" +
        "<OSState>" + $state + "</OSState>" +
        "<OS>" + $os + "</OS>" +
        "<MediaLink>" + $osMediaLink + "</MediaLink>" +
        "</OSDiskConfiguration>" +
        "<DataDiskConfigurations>" +
        "<DataDiskConfiguration>" +
        "<HostCaching>" + $dataCaching + "</HostCaching>" +
        "<Lun>" + $lun + "</Lun>" +
        "<MediaLink>" + $dataMediaLink + "</MediaLink>" +
        "<LogicalDiskSizeInGB>" + $emptyDiskSize + "</LogicalDiskSizeInGB>" +
        "</DataDiskConfiguration>" +
        "</DataDiskConfigurations>" +
        "</VMImage>"
        $uri = $SrvMngtEndPoint + "/" + $SubId + "/" + "services/vmimages"
        $headers = @{"x‐ms‐version"="2014‐06‐01"}
        $response = Invoke‐WebRequest ‐Uri $uri ‐ContentType "application/xml" ‐Body $body ‐Certificate $certificate ‐Headers $headers ‐Method POST
        if ($response.StatusCode ‐ge 200 ‐and $response.StatusCode ‐lt 300)
        {
        echo "Accepted"
        }
        else
        {
        echo "Not Accepted"
        }

이 스크립트를 실행하여 ImageName 매개 변수에 제공된 이름 myVMImage를 사용하여 사용자 VM 이미지를 만듭니다. 이 이미지는 OS 디스크 하나와 데이터 디스크 하나로 구성됩니다.

이 API는 비동기 작업이며 202를 허용하여 응답합니다. VM 이미지가 만들어졌는지 확인하려면 작업 상태를 쿼리해야 합니다. 반환 응답에서 x-ms-request-id는 작업 ID입니다. 이 id는 아래 $opId에서 설정해야 합니다.

        $opId = #Fill In With Operation ID
        $uri2 = $SrvMngtEndPoint + "/" + $SubId + "/" + "operations" + "/" + "$opId"
        $response2 = Invoke-WebRequest -Uri $uri2 -ContentType "application/xml" Certificate $certificate -Headers $headers -Method GET

OS VHD 및 추가 빈 데이터 디스크(이 디스크에 대한 VHD를 만들지 않음)에서 VM 이미지를 만들려면 VM 이미지 만들기 API에서 다음 스크립트를 사용합니다.

        # Image Parameters to Specify
        $ImageName=’myVMImage’
        $Label='IMAGE_LABEL'
        $Description='My VM Image to Test’
        $osCaching='ReadWrite' $os = 'Windows'
        $state = 'Generalized'
        $osMediaLink =
        'http://mystorageaccount.blob.core.windows.net/containername/myOSvhd.vhd'
        $dataCaching='None'
        $lun='1'
        $emptyDiskSize= 32
        # Subscription Related Properties
        $SrvMngtEndPoint='https://management.core.windows.net'
        $subscription = Get-AzureSubscription –Current -ExtendedDetails
        $certificate = $subscription.Certificate
        $SubId = $subscription.SubscriptionId
        $body =  
        "<VMImage xmlns=`"http://schemas.microsoft.com/windowsazure`" xmlns:i=`"http://www.w3.org/2001/XMLSchema-instance`">" +
        "<Name>" + $ImageName + "</Name>" +
        "<Label>" + $Label + "</Label>" +
        "<Description>" + $Description + "</Description>" + "<OSDiskConfiguration>" + "<HostCaching>" + $osCaching + "</HostCaching>" +
        "<OSState>" + $state + "</OSState>" +
        "<OS>" + $os + "</OS>" +
        "<MediaLink>" + $osMediaLink + "</MediaLink>" +
        "</OSDiskConfiguration>" +
        "<DataDiskConfigurations>" +
        "<DataDiskConfiguration>" +
        "<HostCaching>" + $dataCaching + "</HostCaching>" +
        "<Lun>" + $lun + "</Lun>" +
        "<MediaLink>" + $dataMediaLink + "</MediaLink>" +
        "<LogicalDiskSizeInGB>" + $emptyDiskSize + "</LogicalDiskSizeInGB>" + "</DataDiskConfiguration>" +
        "</DataDiskConfigurations>" +
        "</VMImage>"
        $uri = $SrvMngtEndPoint + "/" + $SubId + "/" + "services/vmimages"
        $headers = @{"x-ms-version"="2014-06-01"}
        $response = Invoke-WebRequest -Uri $uri -ContentType "application/xml" -Body $body Certificate $certificate -Headers $headers -Method POST
        if ($response.StatusCode -ge 200 -and $response.StatusCode -lt 300)
        { echo "Accepted"
        } else
        { echo "Not Accepted"
        }

이 스크립트를 실행하여 ImageName 매개 변수에 제공된 이름 "myVMImage"를 사용하여 사용자 VM 이미지를 만듭니다. 이 이미지는 전달된 VHD를 기반으로 하는 OS 디스크 하나와 빈 32GB 데이터 디스크 하나로 구성됩니다.

### 4\.2 사용자 VM 이미지에서 VM 배포
사용자 VM 이미지에서 VM을 배포하려면 최신 [Azure 관리 포털](https://manage.windowsazure.com) 또는 PowerShell을 사용할 수 있습니다.

**최신 Azure 관리 포털에서 VM 배포**

1. **새로 만들기 > 계산 > VM > 갤러리에서**로 이동합니다.

    ![drawing][img-manage-vm-new]

2. **내 이미지**로 이동한 다음 VM을 배포할 VM 이미지를 선택합니다.
  1. '내 이미지' 보기에는 OS 이미지와 VM 이미지가 모두 나열되므로 선택한 이미지에 주의하세요.
  2. VM 이미지의 대부분은 디스크가 두 개 이상이므로 디스크 수를 조사하면 배포 중인 이미지의 형식을 결정하는 데 도움이 됩니다. 하지만 VM 이미지 중에도 OS 디스크가 하나만 존재하여 '디스크 수'가 1로 설정되는 경우가 있습니다.

    ![drawing][img-manage-vm-select]

3. VM 만들기 마법사를 따라 VM 이름, VM 크기, 위치, 사용자 이름 및 암호를 지정합니다.

**PowerShell에서 VM 배포**

대형 VM을 배포하려면 방금 만든 일반화된 VM 이미지에서 다음 cmdlet을 사용할 수 있습니다.

    $img = Get‐AzureVMImage ‐ImageName "myVMImage"
    $user = "user123"
    $pass = "adminPassword123"
    $myVM = New‐AzureVMConfig ‐Name "VMImageVM" ‐InstanceSize "Large" ‐ImageName $img.ImageName | Add‐AzureProvisioningConfig ‐Windows ‐AdminUsername $user ‐Password $pass
    New‐AzureVM ‐ServiceName "VMImageCloudService" ‐VMs $myVM ‐Location "West US" ‐WaitForBoot

## 5\. VM 이미지에 대한 인증받기
Azure Store에 대한 VM 이미지 준비 과정의 다음 단계는 인증받기입니다.

이 프로세스는 특수 인증 도구 실행, VHD가 있는 Azure 컨테이너에 확인 결과 업로드, 제품 추가, SKU 정의, 인증을 위해 VM 이미지 제출 등으로 구성됩니다.

### 5\.1 Microsoft Azure 인증 도구 다운로드 및 실행
Microsoft Azure 인증 도구는 사용자 VM 이미지에서 프로비전된 실행 중인 VM에 대해 실행되어 VM 이미지가 Microsoft Azure와 호환되는지를 확인합니다. VHD 준비에 대한 지침과 요구 사항이 충족되었는지 확인합니다. 이 도구의 출력은 호환성 보고서로, 인증을 요청하는 동안 게시 포털에 업로드해야 합니다.

인증 도구는 Windows VM과 Linux VM 모두에서 사용할 수 있습니다. PowerShell을 통해 Windows VM에 연결하고 SSH.Net을 통해 Linux VM에 연결합니다.

1. 먼저 [Microsoft 다운로드 사이트][link-msft-download]에서 인증 도구를 다운로드합니다.
2. 인증 도구를 열고 **새 테스트 시작** 단추를 클릭합니다.
3. **테스트 정보** 화면에서 테스트 실행에 대한 이름을 입력합니다.
4. Linux VM인지 Windows VM인지 여부를 선택합니다. 선택에 따라 후속 옵션을 선택합니다.

### **Linux VM 이미지에 인증 도구 연결**

1. SSH 인증 모드: 암호 또는 키 파일을 선택합니다.
2. 암호 기반 인증을 사용할 경우 DNS 이름, 사용자 이름 및 암호를 입력합니다.
3. 키 파일 인증을 사용할 경우 DNS, 사용자 이름 및 개인 키 위치를 입력합니다.

  ![Linux VM 이미지의 암호 인증][img-cert-vm-pswd-lnx]

  ![Linux VM 이미지의 키 파일 인증][img-cert-vm-key-lnx]

### **Windows VM 이미지에 인증 도구 연결**

1. 정규화된 VM DNS(Domain Name System)를 입력합니다(예: MyVMName.Cloudapp.net).
2. 사용자 이름과 암호를 입력합니다.

  ![Windows VM 이미지의 암호 인증][img-cert-vm-pswd-win]

Linux 또는 Windows VM 이미지에 대해 올바른 옵션을 선택한 경우 **연결 테스트**를 눌러서 SSH.Net 또는 PowerShell에 테스트를 위한 유효한 연결이 있는지를 확인합니다. 연결되면 **다음**을 눌러서 테스트를 시작합니다.

테스트가 완료되면 각 테스트 요소에 대한 결과(통과/실패/경고)가 표시됩니다.

![Linux VM 이미지에 대한 테스트 사례][img-cert-vm-test-lnx]

![Windows VM 이미지에 대한 테스트 사례][img-cert-vm-test-win]

테스트 중 하나라도 실패하면 이미지가 인증되지 않습니다. 요구 사항을 검토하고 필요한 변경 내용을 확인하세요.

자동화된 테스트에 따라 질문서 화면을 통해 VM 이미지에 추가 입력을 제공하라는 메시지가 표시됩니다. 질문을 작성하거나 **다음**을 누릅니다.

![인증 도구 질문서][img-cert-vm-questionnaire]

![인증 도구 질문서][img-cert-vm-questionnaire-2]

질문서를 완료하면 Linux VM 이미지에 대한 SSH 액세스 정보와 실패한 평가에 대한 설명 등의 추가 정보를 제공할 수 있습니다. 실행된 테스트 사례에 대한 테스트 결과 및 로그 파일과 질문서에 대한 답변을 다운로드할 수 있습니다. VHD와 동일한 컨테이너에 결과를 저장합니다.

![인증 테스트 결과 저장][img-cert-vm-results]

### 5\.2 VM 이미지에 대한 공유 액세스 서명 URI(Uniform Resource Identifier) 가져오기

게시 프로세스 중에 SKU에 대해 만든 각 VHD로 안내하는 URI를 지정합니다. Microsoft는 인증 프로세스 중에 이러한 VHD에 액세스해야 합니다. 따라서 개발자는 각 VHD에 대해 SAS(공유 액세스 서명) URI(Uniform Resource Identifier)를 만들어야 합니다. 이 URI를 게시 포털의 **이미지** 탭에 입력해야 합니다.

만든 SAS URI는 다음 요구 사항을 준수해야 합니다.

- VHD에 대한 SAS URI를 생성할 때 목록 사용 권한과 읽기 전용 권한이면 충분합니다. 쓰기 또는 삭제 권한을 제공하지 마세요.
- 액세스 기간은 SAS URI가 만들어진 날로부터 업무일 기준 7일 이상이어야 합니다.
- 클럭 오차로 인해 직접 오류가 발생하는 것을 방지하려면 시간을 현재 시간보다 15분 이전으로 지정합니다.

SAS URI를 만들려면 [공유 액세스 서명, 1부: SAS 모델 또는 공유 액세스 서명 이해][link-azure-1], [공유 액세스 서명, 2부: Blob 서비스를 통해 SAS 만들기 및 사용][link-azure-2]에 제공된 지침을 따를 수 있습니다.

코드를 사용하여 공유 액세스 키를 생성하는 대신, [Azure 저장소 탐색기][link-azure-codeplex]와 같은 저장소 도구를 사용할 수 있습니다.

**Azure 저장소 탐색기를 사용하여 공유 액세스 키 생성**

1. CodePlex에서 [Azure 저장소 탐색기][link-azure-codeplex] 6 이상을 다운로드합니다.
2. 설치된 경우 응용 프로그램을 엽니다.
3. **계정 추가**를 클릭합니다.

    ![drawing][img-azstg-add]

4. 저장소 계정 이름, 저장소 계정 키 및 저장소 끝점 도메인을 지정합니다. "Https 사용"을 선택하지 **마세요**.

    ![drawing][img-azstg-setup-1]

5. 위의 단계에서 Azure 저장소 탐색기가 특정 저장소 계정에 연결됩니다. 저장소 계정 내의 모든 컨테이너를 보여주는 과정이 시작됩니다. OS 디스크 VHD 파일(시나리오에 적용 가능한 경우 데이터 디스크도 해당)을 복사한 컨테이너를 선택합니다.

    ![drawing][img-azstg-setup-2]

6. Blob 컨테이너를 선택하면 Azure 저장소 탐색기 응용 프로그램에서 컨테이너 내의 파일을 보여주기 시작합니다. 제출해야 하는 이미지 파일(.vhd)를 선택합니다.

    ![drawing][img-azstg-setup-3]

7. 컨테이너의 (.vhd) 파일을 선택한 후 아래 강조 표시된 **보안** 탭을 클릭합니다.

    ![drawing][img-azstg-setup-4]

8. **보안** 탭을 클릭하여 다음 대화 상자가 나타나면 액세스 수준 탭에서 기본값을 그대로 두고 공유 액세스 서명 탭을 클릭합니다.

    ![drawing][img-azstg-setup-5]

9. 이 탭에서 아래 단계에 따라 .vhd 이미지에 대한 SAS URL을 생성합니다.

    ![drawing][img-azstg-setup-6]

    a. 액세스 허용 시작 -> UTC 시간에 대한 보호를 위해, 현재 날짜 이전으로 선택하세요. 예를 들어, 현재 날짜가 2014년 10월 6일이면 여기서 2014년 10월 5일을 선택합니다.

    b. 액세스 허용됨 -> 7 - 8일 이상 지정합니다.

    c. 작업 허용됨 -> 나열 및 읽기 권한을 모두 제공해야 합니다.

    d. .vhd 파일을 올바르게 선택한 경우 Blob 이름 아래에 확장명이 .vhd인 파일이 표시됩니다.

    e. 서명 생성을 클릭합니다.

    f. 생성된 공유 액세스 서명 URI에서 위에 강조 표시된 대로 다음을 확인합니다.

    - i Url이 https로 시작되지 않음을 확인합니다.
    - ii. 해당 이미지 파일 이름 .vhd가 URI에 있습니다.
    - iii. 서명 끝에 =rl(읽기 및 나열 액세스가 성공적으로 제공되었음을 나타냄)이 있는지 확인합니다. g. 생성된 SAS URI가 작동하는지 확인하려면 'Test in Browser(브라우저에서 테스트)'를 클릭합니다. 다운로드 프로세스가 시작됩니다.
10. SAS URI를 복사합니다. 이 URI는 게시 포털에 붙여넣을 URI입니다.
11. SKU에서 각 VHD에 대해 이 단계를 반복합니다.

### 5\.3 VM 이미지에 대한 정보를 제공하고 게시 포털에서 인증 요청
제품 및 SKU를 만든 후 해당 SKU에 연결되는 이미지 세부 정보를 입력해야 합니다.

1. [게시 포털][link-pubportal]로 이동하여 판매자 계정으로 로그인합니다.
2. **VM 이미지** 탭을 선택합니다.
3. 페이지의 맨 위에 나열된 식별자는 SKU 식별자가 아니고 실제 제품 식별자입니다.
4. SKU 섹션에 속성을 입력합니다.

    ![drawing][img-pubportal-vm-skus]

5. **운영 체제 제품군**에서 OS VHD에 연결된 운영 체제 유형을 선택합니다.
6. **운영 체제**에서 운영 체제에 대해 설명합니다. 운영 체제 제품군, 유형, 버전, 업데이트 등과 같은 형식을 고려하세요. 예를 들어 Windows Server Datacenter 2014 R2를 고려합니다.
7. 3개의 권장되는 가상 컴퓨터 크기를 선택합니다. 이는 이미지를 구입하여 배포하려는 경우에 Azure 관리 포털에서 최종 사용자의 가격 책정 계층 블레이드에 표시되는 권장 사항입니다.

  >[AZURE.NOTE]이는 유일한 권장 사항입니다. 최종 사용자는 이미지 제정된 디스크에 적용되는 VM 크기를 선택할 수 있습니다.

8. 버전을 입력합니다. 버전 필드는 제품 및 해당 업데이트를 식별하는 의미 체계 버전을 캡슐화합니다.
  -	버전은 X.Y.Z 형식이며, X, Y 및 Z는 정수여야 합니다.
  -	다른 SKU에서 이미지는 다른 주 버전과 부 버전을 가질 수 있습니다.
  -	SKU 내의 버전은 패치 버전이 증가(X.Y.Z에서 Z)하는 증분 변경이어야 합니다.
9. **OS VHD URL** 아래에서 OS VHD에 대해 만들어진 SAS URI를 입력합니다.
10. 이 SKU에 데이터 디스크가 연결되어 있는 경우 배포 시 이 데이터 디스크를 탑재할 LUN(논리 단위 번호)을 선택합니다.
11. LUN X VHD URL에서 첫 번째 데이터 VHD에 대해 만들어진 SAS URI를 입력합니다.
12.	인증 도구에서 유효성 검사/테스트 결과를 업로드합니다.
13.	**Request Certification(인증 요청)**을 클릭합니다.
14.	모든 추가 데이터 디스크 VHD에 대해 11, 12 및 13단계를 반복합니다.

    ![drawing][img-pubportal-vm-skus-2]

## 다음 단계
인증을 위한 가상 컴퓨터 이미지 SKU가 제출되면 [Azure 마켓플레이스 마케팅 콘텐츠 가이드][link-pushstaging]를 진행할 수 있습니다. 게시 프로세스의 이 단계에서는 **3단계 스테이징에서 VM 제품 테스트** 이전에 필요한 마케팅 콘텐츠, 가격 책정 및 기타 정보를 제공합니다. 여기에서 제품을 Azure 마켓플레이스에 배포하여 일반에게 공개하고 판매하기 전에 다양한 사용 사례 시나리오를 테스트합니다.

## 참고 항목
- [시작: Azure 마켓플레이스에 제품을 게시하는 방법](marketplace-publishing-getting-started.md)

[img-acom-1]: media/marketplace-publishing-vm-image-creation/vm-image-acom-datacenter.png
[img-portal-vm-size]: media/marketplace-publishing-vm-image-creation/vm-image-portal-size.png
[img-portal-vm-create]: media/marketplace-publishing-vm-image-creation/vm-image-portal-create-vm.png
[img-portal-vm-location]: media/marketplace-publishing-vm-image-creation/vm-image-portal-location.png
[img-portal-vm-rdp]: media/marketplace-publishing-vm-image-creation/vm-image-portal-rdp.png
[img-azstg-add]: media/marketplace-publishing-vm-image-creation/vm-image-storage-add.png
[img-azstg-setup-1]: media/marketplace-publishing-vm-image-creation/vm-image-storage-setup.png
[img-azstg-setup-2]: media/marketplace-publishing-vm-image-creation/vm-image-storage-setup-2.png
[img-azstg-setup-3]: media/marketplace-publishing-vm-image-creation/vm-image-storage-setup-3.png
[img-azstg-setup-4]: media/marketplace-publishing-vm-image-creation/vm-image-storage-setup-4.png
[img-azstg-setup-5]: media/marketplace-publishing-vm-image-creation/vm-image-storage-setup-5.png
[img-azstg-setup-6]: media/marketplace-publishing-vm-image-creation/vm-image-storage-setup-6.png
[img-manage-vm-new]: media/marketplace-publishing-vm-image-creation/vm-image-manage-new.png
[img-manage-vm-select]: media/marketplace-publishing-vm-image-creation/vm-image-manage-select.png
[img-cert-vm-key-lnx]: media/marketplace-publishing-vm-image-creation/vm-image-certification-keyfile-linux.png
[img-cert-vm-pswd-lnx]: media/marketplace-publishing-vm-image-creation/vm-image-certification-password-linux.png
[img-cert-vm-pswd-win]: media/marketplace-publishing-vm-image-creation/vm-image-certification-password-win.png
[img-cert-vm-test-lnx]: media/marketplace-publishing-vm-image-creation/vm-image-certification-test-sample-linux.png
[img-cert-vm-test-win]: media/marketplace-publishing-vm-image-creation/vm-image-certification-test-sample-win.png
[img-cert-vm-results]: media/marketplace-publishing-vm-image-creation/vm-image-certification-results.png
[img-cert-vm-questionnaire]: media/marketplace-publishing-vm-image-creation/vm-image-certification-questionnaire.png
[img-cert-vm-questionnaire-2]: media/marketplace-publishing-vm-image-creation/vm-image-certification-questionnaire-2.png
[img-pubportal-vm-skus]: media/marketplace-publishing-vm-image-creation/vm-image-pubportal-skus.png
[img-pubportal-vm-skus-2]: media/marketplace-publishing-vm-image-creation/vm-image-pubportal-skus-2.png

[link-pushstaging]: marketplace-publishing-push-to-staging.md
[link-github-waagent]: https://github.com/Azure/WALinuxAgent
[link-azure-codeplex]: https://azurestorageexplorer.codeplex.com/
[link-azure-2]: ../storage/storage-dotnet-shared-access-signature-part-2/
[link-azure-1]: ../storage/storage-dotnet-shared-access-signature-part-1/
[link-msft-download]: http://www.microsoft.com/download/details.aspx?id=44299
[link-technet-3]: https://technet.microsoft.com/library/hh846766.aspx
[link-technet-2]: https://msdn.microsoft.com/library/dn495261.aspx
[link-azure-portal]: https://portal.azure.com
[link-pubportal]: https://publish.windowsazure.com
[link-sql-2014-ent]: http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2014enterprisewindowsserver2012r2/
[link-sql-2014-std]: http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2014standardwindowsserver2012r2/
[link-sql-2014-web]: http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2014webwindowsserver2012r2/
[link-sql-2012-ent]: http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2enterprisewindowsserver2012/
[link-sql-2012-std]: http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2standardwindowsserver2012/
[link-sql-2012-web]: http://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2webwindowsserver2012/
[link-datactr-2012-r2]: http://azure.microsoft.com/marketplace/partners/microsoft/windowsserver2012r2datacenter/
[link-datactr-2012]: http://azure.microsoft.com/marketplace/partners/microsoft/windowsserver2012datacenter/
[link-datactr-2008-r2]: http://azure.microsoft.com/marketplace/partners/microsoft/windowsserver2008r2sp1/
[link-acct-creation]: marketplace-publishing-accounts-creation-registration.md
[link-azure-vm-1]: ../virtual-machines/virtual-machines-linux-create-upload-vhd/
[link-technet-1]: https://technet.microsoft.com/library/hh848454.aspx
[link-azure-vm-2]: ../virtual-machines/virtual-machines-linux-agent-user-guide/
[link-openssl]: https://www.openssl.org/
[link-intsvc]: http://www.microsoft.com/download/details.aspx?id=41554
[link-python]: https://www.python.org/

<!---HONumber=Oct15_HO3-->