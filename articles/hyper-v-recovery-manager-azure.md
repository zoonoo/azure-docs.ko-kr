<properties linkid="configure-hyper-v-recovery-vault" urlDisplayName="configure-Azure-Site-Recovery" pageTitle="Configure Azure Site Recovery to protect virtual machines on Hyper-V server located in VMM clouds" metaKeywords="Azure Site Recovery, VMM, clouds, disaster recovery" description="Azure Site Recovery coordinates the replication, failover and recovery of Hyper-V virtual machines located in VMM clouds from one on-premises site to another. Azure Site Recovery can also replicate, failover, and recover Hyper-V virtual machine data between VMM clouds and Microsoft Azure." metaCanonical="" umbracoNaviHide="0" disqusComments="1" title="Getting Started with Azure Site Recovery: On-Premises to Azure Protection" editor="jimbe" manager="cfreeman" authors="" />

<tags ms.service="site-recovery" ms.workload="backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author></tags>


# Azure 사이트 복구 시작: 온-프레미스와 Azure 간 보호



<div class="dev-callout"> 

<p>Azure 사이트 복구를 사용하여 System Center VMM(Virtual Machine Manager) 클라우드에 있는 Hyper-V 호스트에서 실행되는 가상 컴퓨터를 보호합니다. 다음을 구성할 수 있습니다.</p>

<ul>
<li><b>온-프레미스와 Azure 간 보호</b> - Hyper-V 호스트 서버에 있는 온-프레미스 가상 컴퓨터를 Azure에 복제합니다. Azure 사이트 복구 자격 증명 모음을 구성하고 사용하도록 설정합니다. 가상 컴퓨터 데이터가 온-프레미스 Hyper-V 서버에서 Azure 저장소로 복제됩니다.</li>

<li><b>온-프레미스 간 보호</b> - Hyper-V 호스트 서버에 있는 가상 컴퓨터를 온-프레미스 사이트 간에 복제합니다. Azure 사이트 복구 자격 증명 모음을 구성하고 사용하도록 설정합니다. 가상 컴퓨터 데이터가 온-프레미스 Hyper-V 서버 간에 복제됩니다. Azure 사이트 복구는 단순히 프로세스를 오케스트레이션합니다.
<a href="http://go.microsoft.com/fwlink/?LinkId=398765">Azure 사이트 복구 시작: 온-프레미스 간 보호</a>에서 제공됩니다.</li>

</ul>
 
<h2><a id="about"></a>이 자습서 정보</h2>


<P>이 자습서를 통해 Azure 사이트 복구를 배포하여 개념을 빠르게 살펴볼 수 있습니다. 이 자습서에서는 가능하면 가장 빠른 경로와 기본 설정을 사용합니다. 다음 단계가 포함됩니다.
<ul>
<li>Azure 사이트 복구 자격 증명 모음 설정 - 자격 증명 모음에 업로드된 인증서를 가져오고, 원본 VMM 서버에서 설정하고, 자격 증명 모음 키를 생성합니다. </li>
<li>원본 VMM 서버 및 Hyper-V 호스트 서버 설정 - 원본 VMM 서버에서 Azure 사이트 복구를 설치하고 Hyper-V 호스트 서버에서 Azure 복구 서비스 에이전트를 설치합니다.</li>
<li>VMM 클라우드 구성 - 원본 VMM 서버에서 클라우드에 대한 보호 설정을 구성합니다.</li>
<li>가상 컴퓨터 사용 - 가상 컴퓨터에 대한 보호를 사용합니다.</li>
<li>장애 조치(Failover) 실행 - 복구 계획을 만들고 테스트 장애 조치(Failover)를 실행합니다.</li>
</ul>


전체 배포에 대한 자세한 내용은 다음을 참조하세요.</P>

<ul>
<li><a href="http://go.microsoft.com/fwlink/?LinkId=321294">Azure 사이트 복구 배포 계획</a> - 전체 배포를 시작하기 전에 완료해야 하는 계획 단계를 설명합니다.</LI>
<li><a href="http://go.microsoft.com/fwlink/?LinkId=402679">Azure 사이트 복구 배포: 온-프레미스와 Azure 간 보호</a> - 전체 배포에 대한 단계별 지침을 제공합니다.</li>
<li><a href="http://go.microsoft.com/fwlink/?LinkId=378272">Azure 사이트 복구 관리 및 모니터링</a> - 장애 조치(Failover)를 실행하고 배포를 관리 및 모니터링하는 방법을 설명합니다.</li>
</ul>
<P>이 자습서를 진행하는 동안 문제가 발생하면 Wiki 문서 <a href="http://go.microsoft.com/fwlink/?LinkId=389879">Azure 사이트 복구: 일반 오류 시나리오 및 해결 방법</a>을 살펴보거나 <a href="http://go.microsoft.com/fwlink/?LinkId=313628">Azure 복구 서비스 포럼</a>(영문)에 문의 사항을 게시하세요.</P>

</div>


<h2><a id="before"></a>Before you begin</h2> 
<div class="dev-callout"> 
<P>이 자습서를 시작하기 전에 필수 구성 요소를 확인하세요.</P>

<h3><a id="HVRMPrereq"></a>Azure 필수 조건</h3>

