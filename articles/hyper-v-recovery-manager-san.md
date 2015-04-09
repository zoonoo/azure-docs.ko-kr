<properties 
	pageTitle="자습서: SAN을 사용하여 온-프레미스 VMM 사이트 간 보호 설정" 
	description="Azure Site Recovery는 SAN 복제를 사용한 온-프레미스 사이트 간의 Hyper-V 가상 컴퓨터 복제, 장애 조치(Failover) 및 복구를 조정합니다." 
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
	ms.date="02/18/2015" 
	ms.author="raynew"/>

# 자습서: SAN을 사용하여 온-프레미스 VMM 사이트 간 보호 설정



<h2><a id="overview" name="overview" href="#overview"></a>개요</h2>
<p>Azure Site Recovery는 여러 배포 시나리오에서 가상 컴퓨터의 복제, 장애 조치(Failover) 및 복구를 오케스트레이션하여 비즈니스 및 작업 연속성 전략에 기여합니다.<p>

<P>이 자습서에서는 온-프레미스 사이트와 다른 VMM 온-프레미스 사이트 사이에서 System Center VMM을 통해 관리되는 Hyper-V 서버에 실행 중인 작업에 대한 보호를 저장소 배열 기반(SAN) 복제를 사용하여 오케스트레이션하고 자동화하기 위해 Azure Site Recovery를 배포하는 방법에 대해 설명합니다. 이 자습서에서는 가능한 경우 가장 빠른 배포 경로와 기본 설정을 사용합니다. 이 시나리오에서는 다음을 수행합니다.</P>

<ul>
	<li>기존 SAN 인프라를 활용하여 Hyper-V 클러스터에 배포된 중요 업무용 응용 프로그램을 보호합니다.</li>
	<li>SAN 복제는 게스트 클러스터를 지원하고 스토리지 배열 기능에 따라 동기화된 복제와 비동기화된 복제를 사용하여 다른 응용 프로그램 계층 간의 복제 일관성을 보장합니다.</li>
	<li>VMM 및 Azure Site Recovery 자격 증명 모음에서 복제를 구성하고 사용하도록 설정합니다. VMM에서 SAN 저장소를 검색 및 분류하고 LUN을 프로비전하고 Hyper-V 클러스터에 저장소를 할당합니다. Azure Site Recovery는 복제를 자동화하고 장애 조치(Failover)를 오케스트레이션합니다. </li>
	</ul>

<UL>
<LI>전체 배포 지침은 <a href="http://go.microsoft.com/fwlink/?LinkId=321294">계획</a> 및 <a href="http://go.microsoft.com/fwlink/?LinkId=519251">배포</a> 가이드에서 확인할 수 있습니다.</LI>
<LI>추가 Azure Site Recovery 배포 시나리오에 대한 자세한 내용은 <a href="http://go.microsoft.com/fwlink/?LinkId=518690">Azure Site Recovery 개요</a>에서 확인할 수 있습니다.</LI>
<LI>이 자습서를 수행하는 동안 문제가 발생하면 위키 문서 <a href="http://go.microsoft.com/fwlink/?LinkId=389879">Azure Site Recovery: 일반 오류 시나리오 및 해결 방법</a>을 살펴보거나 <a href="http://go.microsoft.com/fwlink/?LinkId=313628">Azure 복구 서비스 포럼</a>(영문)에 문의 사항을 게시하세요.</LI>
</UL>




<h2><a id="before" name="before" href="#before"></a>이전</h2>
<div class="dev-callout"> 
<P>연습을 시작하기 전에 모든 요소가 준비되었는지 확인하세요.</P>

