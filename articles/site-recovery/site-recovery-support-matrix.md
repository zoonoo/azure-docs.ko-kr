---
title: Azure 사이트 복구 지원 매트릭스 | Microsoft Docs
description: Azure 사이트 복구에 대한 지원되는 운영 체제 및 구성 요소를 요약해서 설명합니다.
services: site-recovery
documentationcenter: ''
author: rayne-wiselman
manager: jwhit
editor: ''

ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 10/05/2016
ms.author: raynew

---
# <a name="azure-site-recovery-support-matrix"></a>Azure 사이트 복구 지원 매트릭스
이 문서에서는 Azure 사이트 복구 배포에 대한 지원되는 운영 체제 및 구성 요소를 요약해서 설명합니다. 지원되는 구성 요소 및 필수 구성 요소 목록을 해당하는 배포 문서의 각 배포 시나리오에 사용할 수 있지만 이 문서에서는 요약해서 설명합니다.

## <a name="supported-operating-systems-for-virtualization-servers"></a>가상화 서버에 대해 지원되는 운영 체제
| **원본** | **대상** | **호스트 OS** |
| --- | --- | --- | --- |
| **Hyper-V 호스트(VMM 없음)** |Azure |최신 업데이트가 포함된 Windows Server 2012 R2 |
| **Hyper-V 호스트(VMM 포함)** |Azure |최신 업데이트가 포함된 Windows Server 2012 R2 |
| **Hyper-V 호스트(VMM 포함)** |보조 VMM 사이트 |최신 업데이트가 포함된 최소 Windows Server 2012 |
| **VMware 호스트/vCenter** |Azure |vCenter 5.5 또는 6.0(5.5 기능만 지원)  <br/><br/>  최신 업데이트가 있는 vSphere 6.0, 5.5 또는 5.1 |
| **VMware 호스트/vCenter** |보조 VMware 사이트 |vCenter 5.5 또는 6.0(5.5 기능만 지원)  <br/><br/>  최신 업데이트가 있는 vSphere 6.0, 5.5 또는 5.1 |