<ul>
<li><b>Azure 계정</b> - Azure 계정이 필요합니다. 아직 계정이 없으면 <a href="http://aka.ms/try-azure">Azure 무료 평가판</a>을 참조하세요. <a href="http://go.microsoft.com/fwlink/?LinkId=378268">Azure 사이트 복구 관리자 가격 정보</a>(영문)에서 가격 정보를 확인하세요.</li>
<li><b>인증서</b> - 공개 키가 포함된 관리 인증서(.cer)를 자격 증명 모음에 업로드해야 합니다. 이 인증서를 .pfx 파일(개인 키 포함)로 내보내고 자격 증명 모음에 등록하려는 각 VMM 서버로 가져옵니다. 이 자습서에서는 자체 서명된 인증서를 사용합니다. 전체 배포에는 계획 가이드에 설명된 <a href="http://go.microsoft.com/fwlink/?LinkId=321294">인증서 요구 사항</a>을 준수하는 유효한 SSL 인증서를 사용할 수 있습니다</a>.</li>


</li>

<li><b>Azure 저장소 계정</b> - Azure로 복제된 데이터를 저장하려면 Azure 저장소 계정이 필요합니다. 계정의 지역에서 복제 기능을 사용하도록 설정해야 합니다. 계정은 Azure 사이트 복구 서비스와 같은 지역에 있고 같은 구독과 연결되어야 합니다. Azure 저장소에 대한 자세한 내용은 <a href="http://go.microsoft.com/fwlink/?LinkId=398704">Microsoft Azure 저장소 소개</a>를 참조하세요.</li>
</ul>

<h3><a id="VMMPrereq"></a>VMM 필수 구성 요소</h3>

<ul>
<li><b>VMM 서버</b> - System Center 2012 R2에서 실행되는 VMM 서버</li>
<li><b>VMM 클라우드</b> - VMM 서버에 있는 클라우드 하나 이상. 클라우드에 다음이 포함되어야 합니다.
    <ul>
    <li>하나 이상의 VMM 호스트 그룹</li>
    <li>각 호스트 그룹에 있는 Hyper-V 호스트 서버 또는 클러스터 하나 이상</li>
    <li>클라우드의 원본 Hyper-V 서버에 있는 가상 컴퓨터 하나 이상. 가상 컴퓨터는 1세대 가상 컴퓨터여야 합니다.</li>
        </ul></li>  
</ul>

<h3><a id="VMPrereq"></a>가상 컴퓨터 필수 조건</h3>

<ul>
<li><b>세대</b> - Azure에서는 1세대 가상 컴퓨터만 지원합니다.</li>
<li>Azure로의 장애 조치(Failover)를 위한 전체 가상 컴퓨터 지원 요구 사항 목록은 계획 가이드에서 <a href="http://go.microsoft.com/fwlink/?LinkId=402602">필수 조건 및 지원</a>을 확인하세요. </li>  
</ul>

<h2><a id="tutorial"></a>학습 단계</h2> 

필수 구성 요소를 확인한 후 다음을 수행합니다.
<ul>
<li><a href="#createcert">1단계: 인증서 얻기 및 구성</a> - .cer 인증서를 얻어 .pfx 파일로 내보내고 내보낸 .pfx 파일을 VMM 서버로 가져옵니다.</li>
<li><a href="#vault">2단계: 자격 증명 모음 만들기</a> - Azure 사이트 복구 자격 증명 모음을 만듭니다.</li>
<li><a href="#upload">3단계: 자격 증명 모음 구성</a> - 관리 인증서를 자격 증명 모음에 업로드하고 자격 증명 모음용 키를 생성합니다. 이 키는 VMM 서버에 있는 공급자가 Azure 사이트 복구에서 보낸 명령만 실행하도록 하는 데 사용됩니다.</li>
<li><a href="#download">4단계: 공급자 응용 프로그램 설치</a> - VMM 서버에서 Microsoft Azure 사이트 복구 공급자 응용 프로그램을 실행합니다. 공급자가 설치되고 VMM 서버가 자격 증명 모음에 등록됩니다.</li>
<li><a href="#agent">5단계: 에이전트 응용 프로그램 설치</a> - 각 Hyper-V 호스트에 Microsoft Azure 복구 서비스 에이전트를 설치합니다.</li>
<li><a href="#clouds">6단계: 클라우드 보호 구성</a> - VMM 클라우드의 보호 설정을 구성합니다.</li>
<li><a href="#NetworkMapping">7단계: 네트워크 매핑 구성</a> - 선택적으로 원본 VM 네트워크를 대상 Azure 네트워크에 매핑하도록 네트워크 매핑을 구성할 수 있습니다.</li>
<li><a href="#virtualmachines">8단계: 가상 컴퓨터에 보호 사용</a> - 보호된 VMM 클라우드에 있는 가상 컴퓨터에 보호 기능을 사용합니다.</li>
<li><a href="#recovery plans">9단계: 복구 계획 구성 및 실행</a> - 복구 계획을 만들고 계획에 대한 테스트 장애 조치(Failover)를 실행합니다.</li>
</ul>



<a name="createcert"></a><h2> 1단계: 인증서 얻기 및 구성</h2>

