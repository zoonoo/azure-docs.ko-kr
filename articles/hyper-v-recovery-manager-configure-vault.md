<properties linkid="configure-hyper-v-recovery-vault" urlDisplayName="configure-Azure-Site-Recovery" pageTitle="Getting Started with Azure Site Recovery: On-Premises to On-Premises Protection" metaKeywords="Azure Site Recovery, VMM, clouds, disaster recovery" description="Azure Site Recovery coordinates the replication, failover and recovery of Hyper-V virtual machines located in on-premises VMM clouds to another on-premises site." metaCanonical="" umbracoNaviHide="0" disqusComments="1" title="Getting Started with Azure Site Recovery: On-Premises to On-Premises Protection" editor="jimbe" manager="cfreeman" authors="raynew" />

<tags ms.service="site-recovery" ms.workload="backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="raynew" />

# Azure 사이트 복구 시작하기: 온-프레미스에서 온-프레미스로 보호

<div class="dev-callout">

<p>Azure 사이트 복구를 사용하여 VMM 클라우드에 위치한 Hyper-V 호스트 서버에 있는 가상 컴퓨터의 보호를 오케스트레이션하십시오. 다음을 구성할 수 있습니다.<p>

<ul>
<li><b>온-프레미스에서 온-프레미스로 보호</b>—온-프레미스 가상 컴퓨터를 다른 온-프레미스 사이트에 복제합니다. Azure 사이트 복구 자격 증명 모음에서 보호 설정을 구성하고 사용하도록 설정합니다. 가상 컴퓨터 데이터가 한 온-프레미스 Hyper-V 서버에서 다른 온-프레미스 Hyper-V 서버로 복제됩니다. 이 시나리오에 대해서는 <a href="http://go.microsoft.com/fwlink/?LinkId=398765">Azure 사이트 복구 시작하기: 온-프레미스에서 온-프레미스로 보호</a>에서 알아보십시오.
-   </li><li><b>온-프레미스에서 Azure로 보호</b>—온-프레미스 가상 컴퓨터를 Azure에 복제합니다. Azure 사이트 복구 자격 증명 모음에서 보호 설정을 구성하고 사용하도록 설정합니다. 가상 컴퓨터 데이터가 온-프레미스 Hyper-V 서버에서 Azure 저장소로 복제됩니다. 이 시나리오에 대해서는 <a href="http://go.microsoft.com/fwlink/?LinkId=398764">Azure 사이트 복구 시작하기: 온-프레미스에서 Azure로 보호</a>에서 알아보십시오.</li>

</ul>




<h2><a id="about"></a>이 자습서 정보</h2>


<P>이 자습서를 사용하여 온-프레미스에서 Azure로 배포할 때 Azure 사이트 복구의 빠른 개념 증명을 수립하십시오. 가능한 가장 빠른 경로와 기본 설정을 사용하고 있습니다. Azure 사이트 복구 자격 증명 모음을 만들고, 소스 VMM 서버에서 Azure 사이트 복구 공급자를 설치하고, 클라우드 보호 설정을 구성하고, 가상 컴퓨터에 대한 보호를 사용하도록 설정하고, 배포를 테스트하게 될 것입니다.</P>

<P>전체 배포에 대한 정보를 원하면 다음을 참조하십시오.</P>

<UL>
<LI><a href="http://go.microsoft.com/fwlink/?LinkId=321294">Azure 사이트 복구 배포 계획</a>—전체 배포를 시작하기 전에 완료해야 하는 계획 단계를 설명합니다.
</LI>
<LI><a href="http://go.microsoft.com/fwlink/?LinkId=321295">Azure 사이트 복구 배포: 온-프레미스에서 온-프레미스로 보호</a>—전체 배포를 위한 단계별 지침을 제공합니다.</LI>
</UL>
<P>
이 자습서를 수행하는 동안 문제가 발생하면 위키 문서 <a href="http://go.microsoft.com/fwlink/?LinkId=389879">Azure 사이트 복구: 일반 오류 시나리오 및 해결 방법</a>을 살펴보거나 <a href="http://go.microsoft.com/fwlink/?LinkId=313628">Azure 복구 서비스 포럼</a>(영문)에 문의 사항을 게시하십시오.</P>

</div>


<h2><a id="before"></a>시작하기 전에</h2> 
<div class="dev-callout"> 
<P>이 자습서를 시작하기 전에 필수 구성 요소를 확인하십시오.</P>