<UL>
<LI><b>Azure 계정</b> - Azure 계정이 필요합니다. 없는 경우에는 <a href="http://aka.ms/try-azure">Azure 무료 평가판</a>을 참조하세요. 가격 정보는 <a href="http://go.microsoft.com/fwlink/?LinkId=378268">Azure Site Recovery 관리자 가격 정보</a>에서 확인할 수 있습니다.</LI>
<LI>Azure Site Recovery 작동 동안 어떤 정보가 수집, 처리 또는 전송되는지 알고 싶으면 MSDN에서 <a href="http://go.microsoft.com/fwlink/?LinkId=513016">개인 정보 요구 사항</a>을 참조하세요.</LI>
<LI><b>VMM 서버</b> - 각 온-프레미스 사이트에서 물리적 또는 가상 독립 실행형 서버나 가상 클러스터로 배포되고 <a href="http://go.microsoft.com/fwlink/?LinkId=517707">VMM 업데이트 롤업 5.0 미리 보기</a>를 설치한 System Center 2012 R2에서 실행되는 VMM 서버가 필요합니다.</LI>
<LI>Hyper-V 호스트 클러스터가 기본 및 보조 사이트에 배포되어 있고, 최신 업데이트가 설치된 Windows Server 2012 이상을 실행하고 있어야 합니다.</LI>
<LI><b>VMM 클라우드</b> - 클라우드가 보호하려는 기본 VMM 서버에 하나 이상 있고, 보조 VMM 서버에 하나 있어야 합니다. 보호하려는 기본 클라우드는 다음을 포함해야 합니다.<UL>
	<LI>하나 이상의 VMM 호스트 그룹</LI>
	<LI>각 호스트 그룹에 있는 하나 이상의 Hyper-V 클러스터</LI>
	<li>클라우드의 원본 Hyper-V 서버에 위치한 하나 이상의 가상 컴퓨터</li>
		</UL>VMM 클라우드 설정에 대한 자세한 내용은 계획 가이드의 <a href="http://go.microsoft.com/fwlink/?LinkId=513015">VMM 인프라 계획</a>을 참조하세요.</LI>
<LI><b>SAN 저장소</b> - SAN 복제를 사용하면 게스트 클러스터형 가상 컴퓨터를 iSCSI 또는 파이버 채널 저장소나 공유 가상 하드 디스크(vhdx)를 사용하여 복제할 수 있습니다. SAN 필수 조건은 다음과 같습니다.</LI>
	<UL>
	<LI>SAN 배열을 기본 사이트와 보조 사이트에 하나씩 두 개 설정해야 합니다.</LI>
	<LI>배열 간에 네트워크 인프라를 설정해야 합니다. 피어링 및 복제를 구성해야 합니다. 저장소 배열 요구 사항에 따라 복제 라이선스를 설정해야 합니다.</LI>
	<LI>호스트가 ISCSI 또는 파이버 채널을 사용하여 저장소 LUN과 통신할 수 있도록 Hyper-V 호스트 서버와 저장소 배열 간에 네트워킹을 설정해야 합니다.</LI>
	<LI>지원되는 저장소 배열 목록은 <a href="http://go.microsoft.com/fwlink/?LinkId=518669">VMM 및 SAN을 사용하여 Azure Site Recovery 배포 - 지원되는 저장소 배열</a>을 참조하세요.</LI>
	
	<LI>EMC 및 NetApp에서 제공하는 SMI-S 공급자를 설치해야 하며, SAN 배열을 공급자를 통해 관리해야 합니다. 공급자를 해당 설명서에 따라 설치합니다.</LI>
	<LI>VMM 서버가 IP 주소 또는 FQDN을 사용하여 네트워크를 통해 액세스할 수 있는 서버에 배열용 SMI-S 공급자가 있는지 확인합니다.</LI>
	<LI>각 SAN 배열에 이 배포에서 사용할 수 있는 저장소 풀이 하나 이상 있어야 합니다.</LI>
	<LI>기본 사이트의 VMM 서버가 기본 배열을 관리하고 보조 VMM 서버가 보조 배열을 관리합니다.</LI>
	</UL>
<LI><b>네트워크</b> - 필요에 따라 장애 조치(Failover) 후 복제본 가상 컴퓨터가 Hyper-V 호스트 서버에 최적으로 배치되고 적절한 VM 네트워크에 연결할 수 있도록 네트워크 매핑을 구성할 수 있습니다. 네트워크 매핑을 사용하도록 설정한 경우 주 위치의 가상 컴퓨터가 네트워크에 연결되고 대상 위치의 복제본이 매핑된 네트워크에 연결됩니다. 네트워크 매핑을 구성하지 않는 경우에는 장애 조치(Failover) 후 가상 컴퓨터가 VM 네트워크에 연결되지 않습니다. 이 자습서에서는 가장 단순한 연습 설정에 대해 설명하므로 네트워크 매핑을 포함하지 않지만 다음에서 자세한 내용을 확인할 수 있습니다.</LI>
	<UL>
	<LI>계획 가이드의 <a href="http://go.microsoft.com/fwlink/?LinkId=522289">네트워크 매핑</a></LI>
	<LI>SAN 배포 가이드의 <a href="http://go.microsoft.com/fwlink/?LinkId=522290">네트워크 매핑 사용</a></LI>
	</UL>
