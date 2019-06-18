---
title: '빠른 시작: Azure Virtual Machines에서 단일 인스턴스 SAP HANA 수동 설치 | Microsoft Docs'
description: Azure Virtual Machines에서 단일 인스턴스 SAP HANA를 수동으로 설치하기 위한 빠른 시작 가이드입니다.
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: c51a2a06-6e97-429b-a346-b433a785c9f0
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/06/2018
ms.author: hermannd
ms.openlocfilehash: 5091932989849943f00cb71f72378dd17af23a4a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60205049"
---
# <a name="quickstart-manual-installation-of-single-instance-sap-hana-on-azure-virtual-machines"></a>빠른 시작: Azure Virtual Machines에서 단일 인스턴스 SAP HANA 수동 설치
## <a name="introduction"></a>소개
이 가이드에서는 SAP NetWeaver 7.5 및 SAP HANA 1.0 SP12를 수동으로 설치 하는 경우 Azure Virtual Machines에는 단일 인스턴스 SAP HANA를 설정 합니다. 이 가이드의 초점은 Azure에서 SAP HANA를 배포 하는 방법에 있습니다. SAP 설명서를 대체 하지 것입니다. 

> [!NOTE]
> 이 가이드에서는 Azure VM에 SAP HANA를 배포하는 방법에 대해 설명합니다. HANA 대량 인스턴스로 SAP HANA를 배포 하는 방법에 대 한 자세한 내용은 [사용할 수 있는 Azure Virtual Machines에서 SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started)합니다.
 
## <a name="prerequisites"></a>필수 조건
이 가이드에서는 이러한 인프라는 서비스 (IaaS) 기본 사항으로 잘 알고 있다고 가정 합니다.
 * 가상 머신 (Vm) 또는 Azure portal 또는 PowerShell을 통해 가상 네트워크를 배포 하는 방법입니다.
 * Azure 플랫폼 간 명령줄 인터페이스 (CLI), 개체 JSON (JavaScript Notation) 템플릿을 사용 하는 옵션을 포함 합니다.

이 가이드에는 잘 알고 있다고 가정 합니다.
* SAP HANA, SAP NetWeaver 및 온-프레미스에 설치하는 방법
* 설치 하 고 SAP HANA 및 SAP 응용 프로그램 인스턴스가 Azure에서 작동 하는 방법입니다.
* 다음 개념 및 절차:
   * Azure Virtual Network 계획 및 Azure Storage 사용을 포함 하는 Azure에서 SAP 배포에 대 한 계획입니다. 참조 [에서 Azure Virtual Machines SAP NetWeaver-계획 및 구현 가이드](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)합니다.
   * Azure에 VM을 배포하는 배포 원칙 및 방법 - [SAP용 Azure Virtual Machines 배포](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide) 참조
   * SAP NetWeaver ABAP SAP 중앙 서비스 (ASCS), SAP 중앙 서비스 (SCS) 및 서버 ERS (Enqueue Replication) Azure에서 고가용성입니다. [Azure VM에서 SAP NetWeaver에 대한 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide) 참조
   * Azure에서 ASCS/SCS 다중 SID 설치 하는 효율성을 개선 하는 방법에 대 한 세부 정보입니다. [SAP NetWeaver 다중 SID 구성 만들기](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-multi-sid) 참조 
   * Azure에서 Linux 기반 VM에 기반한 SAP NetWeaver 실행 원칙 - 참조 [Microsoft Azure SUSE Linux Vm에서 SAP NetWeaver 실행](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/suse-quickstart)합니다. 이 가이드에는 Linux Azure Vm에 대 한 특정 설정을 제공합니다. 또한 제대로 Linux Vm에 Azure 저장소 디스크를 연결 하는 방법에 대 한 정보를 제공 합니다.

프로덕션 시나리오에 사용할 수 있는 Azure VM 유형은 [SAP documentation for IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)(IAAS에 대한 SAP 설명서)에 나와 있습니다. 비프로덕션 시나리오에 대 한 다양 한 네이티브 Azure VM 유형 사용할 수 있는 경우
VM 구성 및 작업에 대 한 자세한 내용은 참조 하세요. [SAP HANA 인프라 구성 및 Azure에 대 한 작업](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)합니다.
SAP HANA 고가용성에 대 한 참조 [SAP HANA 고가용성에 대 한 Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)합니다.

