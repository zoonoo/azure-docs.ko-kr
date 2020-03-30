---
title: SAP 워크로드에 대한 SAP ASE Azure Virtual Machines DBMS 배포 | Microsoft Docs
description: SAP 워크로드에 대한 SAP ASE Azure Virtual Machines DBMS 배포
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/21/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 007e8d87c670376ad334c1c4e58fd93995930b78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616252"
---
# <a name="sap-ase-azure-virtual-machines-dbms-deployment-for-sap-workload"></a>SAP 워크로드에 대한 SAP ASE Azure Virtual Machines DBMS 배포

이 문서에서는 Azure IaaS에서 SAP ASE를 배포할 때 고려할 여러 가지 영역을 다룹니다. 이 문서의 사전 조건으로, [SAP 워크로드용 Azure Virtual Machines DBMS 배포 고려 사항](dbms_guide_general.md) 문서 및 [Azure의 SAP 워크로드 설명서](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started)의 다른 가이드를 읽어야 합니다. 이 문서에서는 Linux 및 Windows 운영 체제에서 실행되는 SAP ASE를 다룹니다. Azure에서 지원되는 최소 릴리스는 SAP ASE 16.0 패치 수준 2입니다.  최신 버전의 SAP 및 최신 패치 수준을 배포하는 것이 좋습니다.  최소 SAP ASE 16.3 패치 레벨 7을 권장합니다.  SAP의 최신 버전은 [대상 ASE 16.0 릴리스 일정 및 CR 목록 정보에서](https://wiki.scn.sap.com/wiki/display/SYBASE/Targeted+ASE+16.0+Release+Schedule+and+CR+list+Information)찾을 수 있습니다.

SAP 응용 프로그램 또는 설치 미디어 위치를 사용 하 여 릴리스 지원에 대 한 추가 정보는 다음 위치에 있는 SAP 제품 가용성 매트릭스에서 찾을 수 있습니다.

- [SAP 지원 노트 #2134316](https://launchpad.support.sap.com/#/notes/2134316)
- [SAP 지원 노트 #1941500](https://launchpad.support.sap.com/#/notes/1941500)
- [SAP 지원 노트 #1590719](https://launchpad.support.sap.com/#/notes/1590719)
- [SAP 지원 노트 #1973241](https://launchpad.support.sap.com/#/notes/1973241)

비고: SAP 세계 내외의 문서 전체에서 제품 이름은 Sybase ASE 또는 SAP ASE 또는 경우에 따라 둘 다로 참조됩니다. 일관성을 유지하기 위해 이 설명서에서 **SAP ASE라는** 이름을 사용합니다.

## <a name="operating-system-support"></a>운영 체제 지원
SAP 제품 가용성 매트릭스에는 각 SAP 응용 프로그램에 대해 지원되는 운영 체제 및 SAP 커널 조합이 포함되어 있습니다.  리눅스 배포판 SUSE 12.x, SUSE 15.x, 레드 햇 7.x 는 완전히 지원됩니다.  SAP ASE용 운영 체제로 오라클 리눅스는 지원되지 않습니다.  사용 가능한 최신 Linux 릴리스를 사용하는 것이 좋습니다. Windows 고객은 Windows 서버 2016 또는 Windows 서버 2019 릴리스를 사용해야 합니다.  Windows 2012와 같은 이전 Windows 릴리스는 기술적으로 지원되지만 최신 Windows 버전은 항상 권장됩니다.


## <a name="specifics-to-sap-ase-on-windows"></a>Windows의 SAP ASE에 대한 고유 정보
Microsoft Azure부터 기존 SAP ASE 애플리케이션을 Azure Virtual Machines로 마이그레이션할 수 있습니다. Azure Virtual Machine에서 SAP ASE를 사용하면 이러한 애플리케이션을 Microsoft Azure로 쉽게 마이그레이션하여 엔터프라이즈 수준의 애플리케이션에 대한 배포, 관리 및 유지 관리의 총 소유 비용을 줄일 수 있습니다. Azure Virtual Machine에서 SAP ASE를 사용하면 관리자와 개발자가 온-프레미스와 동일한 개발 및 관리 도구를 사용할 수 있습니다.

Microsoft Azure는 가장 작은 SAP 시스템과 환경부터 수천 명의 사용자가 있는 대규모 SAP 시스템과 환경까지 실행할 수 있는 다양한 가상 머신 유형을 제공합니다. SAP 크기 조정 SAPS 번호는 다른 SAP 인증 VM SUS의 [#1928533 SAP 지원 노트에](https://launchpad.support.sap.com/#/notes/1928533)제공됩니다.

Windows에 SAP ASE를 설치하는 설명서는 [Windows용 SAP ASE 설치 가이드에서](https://help.sap.com/viewer/36031975851a4f82b1022a9df877280b/16.0.3.7/en-US/a660d3f1bc2b101487cbdbf10069c3ac.html) 찾을 수 있습니다.

메모리의 페이지 잠금은 SAP ASE 데이터베이스 버퍼가 페이징아웃되지 않도록 하는 설정입니다.  이 설정은 메모리가 많은 대규모 사용 중인 시스템에 유용합니다. 자세한 내용은 BC-DB-SYB에 문의하십시오. 


## <a name="linux-operating-system-specific-settings"></a>리눅스 운영 체제 특정 설정
리눅스 VM에서, 프로필 SAP-ASE 리눅스 거대한 페이지로 실행기본적으로 `saptune` 활성화되어야하고 명령으로 확인할 수 있습니다  

`cat /proc/meminfo` 

페이지 크기는 일반적으로 2048KB입니다. 자세한 내용은 [리눅스에서 기사 거대한 페이지를](https://help.sap.com/viewer/ecbccd52e7024feaa12f4e780b43bc3b/16.0.3.7/en-US/a703d580bc2b10149695f7d838203fad.html) 참조하십시오 


## <a name="recommendations-on-vm-and-disk-structure-for-sap-ase-deployments"></a>SAP ASE 배포를 위한 VM 및 디스크 구조에 대한 권장 사항

SAP NetWeaver 응용 프로그램에 대한 SAP ASE는 중간 크기의 SAP ASE 데이터베이스 서버에 사용되는 일반적인 VM 유형이 Esv3를 포함할 [#1928533 SAP 지원 노트에](https://launchpad.support.sap.com/#/notes/1928533) 나열된 모든 VM 유형에서 지원됩니다.  대규모 다중 테라바이트 데이터베이스는 M 시리즈 VM 유형을 활용할 수 있습니다. SAP ASE 트랜잭션 로그 디스크 쓰기 성능은 M 시리즈 쓰기 가속기를 사용하도록 설정하여 향상될 수 있습니다. 쓰기 가속기는 SAP ASE가 로그 쓰기를 수행하는 방식으로 인해 SAP ASE로 신중하게 테스트해야 합니다.  [#2816580 SAP 지원 노트를](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator) 검토하고 성능 테스트를 실행하는 것이 좋습니다.  
쓰기 액셀러레이터는 트랜잭션 로그 디스크전용으로 설계되었습니다. 디스크 수준 캐시는 NONE으로 설정해야 합니다. Azure 쓰기 가속기가 다른 DBMS와 유사한 개선 사항이 나타나지 않는 경우 놀라지 마십시오. SAP ASE가 트랜잭션 로그에 기록하는 방식에 따라 Azure Write 액셀러레이터가 가속화가 거의 또는 전혀 없을 수 있습니다.
데이터 장치 및 로그 장치에는 별도의 디스크를 권장합니다.  시스템은 sybsecurity를 데이터베이스하고 `saptools` 전용 디스크를 필요로하지 않으며 SAP 데이터베이스 데이터 및 로그 장치를 포함하는 디스크에 배치 할 수 있습니다 

![SAP ASE용 스토리지 구성](./media/dbms-guide-sap-ase/sap-ase-disk-structure.png)

### <a name="file-systems-stripe-size--io-balancing"></a>파일 시스템, 스트라이프 크기 & IO 밸런싱 
SAP ASE는 달리 구성되지 않는 한 데이터를 디스크 저장 장치에 순차적으로 기록합니다. 즉, 4개의 장치가 있는 빈 SAP ASE 데이터베이스는 첫 번째 장치에만 데이터를 씁니다.  다른 디스크 장치는 첫 번째 장치가 가득 차있을 때만 기록됩니다.  각 SAP ASE 장치에 대한 읽기 및 쓰기 IO의 양은 다를 수 있습니다. 사용 가능한 모든 Azure 디스크에서 디스크 IO의 균형을 맞추려면 Windows 저장소 공간 또는 Linux LVM2를 사용해야 합니다. Linux에서는 XFS 파일 시스템을 사용하여 디스크를 포맷하는 것이 좋습니다. LVM 스트라이프 크기는 성능 테스트를 통해 테스트해야 합니다. 128 KB 스트라이프 사이즈가 좋은 출발점입니다. Windows에서는 NTFS 할당 단위 크기(AUS)를 테스트해야 합니다. 64 KB는 시작 값으로 사용할 수 있습니다. 

[SAP 적응](https://blogs.sap.com/2014/07/09/configuring-automatic-database-space-expansion-in-sap-adaptive-server-enterprise/) 형 서버 엔터프라이즈 및 [SAP 지원 참고 #1815695](https://launchpad.support.sap.com/#/notes/1815695)자동 데이터베이스 공간 확장 구성 문서에 설명 된 대로 자동 데이터베이스 확장을 구성 하는 것이 좋습니다. 

### <a name="sample-sap-ase-on-azure-virtual-machine-disk-and-file-system-configurations"></a>Azure 가상 머신, 디스크 및 파일 시스템 구성에서 SAP ASE 샘플 
아래 템플릿은 Linux및 Windows 모두에 대한 샘플 구성을 보여 준다. 가상 컴퓨터 및 디스크 구성을 확인하기 전에 개별 VM의 네트워크 및 스토리지 대역폭 할당량이 비즈니스 요구 사항을 충족하기에 충분한지 확인합니다. 또한 Azure VM 형식마다 VM에 연결할 수 있는 디스크의 최대 수가 다릅니다. 예를 들어 E4s_v3 VM에는 48MB/sec 스토리지 IO 처리량이 제한됩니다. 데이터베이스 백업 활동에 필요한 저장소 처리량이 초당 48MB 이상이 필요한 경우 더 많은 저장소 대역폭 처리량이 있는 더 큰 VM 유형은 피할 수 없습니다. Azure 저장소를 구성할 때 특히 [Azure Premium 저장소의](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance) 경우 용량GB당 처리량과 IOPS가 변경된다는 점에 유의해야 합니다. 이 항목에 대한 자세한 내용은 [Azure에서 사용할 수 있는 디스크 유형입니다.](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types) 특정 Azure VM 형식에 대한 할당량은 [메모리에 최적화된 가상 시스템 크기](https://docs.microsoft.com/azure/virtual-machines/sizes-memory) 및 연결된 아티클에 설명되어 있습니다. 

> [!NOTE]
>  DBMS 시스템이 온-프레미스에서 Azure로 이동하는 경우 VM에서 모니터링을 수행하고 CPU, 메모리, IOPS 및 저장소 처리량을 평가하는 것이 좋습니다. 위에서 언급한 문서에 설명된 VM 할당량 한도와 관찰된 피크 값을 비교합니다.

아래 제공된 예제는 설명적인 용도로 사용되며 개별 요구 사항에 따라 수정할 수 있습니다. SAP ASE의 설계로 인해 데이터 장치의 수는 다른 데이터베이스만큼 중요하지 않습니다. 이 문서에 자세히 설명된 데이터 장치 수는 가이드일 뿐입니다. 

SAP 솔루션 관리자와 같이 데이터베이스 크기가 50GB ~ 250GB인 소규모 SAP ASE DB 서버에 대한 구성의 예는 다음과 같습니다.

| Configuration | Windows | Linux | 주석 |
| --- | --- | --- | --- |
| VM 유형 | E4s_v3 (4 vCPU / 32 GB RAM) | E4s_v3 (4 vCPU / 32 GB RAM) | --- |
| 가속 네트워킹 | 사용 | 사용 | ---|
| SAP ASE 버전 | 16.3 PL 7 이상 | 16.3 PL 7 이상 | --- |
| 데이터 장치 번호 | 4 | 4 | ---|
| 로그 장치 번호 | 1 | 1 | --- |
| # 온도 장치 | 1 | 1 | SAP BW 워크로드에 대한 자세한 내용은 |
| 운영 체제 | Windows Server 2019 | SUSE 12 SP4/ 15 SP1 또는 RHEL 7.6 | --- |
| 디스크 집계 | 스토리지 공간 | LVM2 | --- |
| 파일 시스템 | NTFS | Xfs |
| 블록 크기 형식 지정 | 워크로드 테스트 필요 | 워크로드 테스트 필요 | --- |
| # 및 데이터 디스크의 유형 | 프리미엄 스토리지: 2 x P10 (RAID0) | 프리미엄 스토리지: 2 x P10 (RAID0)| 캐시 = 읽기 전용 |
| # 및 로그 디스크의 유형 | 프리미엄 스토리지: 1 x P20  | 프리미엄 스토리지: 1 x P20 | 캐시 = 없음 |
| ASE 맥스메모리 매개변수 | 실제 RAM의 90% | 실제 RAM의 90% | 단일 인스턴스를 가정 |
| # 백업 장치 | 4 | 4| --- |
| # 및 백업 디스크의 유형 | 1 | 1 | --- |


데이터베이스 크기가 250GB ~ 750GB인 중형 SAP ASE DB 서버의 구성의 예는 소규모 SAP Business Suite 시스템과 같이 보일 수 있습니다.

| Configuration | Windows | Linux | 주석 |
| --- | --- | --- | --- |
| VM 유형 | E16s_v3 (16 vCPU / 128 GB RAM) | E16s_v3 (16 vCPU / 128 GB RAM) | --- |
| 가속 네트워킹 | 사용 | 사용 | ---|
| SAP ASE 버전 | 16.3 PL 7 이상 | 16.3 PL 7 이상 | --- |
| 데이터 장치 번호 | 8 | 8 | ---|
| 로그 장치 번호 | 1 | 1 | --- |
| # 온도 장치 | 1 | 1 | SAP BW 워크로드에 대한 자세한 내용은 |
| 운영 체제 | Windows Server 2019 | SUSE 12 SP4/ 15 SP1 또는 RHEL 7.6 | --- |
| 디스크 집계 | 스토리지 공간 | LVM2 | --- |
| 파일 시스템 | NTFS | Xfs |
| 블록 크기 형식 지정 | 워크로드 테스트 필요 | 워크로드 테스트 필요 | --- |
| # 및 데이터 디스크의 유형 | 프리미엄 스토리지: 4 x P20 (RAID0) | 프리미엄 스토리지: 4 x P20 (RAID0)| 캐시 = 읽기 전용 |
| # 및 로그 디스크의 유형 | 프리미엄 스토리지: 1 x P20  | 프리미엄 스토리지: 1 x P20 | 캐시 = 없음 |
| ASE 맥스메모리 매개변수 | 실제 RAM의 90% | 실제 RAM의 90% | 단일 인스턴스를 가정 |
| # 백업 장치 | 4 | 4| --- |
| # 및 백업 디스크의 유형 | 1 | 1 | --- |

데이터베이스 크기가 750GB ~ 2000GB인 소규모 SAP ASE DB 서버의 구성의 예는 더 큰 SAP Business Suite 시스템과 같이 보일 수 있습니다.

| Configuration | Windows | Linux | 주석 |
| --- | --- | --- | --- |
| VM 유형 | E64s_v3 (64 vCPU / 432 GB RAM) | E64s_v3 (64 vCPU / 432 GB RAM) | --- |
| 가속 네트워킹 | 사용 | 사용 | ---|
| SAP ASE 버전 | 16.3 PL 7 이상 | 16.3 PL 7 이상 | --- |
| 데이터 장치 번호 | 16 | 16 | ---|
| 로그 장치 번호 | 1 | 1 | --- |
| # 온도 장치 | 1 | 1 | SAP BW 워크로드에 대한 자세한 내용은 |
| 운영 체제 | Windows Server 2019 | SUSE 12 SP4/ 15 SP1 또는 RHEL 7.6 | --- |
| 디스크 집계 | 스토리지 공간 | LVM2 | --- |
| 파일 시스템 | NTFS | Xfs |
| 블록 크기 형식 지정 | 워크로드 테스트 필요 | 워크로드 테스트 필요 | --- |
| # 및 데이터 디스크의 유형 | 프리미엄 스토리지: 4 x P30 (RAID0) | 프리미엄 스토리지: 4 x P30 (RAID0)| 캐시 = 읽기 전용 |
| # 및 로그 디스크의 유형 | 프리미엄 스토리지: 1 x P20  | 프리미엄 스토리지: 1 x P20 | 캐시 = 없음 |
| ASE 맥스메모리 매개변수 | 실제 RAM의 90% | 실제 RAM의 90% | 단일 인스턴스를 가정 |
| # 백업 장치 | 4 | 4| --- |
| # 및 백업 디스크의 유형 | 1 | 1 | --- |


전 세계적으로 사용되는 대규모 SAP Business Suite 시스템과 같이 데이터베이스 크기가 2TB+인 소규모 SAP ASE DB 서버에 대한 구성의 예는 다음과 같습니다.

| Configuration | Windows | Linux | 주석 |
| --- | --- | --- | --- |
| VM 유형 | M 시리즈(1.0 ~ 4.0TB RAM)  | M 시리즈(1.0 ~ 4.0TB RAM) | --- |
| 가속 네트워킹 | 사용 | 사용 | ---|
| SAP ASE 버전 | 16.3 PL 7 이상 | 16.3 PL 7 이상 | --- |
| 데이터 장치 번호 | 32 | 32 | ---|
| 로그 장치 번호 | 1 | 1 | --- |
| # 온도 장치 | 1 | 1 | SAP BW 워크로드에 대한 자세한 내용은 |
| 운영 체제 | Windows Server 2019 | SUSE 12 SP4/ 15 SP1 또는 RHEL 7.6 | --- |
| 디스크 집계 | 스토리지 공간 | LVM2 | --- |
| 파일 시스템 | NTFS | Xfs |
| 블록 크기 형식 지정 | 워크로드 테스트 필요 | 워크로드 테스트 필요 | --- |
| # 및 데이터 디스크의 유형 | 프리미엄 스토리지: 4+ x P30 (RAID0) | 프리미엄 스토리지: 4+ x P30 (RAID0)| 캐시 = 읽기 전용, Azure 울트라 디스크 고려 |
| # 및 로그 디스크의 유형 | 프리미엄 스토리지: 1 x P20  | 프리미엄 스토리지: 1 x P20 | 캐시 = 없음, Azure 울트라 디스크 고려 |
| ASE 맥스메모리 매개변수 | 실제 RAM의 90% | 실제 RAM의 90% | 단일 인스턴스를 가정 |
| # 백업 장치 | 16 | 16 | --- |
| # 및 백업 디스크의 유형 | 4 | 4 | LVM2/스토리지 공간 사용 |


### <a name="backup--restore-considerations-for-sap-ase-on-azure"></a>백업 & Azure에서 SAP ASE에 대한 고려 사항을 복원합니다.
데이터 및 백업 장치의 수를 늘리면 백업 및 복원 성능이 향상됩니다. 앞에 표시된 표에 표시된 대로 SAP ASE 백업 장치를 호스팅하는 Azure 디스크를 스트라이프하는 것이 좋습니다. 백업 장치와 디스크 의 수와 균형을 맞추고 백업 처리량이 총 VM 처리량 할당량의 40%-50%를 초과하지 않도록 주의해야 합니다. SAP 백업 압축을 기본값으로 사용하는 것이 좋습니다. 자세한 내용은 문서에서 찾을 수 있습니다.

- [SAP 지원 노트 #1588316](https://launchpad.support.sap.com/#/notes/1588316)
- [SAP 지원 노트 #1801984](https://launchpad.support.sap.com/#/notes/1801984)
- [SAP 지원 노트 #1585981](https://launchpad.support.sap.com/#/notes/1585981) 

드라이브 D:\ 또는 /temp 공간을 데이터베이스 또는 로그 덤프 대상으로 합니다.

### <a name="impact-of-database-compression"></a>데이터베이스 압축의 영향
I/O 대역폭이 제한 요소가 될 수 있는 구성에서 IOPS를 줄이는 조치는 Azure와 같은 IaaS 시나리오에서 실행할 수 있는 워크로드를 늘리는 데 도움이 될 수 있습니다. 따라서 기존 SAP 데이터베이스를 Azure에 업로드하기 전에 SAP ASE 압축이 사용되는지 확인하는 것이 좋습니다.

Azure에 업로드하기 전에 압축을 적용하는 것이 좋은 이유는 다음과 같습니다.

* Azure에 업로드할 데이터의 양이 적어집니다.
* 더 향상된 CPU가 있는 강력한 하드웨어 또는 더 높은 I/O 대역폭이나 낮은 I/O 대기 시간의 온-프레미스를 사용할 경우 압축 실행 시간이 짧아집니다.
* 데이터베이스 크기가 작을수록 디스크 할당 비용이 줄어들 수 있습니다.

데이터 및 LOB 압축은 Azure Virtual Machines에서 호스트되는 VM에서도 온-프레미스에서와 마찬가지로 작동합니다. 기존 SAP ASE 데이터베이스에서 압축이 이미 사용 중인지 확인하는 방법에 대한 자세한 내용은 [SAP 지원 참고 사항 1750510을](https://launchpad.support.sap.com/#/notes/1750510)확인하십시오. SAP ASE 데이터베이스 압축에 대한 자세한 내용은 [SAP 지원 참고 사항을 #2121797](https://launchpad.support.sap.com/#/notes/2121797)

## <a name="high-availability-of-sap-ase-on-azure"></a>Azure에서 SAP ASE의 고가용성 
HADR 사용자 가이드는 2노드 SAP ASE "상시 켜기" 솔루션의 설정 및 구성에 대해 자세히 설명합니다.  또한 세 번째 재해 복구 노드도 지원됩니다. SAP ASE는 공유 디스크 및 기본 OS 클러스터링(부동 IP)을 비롯한 많은 높은 사용 가능한 구성을 지원합니다. Azure에서 지원되는 유일한 구성은 부동 IP 없이 오류 관리자를 사용하는 것입니다.  부동 IP 주소 메서드는 Azure에서 작동하지 않습니다.  SAP 커널은 "HA 인식" 응용 프로그램이며 기본 및 보조 SAP ASE 서버에 대해 알고 있습니다. SAP ASE와 Azure 간에 는 긴밀한 통합이 없으며 Azure 내부 로드 밸런서가 사용되지 않습니다. 따라서 표준 SAP ASE 문서 는 [SAP ASE HADR 사용자 가이드를](https://help.sap.com/viewer/efe56ad3cad0467d837c8ff1ac6ba75c/16.0.3.7/en-US/a6645e28bc2b1014b54b8815a64b87ba.html) 통해 수행해야 합니다. 

> [!NOTE]
> Azure에서 지원되는 유일한 구성은 부동 IP 없이 오류 관리자를 사용하는 것입니다.  부동 IP 주소 메서드는 Azure에서 작동하지 않습니다. 

### <a name="third-node-for-disaster-recovery"></a>재해 복구를 위한 세 번째 노드
로컬 고가용성을 위해 SAP ASE Always-On을 사용하는 것 외에도 구성을 다른 Azure 리전에서 비동기적으로 복제된 노드로 확장할 수 있습니다. 이러한 시나리오에 대한 설명서는 [여기에서](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/installation-procedure-for-sybase-16-3-patch-level-3-always-on/ba-p/368199)찾을 수 있습니다.

## <a name="sap-ase-database-encryption--ssl"></a>SSL에 & SAP ASE 데이터베이스 암호화 
SAP 소프트웨어 프로비저닝 관리자(SWPM)는 설치 하는 동안 데이터베이스를 암호화 하는 옵션을 제공 합니다.  암호화를 사용하려면 SAP 전체 데이터베이스 암호화를 사용하는 것이 좋습니다.  자세한 내용은 다음 에 설명되어 있습니다.

- [SAP 지원 노트 #2556658](https://launchpad.support.sap.com/#/notes/2556658)
- [SAP 지원 노트 #2224138](https://launchpad.support.sap.com/#/notes/2224138)
- [SAP 지원 노트 #2401066](https://launchpad.support.sap.com/#/notes/2401066)
- [SAP 지원 노트 #2593925](https://launchpad.support.sap.com/#/notes/2593925) 

> [!NOTE]
> SAP ASE 데이터베이스가 암호화되면 백업 덤프 압축이 작동하지 않습니다. 또한 [SAP 지원 #2680905](https://launchpad.support.sap.com/#/notes/2680905) 참조 

## <a name="sap-ase-on-azure-deployment-checklist"></a>Azure 배포 검사 목록에 있는 SAP ASE
 
- SAP ASE 16.3 PL7 이상 배포
- 오류 관리자 및 SAPHostAgent의 최신 버전 및 패치로 업데이트
- Windows 2019, Suse 15.1 또는 Redhat 7.6 이상과 같은 최신 인증 OS에 배포
- SAP 인증 VM 사용 – Es_v3 또는 x-대형 시스템 M 시리즈 VM SUS와 같은 고메모리 Azure VM SUS 사용 권장
- 디스크 IOPS 및 VM의 총 VM 집계 처리량 할당량을 디스크 설계와 일치시다.  충분한 수의 디스크 배포
- 올바른 스트라이프 크기 및 파일 시스템으로 Windows 저장소 공간 또는 Linux LVM2를 사용하여 디스크 집계
- 데이터, 로그, 임시 및 백업 을 위한 충분한 수의 장치 만들기
- x-large 시스템에 UltraDisk 사용 고려 
- 리눅스 OS에서 SAP-ASE를 실행 `saptune` 
- DB 암호화로 데이터베이스 보호 – Azure 키 볼트에 키를 수동으로 저장 
- Azure [검사 목록에서 SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist) 완료 
- 로그 백업 및 전체 백업 구성 
- HA/DR 테스트, 백업 및 복원 및 스트레스 & 볼륨 테스트 수행 
- 자동 데이터베이스 확장이 작동하는지 확인 

## <a name="using-dbacockpit-to-monitor-database-instances"></a>DBACockpit을 사용하여 데이터베이스 인스턴스 모니터링
SAP ASE를 데이터베이스 플랫폼으로 사용하는 SAP 시스템의 경우 DBACockpit는 트랜잭션 DBACockpit에 포함된 브라우저 창 또는 Webdynpro로 액세스할 수 있습니다. 그러나 데이터베이스를 모니터링하고 관리하기 위한 모든 기능은 DBACockpit의 Webdynpro 구현에서만 사용할 수 있습니다.

DBACockpit의 Webdynpro 구현에 의해 사용되는 모든 SAP NetWeaver 기능을 사용하려면 온-프레미스 시스템처럼 여러 단계가 필요합니다. #1245200 [SAP 지원 노트를](https://launchpad.support.sap.com/#/notes/1245200) 따라 webdynpros의 사용을 활성화하고 필요한 것들을 생성합니다. 위의 참고 사항의 지침을 따르는 경우 http 및`ICM`https 연결에 사용할 포트와 함께 인터넷 통신 관리자 () 도 구성합니다. HTTP의 기본 설정은 다음과 같이 표시됩니다.

> icm/server_port_0 = PROT=HTTP,PORT=8000,PROCTIMEOUT=600,TIMEOUT=600
> 
> icm/server_port_1 = PROT=HTTPS,PORT=443$$,PROCTIMEOUT=600,TIMEOUT=600
> 
> 

그리고 트랜잭션 DBACockpit에서 생성된 링크는 다음과 같이 표시됩니다.

> https:\//\<정규 화,>:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http:\//\<정규화 호스트 이름>: 8000/sap/bc/webdynpro/sap/dba_cockpit
> 
> 

SAP 시스템을 호스트하는 Azure Virtual Machine이 AD 및 DNS에 연결된 방법에 따라 ICM이 DBACockpit를 여는 머신에서 확인할 수 있는 정규화된 호스트 이름을 사용하는지 확인해야 합니다. [ICM이](https://launchpad.support.sap.com/#/notes/773830) 프로파일 매개 변수를 기반으로 정규화된 호스트 이름을 결정하고 필요한 경우 매개 변수 icm/host_name_full 명시적으로 설정하는 방법을 보려면 SAP 지원 #773830 참조하세요.

온-프레미스와 Azure 간의 상호 온-프레미스 연결 없이 클라우드 전용 시나리오에 VM을 배포한 `domainlabel`경우 공용 IP 주소와 를 정의해야 합니다. VM의 공용 DNS 이름 형식은 다음과 같이 표시됩니다.

> `<custom domainlabel`>.`<azure region`>.cloudapp.azure.com
> 
> 

DNS 이름과 관련된 자세한 내용은 [여기][가상 시스템-azurerm-azuresm]에서 찾을 수 있습니다.

SAP 프로필 매개 변수 icm/host_name_full을 Azure VM의 DNS 이름으로 설정할 경우 링크는 다음과 같습니다.

> https:\//mydomainlabel.westeurope.cloudapp.net:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http:\//mydomainlabel.westeurope.cloudapp.net:8000/sap/bc/webdynpro/sap/dba_cockpit

이 경우 다음을 확인해야 합니다.

* Azure Portal의 네트워크 보안 그룹에 ICM과 통신하는 데 사용되는 TCP/IP 포트에 대한 인바운드 규칙을 추가합니다.
* Windows 방화벽 구성에 ICM과 통신하는 데 사용되는 TCP/IP 포트에 대한 인바운드 규칙을 추가합니다.

사용 가능한 모든 수정 사항을 자동으로 가져오려면 SAP 버전에 적용할 수 있는 수정 사항 모음 SAP Note를 정기적으로 적용하는 것이 좋습니다.

* [SAP 지원 노트 #1558958](https://launchpad.support.sap.com/#/notes/1558958)
* [SAP 지원 노트 #1619967](https://launchpad.support.sap.com/#/notes/1619967)
* [SAP 지원 노트 #1882376](https://launchpad.support.sap.com/#/notes/1882376)

SAP ASE의 DBA Cockpit에 대한 자세한 내용은 다음 SAP Note에서 확인할 수 있습니다.

* [SAP 지원 노트 #1605680](https://launchpad.support.sap.com/#/notes/1605680)
* [SAP 지원 노트 #1757924](https://launchpad.support.sap.com/#/notes/1757924)
* [SAP 지원 노트 #1757928](https://launchpad.support.sap.com/#/notes/1757928)
* [SAP 지원 노트 #1758182](https://launchpad.support.sap.com/#/notes/1758182)
* [SAP 지원 노트 #1758496](https://launchpad.support.sap.com/#/notes/1758496)    
* [SAP 지원 노트 #1814258](https://launchpad.support.sap.com/#/notes/1814258)
* [SAP 지원 노트 #1922555](https://launchpad.support.sap.com/#/notes/1922555)
* [SAP 지원 노트 #1956005](https://launchpad.support.sap.com/#/notes/1956005)


## <a name="useful-links-notes--whitepapers-for-sap-ase"></a>유용한 링크, SAP ASE에 대한 & 백서 참고 사항
[Sybase ASE 16.3 PL7 문서의](https://help.sap.com/viewer/product/SAP_ASE/16.0.3.7/en-US) 시작 페이지는 다음과 같은 문서의 다양한 문서에 대한 링크를 제공합니다.

- SAP ASE 학습 여정 - 관리 & 모니터링
- SAP ASE 학습 여정 - 설치 & 업그레이드

도움이됩니다. 또 다른 유용한 문서는 [마이그레이션 및 런타임을 위한 SAP 적응형 서버 엔터프라이즈 모범 사례의 SAP 응용 프로그램입니다.](https://assets.cdn.sap.com/sapcom/docs/2016/06/26450353-767c-0010-82c7-eda71af511fa.pdf)

기타 유용한 SAP 지원 노트는 다음과 같습니다.

- [SAP 지원 노트 #2134316](https://launchpad.support.sap.com/#/notes/2134316) 
- [SAP 지원 노트 #1748888](https://launchpad.support.sap.com/#/notes/1748888) 
- [SAP 지원 노트 #2588660](https://launchpad.support.sap.com/#/notes/2588660) 
- [SAP 지원 노트 #1680803](https://launchpad.support.sap.com/#/notes/1680803) 
- [SAP 지원 노트 #1724091](https://launchpad.support.sap.com/#/notes/1724091) 
- [SAP 지원 노트 #1775764](https://launchpad.support.sap.com/#/notes/1775764) 
- [SAP 지원 노트 #2162183](https://launchpad.support.sap.com/#/notes/2162183) 
- [SAP 지원 노트 #1928533](https://launchpad.support.sap.com/#/notes/1928533)
- [SAP 지원 노트 #2015553](https://launchpad.support.sap.com/#/notes/2015553)
- [SAP 지원 노트 #1750510](https://launchpad.support.sap.com/#/notes/1750510) 
- [SAP 지원 노트 #1752266](https://launchpad.support.sap.com/#/notes/1752266) 
- [SAP 지원 노트 #2162183](https://launchpad.support.sap.com/#/notes/2162183) 
- [SAP 지원 노트 #1588316](https://launchpad.support.sap.com/#/notes/158831) 

기타 정보는 다음에 게시됩니다. 

- [SAP 어댑티브 서버 엔터프라이즈의 SAP 애플리케이션](https://community.sap.com/topics/applications-on-ase)
- [시베이스 정보 센터](http://infocenter.sybase.com/help/index.jsp) 

월간 뉴스레터는 [#2381575 SAP 지원 노트를](https://launchpad.support.sap.com/#/notes/2381575) 통해 게시됩니다. 

[3번째 DR 노드 설정으로 항상 켜진 Sybase ASE](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/installation-procedure-for-sybase-16-3-patch-level-3-always-on/ba-p/368199) 

## <a name="next-steps"></a>다음 단계
[Azure에서 SAP 워크로드: 계획 및 배포 검사 목록](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist) 확인