</UL>
</div>

<h2><a id="tutorial" name="tutorial" href="#tutorial"></a>학습 단계</h2>
필수 구성 요소를 확인한 후 다음을 수행합니다.

1. <a href="#VMM">1단계: VMM 인프라 준비</a>
	- VMM에서 SAN 저장소 통합 및 분류
	- 논리 단위(LUN) 및 저장소 풀을 프로비전하고 Hyper-V 클러스터에 저장소 할당
	- 함께 복제해야 하는 LUN에 대한 복제 그룹 만들기 
2. <a href="#vault">2단계: 자격 증명 모음 만들기</a>
3. <a href="#download">3단계: VMM 서버에서 공급자 설치</a>- 자격 증명 모음에서 등록 키를 생성하고 공급자 설치 파일을 다운로드합니다. VMM 서버에서 설치 프로그램을 실행하여 공급자를 설치하고 서버를 자격 증명 모음에 등록합니다.
4. <a href="#storage">4단계: 저장소 배열과 풀 매핑</a>- 배열을 매핑하여 기본 풀에서 복제 데이터를 수신하는 보조 저장소 풀을 지정합니다. 매핑 정보는 복제 그룹에 대한 보호를 사용하도록 설정할 때 사용되므로 클라우드 보호를 구성하기 전에 저장소를 매핑합니다.
5. <a href="#clouds">5단계: 클라우드 보호 구성</a>- VMM 클라우드에 대한 보호 설정을 구성합니다.
7. <a href="#replication">6단계: 복제 그룹 사용</a>- 가상 컴퓨터에 대한 보호를 사용하도록 설정하려면 먼저 저장소 복제 그룹에 대한 복제를 사용하도록 설정해야 합니다.
8. <a href="#enablevirtual">7단계: 가상 컴퓨터의 보호 활성화</a>- 복제 그룹이 복제된 후 가상 컴퓨터에 대한 보호를 사용하도록 설정합니다.
9. <a href="#recovery plans">8단계: 배포 테스트</a>- 테스트 장애 조치(Failover)를 실행하여 가상 컴퓨터 및 데이터가 올바로 장애 조치(Failover)되는지 테스트할 수 있습니다. 


<a name="VMM"></a> <h3>1단계: VMM 인프라 준비</h3>

<a name="SAN"></a> <h4>VMM에서 SAN 저장소 통합 및 분류</h4>


1. **패브릭** 작업 영역에서 **저장소**를 클릭합니다. **홈** > **리소스 추가** > **저장 장치**를 클릭하여 저장 장치 추가 마법사를 시작합니다.
2. **저장소 공급자 유형 선택** 페이지에서 **SMI-S 공급자에서 검색하고 관리하는 SAN 및 NAS 장치**를 선택합니다.

	![Provider type](./media/hyper-v-recovery-manager-configure-vault/SRSAN_Providertype.png)

3. **저장소 SMI-S 공급자의 프로토콜 및 주소 지정** 페이지에서 **SMI-S CIMXML**을 선택하고 공급자에 연결하기 위한 설정을 지정합니다.
4. **공급자 IP 주소 또는 FQDN** 및 **TCP/IP 포트**에서 공급자에 연결하기 위한 설정을 지정합니다. SMI-S CIMXML에 대해서만 SSL 연결을 사용할 수 있습니다.

	![Provider connect](./media/hyper-v-recovery-manager-configure-vault/SRSAN_ConnectSettings.png)

5. **실행 계정**에서 공급자에 액세스할 수 있는 VMM 실행 계정을 지정하거나 새 계정을 만듭니다.
6. **정보 수집** 페이지에서 VMM은 자동으로 저장 장치 정보를 검색하고 가져오려고 합니다. 검색을 다시 시도하려면 **Scan Provider(검색 공급자)**를 클릭합니다. 검색 프로세스에 성공하면 검색된 저장소 배열, 저장소 풀, 제조업체, 모델 및 용량이 페이지에 나열됩니다.

	![Discover storage](./media/hyper-v-recovery-manager-configure-vault/SRSAN_Discover.png)