SAP HANA 인스턴스 또는 S/4HANA 또는 BW/4HANA 시스템을 신속 하 게 배포 하려는 경우 사용을 고려 [SAP Cloud Appliance Library](https://cal.sap.com)합니다. 예를 들어 Azure에서 SAP Cloud Appliance Library를 통해 S/4HANA 시스템을 배포 하는 방법에 대 한 설명서를 찾을 수 있습니다 [이 가이드](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h)합니다. Azure 구독 및 SAP 클라우드 어플라이언스 라이브러리를 사용 하 여 등록할 수 있는 사용자를 SAP 사용자 하기만 하면 됩니다.

## <a name="additional-resources"></a>추가 리소스
### <a name="sap-hana-backup"></a>SAP HANA 백업
Azure Vm에서 SAP HANA 데이터베이스를 백업 하는 방법에 대 한 자세한 내용은 다음을 참조 하세요.
* [Azure Virtual Machines의 SAP hana 백업 가이드](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)합니다.
* [파일 수준의 SAP HANA Azure Backup](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)합니다.
* [저장소 스냅숏에 기반한 SAP HANA 백업](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)합니다.

### <a name="sap-cloud-appliance-library"></a>SAP 클라우드 어플라이언스 라이브러리
S/4HANA 또는 BW/4HANA 배포 하려면 SAP 클라우드 어플라이언스 라이브러리를 사용 하는 방법에 대 한 자세한 내용은 [배포 SAP S/4HANA 또는 Microsoft Azure에서 BW/4HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h)합니다.

### <a name="sap-hana-supported-operating-systems"></a>SAP HANA 지원 운영 체제
SAP HANA 지원 운영 체제에 대 한 내용은 참조 [SAP Note 2235581-SAP HANA: 지원 되는 운영 체제](https://launchpad.support.sap.com/#/notes/2235581/E)합니다. Azure VM은 이러한 운영 체제의 하위 집합만 지원합니다. Azure에서 SAP HANA를 배포하는 데 지원되는 운영 체제는 다음과 같습니다. 

* SUSE Linux Enterprise Server 12.x
* Red Hat Enterprise Linux 7.2

SAP HANA 및 다른 Linux 운영 체제에 대한 SAP 추가 설명서는 다음을 참조하세요.

* [SAP Note 171356: SAP Software on Linux: 일반 정보](https://launchpad.support.sap.com/#/notes/1984787)합니다.
* [SAP Note 1944799: SLES 운영 체제 설치를 위한 SAP HANA 지침](https://go.sap.com/documents/2016/05/e8705aae-717c-0010-82c7-eda71af511fa.html)합니다.
* [SAP Note 2205917: SAP HANA DB 권장 되는 SAP 응용 프로그램에 대 한 SLES 12에 대 한 OS 설정을](https://launchpad.support.sap.com/#/notes/2205917/E)합니다.
* [SAP Note 1391070: Linux UUID 솔루션](https://launchpad.support.sap.com/#/notes/1391070)합니다.
* [SAP Note 2009879: Red Hat Enterprise Linux (RHEL) 운영 체제에 대 한 SAP HANA 지침](https://launchpad.support.sap.com/#/notes/2009879)합니다.
* [SAP Note 2292690: SAP HANA DB: RHEL 7을 위한 권장 OS 설정](https://launchpad.support.sap.com/#/notes/2292690/E)합니다.

### <a name="sap-monitoring-in-azure"></a>Azure에서 SAP 모니터링
Azure에서 SAP 모니터링 정보

* [SAP Note 2191498](https://launchpad.support.sap.com/#/notes/2191498/E) SAP 향상 된 Azure에서 Linux Vm을 사용 하 여 모니터링에 대해 설명 합니다. 
* [SAP Note 1102124](https://launchpad.support.sap.com/#/notes/1102124/E) Linux의 SAPOSCOL에 대 한 정보에 설명 합니다. 
* [SAP Note 2178632](https://launchpad.support.sap.com/#/notes/2178632/E) Microsoft Azure에서 SAP 용 주요 모니터링 메트릭에 대해 설명 합니다.

### <a name="azure-vm-types"></a>Azure VM 유형
Azure VM 유형 및 SAP HANA를 사용 하는 SAP 지원 워크 로드 시나리오에 설명 되어 있습니다 [SAP 인증 IaaS 플랫폼](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)합니다. 

SAP NetWeaver 또는 S/4HANA 응용 프로그램 계층에 대해 SAP에서 인증 된 azure VM 형식에 설명 된 [SAP Note 1928533: Azure의 SAP 응용 프로그램: 지원 되는 제품 및 Azure VM 유형](https://launchpad.support.sap.com/#/notes/1928533/E)합니다.

> [!NOTE]
> SAP-Linux-Azure 통합은 Azure Resource Manager에서만 지원하며, 클래식 배포 모델에서는 지원하지 않습니다. 

## <a name="manual-installation-of-sap-hana"></a>SAP HANA 수동 설치

> [!IMPORTANT]
> 선택한 OS SAP 사용할 특정 VM 유형에 따라 SAP HANA에 대 한 인증 인지 확인 합니다. SAP HANA의 목록 해당 VM 유형 조회할 수에 대 한 VM 유형 및 OS 릴리스 certified [SAP HANA 인증 IaaS 플랫폼](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)합니다. 특정 VM 유형에 대 한 SAP HANA 지원 운영 체제 버전의 전체 목록을 가져오려면 나열 된 VM 유형에 대 한 세부 정보 클릭 해야 합니다. 이 문서의 예제에서는 M 시리즈 Vm에서 SAP HANA에 대 한 SAP에서 지원 되지 않는 SUSE Linux Enterprise Server (SLES) OS 릴리스를 사용 했습니다.
>

이 가이드에서는 다음 두 가지 방법으로 Azure VM에 SAP HANA를 수동으로 설치하는 방법에 대해 설명합니다.

* "데이터베이스 인스턴스 설치" 단계에서 분산형된 NetWeaver 설치의 일부로 SAP SWPM Software Provisioning Manager ()를 사용 합니다.
* SAP HANA 데이터베이스 수명 주기 관리자 (HDBLCM) 도구를 사용 하 고 NetWeaver를 설치 합니다.

또한 하나의 단일 VM에 ASCS 인스턴스, SAP HANA, SAP 응용 프로그램 서버 등의 모든 구성 요소를 설치 하려면 SWPM을 사용할 수 있습니다. 이 단계는 설명 [SAP HANA 블로그 알림](https://blogs.saphana.com/2013/12/31/announcement-sap-hana-and-sap-netweaver-as-abap-deployed-on-one-server-is-generally-available/)합니다. 이 옵션은이 빠른 시작 가이드에서는 설명 하지 않지만 고려해 야 할 문제는 동일 합니다.

설치를 시작 하기 전에 참조 하는 것이 좋습니다는 "준비 Azure Vm의 SAP HANA 수동 설치를 위한"이이 가이드의 뒷부분에 나오는 섹션입니다. 이렇게 하면 기본 Azure VM 구성만 사용할 때 발생할 수 있는 몇 가지 기본적인 실수를 방지할 수 있습니다.

## <a name="key-steps-for-sap-hana-installation-when-you-use-sap-swpm"></a>SAP SWPM 사용 시 SAP HANA 설치를 위한 주요 단계
이 섹션에서는 SAP SWPM을 사용하여 분산형 SAP NetWeaver 7.5를 설치할 때의 단일 인스턴스 SAP HANA 수동 설치의 주요 단계를 나열합니다. 개별 단계는 이 가이드의 뒷부분에 나오는 스크린샷에서 자세히 설명합니다.

1. 2개 테스트 VM이 포함된 Azure 가상 네트워크를 만듭니다.
2. Azure Resource Manager 모델에 따라 운영 체제를 사용 하 여 두 개의 Azure Vm을 배포 합니다. 이 예제에서는 SAP 응용 프로그램 12 SP1 용 SUSE Linux Enterprise Server 및 SLES를 사용 합니다. 
3. 응용 프로그램 서버 VM에 2 개의 Azure 표준 또는 프리미엄 저장소 디스크를 예를 들어는 75GB 및 500GB 디스크를 연결 합니다.
4. HANA DB 서버 VM에 Premium Storage 디스크를 연결합니다. 자세한 내용은이 가이드의 뒷부분에 나오는 "디스크 설정" 섹션을 참조 하세요.
5. 크기 또는 처리량 요구 사항에 따라 여러 디스크를 연결 합니다. 그런 다음 스트라이프 볼륨을 만듭니다. VM 내부의 OS 수준에서 논리 볼륨 관리 (LVM) 또는 다중 장치 관리 (mdadm) 도구를 사용 합니다.
6. 연결된 디스크 또는 논리 볼륨에 XFS 파일 시스템을 만듭니다.
7. 새 XFS 파일 시스템을 OS 수준에서 탑재합니다. 모든 SAP 소프트웨어에 대해 하나의 파일 시스템을 사용합니다. 다른 소프트웨어(예: /sapmnt 디렉터리 및 백업)에는 다른 파일 시스템을 사용합니다. SAP HANA DB 서버에서 XFS 파일 시스템을 Premium Storage 디스크(예: /hana 및 /usr/sap)에 탑재합니다. 이 프로세스는 루트 파일 시스템 가득 차지 않도록 해야 합니다. 루트 파일 시스템 Linux Azure Vm에서 크지 합니다. 
8. /etc/hosts 파일에 테스트 VM의 로컬 IP 주소를 입력합니다.
9. /etc/fstab 파일에 **nofail** 매개 변수를 입력합니다.
10. 사용할 Linux OS 릴리스에 따라 Linux 커널 매개 변수를 설정 합니다. 자세한 내용은 HANA 및이 가이드의 "커널 매개 변수" 섹션을 설명 하는 SAP Note를 참조 하세요.
11. 교환 공간 추가
12. 선택적으로 테스트 VM에 그래픽 데스크톱 설치, 그렇지 않은 경우 원격 SAPinst 설치 사용
13. SAP Service Marketplace에서 SAP 소프트웨어를 다운로드합니다.
14. 앱 서버 VM에 SAP ASCS 인스턴스 설치
15. NFS를 사용하여 테스트 VM 간에 /sapmnt 디렉터리 공유 - 애플리케이션 서버 VM은 NFS 서버입니다.
16. HANA DB 서버 VM에 SWPM을 사용 하 여 포함 된 데이터베이스 인스턴스를 설치 합니다.
17. 애플리케이션 서버 VM에 PAS(기본 애플리케이션 서버)를 설치합니다.
18. SAP MC(SAP 관리 콘솔)를 시작합니다. 예를 들어 SAP GUI 또는 HANA Studio와 연결합니다.

## <a name="key-steps-for-sap-hana-installation-when-you-use-hdblcm"></a>HDBLCM 사용 시 SAP HANA 설치를 위한 주요 단계
이 섹션에서는 SAP HDBLCM을 사용하여 분산형 SAP NetWeaver 7.5를 설치할 때의 단일 인스턴스 SAP HANA 수동 설치의 주요 단계를 나열합니다. 개별 단계는 이 가이드의 스크린샷에서 자세히 설명합니다.

1. 2개 테스트 VM이 포함된 Azure 가상 네트워크를 만듭니다.
2. Azure Resource Manager 모델에 따라 운영 체제를 사용 하 여 두 개의 Azure Vm을 배포 합니다. 이 예제에서는 SAP 응용 프로그램 12 SP1 용 SLES 및 SLES를 사용 합니다.
3. 앱 서버 VM에 2 개의 Azure 표준 또는 프리미엄 저장소 디스크를 예를 들어는 75GB 및 500GB 디스크를 연결 합니다.
4. HANA DB 서버 VM에 Premium Storage 디스크를 연결합니다. 자세한 내용은이 가이드의 뒷부분에 나오는 "디스크 설정" 섹션을 참조 하세요.
5. 크기 또는 처리량 요구 사항에 따라 여러 디스크를 연결 합니다. VM 내부의 OS 수준에서 논리 볼륨 관리 또는 mdadm 도구를 사용 하 여 스트라이프 볼륨을 만듭니다.
6. 연결된 디스크 또는 논리 볼륨에 XFS 파일 시스템을 만듭니다.
7. 새 XFS 파일 시스템을 OS 수준에서 탑재합니다. 모든 SAP 소프트웨어에 대해 하나의 파일 시스템을 사용합니다. 다른 소프트웨어(예: /sapmnt 디렉터리 및 백업)에는 다른 파일 시스템을 사용합니다. SAP HANA DB 서버에서 XFS 파일 시스템을 Premium Storage 디스크(예: /hana 및 /usr/sap)에 탑재합니다. 이 프로세스는 루트 파일 시스템 가득 차지 않도록 하기 위해 필요 합니다. 루트 파일 시스템 Linux Azure Vm에서 크지 합니다.
8. /etc/hosts 파일에 테스트 VM의 로컬 IP 주소를 입력합니다.
9. /etc/fstab 파일에 **nofail** 매개 변수를 입력합니다.
10. 사용할 Linux OS 릴리스에 따라 커널 매개 변수를 설정 합니다. 자세한 내용은 HANA 및이 가이드의 "커널 매개 변수" 섹션을 설명 하는 SAP Note를 참조 하세요.
11. 교환 공간 추가
12. 선택적으로 테스트 VM에 그래픽 데스크톱 설치, 그렇지 않은 경우 원격 SAPinst 설치 사용
13. SAP Service Marketplace에서 SAP 소프트웨어를 다운로드합니다.
14. HANA DB 서버 VM에 그룹 ID가 1001인 sapsys 그룹을 만듭니다.
15. HANA 데이터베이스 수명 주기 관리자를 사용 하 여 DB 서버 VM에 SAP HANA를 설치 합니다.
16. 앱 서버 VM에 SAP ASCS 인스턴스 설치
17. NFS를 사용하여 테스트 VM 간에 /sapmnt 디렉터리 공유 - 애플리케이션 서버 VM은 NFS 서버입니다.
18. HANA DB 서버 VM에 SWPM을 사용 하 여 HANA를 포함 하는 데이터베이스 인스턴스를 설치 합니다.
19. 응용 프로그램 서버 VM에 기본 응용 프로그램 서버를 설치 합니다.
20. SAP MC를 시작합니다. SAP GUI 또는 HANA Studio를 통해 연결합니다.

## <a name="prepare-azure-vms-for-a-manual-installation-of-sap-hana"></a>SAP HANA의 수동 설치를 위한 Azure VM 준비
이 섹션에서는 다음 항목을 다룹니다.

* OS 업데이트
* 디스크 설정
* 커널 매개 변수
* 파일 시스템
* /etc/hosts 파일
* /etc/fstab 파일

### <a name="os-updates"></a>OS 업데이트
추가 소프트웨어를 설치 하기 전에 Linux OS 업데이트 및 수정에 대 한 확인 합니다. 패치를 설치 하면 지원 센터에 대 한 호출을 방지할 수 있습니다.

사용 하는지 확인 합니다.
* SUSE Linux Enterprise Server for SAP Applications
* Red Hat Enterprise Linux for SAP Applications 또는 Red Hat Enterprise Linux for SAP HANA 

아직 수행 하지 않은 경우 Linux 공급 업체의 Linux 구독으로 OS 배포를 등록 합니다. SUSE에 서비스를 이미 포함 되어 있고 자동으로 등록 된 SAP 응용 프로그램에 대 한 OS 이미지가 있습니다.

다음은 사용 하 여 SUSE Linux에 대 한 사용 가능한 패치를 확인 하는 방법의 예는 **zypper** 명령:

 `sudo zypper list-patches`

문제의 종류에 따라 패치는 범주 및 심각도별로 분류됩니다. 범주에 대 한 자주 사용 되는 값은 
- 보안
- 권장
- 옵션
- 기능
- 문서
- yast

심각도 대해 자주 사용 되는 값은

- 중요
- 중요
- 보통
- 낮음
- Unspecified

**zypper** 명령은 설치된 패키지에 필요한 업데이트만 찾습니다. 예를 들어이 명령을 사용할 수 있습니다.

`sudo zypper patch  --category=security,recommended --severity=critical,important`

실제로 시스템을 업데이트하지 않고 `--dry-run` 매개 변수를 추가하여 해당 업데이트를 테스트할 수 있습니다.


### <a name="disk-setup"></a>디스크 설정
Azure에서 Linux VM의 루트 파일 시스템의 크기는 제한됩니다. 따라서 SAP를 실행 하려면 Azure VM에 추가 디스크 공간에 연결 해야 합니다. SAP 응용 프로그램 서버 Azure Vm에 대 한 Azure 표준 저장소 디스크를 사용 하 여 충분할 수 있습니다. SAP HANA DBMS Azure Vm에 대 한 프로덕션 및 비 프로덕션 구현에 대 한 Azure premium storage 디스크의 사용은 필수.

기반 합니다 [SAP HANA TDI 저장소 요구 사항](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html), 다음 Azure 프리미엄 저장소 구성이 제안 됩니다. 

| VM SKU | RAM |  /hana/data 및 /hana/log <br /> LVM 또는 mdadm으로 스트라이프 | /hana/shared | /root 볼륨 | /usr/sap |
| --- | --- | --- | --- | --- | --- |
| GS5 | 448GB | 2 x P30 | 1 x P20 | 1 x P10 | 1 x P10 | 

제안 된 디스크 구성에서 HANA 데이터 볼륨 및 로그 볼륨은 LVM 또는 mdadm으로 스트라이프된 Azure premium storage 디스크의 동일한 집합에 배치 됩니다. Azure premium storage에서는 중복성을 위해 디스크의 세 개의 이미지가 유지 하므로 RAID 중복성 수준을 정의할 필요가 없습니다. 

되도록 충분 한 저장소를 구성 하는, 참조 [SAP HANA TDI 저장소 요구 사항](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) 하 고 [SAP HANA 서버 설치 및 업데이트 가이드](https://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm)합니다. 에 설명 된 대로 다른 Azure premium storage 디스크의 다른 가상 하드 디스크 (VHD) 처리량 볼륨 수도 [고성능 premium storage 및 관리 디스크 Vm에 대 한](../../windows/disks-types.md)합니다. 

데이터베이스 또는 트랜잭션 로그 백업을 저장 하기 위해 HANA DBMS Vm에 추가 premium storage 디스크를 추가할 수 있습니다.

스트라이핑 구성에 사용 되는 두 가지 주요 도구에 대 한 자세한 내용은 다음을 참조 하세요.

* [Linux에서 소프트웨어 RAID 구성](../../linux/configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)합니다.
* [Azure에서 Linux VM에 LVM을 구성합니다](../../linux/configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

게스트 OS로 Linux를 실행 하는 Azure Vm에 디스크를 연결 하는 방법에 대 한 자세한 내용은 참조 하세요. [Linux VM에 디스크 추가](../../linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)합니다.

Azure premium Ssd를 사용 하 여 디스크 캐싱 모드를 정의할 수 있습니다. /Hana/data 및 /hana/log을 보유 하는 스트라이프 집합, 디스크 캐싱을 사용 하지 않도록 설정 합니다. 다른 볼륨에 대해, 디스크 캐싱 모드를 설정 합니다 **ReadOnly**합니다.

Vm을 만드는 데 사용할 샘플 JSON 템플릿, 참조를 [Azure 빠른 시작 템플릿](https://github.com/Azure/azure-quickstart-templates)합니다.
vm-simple-sles 템플릿은 기본 템플릿입니다. 여기에는 100GB 추가 데이터 디스크가 포함된 저장소 섹션이 있습니다. 이 템플릿을 기반으로 사용 합니다. 특정 구성에 맞게 템플릿을 조정할 수 있습니다.

> [!NOTE]
> 에 설명 된 대로 UUID를 사용 하 여 Azure 저장소 디스크를 연결 하는 것이 반드시 [Microsoft Azure SUSE Linux Vm에서 SAP NetWeaver 실행](suse-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)합니다.

테스트 환경에서 다음 스크린샷에 표시 된 것 처럼 두 개의 Azure 표준 저장소 디스크가 SAP 앱 서버 VM에 연결 됩니다. 하나는 모든 SAP 소프트웨어, NetWeaver 7.5, SAP GUI, SAP HANA 등 설치에 대 한 저장합니다. 두 번째 디스크 추가 요구 사항에 대 한 충분 한 여유 공간이 있는지 확인 합니다. 예를 들어, 백업 및 테스트 데이터 및 /sapmnt 디렉터리, 즉 SAP 프로필에는 동일한 SAP 환경에 속한 모든 Vm 간에 공유 해야 합니다.

![2개 데이터 디스크 및 해당 크기를 보여 주는 SAP HANA 앱 서버 디스크 창](./media/hana-get-started/image003.jpg)


### <a name="kernel-parameters"></a>커널 매개 변수
SAP HANA에는 특정 Linux 커널 설정이 필요합니다. 이러한 커널은 표준 Azure 갤러리 이미지의 일부가 아닌 설정과 수동으로 설정 해야 합니다. SUSE 또는 Red Hat의 사용 여부에 따라 매개 변수가 다를 수 있습니다. 나열 된 SAP Note는 이전에 이러한 매개 변수에 대 한 정보를 제공 합니다. 표시된 스크린샷에서 SUSE Linux 12 SP1이 사용되었습니다. 

SLES for SAP Applications 12 일반 공급 및 SLES for SAP Applications 12 SP1에는 새로운 도구가 **조정 adm**을 대체 하는 **sapconf** 도구입니다. **tuned-adm**에 대한 특별한 SAP HANA 프로필을 사용할 수 있습니다. SAP HANA에 대 한 시스템을 튜닝 하려면 루트 사용자로 다음 프로필을 입력 합니다.

   `tuned-adm profile sap-hana`

**tuned-adm**에 대한 자세한 내용은 [tuned-adm 관련 SUSE 설명서](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip)(영문)를 참조하세요.

다음 스크린샷에서 볼 수 있습니다 어떻게 **조정 adm** 변경 합니다 `transparent_hugepage` 및 `numa_balancing` 필수 SAP HANA 설정에 따라 값:

![tuned-adm 도구는 필수 SAP HANA 설정에 따라 값을 변경합니다.](./media/hana-get-started/image005.jpg)

SAP HANA 커널을 영구적으로 설정하려면 SLES 12에서 **grub2**를 사용합니다. 에 대 한 자세한 내용은 **grub2**를 참조 합니다 [구성 파일 구조](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip) SUSE 설명서의 섹션입니다.

다음 스크린샷에서는 구성 파일에서 커널 설정을 변경한 다음 **grub2-mkconfig**를 사용하여 컴파일한 방식을 보여 줍니다.

![구성 파일에서 변경된 커널 설정 및 grub2-mkconfig를 통한 컴파일](./media/hana-get-started/image006.jpg)

또 다른 옵션은 YaST 및 **부팅 로더** > **커널 매개 변수** 설정을 사용하여 커널 설정을 변경하는 것입니다.

![YaST 부팅 로더의 커널 매개 변수 설정 탭](./media/hana-get-started/image007.jpg)

### <a name="file-systems"></a>파일 시스템
다음 스크린샷에서는 2개 Azure 표준 저장소 디스크에 연결된 SAP 앱 서버 VM에 만들어진 2개 파일 시스템을 보여 줍니다. 두 파일 시스템의 형식은 XFS 및 며 /sapdata 및 /sapsoftware에 탑재 됩니다.

이 방법으로 파일 시스템을 구성 하는 것이 반드시 합니다. 디스크 공간을 구성 하는 방법에 대 한 다른 옵션이 있습니다. 가장 중요한 고려 사항은 루트 파일 시스템의 여유 공간이 부족하지 않도록 하는 것입니다.

![SAP 앱 서버 VM에 만든 2개 파일 시스템](./media/hana-get-started/image008.jpg)

SAP HANA DB VM과 SAPinst SWPM을 사용 하 여 사용 하는 경우 데이터베이스 설치 중에 대 한 하며 **일반적인** 설치 옵션을 모든 항목이 /hana 및 /usr/sap 아래 설치 합니다. SAP HANA 로그 백업의 기본 위치는 /usr/sap 아래입니다. 마찬가지로 루트 파일 시스템 저장소 공간이 부족 하지 않도록 하려면 반드시 합니다. SWPM을 사용 하 여 SAP HANA를 설치 하기 전에 /hana 및 /usr/sap 아래에 충분 한 여유 공간은 있는지 확인 합니다.

SAP HANA의 표준 파일 시스템 레이아웃에 대 한 참조를 [SAP HANA 서버 설치 및 업데이트 가이드](https://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm)합니다.

![SAP 앱 서버 VM에 만든 추가 파일 시스템](./media/hana-get-started/image009.jpg)

SAP 응용 프로그램 12 Azure 갤러리 이미지에 대 한 표준 SLES/SLES에서 SAP NetWeaver를 설치할 때 다음 스크린샷과에서 같이 스왑 공간이 라는 메시지를 표시 합니다. 이 메시지를 해제하려면 **dd**, **mkswap** 및 **swapon**을 사용하여 스왑 파일을 수동으로 추가하면 됩니다. 에 대해 알아보려면를 검색에서 "수동으로 스왑 파일 추가"를 [YaST partitioner 사용](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip) SUSE 설명서의 섹션입니다.

또 다른 옵션은 Linux VM 에이전트를 사용하여 교환 공간을 구성하는 것입니다. 자세한 내용은 [Azure Linux 에이전트 사용자 가이드](../../extensions/agent-linux.md)를 참조하세요.

![하다 고 알리는 팝업 메시지 교환 공간이 부족](./media/hana-get-started/image010.jpg)


### <a name="the-etchosts-file"></a>/etc/hosts 파일
SAP 설치를 시작하기 전에 먼저 SAP VM의 호스트 이름과 IP 주소를 /etc/hosts 파일에 포함해야 합니다. 하나의 Azure 가상 네트워크 내에 모든 SAP Vm을 배포 합니다. 다음 여기에 표시 된 대로 내부 IP 주소를 사용 하 여:

![/etc/hosts 파일에 나열된 SAP VM의 호스트 이름 및 IP 주소](./media/hana-get-started/image011.jpg)

### <a name="the-etcfstab-file"></a>/etc/fstab 파일

추가 하는 것이 유용 합니다 **nofail** fstab 파일에 대 한 매개 변수입니다. 이 이렇게 하면 디스크에 문제가 있는 경우 VM 부팅 프로세스가 응답 중지 되지 않습니다. 그러나 추가 디스크 공간을 사용할 수 없으며 프로세스가 루트 파일 시스템을 가득 채울 수 있습니다. /hana가 누락되면 SAP HANA가 시작되지 않습니다.

![fstab 파일에 nofail 매개 변수 추가](./media/hana-get-started/image000c.jpg)

## <a name="graphical-gnome-desktop-on-sles-12sles-for-sap-applications-12"></a>SLES 12/SLES for SAP Applications 12의 그래픽 GNOME 데스크톱
이 섹션에서는 다음을 수행하는 방법을 설명합니다.

* 설치 GNOME 데스크톱 및 xrdp SLES 12/SLES for SAP Applications 12입니다.
* SLES 12/SLES for SAP Applications 12 Firefox 사용 하 여 Java 기반 SAP MC를 실행 합니다.

또한이 가이드에 설명 되지 Xterminal 또는 VNC와 같은 대안을 사용할 수 있습니다.

### <a name="install-the-gnome-desktop-and-xrdp-on-sles-12sles-for-sap-applications-12"></a>설치 GNOME 데스크톱 및 xrdp SLES 12/SLES for SAP Applications 12
Windows 백그라운드에 있으면 Firefox, SAPinst, SAP GUI, SAP MC 또는 HANA Studio를 실행 하려면 SAP Linux Vm 내에서 직접 그래픽 데스크톱을 쉽게 사용할 수 있습니다. 그런 다음 Windows 컴퓨터에서 원격 데스크톱 프로토콜 (RDP)를 통해 VM에 연결할 수 있습니다. 프로덕션 및 비프로덕션 Linux 기반 시스템에 Gui를 추가 하는 방법에 대 한 회사 정책에 따라 서버에 GNOME을 설치 하는 것이 좋습니다. 에 Azure SLES 12/SLES for SAP Applications 12 VM GNOME 데스크톱을 설치 하려면 다음이 단계를 따릅니다.

1. PuTTY 창에서 예를 들어, 다음 명령을 입력 하 여 GNOME 데스크톱을 설치 합니다.

   `zypper in -t pattern gnome-basic`

2. xrdp를 설치하여 RDP를 통해 VM에 연결할 수 있도록 합니다.

   `zypper in xrdp`

3. /etc/sysconfig/windowmanager를 편집하고 기본 창 관리자를 GNOME으로 설정합니다.

   `DEFAULT_WM="gnome"`

4. **chkconfig**를 실행하여 다시 부팅한 후 xrdp가 자동으로 시작되도록 합니다.

   `chkconfig -level 3 xrdp on`

5. RDP 연결에 문제가 있으면 다시 시작, 예를 들어, PuTTY 창에서 하려고 합니다.

   `/etc/xrdp/xrdp.sh restart`

6. 이전 단계에서 언급한 xrdp 다시 시작이 작동하지 않으면 .pid 파일을 확인합니다.

   `check /var/run` 

   `xrdp.pid`를 찾아보세요. 찾은 다음 제거하고 다시 시작합니다.

### <a name="start-sap-mc"></a>SAP MC를 시작 합니다.
GNOME 데스크톱을 설치한 후에 Firefox에서 그래픽 Java 기반 SAP MC를 시작 합니다. 에 Azure SLES 12/SLES for SAP Applications 12 VM 실행 될 경우 오류를 표시할 수 있습니다. 누락 된 Java 브라우저 플러그 인으로 인해 오류가 발생합니다.

SAP MC를 시작할 URL은 `<server>:5<instance_number>13`입니다.

자세한 내용은 [웹 기반 SAP 관리 콘솔을 시작할](https://help.sap.com/saphelp_nwce10/helpdata/en/48/6b7c6178dc4f93e10000000a42189d/frameset.htm)합니다.

다음 스크린샷에서 Java 브라우저 플러그 인이 없는 경우 표시 하는 오류 메시지를 보여 줍니다.

![누락된 Java 브라우저 플러그 인을 나타내는 오류 메시지](./media/hana-get-started/image013.jpg)

이 문제를 해결하는 한 가지 방법은 다음 스크린샷과 같이 YaST를 사용하여 누락된 플러그 인을 설치하는 것입니다.

![YaST를 사용하여 누락된 플러그 인 설치](./media/hana-get-started/image014.jpg)

SAP 관리 콘솔 URL을 다시 입력 하는 경우 플러그 인을 활성화 하도록 요청 합니다.

![플러그 인 활성화를 요청하는 대화 상자](./media/hana-get-started/image015.jpg)

누락된 javafx.properties 파일에 대한 오류 메시지가 표시될 수도 있습니다. SAP GUI 7.4 용 Oracle Java 1.8의 요구 사항 관련이 있습니다. 자세한 내용은 [SAP 참고 사항 2059429](https://launchpad.support.sap.com/#/notes/2059424)합니다.
IBM Java 버전 및 SLES/SLES for SAP Applications 12 제공는 openjdk 패키지에는 필요한 javafx.properties 파일에 포함 되지 않습니다. 따라서 Oracle에서 Java SE 8를 다운로드하고 설치하여 이 문제를 해결합니다.

openSUSE에서 openjdk와 비슷한 문제에 대한 내용은 [SAPGui 7.4 Java for openSUSE 42.1 Leap](https://scn.sap.com/thread/3908306)(영문) 토론 스레드를 참조하세요.

## <a name="manual-installation-of-sap-hana-swpm"></a>SAP HANA 수동 설치: SWPM
일련의이 섹션의 스크린샷은 SWPM SAPinst를 사용 하 여 사용 하는 경우 SAP NetWeaver 7.5 및 SAP HANA SP12를 설치 하는 방법에 대 한 주요 단계를 보여 줍니다. NetWeaver 7.5 설치의 일부로 SWPM를 설치할 수도 HANA 데이터베이스를 단일 인스턴스로.

샘플 테스트 환경에서 응용 프로그램 프로그래밍 ABAP (Advanced Business) 앱 서버만을 설치 했습니다. 다음 스크린샷에 표시 된 것과 같이 사용 합니다 **분산 시스템** 하나의 Azure VM에 ASCS 및 기본 응용 프로그램 서버 인스턴스를 설치 하는 옵션입니다. 다른 Azure VM에 데이터베이스 시스템으로 SAP HANA를 사용 했습니다.

![[분산 시스템] 옵션으로 설치된 ASCS 및 기본 애플리케이션 서버 인스턴스](./media/hana-get-started/image012.jpg)

앱 서버 VM에 ASCS 인스턴스를 설치한 후 SAP 관리 콘솔에서 녹색 아이콘으로 식별 됩니다. SAP HANA DB 서버 VM과 SAP 프로필 디렉터리를 포함 하는 /sapmnt 디렉터리 공유 되어야 합니다. DB 설치 단계에서 이 정보에 액세스해야 합니다. 액세스를 제공하는 가장 좋은 방법은 YaST를 사용하여 구성할 수 있는 NFS를 사용하는 것입니다.

![앱 서버 녹색 아이콘을 사용 하 여 VM에 설치 된 ASCS 인스턴스를 보여 주는 SAP 관리 콘솔](./media/hana-get-started/image016.jpg)

앱 서버 VM에서 /sapmnt 디렉터리는 NFS를 통해 사용 하 여 공유 합니다 **rw** 하 고 **no_root_squash** 옵션입니다. 기본값은 **ro** 및 **root_squash**이며, 데이터베이스 인스턴스를 설치할 때 문제가 발생할 수 있습니다.

!["rw" 및 "no_root_squash" 옵션을 사용하여 NFS를 통해 /sapmnt 디렉터리 공유](./media/hana-get-started/image017b.jpg)

앱 서버 VM의에서 /sapmnt 공유를 사용 하 여 SAP HANA DB 서버 VM에 구성 해야 다음 스크린샷에서 볼 수 있듯이 **NFS 클라이언트** 및 YaST:

![NFS 클라이언트를 사용 하 여 구성 된 /sapmnt 공유](./media/hana-get-started/image018b.jpg)

분산된 NetWeaver 7.5 설치의 경우 즉, 수행 하는 **데이터베이스 인스턴스에**, SAP HANA DB 서버 VM에 로그인 하 고 SWPM을 시작 합니다.

![SAP HANA DB 서버 VM에 로그인하고 SWPM을 시작하여 데이터베이스 인스턴스 설치](./media/hana-get-started/image019.jpg)

선택한 후 **일반적인** DB SID, 호스트 이름, 인스턴스 번호 및 DB 시스템 관리자 암호를 설치 및 설치 미디어의 경로를 입력 합니다.

![SAP HANA 데이터베이스 시스템 관리자 로그인 페이지](./media/hana-get-started/image035b.jpg)

DBACOCKPIT 스키마의 암호를 입력합니다.

![DBACOCKPIT 스키마의 암호 입력 상자](./media/hana-get-started/image036b.jpg)

SAPABAP1 스키마 암호에 대한 질문을 입력합니다.

![SAPABAP1 스키마 암호에 대한 질문 입력](./media/hana-get-started/image037b.jpg)

각 작업이 완료되면 DB 설치 프로세스의 각 단계 옆에 녹색 확인 표시가 나타납니다. 메시지 "데이터베이스... 인스턴스의 실행이 완료되었습니다."가 표시됩니다.

![확인 메시지가 표시된 작업 완료 창](./media/hana-get-started/image023.jpg)

설치 후 SAP 관리 콘솔에 녹색 아이콘을 사용 하 여 DB 인스턴스를 보여 줍니다. Hdbindexserver hdbcompileserver 등의 SAP HANA 프로세스의 전체 목록을 표시 합니다.

![SAP HANA 프로세스 목록을 보여 주는 SAP 관리 콘솔 창](./media/hana-get-started/image024.jpg)

다음 스크린샷에서는 HANA 설치 중에 SWPM에서 만든 /hana/shared 디렉터리 아래의 파일 구조 일부를 보여 줍니다. 다른 경로 지정할 수 있는 옵션이 없으므로 SWPM을 사용 하 여 SAP HANA를 설치 하기 전에 /hana 디렉터리 아래에 추가 디스크 공간을 탑재 하려면 반드시 합니다. 이 단계는 루트 파일 시스템의 공간이 부족 방지합니다.

![HANA 설치 중에 만든 /hana/shared 디렉터리 파일 구조](./media/hana-get-started/image025.jpg)

다음 스크린샷에서는 /usr/sap 디렉터리의 파일 구조를 보여 줍니다.

![/usr/sap 디렉터리 파일 구조](./media/hana-get-started/image026.jpg)

분산형 ABAP 설치의 마지막 단계는 기본 애플리케이션 서버 인스턴스를 설치하는 것입니다.

![마지막 단계로 기본 애플리케이션 서버 인스턴스를 보여 주는 ABAP 설치](./media/hana-get-started/image027b.jpg)

기본 응용 프로그램 서버 인스턴스 및 SAP GUI를 설치한 후 사용 합니다 **DBA Cockpit** 트랜잭션 SAP HANA 설치가 올바르게 완료 되었는지 확인 하려면:

![성공적인 설치를 확인하는 DBA Cockpit 창](./media/hana-get-started/image028b.jpg)

마지막 단계로 SAP 앱 서버 VM에서에서 HANA Studio 설치를 첫 번째는 것이 좋습니다. 그런 다음 DB 서버 VM에서 실행 되는 SAP HANA 인스턴스에 연결 합니다.

![SAP 앱 서버 VM에 SAP HANA Studio 설치](./media/hana-get-started/image038b.jpg)

## <a name="manual-installation-of-sap-hana-hdblcm"></a>SAP HANA 수동 설치: HDBLCM
SWPM을 사용하여 분산 설치의 일부로 SAP HANA를 설치하는 것 외에도 먼저 HDBLCM을 사용하여 독립 실행형 HANA를 설치할 수 있습니다. 예를 들어 SAP NetWeaver 7.5를 설치할 수 있습니다. 이 섹션의 스크린샷에서는 이 프로세스가 작동하는 방식을 보여 줍니다.

HANA HDBLCM 도구에 대한 자세한 내용은 다음을 참조하세요.

* [작업에 대 한 올바른 SAP HANA HDBLCM 선택](https://help.sap.com/saphelp_hanaplatform/helpdata/en/68/5cff570bb745d48c0ab6d50123ca60/content.htm)합니다.
* [SAP HANA 수명 주기 관리 도구](https://www.tutorialspoint.com/sap_hana_administration/sap_hana_administration_lifecycle_management.htm)합니다.
* [SAP HANA 서버 설치 및 업데이트 가이드](https://help.sap.com/hana/SAP_HANA_Server_Installation_Guide_en.pdf)합니다.

기본 그룹 ID 설정에 대 한 문제가 발생 하지 않도록 하려는 `\<HANA SID\>adm user`에 HDBLCM 도구로 생성 된 합니다. HDBLCM 통해 SAP HANA를 설치 하기 전에 정의 라는 새 그룹 `sapsys` 그룹 ID를 사용 하 여 `1001`:

![1001 그룹 ID를 사용하여 정의한 새로운 "sapsys" 그룹](./media/hana-get-started/image030.jpg)

HDBLCM을 처음 시작 하면 간단한 시작 메뉴 표시 합니다. 선택 항목 1 **새 시스템 설치**:

![HDBLCM 시작 창의 "새 시스템 설치" 옵션](./media/hana-get-started/image031.jpg)

다음 스크린샷에서는 이전에 선택한 주요 옵션을 모두 보여 줍니다.

> [!IMPORTANT]
> 이 샘플에서는 및 /usr/sap /hana/shared 설치 경로 HANA 로그 및 데이터 볼륨에 대 한 명명 된 디렉터리에 루트 파일 시스템의 일부가 아니어야 합니다. 이러한 디렉터리는 VM에 연결 된 Azure 데이터 디스크에 속합니다. 자세한 내용은 "디스크 설정" 섹션을 참조 하세요. 

이 방법은 루트 파일 시스템의 공간이 부족하지 않도록 방지하는 데 유용합니다. HANA 시스템 관리자는 사용자 ID `1005` 의 일부인를 `sapsys` ID를 사용 하 여 그룹 `1001`, 설치 전에 정의한입니다.

![이전에 선택한 주요 SAP HANA 구성 요소 전체 목록](./media/hana-get-started/image032.jpg)

확인 된 `\<HANA SID\>adm user` /etc/ passwd 디렉터리에 대 한 세부 정보입니다. 검색할 `azdadm`다음 스크린샷에 표시 된 것 처럼:

![/etc/passwd 디렉터리에 나열된 HANA \<HANA SID\>adm 사용자 세부 정보](./media/hana-get-started/image033.jpg)

HDBLCM을 사용하여 SAP HANA를 설치하면 다음 스크린샷과 같이 SAP HANA Studio에서 파일 구조를 볼 수 있습니다. 모든 SAP NetWeaver 테이블을 포함하는 SAPABAP1 스키마는 아직 사용할 수 없습니다.

![SAP HANA Studio의 SAP HANA 파일 구조](./media/hana-get-started/image034.jpg)

SAP HANA를 설치한 후에는 이를 기반으로 하여 SAP NetWeaver를 설치할 수 있습니다. 다음 스크린샷에 표시 된 것과 같이 설치 SWPM을 사용 하 여 분산된 설치로 수행 되었습니다. 이 프로세스는 이전 섹션에서 설명 되어 있습니다. SWPM을 사용 하 여 데이터베이스 인스턴스를 설치할 때 HDBLCM을 사용 하 여 동일한 데이터를 입력 합니다. 예를 들어 호스트 이름, HANA SID 및 인스턴스 번호를 입력합니다. 그러면 SWPM에서 기존 HANA 설치를 사용하고 더 많은 스키마를 추가합니다.

![SWPM을 사용하여 수행한 분산 설치](./media/hana-get-started/image035b.jpg)

다음 스크린샷에서는 DBACOCKPIT 스키마에 대한 데이터를 입력하는 SWPM 설치 단계를 보여 줍니다.

![DBACOCKPIT 스키마 데이터를 입력하는 SWPM 설치 단계](./media/hana-get-started/image036b.jpg)

SAPABAP1 스키마에 대한 데이터를 입력합니다.

![SAPABAP1 스키마에 대한 데이터 입력](./media/hana-get-started/image037b.jpg)

SWPM 데이터베이스 인스턴스 설치가 완료 되 면 SAP HANA Studio에서 SAPABAP1 스키마를 볼 수 있습니다.

![SAP HANA Studio의 SAPABAP1 스키마](./media/hana-get-started/image038b.jpg)

마지막으로 SAP 앱 서버 및 SAP GUI 설치가 완료 되 면 HANA DB 인스턴스를 사용 하 여 확인 합니다 **DBA Cockpit** 트랜잭션:

![DBA Cockpit 트랜잭션으로 확인된 HANA DB 인스턴스](./media/hana-get-started/image039b.jpg)


## <a name="sap-software-downloads"></a>SAP 소프트웨어 다운로드
다음 스크린샷과 같이 SAP Service Marketplace에서 소프트웨어를 다운로드할 수 있습니다.

Linux/HANA용 NetWeaver 7.5 다운로드:

 ![NetWeaver 7.5를 다운로드 하기 위한 SAP 서비스 설치 및 업그레이드 창](./media/hana-get-started/image001.jpg)

HANA SP12 Platform Edition 다운로드:

 ![HANA SP12 Platform Edition을 다운로드 하기 위한 SAP 서비스 설치 및 업그레이드 창](./media/hana-get-started/image002.jpg)

