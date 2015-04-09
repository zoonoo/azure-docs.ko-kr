<properties 
	pageTitle="자습서: 온-프레미스 Hyper-V 사이트와 Azure 간 보호 설정" 
	description="Azure Site Recovery는 온-프레미스 Hyper-V 사이트와 Azure 간 가상 컴퓨터의 복제, 장애 조치(failover) 및 복구를 조정합니다." 
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

# 자습서: 온-프레미스 Hyper-V 사이트와 Azure 간 보호 설정


<h2><a id="overview" name="overview" href="#overview"></a>개요</h2>
<p>Azure Site Recovery는 여러 배포 시나리오에서 가상 컴퓨터의 복제, 장애 조치(Failover) 및 복구를 오케스트레이션하여 비즈니스 및 작업 연속성 전략에 기여합니다.<p>

이 자습서에서는 온-프레미스 사이트의 Windows Server 2012 R2를 사용하여 Hyper-V 서버에서 실행 중인 작업을 보호하기 위해 Azure Site Recovery를 배포하는 방법에 대해 설명합니다. Hyper-V 복제를 사용하여 Hyper-V 서버의 가상 컴퓨터가 Azure로 복제됩니다. 이 배포는 사무실이나 지점에 Hyper-V 서버가 있지만 System Center VMM이 배포되지 않은 경우에 특히 유용합니다.


<LI>이 자습서에서는 가장 빠른 배포 경로와 최소한의 기본 설정을 사용합니다.
배포에 대한 자세한 내용은 <a href="http://go.microsoft.com/fwlink/?LinkId=522087">계획</a> 및 <a href=" http://go.microsoft.com/fwlink/?LinkId=522088">배포</a> 가이드에서 확인할 수 있습니다.</LI>
<LI>추가 Azure Site Recovery 배포 시나리오에 대한 자세한 내용은 <a href="http://go.microsoft.com/fwlink/?LinkId=518690">Azure Site Recovery 개요</a>에서 확인할 수 있습니다.</LI>
<LI>이 자습서를 수행하는 동안 문제가 발생하면 위키 문서 <a href="http://go.microsoft.com/fwlink/?LinkId=389879">Azure Site Recovery: 일반 오류 시나리오 및 해결 방법</a>을 살펴보거나 <a href="http://go.microsoft.com/fwlink/?LinkId=313628">Azure 복구 서비스 포럼</a>(영문)에 문의 사항을 게시하세요.</LI>
</UL>

</div>

<h2><a id="before" name="before" href="#before"></a>필수 조건</h2>
<div class="dev-callout"> 
<P>시작하기 전에 모든 요소가 준비되었는지 확인하세요.</P>

<UL>
<LI><b>Azure 계정</b> - Azure 계정이 필요합니다. 없는 경우에는 <a href="http://aka.ms/try-azure">Azure 무료 평가판</a>을 참조하세요. 가격 정보는 <a href="http://go.microsoft.com/fwlink/?LinkId=378268">Azure Site Recovery 관리자 가격 정보</a>에서 확인할 수 있습니다.</LI>
<LI><b>Hyper-V</b> - 원본 온-프레미스 사이트에서 Hyper-V 역할로 Windows Server 2012 R2를 실행하는 서버가 하나 이상 있어야 합니다. Hyper-V 서버에 가상 컴퓨터가 하나 이상 포함되어야 합니다. Hyper-V 서버가 직접 또는 프록시를 통해 인터넷에 연결되어야 합니다.</LI>
<LI><b>가상 컴퓨터</b> - 보호할 가상 컴퓨터가 가상 컴퓨터에 대한 Azure 필수 조건에 맞아야 합니다. <a href="http://go.microsoft.com/fwlink/?LinkId=522287">가상 컴퓨터 지원</a>을 참조하세요.</LI>

