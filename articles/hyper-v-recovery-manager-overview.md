<properties 
	pageTitle="Azure Site Recovery 개요" 
	description="Azure Site Recovery를 배포하여 온-프레미스 가상 컴퓨터를 다른 온-프레미스 사이트 또는 Azure에 대해 보호합니다." 
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

# Azure Site Recovery 개요

<div class="dev-callout"> 
<p>Azure Site Recovery는 다음과 같은 다양한 시나리오에서 복제 및 장애 조치(Failover)를 오케스트레이션합니다.</p>


<ul>
<li>**Hyper-V 복제를 사용한 온-프레미스 Hyper-V 사이트와 Azure 간 보호** - 하나 이상의 Hyper-V 서버를 사용하지만 System Center VMM 없이 온-프레미스 사이트의 복제, 장애 조치(Failover) 및 복구를 오케스트레이션합니다. 가상 컴퓨터 데이터가 원본 Hyper-V 호스트 서버에서 Azure로 복제됩니다. <a href="http://go.microsoft.com/fwlink/?LinkId=522298">Azure Site Recovery 시작: Hyper-V 복제를 사용한 온-프레미스 Hyper-V 사이트와 Azure 간 보호</a>를 참조하세요.</li>
<li>**Hyper-V 복제를 사용한 온-프레미스 VMM 사이트와 온-프레미스 VMM 간 보호** - 온-프레미스 VMM 사이트 간의 복제, 장애 조치(Failover) 및 복구를 오케스트레이션합니다. 가상 컴퓨터 데이터가 원본 Hyper-V 호스트 서버에서 대상 호스트 서버로 복제됩니다. <a href="http://go.microsoft.com/fwlink/?LinkId=398765">Azure Site Recovery 시작: Hyper-V 복제를 사용한 두 개의 온-프레미스 VMM 사이트 간 보호</a>를 참조하세요.</li>

<li>**SAN 복제를 사용한 온-프레미스 VMM 사이트와 온-프레미스 VMM 간 보호** - 원본 및 대상 온-프레미스 사이트에 가상 컴퓨터 데이터를 호스트하는 SAN 장치 간에 저장소 배열 기반 복제를 사용하여 종단 간 복제, 장애 조치(Failover) 및 복구를 오케스트레이션합니다. <a href="http://go.microsoft.com/fwlink/?LinkId=518683">Azure Site Recovery 시작: : SAN 복제를 사용한 두 개의 온-프레미스 VMM 사이트 간 보호</a>를 참조하세요.</li>

<li>**온-프레미스 VMM 사이트와 Azure 간 보호** - 온-프레미스 VMM 사이트와 Azure 간의 복제, 장애 조치(Failover) 및 복구를 오케스트레이션합니다. 복제된 가상 컴퓨터 데이터는 Azure 저장소에 저장됩니다. <a href="http://go.microsoft.com/fwlink/?LinkId=398764">Azure Site Recovery 시작: 온-프레미스 VMM 사이트와 Azure 간 보호</a>를 참조하세요.</li>

<li>**InMage를 사용한 온-프레미스 VMWare 사이트와 온-프레미스 VMWare 사이트 간 보호** - InMage Scout는 최근에 Microsoft에 인수되었으며 온-프레미스 VMWare 사이트 간에 실시간 복제를 제공합니다. 현재 InMage는 Azure Site Recovery 서비스 구독을 통해 별도 제품으로 제공됩니다. <a href="http://go.microsoft.com/fwlink/?LinkId=518684">Azure Site Recovery 시작: InMage를 사용한 온-프레미스 VMWare 사이트 간 보호</a>를 참조하세요.</li>
</ul>

<p>기능 매트릭스에 이러한 시나리오가 요약되어 있습니다.</p>

<table border="1">
<thead>
<tr>
	<th>기능</th><th>온-프레미스와 Azure 간</th>
	<th>온-프레미스와 온-프레미스 간(Hyper-V)</th>
	<th>온-프레미스와 온-프레미스 간(SAN)</th>
</tr>
</thead>

<tr>
<td>Azure Site Recovery로 전송되는 데이터</td>
<td><p>클라우드 메타데이터가 Azure Site Recovery로 전송됩니다.</p> <p>복제된 데이터는 Azure 저장소에 저장됩니다.</p></td>
<td><p>클라우드 메타데이터가 Azure Site Recovery로 전송됩니다.</p> <p>복제된 데이터는 대상 Hyper-V 서버에서 지정한 위치에 저장됩니다.</p></td>
<td><p>클라우드 메타데이터가 Azure Site Recovery로 전송됩니다.</p> <p>복제된 데이터는 대상 배열 저장소에 저장됩니다.</p></td>
</tr>

