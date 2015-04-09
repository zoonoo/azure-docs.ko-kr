<properties 
	pageTitle="자습서: 온-프레미스 VMM 사이트와 Azure 간 보호 설정" 
	description="Azure Site Recovery는 온-프레미스 VMM 클라우드에 있는 Hyper-V 가상 컴퓨터와 Azure 간의 복제, 장애 조치(Failover) 및 복구를 조정합니다." 
	services="site-recovery" 
	documentationCenter="" 
	authors="raynew" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.workload="backup-recovery" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/18/2015" 
	ms.author="raynew"/>

# 자습서: 온-프레미스 VMM 사이트와 Azure 간 보호 설정

<h2><a id="overview" name="overview" href="#overview"></a>개요</h2>
<p>Azure Site Recovery는 여러 배포 시나리오에서 가상 컴퓨터의 복제, 장애 조치(Failover) 및 복구를 오케스트레이션하여 비즈니스 및 작업 연속성 전략에 기여합니다.<p>

<P>이 자습서에서는 Azure Site Recovery를 배포하여 Hyper-V 복제를 사용한 온-프레미스 VMM 사이트와 Azure 간 보호를 오케스트레이션하는 방법에 대해 설명합니다.  이 자습서에서는 가능한 경우 가장 빠른 배포 경로와 기본 설정을 사용합니다.</P>

<UL>
<LI>전체 배포에 대한 자세한 내용은 <a href="http://go.microsoft.com/fwlink/?LinkId=321294">계획</a> 및 <a href="http://go.microsoft.com/fwlink/?LinkId=402679">배포</a> 가이드를 참조하세요.</LI>
<LI>추가 Azure Site Recovery 배포 시나리오에 대한 자세한 내용은 <a href="http://go.microsoft.com/fwlink/?LinkId=518690">Azure Site Recovery 개요</a>에서 확인할 수 있습니다.</LI>
<LI>이 자습서를 수행하는 동안 문제가 발생하면 위키 문서 <a href="http://go.microsoft.com/fwlink/?LinkId=389879">Azure Site Recovery: 일반 오류 시나리오 및 해결 방법</a>을 살펴보거나 <a href="http://go.microsoft.com/fwlink/?LinkId=313628">Azure 복구 서비스 포럼</a>(영문)에 문의 사항을 게시하세요.</LI>
</UL>


<h2><a id="prerequisites" name="prerequisites" href="#prerequisites"></a>필수 조건</h2>
<div class="dev-callout"> 
<P>자습서를 시작하기 전에 모든 요소가 준비되었는지 확인하세요.</P>

<UL>
<LI><b>Azure 계정</b> - Azure 계정이 필요합니다. 없는 경우에는 <a href="http://aka.ms/try-azure">Azure 무료 평가판</a>을 참조하세요. 구독 가격 정보는 <a href="http://go.microsoft.com/fwlink/?LinkId=378268">Azure Site Recovery 관리자 가격 정보</a>에서 확인할 수 있습니다.</LI>

<LI><b>Azure 저장소 계정</b> - Azure로 복제된 데이터를 저장하려면 Azure 저장소 계정이 필요합니다. 계정의 지역에서 복제 기능을 사용하도록 설정해야 합니다. 계정은 Azure Site Recovery 서비스와 같은 지역에 있고 같은 구독과 연결되어야 합니다. Azure 저장소 설정에 대한 자세한 내용은 <a href="http://go.microsoft.com/fwlink/?LinkId=398704">Microsoft Azure 저장소 소개</a>를 참조하세요.</LI><LI><b>VMM 서버</b> - System Center 2012 R2에서 실행되는 VMM 서버</LI>
<LI><b>VMM 클라우드</b> - VMM 서버에 있는 클라우드 하나 이상. 클라우드에 다음이 포함되어야 합니다.
	<UL>
	<LI>하나 이상의 VMM 호스트 그룹</LI>
	<LI>각 호스트 그룹에 있는 하나 이상의 Hyper-V 호스트 서버 또는 클러스터</LI>
	<li>클라우드의 원본 Hyper-V 서버에 위치한 하나 이상의 가상 컴퓨터 가상 컴퓨터는 1세대 가상 컴퓨터여야 합니다.</li>
		</UL></LI>	
