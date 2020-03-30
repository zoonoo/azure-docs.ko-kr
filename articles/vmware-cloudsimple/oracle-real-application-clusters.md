---
title: 클라우드간이별 Azure VMware 솔루션 - 오라클 RAC를 위한 클라우드단순 프라이빗 클라우드 최적화
description: 새 클러스터를 배포하고 RAC(오라클 실제 응용 프로그램 클러스터) 설치 및 구성을 위한 VM을 최적화하는 방법에 대해 설명합니다.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 733a225c66040cb2ab819f041647120c8b63b6a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77016020"
---
# <a name="optimize-your-cloudsimple-private-cloud-for-installing-oracle-rac"></a>오라클 RAC 설치를 위한 클라우드심플 프라이빗 클라우드 최적화

클라우드심플 프라이빗 클라우드 환경에서 RAC(오라클 실제 애플리케이션 클러스터)를 배포할 수 있습니다. 이 가이드는 새 클러스터를 배포하고 Oracle RAC 솔루션에 대한 VM을 최적화하는 방법에 대해 설명합니다. 이 항목의 단계를 완료한 후 Oracle RAC를 설치하고 구성할 수 있습니다.

## <a name="storage-policy"></a>스토리지 정책

Oracle RAC를 성공적으로 구현하려면 클러스터에 적절한 수의 노드가 필요합니다.  vSAN 저장소 정책에서 FTT(허용 실패)는 데이터베이스, 로그 및 디스크를 다시 저장하는 데 사용되는 데이터 디스크에 적용됩니다.  오류를 효과적으로 용인하는 데 필요한 노드 수는 2N+1이며 N은 FTT 값입니다.

예: 원하는 FTT가 2인 경우 클러스터의 총 노드 수는 2*2+1 = 5여야 합니다.

## <a name="overview-of-deployment"></a>배포 개요

다음 섹션에서는 Oracle RAC를 위한 CloudSimple 프라이빗 클라우드 환경을 설정하는 방법을 설명합니다.

1. 디스크 구성에 대한 모범 사례
2. 클라우드 단순 프라이빗 클라우드 vSphere 클러스터 배포
3. 오라클 RAC용 네트워킹 설정
4. 설치 vSAN 스토리지 정책
5. Oracle VM 을 만들고 공유 VM 디스크 만들기
6. VM-호스트 선호도 규칙 설정

## <a name="best-practices-for-disk-configuration"></a>디스크 구성에 대한 모범 사례

Oracle RAC 가상 시스템에는 특정 기능에 사용되는 여러 디스크가 있습니다.  공유 디스크는 Oracle RAC 클러스터에서 사용하는 모든 가상 시스템에 탑재됩니다.  운영 체제 및 소프트웨어 설치 디스크는 개별 가상 시스템에만 탑재됩니다.  

![오라클 RAC 가상 머신 디스크 개요](media/oracle-vm-disks-overview.png)

다음 예제에서는 아래 표에 정의된 디스크를 사용합니다.

| 디스크                                      | 목적                                       | 공유 디스크 |
|-------------------------------------------|-----------------------------------------------|-------------|
| OS                                        | 운영 체제 디스크                         | 예          |
| 그리드                                      | 오라클 그리드 소프트웨어설치 위치     | 예          |
| DATABASE                                  | 오라클 데이터베이스 소프트웨어설치 위치 | 예          |
| 오라홈                                   | 오라클 데이터베이스 바이너리의 기본 위치    | 예          |
| 데이터1, 데이터2, 데이터3, 데이터4                | Oracle 데이터베이스 파일이 저장되는 디스크   | yes         |
| REDO1, 다시 2, 다시 3, REDO4, REDO5, 다시 6  | 로그 디스크 다시 재생                                | yes         |
| OCR1, OCR2, OCR3, OCR4, OCR5              | 투표 디스크                                  | yes         |
| FRA1, FRA2                                | 빠른 복구 영역 디스크                      | yes         |

![오라클 가상 머신 디스크 구성](media/oracle-vmdk.png)

### <a name="virtual-machine-configuration"></a>가상 머신 구성

* 각 가상 컴퓨터는 4개의 SCSI 컨트롤러로 구성됩니다.
* SCSI 컨트롤러 유형은 VMware 파라가상으로 설정됩니다.
* 여러 가상 디스크(.vmdk)가 만들어집니다.
* 디스크는 다른 SCSI 컨트롤러에 탑재됩니다.
* 공유 클러스터 디스크에 대해 다중 작성기 공유 유형이 설정됩니다.
* vSAN 저장소 정책은 디스크의 고가용성을 보장하기 위해 정의됩니다.