7. **관리할 저장소 풀을 선택한 후 분류 할당**에서 VMM이 관리할 저장소 풀을 선택한 후 분류를 할당합니다. LUN 정보는 저장소 풀에서 가져옵니다. 보호해야 하는 응용 프로그램, 응용 프로그램의 용량 요구 사항 및 무엇을 함께 복제해야 하는지에 대한 요구 사항을 기준으로 LUN을 만듭니다.

	![Classify storage](./media/hyper-v-recovery-manager-configure-vault/SRSAN_Classify.png)

<a name="LUNs"></a> <h4>LUN을 만들고 저장소 할당</h4>


1. SAN 저장소가 VMM에 통합된 후 LUN을 만듭니다(프로비전합니다). 자세한 내용은 다음을 참조하세요.
	
	- <a href="http://go.microsoft.com/fwlink/?LinkId=518490">VMM에서 논리 단위를 만드는 방법을 선택하는 방법</a>(영문)
	- <a href="http://go.microsoft.com/fwlink/?LinkId=518491">VMM에서 저장소 논리 단위를 프로비전하는 방법</a>(영문)
2. 그런 다음 VMM이 프로비전된 저장소에 가상 컴퓨터 데이터를 배포할 수 있도록 Hyper-V 호스트에 저장소 용량을 할당합니다. 
	- 클러스터에 저장소를 할당하려면 먼저 클러스터가 있는 VMM 호스트 그룹에 저장소를 할당해야 합니다. <a href="http://go.microsoft.com/fwlink/?LinkId=518493">호스트 그룹에 저장소 논리 단위를 할당하는 방법</a> 및 <a href="http://go.microsoft.com/fwlink/?LinkId=518492">호스트 그룹에 저장소 풀을 할당하는 방법</a>을 참조하세요.
	- <a href="http://go.microsoft.com/fwlink/?LinkId=513017">VMM에서 Hyper-V 호스트 클러스터에 저장소를 구성하는 방법</a>에 설명된 대로 클러스터에 저장소 용량을 할당합니다.
	

<a name="RGs"></a> <h4>복제 그룹 만들기</h4>

1. 함께 복제해야 하는 LUN을 모두 포함하는 복제 그룹을 만듭니다.
2. VMM 콘솔에서 저장소 배열 속성의 **복제 그룹** 탭을 열고 **새로 만들기**를 클릭합니다.
	![SAN replication group](./media/hyper-v-recovery-manager-configure-vault/SRSAN_RepGroup.png)



<a name="vault"></a> <h3>2단계: 자격 증명 모음 만들기</h3>
배포 필수 조건을 확인한 후 Azure Site Recovery 자격 증명 모음을 만듭니다. 또는 기존 자격 증명 모음을 사용하고 SAN 복제를 구성할 수도 있습니다.

