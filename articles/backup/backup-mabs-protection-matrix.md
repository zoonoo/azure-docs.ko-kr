---
title: MABS(Azure Backup Server) V3 UR1 보호 매트릭스
description: 이 문서에서는 Azure Backup Server에서 보호하는 모든 워크로드, 데이터 형식 및 설치 프로그램을 나열하는 지원 매트릭스를 제공합니다.
ms.date: 03/19/2020
ms.topic: conceptual
ms.openlocfilehash: d8f2a38db468ec57cdc0a4f0813fe7ae8e341d52
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102609773"
---
# <a name="mabs-azure-backup-server-v3-ur1-protection-matrix"></a>MABS(Azure Backup Server) V3 UR1 보호 매트릭스

이 문서에는 다양한 서버 및 Azure Backup Server로 보호할 수 있는 워크로드가 나와 있습니다. 다음 매트릭스는 Azure Backup Server를 사용하여 보호할 수 있는 항목을 나열합니다.

MABS v3 UR1에 대해 다음 행렬을 사용 합니다.

* 워크로드 - 기술의 워크로드 유형.

* 버전 – 워크 로드에 대해 지원 되는 MABS 버전입니다.

* MABS 설치 – MABS를 설치 하려는 컴퓨터/위치입니다.

* 보호 및 복구 - 지원되는 배포 또는 지원되는 스토리지 컨테이너 같은 워크로드에 대한 세부 정보를 나열합니다.