아래와 같이 인증서를 얻고 구성합니다.
<ul>
<li><a href="#obtaincert">연습용 자체 서명된 인증서 얻기</a> - MakeCert 도구를 사용하여 인증서를 얻습니다.</li>
<li><a href="#exportcert">.pfx 형식으로 인증서 내보내기</a> - 인증서를 만들었던 서버에서 .cer 파일을 .pfx 파일로 내보냅니다(개인 키 포함). </li>
<li><a href="#importcert">VMM 서버로 .pfx 인증서 가져오기</a> - .pfx 파일을 내보내고 나서 자격 증명 모음에 등록하려는 VMM 서버에 있는 로컬 컴퓨터 저장소의 개인 폴더로 가져옵니다.</li>
</ul>


<h3><a id="obtaincert"></a>자체 서명된 인증서(.cer) 얻기</h3>
<P>모든 인증서 요구 사항을 준수하는 .cer x.509 인증서를 만듭니다.</P>
<ul>
<li>
MakeCert를 실행하려는 컴퓨터에 최신 버전의 <a href="http://go.microsoft.com/fwlink/?LinkId=378269">Windows SDK</a>를 다운로드합니다. 전체 SDK를 설치할 필요는 없습니다.</li>
<ul>
<li>위치 지정 페이지에서 <b>이 컴퓨터에 Windows Software Development Kit for Windows 8.1 설치</b>를 선택합니다.</li>
<li>설치하려는 기능을 선택하세요. 페이지에서 <b>Windows SDK(소프트웨어 개발 키트)</b>를 제외한 다른 모든 옵션의 선택을 취소합니다.</li>
<li>설치를 완료한 후 makecert.exe가 C:\ProgramFiles (x86)\Windows Kits\<i>WindowsVersion</i>\bin\x64 폴더에 나타나는지 확인합니다.</li>
<li>관리자 권한으로 명령 프롬프트(cmd.exe)를 열고 makecert.exe 폴더로 이동합니다.</li> 
<li>다음 명령을 실행하여 자체 서명된 인증서를 만듭니다. CertificateName을 인증서에 사용할 이름으로 바꾸고, -e 다음에 인증서의 실제 만료 날짜를 지정합니다.</li>
<code>
makecert.exe -r -pe -n CN=CertificateName -ss my -sr localmachine -eku 1.3.6.1.5.5.7.3.2 -len 2048 -e 01/01/2016 CertificateName.cer</code>
</ul>
<P>성공 상태는 인증서가 만들어졌음을 나타냅니다. makecert.exe와 같은 폴더에 저장됩니다. 내보내기를 위해 더 액세스하기 쉬운 위치로 인증서를 이동할 수 있습니다.</P>

<h3><a id="exportcert"></a>.pfx 형식으로 인증서 내보내기</h3>
<P>이 절차의 단계를 완료하여 .cer 파일을 .pfx 형식으로 내보냅니다.</P>
<ul>
<li>성공 상태는 인증서가 만들어졌음을 나타냅니다. makecert.exe와 같은 폴더에 저장됩니다. 내보내기를 위해 더 액세스하기 쉬운 위치로 인증서를 이동할 수 있습니다.</li>
<li>세부 정보 창에서 관리할 인증서를 클릭합니다.</li>
<li><b>작업</b> 메뉴에서 <b>모든 작업</b>을 가리키고 <b>내보내기</b>를 클릭합니다. 인증서 내보내기 마법사가 나타납니다. <b>다음</b>을 클릭합니다.</li>
<li><b>개인 키 내보내기</b> 페이지에서 <b>예</b>를 클릭하여 개인 키를 내보냅니다. <b>다음</b>을 클릭합니다. 이 작업은 설치 후 개인 키를 다른 서버로 내보낼 경우에만 필요합니다.</li>
<li>파일 내보내기 형식 페이지에서 <b>개인 정보 교환 &ndash; PKCS #12(.PFX)</b>를 선택합니다. <b>다음</b>을 클릭합니다.</li>
<li><b>암호</b> 페이지에서 개인 키를 암호화하는 데 사용되는 암호를 입력하고 확인합니다. <b>다음</b>을 클릭합니다.</li>
<li>마법사 페이지에 따라 인증서를 .pfx 형식으로 내보냅니다.</li>
</ol>

<h3><a id="importcert"></a>VMM 서버로 .pfx 인증서 가져오기</h3>
<p>내보낸 후 .pfx를 VMM 서버로 복사하고 나서 가져옵니다. VMM 서버에서 MakeCert.exe를 실행한 경우에는 해당 서버에 인증서를 가져올 필요가 없습니다.</p>
 
<ul>
<li>개인 키(.pfx) 인증서 파일을 로컬 서버의 위치에 복사합니다.</li>
<li>인증서 MMC 스냅인에서 <b>컴퓨터 계정</b>을 선택한 후 <b>다음</b>을 클릭합니다.</li>
<li><b>로컬 컴퓨터</b>를 선택하고 <b>마침</b>을 클릭합니다.</li>
<li>MMC에서 <b>인증서</b>를 확장하고, <b>개인</b>을 마우스 오른쪽 단추로 클릭하고, <b>모든 작업</b>을 가리킨 후 <b>가져오기</b>를 클릭하여 인증서 가져오기 마법사를 시작합니다.</li>
<li>가져올 파일 페이지에서 <b>찾아보기</b>를 클릭하고 가져올 인증서가 포함된 .pfx 인증서 파일이 있는 폴더를 찾습니다. 적합한 파일을 선택한 다음 <b>열기</b>를 클릭합니다.</li>
<li>암호 페이지에서 <b>암호</b> 상자에 개인 키 .pfx 파일의 암호를 입력하고 <b>다음</b>을 클릭합니다.</li>
<li>인증서 저장소 페이지에서 <b>모든 인증서를 다음 저장소에 저장</b>을 선택하고, <b>찾아보기</b>를 클릭하고, <b>개인</b> 저장소를 선택하고, <b>확인</b>을 클릭한 후 <b>다음</b>을 클릭합니다. 마법사를 완료합니다.</li>
</ul>