</UL>
<h2><a id="tutorial" name="tutorial" href="#tutorial"></a>학습 단계</h2>
필수 구성 요소를 확인한 후 다음을 수행합니다.
<UL>
<LI><a href="#vault">1단계: 자격 증명 모음 만들기</a>- Azure Site Recovery 자격 증명 모음을 만듭니다.</LI>
<LI><a href="#site">2단계: Hyper-V 사이트 만들기</a>- 보호할 가상 컴퓨터가 포함된 모든 Hyper-V 서버에 대한 논리 컨테이너로 Hyper-V 사이트를 만듭니다.</LI>
<LI><a href="#download">3단계: Hyper-V 서버 준비</a>- 등록 키를 생성하고 공급자 설치 파일을 다운로드합니다. 사이트의 각 Hyper-V 서버에서 파일을 실행하고 키를 선택하여 서버를 자격 증명 모음에 등록합니다.</LI>
<LI><a href="#resources">4단계: 리소스 준비</a>- 복제된 가상 컴퓨터를 저장할 Azure 저장소 계정을 만듭니다.</LI>
<LI><a href="#protectiongroup">5단계: 보호 그룹 만들기 및 구성</a>- 보호 그룹을 만들고 이 그룹에 보호 설정을 적용합니다. 보호 설정은 그룹에 추가하는 모든 가상 컴퓨터에 적용됩니다.</LI>
<LI><a href="#enablevirtual">6단계: 가상 컴퓨터의 보호 활성화</a>- 가상 컴퓨터를 보호 그룹에 추가하여 가상 컴퓨터에 대한 보호를 사용하도록 설정합니다.</LI>
<LI><a href="#recovery plans">7단계: 배포 테스트</a>- 가상 컴퓨터에 대한 테스트 장애 조치(Failover)를 실행합니다.</LI>

</UL>




<a name="vault"></a> <h3>1단계: 자격 증명 모음 만들기</h3>