<h3><a id="HVRMPrereq"></a>필수 조건</h3>


<UL>
<LI><b>Azure 계정</b>—Azure 계정이 필요합니다. 없는 경우에는 <a href="http://aka.ms/try-azure">Azure 무료 평가판</a>을 참조하십시오. 가격 정보는 <a href="http://go.microsoft.com/fwlink/?LinkId=378268">Azure 사이트 복구 관리자 가격 정보</a>에서 확인할 수 있습니다.</LI>
<LI><b>VMM 서버</b>—System Center 2012 SP1 또는 System Center 2012 R2에서 실행 중인 VMM 서버가 최소한 하나 필요합니다.</LI>
<LI><b>VMM 클라우드</b>—보호할 원본 VMM 서버, 그리고 대상 VMM 서버에 클라우드가 적어도 하나씩 있어야 합니다. 하나의 VMM 서버만 실행 중인 경우 두 개의 클라우드가 있어야 합니다. 보호하려는 기본 클라우드는 다음을 포함해야 합니다.<UL>
	<LI>하나 이상의 VMM 호스트 그룹</LI>
	<LI>각 호스트 그룹에 있는 하나 이상의 Hyper-V 호스트 서버 또는 클러스터</LI>
	<li>클라우드의 원본 Hyper-V 서버에 위치한 하나 이상의 가상 컴퓨터</li>
		</UL></LI>



<h2><a id="tutorial"></a>학습 단계</h2>

필수 구성 요소를 확인한 후 다음을 수행합니다.