이 단계를 완료하면 자격 증명 모음을 구성할 때 업로드할 .cer 인증서를 선택할 수 있고, 공급자 설치 중 VMM 서버를 등록할 때 .pfx 인증서를 선택할 수 있습니다.
</div>


<a name="vault"></a><h2> 2단계: 자격 증명 모음 만들기</h2>

1. [관리 포털][관리 포털]에 로그인합니다.


2. **데이터 서비스**, **복구 서비스**를 차례로 확장하고 **사이트 복구 자격 증명 모음**을 클릭합니다.

3. **새로 만들기**, **빠른 생성**을 차례로 클릭합니다.


4. **이름**에 자격 증명 모음을 식별하기 위한 이름을 입력합니다.

5. **지역**에서 자격 증명 모음에 대한 지리적 지역을 선택합니다. 사용 가능한 지리적 지역에는 동아시아, 서유럽, 미국 서부, 미국 동부, 북유럽, 동남아시아
6. **자격 증명 모음 만들기**를 클릭합니다.

    ![새 자격 증명 모음][새 자격 증명 모음]

상태 표시줄을 확인하여 자격 증명 모음이 만들어졌는지 확인합니다. 자격 증명 모음은 주 복구 서비스 페이지에 **활성**으로 나열됩니다.

<a name="upload"></a> 3단계: 자격 증명 모음 구성</h2>


1. **복구 서비스** 페이지에서 자격 증명 모음을 클릭하여 빠른 시작 페이지를 엽니다. 언제든지 아이콘을 사용하여 빠른 시작을 열 수 있습니다.

    ![빠른 시작 아이콘][빠른 시작 아이콘]

2. **복구 설정** 드롭다운 목록에서 **온-프레미스 사이트와 Microsoft Azure 간**을 선택합니다.
3. 인증서(.cer)를 자격 증명 모음으로 업로드하려면 **인증서 관리**를 클릭합니다.

    ![빠른 시작][빠른 시작]

4. **인증서 관리** 대화 상자에서 **파일 찾아보기**를 클릭하고 .cer 파일을 선택합니다.


    ![인증서 관리][인증서 관리]

5. 자격 증명 모음용 키를 생성하려면 **자격 증명 모음 키 얻기**를 클릭합니다. 키가 자동으로 생성됩니다. 키를 다시 생성하면 기존 키를 덮어씁니다. VMM 서버에 Azure 사이트 복구 공급자를 설치할 때 이 키가 필요합니다.

    ![인증서 관리][1]


<a name="download"></a> 4단계: Azure 사이트 복구 공급자 설치 </h2>


1. **빠른 시작** 페이지에서 **공급자 다운로드**를 클릭하여 최신 버전의 공급자 설치 파일을 받습니다.

    ![공급자 파일 다운로드][공급자 파일 다운로드]

2. 원본 VMM 서버에서 이 파일을 실행합니다.

    ![다운로드 에이전트][다운로드 에이전트]

3. 공급자가 설치되고 나면 설정을 계속해서 서버를 자격 증명 모음에 등록합니다.
    ![설치 완료][설치 완료]
4. VMM 서버에서 실행 중인 공급자를 인터넷에 연결하는 방법을 인터넷 연결 페이지에서 지정합니다. **다음**을 클릭하여 서버에 구성된 기본 인터넷 연결 설정을 사용합니다.
    ![인터넷 설정][인터넷 설정]
5. 자격 증명 모음 등록 페이지에서 다음을 수행합니다.
	<ul>
	<li>VMM 서버로 가져온 개인 키(.pfx)를 선택합니다.</li>
	<li>서버를 등록할 자격 증명 모음을 선택합니다.</li>
	<li>자격 증명 모음 키를 지정합니다. 이 키는 앞에서 생성한 자격 증명 모음 키입니다. 빠른 시작 페이지에서 키 값을 잘라내어 붙여넣습니다.</li>
	</ul>
	
	![인증서 등록][인증서 등록]

6. 데이터 암호화 페이지에서 특정 클라우드에 대한 복제 중에 데이터를 암호화하는 옵션을 허용할지를 지정합니다. 이 옵션을 선택하면 SSL 인증서가 자동으로 생성됩니다. 장애 조치(Failover)를 실행할 때 이 인증서를 선택해야 합니다. 이 설정을 사용하도록 설정하고 나서 Azure 사이트 복구 포털에서 클라우드에 대해 데이터 암호화를 사용하거나 사용하지 않도록 설정할 수 있습니다. 이 자습서에 대한 기본 설정을 그대로 두고 **다음**을 클릭합니다.

    ![인증서 자격 증명 모음][인증서 자격 증명 모음]

