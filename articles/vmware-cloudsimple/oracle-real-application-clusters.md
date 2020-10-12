---
title: CloudSimple의 Azure VMware 솔루션-Oracle RAC 용 CloudSimple 사설 클라우드 최적화
description: 새 클러스터를 배포 하 고 Oracle RAC (실제 응용 프로그램 클러스터) 설치 및 구성을 위한 VM을 최적화 하는 방법을 설명 합니다.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2cc2f954f4255c00b7c3549ab5d33d71b240fb70
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86507674"
---
# <a name="optimize-your-cloudsimple-private-cloud-for-installing-oracle-rac"></a>Oracle RAC 설치를 위한 CloudSimple 사설 클라우드 최적화

CloudSimple 사설 클라우드 환경에서 Oracle RAC (실제 응용 프로그램 클러스터)를 배포할 수 있습니다. 이 가이드에서는 새 클러스터를 배포 하 고 Oracle RAC 솔루션에 대 한 VM을 최적화 하는 방법을 설명 합니다. 이 항목의 단계를 완료 한 후 Oracle RAC를 설치 하 고 구성할 수 있습니다.

## <a name="storage-policy"></a>저장소 정책

Oracle RAC를 성공적으로 구현 하려면 클러스터에 적절 한 수의 노드가 필요 합니다.  VSAN 저장소 정책에서는 데이터베이스, 로그 및 다시 실행 디스크를 저장 하는 데 사용 되는 데이터 디스크에 FTT (허용할 수 없음)가 적용 됩니다.  오류를 효과적으로 허용 하는 데 필요한 노드 수는 2N + 1입니다. 여기서 N은 FTT의 값입니다.

예: 원하는 FTT가 2 인 경우 클러스터의 총 노드 수는 2 * 2 + 1 = 5 여야 합니다.

## <a name="overview-of-deployment"></a>배포 개요

다음 섹션에서는 Oracle RAC 용 CloudSimple 사설 클라우드 환경을 설정 하는 방법을 설명 합니다.

1. 디스크 구성 모범 사례
2. CloudSimple 사설 클라우드 vSphere 클러스터 배포
3. Oracle RAC에 대 한 네트워킹 설정
4. VSAN 저장소 정책 설정
5. Oracle Vm 만들기 및 공유 VM 디스크 만들기
6. VM-호스트 선호도 규칙 설정

## <a name="best-practices-for-disk-configuration"></a>디스크 구성 모범 사례

Oracle RAC 가상 컴퓨터에는 특정 기능에 사용 되는 여러 디스크가 있습니다.  공유 디스크는 Oracle RAC 클러스터에서 사용 하는 모든 가상 컴퓨터에 탑재 됩니다.  운영 체제 및 소프트웨어 설치 디스크는 개별 가상 컴퓨터에만 탑재 됩니다.  

![Oracle RAC 가상 머신 디스크 개요](media/oracle-vm-disks-overview.png)

다음 예에서는 아래 테이블에 정의 된 디스크를 사용 합니다.

| 디스크                                      | 목적                                       | 공유 디스크 |
|-------------------------------------------|-----------------------------------------------|-------------|
| OS                                        | 운영 체제 디스크                         | 아니요          |
| 그리드에                                      | Oracle 그리드 소프트웨어의 설치 위치     | 아니요          |
| DATABASE                                  | Oracle 데이터베이스 소프트웨어의 설치 위치 | 아니요          |
| ORAHOME                                   | Oracle 데이터베이스 이진 파일의 기본 위치    | 아니요          |
| DATA1, DATA2, DATA3, DATA4                | Oracle 데이터베이스 파일이 저장 되는 디스크   | 예         |
| REDO1, REDO2, REDO3, REDO4, REDO5, REDO6  | 로그 디스크 다시 실행                                | 예         |
| OCR1, OCR2, OCR3, OCR4, OCR5              | 투표 디스크                                  | 예         |
| FRA1, FRA2                                | 빠른 복구 영역 디스크                      | 예         |