### <a name="operating-system-and-software-disk-configuration"></a>운영 체제 및 소프트웨어 디스크 구성

각 Oracle 가상 머신은 호스트 운영 체제, 스왑, 소프트웨어 설치 및 기타 OS 기능을 위한 여러 디스크로 구성됩니다.  이러한 디스크는 가상 시스템 간에 공유되지 않습니다.  

* 각 가상 시스템에 대한 세 개의 디스크는 가상 디스크로 구성되고 Oracle RAC 가상 시스템에 탑재됩니다.
    * OS 디스크
    * 오라클 그리드를 저장하기위한 디스크는 파일을 설치
    * 오라클 데이터베이스 설치 파일을 저장하기위한 디스크
* 디스크는 **씬 프로비전된**로 구성할 수 있습니다.
* 각 디스크는 첫 번째 SCSI 컨트롤러(SCSI0)에 탑재됩니다.  
* 공유가 **공유 없음으로**설정됩니다.
* 중복성은 vSAN 정책을 사용하는 저장소에 정의됩니다.  

![오라클 RAC 데이터 디스크 그룹 구성](media/oracle-vm-os-disks.png)

### <a name="data-disk-configuration"></a>데이터 디스크 구성

데이터 디스크는 주로 데이터베이스 파일을 저장하는 데 사용됩니다.  

* 4개의 디스크가 가상 디스크로 구성되고 모든 Oracle RAC 가상 시스템에 탑재됩니다.
* 각 디스크는 다른 SCSI 컨트롤러에 탑재됩니다.
* 각 가상 디스크는 **두꺼운 프로비저닝 열망 제로로**구성됩니다.  
* 공유는 **다중 기록기로**설정됩니다.  
* 디스크는 ASM(자동 저장소 관리) 디스크 그룹으로 구성해야 합니다.  
* 중복성은 vSAN 정책을 사용하는 저장소에 정의됩니다.  
* ASM 중복성은 **외부** 중복으로 설정됩니다.

![오라클 RAC 데이터 디스크 그룹 구성](media/oracle-vm-data-disks.png)

### <a name="redo-log-disk-configuration"></a>로그 디스크 구성 다시 재생

다시 로그 파일은 데이터베이스에 대한 변경 내용의 복사본을 저장하는 데 사용됩니다.  로그 파일은 오류가 발생한 후 데이터를 복구해야 하는 경우에 사용됩니다.

* 로그 디스크 다시 재생은 여러 디스크 그룹으로 구성해야 합니다.  
* 모든 Oracle RAC 가상 시스템에 6개의 디스크가 생성되고 탑재됩니다.
* 디스크는 다른 SCSI 컨트롤러에 장착됩니다.
* 각 가상 디스크는 **두꺼운 프로비저닝 열망 제로로**구성됩니다.
* 공유는 **다중 기록기로**설정됩니다.  
* 디스크는 두 개의 ASM 디스크 그룹으로 구성되어야 합니다.
* 각 ASM 디스크 그룹에는 서로 다른 SCSI 컨트롤러에 있는 세 개의 디스크가 포함되어 있습니다.  
* ASM 중복성은 **일반** 중복으로 설정됩니다.
* ASM 다시 수행 로그 그룹 모두에서 5개의 다시 수행 로그 파일이 만들어집니다.

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

![오라클 RAC 로그 디스크 그룹 구성 다시](media/oracle-vm-redo-log-disks.png)

### <a name="oracle-voting-disk-configuration"></a>오라클 투표 디스크 구성

투표 디스크는 분할 브레인 상황을 피하기 위해 추가 통신 채널로 쿼럼 디스크 기능을 제공합니다.

* 모든 Oracle RAC 가상 시스템에 5개의 디스크가 만들어지고 탑재됩니다.
* 디스크는 하나의 SCSI 컨트롤러에 장착됩니다.
* 각 가상 디스크는 **두꺼운 프로비저닝 열망 제로로**구성됩니다.
* 공유는 **다중 기록기로**설정됩니다.  
* 디스크는 ASM 디스크 그룹으로 구성해야 합니다.  
* ASM 중복성은 **높은** 중복성으로 설정됩니다.

![오라클 RAC 투표 디스크 그룹 구성](media/oracle-vm-voting-disks.png)

### <a name="oracle-fast-recovery-area-disk-configuration-optional"></a>오라클 빠른 복구 영역 디스크 구성(선택 사항)