<tr>
<td>자격 증명 모음 요구 사항</td>
<td><p>Azure Site Recovery 자격 증명 모음을 설정하려면 Azure 계정이 있어야 합니다. <a href="http://aka.ms/try-azure">Azure 무료 평가판</a>을 참조하세요. 가격 정보는 <a href="http://go.microsoft.com/fwlink/?LinkId=378268">Azure Site Recovery 관리자 가격 정보</a>에서 확인할 수 있습니다.</p></td>
<td><p>Azure Site Recovery를 사용할 수 있는 Azure 계정</p>
</td><td><p>Azure Site Recovery를 사용할 수 있는 Azure 계정</p></td>
</tr>

<tr>
<td>복제</td>
<td>가상 컴퓨터가 원본 온-프레미스 Hyper-V 서버에서 대상 Azure 저장소로 복제됩니다. 다시 원본 위치로 복제하기 위해 역방향 복제를 설정할 수 있습니다.</td>
<td>가상 컴퓨터가 원본 온-프레미스 Hyper-V 서버에서 다른 온-프레미스 Hyper-V 서버로 복제됩니다. 다시 원본 위치로 복제하기 위해 역방향 복제를 설정할 수 있습니다.</td>
<td>가상 컴퓨터가 원본 SAN 저장 장치에서 대상 SAN 장치로 복제됩니다. 다시 원본 위치로 복제하기 위해 역방향 복제를 설정할 수 있습니다.</td>
</tr>

<tr>
<td>가상 컴퓨터 저장소</td>
<td>Azure 저장소에 저장된 가상 컴퓨터 하드 디스크</td>
<td>Hyper-V 호스트에 저장된 가상 컴퓨터 하드 디스크</td>
<td>SAN 저장소 배열에 저장된 가상 컴퓨터 하드 디스크</td>
</tr>

<tr>
<td>Azure 저장소</td>
<td><p>Azure 저장소 계정이 필요합니다.</p> <p>계정의 지역에서 복제 기능을 사용하도록 설정하고, 계정이 Azure Site Recovery 서비스와 같은 지역에 있고, 같은 구독과 연결되어야 합니다.</p></td>
<td>해당 없음</td>
<td>해당 없음</td>
</tr>

<tr>
<td>SAN 저장소 배열</td>
<td><p>해당 없음</p></td>
<td>해당 없음</td>
<td>SAN 저장소 배열이 원본 및 대상 사이트 둘 다에서 사용 가능하고 VMM에 의해 관리되어야 합니다. 자세한 내용은 <a href="http://go.microsoft.com/fwlink/?LinkId=518685">배포 필수 조건</a>을 참조하세요. </td>
</tr>

<tr>
<td>VMM 서버</td>
<td>원본 사이트에만 VMM 서버가 있으면 됩니다. VMM 서버에 Hyper-V 호스트 서버 또는 클러스터를 하나 이상 포함하는 클라우드가 하나 이상 있어야 합니다.</td>
<td>각각 클라우드가 하나 이상 있는 원본 및 대상 VMM 서버와 클라우드 두 개가 있는 단일 VMM 서버가 필요합니다. 클라우드에 Hyper-V 호스트 서버 또는 클러스터가 하나 이상 포함되어야 합니다.</td>
<td>각각 클라우드가 하나 이상 있는 원본 및 대상 VMM 서버가 필요합니다. 클라우드에 Hyper-V 클러스터가 하나 이상 포함되어야 합니다.</td>
</tr>

<tr>
<td>VMM System Center 버전</td>
<td>System Center 2012 R2</td>
<td><p>System Center 2012 SP1</p><p>System Center 2012 R2</p></td>
<td><p>VMM 업데이트 롤업 5.0을 설치한 System Center 2012 R2</p></td>
</tr>

