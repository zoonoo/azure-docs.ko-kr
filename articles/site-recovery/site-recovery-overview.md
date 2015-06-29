<properties
	pageTitle="사이트 복구 개요" 
	description="Azure Site Recovery는 온-프레미스에 있는 가상 컴퓨터와 물리적 서버의 복제, 장애 조치 및 복구를 Azure 또는 보조 온-프레미스 사이트로 조정합니다." 
	services="site-recovery" 
	documentationCenter="" 
	authors="rayne-wiselman" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery" 
	ms.date="05/10/2015" 
	ms.author="raynew"/>

#  사이트 복구 개요

사이트 복구 서비스는 Azure 또는 보조 온-프레미스 데이터센터로 복제 및 장애 조치를 오케스트레이션 및 자동화하여 온-프레미스 물리적 서버 및 가상 컴퓨터를 보호하는 강력한 비즈니스 연속성 및 재해 복구(BCDR) 솔루션에 기여합니다.

- **단순화**-사이트 복구는 복제를 쉽게 구성할 수 있도록 하여 BCDR 전략을 간소화하며, 온-프레미스 워크로드 및 응용프로그램에 대해 장애 조치 및 복구를 실행합니다.
- **복제**-Azure 저장소 또는 보조 데이터센터에 온-프레미스 워크로드를 복제할 수 있습니다. 
- **자격 증명 모음**-선택한 Azure 지역에서 사이트 복구 자격 증명 모음을 설정하는 복제를 관리합니다. 이 영역에 있는 모든 메타 데이터가 유지됩니다.
- **메타 데이터**-Azure에 전송되는 응용프로그램 데이터가 없습니다. 사이트 복구는 복제 및 장애 조치를 오케스트레이션하기 위해 가상 컴퓨터 및 VMM 클라우드 이름과 같은 메타 데이터만 필요합니다. 
- **Azure 연결**-관리 서버는 배포 시나리오에 따라 Azure와 통신합니다. 예를 들어, 온-프레미스 VMM 클라우드에 있는 가상 컴퓨터를 복제하고 있는 경우, VMM 서버는 암호화된 아웃바운드 HTTPS 연결을 통해 사이트 복구와 통신합니다. 가상 컴퓨터 또는 Hyper-V 호스트에서 연결할 필요가 없습니다.
- **Hyper-V 복제본**-Azure Site Recovery는 복제 프로세스에 대한 Hyper-V 복제본을 활용하고 두 온-프레미스 VMM 사이트 사이에서 복제하는 경우 SAN 복제도 사용할 수 있습니다. 사이트 복구는 데이터 블록뿐 아니라 초기 복제에 대한 전체 VHD를 복제하는 스마트 복제를 사용합니다. 진행 중인 복제의 경우 델타 변경만 복제됩니다. 사이트 복구는 오프라인 데이터 전송을 지원하고 WAN 최적화와 작동합니다.
- **가격**-사이트 복구 가격에 대해 [더 자세히 알아볼](pricing/details/site-recovery) 수 있습니다.


## 배포 시나리오

이 표에서 사이트 복구에서 지원되는 복제 시나리오를 요약합니다.