7. VMM 서버 페이지에서 다음을 수행합니다.

	- VMM 서버의 식별 이름을 지정합니다. 이 이름은 Azure 사이트 복구 콘솔에서 서버를 식별하는 데 사용됩니다.
	- **클라우드 메타데이터를 자격 증명 모음과 동기화**를 선택하여 VMM 클라우드에 대한 정보를 Azure 사이트 복구 자격 증명 모음과 동기화합니다. 이 작업은 각 서버에서 한 번만 수행해야 합니다. 모든 클라우드를 동기화하지 않는 경우 클라우드 보호 설정을 구성하기 전에 각 클라우드를 개별적으로 게시하여 동기화할 수 있습니다.

8. **등록**을 클릭하여 프로세스를 완료합니다.

    ![PublishCloud][PublishCloud]


<p>서버가 성공적으로 등록되면 자격 증명 모음에서 서버 페이지의 **리소스** 탭에 서버 이름이 표시됩니다.</p>

<h2><a id="storage"></a> 5단계: Azure 복구 서비스 에이전트 설치</h2>

<p>보호할 VMM 클라우드에 있는 각 Hyper-V 호스트 서버에 Azure 복구 서비스 에이전트를 설치합니다.</p>

1. 빠른 시작 페이지에서 **Azure 사이트 복구 서비스 에이전트 다운로드 및 호스트에 설치**를 클릭하여 최신 버전의 에이전트 설치 파일을 받습니다.

    ![복구 서비스 에이전트 설치][복구 서비스 에이전트 설치]

2. 보호할 VMM 클라우드에 있는 각 Hyper-V 호스트 서버에서 설치 파일을 실행합니다.
3. 필수 조건 확인 페이지에서 **다음**을 클릭합니다. 누락된 필수 조건은 자동으로 설치됩니다.

![필수 조건 복구 서비스 에이전트][필수 조건 복구 서비스 에이전트]

1. 설치 설정 페이지에서 에이전트를 설치할 위치를 지정하고 백업 메타데이터를 설치할 캐시 위치를 선택합니다. **설치**를 클릭합니다.

VMM 서버에서 실행 중인 공급자를 인터넷에 연결하는 방법을 인터넷 연결 페이지에서 지정합니다. **다음**을 클릭하여 서버에 구성된 기본 인터넷 연결 설정을 사용합니다. ![복구 서비스 에이전트 위치][복구 서비스 에이전트 위치]


<h2><a id="clouds"></a>6단계: 클라우드 보호 설정 구성</h2>

VMM 서버가 등록되면 클라우드 보호 설정을 구성할 수 있습니다. 공급자를 설치할 때 **클라우드 데이터를 자격 증명 모음과 동기화** 옵션을 사용하도록 설정했으므로 VMM 서버의 모든 클라우드가 자격 증명 모음의 **보호된 항목** 탭에 나타납니다.


![게시된 클라우드][게시된 클라우드]


다음과 같이 보호 설정을 구성합니다.


1. 빠른 시작 페이지에서 **VMM 클라우드에 대한 보호 설정**을 클릭합니다.

![클라우드 보호 사용][클라우드 보호 사용]

1. <b>보호된 항목</b> 탭에서 구성할 클라우드를 클릭하고 <b>구성</b> 탭으로 이동합니다.
2. <b>대상</b>에서 <b>Microsoft Azure</b>를 선택합니다.
3. <b>저장소 계정**에서 Azure 가상 컴퓨터를 저장하는 데 사용할 Azure 저장소를 선택합니다.
4. <b>저장된 데이터 암호화</b>를 <b>끄기</b>로 설정합니다. 이 설정은 온-프레미스 사이트와 Azure 간에 복제된 데이터를 암호화하도록 지정합니다.
5. <b>복사 빈도</b>에서 기본 설정을 그대로 둡니다. 이 값은 원본 위치와 대상 위치 간에 데이터를 동기화해야 하는 빈도를 지정합니다.
6. <b>복구 지점 유지</b>에서 기본 설정을 그대로 둡니다. 기본값인 0인 경우에는 주 가상 컴퓨터의 가장 최근 복구 지점만 복제본 호스트 서버에 저장됩니다.
7. <b>응용 프로그램에 일관된 스냅숏의 빈도</b>에서 기본 설정을 그대로 둡니다. 이 값은 스냅숏을 만드는 빈도를 지정합니다. 스냅숏은 VSS(볼륨 섀도 복사본 서비스)를 사용하여 스냅숏이 만들어질 때 응용 프로그램이 일관된 상태가 되도록 합니다. 값을 설정할 경우 구성할 추가 복구 지점 수보다 적은지 확인하세요.

    ![클라우드 구성][클라우드 구성]

8. <b>복제 시작 시간</b>에서 Azure로 데이터 초기 복제를 시작하는 시간을 지정합니다. Hyper-V 호스트 서버의 시간대가 사용됩니다. 초기 복제는 사용률이 낮은 시간에 예약하는 것이 좋습니다.

    ![클라우드 복제 설정][클라우드 복제 설정]

<p>설정을 저장하고 나면 작업이 생성되고 <b>작업</b> 탭에서 모니터링할 수 있습니다. VMM 원본의 모든 Hyper-V 호스트 서버가 복제에 대해 구성됩니다. 