<LI><b>가상 컴퓨터</b> - Azure 요구 사항을 준수하는 가상 컴퓨터가 필요합니다. 계획 가이드의 <a href="http://go.microsoft.com/fwlink/?LinkId=402602">필수 구성 요소 및 지원</a>을 참조하세요</LI>
<LI>Azure로의 장애 조치(Failover)를 위한 가상 컴퓨터 지원 요구 사항의 전체 목록은 다음 항목을 참조하세요.  </LI>
</UL>
</div>

<h2><a id="tutorial" name="tutorial" href="#tutorial"></a>학습 단계</h2>
필수 구성 요소를 확인한 후 다음을 수행합니다.
<UL>

<LI><a href="#vault">1단계: 자격 증명 모음 만들기</a>- Azure Site Recovery 자격 증명 모음을 만듭니다.</LI>
<LI><a href="#download">2단계: VMM 서버에서 공급자 응용 프로그램 설치</a>- 자격 증명 모음에서 등록 키를 생성하고 공급자 설치 파일을 다운로드합니다. VMM 서버에서 설치 프로그램을 실행하여 공급자를 설치하고 VMM 서버를 자격 증명 모음에 등록합니다.</LI>
<LI><a href="#storage">3단계: Azure 저장소 계정 추가</a>- 계정이 없으면 새로 만듭니다. </LI>
<LI><a href="#agent">4단계: 에이전트 응용 프로그램 설치</a>- 보호할 VMM 클라우드에 있는 각 Hyper-V 호스트 서버에 Microsoft Azure 복구 서비스 에이전트를 설치합니다.</LI>
<LI><a href="#clouds">5단계: 클라우드 보호 구성</a>- VMM 클라우드에 대한 보호 설정을 구성합니다.</LI>
<LI><a href="#NetworkMapping">6단계: 네트워크 매핑 구성</a>- 선택적으로 원본 VM 네트워크를 대상 Azure 가상 네트워크에 매핑하도록 네트워크 매핑을 구성할 수 있습니다.</LI>
<LI><a href="#virtualmachines">7단계: 가상 컴퓨터의 보호 활성화</a>- 보호된 VMM 클라우드에 있는 가상 컴퓨터에 보호 기능을 사용하도록 설정합니다.</LI>
<LI><a href="#test">8단계: 배포 테스트</a>- 단일 가상 컴퓨터에 대해 테스트 장애 조치(Failover)를 실행하거나 복구 계획을 만들고 계획에 대한 테스트 장애 조치(Failover)를 실행하여 작동하는지 확인합니다.</LI>
</UL>



<a name="vault"></a> <h3>1단계: 자격 증명 모음 만들기</h3>