**다음으로 복제** | **(온-프레미스)에서 복제** | **세부 정보** | **문서**
---|---|---|---
Azure | Hyper-V 사이트 | Hyper-V 사이트로 정의되는 하나 이상의 온-프레미스 Hyper-V 호스트 서버의 가상 컴퓨터를 Azure에 복제합니다. VMM 서버가 필요하지 않습니다. | [자세히 알아보기](site-recovery-hyper-v-site-to-azure.md)
Azure| VMM 서버 | VMM 클라우드에 있는 하나 이상의 온-프레미스 Hyper-V 호스트 서버의 가상 컴퓨터를 Azure에 복제합니다. | [자세히 알아보기](site-recovery-vmm-to-azure.md) 
Azure | 실제 Windows Server | Azure에 실제 Windows 또는 Linux 서버 복제 | [자세히 알아보기](site-recovery-vmware-to-azure.md)
Azure | VMware 가상 컴퓨터 | Azure에 VMWare 가상 컴퓨터 복제 | [자세히 알아보기](site-recovery-vmware-to-azure.md)
보조 데이터센터 | VMM 서버 | VMM 클라우드에 있는 온-프레미스 Hyper-V 호스트 서버의 가상 컴퓨터를 다른 데이터센터의 보조 VMM 서버에 복제 | [자세히 알아보기](site-recovery-vmm-to-vmm.md)
보조 데이터센터 | SAN 사용 VMM 서버 | VMM 클라우드에 있는 온-프레미스 Hyper-V 호스트 서버의 가상 컴퓨터를 SAN 복제를 사용하는 다른 데이터센터의 보조 VMM 서버에 복제| [자세히 알아보기](site-recovery-vmm-san.md)
보조 데이터센터 | 단일 VMM 서버 | VMM 클라우드에 있는 온-프레미스 Hyper-V 호스트 서버의 가상 컴퓨터를 동일한 VMM 서버의 보조 클라우드에 복제 | [자세히 알아보기](site-recovery-single-vmm.md) 


## 워크로드 지침

ASR 복제 기술은 가상 컴퓨터에서 실행 중인 모든 응용프로그램과 호환됩니다. 각 응용프로그램에 추가 지원을 하기 위해 응용프로그램 제품팀과 협력하여 추가 테스트를 수행했습니다.

**워크로드** | <p>**Hyper-V 가상 컴퓨터 복제**</p> <p>**(보조 사이트에)**</p> | <p>**Hyper-V 가상 컴퓨터 복제**</p> <p>**(Azure에)**</p> | <p>**VMware 가상 컴퓨터 복제**</p> <p>**(보조 사이트에)**</p> | <p>**VMware 가상 컴퓨터 복제**</p><p>**(Azure에)**</p>
---|---|---|---|---
Active Directory, DNS | Y | Y | Y | 서비스 예정 
웹앱(IIS, SQL) | Y | Y | Y | 서비스 예정
SCOM | Y | Y | Y | 서비스 예정
Sharepoint | Y | Y | Y | 서비스 예정
<p>SAP</p><p>비 클러스터에 대헤 Azure에 SAP 사이트 복제</p> | 예(Microsoft에서 테스트) | 예(Microsoft에서 테스트) | 예(Microsoft에서 테스트) | 서비스 예정
Exchange(비 DAG) | Y | 서비스 예정 | Y | 서비스 예정
원격 데스크톱/VDI | Y | Y | Y | 서비스 예정 
<p>Linux</p> <p>(운영 체제 및 앱)</p> | 예(Microsoft에서 테스트) | 예(Microsoft에서 테스트) | 예(Microsoft에서 테스트) | 서비스 예정 
Dynamics AX | Y | Y | Y | 서비스 예정
Dynamics CRM | 서비스 예정 | 서비스 예정 | Y | 서비스 예정
Oracle | 서비스 예정 | 서비스 예정 | 예(Microsoft에서 테스트) | 서비스 예정


## 기능 및 요구 사항 

이 테이블에서는 주 사이트 복구 기능 및 Azure에 복제, 기본 Hyper-V 복제본 복제 및 SAN을 사용하는 보조 사이트에 복제하는 동안 처리되는 방법을 요약합니다.

<table border="1">
<thead>
<tr>
	<th>기능</th><th>Azure에 복제</th>
	<th>보조 사이트에 복제(Hyper-V 복제본)</th>
	<th>보조 사이트에 복제(SAN)</th>
</tr>
</thead>