빠른 복구 영역(FRA)은 Oracle ASM 디스크 그룹이 관리하는 파일 시스템입니다.  FRA는 백업 및 복구 파일을 위한 공유 저장소 위치를 제공합니다. 오라클은 빠른 복구 영역에서 보관된 로그와 플래시백 로그를 생성합니다. 오라클 복구 관리자(RMAN)는 선택적으로 백업 세트와 이미지 복사본을 빠른 복구 영역에 저장할 수 있으며 미디어 복구 중에 파일을 복원할 때 이를 사용합니다.

* 모든 Oracle RAC 가상 시스템에 두 개의 디스크가 만들어지고 탑재됩니다.
* 디스크는 다른 SCSI 컨트롤러에 장착됩니다.
* 각 가상 디스크는 **두꺼운 프로비저닝 열망 제로로**구성됩니다.
* 공유는 **다중 기록기로**설정됩니다.  
* 디스크는 ASM 디스크 그룹으로 구성해야 합니다.  
* ASM 중복성은 **외부** 중복으로 설정됩니다.

![오라클 RAC 투표 디스크 그룹 구성](media/oracle-vm-fra-disks.png)

## <a name="deploy-cloudsimple-private-cloud-vsphere-cluster"></a>클라우드 심플 프라이빗 클라우드 vSphere 클러스터 배포

프라이빗 클라우드에 vSphere 클러스터를 배포하려면 다음 프로세스를 따르십시오.

1. CloudSimple 포털에서 [프라이빗 클라우드를 만듭니다.](create-private-cloud.md) CloudSimple 새로 만든 사설 클라우드에서 '클라우드 소유자'라는 기본 vCenter 사용자를 만듭니다. 기본 프라이빗 클라우드 사용자 및 권한 모델에 대한 자세한 내용은 [사설 클라우드 권한 모델 알아보기를](learn-private-cloud-permissions.md)참조하십시오.  이 단계는 프라이빗 클라우드에 대한 기본 관리 클러스터를 만듭니다.

2. CloudSimple 포털에서 새 [클러스터로 프라이빗 클라우드를 확장합니다.](expand-private-cloud.md)  이 클러스터는 Oracle RAC를 배포하는 데 사용됩니다.  원하는 내결함성(최소 3개 노드)을 기준으로 노드 수를 선택합니다.

## <a name="set-up-networking-for-oracle-rac"></a>오라클 RAC용 네트워킹 설정

