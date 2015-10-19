<properties
	pageTitle="SAN을 사용하여 온-프레미스 VMM 사이트 간 보호 설정"
	description="Azure Site Recovery는 SAN 복제를 사용한 온-프레미스 사이트 간의 Hyper-V 가상 컴퓨터 복제, 장애 조치(Failover) 및 복구를 조정합니다."
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor="tysonn"/>

<tags
	ms.service="site-recovery"
	ms.workload="backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/21/2015"
	ms.author="raynew"/>

# SAN을 사용하여 온-프레미스 VMM 사이트 간 보호 설정

Azure Site Recovery는 가상 컴퓨터와 물리적 서버의 복제, 장애 조치(Failover) 및 복구를 오케스트레이션하여 BCDR(비즈니스 연속성 및 재해 복구) 전략에 기여합니다. [Azure Site Recovery 개요](site-recovery-overview.md)에서 가능한 배포 시나리오에 관해 읽어봅니다.

이 연습에서는 저장소 배열 기반(SAN) 복제를 사용하여 System Center VMM을 통해 관리되는 온-프레미스 Hyper-V 서버에서 실행 중인 가상 컴퓨터 작업에 대한 보호를 다른 VMM 온-프레미스 사이트에 오케스트레이션하고 자동화하기 위해 사이트 복구를 배포하는 방법에 대해 설명합니다.

비즈니스 이점은 다음과 같습니다.