1. [관리 포털](https://manage.windowsazure.com)에 로그인합니다.


2. <b>데이터 서비스</b>, <b>복구 서비스</b>를 차례로 확장하고 <b>사이트 복구 자격 증명 모음</b>을 클릭합니다.


3. <b>새로 만들기</b>를 클릭한 후 <b>빠른 생성</b>을 클릭합니다.
	
4. <b>이름</b>에 자격 증명 모음을 식별하기 위한 이름을 입력합니다.

5. <b>지역</b>에서 자격 증명 모음에 대한 지리적 지역을 선택합니다. 지원되는 지역을 확인하려면 <a href="http://go.microsoft.com/fwlink/?LinkId=389880">Azure Site Recovery 가격 정보</a>에서 지리적 가용성을 참조하세요.

6. <b>자격 증명 모음 만들기</b>를 클릭합니다. 

	![New Vault](./media/hyper-v-recovery-manager-configure-vault/SRSAN_HvVault.png)

<P>상태 표시줄을 점검하여 자격 증명 모음이 성공적으로 만들어졌는지 확인합니다. 자격 증명 모음은 기본 복구 서비스 페이지에서 <b>활성</b>으로 나열됩니다.</P>

<a name="upload"></a> <h2>3단계: 자격 증명 모음 구성</h3>


1. <b>복구 서비스</b> 페이지에서 자격 증명 모음을 클릭하여 빠른 시작 페이지를 엽니다. 빠른 시작은 언제든지 아이콘을 사용하여 열 수도 있습니다.

	![Quick Start Icon](./media/hyper-v-recovery-manager-configure-vault/SRSAN_QuickStartIcon.png)

2. 드롭다운 목록에서 **Between Hyper-V on-premises site using array replication(배열 복제를 사용하여 Hyper-V 온-프레미스 사이트 간)**을 선택합니다.


<a name="download"></a> <h3>3단계: Azure Site Recovery 공급자 설치</h3>
자격 증명 모음을 만들었으면 등록 키를 포함하는 등록 파일을 생성합니다. Azure Site Recovery 공급자를 설치할 때 이 파일을 선택합니다. 

1. <b>빠른 시작</b> 페이지의 **Prepare VMM servers(VMM 서버 준비)**에서 **Generate registration key(등록 키 생성)** 파일을 클릭합니다. 이 키는 생성된 날로부터 5일간 유효합니다. VMN 서버에서 액세스할 수 있는 안전한 위치에 파일을 다운로드해야 합니다. 예를 들어 공유에 다운로드할 수 있습니다. 공급자를 설치할 때 이 파일을 선택해야 합니다.

	![Registration key](./media/hyper-v-recovery-manager-configure-vault/SRSAN_QuickStartRegKey.png)


2. <b>빠른 시작</b> 페이지의 **Prepare VMM servers(VMM 서버 준비)**에서 <b>Download Microsoft Azure Site Recovery Provider for installation on VMM servers(VMM 서버에 설치할 Microsoft Azure Site Recovery 공급자 다운로드)</b>를 클릭하여 최신 버전의 공급자 설치 파일을 받습니다.
3. 원본 및 대상 VMM 서버에서 이 파일을 실행합니다.
4. **사전 요구 사항 확인**에서 공급자 설치를 시작하기 위해 VMM 서비스를 중지하도록 선택합니다. 서비스가 중지되고 설정이 완료될 때 자동으로 다시 시작됩니다. 

	![Prerequisites](./media/hyper-v-recovery-manager-configure-vault/SRSAN_ProviderPrereq.png)

5. **Microsoft 업데이트**에서 업데이트를 옵트인(opt in)할 수 있습니다. 이 설정이 사용되면 공급자가 Microsoft 업데이트 정책에 따라 설치됩니다.

공급자가 설치된 후에는 설정을 계속 진행하여 자격 증명 모음에 서버를 등록합니다.

6. VMM 서버에서 실행 중인 공급자를 인터넷에 연결하는 방법을 인터넷 연결 페이지에서 지정합니다. 서버에 구성된 기본 인터넷 연결 설정을 사용하려면 <b>Use default system proxy settings(기본 시스템 프록시 설정 사용)</b>를 선택합니다.

	![Internet Settings](./media/hyper-v-recovery-manager-configure-vault/SRSAN_ProviderProxy.png)

7. **등록 키**에서 Azure Site Recovery를 다운로드하고 VMM 서버에 복사했다고 선택합니다.
8. 자격 증명 모음에서 VMM 서버를 식별하기 위한 이름을 **서버 이름**에서 지정합니다.

	![Server registration](./media/hyper-v-recovery-manager-configure-vault/SRSAN_ProviderRegKeyServerName.png)

9. **초기 클라우드 메타데이터** 동기화에서 VMM 서버에 있는 모든 클라우드의 메타데이터를 자격 증명 모음과 동기화할 것인지 여부를 선택합니다. 이 작업은 각 서버에서 한 번만 수행해야 합니다. 모든 클라우드를 동기화하지 않는 경우 이 설정을 선택 취소된 상태로 두고 VMM 콘솔의 클라우드 속성에서 각 클라우드를
10. 
11. 개별적으로 동기화할 수 있습니다.

10. **데이터 암호화** 옵션은 이 시나리오에서 사용되지 않습니다. 

	![Server registration](./media/hyper-v-recovery-manager-configure-vault/SRSAN_ProviderSyncEncrypt.png)

11. <b>등록</b>을 클릭하여 프로세스를 완료합니다. 등록 후에 VMM 서버의 메타데이터가 Azure Site Recovery에 의해 검색됩니다. 서버가 자격 증명 모음의 **서버** 페이지에서 <b>리소스</b> 탭에 표시됩니다. 공급자 설치 후에는 VMM 콘솔에서 공급자 설정을 수정합니다.

 
<h3><a id="storage"></a>4단계: 저장소 배열과 풀 매핑</h3>

기본 및 보조 사이트에서 저장소 배열과 풀 간의 매핑을 만들어야 합니다.

시작하기 전에 자격 증명 모음에서 클라우드가 표시되는지 확인합니다. 공급자를 설치할 때 모든 클라우드를 동기화하도록 선택하거나 VMM 콘솔에서 클라우드 속성의 **일반** 탭에서 특정 클라우드를 동기화하도록 선택하면 클라우드가 검색됩니다. 그런 다음 다음과 같이 저장소 배열을 매핑합니다.

1. **리소스** > **서버 저장소** > **Map Source and Target Arrays(원본 및 대상 배열 매핑)**를 클릭합니다.
	![Server registration](./media/hyper-v-recovery-manager-configure-vault/SRSAN_StorageMap.png)
2. 기본 사이트의 저장소 배열을 선택하고 보조 사이트의 저장소 배열에 매핑합니다.
3.  배열 내의 원본 및 대상 저장소 풀을 매핑합니다. 이렇게 하려면 **저장소 풀**에서 매핑할 원본 및 대상 저장소 풀을 선택합니다.

	![Server registration](./media/hyper-v-recovery-manager-configure-vault/SRSAN_StorageMapPool.png)




<h3><a id="clouds"></a>5단계: 클라우드 보호 설정 구성</h3>

VMM 서버가 등록되면 클라우드 보호 설정을 구성할 수 있습니다. 공급자를 설치할 때 **Synchronize cloud data with the vault(클라우드 데이터를 자격 증명 모음과 동기화)** 옵션을 설정했으므로 VMM 서버에 있는 모든 클라우드가 자격 증명 모음에서 <b>보호되는 항목</b> 탭에 표시됩니다.

![Published Cloud](./media/hyper-v-recovery-manager-configure-vault/SRSAN_CloudsList.png)

1. 빠른 시작 페이지에서 ** VMM 클라우드에 대해 보호 설정**을 클릭합니다.
2. **보호된 항목** 탭에서 구성할 클라우드를 선택하고 **구성** 탭으로 이동합니다. 다음 사항에 유의하세요.
3. <b>대상</b>에서 <b>VMM</b>을 선택합니다.
4. <b>대상 위치</b>에서 복구에 사용할 클라우드를 관리하는 온사이트 VMM 서버를 선택합니다.
5. <b>대상 클라우드</b>에서 원본 클라우드의 가상 컴퓨터 장애 조치(Failover)에 사용할 대상 클라우드를 선택합니다. 다음 사항에 유의하세요.
	- 보호할 가상 컴퓨터의 복구 요구 사항을 충족하는 대상 클라우드를 선택하는 것이 좋습니다.
	- 클라우드는 단일 클라우드 쌍에 기본 클라우드 또는 대상 클라우드로만 속할 수 있습니다.
6. Azure Site Recovery는 클라우드가 SAN 복제 지원 저장소에 액세스할 수 있는지, 스토리지 배열이 피어 관계가 있는지 확인합니다. 참여하는 배열 피어가 표시됩니다.
7. 확인에 성공하면 **복제 유형**에서 **SAN**을 선택합니다.

<p>설정을 저장하고 나면 작업이 생성되고 <b>작업</b> 탭에서 모니터링할 수 있습니다. 클라우드 설정은 <b>구성</b> 탭에서 수정할 수 있습니다. 대상 위치 또는 대상 클라우드를 수정하려면 클라우드 구성을 제거한 후 클라우드를 다시 구성해야 합니다.</p>


<h3><a id="replication"></a>6단계: 복제 그룹에 대해 복제 사용</h3>

가상 컴퓨터에 대해 보호를 사용하도록 설정하려면 먼저 저장소 복제 그룹에 대해 복제를 사용하도록 설정해야 합니다. 

1. Azure Site Recovery 포털에 있는 기본 클라우드의 속성 페이지에서 **가상 컴퓨터** 탭을 엽니다. **Add Replication Group(복제 그룹 추가)**을 클릭합니다.
2. 클라우드와 연결된 VMM 복제 그룹을 하나 이상 선택하고 원본 및 대상 배열을 확인하고 복제 빈도를 지정합니다.

<P>이 작업이 완료되면 Azure Site Recovery에서는 VMM 및 SMI-S 공급자와 함께 대상 사이트 저장소 LUN을 프로비전하고 저장소 복제를 사용하도록 설정합니다. 복제 그룹이 이미 복제된 경우 Azure Site Recovery에서는 기존 복제 관계를 다시 사용하고 Azure Site Recovery의 정보를 업데이트합니다.</P>

<h2><a id="enablevirtual"></a>7단계: 가상 컴퓨터 보호 사용</h2>
<p>저장소 그룹이 복제되면 다음 방법 중 하나를 사용하여 VMM 콘솔에서 가상 컴퓨터에 대한 보호를 사용하도록 설정합니다.</p>



- **새 가상 컴퓨터** - 새 가상 컴퓨터를 만들 때 VMM 콘솔에서 Azure Site Recovery 보호를 사용하도록 설정하고 가상 컴퓨터를 복제 그룹과 연결합니다.
이 옵션을 사용하면 VMM은 지능형 배치를 사용하여 가상 컴퓨터 저장소를 복제 그룹의 LUN에 최적으로 배치합니다. Azure Site Recovery에서는 보조 사이트의 섀도 가상 컴퓨터 생성을 오케스트레이션하고 장애 조치(Failover) 이후 복제본 가상 컴퓨터를 시작할 수 있도록 용량을 할당합니다.
- **기존 가상 컴퓨터** - 가상 컴퓨터가 VMM에 이미 배포된 경우 Azure Site Recovery 보호를 사용하도록 설정하고 복제 그룹으로 저장소를 마이그레이션할 수 있습니다. 완료되면 VMM과 Azure Site Recovery에서는 새 가상 컴퓨터를 검색하고 보호를 위해 Azure Site Recovery에서 가상 컴퓨터를 관리하기 시작합니다. 섀도 가상 컴퓨터는 보조 사이트에서 만들어지며 장애 조치(Failover) 이후 복제본 가상 컴퓨터가 시작될 수 있도록 용량이 할당됩니다.


	![Enable protection](./media/hyper-v-recovery-manager-configure-vault/SRSAN_EnableProtection.png)
	

<P>보호를 사용하도록 설정한 가상 컴퓨터는 Azure Site Recovery 콘솔에 표시됩니다. 가상 컴퓨터 속성을 보고, 상태를 추적하고, 여러 가상 컴퓨터를 포함하는 복제 그룹을 장애 조치(Failover)할 수 있습니다. SAN 복제에서 복제 그룹과 연결된 모든 가상 컴퓨터는 함께 장애 조치(Failover)해야 합니다. 이는 장애 조치(Failover)가 먼저 저장소 계층에서 수행되기 때문입니다. 복제 그룹을 제대로 그룹화하고 연결된 가상 컴퓨터만 함께 배치하는 것이 중요합니다.</P>

**작업** 탭에서 초기 복제를 비롯하여 보호 사용 작업의 진행 상태를 추적합니다. 보호 완료 작업이 실행된 후에는 가상 컴퓨터가 장애 조치(Failover)를 수행할 준비가 되어 있습니다. 
	![Virtual machine protection job](./media/hyper-v-recovery-manager-configure-vault/SRSAN_JobPropertiesTab.png)

<h3><a id="recoveryplans"></a>8단계: 배포 테스트</h3>



배포를 테스트하여 가상 컴퓨터 및 데이터가 예상대로 장애 조치(Failover)되는지 확인합니다. 이렇게 하려면 복제 그룹을 선택하여 복구 계획을 만든 다음 계획에 대해 테스트 장애 조치(Failover)를 실행합니다.

1. **복구 계획** 탭에서 **복구 계획 만들기**를 클릭합니다.
2. 복구 계획 이름, 원본 및 대상 VMM 서버를 지정합니다. 원본 서버에 장애 조치(Failover) 및 복구를 사용하도록 설정한 가상 컴퓨터가 있어야 합니다. **SAN**을 선택하여 SAN 복제가 구성된 클라우드만 표시합니다.

	![Create recovery plan](./media/hyper-v-recovery-manager-configure-vault/SRSAN_RPlan.png)

4. **가상 컴퓨터 선택**에서 복제 그룹을 선택합니다. 복제 그룹과 연관된 모든 가상 컴퓨터가 선택되고 복구 계획에 추가됩니다. 이러한 가상 컴퓨터는 복구 계획 기본 그룹인 그룹 1에 추가됩니다. 필요한 경우 그룹을 추가할 수 있습니다. 복제 후 가상 컴퓨터가 복구 계획 그룹의 순서에 따라 시작됩니다.

	![Add virtual machines](./media/hyper-v-recovery-manager-configure-vault/SRSAN_RPlanVM.png)	
5. 만든 복구 계획은 **복구 계획** 탭의 목록에 표시됩니다. 
6. **복구 계획** 탭에서 계획을 선택하고 **테스트 장애 조치(Failover)**를 클릭합니다.
7. **테스트 장애 조치(Failover) 확인** 페이지에서 **없음**을 선택합니다. 이 옵션을 사용하도록 설정하면 장애 조치(Failover)된 복제본 가상 컴퓨터가 네트워크에 연결되지 않습니다. 여기서는 가상 컴퓨터가 올바로 장애 조치(Failover)되는지 테스트하지만 복제 네트워크 환경을 테스트하지는 않습니다. 보다 포괄적인 테스트 장애 조치(Failover)를 실행하려는 경우 MSDN에서 <a href="http://go.microsoft.com/fwlink/?LinkId=522291">온-프레미스 배포 테스트</a>를 참조하세요.

	![Select test network](./media/hyper-v-recovery-manager-configure-vault/SRSAN_TestFailover1.png)


8. 테스트 가상 컴퓨터는 복제본 가상 컴퓨터가 있는 호스트와 동일한 호스트에 생성됩니다. 복제본 가상 컴퓨터가 있는 클라우드에는 추가되지 않습니다.
9. 복제 후 복제본 가상 컴퓨터의 IP 주소는 주 가상 컴퓨터의 IP 주소와 같지 않게 됩니다. DHCP에서 주소를 발급하는 경우 주소가 자동으로 업데이트됩니다. DHCP를 실행하고 있지 않은 경우 주소가 동일한지 확인하려면 몇 가지 스크립트를 실행해야 합니다.
10. 다음 샘플 스크립트를 실행하여 IP 주소를 검색합니다.
    **$vm = Get-SCVirtualMachine -Name <VM_NAME>
	$na = $vm[0].VirtualNetworkAdapters>
	$ip = Get-SCIPAddress -GrantToObjectID $na[0].id
	$ip.address**  
11. 다음 샘플 스크립트에서 이전 샘플 스크립트를 사용하여 검색한 IP 주소를 지정하고 실행하여 DNS를 업데이트합니다.

	**[string]$Zone,
	[string]$name,
	[string]$IP
	)
	$Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
	$newrecord = $record.clone()
	$newrecord.RecordData[0].IPv4Address  =  $IP
	Set-DnsServerResourceRecord -zonename com -OldInputObject $record -NewInputObject $Newrecord**