![Oracle 가상 머신 디스크 구성](media/oracle-vmdk.png)

### <a name="virtual-machine-configuration"></a>가상 머신 구성

* 각 가상 머신은 4 개의 SCSI 컨트롤러를 사용 하 여 구성 됩니다.
* SCSI 컨트롤러 유형이 VMware paravirtual로 설정 되어 있습니다.
* 여러 가상 디스크 (. .vmdk)가 생성 됩니다.
* 디스크가 서로 다른 SCSI 컨트롤러에 탑재 되어 있습니다.
* 공유 클러스터 디스크에 대해 다중 기록기 공유 유형이 설정 됩니다.
* vSAN 저장소 정책은 디스크의 고가용성을 보장 하기 위해 정의 됩니다.

### <a name="operating-system-and-software-disk-configuration"></a>운영 체제 및 소프트웨어 디스크 구성

각 Oracle 가상 머신은 호스트 운영 체제, 교환, 소프트웨어 설치 및 기타 OS 기능을 위해 여러 디스크로 구성 됩니다.  이러한 디스크는 가상 컴퓨터 간에 공유 되지 않습니다.  

* 각 가상 컴퓨터에 대해 3 개의 디스크가 가상 디스크로 구성 되 고 Oracle RAC 가상 컴퓨터에 탑재 됩니다.
    * OS 디스크
    * Oracle 그리드를 저장 하기 위한 디스크 파일 설치
    * Oracle 데이터베이스 설치 파일을 저장 하기 위한 디스크
* 디스크는 **씬 프로 비전**된 것으로 구성할 수 있습니다.
* 각 디스크는 첫 번째 SCSI 컨트롤러 (SCSI0)에 탑재 됩니다.  
* 공유는 **공유 안 함**으로 설정 됩니다.
* 중복성은 vSAN 정책을 사용 하 여 저장소에 정의 됩니다.  

![Oracle RAC 데이터 디스크 그룹 구성](media/oracle-vm-os-disks.png)

### <a name="data-disk-configuration"></a>데이터 디스크 구성

데이터 디스크는 주로 데이터베이스 파일을 저장 하는 데 사용 됩니다.  

* 4 개의 디스크가 가상 디스크로 구성 되 고 모든 Oracle RAC 가상 머신에서 탑재 됩니다.
* 각 디스크는 다른 SCSI 컨트롤러에 탑재 됩니다.
* 각 가상 디스크는 **두꺼운 프로 비전 즉시 0**으로 구성 됩니다.  
* 공유는 **다중 기록기**로 설정 됩니다.  
* 디스크는 ASM (자동 저장소 관리) 디스크 그룹으로 구성 해야 합니다.  
* 중복성은 vSAN 정책을 사용 하 여 저장소에 정의 됩니다.  
* ASM 중복은 **외부** 중복성으로 설정 됩니다.

![Oracle RAC 데이터 디스크 그룹 구성](media/oracle-vm-data-disks.png)

### <a name="redo-log-disk-configuration"></a>로그 디스크 구성 다시 실행

다시 실행 로그 파일은 데이터베이스의 변경 내용에 대 한 복사본을 저장 하는 데 사용 됩니다.  로그 파일은 오류 발생 후에 데이터를 복구 해야 할 때 사용 됩니다.

* 다시 실행 로그 디스크는 여러 디스크 그룹으로 구성 해야 합니다.  
* 6 개의 디스크가 모든 Oracle RAC 가상 컴퓨터에 생성 되 고 탑재 됩니다.
* 디스크가 서로 다른 SCSI 컨트롤러에 탑재 되어 있습니다.
* 각 가상 디스크는 **두꺼운 프로 비전 즉시 0**으로 구성 됩니다.
* 공유는 **다중 기록기**로 설정 됩니다.  
* 디스크는 두 개의 ASM 디스크 그룹으로 구성 되어야 합니다.
* 각 ASM 디스크 그룹에는 서로 다른 SCSI 컨트롤러에 있는 세 개의 디스크가 포함 되어 있습니다.  
* ASM 중복성은 **일반** 중복성으로 설정 됩니다.
* 두 ASM Redo log 그룹 모두에서 5 개의 다시 실행 로그 파일이 생성 됩니다.

