<properties linkid="Azure Site Recovery Overview" urlDisplayName="Azure Site Recovery Overview" pageTitle="Azure Site Recovery Overview" metaKeywords="Azure Site Recovery, on-premises, clouds, Azure, VMM, Hyper-V" description="Deploy Azure Site Recovery to protect virtual machines on Hyper-V host servers that are located in VMM clouds. You can deploy from one on-premises site to another, or from an on-premises site to Azure." metaCanonical="" umbracoNaviHide="0" disqusComments="1" title="Azure Site Recovery Overview" editor="jimbe" manager="cfreeman" authors="raynew" />

<tags ms.service="site-recovery" ms.workload="backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="raynew"></tags>

# Azure 사이트 복구 개요

<div class="dev-callout">
<p>Azure 사이트 복구를 배포하여 System Center VMM(Virtual Machine Manager) 클라우드에 있는 Hyper-V 호스트 서버에서 실행되는 가상 컴퓨터를 보호합니다. 다음과 같은 다양한 방법으로 Azure 사이틀 복구를 배포할 수 있습니다.</p>


<ul>
<li><b>온-프레미스 간 보호</b> - 온-프레미스 사이트 간에 가상 컴퓨터를 복제합니다. Azure 사이트 복구 자격 증명 모음을 구성하고 사용하도록 설정합니다. 가상 컴퓨터 데이터가 원본 Hyper-V 호스트 서버에서 대상 호스트 서버로 복제됩니다. Azure 사이트 복구는 프로세스를 오케스트레이션합니다. 이 시나리오에 대한 자습서는 [Azure 사이트 복구 시작: 온-프레미스 간 보호][Azure 사이트 복구 시작: 온-프레미스 간 보호]에서 제공됩니다.</li>

<li><b>온-프레미스와 Azure 간 보호</b> - 온-프레미스 사이트에서 Microsoft Azure로 가상 컴퓨터를 복제합니다. Azure 사이트 복구 자격 증명 모음을 구성하고 사용하도록 설정합니다. Azure 사이트 복구는 프로세스를 오케스트레이션하고 복제된 가상 컴퓨터 데이터는 Azure 저장소에 저장됩니다. 이 시나리오에 대한 자습서는 [Azure 사이트 복구 시작: 온-프레미스와 Azure 간 보호][Azure 사이트 복구 시작: 온-프레미스와 Azure 간 보호]에서 제공됩니다.</li>
</ul>

<p>다음 표에서는 두 가지 배포 옵션을 간략하게 설명하고 비교합니다.</p>

<table border="1">
<thead>
<tr>
	<th>기능</th><th>온-프레미스와 Azure 간</th>
	<th>온-프레미스 간</th>
</tr>
</thead>

<tr>
<td>데이터를 Azure 사이트 복구로</td>
<td>클라우드 메타데이터가 Azure 사이트 복구로 전송됩니다. 복제된 데이터는 Azure 저장소에 저장됩니다.</td>
<td>클라우드 메타데이터가 Azure 사이트 복구로 전송됩니다. 복제된 데이터는 대상 Hyper-V 호스트 서버에 저장됩니다.</td>
</tr>

<tr>
<td>자격 증명 모음 요구 사항</td>
<td><p>자격 증명 모음에는 <a href="%20http://go.microsoft.com/fwlink/?LinkId=386485">자격 증명 모음 요구 사항</a>을 준수하는 인증서가 필요합니다.</p></td>
<td><p>자격 증명 모음 키가 자동으로 생성됩니다. 이 키는 VMM 서버의 Azure 사이트 복구 공급자와 Azure 사이트 복구 간의 트래픽 무결성을 확인합니다.</p></td>
</tr>

<tr>
<td>자격 증명 모음에는 <a href="%20http://go.microsoft.com/fwlink/?LinkId=386485">자격 증명 모음 요구 사항</a>을 준수하는 인증서가 필요합니다.</td>
<td>자격 증명 모음 키가 자동으로 생성됩니다. 이 키는 VMM 서버의 Azure 사이트 복구 공급자와 Azure 사이트 복구 간의 트래픽 무결성을 확인합니다.</td>
<td>복제</td>
</tr>

<tr>
<td>가상 컴퓨터가 온-프레미스 Hyper-V 서버에서 Azure 저장소로 복제됩니다.</td>
<td>가상 컴퓨터가 온-프레미스 Hyper-V 서버 간에 복제됩니다.</td>
<td>가상 컴퓨터 저장소</td>
</tr>