## <a name="supported-requirements-for-replicated-machines"></a>복제된 컴퓨터에 대해 지원되는 요구 사항
| **원본** | **복제되는 대상** | **대상** | **호스트 OS** |
| --- | --- | --- | --- |
| **Hyper-V VM** |모든 워크로드 |Azure |[Azure에서 지원하는](https://technet.microsoft.com/library/cc794868.aspx) 모든 게스트 OS<br/><br/> VM은 [Azure 요구 사항](site-recovery-best-practices.md#azure-virtual-machine-requirements) |
| **Hyper-V VM(VMM 포함)** |모든 워크로드 |Azure |[Azure에서 지원하는](https://technet.microsoft.com/library/cc794868.aspx) 모든 게스트 OS<br/><br/> VM은 [Azure 요구 사항](site-recovery-best-practices.md#azure-virtual-machine-requirements) |
| **Hyper-V VM(VMM 포함)** |모든 워크로드 |보조 VMM 사이트 |[Hyper-V에서 지원하는](https://technet.microsoft.com/library/mt126277.aspx) |
| **VMware VM** |Windows VM에서 실행되는 모든 워크로드 |Azure |64비트 Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1 이상<br/><br/> VM은 [Azure 요구 사항](site-recovery-best-practices.md#azure-virtual-machine-requirements) |
| **VMware VM** |Linux VM에서 실행되는 모든 워크로드 |Azure |Red Hat Enterprise Linux 6.7, 7.1, 7.2 <br/><br/> Centos 6.5, 6.6, 6.7, 7.0, 7.1, 7.2 <br/><br/> Red Hat 호환 커널 또는 UEK3(Unbreakable Enterprise Kernel Release 3)을 실행하는 Oracle Enterprise Linux 6.4, 6.5 <br/><br/> SUSE Linux Enterprise Server 11 SP3 <br/><br/> 필수 저장소: 파일 시스템(EXT3, ETX4, ReiserFS, XFS), 소프트웨어 장치 매퍼(다중 경로), 볼륨 관리자(LVM2). HP CCISS 컨트롤러 저장소가 있는 물리적 서버는 지원되지 않습니다. ReiserFS 파일 시스템은 SUSE Linux Enterprise Server 11 SP3에서만 지원됩니다.<br/><br/> VM은 [Azure 요구 사항](site-recovery-best-practices.md#azure-virtual-machine-requirements) |
| **VMware VM** |Windows VM에서 실행되는 모든 워크로드 |보조 VMware 사이트 |64비트 Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1 이상 |
| **VMware VM** |Linux VM에서 실행되는 모든 워크로드 |보조 VMware 사이트 |Red Hat Enterprise Linux 6.7, 7.1, 7.2 <br/><br/> Centos 6.5, 6.6, 6.7, 7.0, 7.1, 7.2 <br/><br/> Red Hat 호환 커널 또는 UEK3(Unbreakable Enterprise Kernel Release 3)을 실행하는 Oracle Enterprise Linux 6.4, 6.5 <br/><br/> SUSE Linux Enterprise Server 11 SP3 <br/><br/> 필수 저장소: 파일 시스템(EXT3, ETX4, ReiserFS, XFS), 소프트웨어 장치 매퍼(다중 경로), 볼륨 관리자(LVM2). HP CCISS 컨트롤러 저장소가 있는 물리적 서버는 지원되지 않습니다. ReiserFS 파일 시스템은 SUSE Linux Enterprise Server 11 SP3에서만 지원됩니다. |
| **물리적 서버** |Windows에서 실행되는 모든 워크로드 |Azure |64비트 Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 SP1 이상 |
| **물리적 서버** |Linux에서 실행되는 모든 워크로드 |Azure |Red Hat Enterprise Linux 6.7,7.1,7.2 <br/><br/> Centos 6.5, 6.6,6.7,7.0,7.1,7.2 <br/><br/> Red Hat 호환 커널 또는 UEK3(Unbreakable Enterprise Kernel Release 3)을 실행하는 Oracle Enterprise Linux 6.4, 6.5 <br/><br/> SUSE Linux Enterprise Server 11 SP3 <br/><br/> 필수 저장소: 파일 시스템(EXT3, ETX4, ReiserFS, XFS), 소프트웨어 장치 매퍼(다중 경로), 볼륨 관리자(LVM2). HP CCISS 컨트롤러 저장소가 있는 물리적 서버는 지원되지 않습니다. ReiserFS 파일 시스템은 SUSE Linux Enterprise Server 11 SP3에서만 지원됩니다. |
| **물리적 서버** |Windows에서 실행되는 모든 워크로드 |보조 사이트 |64비트 Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 SP1 이상 |
| **물리적 서버** |Linux에서 실행되는 모든 워크로드 |보조 사이트 |Red Hat Enterprise Linux 6.7,7.1,7.2 <br/><br/> Centos 6.5, 6.6,6.7,7.0,7.1,7.2 <br/><br/> Red Hat 호환 커널 또는 UEK3(Unbreakable Enterprise Kernel Release 3)을 실행하는 Oracle Enterprise Linux 6.4, 6.5 <br/><br/> SUSE Linux Enterprise Server 11 SP3 <br/><br/> 필수 저장소: 파일 시스템(EXT3, ETX4, ReiserFS, XFS), 소프트웨어 장치 매퍼(다중 경로), 볼륨 관리자(LVM2). HP CCISS 컨트롤러 저장소가 있는 물리적 서버는 지원되지 않습니다. ReiserFS 파일 시스템은 SUSE Linux Enterprise Server 11 SP3에서만 지원됩니다. |

## <a name="provider-versions"></a>공급자 버전
| **Name** | **설명** | **최신 버전** | **지원** | **세부 정보** |
| --- | --- | --- | --- | --- |
| **Azure Site Recovery 공급자** |온-프레미스 서버와 Azure/보조 사이트 간 통신 조정  <br/><br/>  온-프레미스 VMM 서버에 설치, VMM 서버가 없는 경우 Hyper-V 서버에 설치 |5.1.1700(포털에서 사용 가능) |[최신 기능 및 수정](https://support.microsoft.com/kb/3155002) | |
| **Azure Site Recovery 통합 설치(VMware-Azure)** |온-프레미스 VMware 서버와 Azure 간 통신 조정  <br/><br/>  온-프레미스 VMware 서버에 설치 |9.3.4246.1(포털에서 사용 가능) |[최신 기능 및 수정](https://support.microsoft.com/kb/3155002) | |
| **모바일 서비스** |온-프레미스 VMware 서버/물리적 서버 및 Azure/보조 사이트 간 복제 조정 |NA(포털에서 사용 가능) |복제하려는 각 VMware VM 또는 물리적 서버에 설치 **MARS(Microsoft Azure Recovery Services) 에이전트** |Hyper-V VM과 Azure 간 복제 조정<br/><br/>  온-프레미스 Hyper-V 서버에 설치(VMM 서버 존재 여부는 관계 없음) |

## <a name="next-steps"></a>다음 단계
[배포 준비](site-recovery-best-practices.md)

<!--HONumber=Oct16_HO2-->