```
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
```

![Oracle RAC 다시 실행 로그 디스크 그룹 구성](media/oracle-vm-redo-log-disks.png)

### <a name="oracle-voting-disk-configuration"></a>Oracle 투표 디스크 구성

투표 디스크는 분할 된 두뇌 상황을 방지 하기 위해 추가 통신 채널로 쿼럼 디스크 기능을 제공 합니다.

* 모든 Oracle RAC 가상 머신에서 5 개의 디스크가 생성 되 고 탑재 됩니다.
* 디스크가 하나의 SCSI 컨트롤러에 탑재 되어 있습니다.
* 각 가상 디스크는 **두꺼운 프로 비전 즉시 0**으로 구성 됩니다.
* 공유는 **다중 기록기**로 설정 됩니다.  
* 디스크는 ASM 디스크 그룹으로 구성 해야 합니다.  
* ASM 중복성이 **높은** 중복성으로 설정 되어 있습니다.

![Oracle RAC 투표 디스크 그룹 구성](media/oracle-vm-voting-disks.png)

### <a name="oracle-fast-recovery-area-disk-configuration-optional"></a>Oracle 빠른 복구 영역 디스크 구성 (선택 사항)

FRA (빠른 복구 영역)는 Oracle ASM 디스크 그룹에 의해 관리 되는 파일 시스템입니다.  FRA은 백업 및 복구 파일을 위한 공유 저장소 위치를 제공 합니다. Oracle은 빠른 복구 영역에 보관 된 로그 및 플래시 백 로그를 만듭니다. RMAN (Oracle Recovery Manager)은 필요에 따라 빠른 복구 영역에 백업 세트 및 이미지 복사본을 저장 하 고 미디어 복구 중에 파일을 복원할 때이를 사용 합니다.

* 모든 Oracle RAC 가상 컴퓨터에 두 개의 디스크가 만들어지고 탑재 됩니다.
* 디스크가 다른 SCSI 컨트롤러에 탑재 되어 있습니다.
* 각 가상 디스크는 **두꺼운 프로 비전 즉시 0**으로 구성 됩니다.
* 공유는 **다중 기록기**로 설정 됩니다.  
* 디스크는 ASM 디스크 그룹으로 구성 해야 합니다.  
* ASM 중복은 **외부** 중복성으로 설정 됩니다.

![Oracle RAC 투표 디스크 그룹 구성](media/oracle-vm-fra-disks.png)

## <a name="deploy-cloudsimple-private-cloud-vsphere-cluster"></a>CloudSimple 사설 클라우드 vSphere 클러스터 배포

사설 클라우드에 vSphere 클러스터를 배포 하려면 다음 프로세스를 수행 합니다.

1. CloudSimple 포털에서 [사설 클라우드를 만듭니다](create-private-cloud.md). CloudSimple은 새로 만든 사설 클라우드에서 ' cloudowner ' 라는 기본 vCenter 사용자를 만듭니다. 기본 사설 클라우드 사용자 및 사용 권한 모델에 대 한 자세한 내용은 [사설 클라우드 권한 모델 알아보기](learn-private-cloud-permissions.md)를 참조 하세요.  이 단계에서는 사설 클라우드에 대 한 기본 관리 클러스터를 만듭니다.

2. CloudSimple 포털에서 새 클러스터를 사용 하 여 [사설 클라우드를 확장](expand-private-cloud.md) 합니다.  이 클러스터는 Oracle RAC를 배포 하는 데 사용 됩니다.  원하는 내결함성 (3 개 노드 이상)에 따라 노드 수를 선택 합니다.