<tr>
<td>Azure 저장소에 저장된 가상 컴퓨터 하드 디스크</td>
<td><p>Hyper-V 호스트에 저장된 가상 컴퓨터 하드 디스크</p> <p>Azure 저장소</td><td align="left"><p>Azure 저장소 계정이 필요합니다.</p>
<p>계정의 지역에서 복제 기능을 사용하도록 설정하고, 계정이 Azure 사이트 복구 서비스와 같은 지역에 있고, 같은 구독과 연결되어야 합니다.</p></td>
<td>해당 없음</td>
</tr>
<tr class="even">
<td align="left">VMM 서버</td>
<td align="left">원본 VMM 서버만 필요합니다.</td>
<td align="left">각각 클라우드가 하나 이상 있는 원본 및 대상 VMM 서버와 클라우드 두 개가 있는 단일 VMM 서버가 필요합니다.</td>
</tr>
<tr class="odd">
<td align="left">VMM 서버 System Center 버전</td>
<td align="left">System Center 2012 R2</td>
<td align="left"><p>System Center 2012 SP1</p>
<p>System Center 2012 R2</p></td>
</tr>
<tr class="even">
<td align="left">Azure 사이트 복구 공급자</td>
<td align="left">원본 VMM 서버 설치</td>
<td align="left">원본 및 대상 VMM 서버 설치</td>
</tr>
<tr class="odd">
<td align="left">Azure 복구 서비스 에이전트</td>
<td align="left">VMM 클라우드에 있는 Hyper-V 호스트 서버에 설치</td>
<td align="left">필요하지 않음</td>
</tr>
<tr class="even">
<td align="left">가상 컴퓨터 복구 지점</td>
<td align="left"><p>시간별로 복구 지점을 설정합니다.</p>
<p>복구 지점 유지 기간을 지정합니다(0-24시간). 복구 지점은 처음 1시간 동안 공식 90/copy_frequency를 사용하여 생성되고 이후 1시간에 한 번 생성됩니다.</p></td>
<td align="left"><p>용량별로 복구 지점을 설정합니다.</p>
<p>유지해야 하는 추가 복구 지점 수를 지정합니다(0-15). 기본적으로 복구 지점은 매시간 생성됩니다. 0으로 설정하면 최신 복구 지점만 복제본 Hyper-V 호스트 서버에 저장됩니다.</p></td>
</tr>
<tr class="odd">
<td align="left">네트워크 매핑</td>
<td align="left"><p>VM 네트워크를 Azure 네트워크에 매핑합니다.</p>
<p>네트워크 매핑을 사용하면 같은 원본 VM 네트워크에서 장애 조치(Failover)되는 모든 가상 컴퓨터가 장애 조치(Failover) 후에 연결될 수 있습니다. 또한 대상 Azure 네트워크에 네트워크 게이트웨이가 있으면 가상 컴퓨터가 온-프레미스 가상 컴퓨터에 연결될 수 있습니다.</p>
<p>매핑을 사용하지 않으면 같은 복구 계획에서 장애 조치(Failover)되는 가상 컴퓨터만 Azure로의 장애 조치(Failover) 후에 서로 연결될 수 있습니다.</p></td>
<td align="left"><p>원본 VM 네트워크를 대상 VM 네트워크에 매핑합니다.</p>
<p>네트워크 매핑은 복제된 가상 컴퓨터를 최적의 Hyper-V 호스트 서버에 저장하는 데 사용되고 원본 VM 네트워크와 연결된 가상 컴퓨터가 장애 조치(Failover) 후에 매핑된 대상 네트워크와 연결되도록 합니다.</p>
<p>매핑을 사용하지 않으면 복제된 가상 컴퓨터가 네트워크에 연결되지 않습니다.</p></td>
</tr>
<tr class="even">
<td align="left">저장소 매핑</td>
<td align="left">해당 없음</td>
<td align="left"><p>원본 VMM 서버의 저장소 본류를 대상 VMM 서버의 저장소 분류에 매핑합니다.</p>
<p>매핑을 사용하면 원본 저장소 분류의 가상 컴퓨터 하드 디스크가 장애 조치(Failover) 후에 대상 저장소 분류에 있게 됩니다.</p>
<p>저장소 매핑을 사용하지 않으면 복제된 가상 하드 디스크가 대상 Hyper-V 호스트 서버의 기본 위치에 저장됩니다.</p></td>
</tr>
</tbody>
</table>

궁금한 사항은 [Azure 복구 서비스 포럼][Azure 복구 서비스 포럼]을 참조하세요.

</div>

[Azure 사이트 복구 시작: 온-프레미스 간 보호]: http://go.microsoft.com/fwlink/?LinkId=398765
[Azure 사이트 복구 시작: 온-프레미스와 Azure 간 보호]: http://go.microsoft.com/fwlink/?LinkId=398764
[자격 증명 모음 요구 사항]: %20http://go.microsoft.com/fwlink/?LinkId=386485
[Azure 복구 서비스 포럼]: http://go.microsoft.com/fwlink/?LinkId=313628