<h2><a id="runtest" name="runtest" href="#runtest"></a>작업 모니터</h2>
<p><b>작업</b> 탭과 <b>대시보드</b>를 사용하여 Azure Site Recovery 자격 증명 모음에서 수행한 주 작업을 보고 모니터링할 수 있습니다. 이러한 작업에는 클라우드에 대한 보호 구성, 가상 컴퓨터의 보호 설정 및 해제, 장애 조치(Failover) 실행(계획됨, 계획되지 않음 또는 테스트), 계획되지 않은 장애 조치(Failover) 커밋이 포함됩니다.</p>

<p><b>작업</b> 탭에서는 작업을 보고, 작업 세부 정보 및 오류를 드릴다운하고, 쿼리 작업을 실행하여 특정 조건과 일치하는 작업을 검색하고, 작업을 Excel로 내보내고, 실패한 작업을 다시 시작할 수 있습니다.</p>

<p><b>대시보드</b>에서는 최신 버전의 공급자 및 에이전트 설치 파일을 다운로드하고, 자격 증명 모음에 대한 구성 정보를 가져오고, 자격 증명 모음에서 보호를 관리하는 가상 컴퓨터의 수를 확인하고, 최근 작업을 보고, 자격 증명 모음 인증서를 관리하고, 가상 컴퓨터를 재동기화할 수 있습니다.</p>

<p>작업 및 대시보드와의 상호 작용에 대한 자세한 내용은 <a href="http://go.microsoft.com/fwlink/?LinkId=398534">작업 및 모니터링 가이드</a>를 참조하세요.</p>
	
<h2><a id="next" name="next" href="#next"></a>다음 단계</h2>
<UL>
<LI>정식 프로덕션 환경에서 Azure Site Recovery를 계획하고 배포하려면 <a href="http://go.microsoft.com/fwlink/?LinkId=321294">Azure Site Recovery용 계획 가이드</a> 및 <a href="http://go.microsoft.com/fwlink/?LinkId=321295">Azure Site Recovery용 배포 가이드</a>를 참조하세요.</LI>
<LI>궁금한 사항은 <a href="http://go.microsoft.com/fwlink/?LinkId=313628">Azure 복구 서비스 포럼</a>을 참조하세요.</LI> 
</UL>

<!--HONumber=49-->