1. [관리 포털](https://manage.windowsazure.com)에 로그인합니다.


2. <b>데이터 서비스</b>, <b>복구 서비스</b>를 차례로 확장하고 <b>사이트 복구 자격 증명 모음</b>을 클릭합니다.

3. <b>새로 만들기</b>를 클릭한 후 <b>빠른 생성</b>을 클릭합니다.
	

4. <b>이름</b>에 자격 증명 모음을 식별하기 위한 이름을 입력합니다.

5. <b>지역</b>에서 자격 증명 모음에 대한 지리적 지역을 선택합니다. 사용 가능한 지리적 지역에는 동아시아, 서유럽, 미국 서부, 미국 동부, 북유럽, 동남아시아
6. <b>자격 증명 모음 만들기</b>를 클릭합니다. 

	![New Vault](./media/hyper-v-recovery-manager-configure-vault/SR_HvVault.png)

<P>상태 표시줄을 점검하여 자격 증명 모음이 성공적으로 만들어졌는지 확인합니다. 자격 증명 모음은 기본 복구 서비스 페이지에서 <b>활성</b>으로 나열됩니다.</P>





 <a name="download"></a> <h3>2단계: 등록 키 생성 및 Azure Site Recovery 공급자 설치</h3>
 

1. <b>복구 서비스</b> 페이지에서 자격 증명 모음을 클릭하여 빠른 시작 페이지를 엽니다. 빠른 시작은 언제든지 아이콘을 사용하여 열 수도 있습니다.

	![Quick Start Icon](./media/hyper-v-recovery-manager-configure-vault/SR_QuickStartIcon.png)

2. 드롭다운 목록에서 **온-프레미스 Hyper-V 사이트와 Microsoft Azure 간**을 선택합니다.
3. **VMM 서버 준비**에서 **등록 키 생성** 파일을 클릭합니다. 이 키는 생성된 날로부터 5일간 유효합니다. 파일을 VMM 서버에 복사합니다. 공급자를 설정할 때 필요합니다.

	![Registration key](./media/hyper-v-recovery-manager-configure-vault/SR_E2ARegisterKey.png)

4. <b>빠른 시작</b> 페이지의 **Prepare VMM servers(VMM 서버 준비)**에서 <b>Download Microsoft Azure Site Recovery Provider for installation on VMM servers(VMM 서버에 설치할 Microsoft Azure Site Recovery 공급자 다운로드)</b>를 클릭하여 최신 버전의 공급자 설치 파일을 받습니다.

2. 원본 VMM 서버에서 이 파일을 실행합니다.


3. **사전 요구 사항 확인**에서 공급자 설치를 시작하기 위해 VMM 서비스를 중지하도록 선택합니다. 서비스가 중지되고 설정이 완료될 때 자동으로 다시 시작됩니다.

	![Prerequisites](./media/hyper-v-recovery-manager-configure-vault/SR_ProviderPrereq.png)

4. **Microsoft 업데이트**에서 업데이트를 옵트인(opt in)할 수 있습니다. 이 설정이 사용되면 공급자가 Microsoft 업데이트 정책에 따라 설치됩니다.

	![Microsoft Updates](./media/hyper-v-recovery-manager-configure-vault/SR_ProviderUpdate.png)

공급자가 설치된 후에는 설정을 계속 진행하여 자격 증명 모음에 서버를 등록합니다.

5. VMM 서버에서 실행 중인 공급자를 인터넷에 연결하는 방법을 **인터넷 연결**에서 지정합니다. 서버에 구성되어 있는 기본 인터넷 연결 설정을 사용하려면 <b>기본 시스템 프록시 설정 사용</b>을 선택합니다.

	![Internet Settings](./media/hyper-v-recovery-manager-configure-vault/SR_ProviderProxy.png)

6. **등록 키**에서 Azure Site Recovery를 다운로드하고 VMM 서버에 복사했다고 선택합니다.
7. **자격 증명 모음 이름**에서 서버를 등록할 자격 증명 모음의 이름을 확인합니다.
8. 자격 증명 모음에서 VMM 서버를 식별하기 위한 이름을 **서버 이름**에서 지정합니다.

	
	![Server registration](./media/hyper-v-recovery-manager-configure-vault/SR_ProviderRegKeyServerName.png)

8. **초기 클라우드 메타데이터** 동기화에서 VMM 서버에 있는 모든 클라우드의 메타데이터를 자격 증명 모음과 동기화할 것인지 여부를 선택합니다. 이 작업은 각 서버에서 한 번만 수행해야 합니다. 모든 클라우드를 동기화하지 않는 경우 이 설정을 선택 취소된 상태로 두고 VMM 콘솔의 클라우드 속성에서 각 클라우드를 개별적으로 동기화할 수 있습니다.


9. **데이터 암호화**에서는 데이터 암호화를 위해 자동으로 생성되는 SSL 인증서를 저장할 위치를 지정합니다. 이 인증서는 Azure Site Recovery 포털에서 Azure가 보호하는 클라우드에 대해 데이터 암호화를 사용하도록 설정하는 경우 사용되므로 안전하게 보관해야 합니다. Azure로 장애 조치(Failover)를 실행할 때 암호화된 데이터를 암호 해독하기 위해 이 인증서를 선택해야 합니다. 
온-프레미스 사이트 간에 복제하는 경우에는 이 옵션이 관련이 없습니다.

	![Server registration](./media/hyper-v-recovery-manager-configure-vault/SR_ProviderSyncEncrypt.png)

8. <b>등록</b>을 클릭하여 프로세스를 완료합니다. 등록 후에 VMM 서버의 메타데이터가 Azure Site Recovery에 의해 검색됩니다. 서버가 자격 증명 모음에 있는 **서버** 페이지의 <b>리소스</b> 탭 끝에 표시됩니다.

<h3><a id="storage"></a>3단계: Azure 저장소 계정 만들기</h3>
Azure 저장소 계정이 없으면 **Azure 저장소 계정 추가**를 클릭합니다. 계정의 지역에서 복제 기능을 사용하도록 설정해야 합니다. 계정은 Azure Site Recovery 서비스와 같은 하위 지역에 있고 같은 구독과 연결되어야 합니다.

<P>이 자습서를 사용하여 온-프레미스에서 Azure로 배포할 때 Azure Site Recovery의 빠른 개념 증명을 수립하세요. 가능한 가장 빠른 경로와 기본 설정을 사용하고 있습니다. Azure Site Recovery 자격 증명 모음을 만들고, 소스 VMM 서버에서 Azure Site Recovery 공급자를 설치하고, VMM 클라우드의 Hyper-V 호스트 서버에 Azure 복구 서비스 에이전트를 설치하고, 클라우드 보호 설정을 구성하고, 가상 컴퓨터에 대한 보호를 사용하도록 설정하고, 배포를 테스트할 것입니다.</P>

![Storage account](./media/hyper-v-recovery-manager-configure-vault/SR_E2AStorageAgent.png)

<h3><a id="agent"></a>4단계: Hyper-V 호스트에 Azure 복구 서비스 에이전트 설치</h3>

보호할 VMM 클라우드에 있는 각 Hyper-V 호스트 서버에 Azure 복구 서비스 에이전트를 설치합니다.

1. 빠른 시작 페이지에서 <b>Azure Site Recovery 서비스 에이전트 다운로드 및 호스트에 설치</b>를 클릭하여 최신 버전의 에이전트 설치 파일을 받습니다.

	![Install Recovery Services Agent](./media/hyper-v-recovery-manager-configure-vault/SR_E2AInstallHyperVAgent.png)

2. 보호할 VMM 클라우드에 있는 각 Hyper-V 호스트 서버에서 설치 파일을 실행합니다.
3. **필수 조건 확인** 페이지에서 <b>다음</b>을 클릭합니다. 누락된 필수 구성 요소는 자동으로 설치됩니다.

	![Prerequisites Recovery Services Agent](./media/hyper-v-recovery-manager-configure-vault/SR_AgentPrereqs.png)

4. **설치 설정** 페이지에서 에이전트를 설치할 위치를 지정하고 백업 메타데이터를 설치할 캐시 위치를 선택합니다. <b>설치</b>를 클릭합니다.


<h3><a id="clouds"></a>5단계: 클라우드 보호 설정 구성</h3>

VMM 서버가 등록되면 클라우드 보호 설정을 구성할 수 있습니다. 공급자를 설치할 때 **Synchronize cloud data with the vault(클라우드 데이터를 자격 증명 모음과 동기화)** 옵션을 설정했으므로 VMM 서버에 있는 모든 클라우드가 자격 증명 모음에서 <b>보호되는 항목</b> 탭에 표시됩니다.

![Published Cloud](./media/hyper-v-recovery-manager-configure-vault/SR_CloudsList.png)


1. 빠른 시작 페이지에서 ** VMM 클라우드에 대해 보호 설정**을 클릭합니다.
2. **보호된 항목** 탭에서 구성할 클라우드를 클릭하고 **구성** 탭으로 이동합니다.
3. <b>대상</b>에서 <b>Microsoft Azure</b>를 선택합니다.
4. <b>저장소 계정</b>에서 Azure 가상 컴퓨터를 저장하는 데 사용할 Azure 저장소를 선택합니다.
5. <b>저장된 데이터 암호화</b>를 <b>끄기</b>로 설정합니다. 이 설정은 온-프레미스 사이트와 Azure 간에 복제된 데이터를 암호화하도록 지정합니다.
6. <b>복사 빈도</b>에서 기본 설정을 그대로 둡니다. 이 값은 원본 위치와 대상 위치 사이에 데이터를 동기화해야 하는 빈도를 지정합니다.
7. <b>복구 지점 유지</b>에서 기본 설정을 그대로 둡니다. 기본값인 0인 경우에는 주 가상 컴퓨터의 가장 최근 복구 지점만 복제본 호스트 서버에 저장됩니다.
8. <b>응용 프로그램에 일관된 스냅숏의 빈도</b>에서 기본 설정을 그대로 둡니다. 이 값은 스냅숏을 만드는 빈도를 지정합니다. 스냅숏은 VSS(볼륨 섀도 복사본 서비스)를 사용하여 스냅숏이 만들어질 때 응용 프로그램이 일관된 상태가 되도록 합니다.  값을 설정할 경우 구성할 추가 복구 지점 수보다 적은지 확인하세요.
9. <b>복제 시작 시간</b>에서 Azure로 데이터 초기 복제를 시작하는 시간을 지정합니다. Hyper-V 호스트 서버의 시간대가 사용됩니다. 초기 복제는 사용률이 낮은 시간에 예약하는 것이 좋습니다. 

	![Cloud replication settings](./media/hyper-v-recovery-manager-configure-vault/SR_CloudSettingsE2A.png)

설정을 저장하고 나면 작업이 생성되고 <b>작업</b> 탭에서 모니터링할 수 있습니다. VMM 원본 클라우드에 있는 모든 Hyper-V 호스트 서버가 복제 대상으로 구성됩니다.

저장하고 나면 클라우드 설정은 <b>구성</b> 탭에서 수정할 수 있습니다. 대상 위치 또는 대상 저장소를 수정하려면 클라우드 구성을 제거한 후 클라우드를 다시 구성해야 합니다. 저장소 계정을 변경하면 변경 내용은 저장소 계정이 수정된 후에 보호하도록 설정된 가상 컴퓨터에만 적용됩니다. 기존 가상 컴퓨터는 새 저장소 계정으로 마이그레이션되지 않습니다.</p>

<h3><a id="networkmapping"></a>6단계: 네트워크 매핑 구성</h3>

<p>이 자습서에서는 테스트 환경에서 Azure Site Recovery를 배포하는 가장 간단한 경로를 설명합니다. 이 자습서를 진행하는 동안 네트워크 매핑을 구성하지 않으려면 계획 가이드에서 <a href="http://go.microsoft.com/fwlink/?LinkId=324817">네트워크 매핑 준비</a>를 읽으세요. 매핑을 구성하려면 배포 가이드에 있는 <a href="http://go.microsoft.com/fwlink/?LinkId=402533">네트워크 매핑 구성</a> 단계를 따르세요.</p>




<h3><a id="virtualmachines"></a>7단계: 가상 컴퓨터의 보호 활성화</h3>

서버, 클라우드 및 네트워크가 제대로 구성되었으면 클라우드에서 가상 컴퓨터에 대한 보호를 설정할 수 있습니다. 다음 사항에 유의하세요.

- 가상 컴퓨터는 Azure 요구 사항을 충족해야 합니다. 계획 가이드의 <a href="http://go.microsoft.com/fwlink/?LinkId=402602">필수 조건 및 지원</a>에서 해당 요구 사항을 확인하세요.
- 보호를 사용하도록 설정하려면 가상 컴퓨터에 대해 운영 체제 및 운영 체제 디스크 속성을 설정해야 합니다. VMM에서 가상 컴퓨터 템플릿을 사용하여 가상 컴퓨터를 만들 때 속성을 설정할 수 있습니다. 가상 컴퓨터 속성의 **일반** 및 **하드웨어 구성** 탭에서 기존 가상 컴퓨터에 대해 이러한 속성을 설정할 수도 있습니다. 이러한 속성을 VMM에서 설정하지 않는 경우 Azure Site Recovery 포털에서 구성할 수 있습니다.

![Create virtual machine](./media/hyper-v-recovery-manager-configure-vault/SR_EnableVMME2ANew.png)

![Modify virtual machine properties](./media/hyper-v-recovery-manager-configure-vault/SR_EnableVMME2AExisting.png)


1. 보호를 사용하도록 설정하려면 가상 컴퓨터가 있는 클라우드의 <b>가상 컴퓨터</b> 탭에서 <b>보호 사용</b>을 클릭하고 <b>가상 컴퓨터 추가</b>를 선택합니다.
2. 클라우드에 있는 가상 컴퓨터의 목록에서 보호할 가상 컴퓨터를 선택합니다. 

	![Enable virtual machine protection](./media/hyper-v-recovery-manager-configure-vault/SR_EnableVMME2ASelectVM.png)

3. 가상 컴퓨터 속성을 확인하고 필요한 대로 수정합니다.

	![Verify virtual machines](./media/hyper-v-recovery-manager-configure-vault/SR_EnableVME2AProps.png)

**작업** 탭에서 초기 복제를 비롯하여 보호 사용 작업의 진행 상태를 추적합니다. 보호 완료 작업이 실행된 후에는 가상 컴퓨터가 장애 조치(Failover)를 수행할 준비가 되어 있습니다. 보호를 사용하도록 설정하고 가상 컴퓨터가 복제되고 나면 Azure에서 가상 컴퓨터를 볼 수 있습니다.


![Virtual machine protection job](./media/hyper-v-recovery-manager-configure-vault/SR_VMJobs.png)


<h3><a id="test"></a>8단계: 배포 테스트</h3>
배포를 테스트하려면 단일 가상 컴퓨터에 대한 테스트 장애 조치(Failover)를 실행하거나, 여러 개의 가상 컴퓨터로 구성된 복구 계획을 만들고 이 계획에 대한 테스트 장애 조치(Failover)를 실행하면 됩니다.  테스트 장애 조치(Failover)에서는 격리된 네트워크에서 장애 조치(Failover) 및 복구 메커니즘을 시뮬레이션합니다. 다음 사항에 유의하세요.
<UL>
<li>장애 조치(Failover) 후에 원격 데스크탑을 사용하여 Azure의 가상 컴퓨터에 연결하려면 가상 컴퓨터에서 원격 데스크탑 연결을 사용하도록 설정하고 나서 테스트 장애 조치(Failover)를 실행합니다.</li>
<li>장애 조치(Failover) 후에 공개 IP 주소를 사용하여 원격 데스크탑을 통해 Azure의 가상 컴퓨터에 연결합니다. 이 작업을 하려면 공개 주소를 사용하여 가상 컴퓨터에 연결하지 못하도록 차단하는 도메인 정책이 없어야 합니다.</li>
</UL>

1. **복구 계획** 탭에서 새 계획을 추가합니다. 이름을 지정하고 **원본 유형**에 **VMM**, **원본**에 원본 VMM 서버를 지정합니다. 대상은 Azure입니다.

	![Create recovery plan](./media/hyper-v-recovery-manager-configure-vault/SRAzure_RP1.png)

2. **테스트 장애 조치(Failover) 확인** 페이지에서 **없음**을 선택합니다. 이 설정을 사용한 테스트 장애 조치(Failover)에서는 가상 컴퓨터가 Azure로 올바로 복제되었는지 확인하지만 복제 네트워크 구성은 확인하지 않습니다. 지정된 Azure 네트워크 사용하여 테스트를 실행하려면 <a href="http://go.microsoft.com/fwlink/?LinkId=522292">온-프레미스와 Azure 간 배포</a>를 참조하세요.

	![No network](./media/hyper-v-recovery-manager-configure-vault/SRAzure_TestFailoverNoNetwork.png)


3. 장애 조치(Failover)가 **Complete testing(테스트 완료)** 단계에 도달하면 **Complete Test(테스트 완료)**를 클릭하여 테스트 장애 조치(Failover)를 완료합니다. **작업** 탭으로 드릴다운하여 장애 조치(Failover) 진행률 및 상태를 추적하고 필요한 작업을 수행할 수 있습니다.
4. 장애 조치(Failover)가 완료되면 다음을 수행합니다.
	- 가상 컴퓨터가 성공적으로 시작되는지 확인합니다.
	- **참고**를 클릭하여 테스트 장애 조치(Failover)와 연관된 항목을 기록한 후 저장합니다.
	- **The test failover is complete(테스트 장애 조치(Failover)가 완료되었습니다)**를 클릭합니다. 테스트 환경을 정리하여 자동으로 테스트 가상 가상 컴퓨터의 전원을 끄고 테스트 Azure 네트워크를 삭제합니다.
5. 장애 조치(Failover) 후에는 Azure 포털에서 가상 컴퓨터 테스트 복제본을 확인할 수 있습니다. 온-프레미스 네트워크에서 가상 컴퓨터에 액세스할 수 있도록 설정한 경우 가상 컴퓨터에 대한 원격 데스크톱 연결을 시작할 수 있습니다.



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