저장하고 나면 클라우드 설정은 <b>구성</b> 탭에서 수정할 수 있습니다. 대상 위치 또는 대상 저장소를 수정하려면 클라우드 구성을 제거한 후 클라우드를 다시 구성해야 합니다. 저장소 계정을 변경하면 변경 내용은 저장소 계정이 수정된 후에 보호하도록 설정된 가상 컴퓨터에만 적용됩니다. 기존 가상 컴퓨터는 새 저장소 계정으로 마이그레이션되지 않습니다.</p>

<h2><a id="networkmapping"></a>7단계: 네트워크 매핑 구성 </h2>

<p>선택적으로 원본 VM 네트워크를 대상 Azure 가상 네트워크에 매핑하도록 네트워크 매핑을 사용할 수 있습니다. 네트워크 매핑을 만들지 않으면 같은 복구 계획에서 장애 조치(Failover)되는 가상 컴퓨터만 Azure에서 서로 연결될 수 있습니다. 네트워크 매핑을 만들면 같은 네트워크에서 장애 조치(Failover)되는 모든 가상 컴퓨터가 속해 있는 복구 계획과 관계없이 서로 연결될 수 있습니다. 또한 네트워크 게이트웨이가 대상 Azure 네트워크에서 설정되어 있으면 가상 컴퓨터가 온-프레미스 가상 컴퓨터에 연결될 수 있습니다. 이 자습서를 진행하는 동안 네트워크 매핑을 구성하려면 배포 가이드에서 [네트워크 매핑 구성][네트워크 매핑 구성]을 참조하세요.</p>




<h2><a id="virtualmachines"></a> 8단계: 가상 컴퓨터의 보호 활성화</h2>

서버, 클라우드 및 네트워크가 제대로 구성되었으면 클라우드에서 가상 컴퓨터에 대한 보호를 설정할 수 있습니다.



가상 컴퓨터에 대해 보호 기능을 사용하도록 설정하기 전에 필요하면 설정을 확인하고 업데이트하세요. 예를 들어 가상 컴퓨터의 게스트 운영 체제는 Windows Server 2008 이상 또는 Linux여야 합니다. 가상 컴퓨터는 1세대 가상 컴퓨터여야 합니다. 전체 Azure 사이트 복구 요구 사항 목록은 계획 가이드에서 [필수 조건 및 지원][필수 조건 및 지원]을 참조하세요.

<p>VMM 콘솔에서 설정을 확인하고 업데이트합니다. 가상 컴퓨터 속성의 일반 페이지에서 가상 컴퓨터에 대한 운영 체제 설정을 수정합니다. 하드웨어 구성 페이지에서 운영 체제 디스크 설정을 업데이트합니다.</p>

![가상 컴퓨터 속성 수정][가상 컴퓨터 속성 수정]

![가상 컴퓨터 속성 수정][2]

<ul>
<li>보호 기능을 사용하려면 가상 컴퓨터가 있는 클라우드의 <b>가상 컴퓨터</b> 탭에서 <b>보호 사용</b>을 클릭하고 <b>가상 컴퓨터 추가</b>를 선택합니다.</li>
<li>클라우드의 가상 컴퓨터 목록에서 보호할 가상 컴퓨터를 선택합니다.</li> 
</ul>

![가상 컴퓨터 보호 사용][가상 컴퓨터 보호 사용]


보호 기능이 사용하도록 설정되고 나면 작업이 만들어집니다. 먼저 보호 사용 작업이 실행됩니다. 초기 복제가 완료되고 나서 보호 완료 작업이 실행됩니다. 이러한 작업이 성공적으로 완료되어야만 가상 컴퓨터를 장애 조치(Failover)할 준비가 됩니다. <b>작업</b> 탭에서 진행률을 모니터링할 수 있습니다.

![가상 컴퓨터 보호 작업][가상 컴퓨터 보호 작업]


<h2><a id="recoveryplans"></a>9단계: 복구 계획 구성 및 실행</h2>
복구 계획은 가상 컴퓨터가 단일 단위로 장애 조치(Failover)될 수 있도록 그룹화합니다. 복구 계획을 만들려면 다음을 수행합니다.

1. <b>복구 계획</b> 탭에서 <b>만들기</b>를 클릭합니다.
2. 복구 페이지 이름 및 대상 지정 페이지에서 원본 VMM 서버 및 Azure를 대상으로 선택합니다.

    ![복구 계획 만들기][복구 계획 만들기]

3. 가상 컴퓨터 선택 페이지에서 복구 계획에 추가할 가상 컴퓨터를 선택합니다. 보호 기능이 사용되는 가상 컴퓨터만 표시됩니다. 가상 컴퓨터는 복구 계획 기본 그룹(그룹 1)에 추가됩니다.
4. 확인 표시를 클릭하여 복구 계획을 만듭니다.

    ![복구 계획 VM][복구 계획 VM]



<h3><a id="run"></a>장애 조치(Failover) 테스트 </h3>
복구 계획은 사전 진단 테스트 또는 계획된 장애 조치(Failover)의 일부로써 실행되거나 계획되지 않은 장애 조치(Failover) 시 실행될 수 있습니다. 이 연습에서는 VMM에서 Azure로 테스트 장애 조치(Failover)를 실행하여 장애 조치(Failover) 전략이 예상대로 작동하는지 확인하는 방법을 설명합니다. 테스트 장애 조치(Failover)에서는 격리된 네트워크에서 장애 조치(Failover) 및 복구 메커니즘을 시뮬레이션합니다. 다음 사항에 유의하세요.
<ul>
<li>장애 조치(Failover) 후에 원격 데스크탑을 사용하여 Azure의 가상 컴퓨터에 연결하려면 가상 컴퓨터에서 원격 데스크탑 연결을 사용하도록 설정하고 나서 테스트 장애 조치(Failover)를 실행합니다.</li>
<li>장애 조치(Failover) 후에 공개 IP 주소를 사용하여 원격 데스크탑을 통해 Azure의 가상 컴퓨터에 연결합니다. 이 작업을 하려면 공개 주소를 사용하여 가상 컴퓨터에 연결하지 못하도록 차단하는 도메인 정책이 없어야 합니다.</li>
</UL>