<tr>
<td>VMM 구성</td>
<td><p>원본 및 대상 사이트에서 클라우드 설정</p><p>원본 및 대상 사이트에서 VM 네트워크 설정</p><p>원본 및 대상 사이트에서 저장소 분류 설정 <p>원본 및 대상 VMM 서버에 공급자 설치</p></td>
<td><p>원본 사이트에서 클라우드 설정</p><p>SAN 저장소 설정</p><p>원본 사이트에서 VM 네트워크 설정</p><p>원본 VMM 서버에 공급자 설치</p><p>가상 컴퓨터 보호 사용</p></td>
<td><p>원본 및 대상 사이트에서 클라우드 설정</p><p>원본 및 대상 사이트에서 VM 네트워크 설정</p><p>원본 및 대상 VMM 서버에 공급자 설치</p><p>가상 컴퓨터 보호 사용</p></td>
</tr>

<tr>
<td>Azure Site Recovery 공급자</td>
<td>원본 VMM 서버 설치</td>
<td>원본 및 대상 VMM 서버 설치</td>
<td>원본 및 대상 VMM 서버 설치</td>
</tr>

<tr>
<td>Azure 복구 서비스 에이전트</td>
<td>VMM 클라우드에 있는 Hyper-V 호스트 서버에 설치</td>
<td>필요하지 않음</td>
<td>필요하지 않음</td>
</tr>

<tr>
<td>가상 컴퓨터 복구 지점</td>
<td><p>시간별로 복구 지점을 설정합니다.</p> <p>복구 지점 유지 기간을 지정합니다(0-24시간). 복구 지점은 처음 1시간 동안 공식 90/copy_frequency를 사용하여 생성되고 이후 1시간에 한 번 생성됩니다.</p></td>
<td><p>용량별로 복구 지점을 설정합니다.</p> <p>유지해야 하는 추가 복구 지점 수를 지정합니다(0-15). 기본적으로 복구 지점은 매시간 생성됩니다. 0으로 설정하면 최신 복구 지점만 복제본 Hyper-V 호스트 서버에 저장됩니다.</p></td>
<td>배열 저장소 설정에서 구성됩니다.</td>
</tr>

<tr>
<td>네트워크 매핑</td>
<td><p>VM 네트워크를 Azure 네트워크에 매핑합니다.</p> <p>네트워크 매핑을 사용하면 같은 원본 VM 네트워크에서 장애 조치(Failover)되는 모든 가상 컴퓨터가 장애 조치(Failover) 후에 연결될 수 있습니다. 또한 대상 Azure 네트워크에 네트워크 게이트웨이가 있으면 가상 컴퓨터가 온-프레미스 가상 컴퓨터에 연결될 수 있습니다. </p><p>매핑을 사용하지 않으면 같은 복구 계획에서 장애 조치(Failover)되는 가상 컴퓨터만 Azure로의 장애 조치(Failover) 후에 서로 연결될 수 있습니다.</p></td>
<td><p>원본 VM 네트워크를 대상 VM 네트워크에 매핑합니다.</p> <p>네트워크 매핑은 복제된 가상 컴퓨터를 최적의 Hyper-V 호스트 서버에 저장하는 데 사용되고 원본 VM 네트워크와 연결된 가상 컴퓨터가 장애 조치(Failover) 후에 매핑된 대상 네트워크와 연결되도록 합니다. </p><p>매핑을 사용하지 않으면 복제된 가상 컴퓨터가 네트워크에 연결되지 않습니다.</p></td>
<td><p>원본 VM 네트워크를 대상 VM 네트워크에 매핑합니다.</p> <p>네트워크 매핑은 원본 VM 네트워크와 연결된 가상 컴퓨터가 장애 조치(Failover) 후 매핑된 대상 네트워크와 연결되도록 합니다. </p><p>매핑을 사용하지 않으면 복제된 가상 컴퓨터가 네트워크에 연결되지 않습니다.</p></td>
</tr>

<tr>
<td>저장소 매핑</td>
<td>해당 없음</td>
<td><p>원본 VMM 서버의 저장소 본류를 대상 VMM 서버의 저장소 분류에 매핑합니다.</p> <p>매핑을 사용하면 원본 저장소 분류의 가상 컴퓨터 하드 디스크가 장애 조치(Failover) 후에 대상 저장소 분류에 있게 됩니다.</p><p>저장소 매핑을 사용하지 않으면 복제된 가상 하드 디스크가 대상 Hyper-V 호스트 서버의 기본 위치에 저장됩니다.</p></td>
<td><p>기본 사이트 및 보조 사이트에서 저장소 배열과 풀 간에 매핑합니다.</p></td>
</tr>

</table>

<p>궁금한 사항은 <a href="http://go.microsoft.com/fwlink/?LinkId=313628">Azure 복구 서비스 포럼</a>을 참조하세요.</p> 

<!--HONumber=49-->