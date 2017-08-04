---
title: "Azure에서 Oracle 데이터베이스의 디자인 및 구현 | Microsoft Docs"
description: "Azure 환경에서 Oracle 데이터베이스를 디자인 및 구현합니다."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: v-shiuma
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 6/22/2017
ms.author: rclaus
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: e13e61a2d055ce4f9777cea8bf6199f2acf9e7bf
ms.contentlocale: ko-kr
ms.lasthandoff: 07/21/2017

---


# <a name="design-and-implement-of-oracle-database-on-azure"></a>Azure에서 Oracle 데이터베이스의 디자인 및 구현

## <a name="assumptions"></a>가정

- 온-프레미스에서 Azure로 Oracle 데이터베이스 마이그레이션 계획
- Oracle AWR의 다양한 메트릭 보고서 이해
- 응용 프로그램 성능 및 플랫폼 사용률에 관한 기준 확립

## <a name="goals"></a>목표

- Azure에서 Oracle 배포를 최적화하기 위한 이해
- Azure 환경에서 Oracle 데이터베이스에 대한 옵션 조정 성능 탐색

### <a name="on-premises-vs-on-azure"></a>온-프레미스 대 Azure

다음은 온-프레미스 응용 프로그램을 Azure로 마이그레이션할 때 중요한 고려 사항입니다. 온-프레미스와 달리 Azure의 리소스(VM, 디스크, VNet 등)는 다른 클라이언트 간에 공유됩니다. 또한 리소스는 요구 사항을 기반으로 스로틀될 수 있습니다. 실패 방지(MTBF)에 집중하는 대신 Azure는 고장에서 더 많이 살아남는 쪽을 택했습니다(MTTR).

다음 테이블에 몇 가지 차이점이 나열되어 있습니다.