1. [관리 포털](https://manage.windowsazure.com)에 로그인합니다.


2. <b>데이터 서비스</b>, <b>복구 서비스</b>를 차례로 확장하고 <b>사이트 복구 자격 증명 모음</b>을 클릭합니다.


3. <b>새로 만들기</b>를 클릭한 후 <b>빠른 생성</b>을 클릭합니다.
	
4. <b>이름</b>에 자격 증명 모음을 식별하기 위한 이름을 입력합니다.

5. <b>지역</b>에서 자격 증명 모음에 대한 지리적 지역을 선택합니다. 지원되는 지역을 확인하려면 <a href="http://go.microsoft.com/fwlink/?LinkId=389880">Azure Site Recovery 가격 정보</a>에서 지리적 가용성을 참조하세요.

6. <b>자격 증명 모음 만들기</b>를 클릭합니다. 

	![New Vault](./media/hyper-v-recovery-manager-configure-vault/SR_HvVault.png)

<P>상태 표시줄을 점검하여 자격 증명 모음이 성공적으로 만들어졌는지 확인합니다. 자격 증명 모음은 기본 복구 서비스 페이지에서 <b>활성</b>으로 나열됩니다.</P>

<a name="site"></a> <h3>2단계: Hyper-V 사이트 만들기</h3>

1. 복구 서비스 페이지에서 자격 증명 모음을 클릭하여 빠른 시작 페이지를 엽니다. 빠른 시작은 언제든지 아이콘을 사용하여 열 수도 있습니다.

	![Hyper-V site scenario](./media/hyper-v-recovery-manager-configure-vault/SR_QuickStartIcon.png)

2. 드롭다운 목록에서 **온-프레미스 Hyper-V 사이트와 Azure 간**을 선택합니다.

	![Hyper-V site scenario](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_SelectScenario.png)

3. **Create a Hyper-V Site(Hyper-V 사이트 만들기)**에서 **Create Hyper-V site((Hyper-V 사이트 만들기)**를 클릭합니다. 사이트 이름을 지정하고 저장합니다.

	![Hyper-V site](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_CreateSite2.png)

<a name="download"></a> <h3>3단계: Hyper-V 서버 준비</h3>
	

1. **Prepare Hyper-V servers(Hyper-V 서버 준비)**에서 **Download a registration key(등록 키 다운로드)** 파일을 클릭합니다.
2. **Download Registration Key(등록 키 다운로드)** 페이지에서 사이트 옆의 **다운로드**를 클릭합니다. Hyper-V 서버에서 쉽게 액세스할 수 있는 안전한 위치로 키를 다운로드합니다. 이 키는 생성된 날로부터 5일간 유효합니다.

	![Registration key](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_DownloadKey2.png)

4. <b>공급자 다운로드</b>를 클릭하여 최신 버전을 가져옵니다.
5. 자격 증명 모음에 등록하려는 각 Hyper-V 서버에서 파일을 실행합니다. 이 파일은 다음 두 구성 요소를 설치합니다.
	- **Azure Site Recovery 공급자** - Hyper-V 서버와 Azure Site Recovery 포털 간의 통신 및 오케스트레이션을 처리합니다. 
	- **Azure 복구 서비스 에이전트** - 원본 Hyper-V 서버에 실행 중인 가상 컴퓨터와 Azure 저장소 간의 데이터 전송을 처리합니다. 
6. **Microsoft 업데이트**에서 업데이트를 옵트인(opt in)할 수 있습니다. 이 설정을 사용하도록 설정하면 공급자와 에이전트 업데이트가 Microsoft 업데이트 정책에 따라 설치됩니다.

	![Microsoft Updates](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_Provider1.png)

7. **설치**에 Hyper-V 서버에서 공급자 및 에이전트를 설치할 위치를 지정합니다.

	![Install location](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_Provider2.png)

8. 설치가 완료되면 설정을 계속 진행하여 자격 증명 모음에 서버를 등록합니다.

	![Installation complete](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_Provider3.png)


9. **인터넷 연결** 페이지에서 공급자가 Azure Site Recovery에 연결하는 방법을 지정합니다. 서버에 구성되어 있는 기본 인터넷 연결 설정을 사용하려면 <b>기본 시스템 프록시 설정 사용</b>을 선택합니다. 

	![Internet Settings](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_Provider4.png)

9. **Vault Settings(자격 증명 모음 설정)** 페이지에서 **찾아보기**를 클릭하여 키 파일을 선택합니다. Azure Site Recovery 구독, 자격 증명 모음 이름 및 Hyper-V 서버가 속한 Hyper-V 사이트를 지정합니다.

	![Server registration](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_SelectKey.png)


11. 서버를 자격 증명 모음에 등록하는 등록이 시작됩니다.

	![Server registration](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_Provider6.png)

11. 등록이 완료되면 Azure Site Recovery에서 Hyper-V 서버의 메타데이터를 가져오고 서버가 자격 증명 모음의 **서버** 페이지에 있는 **Hyper-V Sites(Hyper-V 사이트)** 탭에 표시됩니다.

	![Server registration](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_Provider7.png)


<h3><a id="resources"></a>4단계: 리소스 준비</h3>


1. 빠른 시작 페이지의 **리소스 준비**에서 **저장소 계정 만들기**를 선택하여 Azure 저장소 계정이 아직 없는 경우 새로 만듭니다. 계정의 지역에서 복제 기능을 사용하도록 설정해야 합니다. 계정은 Azure Site Recovery 자격 증명 모음과 같은 지역에 있고 같은 구독과 연결되어야 합니다.

	![Create storage account](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_CreateResources1.png)



<h3><a id="protectiongroup"></a>5단계: 보호 그룹 만들기 및 구성</h3>

<p>보호 그룹은 보호 설정이 동일한 가상 컴퓨터를 하나로 묶습니다. 보호 설정을 보호 그룹에 적용하면 이러한 설정은 그룹에 추가한 가상 컴퓨터 모두에 적용됩니다.</p>
1. **Create and configure protection groups(보호 그룹 만들기 및 구성)**에서 **Create a protection group(보호 그룹 만들기)**을 클릭합니다. 필수 조건이 갖추어지지 않은 경우 메시지가 표시되며 **세부 정보 보기**를 클릭하여 자세한 내용을 확인할 수 있습니다.

2. **보호 그룹** 탭에서 보호 그룹을 추가합니다. 이름, 원본 Hyper-V 사이트, 대상 **Azure**, Azure Site Recovery 구독 이름 및 Azure 저장소 계정을 지정합니다.

	![Protection group](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_ProtectionGroupCreate.png)


2. **복제 설정**에서 기본 설정을 그대로 둡니다.

	![Protection group](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_ProtectionGroup2.png)


<h3><a id="enablevirtual"></a>6단계: 가상 컴퓨터 보호 사용</h3>
<p>가상 컴퓨터를 보호 그룹에 추가하여 가상 컴퓨터에 대한 보호를 사용하도록 설정할 수 있습니다.</p>

1. 보호 그룹에 대한 <b>컴퓨터</b> 탭에서 <b>가상 컴퓨터를 보호 그룹에 추가하여 보호 사용</b>을 클릭합니다.
2. **Enable Virtual Machine Protection(가상 컴퓨터 보호 사용)** 페이지에서 보호할 가상 컴퓨터를 선택합니다. 

	![Enable virtual machine protection](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_AddVM1.png)

보호 사용 작업이 시작됩니다. **작업** 탭에서 진행률을 추적할 수 있습니다. 보호 완료 작업이 실행된 후에는 가상 컴퓨터가 장애 조치(Failover)를 수행할 준비가 되어 있습니다. 보호를 사용하도록 설정하고 초기 복제가 완료되면 Azure에서 가상 컴퓨터를 볼 수 있습니다.

Azure Site Recovery의 **보호된 항목** > **보호 그룹** > *protectiongroup_name* > **가상 컴퓨터**에서 보호된 가상 컴퓨터를 확인할 수 있습니다. 


<h3><a id="recoveryplans"></a>7단계: 배포 테스트</h3>

배포를 테스트하여 프로덕션 환경에 영향을 주지 않고 격리된 네트워크에서 장애 조치(Failover) 및 복구 메커니즘을 시뮬레이트할 수 있습니다. 이렇게 하려면 보호된 가상 컴퓨터에 대한 테스트 장애 조치(Failover)를 실행합니다.

1. **보호된 항목** > **보호 그룹** > *protectiongroup_name* > **가상 컴퓨터**에서 장애 조치(Failover)할 가상 컴퓨터를 선택하고 **테스트 장애 조치(Failover)**를 클릭합니다.
2. **테스트 장애 조치(Failover) 확인** 페이지에서 **없음**을 선택합니다. 

	![Recovery plan](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_TestFailoverNoNetwork.png)

3. **작업** 탭에서 장애 조치(Failover) 진행률 및 상태를 추적할 수 있습니다.
4. 장애 조치(Failover)가 **Complete testing(테스트 완료)** 단계에 도달하면 다음과 같이 검증을 완료합니다.
	- 장애 조치(Failover) 이후 Azure 포털에서 복제본 가상 컴퓨터를 봅니다. 가상 컴퓨터가 성공적으로 시작되는지 확인합니다.
	- 온-프레미스 네트워크에서 가상 컴퓨터에 액세스할 수 있도록 설정한 경우 가상 컴퓨터에 대한 원격 데스크톱 연결을 시작할 수 있습니다.
	- **Complete the test(테스트 완료)**를 클릭하여 테스트를 완료합니다.
	- **참고**를 클릭하여 테스트 장애 조치(Failover)와 연관된 항목을 기록한 후 저장합니다.
	- **The test failover is complete(테스트 장애 조치(Failover)가 완료되었습니다)**를 클릭합니다. 테스트 환경을 정리하여 자동으로 테스트 가상 컴퓨터의 전원을 끄고 컴퓨터를 삭제합니다.

<h2><a id="runtest" name="runtest" href="#runtest"></a>작업 모니터</h2>
<p><b>작업</b> 탭과 <b>대시보드</b>를 사용하여 Azure Site Recovery 자격 증명 모음에서 수행한 주 작업을 보고 모니터링할 수 있습니다. 이러한 작업에는 클라우드에 대한 보호 구성, 가상 컴퓨터의 보호 설정 및 해제, 장애 조치(Failover) 실행(계획됨, 계획되지 않음 또는 테스트), 계획되지 않은 장애 조치(Failover) 커밋이 포함됩니다.</p>

<p><b>작업</b> 탭에서는 작업을 보고, 작업 세부 정보 및 오류를 드릴다운하고, 쿼리 작업을 실행하여 특정 조건과 일치하는 작업을 검색하고, 작업을 Excel로 내보내고, 실패한 작업을 다시 시작할 수 있습니다.</p>

<p><b>대시보드</b>에서는 최신 버전의 공급자 및 에이전트 설치 파일을 다운로드하고, 자격 증명 모음에 대한 구성 정보를 가져오고, 자격 증명 모음에서 보호를 관리하는 가상 컴퓨터의 수를 확인하고, 최근 작업을 보고, 자격 증명 모음 인증서를 관리하고, 가상 컴퓨터를 재동기화할 수 있습니다.</p>

<p>작업 및 대시보드와의 상호 작용에 대한 자세한 내용은 <a href="http://go.microsoft.com/fwlink/?LinkId=398534">작업 및 모니터링 가이드</a>를 참조하세요.</p>

<h2><a id="next" name="next" href="#next"></a>다음 단계</h2>
<UL>
<LI>정식 프로덕션 환경에서 Azure Site Recovery를 계획하고 배포하려면 <a href="http://go.microsoft.com/fwlink/?LinkId=522087">Azure Site Recovery용 계획 가이드</a> 및 <a href=" http://go.microsoft.com/fwlink/?LinkId=522088">Azure Site Recovery용 배포 가이드</a>를 참조하세요.</LI>
<LI>궁금한 사항은 <a href="http://go.microsoft.com/fwlink/?LinkId=313628">Azure 복구 서비스 포럼</a>을 참조하세요.</LI> 
</UL>

<!--HONumber=49-->