- 사이트 복구에 의해 자동화된 엔터프라이즈 확장 가능한 복제 솔루션을 제공합니다.
- 파이버 채널 및 iSCSI 저장소에서 모두 엔터프라이즈 저장소 파트너를 통해 제공되는 SAN 복제 기능을 활용할 수 있습니다. [SAN 저장소 파트너](http://go.microsoft.com/fwlink/?LinkId=518669)를 참조하세요.
- 기존 SAN 인프라를 활용하여 Hyper-V 클러스터에 배포된 중요 업무용 응용프로그램을 보호합니다.
- 게스트 클러스터에 대한 지원을 제공합니다.
- 저장소 배열 기능에 따라 낮은 RTO 및 RPO에 대해 동기화된 복제와 뛰어난 유연성을 위해 비동기화된 복제를 사용하여 다른 응용프로그램 계층 간의 복제 일관성을 보장합니다.  
- VMM과의 통합은 기존 저장소를 검색하는 VMM의 VMM 콘솔 및 SMI-S 내에서 SAN 관리를 제공합니다.  


## 이 문서의 내용

이 문서에는 개요 및 배포 필수 조건이 포함되어 있습니다. VMM 및 사이트 복구 자격 증명 모음에서 복제를 구성하고 사용하는 방법을 안내합니다. VMM에서 SAN 저장소를 검색 및 분류하고 LUN을 프로비전하고 Hyper-V 클러스터에 저장소를 할당합니다. 끝으로, 장애 조치(Failover)를 테스트하여 모두 예상대로 작동하는지 확인합니다.

문제가 발생할 경우 [Azure 복구 서비스 포럼](http://go.microsoft.com/fwlink/?LinkId=313628)에 문의 사항을 게시하세요.

## 개요
이 시나리오에서는 한 온-프레미스 VMM 사이트에서 SAN 복제를 사용하는 다른 사이트로 Hyper-V 가상 컴퓨터를 백업하여 작업을 보호합니다.

![SAN 아키텍처](./media/site-recovery-vmm-san/ASRSAN_Arch.png)

### 시나리오 구성 요소

- **온-프레미스 가상 컴퓨터**—VMM 사설 클라우드에서 관리되는 온-프레미스 Hyper-V 서버에는 보호하려는 가상 컴퓨터가 포함됩니다.
- **온-프레미스 VMM 서버**—보호하려는 기본 사이트 및 보조 사이트에서 실행 중인 하나 이상의 VMM 서버를 포함할 수 있습니다.
- **SAN 저장소**—기본 사이트와 보조 사이트에 각각 하나씩 있는 SAN 배열
-  **Azure Site Recovery 자격 증명 모음**—자격 증명 모음은 온-프레미스 사이트 간의 데이터 복제본, 장애 조치(Failover) 및 복구를 조정하고 오케스트레이션합니다.
- **Azure Site Recovery 공급자**—공급자가 각 VMM 서버에 설치됩니다.

## 시작하기 전에

### Azure 필수 조건

- [Microsoft Azure](http://azure.microsoft.com/) 계정이 있어야 합니다. [무료 평가판](http://aka.ms/try-azure)으로 시작할 수 있습니다. 가격 정보는 [Azure 사이트 복구 관리자 가격 정보](http://go.microsoft.com/fwlink/?LinkId=378268)에서 확인할 수 있습니다.

### VMM 필수 구성 요소

- 각각의 온-프레미스 사이트에서 물리적 또는 가상 독립 실행형 서버나 가상 클러스터로 배포되고 [VMM 업데이트 롤업 5.0](http://go.microsoft.com/fwlink/?LinkId=517707)을 설치한 System Center 2012 R2에서 실행되는 한 개 이상의 VMM 서버가 필요합니다.
- 클라우드가 보호하려는 기본 VMM 서버에 하나 이상 있고, 보조 VMM 서버에 하나 있어야 합니다. 보호하려는 기본 클라우드는 다음을 포함해야 합니다.
	- 하나 이상의 VMM 호스트 그룹
	- 각 호스트 그룹에 있는 하나 이상의 Hyper-V 클러스터
	- 클라우드의 원본 Hyper-V 서버에 위치한 하나 이상의 가상 컴퓨터

### Hyper-V 요구 사항

- Hyper-V 호스트 클러스터가 기본 및 보조 사이트에 배포되어 있고, 최신 업데이트가 설치된 Windows Server 2012 이상을 실행하고 있어야 합니다.

### SAN 필수 조건

- SAN 복제를 사용하면 게스트 클러스터형 가상 컴퓨터를 iSCSI 또는 파이버 채널 저장소나 공유 가상 하드 디스크(vhdx)를 사용하여 복제할 수 있습니다. SAN 필수 조건은 다음과 같습니다.
	- SAN 배열을 기본 사이트와 보조 사이트에 하나씩 두 개 설정해야 합니다.
	- 배열 간에 네트워크 인프라를 설정해야 합니다. 피어링 및 복제를 구성해야 합니다. 저장소 배열 요구 사항에 따라 복제 라이선스를 설정해야 합니다.
	- 호스트가 ISCSI 또는 파이버 채널을 사용하여 저장소 LUN과 통신할 수 있도록 Hyper-V 호스트 서버와 저장소 배열 간에 네트워킹을 설정해야 합니다.
	- [지원되는 저장소 배열](http://go.microsoft.com/fwlink/?LinkId=518669) 목록을 참조하세요.
	- 저장소 배열 제조업체를 통해 제공되는 SMI-S 공급자를 설치해야 하며 SAN 배열을 공급자를 통해 관리해야 합니다. 해당 설명서에 따라 공급자를 설정합니다.
	- VMM 서버가 IP 주소 또는 FQDN을 사용하여 네트워크를 통해 액세스할 수 있는 서버에 배열용 SMI-S 공급자가 있는지 확인합니다.
	- 각 SAN 배열에 이 배포에서 사용할 수 있는 하나 이상의 저장소 풀이 있어야 합니다. 기본 사이트에서 VMM 서버에 기본 배열을 관리해야 하며 보조 VMM 서버가 보조 배열을 관리합니다.
	- 기본 사이트의 VMM 서버가 기본 배열을 관리하고 보조 VMM 서버가 보조 배열을 관리합니다.

### 네트워크 필수 조건

필요에 따라 장애 조치(Failover) 후 복제본 가상 컴퓨터가 Hyper-V 호스트 서버에 최적으로 배치되고 적절한 VM 네트워크에 연결할 수 있도록 네트워크 매핑을 구성할 수 있습니다. 다음 사항에 유의하세요.

- 네트워크 매핑을 사용하도록 설정한 경우 주 위치의 가상 컴퓨터가 네트워크에 연결되고 대상 위치의 복제본이 매핑된 네트워크에 연결됩니다.
- 네트워크 매핑을 구성하지 않는 경우에는 장애 조치(Failover) 후 가상 컴퓨터가 VM 네트워크에 연결되지 않습니다.
- VMM에서 VM 네트워크를 설정해야 합니다. 자세한 내용은 VMM에서 VM 네트워크 및 게이트웨이 구성을 읽어봅니다.
- 원본 VMM 서버에서 가상 컴퓨터가 VM 네트워크에 연결되어야 합니다. 원본 VM 네트워크는 클라우드와 연결되는 논리 네트워크에 연결되어야 합니다.


## 1단계: VMM 인프라 준비

다음과 같이 필요한 VMM 인프라 준비 방법:

1. VMM 클라우드 설정 확인
2. VMM에서 SAN 저장소 통합 및 분류
3. LUN을 만들고 저장소 할당
4. 복제 그룹 만들기
5. VM 네트워크 설정

### VMM 클라우드 설정 확인

사이트 복구는 VMM 클라우드의 Hyper-V 호스트 서버에 있는 가상 컴퓨터의 보호를 오케스트레이션합니다. 사이트 복구 배포를 시작하기 전에 해당 클라우드가 올바르게 설정되었는지 확인해야 합니다. 몇 가지 원본은 다음과 같습니다.

- [사설 클라우드의 새로운 기능](http://go.microsoft.com/fwlink/?LinkId=324952)
- Gunter Danzeisen 블로그의 [VMM 2012 및 클라우드](http://go.microsoft.com/fwlink/?LinkId=324956).
- [VMM 클라우드 패브릭 구성](https://msdn.microsoft.com/library/azure/dn883636.aspx#BKMK_Fabric)
- [VMM에서 사설 클라우드 만들기](http://go.microsoft.com/fwlink/?LinkId=324953)
- Keith Mayer 블로그의 [연습: 사설 클라우드 만들기](http://go.microsoft.com/fwlink/?LinkId=324954).

### VMM에서 SAN 저장소 통합 및 분류


다음과 같이 VMM 콘솔에 SAN 분류를 추가합니다.

1. **패브릭** 작업 영역에서 **저장소**를 클릭합니다. **홈** >** 리소스 추가** > **저장 장치**를 클릭하여 저장소 추가 마법사를 시작합니다.
2. **저장소 공급자 유형 선택** 페이지에서 **SMI-S 공급자에서 검색하고 관리하는 SAN 및 NAS 장치**를 선택합니다.

	![공급자 유형](./media/site-recovery-vmm-san/SRSAN_Providertype.png)

3. **저장소 SMI-S 공급자의 프로토콜 및 주소 지정** 페이지에서 **SMI-S CIMXML**을 선택하고 공급자에 연결하기 위한 설정을 지정합니다.
4. **공급자 IP 주소 또는 FQDN** 및 **TCP/IP 포트**에서 공급자에 연결하기 위한 설정을 지정합니다. SMI-S CIMXML에 대해서만 SSL 연결을 사용할 수 있습니다.

	![공급자 연결](./media/site-recovery-vmm-san/SRSAN_ConnectSettings.png)

5. **실행 계정**에서 공급자에 액세스할 수 있는 VMM 실행 계정을 지정하거나 새 계정을 만듭니다.
6. **정보 수집** 페이지에서 VMM은 자동으로 저장소를 검색하고 가져오려고 합니다. 검색을 다시 시도하려면 **검색 공급자**를 클릭합니다. 검색 프로세스에 성공하면 검색된 저장소 배열, 저장소 풀, 제조업체, 모델 및 용량이 페이지에 나열됩니다.

	![저장소 검색](./media/site-recovery-vmm-san/SRSAN_Discover.png)

7. **관리할 저장소 풀을 선택한 후 분류 할당**에서 VMM이 관리할 저장소 풀을 선택한 후 분류를 할당합니다. LUN 정보는 저장소 풀에서 가져옵니다. 보호해야 하는 응용 프로그램, 응용 프로그램의 용량 요구 사항 및 무엇을 함께 복제해야 하는지에 대한 요구 사항을 기준으로 LUN을 만듭니다.

	![저장소 분류](./media/site-recovery-vmm-san/SRSAN_Classify.png)

### LUN을 만들고 저장소 할당

1. SAN 저장소가 VMM에 통합된 후 LUN(논리 단위)을 만듭니다(프로비전합니다).

	- [VMM에서 논리 단위를 만드는 방법을 선택하는 방법](http://go.microsoft.com/fwlink/?LinkId=518490)
	- [VMM에서 저장소 논리 단위를 프로비전하는 방법](http://go.microsoft.com/fwlink/?LinkId=518491)

2. 그런 다음 VMM이 프로비전된 저장소에 가상 컴퓨터 데이터를 배포할 수 있도록 Hyper-V 호스트에 저장소 용량을 할당합니다.

	- 클러스터에 저장소를 할당하려면 먼저 클러스터가 있는 VMM 호스트 그룹에 저장소를 할당해야 합니다. [호스트 그룹에 저장소 논리 단위를 할당하는 방법](http://go.microsoft.com/fwlink/?LinkId=518493) 및 [호스트 그룹에 저장소 풀을 할당하는 방법](http://go.microsoft.com/fwlink/?LinkId=518492)을 참조하세요.</a>.
	- 그런 다음 [VMM에서 Hyper-V 호스트 클러스터에 저장소를 구성하는 방법](http://go.microsoft.com/fwlink/?LinkId=513017)에 설명된 대로 클러스터에 저장소 용량을 할당합니다.</a>.

### 복제 그룹 만들기

함께 복제해야 하는 LUN을 모두 포함하는 복제 그룹을 만듭니다.

1. VMM 콘솔에서 저장소 배열 속성의 **복제 그룹** 탭을 열고 **새로 만들기**를 클릭합니다.
2. 그런 다음 복제 그룹을 만듭니다.

	![SAN 복제 그룹](./media/site-recovery-vmm-san/SRSAN_RepGroup.png)

### 네트워크 설정

네트워크 매핑을 구성하려면 다음을 수행합니다.

1. [네트워크 매핑](https://msdn.microsoft.com/library/azure/dn801052.aspx)에 대해 자세히 알아봅니다.
2. VMM에서 VM 네트워크를 준비합니다.

	- [논리 네트워크 설정](http://go.microsoft.com/fwlink/?LinkId=386307)에 대해 자세히 알아봅니다. 논리 네트워크 설정—VMM 개요에서 논리 네트워킹 구성을 읽어봅니다.
	- [VM 네트워크를 설정합니다](http://go.microsoft.com/fwlink/?LinkId=386308).

## 2단계: 자격 증명 모음 만들기


1. [관리 포털](https://portal.azure.com)에 로그인합니다.


2. **데이터 서비스** > **복구 서비스**를 확장하고 **사이트 복구 자격 증명 모음**을 클릭합니다.


3. **새로 만들기** > **빠른 생성**을 클릭합니다.

4. **이름**에 자격 증명 모음을 식별하기 위한 이름을 입력합니다.

5. **지역**에서 자격 증명 모음에 대한 지리적 지역을 선택합니다. 지원되는 지역을 확인하려면 [Azure 사이트 복구 가격 정보](href="http://go.microsoft.com/fwlink/?LinkId=389880)에서 지리적 가용성을 참조하십시오.

6. **자격 증명 모음 만들기**를 클릭합니다.

	![새 자격 증명 모음](./media/site-recovery-vmm-san/SRSAN_HvVault.png)

상태 표시줄을 점검하여 자격 증명 모음이 성공적으로 만들어졌는지 확인합니다. 자격 증명 모음은 기본 **복구 서비스** 페이지에서 **활성**으로 나열됩니다.


### VMM 서버 등록

1. <b>복구 서비스</b> 페이지에서 자격 증명 모음을 클릭하여 퀵스타트 페이지를 엽니다. 빠른 시작은 언제든지 아이콘을 사용하여 열 수도 있습니다.

	![빠른 시작 아이콘](./media/site-recovery-vmm-san/SRSAN_QuickStartIcon.png)

2. 드롭다운 목록에서 **배열 복제를 사용하여 Hyper-V 온-프레미스 사이트 간**을 선택합니다.

	![등록 키](./media/site-recovery-vmm-san/SRSAN_QuickStartRegKey.png)

4. *빠른 시작* 페이지의 **VMM 서버 준비**에서 *VMM 서버에 설치할 Microsoft Azure 사이트 복구 공급자 다운로드*를 클릭하여 최신 버전의 공급자 설치 파일을 받습니다.

2. 원본 VMM 서버에서 이 파일을 실행합니다. VMM이 클러스터에 배포되고 공급자를 처음 설치하는 경우 활성 노드에 설치하고 설치를 완료하여 VMM 서버를 자격 증명 모음에 등록합니다. 그런 후에 다른 노드에 공급자를 설치합니다. 공급자를 업그레이드하는 경우 모두 동일한 공급자 버전을 실행해야 하므로 모든 노드에서 업그레이드해야 합니다.


3. 설치 관리자는 몇 가지 **사전 요구 사항 확인**을 수행하고 공급자 설정을 시작하기 위해 VMM 서비스를 중지하는 권한을 요청합니다. 설정이 완료되면 VMM 서비스가 자동으로 다시 시작됩니다. VMM 클러스터에 설치하는 경우 클러스터 역할을 중지하라는 메시지가 표시됩니다.

4. **Microsoft 업데이트**에서 업데이트를 선택할 수 있습니다. 이 설정이 사용되면 공급자가 Microsoft 업데이트 정책에 따라 설치됩니다.

	![Microsoft 업데이트](./media/site-recovery-vmm-san/VMMASRInstallMUScreen.png)


1.  설치 위치는 **<SystemDrive>\\Program Files\\Microsoft System Center 2012 R2\\Virtual Machine Manager\\bin**으로 설정됩니다. 공급자 설치를 시작하려면 설치 단추를 클릭합니다. ![InstallLocation](./media/site-recovery-vmm-san/VMMASRInstallLocationScreen.png)



1. 공급자가 설치된 후 '등록' 단추를 클릭하여 자격 증명 모음에 서버를 등록합니다. ![InstallComplete](./media/site-recovery-vmm-san/VMMASRInstallComplete.png)

5. VMM 서버에서 실행 중인 공급자를 인터넷에 연결하는 방법을 **인터넷 연결**에서 지정합니다. 서버에 구성되어 있는 기본 인터넷 연결 설정을 사용하려면 *기본 시스템 프록시 설정 사용*을 선택합니다.

	![인터넷 설정](./media/site-recovery-vmm-san/VMMASRRegisterProxyDetailsScreen.png) - 사용자 지정 프록시를 사용하려는 경우 공급자를 설치하기 전에 설정해야 합니다. 사용자 지정 프록시 설정을 구성하는 경우 프록시 연결 상태를 확인하기 위한 테스트가 실행됩니다. - 사용자 지정 프록시를 사용하거나 기본 프록시에 인증이 필요할 경우 프록시 주소와 포트를 포함한 프록시 상세 정보를 입력해야 합니다. - 다음 URL은 VMM 서버 및 Hyper-v 호스트에서 액세스할 수 있어야 합니다. - *.hypervrecoverymanager.windowsazure.com - *.accesscontrol.windows.net - *.backup.windowsazure.com - *.blob.core.windows.net - *.store.core.windows.net - Allow the IP addresses described in [Azure 데이터 센터 IP 범위](http://go.microsoft.com/fwlink/?LinkId=511094) 및 HTTPS(443) 프로토콜에 설명된 IP 주소를 허용하십시오. 사용하려는 Azure 지역 및 미국 서부의 IP 범위를 허용해야 합니다.

	- 사용자 지정 프록시를 사용하는 경우 지정된 프록시 자격 증명을 사용하여 VMM 실행 계정(DRAProxyAccount)이 자동으로 만들어집니다. 이 계정이 성공적으로 인증될 수 있도록 프록시 서버를 구성합니다. VMM 콘솔에서 VMM 실행 계정 설정을 수정할 수 있습니다. 이렇게 하려면 설정 작업 영역을 열고 보안을 확장한 다음 실행 계정을 클릭하고 DRAProxyAccount의 암호를 수정합니다. 이 설정이 적용되도록 VMM 서비스를 다시 시작해야 합니다.

6. **등록 키**에서 Azure 사이트 복구를 다운로드하고 VMM 서버에 복사했다는 것을 선택합니다.
7. **자격 증명 모음 이름**에서 서버를 등록할 자격 증명 모음의 이름을 확인합니다. *다음*을 클릭합니다.


	![서버 등록](./media/site-recovery-vmm-san/VMMASRRegisterVaultCreds.png)

9. 이 설정은 VMM-Azure 시나리오에 에 대해서만 사용되고, VMM-VMM 전용 사용자인 경우 이 화면을 무시할 수 있습니다.

	![서버 등록](./media/site-recovery-vmm-san/VMMASRRegisterEncryptionScreen.png)

8. 자격 증명 모음에서 VMM 서버를 식별하기 위한 이름을 **서버 이름**에서 지정합니다. 클러스터 구성에서 VMM 클러스터 역할 이름을 지정합니다.

8. **초기 클라우드 메타데이터** 동기화에서 VMM 서버에 있는 모든 클라우드의 메타데이터를 자격 증명 모음과 동기화할 것인지 여부를 선택합니다. 이 작업은 각 서버에서 한 번만 수행해야 합니다. 모든 클라우드를 동기화하지 않는 경우 이 설정을 선택 취소된 상태로 두고 VMM 콘솔의 클라우드 속성에서 각 클라우드를 개별적으로 동기화할 수 있습니다. ![서버 등록](./media/site-recovery-vmm-san/VMMASRRegisterFriendlyName.png)


8. *다음*을 클릭하여 프로세스를 완료합니다. 등록 후에 VMM 서버의 메타데이터가 Azure Site Recovery에 의해 검색됩니다. 서버가 자격 증명 모음의 **서버** 페이지에서 *VMM 서버* 탭에 표시됩니다.

>[AZURE.NOTE]다음 명령줄을 사용하여 Azure Site Recovery 공급자를 설치할 수도 있습니다. 이 방법은 Windows Server 2012 R2용 Server CORE에 대한 공급자를 설치하는 데 사용할 수 있습니다.
>
>1. 공급자 설치 파일 및 등록 키를 폴더(예: C:\\ASR)로 다운로드합니다.
>2. System Center Virtual Machine Manager 서비스 중지
>3. **관리자** 권한으로 명령 프롬프트에서 아래 명령을 실행하여 공급자 설치 관리자 추출
>
    	C:\Windows\System32> CD C:\ASR
    	C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q
>4. 다음 명령을 사용하여 공급자 설치
>
		C:\ASR> setupdr.exe /i
>5. 다음 명령을 실행하여 공급자 등록
>
    	CD C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin
    	C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file> /EncryptionEnabled <full file name to save the encryption certificate>         
 ####명령줄 설치 매개 변수 목록####
>
 - **/Credentials** : 등록 키 파일이 있는 위치를 지정하는 필수 매개 변수입니다.  
 - **/FriendlyName** : Azure Site Recovery 포털에 나타나는 Hyper-V 호스트 서버의 이름에 대한 필수 매개 변수입니다.
 - **/EncryptionEnabled** : Azure에서 미사용 중인 가상 컴퓨터의 암호화해야 하는 경우 VMM-Azure 시나리오에서만 사용해야 하는 선택적 매개 변수입니다. 제공한 파일의 이름에 **.pfx** 확장자가 있는지를 확인하세요.
 - **/proxyAddress** : 프록시 서버의 주소를 지정하는 선택적 매개 변수입니다.
 - **/proxyport** : 프록시 서버의 포트를 지정하는 선택적 매개 변수입니다.
 - **/proxyUsername** : (프록시가 인증을 필요로 하는 경우) 프록시 사용자 이름을 지정하는 선택적 매개 변수입니다.
 - **/proxyPassword** : (프록시가 인증을 필요로 하는 경우) 프록시 서버를 인증하기 위한 암호를 지정하는 선택적 매개 변수입니다.


## 4단계: 저장소 배열과 풀 매핑

배열을 매핑하여 기본 풀에서 복제 데이터를 수신하는 보조 저장소 풀을 지정합니다. 매핑 정보는 복제 그룹에 대한 보호를 사용하도록 설정할 때 사용되므로 클라우드 보호를 구성하기 전에 저장소를 매핑해야 합니다.

시작하기 전에 자격 증명 모음에서 클라우드가 표시되는지 확인합니다. 공급자를 설치할 때 모든 클라우드를 동기화하도록 선택하거나 VMM 콘솔에서 클라우드 속성의 **일반** 탭에서 특정 클라우드를 동기화하도록 선택하면 클라우드가 검색됩니다. 그런 다음 다음과 같이 저장소 배열을 매핑합니다.

1. **리소스** > **서버 저장소** > **원본 및 대상 배열 매핑**을 클릭합니다. ![서버 등록](./media/site-recovery-vmm-san/SRSAN_StorageMap.png)
2. 기본 사이트의 저장소 배열을 선택하고 보조 사이트의 저장소 배열에 매핑합니다.
3.  배열 내의 원본 및 대상 저장소 풀을 매핑합니다. 이렇게 하려면 **저장소 풀**에서 매핑할 원본 및 대상 저장소 풀을 선택합니다.

	![서버 등록](./media/site-recovery-vmm-san/SRSAN_StorageMapPool.png)

## 5단계: 클라우드 보호 설정 구성

VMM 서버가 등록되면 클라우드 보호 설정을 구성할 수 있습니다. 공급자를 설치할 때 **클라우드 데이터를 자격 증명 모음과 동기화** 옵션을 설정했으므로 VMM 서버에 있는 모든 클라우드가 자격 증명 모음에서 <b>보호되는 항목</b> 탭에 표시됩니다.

![게시된 클라우드](./media/site-recovery-vmm-san/SRSAN_CloudsList.png)

1. 빠른 시작 페이지에서 **VMM 클라우드에 대해 보호 설정**을 클릭합니다.
2. **보호되는 항목** 탭에서 구성할 클라우드를 선택하고 **구성** 탭으로 이동합니다. 다음 사항에 유의하세요.
3. <b>대상</b>에서 <b>VMM</b>을 선택합니다.
4. <b>대상 위치</b>에서 복구에 사용할 클라우드를 관리하는 온사이트 VMM 서버를 선택합니다.
5. <b>대상 클라우드</b>에서 원본 클라우드의 가상 컴퓨터 장애 조치(Failover)에 사용할 대상 클라우드를 선택합니다. 다음 사항에 유의하세요.
	- 보호할 가상 컴퓨터의 복구 요구 사항을 충족하는 대상 클라우드를 선택하는 것이 좋습니다.
	- 클라우드는 단일 클라우드 쌍에 기본 클라우드 또는 대상 클라우드로만 속할 수 있습니다.
6. Azure Site Recovery는 클라우드가 SAN 복제 지원 저장소에 액세스할 수 있는지, 스토리지 배열이 피어 관계가 있는지 확인합니다. 참여하는 배열 피어가 표시됩니다.
7. 확인에 성공하면 **복제 유형**에서 **SAN**을 선택합니다.

<p>설정을 저장하고 나면 작업이 생성되고 <b>작업</b> 탭에서 모니터링할 수 있습니다. 클라우드 설정은 <b>구성</b> 탭에서 수정할 수 있습니다. 대상 위치 또는 대상 클라우드를 수정하려면 클라우드 구성을 제거한 후 클라우드를 다시 구성해야 합니다.</p>

## 6단계: 네트워크 매핑 사용

1. 퀵스타트 페이지에서 **네트워크 매핑**을 클릭합니다.
2. 네트워크를 매핑할 원본 VMM 서버를 선택한 다음 네트워크를 매핑할 대상 VMM 서버를 선택합니다. 원본 네트워크 및 연결된 대상 네트워크의 목록이 표시됩니다. 현재 매핑되지 않은 네트워크에 대해서는 빈 값이 표시됩니다. 원본 및 대상 네트워크 이름 옆에 있는 정보 아이콘을 클릭하여 각 네트워크의 서브넷을 확인합니다.
3. **원본 네트워크**에서 네트워크를 선택하고 **매핑**을 클릭합니다. 서비스에서 대상 서버의 VM 네트워크를 감지하고 표시합니다.

	![SAN 아키텍처](./media/site-recovery-vmm-san/ASRSAN_NetworkMap1.png)

4. 표시되는 대화 상자에서 대상 VMM 서버의 VM 네트워크 중 하나를 선택합니다.

	![SAN 아키텍처](./media/site-recovery-vmm-san/ASRSAN_NetworkMap2.png)

5. 대상 네트워크를 선택하면 원본 네트워크를 사용하는 보호된 클라우드가 표시됩니다. 보호에 사용되는 클라우드와 연결된 사용 가능한 대상 네트워크도 표시됩니다. 보호에 사용 중인 모든 클라우드에 사용할 수 있는 대상 네트워크를 선택하는 것이 좋습니다.
6.  확인 표시를 클릭하여 매핑 프로세스를 완료합니다. 매핑 프로세스를 추적하는 작업이 시작됩니다. **작업** 탭에서 작업을 확인할 수 있습니다.


## 7단계: 복제 그룹에 대해 복제 사용</h3>

가상 컴퓨터에 대해 보호를 사용하도록 설정하려면 먼저 저장소 복제 그룹에 대해 복제를 사용하도록 설정해야 합니다.

1. Azure Site Recovery 포털에 있는 기본 클라우드의 속성 페이지에서 **가상 컴퓨터** 탭을 엽니다. **복제 그룹 추가**를 클릭합니다.
2. 클라우드와 연결된 VMM 복제 그룹을 하나 이상 선택하고 원본 및 대상 배열을 확인하고 복제 빈도를 지정합니다.

이 작업이 완료되면 Azure Site Recovery에서는 VMM 및 SMI-S 공급자와 함께 대상 사이트 저장소 LUN을 프로비전하고 저장소 복제를 사용하도록 설정합니다. 복제 그룹이 이미 복제된 경우 Azure Site Recovery에서는 기존 복제 관계를 다시 사용하고 Azure Site Recovery의 정보를 업데이트합니다.

## 가상 컴퓨터의 보호 활성화

저장소 그룹이 복제되면 다음 방법 중 하나를 사용하여 VMM 콘솔에서 가상 컴퓨터에 대한 보호를 사용하도록 설정합니다.

- **새 가상 컴퓨터**—VMM 콘솔에서 새 가상 컴퓨터를 만들 때 Azure Site Recovery 보호를 사용하도록 설정하고 가상 컴퓨터를 복제 그룹과 연결합니다. 이 옵션을 사용하면 VMM은 지능형 배치를 사용하여 가상 컴퓨터 저장소를 복제 그룹의 LUN에 최적으로 배치합니다. Azure Site Recovery에서는 보조 사이트의 섀도 가상 컴퓨터 생성을 오케스트레이션하고 장애 조치(Failover) 이후 복제본 가상 컴퓨터를 시작할 수 있도록 용량을 할당합니다.
- **기존 가상 컴퓨터**—가상 컴퓨터가 VMM에 이미 배포된 경우 Azure Site Recovery 보호를 사용하도록 설정하고 복제 그룹으로 저장소를 마이그레이션할 수 있습니다. 완료되면 VMM과 Azure Site Recovery에서는 새 가상 컴퓨터를 검색하고 보호를 위해 Azure Site Recovery에서 가상 컴퓨터를 관리하기 시작합니다. 섀도 가상 컴퓨터는 보조 사이트에서 만들어지며 장애 조치(Failover) 이후 복제본 가상 컴퓨터가 시작될 수 있도록 용량이 할당됩니다.


	![보호 사용](./media/site-recovery-vmm-san/SRSAN_EnableProtection.png)


<P>보호를 사용하도록 설정한 가상 컴퓨터는 Azure Site Recovery 콘솔에 표시됩니다. 가상 컴퓨터 속성을 보고, 상태를 추적하고, 여러 가상 컴퓨터를 포함하는 복제 그룹을 장애 조치(Failover)할 수 있습니다. SAN 복제에서 복제 그룹과 연결된 모든 가상 컴퓨터는 함께 장애 조치(Failover)해야 합니다. 이는 장애 조치(Failover)가 먼저 저장소 계층에서 수행되기 때문입니다. 복제 그룹을 제대로 그룹화하고 연결된 가상 컴퓨터만 함께 배치하는 것이 중요합니다.</P>

**작업** 탭에서 초기 복제를 비롯하여 보호 사용 작업의 진행 상태를 추적합니다. 보호 완료 작업이 실행된 후에는 가상 컴퓨터가 장애 조치(Failover)를 수행할 준비가 되어 있습니다. ![가상 컴퓨터 보호 작업](./media/site-recovery-vmm-san/SRSAN_JobPropertiesTab.png)

## 8단계: 배포 테스트</h3>

배포를 테스트하여 가상 컴퓨터 및 데이터가 예상대로 장애 조치(Failover)되는지 확인합니다. 이렇게 하려면 복제 그룹을 선택하여 복구 계획을 만든 다음 계획에 대해 테스트 장애 조치(Failover)를 실행합니다.

1. **복구 계획** 탭에서 **복구 계획 만들기**를 클릭합니다.
2. 복구 계획 이름, 원본 및 대상 VMM 서버를 지정합니다. 원본 서버에 장애 조치(Failover) 및 복구를 사용하도록 설정한 가상 컴퓨터가 있어야 합니다. **SAN**을 선택하여 SAN 복제가 구성된 클라우드만 표시합니다. 3. ![복구 계획 만들기](./media/site-recovery-vmm-san/SRSAN_RPlan.png)

4. **가상 컴퓨터 선택**에서 복제 그룹을 선택합니다. 복제 그룹과 연관된 모든 가상 컴퓨터가 선택되고 복구 계획에 추가됩니다. 이러한 가상 컴퓨터는 복구 계획 기본 그룹인 그룹 1에 추가됩니다. 필요한 경우 그룹을 추가할 수 있습니다. 복제 후 가상 컴퓨터가 복구 계획 그룹의 순서에 따라 시작됩니다.

	![가상 컴퓨터 추가](./media/site-recovery-vmm-san/SRSAN_RPlanVM.png)
5. 만든 복구 계획은 **복구 계획** 탭의 목록에 표시됩니다.
6. **복구 계획** 탭에서 계획을 선택하고 **테스트 장애 조치**를 클릭합니다.
7. **테스트 장애 조치(Failover) 확인** 페이지에서 **없음**을 선택합니다. 이 옵션을 사용하도록 설정하면 장애 조치(Failover)된 복제본 가상 컴퓨터가 네트워크에 연결되지 않습니다. 여기서는 가상 컴퓨터가 올바로 장애 조치(Failover)되는지 테스트하지만 복제 네트워크 환경을 테스트하지는 않습니다. 보다 포괄적인 테스트 장애 조치(Failover)를 실행하려는 경우 <a href="http://go.microsoft.com/fwlink/?LinkId=522291">MSDN에서 온-프레미스 배포 테스트</a>를 참조하세요.

	![테스트 네트워크 선택](./media/site-recovery-vmm-san/SRSAN_TestFailover1.png)


8. 테스트 가상 컴퓨터는 복제본 가상 컴퓨터가 있는 호스트와 동일한 호스트에 생성됩니다. 복제본 가상 컴퓨터가 있는 클라우드에는 추가되지 않습니다.
9. 복제 후 복제본 가상 컴퓨터의 IP 주소는 주 가상 컴퓨터의 IP 주소와 같지 않게 됩니다. DHCP에서 주소를 발급하는 경우 주소가 자동으로 업데이트됩니다. DHCP를 실행하고 있지 않은 경우 주소가 동일한지 확인하려면 몇 가지 스크립트를 실행해야 합니다.
10. 다음 샘플 스크립트를 실행하여 IP 주소를 검색합니다.

    	$vm = Get-SCVirtualMachine -Name <VM_NAME>
		$na = $vm[0].VirtualNetworkAdapters>
		$ip = Get-SCIPAddress -GrantToObjectID $na[0].id
		$ip.address  

11. 다음 샘플 스크립트에서 이전 샘플 스크립트를 사용하여 검색한 IP 주소를 지정하고 실행하여 DNS를 업데이트합니다.

		[string]$Zone,
		[string]$name,
		[string]$IP
		)
		$Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
		$newrecord = $record.clone()
		$newrecord.RecordData[0].IPv4Address  =  $IP
		Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord

## 작업 모니터

**작업** 및 **대시보드** 탭을 사용하여 Azure 사이트 복구 자격 증명 모음에서 수행한 주 작업을 보고 모니터링할 수 있습니다. 이러한 작업에는 클라우드에 대한 보호 구성, 가상 컴퓨터의 보호 설정 및 해제, 장애 조치(Failover) 실행(계획됨, 계획되지 않음 또는 테스트), 계획되지 않은 장애 조치(Failover) 커밋이 포함됩니다.

**작업** 탭에서 작업을 보고, 작업 세부 정보 및 오류를 드릴다운하고, 쿼리 작업을 실행하여 특정 조건과 일치하는 작업을 검색하고, 작업을 Excel로 내보내고, 실패한 작업을 다시 시작합니다.

**대시보드**에서는 최신 버전의 공급자 및 에이전트 설치 파일을 다운로드하고, 자격 증명 모음에 대한 구성 정보를 가져오고, 자격 증명 모음에서 보호를 관리하는 가상 컴퓨터의 수를 확인하고, 최근 작업을 보고, 자격 증명 모음 인증서를 관리하고, 가상 컴퓨터를 재동기화할 수 있습니다.

작업 및 대시보드와의 상호 작용에 대한 자세한 내용은 [작업 및 모니터링](http://go.microsoft.com/fwlink/?LinkId=398534)을 참조하세요.

<!---HONumber=Oct15_HO2-->