## <a name="set-up-networking-for-oracle-rac"></a>Oracle RAC에 대 한 네트워킹 설정

1. 사설 클라우드에서 Oracle 공용 네트워크용으로 하나, Oracle 개인 네트워크에 대해 하나, 적절 한 서브넷 CIDRs를 할당 하는 [두 개의 vlan을 만듭니다](create-vlan-subnet.md).
2. Vlan을 만든 후 [사설 클라우드 vCenter에서 분산 포트 그룹](create-vlan-subnet.md#use-vlan-information-to-set-up-a-distributed-port-group-in-vsphere)을 만듭니다.
3. Oracle 환경을 위한 관리 클러스터에서 [DHCP 및 DNS 서버 가상 머신을](dns-dhcp-setup.md) 설정 합니다.
4. 사설 클라우드에 설치 된 [dns 서버에서 dns 전달을 구성](on-premises-dns-setup.md#create-a-conditional-forwarder) 합니다.

## <a name="set-up-vsan-storage-policies"></a>VSAN 저장소 정책 설정

vSAN 정책은 VM 디스크에 저장 된 데이터에 대해 허용 되는 오류 및 디스크 스트라이프를 정의 합니다.  VM을 만드는 동안 생성 된 저장소 정책을 VM 디스크에 적용 해야 합니다.

1. 사설 클라우드의 [vSphere 클라이언트에 로그인](./vcenter-access.md) 합니다.
2. 상단 메뉴에서 **정책 및 프로필**을 선택 합니다.
3. 왼쪽 메뉴에서 **Vm 저장소 정책** 을 선택 하 고 **Vm 저장소 정책 만들기**를 선택 합니다.
4. 정책에 대 한 의미 있는 이름을 입력 하 고 **다음**을 클릭 합니다.
5. **정책 구조** 섹션에서 **vsan storage에 대 한 규칙 사용** 을 선택 하 고 **다음**을 클릭 합니다.
6. **Vsan**  >  **가용성** 섹션에서 사이트 재해 허용 범위에 대해 **없음** 을 선택 합니다. 허용 되는 오류에 대해 원하는 FTT의 **RAID 미러링** 옵션을 선택 합니다.
    ![vSAN 설정 ](media/oracle-rac-storage-wizard-vsan.png) .
7. **고급** 섹션에서 개체당 디스크 줄무늬 수를 선택 합니다. 개체 공간 예약의 경우 **굵은 프로 비전**됨을 선택 합니다. **개체 체크섬 사용 안 함**을 선택 합니다. **다음**을 클릭 합니다.
8. 화면의 지시에 따라 호환 되는 vSAN 데이터 저장소 목록을 확인 하 고 설정을 검토 한 후 설치를 완료 합니다.

## <a name="create-oracle-vms-and-create-shared-vm-disks-for-oracle"></a>Oracle Vm 만들기 및 Oracle 용 공유 VM 디스크 만들기

Oracle 용 VM을 만들려면 기존 VM을 복제 하거나 새 VM을 만듭니다.  이 섹션에서는 새 VM을 만든 후 복제 하 여 기본 운영 체제를 설치한 후 두 번째 VM을 만드는 방법을 설명 합니다.  Vm을 만든 후에는 해당 Vm에 추가 디스크를 만들 수 있습니다.  Oracle 클러스터는 공유 디스크를 사용 하 여 저장, 데이터, 로그 및 다시 실행 로그를 저장 합니다.

### <a name="create-vms"></a>VM 만들기

1. VCenter에서 **호스트 및 클러스터** 아이콘을 클릭 합니다. Oracle 용으로 만든 클러스터를 선택 합니다.
2. 클러스터를 마우스 오른쪽 단추로 클릭 하 고 **새 가상 컴퓨터**를 선택 합니다.
3. **새 가상 컴퓨터 만들기** 를 선택 하 고 **다음**을 클릭 합니다.
4. 컴퓨터 이름을로, Oracle VM의 위치를 선택 하 고 **다음**을 클릭 합니다.
5. 클러스터 리소스를 선택 하 고 **다음**을 클릭 합니다.
6. 클러스터에 대 한 vSAN 데이터 저장소를 선택 하 고 **다음**을 클릭 합니다.
7. 기본 ESXi 6.5 호환성 선택 항목을 유지 하 고 **다음**을 클릭 합니다.
8. 만들려는 VM에 대 한 ISO의 게스트 OS를 선택 하 고 **다음**을 클릭 합니다.
9. OS를 설치 하는 데 필요한 하드 디스크 크기를 선택 합니다.
10. 다른 장치에 응용 프로그램을 설치 하려면 **새 장치 추가**를 클릭 합니다.
11. 네트워크 옵션을 선택 하 고 공용 네트워크에 대해 만든 분산 포트 그룹을 할당 합니다.
12. 다른 네트워크 인터페이스를 추가 하려면 **새 장치 추가** 를 클릭 하 고 개인 네트워크에 대해 만들어진 분산 포트 그룹을 선택 합니다.
13. 새 DC/DVD 드라이브의 경우 기본 운영 체제 설치에 대 한 ISO가 포함 된 데이터 저장소 ISO 파일을 선택 합니다. 이전에 Iso 및 Templates 폴더에 업로드 한 파일을 선택 하 고 **확인**을 클릭 합니다.
14. 설정을 검토 하 고 **확인** 을 클릭 하 여 새 VM을 만듭니다.
15. VM의 전원을 켭니다. 운영 체제 및 필요한 업데이트를 설치 합니다.

운영 체제가 설치 된 후에는 두 번째 VM을 복제할 수 있습니다. VM 항목을 마우스 오른쪽 단추로 클릭 하 고 및 복제 옵션을 선택 합니다.

### <a name="create-shared-disks-for-vms"></a>Vm에 대 한 공유 디스크 만들기

Oracle은 공유 디스크를 사용 하 여 데이터, 로그 및 다시 실행 로그 파일을 저장 합니다.  VCenter에서 공유 디스크를 만들어 두 Vm 모두에 탑재할 수 있습니다.  성능을 높이려면 다른 SCSI 컨트롤러에 데이터 디스크를 저장 합니다. 아래 단계에서는 vCenter에서 공유 디스크를 만든 다음 가상 컴퓨터에 연결 하는 방법을 보여 줍니다. vCenter Flash 클라이언트는 VM 속성을 수정 하는 데 사용 됩니다.

#### <a name="create-disks-on-the-first-vm"></a>첫 번째 VM에 디스크 만들기

1. VCenter에서 Oracle Vm 중 하나를 마우스 오른쪽 단추로 클릭 하 고 **설정 편집**을 선택 합니다.
2. 새 장치 섹션에서 **SCSI 컨트롤러** 를 선택 하 고 **추가**를 클릭 합니다.
3. 새 장치 섹션에서 **새 하드 디스크** 를 선택 하 고 **추가**를 클릭 합니다.
4. 새 하드 디스크의 속성을 확장 합니다.
5. 하드 디스크의 크기를 지정 합니다.
6. VM 저장소 정책을 이전에 정의한 vSAN 저장소 정책으로 지정 합니다.
7. VSAN 데이터 저장소에서 폴더로 위치를 선택 합니다. 위치는 디스크를 검색 하 고 두 번째 VM에 연결 하는 데 도움이 됩니다.
8. 디스크 프로 비전의 경우 **굵은 프로 비전 즉시 0**을 선택 합니다.
9. 공유의 경우 **다중 기록기**를 지정 합니다.
10. 가상 장치 노드에 대해 2 단계에서 만든 새 SCSI 컨트롤러를 선택 합니다.

    ![첫 번째 VM에 디스크 만들기](media/oracle-rac-new-hard-disk.png)

Oracle 데이터, 로그 및 다시 실행 로그 파일에 필요한 모든 새 디스크에 대해 2 ~ 10 단계를 반복 합니다.

#### <a name="attach-disks-to-second-vm"></a>두 번째 VM에 디스크 연결

1. VCenter에서 Oracle Vm 중 하나를 마우스 오른쪽 단추로 클릭 하 고 **설정 편집**을 선택 합니다.
2. 새 장치 섹션에서 **SCSI 컨트롤러** 를 선택 하 고 **추가**를 클릭 합니다.
3. 새 장치 섹션에서 **기존 하드 디스크** 를 선택 하 고 **추가**를 클릭 합니다.
4. 첫 번째 VM에 대해 디스크가 생성 된 위치를 찾아 .VMDK 파일을 선택 합니다.
5. VM 저장소 정책을 이전에 정의한 vSAN 저장소 정책으로 지정 합니다.
6. 디스크 프로 비전의 경우 **굵은 프로 비전 즉시 0**을 선택 합니다.
7. 공유의 경우 **다중 기록기**를 지정 합니다.
8. 가상 장치 노드에 대해 2 단계에서 만든 새 SCSI 컨트롤러를 선택 합니다.

    ![첫 번째 VM에 디스크 만들기](media/oracle-rac-existing-hard-disk.png)

Oracle 데이터, 로그 및 다시 실행 로그 파일에 필요한 모든 새 디스크에 대해 2 ~ 7 단계를 반복 합니다.

## <a name="set-up-vm-host-affinity-rules"></a>VM 호스트 선호도 규칙 설정

VM-호스트 선호도 규칙은 VM이 원하는 호스트에서 실행 되는지 확인 합니다.  VCenter에 대 한 규칙을 정의 하 여 Oracle VM이 적절 한 리소스와 함께 호스트에서 실행 되도록 하 고 특정 라이선스 요구 사항을 충족할 수 있습니다.

1. CloudSimple 포털에서 cloudowner 사용자의 [권한을 에스컬레이션](escalate-private-cloud-privileges.md) 합니다.
2. 사설 클라우드의 vSphere 클라이언트에 로그인 합니다.
3. VSphere 클라이언트에서 Oracle Vm이 배포 되는 클러스터를 선택 하 고 **구성**을 클릭 합니다.
4. 구성 아래에서 **v m/호스트 그룹**을 선택 합니다.
5. 클릭 **+** 합니다.
6. VM 그룹을 추가 합니다. 유형으로 **VM 그룹** 을 선택 합니다. 그룹의 이름을 입력 합니다. Vm을 선택 하 고 **확인** 을 클릭 하 여 그룹을 만듭니다.
6. 호스트 그룹을 추가 합니다. **호스트 그룹** 을 유형으로 선택 합니다. 그룹의 이름을 입력 합니다. Vm이 실행 될 호스트를 선택 하 고 **확인** 을 클릭 하 여 그룹을 만듭니다.
7. 규칙을 만들려면 **v m/호스트 규칙**을 클릭 합니다.
8. 클릭 **+** 합니다.
9. 규칙의 이름을 입력 하 고 **사용**을 선택 합니다.
10. 규칙 유형에 대해 **호스트 하려면 Virtual Machines**을 선택 합니다.
11. Oracle Vm을 포함 하는 VM 그룹을 선택 합니다.
12. Select는 **이 그룹의 호스트에서 실행 해야**합니다.
13. 만든 호스트 그룹을 선택 합니다.
14. **확인** 을 클릭 하 여 규칙을 만듭니다.

## <a name="references"></a>참조

* [VSAN 정책 정보](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.virtualsan.doc/GUID-08911FD3-2462-4C1C-AE81-0D4DBC8F7990.html)
* [Shared Vmdk에 대 한 VMware 다중 기록기 특성](https://docs.vmware.com/en/VMware-Cloud-on-AWS/solutions/VMware-Cloud-on-AWS.df6735f8b729fee463802083d46fdc75/GUID-A7642A82B3D6C5F7806DB40A3F2766D9.html)
