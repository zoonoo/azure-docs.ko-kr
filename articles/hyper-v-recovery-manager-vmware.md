<properties 
	pageTitle="자습서: 온-프레미스 VMWare 사이트 간 보호 설정" 
	description="Azure Site Recovery의 InMage는 온-프레미스 VMWare 사이트 간의 복제, 장애 조치(Failover) 및 복구를 처리합니다." 
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

# 자습서: 온-프레미스 VMWare 사이트 간 보호 설정


<h2><a id="overview" name="overview" href="#overview"></a>개요</h2>

<p>Azure Site Recovery의 InMage는 VMWare 사이트 간의 실시간 복제 기능을 제공합니다. InMage는 Azure Site Recovery 서비스에 대한 구독에 포함되어 있습니다.</p>




<h2><a id="before" name="before" href="#before"></a>필수 조건</h2>
<div class="dev-callout"> 

<UL>
<LI><b>Azure 계정</b> - Azure 계정이 필요합니다. 없는 경우에는 <a href="http://aka.ms/try-azure">Azure 무료 평가판</a>을 참조하세요. 구독 가격 정보는 <a href="http://go.microsoft.com/fwlink/?LinkId=378268">Azure Site Recovery 관리자 가격 정보</a>에서 확인할 수 있습니다.</LI>
</UL>


<h2><a id="tutorial" name="tutorial" href="#tutorial"></a>학습 단계</h2>
<a name="vault"></a> <h3>1단계: 자격 증명 모음 만들기 및 InMage 다운로드</h3>

1. [관리 포털](https://manage.windowsazure.com)에 로그인합니다.


2. <b>데이터 서비스</b>, <b>복구 서비스</b>를 차례로 확장하고 <b>사이트 복구 자격 증명 모음</b>을 클릭합니다.


3. <b>새로 만들기</b>를 클릭한 후 <b>빠른 생성</b>을 클릭합니다.
	
4. <b>이름</b>에 자격 증명 모음을 식별하기 위한 이름을 입력합니다.

5. <b>지역</b>에서 자격 증명 모음에 대한 지리적 지역을 선택합니다. 지원되는 지역을 확인하려면 <a href="http://go.microsoft.com/fwlink/?LinkId=389880">Azure Site Recovery 가격 정보</a>에서 지리적 가용성을 참조하세요.

6. <b>자격 증명 모음 만들기</b>를 클릭합니다. 

	![New Vault](./media/hyper-v-recovery-manager-configure-vault/SRSAN_HvVault.png)

<P>상태 표시줄을 점검하여 자격 증명 모음이 성공적으로 만들어졌는지 확인합니다. 자격 증명 모음은 기본 복구 서비스 페이지에서 <b>활성</b>으로 나열됩니다.</P>

<a name="upload"></a> <h3>2단계: 자격 증명 모음 구성</h3>


1. <b>복구 서비스</b> 페이지에서 자격 증명 모음을 클릭하여 빠른 시작 페이지를 엽니다. 빠른 시작은 언제든지 아이콘을 사용하여 열 수도 있습니다.

	![Quick Start Icon](./media/hyper-v-recovery-manager-configure-vault/SRSAN_QuickStartIcon.png)

2. 드롭다운 목록에서 **Between two on-premises VMWare sites(두 개의 온-프레미스 VMWare 사이트 간)**를 선택합니다.
3. InMage Scout를 다운로드합니다.
	
	![VMWare](./media/hyper-v-recovery-manager-configure-vault/SRVMWare_SelectScenario.png)

4. 제품과 함께 다운로드한 InMage 설명서를 이용하여 두 VMWare 사이트 간 복제를 설정합니다.


<h2><a id="next" name="next" href="#next"></a>다음 단계</h2>
<UL>

<LI>궁금한 사항은 <a href="http://go.microsoft.com/fwlink/?LinkId=313628">Azure 복구 서비스 포럼</a>을 참조하세요.</LI> 
</UL>

<!--HONumber=49-->