<h4><a id="runtest"></a> 장애 조치(Failover) 실행 </h5>
다음과 같이 복구 계획용 테스트 장애 조치(Failover)를 실행합니다.


1. 복구 계획을 실행하기 전에 계획에서 가상 컴퓨터 설정의 유효성을 검사해야 합니다. 이 작업을 하려면 클라우드에 대한 속성 페이지에서 가상 컴퓨터를 클릭합니다. 장애 조치(Failover)에 대한 원본 및 대상 속성 페이지에서 설정을 확인합니다. 특히 Azure의 대상 가상 컴퓨터에 대해 제안된 크기가 올바르고 네트워크 설정이 정확한지 확인합니다. 전체 가상 컴퓨터 필수 조건 목록은 [필수 조건 및 지원][3]을 참조하세요.

![가상 컴퓨터 속성][가상 컴퓨터 속성]

1. **복구 계획** 탭에서 필요한 복구 계획을 선택합니다.
2.  장애 조치(Failover)를 시작하려면 **테스트 장애 조치(Failover)** 단추를 클릭합니다.
3.  테스트 장애 조치(Failover) 확인 페이지에서 장애 조치(Failover) 후에 가상 컴퓨터가 연결될 Azure 네트워크를 선택합니다. 선택적으로 <b>네트워크 없음</b>을 선택할 수 있습니다. 이 설정을 선택하면 장애 조치(Failover) 후에 가상 컴퓨터가 네트워크에 연결되지 않습니다.


![테스트 장애 조치(Failover)][테스트 장애 조치(Failover)]

<b>작업</b> 탭에서 테스트 장애 조치(Failover) 작업의 진행 상황을 살펴볼 수 있습니다. 테스트 장애 조치(Failover)를 완료한 후 다음을 수행합니다.

1. Azure에서 가상 컴퓨터가 성공적으로 시작되는지 확인합니다.
2. **참고**를 클릭하여 테스트 장애 조치(Failover)와 연관된 항목을 기록한 후 저장합니다.
3. **작업** 탭의 세부 정보 이외에 복구 계획용 테스트 장애 조치(Failover)를 실행하면 프로세스가 복구 계획 세부 정보 페이지에 표시됩니다. 장애 조치(Failover) 단계와 상태를 확인하고 테스트 장애 조치(Failover)에 대한 참고 사항을 보거나 만들 수 있습니다.

<h4><a id="runtest"></a> 활동 모니터링 </h5>
<p><b>작업</b> 탭과 <b>대시보드</b>를 사용하여 클라우드에 대한 보호 구성, 가상 컴퓨터에 대한 보호 사용 및 사용 안 함, 장애 조치(Failover)(계획됨, 계획되지 않음 또는 테스트) 실행, 계획되지 않은 장애 조치(Failover) 커밋을 비롯하여 Azure 사이트 복구 자격 증명 모음에서 수행되는 주 작업을 보고 모니터링할 수 있습니다.</p>

<p><b>작업</b> 탭에서 작업을 보고, 작업 세부 정보 및 오류로 드릴다운하고, 작업 쿼리를 실행하여 특정 기준과 일치하는 작업을 검색하고, 작업을 Excel로 내보내고, 실패한 작업을 다시 시작합니다.</p>

<p><b>대시보드</b>에서 최신 버전의 공급자 및 에이전트 설치 파일을 다운로드하고, 자격 증명 모음에 대한 구성 정보를 가져오고, 자격 증명 모음을 통해 보호를 관리하는 가상 컴퓨터 수를 확인하고, 최근 작업을 확인하고, 자격 증명 모음 인증서를 관리하고, 가상 컴퓨터를 다시 동기화할 수 있습니다.</p>

작업 및 대시보드 조작에 대한 자세한 내용은 [작업 및 모니터링 가이드][작업 및 모니터링 가이드]를 참조하세요.

<h2><a id="next"></a> 다음 단계 </h2>
<ul>
<li>전체 프로덕션 환경에서 Azure 사이트 복구를 계획하고 배포하려면 [Azure 사이트 복구 계획 가이드][Azure 사이트 복구 배포 계획] 및 [Azure 사이트 복구 배포 가이드][Azure 사이트 복구 배포 가이드]를 참조하세요.</li>


<li>궁금한 사항은 [Azure 복구 서비스 포럼][Azure 복구 서비스 포럼]을 참조하세요.</li> 
</ul>