1. 프라이빗 클라우드에서 오라클 공용 네트워크와 오라클 프라이빗 네트워크에 대해 두 [개의 VLAN을 만들고](create-vlan-subnet.md)적절한 서브넷 CiDR을 할당합니다.
2. VLAN을 만든 후 프라이빗 [클라우드 vCenter에서 분산 포트 그룹을](create-vlan-subnet.md#use-vlan-information-to-set-up-a-distributed-port-group-in-vsphere)만듭니다.
3. 오라클 환경에 대한 관리 클러스터에 [DHCP 및 DNS 서버 가상 머신을](dns-dhcp-setup.md) 설정합니다.
4. 프라이빗 클라우드에 설치된 [DNS 서버에서 DNS 전달을 구성합니다.](on-premises-dns-setup.md#create-a-conditional-forwarder)

## <a name="set-up-vsan-storage-policies"></a>vSAN 스토리지 정책 설정

vSAN 정책은 VM 디스크에 저장된 데이터에 대한 허용 오류 및 디스크 스트라이프를 정의합니다.  생성된 저장소 정책은 VM을 만드는 동안 VM 디스크에 적용되어야 합니다.

1. 프라이빗 [클라우드의 vSphere 클라이언트에 로그인합니다.](https://docs.azure.cloudsimple.com/vsphere-access)
2. 상단 메뉴에서 **정책 및 프로필을 선택합니다.**
3. 왼쪽 메뉴에서 **VM 저장소 정책을** 선택한 다음 **VM 저장소 정책 만들기를**선택합니다.
4. 정책에 대한 의미 있는 이름을 입력하고 **다음을 클릭합니다.**
5. 정책 **구조** 섹션에서 **vSAN 저장소에 대한 규칙 활성화를** 선택하고 **다음을**클릭합니다.
6. **vSAN** > **가용성** 섹션에서 사이트 재해 허용 오차에 대한 **없음을** 선택합니다. 실패가 허용되지 않는 경우 원하는 FTT에 대한 **RAID - 미러링** 옵션을 선택합니다.
    ![vSAN](media/oracle-rac-storage-wizard-vsan.png)설정 .
7. **고급** 섹션에서 개체당 디스크 스트라이프 수를 선택합니다. 개체 공간 예약의 경우 **두꺼운 프로비저닝을 선택합니다.** **개체 체크섬 사용 안 함**선택 . **다음을 클릭합니다.**
8. 화면의 지침에 따라 호환되는 vSAN 데이터스토어 목록을 보고 설정을 검토하고 설정을 완료합니다.

## <a name="create-oracle-vms-and-create-shared-vm-disks-for-oracle"></a>오라클 VM 을 만들고 오라클을 위한 공유 VM 디스크 생성

오라클용 VM을 만들려면 기존 VM을 복제하거나 새 VM을 만듭니다.  이 섹션에서는 새 VM을 만든 다음 복제하여 기본 운영 체제를 설치한 후 두 번째 VM을 만드는 방법에 대해 설명합니다.  VM을 만든 후 VM에 추가 디스크를 만들 수 있습니다.  Oracle 클러스터는 로그를 저장, 데이터, 로그 및 다시 재생하기 위해 공유 디스크를 사용합니다.

### <a name="create-vms"></a>VM 만들기

1. vCenter에서 호스트 **및 클러스터 아이콘을 클릭합니다.** 오라클에 대해 만든 클러스터를 선택합니다.
2. 클러스터를 마우스 오른쪽 단추로 클릭하고 **새 가상 컴퓨터를**선택합니다.
3. **새 가상 컴퓨터 만들기를** 선택하고 다음 을 **클릭합니다.**
4. 컴퓨터 이름을 지정하고 Oracle VM의 위치를 선택한 다음 을 **클릭합니다.**
5. 클러스터 리소스를 선택하고 다음 을 **클릭합니다.**
6. 클러스터의 vSAN 데이터 스토어를 선택하고 다음 을 **클릭합니다.**
7. 기본 ESXi 6.5 호환성 선택을 유지하고 **다음을**클릭합니다.
8. 만드는 VM에 대한 ISO의 게스트 OS를 선택하고 **다음을**클릭합니다.
9. OS 를 설치하는 데 필요한 하드 디스크 크기를 선택합니다.
10. 다른 장치에 응용 프로그램을 설치하려면 **새 장치 추가를**클릭합니다.
11. 네트워크 옵션을 선택하고 공용 네트워크에 대해 만든 분산 포트 그룹을 할당합니다.
12. 추가 네트워크 인터페이스를 추가하려면 **새 장치 추가를** 클릭하고 개인 네트워크에 대해 만든 분산 포트 그룹을 선택합니다.
13. 새 DC/DVD 드라이브의 경우 기본 운영 체제 설치를 위해 ISO가 포함된 데이터스토어 ISO 파일을 선택합니다. 이전에 ISO 및 템플릿 폴더에 업로드한 파일을 선택하고 **확인을**클릭합니다.
14. 설정을 검토하고 **확인을** 클릭하여 새 VM을 만듭니다.
15. VM의 전원을 켜는 경우 운영 체제 및 필요한 업데이트 설치

운영 체제를 설치한 후 두 번째 VM을 복제할 수 있습니다. VM 항목을 마우스 오른쪽 단추로 클릭하고 및 복제 옵션을 선택합니다.

### <a name="create-shared-disks-for-vms"></a>VM용 공유 디스크 만들기

오라클은 공유 디스크를 사용하여 데이터를 저장하고 로그 파일을 다시 재생합니다.  vCenter에서 공유 디스크를 만들고 두 VM에 모두 마운트할 수 있습니다.  더 높은 성능을 위해 데이터 디스크를 다른 SCSI 컨트롤러 에 배치하십시오 아래 단계는 vCenter에서 공유 디스크를 만든 다음 가상 컴퓨터에 연결하는 방법을 보여 준다. vCenter 플래시 클라이언트는 VM 속성을 수정하는 데 사용됩니다.

#### <a name="create-disks-on-the-first-vm"></a>첫 번째 VM에서 디스크 만들기

1. vCenter에서 Oracle VM 중 하나를 마우스 오른쪽 단추로 클릭하고 **설정 편집을**선택합니다.
2. 새 장치 섹션에서 **SCSI 컨트롤러를** 선택하고 **추가 를**클릭합니다.
3. 새 장치 섹션에서 **새 하드 디스크를** 선택하고 **추가 를**클릭합니다.
4. 새 하드 디스크의 속성을 확장합니다.
5. 하드 디스크의 크기를 지정합니다.
6. VM 저장소 정책을 이전에 정의한 vSAN 저장소 정책으로 지정합니다.
7. vSAN 데이터스토어에서 위치를 폴더로 선택합니다. 이 위치는 디스크를 검색하고 두 번째 VM에 연결하는 데 도움이 됩니다.
8. 디스크 프로비저닝의 경우 **[0] []프로비저닝 을 선택합니다.**
9. 공유의 경우 **다중 기록기를**지정합니다.
10. 가상 장치 노드의 경우 2단계에서 만든 새 SCSI 컨트롤러를 선택합니다.

    ![첫 번째 VM에서 디스크 만들기](media/oracle-rac-new-hard-disk.png)

Oracle 데이터, 로그 및 다시 재생에 필요한 모든 새 디스크에 대해 2 ~10단계를 반복합니다.

#### <a name="attach-disks-to-second-vm"></a>디스크를 두 번째 VM에 연결

1. vCenter에서 Oracle VM 중 하나를 마우스 오른쪽 단추로 클릭하고 **설정 편집을**선택합니다.
2. 새 장치 섹션에서 **SCSI 컨트롤러를** 선택하고 **추가 를**클릭합니다.
3. 새 장치 섹션에서 **기존 하드 디스크를** 선택하고 **추가 를**클릭합니다.
4. 첫 번째 VM에 대해 디스크가 생성된 위치를 찾아보고 VMDK 파일을 선택합니다.
5. VM 저장소 정책을 이전에 정의한 vSAN 저장소 정책으로 지정합니다.
6. 디스크 프로비저닝의 경우 **[0] []프로비저닝 을 선택합니다.**
7. 공유의 경우 **다중 기록기를**지정합니다.
8. 가상 장치 노드의 경우 2단계에서 만든 새 SCSI 컨트롤러를 선택합니다.

    ![첫 번째 VM에서 디스크 만들기](media/oracle-rac-existing-hard-disk.png)

Oracle 데이터, 로그 및 다시 재생에 필요한 모든 새 디스크에 대해 2 - 7단계를 반복합니다.

## <a name="set-up-vm-host-affinity-rules"></a>VM 호스트 선호도 규칙 설정

VM-호스트 선호도 규칙은 VM이 원하는 호스트에서 실행되도록 합니다.  vCenter에 대한 규칙을 정의하여 적절한 리소스로 오라클 VM이 호스트에서 실행되도록 하고 특정 라이선스 요구 사항을 충족할 수 있습니다.

1. CloudSimple 포털에서 클라우드 소유자 사용자의 [권한을 에스컬레이션합니다.](escalate-private-cloud-privileges.md)
2. 프라이빗 [클라우드의 vSphere 클라이언트에 로그인합니다.](https://docs.azure.cloudsimple.com/vsphere-access)
3. vSphere 클라이언트에서 Oracle VM이 배포되는 클러스터를 선택하고 **구성을**클릭합니다.
4. 구성에서 **VM/호스트 그룹을**선택합니다.
5. 을 **+** 클릭합니다.
6. VM 그룹을 추가합니다. **VM 그룹을** 유형으로 선택합니다. 그룹의 이름을 입력합니다. VM을 선택한 다음 **확인을** 클릭하여 그룹을 만듭니다.
6. 호스트 그룹을 추가합니다. **호스트 그룹을** 유형으로 선택합니다. 그룹의 이름을 입력합니다. VM이 실행될 호스트를 선택한 다음 **확인을** 클릭하여 그룹을 만듭니다.
7. 규칙을 만들려면 **VM/호스트 규칙을**클릭합니다.
8. 을 **+** 클릭합니다.
9. 규칙의 이름을 입력하고 **사용 을**선택합니다.
10. 규칙 유형의 경우 **호스트할 가상 컴퓨터를**선택합니다.
11. Oracle VM이 포함된 VM 그룹을 선택합니다.
12. **이 그룹의 호스트에서 실행해야 함을**선택합니다.
13. 만든 호스트 그룹을 선택합니다.
14. **확인을** 클릭하여 규칙을 만듭니다.

## <a name="references"></a>참조

* [vSAN 정책 소개](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.virtualsan.doc/GUID-08911FD3-2462-4C1C-AE81-0D4DBC8F7990.html)
* [공유 VMDK에 대한 VMware 다중 작성기 특성](https://docs.vmware.com/en/VMware-Cloud-on-AWS/solutions/VMware-Cloud-on-AWS.df6735f8b729fee463802083d46fdc75/GUID-A7642A82B3D6C5F7806DB40A3F2766D9.html)
