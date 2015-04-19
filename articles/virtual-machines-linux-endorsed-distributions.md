<properties 
	pageTitle="Azure에서 Linux의 인증 배포 수행" 
	description="Ubuntu, OpenLogic 및 SUSE 관련 지침을 포함하여 Azure에서 Linux의 인증 배포를 수행하는 방법에 대해 알아봅니다." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="szarkos" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/05/2014" 
	ms.author="szark"/>





#Azure 인증 배포의 Linux

다양한 Linux 커뮤니티와의 작업을 통해 좀더 인증된 배포를 제공하며, Azure 갤러리의 배포 이미지는 다음 파트너를 통해 제공됩니다. 그러는 동안 이 갤러리에서 사용할 수 없는 배포의 경우, [이 페이지]의 지침에 따라 자신만의 Linux를 언제든지 가져올 수 있습니다(virtual-machines-linux-create-upload-vhd.md).

## Canonical ##
 
[http://www.ubuntu.com/cloud/azure](http://www.ubuntu.com/cloud/azure)

Canonical 엔지니어링과 개방형 커뮤니티 관리로 인해 고객에 대한 개인 클라우드 서비스와 클라이언트, 서버 및 클라우드 컴퓨팅에서 Ubuntu가 성공할 수 있었습니다. Ubuntu의 통합된 무료 플랫폼에 대해 Canonical은 전화에서 클라우드에 이르기까지 전화, 태블릿, TV 및 데스크톱에 일관된 인터페이스를 보여주어 공용 클라우드 공급자에서 가전 제품 제조업체에 이르기까지 다양한 기관에서 Ubuntu를 우선적으로 선택하도록 만들고 개인 과학 분야 기술자들 사이에서 선호하도록 만듭니다.

Canonical은 전 세계의 개발자 및 엔지니어링 센터와 함께 PC에서 서버 및 핸드헬드 장치에 이르는 업계에 Ubuntu 솔루션을 제공하기 위해 하드웨어 제조업체, 콘텐츠 공급자 및 소프트웨어 개발자와 유일하게 파트너 입장에 있습니다.

## OpenLogic ##
 
[http://www.openlogic.com/azure](http://www.openlogic.com/azure)

OpenLogic은 클라우드와 데이터 센터에 있어서 엔터프라이즈 오픈 소스 솔루션의 선두 주자입니다. OpenLogic은 오픈 소스 소프트웨어를 안전하게 얻고 지원하고 제어하도록 다양한 산업 분야에 걸쳐 수 많은 선두 엔터프라이즈를 돕습니다. OpenLogic은 OpenLogic Expert Community에서 지원하는 600개의 오픈 소스 패키지에 대한 상업용 기술 지원 및 보장을 제공하며, 여기에는 Azure에 CentOS 이미지를 제공하기 위한 착수 파트너가 되는 것뿐만 아니라 CentOS에 대한 엔터프라이즈 수준의 지원도 포함됩니다.

## Oracle##
[http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html](http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html)

Oracle의 전략은 공용 및 사설 클라우드에 대한 솔루션의 다양한 포트폴리오를 제공하며, 다른 클라우드뿐만 아니라 Oracle 클라우드에서 Oracle 소프트웨어를 배포하는 방법에 있어서 고객에게 선택권과 유연성을 제공하는 것입니다.  Oracle과 Microsoft의 파트너 관계를 통해 고객은 Oracle의 확실한 인증 및 지원으로 Microsoft 공용 및 사설 클라우드에서 Oracle 소프트웨어를 배포할 수 있습니다.  Oracle 공용 및 사설 클라우드 솔루션에 대한 Oracle의 이행 및 투자는 변하지 않습니다.

##SUSE##
 
[http://www.suse.com/suse-linux-enterprise-server-on-azure](http://www.suse.com/suse-linux-enterprise-server-on-azure)

SUSE Linux Enterprise Server on Azure는 클라우드 컴퓨팅에 대해 우수한 안정성과 보안을 제공하는 검증된 플랫폼입니다. SUSE의 다양한 Linux 플랫폼은 Azure 클라우드 서비스와 자연스럽게 통합되어 쉽게 관리할 수 있는 클라우드 환경을 제공합니다. 1,800곳이 넘는 SUSE Linux Enterprise Server의 독립 소프트웨어 공급업체에서 9,200개 이상의 인증된 응용 프로그램을 통해 SUSE는 데이터 센터에서 지원되는 실행 중인 작업이 확실히 Azure에 배포될 수 있도록 합니다.

## 지원되는 버전 ##

다음 표에서는 다른 배포 버전인 LIS(Linux Integration Services) 드라이버와 Azure에서 작동을 테스트한 Azure Linux 에이전트 버전을 보여 줍니다. LIS 드라이버는 기본적으로 배포의 커널에 제공되거나 [여기](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409)에서 다운로드할 수 있습니다. Linux Agent 버전은 배포의 패키지 리포지토리 또는 [Github](https://github.com/azure/walinuxagent)에서 사용할 수 있습니다.

이 표에는 Azure에서 최적으로 작동하기 위해 일부 배포 버전에 필요한 [Linux 커널 호환성 패치](http://go.microsoft.com/fwlink/?LinkID=403027&clcid=0x409)에 대한 링크도 포함되어 있습니다.

<table border="1" width="600">
  <tr bgcolor="#E9E7E7">
		<th>배포</th>		
	    <th>버전</th>
	    <th>드라이버</th>
		<th>커널 호환성 패치</th>
		<th>에이전트</th>
			</tr>
	<tr>
		<th>  Canonical Ubuntu </th>
		<td> Ubuntu 12.04.1 이상, 14.04 및 14.10 </td>
		<td>커널에 있음</td>
		<td><a href="http://go.microsoft.com/fwlink/?LinkID=275152&amp;clcid=0x409">12.04 또는 12.04.01에만 필요함</a></td>
		<td>패키지: walinuxagent의 패키지 리포지토리에 있음  <br />
			원본: <a href="http://go.microsoft.com/fwlink/p/?LinkID=250998">GitHub</a></td>
			</tr>
	<tr>
		<th> OpenLogic의 CentOS </th>
		<td> CentOS 6.3 이상</td>
	    <td>CentOS 6.3: <a href="http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409">LIS 드라이버</a>, CentOS 6.4+ 이상 드라이버: 커널에 있음</td>
		<td><a href="http://go.microsoft.com/fwlink/?LinkID=275153&amp;clcid=0x409">6.3에만 필요함</a></td>
		<td>패키지: walinuxagent의 <a href="http://olcentgbl.trafficmanager.net/openlogic/6/openlogic/x86_64/RPMS/">Open Logic 패키지 리포지토리</a>에 있음<br />
			원본: <a href="http://go.microsoft.com/fwlink/p/?LinkID=250998">GitHub</a></td>
 		

	</tr>
	<tr>
		<th> <a href="https://coreos.com/docs/running-coreos/cloud-providers/azure/">CoreOS</a> </th>
		<td> 494.4.0+ </td>
        <td> 커널에 있음 </td>
		<td> 해당 없음 </td>
		<td> 원본: <a href="https://github.com/coreos/coreos-overlay/tree/master/app-emulation/wa-linux-agent">GitHub</a></td>
		
	</tr>
	<tr>
		<th> Oracle Linux </th>
		<td> 6.4+</td>
        <td>커널에 있음</td>
		<td>해당 없음</td>
		<td>패키지: 리포지토리에 있음. 이름: WALinuxAgent<br />
			원본: <a href="http://go.microsoft.com/fwlink/p/?LinkID=250998">GitHub</a></td>
		
	</tr>
	<tr>
		<th> SUSE Linux Enterprise </th>
		<td> SLES 11 SP3 이상</td>
        <td>커널에 있음</td>
		<td>해당 없음</td>
		<td>패키지: <a href="https://build.opensuse.org/project/show/Cloud:Tools" >Cloud:Tools</a> 리포지토리에 있음, 이름: WALinuxAgent<br />
			소스 코드: <a href="http://go.microsoft.com/fwlink/p/?LinkID=250998">GitHub</a></td>
		
	</tr>
	<tr>
		<th> openSUSE </th>
		<td> OpenSUSE 13.1 이상</td>
		<td>커널에 있음</td>
		<td>해당 없음</td>
		<td>패키지: <a href="https://build.opensuse.org/project/show/Cloud:Tools" >Cloud:Tools</a> 리포지토리에 있음, 이름: WALinuxAgent<br />
			소스 코드: <a href="http://go.microsoft.com/fwlink/p/?LinkID=250998">GitHub</a></td>
		
	</tr>
</table>



<!--HONumber=42-->