[Azure 사이트 복구 시작: 온-프레미스 간 보호]: http://go.microsoft.com/fwlink/?LinkId=398765
[Azure 사이트 복구 배포 계획]: http://go.microsoft.com/fwlink/?LinkId=321294
[Azure 사이트 복구 배포: 온-프레미스와 Azure 간 보호]: http://go.microsoft.com/fwlink/?LinkId=402679
[Azure 사이트 복구 관리 및 모니터링]: http://go.microsoft.com/fwlink/?LinkId=378272
[Azure 사이트 복구: 일반 오류 시나리오 및 해결 방법]: http://go.microsoft.com/fwlink/?LinkId=389879
[Azure 복구 서비스 포럼]: http://go.microsoft.com/fwlink/?LinkId=313628
[Azure 무료 평가판]: http://aka.ms/try-azure
[Azure 사이트 복구 관리자 가격 정보]: http://go.microsoft.com/fwlink/?LinkId=378268
[Microsoft Azure 저장소 소개]: http://go.microsoft.com/fwlink/?LinkId=398704
[필수 조건 및 지원]: http://go.microsoft.com/fwlink/?LinkId=402602
[1단계: 인증서 얻기 및 구성]: #createcert
[2단계: 자격 증명 모음 만들기]: #vault
[3단계: 자격 증명 모음 구성]: #upload
[4단계: 공급자 응용 프로그램 설치]: #download
[5단계: 에이전트 응용 프로그램 설치]: #agent
[6단계: 클라우드 보호 구성]: #clouds
[7단계: 네트워크 매핑 구성]: #NetworkMapping
[8단계: 가상 컴퓨터에 보호 사용]: #virtualmachines
[9단계: 복구 계획 구성 및 실행]: #recovery%20plans
[연습용 자체 서명된 인증서 얻기]: #obtaincert
[.pfx 형식으로 인증서 내보내기]: #exportcert
[VMM 서버로 .pfx 인증서 가져오기]: #importcert
[Windows SDK]: http://go.microsoft.com/fwlink/?LinkId=378269
[관리 포털]: https://manage.windowsazure.com
[새 자격 증명 모음]: ./media/hyper-v-recovery-manager-configure-vault/SR_HvVault.png
[빠른 시작 아이콘]: ./media/hyper-v-recovery-manager-configure-vault/SR_QuickStartIcon.png
[빠른 시작]: ./media/hyper-v-recovery-manager-configure-vault/SR_QuickStart.png
[인증서 관리]: ./media/hyper-v-recovery-manager-configure-vault/SR_ManageCert.png
[1]: ./media/hyper-v-recovery-manager-configure-vault/SR_VaultKey.png
[공급자 파일 다운로드]: ./media/hyper-v-recovery-manager-configure-vault/SR_DownloadProviderFile.png
[다운로드 에이전트]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderInstall1.png
[설치 완료]: ./media/hyper-v-recovery-manager-configure-vault/SR_InstallWiz.png
[인터넷 설정]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderProxy.png
[인증서 등록]: ./media/hyper-v-recovery-manager-configure-vault/SR_CertReg1.png
[인증서 자격 증명 모음]: ./media/hyper-v-recovery-manager-configure-vault/SR_CertReg2.png
[PublishCloud]: ./media/hyper-v-recovery-manager-configure-vault/SR_VMMServerName.png
[복구 서비스 에이전트 설치]: ./media/hyper-v-recovery-manager-configure-vault/SR_HyperVAgent.png
[필수 조건 복구 서비스 에이전트]: ./media/hyper-v-recovery-manager-configure-vault/SR_AgentPrereqs.png
[복구 서비스 에이전트 위치]: ./media/hyper-v-recovery-manager-configure-vault/SR_AgentInstallSettings.png
[게시된 클라우드]: ./media/hyper-v-recovery-manager-configure-vault/SR_Clouds.png
[클라우드 보호 사용]: ./media/hyper-v-recovery-manager-configure-vault/SR_EnableCloudProtection.png
[클라우드 구성]: ./media/hyper-v-recovery-manager-configure-vault/SR_CloudConfigureE2A1.png
[클라우드 복제 설정]: ./media/hyper-v-recovery-manager-configure-vault/SR_PublishCloudSetup2.png
[네트워크 매핑 구성]: http://go.microsoft.com/fwlink/?LinkId=402533
[가상 컴퓨터 속성 수정]: ./media/hyper-v-recovery-manager-configure-vault/SR_VMMVMPropsGeneral.png
[2]: ./media/hyper-v-recovery-manager-configure-vault/SR_VMMVMPropsHW.png
[가상 컴퓨터 보호 사용]: ./media/hyper-v-recovery-manager-configure-vault/SR_EnableProtectionVM.png
[가상 컴퓨터 보호 작업]: ./media/hyper-v-recovery-manager-configure-vault/SR_VMJobs.png
[복구 계획 만들기]: ./media/hyper-v-recovery-manager-configure-vault/SR_RecoveryPlan1.png
[복구 계획 VM]: ./media/hyper-v-recovery-manager-configure-vault/SR_RecoveryPlan2.png
[3]: http://go.microsoft.com/fwlink/?LinkId=402676
[가상 컴퓨터 속성]: ./media/hyper-v-recovery-manager-configure-vault/SR_VMPropertiesE2A.png
[테스트 장애 조치(Failover)]: ./media/hyper-v-recovery-manager-configure-vault/SR_TestFailover.png
[작업 및 모니터링 가이드]: http://go.microsoft.com/fwlink/?LinkId=398534
[Azure 사이트 복구 배포 가이드]: http://go.microsoft.com/fwlink/?LinkId=321295