> 
> |  | **온-프레미스** | **Azure** |
> | --- | --- | --- |
> | **네트워킹** |LAN/WAN  |SDN - 소프트웨어 정의 네트워킹|
> | **보안 그룹** |IP/포트 제한 도구 |[NSG(네트워크 보안 그룹)](https://azure.microsoft.com/blog/network-security-groups) |
> | **복원력** |MTBF(평균 고장 간격) |MTTR(평균 복구 시간)|
> | **계획된 유지 보수** |패치/업그레이드|[가용성 집합](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines)(Azure에서 관리되는 패치/업그레이드) |
> | **리소스** |전용  |다른 클라이언트와 공유|
> | **지역** |데이터 센터 |[지역 쌍](https://docs.microsoft.com/azure/virtual-machines/windows/regions-and-availability)|
> | **저장소** |SAN/물리적 디스크 |[Azure 관리 저장소](https://azure.microsoft.com/pricing/details/managed-disks/?v=17.23h)|
> | **규모** |수직적 확장 |수평적 확장|


### <a name="requirements"></a>요구 사항

- 데이터베이스 크기 및 증가 속도를 결정
- IOPS 요구 사항을 결정, Oracle AWR 보고서 또는 다른 네트워크 모니터링 도구에 따라 예측 가능

## <a name="configuration-options"></a>구성 옵션

Azure 환경에서 향상된 성능을 조정할 수 있는 잠재적인 네 가지 영역이 있습니다.

- 가상 컴퓨터 크기
- 네트워크 처리량
- 디스크 형식 및 구성
- 디스크 캐시 설정

### <a name="generate-awr-report"></a>AWR 보고서 생성

기존 Oracle 데이터베이스와 Azure로 마이그레이션할 계획이 있는 경우 메트릭(IOPS, Mbps GiBs 등)을 가져오도록 Oracle AWR 보고서를 실행한 다음, 수집된 메트릭에 기반하여 VM을 선택할 수 있습니다. 또는 인프라 팀에 문의하여 유사한 정보를 얻을 수 있습니다.

차이를 비교할 수 있도록 일반 시간 및 최고 워크로드 동안의 AWR 보고서 실행을 고려할 수도 있습니다. 이러한 보고서에 따라, 평균 워크로드 또는 최대 워크로드 시 VM 크기를 정할 수 있습니다.

다음은 AWR 보고서를 만드는 방법에 대한 예입니다.

```bash
$ sqlplus / as sysdba
SQL> EXEC DBMS_WORKLOAD_REPOSITORY.CREATE_SNAPSHOT;
SQL> @?/rdbms/admin/awrrpt.sql
```

### <a name="key-metrics"></a>주요 메트릭

다음은 AWR 보고서에서 얻을 수 있는 메트릭입니다.

- 총 코어 수
- CPU 클럭 속도
- 전체 메모리(GB)
- CPU 사용률
- 최대 데이터 전송 속도
- IO 변경률(읽기/쓰기)
- 다시 실행 로그 속도(MBPs)
- 네트워크 처리량
- 네트워크 대기 시간 비율(낮음/높음)
- 데이터베이스 크기(GB)
- SQL*Net과 클라이언트 간 수신된 바이트

### <a name="virtual-machine-size"></a>가상 컴퓨터 크기

#### <a name="1-initial-estimate-of-vm-size-is-based-on-cpumemoryio-usage-from-the-awr-report"></a>1. VM 크기의 초기 추정치는 AWR 보고서의 CPU/메모리/IO 사용량을 기반으로 합니다.

시스템 병목 현상이 있는 곳으로 표시되는 상위 5개 시간 전경 이벤트를 확인할 수 있습니다.

예를 들어 아래 다이어그램에는 다시 실행 로그 파일에 로그 버퍼를 쓰는 LGWR에 대한 대기 수인 로그 파일 동기화가 가장 위에 있습니다. 이는 더 나은 성능의 저장소/디스크가 필요하다는 뜻입니다. 도한 CPU 수(코어) 및 메모리가 다이어그램에 표시됩니다.

![AWR 보고서 페이지의 스크린샷](./media/oracle-design/cpu_memory_info.png)

아래 다이어그램에는 읽기 및 쓰기의 총 IO가 표시되어 있습니다. 보고서 시간 동안 59GB의 읽기 및 247.3GB의 쓰기가 있었습니다.

![AWR 보고서 페이지의 스크린샷](./media/oracle-design/io_info.png)

#### <a name="2-estimate-the-vm-size"></a>2. VM 크기 예측

다음 단계는 AWR 보고서에서 수집한 정보를 기반으로 사용자 요구 사항을 충족하는 유사한 크기의 VM을 선택하는 것입니다. 이 링크[가상 컴퓨터 크기](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-sizes-memory)에 사용 가능한 VM 목록이 나열되어 있습니다.

#### <a name="3-fine-tune-the-vm-sizing-with-similar-vm-series-based-on-the-acu"></a>3. ACU에 따라 유사한 VM 시리즈로 VM 크기를 미세 조정

VM을 선택했으면 VM에 대한 ACU에 주의해야 합니다. 요구 사항에 더 적합하도록 ACU 값에 따라 유사한 VM을 선택할 수도 있습니다. 자세한 내용은 [Azure 계산 단위](https://docs.microsoft.com/azure/virtual-machines/windows/acu)를 참조하세요.

![ACU 단위 페이지의 스크린샷](./media/oracle-design/acu_units.png)

### <a name="network-throughput"></a>네트워크 처리량

처리량과 IOPS 간 관계는 다음과 같습니다.

![처리량의 스크린샷](./media/oracle-design/throughput.png)

총 네트워크 처리량은 다음을 기반으로 예측합니다.
- SQL*Net 트래픽
- MBps x 서버 수(데이터 가드와 같은 아웃바운드 스트림)
- 응용 프로그램 복제와 같은 다른 요소

![SQL*Net 처리량의 스크린샷](./media/oracle-design/sqlnet_info.png)

기본, VpnGw, ExpressRoute와 같이 네트워크 대역폭 요구 사항에 따라 여러 게이트웨이 형식을 선택하여 사용할 수 있습니다. 자세한 정보는 [VPN Gateway 가격 책정 페이지](https://azure.microsoft.com/en-us/pricing/details/vpn-gateway/?v=17.23h)에서 확인할 수 있습니다.

추천

- 네트워크 대기 시간은 온-프레미스 배포에 비해 더 높습니다. 네트워크 왕복 수를 줄이면 성능이 크게 향상될 수 있습니다.
- 왕복을 줄이기 위해 동일한 가상 컴퓨터에서 높은 트랜잭션 또는 “Chatty” 앱이 있는 응용 프로그램을 통합합니다.

### <a name="disk-types-and-configurations"></a>디스크 형식 및 구성

- 기본 OS 디스크: 영구 데이터 및 캐시를 사용합니다. 이 디스크는 부팅 시간에서 OS 액세스에 최적화되었으며, 트랜잭션 또는 데이터 웨어하우스(분석) 워크로드용으로 설계된 것은 아닙니다.

- 관리되지 않는 디스크: VM 디스크에 해당하는 VHD(가상 하드 디스크) 파일을 저장하는 데 사용되는 저장소 계정을 관리합니다. VHD 파일은 Azure Storage 계정에 페이지 Blob으로 저장됩니다.

- 관리 디스크: Azure에서 VM 디스크에 사용되는 저장소 계정을 관리합니다. 필요한 디스크 유형(Premium 또는 Standard) 및 디스크 크기를 지정합니다. Azure에서 사용자에게 맞는 디스크를 만들고 관리합니다.

- Premium Storage 디스크(프로덕션 워크로드에 가장 적합): Premium Storage는 특정 크기-시리즈 VM(DS, DSv2, GS 및 F 시리즈)에 연결할 수 있는 VM 디스크를 지원합니다. 프리미엄 디스크는 다양한 크기로 제공되며 32GB에서 4096GB까지 다양한 디스크 크기를 선택할 수 있습니다. 디스크 크기마다 자체 성능 사양이 있습니다. 응용 프로그램 요구 사항에 따라 하나 이상의 디스크를 VM에 연결할 수 있습니다.

포털에서 관리되는 새 디스크를 만드는 경우 ‘계정 형식’을 선택하여 사용하려는 디스크 형식을 지정할 수 있습니다. 모든 사용 가능한 디스크가 드롭다운 상자에 표시되는 것은 아닙니다. Azure Storage가 VM과 통합되어 있고, 여기에 SKU 간 한도가 있기 때문입니다(예: 최대 드라이브 수, 최대 IOPS). 특정 크기의 VM을 골랐으면 해당 VM 크기에 따라 사용 가능한 Premium Storage SKU만 표시합니다.

![관리되는 디스크 페이지의 스크린샷](./media/oracle-design/premium_disk01.png)

자세한 내용은 [Azure Premium Storage](https://docs.microsoft.com/azure/storage/storage-premium-storage)를 참조하세요.

VM에서 저장소를 구성한 경우 데이터베이스를 만들기 전에 디스크의 부하 테스트를 수행할 수 있습니다. 대기 시간 및 처리량 모두에 관해 IO 속도를 알면 VM이 대기 시간 대상으로 예상 처리량을 지원할 수 있는지를 확인하는 데 도움이 됩니다.

Oracle Orion, Sysbench, Fio 등과 같이 응용 프로그램 부하 테스트를 위한 몇 가지 도구가 있습니다.

Oracle 데이터베이스를 배포한 후 부하 테스트를 다시 실행하고 일반 및 최대 워크로드를 시작하면, 사용자 환경의 기준이 결과로 표시됩니다.

저장소 크기가 아닌 IOPS 속도에 따라 저장소 크기를 정하는 것이 더 중요할 수 있습니다. 예를 들어 필요한 IOPS는 5000이지만 사용자는 200GB만 필요할 수 있습니다. 200GB 이상의 저장소 크기를 제공하는 경우에도 여전히 P30 클래스 프리미엄 디스크를 가져올 수도 있습니다.

IOPS 속도는 AWR 보고서에서 얻을 수 있습니다. 이는 다시 실행 로그, 물리적 읽기 및 쓰기 속도에 의해 결정됩니다.

![AWR 보고서 페이지의 스크린샷](./media/oracle-design/awr_report.png)

예를 들어 다시 실행 크기는 11.63Mbps와 같은 초당 12200000바이트입니다. IOPS는 12200000/2358 = 5174입니다.

IO 요구 사항이 명확하면 그러한 요구 사항에 가장 적합한 드라이브 조합을 선택할 수 있습니다.

추천

- 데이터 테이블스페이스의 경우 관리되는 저장소 또는 Oracle ASM을 사용하여 몇 개의 디스크에 I/O 워크로드를 분산
- I/O 블록 크기가 증가함에 따라 읽기 및 쓰기 집약적 작업을 위한 더 많은 데이터 디스크 추가
- 대규모 순차적 프로세스를 위한 블록 크기 증가
- IO 감소를 위한 데이터 압축 사용(데이터 및 인덱스 모두 해당)
- 개별 데이터 디스크에서 다시 실행 로그, 시스템, 임시 및 TS 실행 취소 분리
- 기본 OS 디스크(/dev/sda)에 응용 프로그램 파일을 배치하지 않는 것이 좋습니다. 이 디스크는 빠른 VM 부팅 시간에 최적화되었으며, 사용자 응용 프로그램을 위한 좋은 성능을 제공하지 않을 수 있습니다.

### <a name="disk-cache-settings"></a>디스크 캐시 설정

호스트 캐싱을 위한 세 가지 옵션이 있습니다.

- 읽기 전용: 모든 요청이 향후 읽기에 대해 캐시됩니다. 모든 쓰기가 Windows Azure Storage Blob에 직접 지속됩니다.

- 읽기 쓰기: 이것은 “미리 읽기” 알고리즘입니다. 읽기 및 쓰기가 향후 읽기에 대해 캐시됩니다. Write-through 이외의 쓰기 작업은 먼저 로컬 캐시에 지속됩니다. SQL Server의 경우 Write-through를 사용하기 때문에 쓰기는 WA 저장소에 지속됩니다. 가벼운 워크로드에서 가장 낮은 디스크 대기 시간을 제공합니다.

- 없음(사용 안 함): 캐시를 우회할 수 있습니다. 모든 데이터가 디스크에 전송되며 Windows Azure Storage에 지속됩니다. 이 메서드를 사용하면 I/O 집약적 워크로드에 대해 가장 높은 I/O 속도를 얻을 수 있습니다. “트랜잭션 비용”도 고려해야 합니다.

추천

처리량을 최대화하려면 호스트 캐싱을 ‘없음’으로 시작하는 것이 좋습니다. Premium Storage의 경우 ‘읽기 전용’ 또는 ‘없음’ 옵션으로 파일 시스템을 탑재할 때 “barrier”를 사용하지 않도록 설정해야 하는 것이 중요합니다. /etc/fstab 파일을 UUID로 디스크에 업데이트합니다.

자세한 내용은 [Linux VM용 Premium Storage](https://docs.microsoft.com/azure/storage/storage-premium-storage#premium-storage-for-linux-vms)를 참조하세요.

![관리되는 디스크 페이지의 스크린샷](./media/oracle-design/premium_disk02.png)

- OS 디스크의 경우 기본 ‘읽기/쓰기’ 캐싱 사용
- 시스템, 임시 및 실행 취소의 경우 캐싱에 ‘없음’ 사용
- 데이터의 경우 캐싱에 ‘없음’을 사용하지만, 데이터베이스가 읽기 전용이거나 읽기 집약적일 경우 ‘읽기 전용’ 캐싱 사용

데이터 디스크 설정을 저장하고 나면, OS 수준에서 드라이브를 마운트 해제하고 변경한 후에 다시 탑재하는 경우 외에는 호스트(AFAIK) 캐시 설정을 변경할 수 없습니다.


## <a name="security"></a>보안

Azure 환경을 설치하여 구성하고 나면 다음 단계는 네트워크를 보호하는 것입니다. 몇 가지 권장 사항입니다.

- NSG 정책

NSG는 서브넷 또는 NIC에 의해 정의될 수 있습니다.  응용 프로그램 방화벽과 같은 작업에 대한 보안 및 강제 라우팅 모두 서브넷 수준에서 액세스를 제어하는 것이 더 간단합니다.

- Jumpbox ![Jumpbox 토폴로지 페이지의 스크린샷](./media/oracle-design/jumpbox.png)

더 안전한 액세스를 위해 관리자는 응용 프로그램 서비스 또는 데이터베이스에 직접 연결해서는 안 됩니다. Jumpbox는 관리자 컴퓨터와 Azure 리소스 간 미디어로 사용됩니다.

관리자 컴퓨터는 Jumpbox에 대해서만 제한된 IP 액세스가 가능합니다. 그러면 Jumpbox는 응용 프로그램/데이터베이스에 대한 액세스를 갖습니다.

- 개인 네트워크(서브넷)

응용 프로그램 서비스 및 데이터베이스는 분리된 서브넷에 있는 것이 좋습니다. 따라서 NSG 정책에 따라 더 효율적인 제어를 설정할 수 있습니다.


## <a name="additional-readings"></a>추가 정보:

- [Oracle ASM 구성](configure-oracle-asm.md)
- [Oracle Data Guard 구성](configure-oracle-dataguard.md)
- [Oracle Golden Gate 구성](configure-oracle-golden-gate.md)
- [Oracle Backup 및 Recovery](oracle-backup-recovery.md)

## <a name="next-steps"></a>다음 단계

[자습서: 고가용성 VM 만들기](../../linux/create-cli-complete.md)

[VM 배포 Azure CLI 샘플 탐색](../../linux/cli-samples.md)