<tr>
<td>데이터 복제</td>
<td><p>온-프레미스 서버 및 가상 컴퓨터에 대한 메타데이터는 사이트 복구 자격 증명 모음에 저장됩니다.</p> <p>복제된 데이터는 Azure 저장소에 저장됩니다.</p></td>
<td><p>온-프레미스 서버 및 가상 컴퓨터에 대한 메타데이터는 사이트 복구 자격 증명 모음에 저장됩니다.</p> <p>복제된 데이터는 대상 Hyper-V 서버에서 지정한 위치에 저장됩니다.</p></td>
<td><p>온-프레미스 서버 및 가상 컴퓨터에 대한 메타데이터는 사이트 복구 자격 증명 모음에 저장됩니다.</p> <p>복제된 데이터는 대상 배열 저장소에 저장됩니다.</p></td>
</tr>

<tr>
<td>자격 증명 모음 요구 사항</td>
<td><p>사이트 복구 서비스를 사용하는 Azure 계정</p></td>
<td><p>사이트 복구 서비스를 사용하는 Azure 계정</p>
</td><td><p>사이트 복구 서비스를 사용하는 Azure 계정</p></td>
</tr>

<tr>
<td>복제</td>
<td>원본 Hyper-V 호스트에서 Azure 저장소로 가상 컴퓨터를 복제합니다. 원본 위치로 장애를 복구합니다.</td>
<td>원본 Hyper-V 호스트에서 대상 Hyper-V 호스트로 가상 컴퓨터를 복제합니다. 원본 위치로 장애를 복구합니다.</td>
<td>원본 SAN 저장소에서 대상 SAN 장치로 가상 컴퓨터를 복제합니다. 원본 위치로 장애를 복구합니다.</td>
</tr>

<tr>
<td>가상 컴퓨터</td>
<td>Azure 저장소에 저장된 가상 컴퓨터 하드 디스크</td>
<td>Hyper-V 호스트에 저장된 가상 컴퓨터 하드 디스크</td>
<td>SAN 저장소 배열에 저장된 가상 컴퓨터 하드 디스크</td>
</tr>

<tr>
<td>Azure 저장소</td>
<td>복제된 가상 컴퓨터 하드 디스크 저장에 필요</td>
<td>해당 없음</td>
<td>해당 없음</td>
</tr>

<tr>
<td>SAN 저장소 배열</td>
<td><p>해당 없음</p></td>
<td>해당 없음</td>
<td>SAN 저장소 배열이 원본 및 대상 사이트 둘 다에서 사용 가능하고 VMM을 통해 관리되어야 합니다.</td>
</tr>

<tr>
<td>VMM 서버</td>
<td>원본 사이트의 VMM 서버만 해당됩니다. </td>
<td>원본 및 대상 사이트에 있는 VMM 서버를 사용하는 것이 좋습니다. 단일 VMM 서버의 클라우드 간에 복제할 수 있습니다.</td>
<td>원본 및 대상 VMM 사이트의 VMM 서버입니다. 클라우드에 Hyper-V 클러스터가 하나 이상 포함되어야 합니다.</td>
</tr>

<tr>
<td>VMM 버전</td>
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
<td><p>Azure Site Recovery 공급자</p><p>사이트 복구에 HTTPS를 통한 연결에 사용됨</p></td>
<td>원본 VMM 서버 설치</td>
<td>원본 및 대상 VMM 서버 설치</td>
<td>원본 및 대상 VMM 서버 설치</td>
</tr>

<tr>
<td><p>Azure 복구 서비스 에이전트</p><p>사이트 복구에 HTTPS를 통한 연결에 사용됨</p></td>
<td>Hyper-V 호스트 서버에 설치</td>
<td>필요하지 않음</td>
<td>필요하지 않음</td>
</tr>

<tr>
<td>가상 컴퓨터 복구 지점</td>
<td><p>시간별로 복구 지점을 설정합니다.</p> <p>복구 지점 유지 기간을 지정합니다(0~24시간).</p></td>
<td><p>용량별로 복구 지점을 설정합니다.</p> <p>유지해야 하는 추가 복구 지점 수를 지정합니다(0-15). 기본적으로 복구 지점은 매시간 생성됩니다.</p></td>
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

## 다음 단계

이 개요를 마친 후 [모범 사례를 읽어보고](site-recovery-best-practices.md) 배포 계획을 시작합니다.
 

<!---HONumber=58_postMigration-->