>[!NOTE]
>32 비트 보호 에이전트에 대 한 지원은 MABS v3 UR1에서 더 이상 사용 되지 않습니다. [32 비트 보호 에이전트 사용](backup-mabs-whats-new-mabs.md#32-bit-protection-agent-deprecation)중단을 참조 하세요.

## <a name="protection-support-matrix"></a>보호 지원 매트릭스

다음 섹션에서는 MABS에 대 한 보호 지원 매트릭스에 대해 자세히 설명 합니다.

* [애플리케이션 백업](#applications-backup)
* [VM 백업](#vm-backup)
* [Linux](#linux)

## <a name="applications-backup"></a>애플리케이션 백업

| **작업**               | **버전**                                                  | **Azure Backup Server 설치**                       | **지원 되는 Azure Backup Server** | **보호 및 복구**                                  |
| -------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | --------------------------------- | ------------------------------------------------------------ |
| 클라이언트 컴퓨터 (64 비트) | Windows 10                                                  | 물리적 서버  <br><br>    Hyper-V 가상 머신   <br><br>   VMware 가상 머신 | V3 UR1                            | 볼륨, 공유, 폴더, 파일, 중복 제거 된 볼륨   <br><br>   보호되는 볼륨은 NTFS여야 합니다. FAT 및 FAT32는 지원되지 않습니다.  <br><br>    볼륨은 1GB 이상이어야 합니다. Azure Backup Server는 VSS (볼륨 섀도 복사본 서비스)를 사용 하 여 데이터 스냅숏을 생성 하며, 스냅숏은 볼륨이 1gb 이상인 경우에만 작동 합니다. |
| Servers (64 비트)          | Windows Server 2019, 2016, 2012 R2, 2012                    | Azure 가상 머신 (워크 로드가 Azure 가상 머신으로 실행 되는 경우)  <br><br>    실제 서버  <br><br>    Hyper-V 가상 머신 <br><br>     VMware 가상 머신  <br><br>    Azure Stack | V3 UR1                            | 볼륨, 공유, 폴더, 파일 <br><br>    중복 제거 된 볼륨 (NTFS 전용) <br><br>Windows Server 2019에서 실행 되는 MABS v3로 WS 2016 NTFS 중복 제거 된 볼륨을 보호 하는 경우 복구에 영향을 줄 수 있습니다. 이후 버전의 MABS에 포함 될 수 있는 중복 제거 된 되지 않는 방식으로 복구를 수행 하기 위한 수정이 있습니다. MABS v3 UR1에서이 수정이 필요한 경우 MABS 지원 담당자에 게 문의 하세요.<br><br> Windows Server 2016에서 MABS v3을 사용 하 여 WS 2019 NTFS 중복 제거 된 볼륨을 보호 하는 경우 백업 및 복원은 중복 제거 된 되지 않습니다. 즉, 백업에서 원본 NTFS 중복 제거 된 볼륨 보다 MABS 서버에서 더 많은 공간을 사용 합니다.   <br><br>   시스템 상태 및 운영 체제 미 설치 (워크 로드가 Azure 가상 머신으로 실행 되는 경우 지원 되지 않음) |
| Servers (64 비트)          | Windows Server 2008 R2 SP1, Windows Server 2008 SP2 ( [Windows Management Framework](https://www.microsoft.com/download/details.aspx?id=54616)를 설치 해야 하는 경우) | 물리적 서버  <br><br>    Hyper-V 가상 머신  <br><br>      VMware 가상 컴퓨터  <br><br>   Azure Stack | V3 UR1                            | 볼륨, 공유, 폴더, 파일, 시스템 상태/완전 복구        |
| SQL Server                | SQL Server 2019, 2017, 2016, [지원 되는 sps](https://support.microsoft.com/lifecycle/search?alpha=SQL%20Server%202016), 2014 및 지원 되는 [sps](https://support.microsoft.com/lifecycle/search?alpha=SQL%20Server%202014) | 물리적 서버  <br><br>     Hyper-V 가상 머신   <br><br>     VMware 가상 컴퓨터  <br><br>   Azure 가상 머신(작업이 Azure 가상 머신으로 실행 중일 때)  <br><br>     Azure Stack | V3 UR1                            | 모든 배포 시나리오: 데이터베이스       <br><br>  MABS v3 UR1는 ReFS 볼륨을 통해 SQL 데이터베이스의 백업을 지원 합니다.     <br><br>     MABS는 Windows Server 2012 Scale-Out 파일 서버 (SOFS)에서 호스트 되는 SQL Server 데이터베이스를 지원 하지 않습니다. <br><br>   MABS는 장애 조치 (failover) 클러스터의 역할 이름이 SQL의 명명 된 AG와 다른 DAG (SQL server Distributed Availability Group) 또는 AG (가용성 그룹)를 보호할 수 없습니다.       |
| Exchange                   | Exchange 2019, 2016                                         | 물리적 서버   <br><br>   Hyper-V 가상 머신  <br><br>      VMware 가상 컴퓨터  <br><br>   Azure Stack  <br><br>    Azure 가상 머신(작업이 Azure 가상 머신으로 실행 중일 때) | V3 UR1                            | 보호 (모든 배포 시나리오): 독립 실행형 Exchange server, DAG (데이터베이스 가용성 그룹)의 데이터베이스  <br><br>    복구(모든 배포 시나리오): 사서함, DAG의 사서함 데이터베이스    <br><br>  참조를 통한 Exchange 백업은 MABS v3 UR1에서 지원 됩니다. |
| SharePoint                 | SharePoint 2019, 2016 (최신 Sp 포함)                       | 물리적 서버  <br><br>    Hyper-V 가상 머신 <br><br>    VMware 가상 컴퓨터  <br><br>   Azure 가상 머신(작업이 Azure 가상 머신으로 실행 중일 때)   <br><br>   Azure Stack | V3 UR1                            | 보호 (모든 배포 시나리오): 팜, 프런트 엔드 웹 서버 콘텐츠  <br><br>    복구 (모든 배포 시나리오): 팜, 데이터베이스, 웹 응용 프로그램, 파일 또는 목록 항목, SharePoint 검색, 프런트 엔드 웹 서버  <br><br>    콘텐츠 데이터베이스에 대해 SQL Server 2012 AlwaysOn 기능을 사용 하는 SharePoint 팜을 보호 하는 것은 지원 되지 않습니다. |

## <a name="vm-backup"></a>VM 백업

| **작업**                                                 | **버전**                                             | **Azure Backup Server 설치**                      | **지원 되는 Azure Backup Server** | **보호 및 복구**                                 |
| ------------------------------------------------------------ | ------------------------------------------------------- | ------------------------------------------------------------ | ---------------------------------- | ------------------------------------------------------------ |
| Hyper-v 호스트-MABS 보호 에이전트 (hyper-v 호스트 서버, 클러스터 또는 VM) | Windows Server 2019, 2016, 2012 R2, 2012               | 물리적 서버  <br><br>    Hyper-V 가상 머신 <br><br>    VMware 가상 컴퓨터 | V3 UR1                             | 보호: Hyper-v 컴퓨터, Csv (클러스터 공유 볼륨)  <br><br>    복구: 가상 머신, Windows, 볼륨, 가상 하드 드라이브에 대해서만 사용할 수 있는 파일 및 폴더의 항목 수준 복구 |
| VMware Vm                                                  | VMware server 5.5, 6.0 또는 6.5, 6.7 (사용이 허가 된 버전) | Hyper-v 가상 컴퓨터  <br><br>   VMware 가상 컴퓨터         | V3 UR1                             | 보호: Csv (클러스터 공유 볼륨), NFS 및 SAN 저장소의 VMware Vm   <br><br>     복구: 가상 머신, Windows, 볼륨, 가상 하드 드라이브에 대해서만 사용할 수 있는 파일 및 폴더의 항목 수준 복구 <br><br>    VMware vApps는 지원 되지 않습니다. |

>[!NOTE]
> MABS는 통과 디스크가 있는 가상 컴퓨터 또는 원격 VHD를 사용 하는 가상 컴퓨터의 백업을 지원 하지 않습니다. 이러한 시나리오에서는 MABS를 사용 하 여 게스트 수준 백업을 사용 하 고 가상 컴퓨터에 에이전트를 설치 하 여 데이터를 백업 하는 것이 좋습니다.

## <a name="linux"></a>Linux

| **작업** | **버전**                               | **Azure Backup Server 설치**                      | **지원 되는 Azure Backup Server** | **보호 및 복구**                                 |
| ------------ | ----------------------------------------- | ------------------------------------------------------------ | ---------------------------------- | ------------------------------------------------------------ |
| Linux        | [Hyper-v](back-up-hyper-v-virtual-machines-mabs.md) 또는 [VMware](backup-azure-backup-server-vmware.md) 게스트로 실행 되는 Linux | 실제 서버, 온-프레미스 Hyper-v VM, VMware의 Windows VM | V3 UR1                             | Hyper-v는 Windows Server 2012 R2, Windows Server 2016 또는 Windows Server 2019에서 실행 되어야 합니다. 보호: 전체 가상 머신   <br><br>   복구: 전체 가상 머신   <br><br>    파일에 일관적인 스냅샷만 지원됩니다.    <br><br>   지원 되는 Linux 배포 및 버전의 전체 목록은 [Azure에서 보증 배포의 Linux](../virtual-machines/linux/endorsed-distros.md)문서를 참조 하세요. |

## <a name="azure-expressroute-support"></a>Azure Express 경로 지원

공용 피어 링 (이전 회로에 사용 가능) 및 Microsoft 피어 링을 사용 하 여 Azure Express 경로를 통해 데이터를 백업할 수 있습니다. 개인 피어 링에 대 한 백업은 지원 되지 않습니다.

공용 피어 링 사용: 다음 도메인/주소에 대 한 액세스를 확인 합니다.

* URL
  * `www.msftncsi.com`
  * `*.Microsoft.com`
  * `*.WindowsAzure.com`
  * `*.microsoftonline.com`
  * `*.windows.net`
  * `www.msftconnecttest.com`
* IP 주소
  * 20.190.128.0/18
  * 40.126.0.0/18

Microsoft 피어 링을 사용 하 여 다음 서비스/지역 및 관련 커뮤니티 값을 선택 합니다.

* Azure Active Directory(12076:5060)
* Microsoft Azure 지역 (Recovery Services 자격 증명 모음의 위치에 따라)
* Azure Storage (Recovery Services 자격 증명 모음의 위치에 따라)

자세한 내용은 [express 경로 라우팅 요구 사항](../expressroute/expressroute-routing.md)을 참조 하세요.

>[!NOTE]
>공용 피어 링은 새 회로에서 사용 되지 않습니다.

## <a name="cluster-support"></a>클러스터 지원

Azure Backup Server는 다음과 같은 클러스터 된 애플리케이션에서 데이터를 보호할 수 있습니다.

* 파일 서버

* SQL  Server

* Hyper-v-스케일 아웃 MABS 보호 에이전트를 사용 하 여 Hyper-v 클러스터를 보호 하는 경우 보호 된 Hyper-v 작업에 대 한 보조 보호를 추가할 수 없습니다.

* Exchange Server - Azure Backup Server는 지원되는 Exchange Server 버전의 공유되지 않은 디스크 클러스터(클러스터 연속 복제)를 보호할 수 있으며 로컬 연속 복제를 위해 구성된 Exchange Server도 보호할 수 있습니다.

* SQL Server - Azure Backup Server는 CSV(클러스터 공유 볼륨)에서 호스트되는 SQL Server 데이터베이스를 백업할 수 없습니다.

>[!NOTE]
>MABS는 Csv (클러스터 공유 볼륨)의 Hyper-v 가상 컴퓨터에 대 한 보호만 지원 합니다. CSV에서 호스트되는 다른 작업에 대한 보호는 지원되지 않습니다.

Azure Backup Server는 MABS 서버와 동일한 도메인에 있는 클러스터 작업 및 하위 도메인 이나 트러스트 된 도메인에 있는 클러스터 작업을 보호할 수 있습니다. 트러스트되지 않은 도메인 또는 작업 그룹의 데이터 원본을 보호하려면 단일 서버의 경우 NTLM 또는 인증서 인증을 사용하거나 클러스터의 경우 인증서 인증만 사용합니다.

## <a name="data-protection-issues"></a>데이터 보호 문제

* Hyper-v VSS 기록기는 공유 Vhd로 백업 된 볼륨을 백업할 수 없으므로, MABS는 공유 드라이브 (다른 Vm에 연결 될 수 있음)를 사용 하 여 Vm을 백업할 수 없습니다.

* 공유 폴더를 보호할 때 공유 폴더의 경로에는 볼륨의 논리 경로가 포함됩니다. 공유 폴더를 이동할 경우 보호가 실패합니다. 보호된 공유 폴더를 이동해야 하는 경우 보호 그룹에서 해당 폴더를 제거한 다음, 이동 후 보호에 추가합니다.  또한 파일 시스템 암호화 (EFS)를 사용 하는 볼륨에서 보호 된 데이터 원본의 경로를 변경 하 고 새 파일 경로가 5120 자를 초과 하는 경우 데이터 보호가 실패 합니다.

* 보호를 중지하지 않고 보호된 컴퓨터의 도메인을 변경할 수 없습니다. 또한 보호 된 컴퓨터의 도메인을 변경할 수 없으며 기존 복제본 및 복구 지점은 다시 보호 때 컴퓨터에 연결할 수 없습니다. 보호된 컴퓨터의 도메인을 변경해야 하는 경우 먼저 컴퓨터의 데이터 원본을 보호에서 제거합니다. 그런 다음 새 도메인을 지정한 후 컴퓨터에서 데이터 원본을 보호합니다.

* 보호를 중지하지 않고 보호된 컴퓨터의 도메인을 변경할 수 없습니다. 또한 보호 된 컴퓨터의 이름을 변경할 수 없으며 기존 복제본 및 복구 지점은 다시 보호 때 컴퓨터에 연결할 수 없습니다. 보호된 컴퓨터의 이름을 변경해야 하는 경우 먼저 컴퓨터의 데이터 원본을 보호에서 제거합니다. 그런 다음 새 이름을 지정한 후 컴퓨터에서 데이터 원본을 보호합니다.

* MABS는 보호 에이전트를 설치 하는 동안 보호 된 컴퓨터의 표준 시간대를 자동으로 식별 합니다. 보호를 구성한 후 보호된 컴퓨터를 다른 표준 시간대로 이동하는 경우 제어판에서 컴퓨터 시간을 변경해야 합니다. 그런 다음 MABS 데이터베이스의 표준 시간대를 업데이트 합니다.

* MABS는 MABS 서버와 동일한 도메인 또는 자식 도메인 및 트러스트 된 도메인에서 작업을 보호할 수 있습니다. NTLM 또는 인증서 인증을 사용 하 여 작업 그룹 및 신뢰할 수 없는 도메인에서 다음과 같은 작업을 보호할 수도 있습니다.

  * SQL  Server
  * 파일 서버
  * Hyper-V

  이러한 작업은 단일 서버 또는 클러스터 구성에서 실행할 수 있음 트러스트 된 도메인에 없는 작업을 보호 하려면 지원 되는 항목 및 필요한 인증에 대 한 정확한 내용은 [작업 그룹 및 신뢰할 수 없는 도메인의 컴퓨터 준비](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm) 를 참조 하세요.

## <a name="unsupported-data-types"></a>지원되지 않는 데이터 형식

MABS는 다음 데이터 형식에 대 한 보호를 지원 하지 않습니다.

* 하드 링크

* DFS 링크 및 연결 지점과 같은 재분석 지점

* 탑재 지점 메타데이터 - 보호 그룹에는 탑재 지점이 있는 데이터가 포함될 수 있습니다. 이 경우 DPM은 탑재 지점의 대상인 탑재된 볼륨을 보호하지만 탑재 지점 메타데이터는 보호하지 않습니다. 탑재 지점이 포함된 데이터를 복구하려면 탑재 지점 계층을 수동으로 다시 만들어야 합니다.

* 탑재된 볼륨 내의 탑재된 볼륨에 있는 데이터

* 휴지통

* 페이징 파일

* 시스템 볼륨 정보 폴더. 컴퓨터의 시스템 정보를 보호 하려면 컴퓨터의 시스템 상태를 보호 그룹 구성원으로 선택 해야 합니다.

* NTFS가 아닌 볼륨

* Windows Vista의 하드 링크 또는 바로 가기 링크가 포함된 파일

* UPDs (사용자 프로필 디스크)를 호스트 하는 파일 공유의 데이터

* 다음과 같은 특성 조합이 포함된 파일:

  * 암호화 및 재분석

  * 암호화 및 SIS(단일 인스턴스 스토리지)

  * 암호화 및 대/소문자 구분

  * 암호화 및 스파스

  * 대/소문자 구분 및 SIS

  * 압축 및 SIS

## <a name="next-steps"></a>다음 단계

* [Microsoft Azure Backup Server 또는 System Center DPM을 사용 하 여 백업에 대 한 지원 매트릭스](backup-support-matrix-mabs-dpm.md)