<UL>
<LI><a href="#vault">1단계: 자격 증명 모음 만들기</a>—Azure 사이트 복구 자격 증명 모음을 만듭니다.</LI>
<LI><a href="#download">2단계: 공급자 응용 프로그램 설치</a>—등록 키를 생성하고, VMM 서버에서 Microsoft Azure 사이트 복구 공급자 응용 프로그램을 실행합니다. 그러면 공급자가 설치되고 자격 증명 모음에서 VMM 서버가 등록됩니다.</LI>
<LI><a href="#clouds">[3단계: 클라우드 보호 구성</a>—VMM 클라우드의 보호 설정을 구성합니다.</LI>
<LI><a href="#networkmapping">5단계: 네트워크 매핑 구성—원본 VM 네트워크를 대상 VM 네트워크에 매핑하도록 네트워크 매핑을 선택적으로 구성할 수 있습니다.</LI>
<LI><a href="#storagemapping">6단계: 저장소 매핑 구성</a>—원본 VMM 서버의 저장소 분류를 대상 서버의 저장소 분류에 매핑하도록 저장소 매핑을 선택적으로 구성할 수 있습니다.</LI>
<LI><a href="#enablevirtual">7단계: 가상 컴퓨터에 보호 사용</a>—보호되는 VMM 클라우드에 있는 가상 컴퓨터에 보호 기능을 사용합니다.</LI>
<LI><a href="#recovery plans">8단계: 복구 계획 구성 및 실행</a>—복구 계획을 만들고 계획에 대한 테스트 장애 조치(Failover)를 실행합니다.</LI>

</UL>




<a name="vault"></a> <h2>1단계: 자격 증명 모음 만들기</h2>

1.  [관리 포털][관리 포털]에 로그인합니다.

2.  <b>데이터 서비스</b>를 확장하고, <b>복구 서비스</b>를 확장하고, <b>사이트 복구 자격 증명 모음</b>을 클릭합니다.

3.  <b>새로 만들기</b>를 클릭한 다음 <b>빠른 생성</b>을 클릭합니다.

4.  <b>이름</b>에 자격 증명 모음을 식별하기 위한 이름을 입력합니다.

5.  <b>지역</b>에서 자격 증명 모음에 대한 지리적 지역을 선택합니다. 지원되는 지역을 확인하려면 <a href="http://go.microsoft.com/fwlink/?LinkId=389880">Azure 사이트 복구 가격 정보</a>에서 지리적 가용성을 참조하십시오.

6.  <b>자격 증명 모음 만들기</b>를 클릭합니다.

    ![새 자격 증명 모음][새 자격 증명 모음]

<p>상태 표시줄을 점검하여 자격 증명 모음이 성공적으로 만들어졌는지 확인합니다. 자격 증명 모음은 기본 복구 서비스 페이지에서 <b>활성</b>으로 나열됩니다.</p>

<a name="upload"></a> <h2>2단계: 자격 증명 모음 구성</h2>

1.  <b>복구 서비스</b> 페이지에서 자격 증명 모음을 클릭하여 빠른 시작 페이지를 엽니다. 빠른 시작은 언제든지 아이콘을 사용하여 열 수도 있습니다.

    ![빠른 시작 아이콘][빠른 시작 아이콘]

2.  드롭다운 목록에서 **두 개의 온-프레미스 Hyper-V 사이트 사이**를 선택합니다.
3.  **VMM 서버 준비**에서 **등록 키 생성** 파일을 클릭합니다. 이 키는 생성된 날로부터 5일간 유효합니다. 파일을 VMM 서버에 복사합니다. 공급자를 설정할 때 필요합니다.

    ![등록 키][등록 키]

<a name="download"></a> <h2>3단계: Azure 사이트 복구 공급자 설치</h2>


1.  <b>빠른 시작</b> 페이지의 **VMM 서버 준비**에서 **VMM 서버에 설치할 Microsoft Azure 사이트 복구 공급자 다운로드**를 클릭하여 최신 버전의 공급자 설치 파일을 받습니다.

2.  원본 및 대상 VMM 서버에서 이 파일을 실행합니다.

3.  **사전 요구 사항 확인**에서 공급자 설정을 시작하기 위해 VMM 서비스를 중지하도록 선택합니다. 서비스가 중지되고 설정이 완료될 때 자동으로 다시 시작됩니다.

    ![필수 조건][필수 조건]

4.  **Microsoft 업데이트**에서 업데이트를 선택할 수 있습니다. 이 설정이 사용되면 공급자가 Microsoft 업데이트 정책에 따라 설치됩니다.

    ![Microsoft 업데이트][Microsoft 업데이트]

공급자가 설치된 후에는 설정을 계속 진행하여 자격 증명 모음에 서버를 등록합니다.

5.  VMM 서버에서 실행 중인 공급자를 인터넷에 연결하는 방법을 인터넷 연결 페이지에서 지정합니다. 서버에 구성된 기본 인터넷 연결 설정을 사용하려면 <b>기본 시스템 프록시 설정 사용</b>을 선택합니다.

    ![인터넷 설정][인터넷 설정]

6.  **등록 키**에서 Azure 사이트 복구를 다운로드하고 VMM 서버에 복사했다는 것을 선택합니다.
7.  **자격 증명 모음 이름**에서 서버를 등록할 자격 증명 모음의 이름을 확인합니다.
8.  자격 증명 모음에서 VMM 서버를 식별하기 위한 이름을 **서버 이름**에서 지정합니다.

    ![서버 등록][서버 등록]

9.  **초기 클라우드 메타데이터** 동기화에서 VMM 서버에 있는 모든 클라우드의 메타데이터를 자격 증명 모음과 동기화할 것인지 여부를 선택합니다. 이 작업은 각 서버에서 한 번만 수행해야 합니다. 모든 클라우드를 동기화하지 않는 경우 이 설정을 선택 취소된 상태로 두고 VMM 콘솔의 클라우드 속성에서 각 클라우드를 개별적으로 동기화할 수 있습니다.

7.  **데이터 암호화**에서, Azure에서 보호되는 데이터를 암호화하는 데 사용되는 인증서를 생성합니다.
    이 옵션은 한 온-프레미스를 다른 온-프레미스로 복제하는 경우 관계가 없습니다.

    ![서버 등록][1]

8.  <b>등록</b>을 클릭하여 프로세스를 완료합니다. 등록 후에 VMM 서버의 메타데이터가 Azure 사이트 복구에 의해 검색됩니다. 서버가 자격 증명 모음에 있는 <b>서버</b> 페이지의 **리소스** 탭 끝에 표시됩니다.


<h2><a id="clouds"></a>4단계: 클라우드 보호 설정 구성</h2>

VMM 서버가 등록되면 클라우드 보호 설정을 구성할 수 있습니다. VMM 서버에 있는 모든 클라우드가 **보호되는 항목** 탭에 나타나도록 공급자를 설치할 때 **클라우드 데이터를 자격 증명 모음과 동기화** 옵션을 설정했습니다.

![게시된 클라우드][게시된 클라우드]

1.  빠른 시작 페이지에서 **VMM 클라우드에 대해 보호 설정**을 클릭합니다.
2.  **보호되는 항목** 탭에서 구성할 클라우드를 선택하고 **구성** 탭으로 이동합니다. 다음 사항에 유의하십시오.
3.  <b>대상</b>에서 <b>VMM</b>을 선택합니다.
4.  <b>대상 위치</b>에서 복구에 사용할 클라우드를 관리하는 온-사이트 VMM 서버를 선택합니다.
5.  <b>대상 클라우드</b>에서 원본 클라우드의 가상 컴퓨터 장애 조치(Failover)에 사용할 대상 클라우드를 선택합니다. 다음 사항에 유의하십시오.

    -   보호할 가상 컴퓨터의 복구 요구 사항을 충족하는 대상 클라우드를 선택하는 것이 좋습니다.
    -   클라우드는 단일 클라우드 쌍에 기본 클라우드 또는 대상 클라우드로만 속할 수 있습니다.

6.  <b>복사 빈도</b>에서 기본 설정을 그대로 둡니다. 이 값은 원본 위치와 대상 위치 사이에 데이터를 동기화해야 하는 빈도를 지정합니다. 이 설정은 Hyper-V 호스트가 Windows Server 2012 R2를 실행 중일 경우에만 관련이 있습니다. 다른 서버의 경우에는 기본 설정인 5분이 사용됩니다.
7.  <b>추가 복구 지점</b>에서 기본 설정을 그대로 둡니다. 이 값은 추가 복구 지점을 만들지 여부를 지정합니다. 기본값인 0은 주 가상 컴퓨터의 가장 최근 복구 지점만 복제본 호스트 서버에 저장된다는 것을 지정합니다.
8.  <b>응용 프로그램에 일관된 스냅숏의 빈도</b>에서 기본 설정을 그대로 둡니다. 이 값은 스냅숏을 만드는 빈도를 지정합니다. 스냅숏은 VSS(볼륨 섀도 복사본 서비스)를 사용하여 스냅숏이 만들어질 때 응용 프로그램이 일관된 상태가 되도록 합니다. 자습서 연습에서는 이 값을 설정하지 않으려면 구성하는 추가 복구 지점의 수보다 적도록 설정되어야 합니다.

     ![보호 설정 구성][보호 설정 구성]

9.  <b>데이터 전송 압축</b>에서 전송되는 복제된 데이터를 압축할지 여부를 지정합니다.
10. <b>인증</b>에서 기본 및 복구 Hyper-V 호스트 서버 간에 트래픽을 인증하는 방법을 지정합니다. 작업 중인 Kerberos 환경이 구성되어 있지 않으면 이 연습용으로 HTTPS를 선택합니다. Azure 사이트 복구가 HTTPS 인증을 위한 인증서를 자동으로 구성합니다. 수동으로 구성할 필요가 없습니다. 이 설정은 Windows Server 2012 R2에서 실행 중인 Hyper-V 호스트 서버와만 관련이 있습니다.
11. <b>포트</b>에서 기본 설정을 그대로 둡니다. 이 값은 원본 및 대상 Hyper-V 호스트 컴퓨터가 복제 트래픽을 수신하는 포트 번호를 설정합니다.
12. <b>복제 방법</b>에서 정기 복제가 시작되기 전에 원본 위치에서 대상 위치로 데이터의 초기 복제를 처리할 방법을 지정합니다.

    -   <b>네트워크 이용</b>—네트워크를 이용하여 데이터를 복사하면 시간이 많이 걸리고 리소스가 많이 필요할 수 있습니다. 클라우드에 상대적으로 하드 디스크가 작은 가상 컴퓨터가 포함된 경우와 주 VMM 서버가 넓은 대역폭을 사용하여 보조 VMM 서버에 연결된 경우 이 옵션을 사용하는 것이 좋습니다. 복사를 즉시 시작하도록 지정하거나 시간을 선택할 수 있습니다. 네트워크 복제를 사용하는 경우 사용률이 낮은 시간에 예약하는 것이 좋습니다.
    -   <b>오프라인</b>—이 방법은 외부 미디어를 사용하여 초기 복제를 수행하도록 지정합니다. 이 방법은 네트워크 성능 저하를 방지하려는 경우나 지리적으로 멀리 떨어진 위치에 유용합니다. 이 방법을 사용하려면 원본 클라우드에서 내보내기 위치를 지정하고 대상 클라우드에서 가져오기 위치를 지정합니다. 가상 컴퓨터에 대한 보호를 설정할 때 가상 하드 디스크가 지정된 내보내기 위치에 복사됩니다. 이 하드 디스크를 대상 사이트에 보내고 가져오기 위치에 복사합니다. 시스템에서는 가져온 정보를 복제 가상 컴퓨터에 복사합니다. 오프라인 복제 필수 구성 요소의 전체 목록을 보려면 배포 가이드에서 <a href="http://go.microsoft.com/fwlink/?LinkId=323469">3단계: VMM 클라우드의 보호 설정 구성</a>을 참조하십시오.

13. 클라우드 속성의 가상 컴퓨터 탭에서 **가상 컴퓨터 보호 삭제** 옵션을 선택하여 가상 컴퓨터의 보호를 중지할 경우 복제 가상 컴퓨터가 삭제되도록 지정하려면 **복제 가상 컴퓨터 삭제**를 선택합니다. 이 설정이 사용되면 보호를 사용하지 않을 때 가상 컴퓨터가 Azure 사이트 복구에서 제거되고, 가상 컴퓨터의 사이트 복구 설정이 VMM 콘솔에서 제거되고, 복제본이 삭제됩니다.
    ![보호 설정 구성][2]

<p>설정을 저장하고 나면 작업이 생성되고 **작업** 탭에서 모니터링할 수 있습니다. VMM 원본 클라우드에 있는 모든 Hyper-V 호스트 서버가 복제 대상으로 구성됩니다. 클라우드 설정은 **구성** 탭에서 수정할 수 있습니다. 대상 위치 또는 대상 클라우드를 수정하려면 클라우드 구성을 제거한 후 클라우드를 다시 구성해야 합니다.</b>

<h2><a id="networkmapping"></a>5단계: 네트워크 매핑 구성</h2>

<p>이 자습서에서는 테스트 환경에서 Azure 사이트 복구를 배포하는 가장 간단한 경로를 설명합니다. 이 자습서를 진행하는 동안 네트워크 매핑을 구성하지 않으려면 계획 가이드에서 <a href="http://go.microsoft.com/fwlink/?LinkId=324817">네트워크 매핑 준비</a>를 읽으십시오. 매핑을 구성하려면 배포 가이드에 <a href="http://go.microsoft.com/fwlink/?LinkId=402534">네트워크 매핑 구성</a> 단계를 따르십시오.</p>

<h2><a id="storagemapping"></a>6단계: 저장소 매핑 구성</h2>

<p>이 자습서에서는 테스트 환경에서 Azure 사이트 복구를 배포하는 가장 간단한 경로를 설명합니다. 이 자습서를 진행하는 동안 저장소 매핑을 구성하지 않으려면 배포 가이드에 있는 <a href="http://go.microsoft.com/fwlink/?LinkId=402535">저장소 매핑 구성</a> 단계를 따르십시오.</p>

<h2><a id="enablevirtual"></a>7단계: 가상 컴퓨터 보호 사용</h2>
<p>서버, 클라우드 및 네트워크가 제대로 구성되었으면 클라우드에서 가상 컴퓨터에 대한 보호를 설정할 수 있습니다.</p>
<OL>
<li>가상 컴퓨터가 위치한 클라우드의 <b>가상 컴퓨터</b> 탭에서 <b>보호 사용</b>을 클릭한 다음 <b>가상 컴퓨터 추가</b>를 선택합니다. </li>
<li>클라우드에 있는 가상 컴퓨터의 목록에서 보호할 가상 컴퓨터를 선택합니다.</li> 
</OL>

![가상 컴퓨터 보호 사용][가상 컴퓨터 보호 사용]

<p><b>작업</b> 탭에서 초기 복제를 비롯하여 보호 사용 작업의 진행 상태를 추적합니다. 보호 완료 작업이 실행된 후에는 가상 컴퓨터가 장애 조치(Failover)를 수행할 준비가 되어 있습니다. 보호를 사용하도록 설정하고 가상 컴퓨터가 복제되고 나면 Azure에서 가상 컴퓨터를 볼 수 있습니다.</p>

![가상 컴퓨터 보호 작업][가상 컴퓨터 보호 작업]

<h2><a id="recoveryplans"></a>8단계: 배포 테스트</h2>

배포를 테스트하려면 단일 가상 컴퓨터에 대한 테스트 장애 조치(Failover)를 실행하거나, 여러 개의 가상 컴퓨터로 구성된 복구 계획을 만들고 이 계획에 대한 테스트 장애 조치(Failover)를 실행하면 됩니다. 테스트 장애 조치(Failover)에서는 격리된 네트워크에서 장애 조치(Failover) 및 복구 메커니즘을 시뮬레이션합니다.

<UL>
<li>복구 계획을 만드는 방법은 <a href="http://go.microsoft.com/fwlink/?LinkId=511492">복구 계획 만들기 및 사용자 지정: 온-프레미스에서 Azure로</a>를 참조하십시오.</li>
<li>테스트 장애 조치(Failover)를 실행하는 방법은 <a href="http://go.microsoft.com/fwlink/?LinkId=511493">온-프레미스에서 온-프레미스로의 배포 테스트</a>를 참조하십시오.</li>
</UL>

<h3><a id="runtest"></a>작업 모니터</h3>

<p><b>작업</b> 및 <b>대시보드</b> 탭을 사용하여 Azure 사이트 복구 자격 증명 모음에서 수행한 주 작업을 보고 모니터링할 수 있습니다. 이러한 작업에는 클라우드에 대한 보호 구성, 가상 컴퓨터의 보호 설정 및 해제, 장애 조치(Failover) 실행(계획됨, 계획되지 않음 또는 테스트), 계획되지 않은 장애 조치(Failover) 커밋이 포함됩니다.</p>

<p><b>작업</b> 탭에서 작업을 보고, 작업 세부 정보 및 오류를 드릴다운하고, 쿼리 작업을 실행하여 특정 조건과 일치하는 작업을 검색하고, 작업을 Excel로 내보내고, 실패한 작업을 다시 시작합니다.</p>

<p><b>대시보드</b>에서는 최신 버전의 공급자 및 에이전트 설치 파일을 다운로드하고, 자격 증명 모음에 대한 구성 정보를 가져오고, 자격 증명 모음에서 보호를 관리하는 가상 컴퓨터의 수를 확인하고, 최근 작업을 보고, 자격 증명 모음 인증서를 관리하고, 가상 컴퓨터를 재동기화할 수 있습니다.</p>

<p>작업 및 대시보드와의 상호 작용에 대한 자세한 내용은 <a href="http://go.microsoft.com/fwlink/?LinkId=398534">작업 및 모니터링 가이드</a>를 참조하십시오.</p>

<h2><a id="next"></a>다음 단계</h2><UL>
<LI>정식 프로덕션 환경에서 Azure 사이트 복구를 계획하고 배포하려면 <a href="http://go.microsoft.com/fwlink/?LinkId=321294">Azure 사이트 복구 배포 계획</a> 및 <a href="http://go.microsoft.com/fwlink/?LinkId=321295">Azure 사이트 복구 배포: 온-프레미스에서 온-프레미스로 보호</a>를 참조하십시오.</LI>
<LI>궁금한 사항은 <a href="http://go.microsoft.com/fwlink/?LinkId=313628">Azure 복구 서비스 포럼</a>을 참조하십시오.</LI> 
</UL>

  [관리 포털]: https://manage.windowsazure.com
  [새 자격 증명 모음]: ./media/hyper-v-recovery-manager-configure-vault/SR_HvVault.png
  [빠른 시작 아이콘]: ./media/hyper-v-recovery-manager-configure-vault/SR_QuickStartIcon.png
  [등록 키]: ./media/hyper-v-recovery-manager-configure-vault/SR_E2ERegisterKey.png
  [필수 조건]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderPrereq.png
  [Microsoft 업데이트]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderUpdate.png
  [인터넷 설정]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderProxy.png
  [서버 등록]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderRegKeyServerName.png
  [1]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderSyncEncrypt.png
  [게시된 클라우드]: ./media/hyper-v-recovery-manager-configure-vault/SR_CloudsList.png
  [보호 설정 구성]: ./media/hyper-v-recovery-manager-configure-vault/SR_CloudSettingsE2E.png
  [2]: ./media/hyper-v-recovery-manager-configure-vault/SR_CloudSettingsE2ERep.png
  [가상 컴퓨터 보호 사용]: ./media/hyper-v-recovery-manager-configure-vault/SR_EnableProtectionVM.png
  [가상 컴퓨터 보호 작업]: ./media/hyper-v-recovery-manager-configure-vault/SR_VMJobs.png
