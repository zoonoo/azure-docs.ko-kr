---
title: Azure 사이트 복구를 통해 Azure VM의 복제 문제 해결
description: Azure 사이트 복구를 사용 하 고 Azure VM 재해 복구에서 복제 문제 해결
author: sideeksh
manager: rochakm
ms.topic: troubleshooting
ms.date: 8/2/2019
ms.openlocfilehash: 67b68cc8a1db4a058675dc51fb3805093c455908
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80276668"
---
# <a name="troubleshoot-replication-in-azure-vm-disaster-recovery"></a>Azure VM 재해 복구에서 복제 문제 해결

이 문서에서는 지역 간에 Azure 가상 머신을 복제 및 복구할 때 Azure Site Recovery에서 발생하는 일반적인 문제를 설명합니다. 또한 일반적인 문제를 해결하는 방법에 대해서도 설명합니다. 지원되는 구성에 대한 자세한 내용은 [Azure VM을 복제하기 위한 지원 매트릭스](site-recovery-support-matrix-azure-to-azure.md)를 참조하세요.

Azure 사이트 복구는 원본 지역의 데이터를 재해 복구 지역으로 일관되게 복제합니다. 또한 5분마다 충돌 일관된 복구 지점을 생성합니다. Site Recovery에서 60분 동안 복구 지점을 만들 수 없는 경우 다음 정보를 알려줍니다.

오류 메시지: "지난 60분 동안 VM에 사용할 수 있는 충돌 일관된 복구 지점이 없습니다."</br>
오류 ID: 153007

다음 섹션에서는 원인 및 해결 방법을 설명합니다.

## <a name="high-data-change-rate-on-the-source-virtual-machine"></a><a name="high-data-change-rate-on-the-source-virtal-machine"></a>원본 가상 머신의 높은 데이터 변경률

Azure 사이트 복구원본 가상 컴퓨터의 데이터 변경률이 지원되는 제한보다 높은 경우 이벤트를 만듭니다. 이탈이 높음이 문제인지 확인하려면 **복제된 항목** > **VM** > **이벤트로 이동하십시오.**
"지원되는 한도를 초과하는 데이터 변경 률"이 표시됩니다.

![너무 높은 데이터 변경률을 보여 주는 Azure 사이트 복구 페이지](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event.png)

이벤트를 선택하면 정확한 디스크 정보가 표시됩니다.

![데이터 변경 률 이벤트 세부 정보를 표시하는 페이지](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event2.png)

### <a name="azure-site-recovery-limits"></a>Azure Site Recovery 제한

다음 테이블에는 Azure Site Recovery 제한이 제공됩니다. 이러한 제한은 테스트를 기반으로 하지만 가능한 모든 응용 프로그램 입력 출력(I/O) 조합을 다룰 수는 없습니다. 실제 결과는 애플리케이션 I/O 조합에 따라 달라질 수 있습니다.

고려해야 할 두 가지 제한사항: 디스크당 데이터 변동 및 가상 컴퓨터당 데이터 변동. 예를 들어 다음 표의 프리미엄 P20 디스크를 살펴보겠습니다. 단일 VM의 경우 사이트 복구는 최대 5개의 디스크로 디스크당 5MB/s의 변동을 처리할 수 있습니다. 사이트 복구에는 VM당 총 변동이 25MB/s로 제한됩니다.

**복제 저장소 대상** | **원본 디스크의 평균 I/O 크기** |**원본 디스크의 평균 데이터 변동** | **원본 데이터 디스크의 일별 총 데이터 변동**
---|---|---|---
Standard Storage | 8KB    | 2MB/초 | 디스크당 168GB
프리미엄 P10 또는 P15 디스크 | 8KB    | 2MB/초 | 디스크당 168GB
프리미엄 P10 또는 P15 디스크 | 16KB | 4MB/초 |    디스크당 336GB
프리미엄 P10 또는 P15 디스크 | 32KB 이상 | 8MB/초 | 디스크당 672GB
프리미엄 P20 또는 P30 또는 P40 또는 P50 디스크 | 8KB    | 5MB/초 | 디스크당 421GB
프리미엄 P20 또는 P30 또는 P40 또는 P50 디스크 | 16KB 이상 |20 MB/s | 디스크당 1684GB

### <a name="solution"></a>해결 방법

Azure 사이트 복구에는 디스크 유형에 따라 데이터 변경 속도가 제한됩니다. 이 문제가 되풀이되거나 일시적인지 확인하려면 영향을 받는 가상 시스템의 데이터 변경 률을 찾습니다. 원본 가상 머신으로 이동하여 **모니터링** 아래의 메트릭을 찾은 다음, 이 스크린샷과 같이 메트릭을 추가합니다.

![데이터 변경률을 찾기 위한 3단계 프로세스를 보여 주는 페이지](./media/site-recovery-azure-to-azure-troubleshoot/churn.png)

1. **메트릭 추가**를 선택하고 **OS 디스크 쓰기 바이트/초** 및 **데이터 디스크 쓰기 바이트/초**를 추가합니다.
1. 스크린샷에 표시된 것처럼 스파이크를 모니터링합니다.
1. OS 디스크 및 결합된 모든 데이터 디스크에서 발생하는 총 쓰기 작업을 보여줍니다. 이러한 메트릭은 디스크별 수준에서 정보를 제공하지는 않지만 데이터 변동의 총 패턴을 나타냅니다.

데이터 변경 률이 급증하면 가끔 씩씩한 데이터 버스트에서 발생할 수 있습니다. 데이터 변경 률이 10MB/s(프리미엄) 또는 2MB/s(표준의 경우)를 초과하여 다운되면 복제가 따라잡게 됩니다. 변동이 지원되는 제한을 훨씬 초과하는 경우 다음 옵션 중 하나를 고려하십시오.

- 높은 데이터 변경 률을 일으키는 디스크제외: 첫째, 복제를 사용하지 않도록 설정합니다. 그런 다음 [PowerShell](./azure-to-azure-exclude-disks.md)을 사용하여 디스크를 제외할 수 있습니다.
- 재해 복구 저장소 디스크 계층 변경: 이 옵션은 디스크 데이터 변동이 20MB/s 미만인 경우에만 가능합니다. P10 디스크가 있는 VM의 데이터 변동이 8MB/s보다 크지만 10MB/s 미만이라고 가정해 보겠습니다. 고객이 보호 중에 대상 스토리지에 대해 P30 디스크를 사용할 수 있는 경우 문제를 해결할 수 있습니다. 이 솔루션은 프리미엄 관리 디스크를 사용하는 컴퓨터에서만 가능합니다. 다음 단계를 수행하세요.

    1. 영향을 받는 복제된 컴퓨터의 **디스크로** 이동하여 복제본 디스크 이름을 복사합니다.
    1. 관리되는 디스크의 이 복제본으로 이동합니다.
    1. **개요에** SAS URL이 생성되었다는 배너가 표시될 수 있습니다. 이 배너를 선택하고 내보내기를 취소합니다. 배너가 표시되지 않으면 이 단계를 무시합니다.
    1. SAS URL이 해지되는 즉시 관리 디스크의 **구성으로** 이동합니다. 사이트 복구가 원본 디스크에서 관찰된 이탈률을 지원하므로 크기를 늘립니다.

## <a name="network-connectivity-problems"></a><a name="Network-connectivity-problem"></a>네트워크 연결 문제

### <a name="network-latency-to-a-cache-storage-account"></a>캐시 스토리지 계정의 네트워크 대기 시간

Site Recovery는 복제된 데이터를 캐시 스토리지 계정으로 보냅니다. 가상 컴퓨터에서 캐시 저장소 계정에 데이터를 업로드하는 속도가 3초 만에 4MB보다 느린 경우 네트워크 대기 시간이 발생할 수 있습니다.

대기 시간 관련 문제를 확인하려면 [AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy)를 사용합니다. 이 명령줄 유틸리티를 사용하여 가상 컴퓨터에서 캐시 저장소 계정으로 데이터를 업로드할 수 있습니다. 대기 시간이 많은 경우 NVA(네트워크 가상 어플라이언스)를 사용하여 VM에서 아웃바운드 네트워크 트래픽을 제어하는지 여부를 확인합니다. 모든 복제 트래픽이 NVA를 통과하는 경우 어플라이언스가 제한될 수 있습니다.

복제 트래픽이 NVA로 이동하지 않도록 "Storage"에 대한 가상 네트워크에서 네트워크 서비스 엔드포인트를 만드는 것이 좋습니다. 자세한 내용은 [네트워크 가상 어플라이언스 구성](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#network-virtual-appliance-configuration)을 참조하세요.

### <a name="network-connectivity"></a>네트워크 연결

사이트 복구 복제가 작동하려면 VM이 특정 URL 또는 IP 범위에 아웃바운드 연결을 제공해야 합니다. 방화벽 뒤에 VM이 있거나 NSG(네트워크 보안 그룹) 규칙을 사용하여 아웃바운드 연결을 제어할 수 있습니다. 이 경우 문제가 발생할 수 있습니다. 모든 URL이 연결되었는지 확인하려면 [Site Recovery URL에 대한 아웃바운드 연결](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges)을 참조하세요.

## <a name="error-id-153006---no-app-consistent-recovery-point-available-for-the-vm-in-the-past-x-minutes"></a>오류 ID 153006 - 지난 "X" 분에서 VM에 사용할 수 있는 앱 일관된 복구 지점이 없습니다.

다음은 가장 일반적인 문제 중 일부입니다.

#### <a name="known-issue-in-sql-server-20082008-r2"></a>SQL 서버 2008/2008 R2에서 알려진 문제

**해결 방법**: SQL 서버 2008/2008 R2에 알려진 문제가 있습니다. SQL Server [2008 R2를 호스팅하는 서버에 대해 Azure 사이트 복구 에이전트 또는 기타 구성 요소가 아닌 VSS 백업이 실패하는](https://support.microsoft.com/help/4504103/non-component-vss-backup-fails-for-server-hosting-sql-server-2008-r2)문서를 참조하십시오.

#### <a name="azure-site-recovery-jobs-fail-on-servers-hosting-any-version-of-sql-server-instances-with-auto_close-dbs"></a>azure 사이트 복구 작업 AUTO_CLOSE DB를 사용 하 고 모든 버전의 SQL Server 인스턴스를 호스팅 하는 서버에서 실패 합니다.

**해결 방법**: Azure [사이트 복구 작업과 같은 구성 요소 VSS 백업이 AUTO_CLOSE DB를 사용하여 SQL Server 인스턴스를 호스팅하는 서버에서 실패하는](https://support.microsoft.com/help/4504104/non-component-vss-backups-such-as-azure-site-recovery-jobs-fail-on-ser)문제를 참조하십시오.


#### <a name="known-issue-in-sql-server-2016-and-2017"></a>SQL Server 2016 및 2017의 알려진 이슈

**해결 방법**: SQL [Server 2016 및 2017에서 구성 요소가 아닌 기반 백업으로 가상 컴퓨터를 백업할 때 오류 발생](https://support.microsoft.com/en-us/help/4508218/cumulative-update-16-for-sql-server-2017)을 참조하십시오.

#### <a name="youre-using-azure-storage-spaces-direct-configuration"></a>Azure 저장소 공간 직접 구성을 사용 하는

**해결 방법**: Azure 사이트 복구저장소 공간 직접 구성에 대한 응용 프로그램 일관된 복구 지점을 만들 수 없습니다. [복제 정책을 구성합니다.](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-enable-replication-s2d-vms)

### <a name="more-causes-because-of-vss-related-issues"></a>VSS 관련 문제로 인해 더 많은 원인:

추가 문제를 해결하려면 원본 컴퓨터의 파일을 확인하여 오류 에 대한 정확한 오류 코드를 얻습니다.

    C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\Application Data\ApplicationPolicyLogs\vacp.log

파일의 오류를 찾으려면 편집기에서 vacp.log 파일을 열어 문자열 "vacpError"를 검색합니다.

    Ex: vacpError:220#Following disks are in FilteringStopped state [\\.\PHYSICALDRIVE1=5, ]#220|^|224#FAILED: CheckWriterStatus().#2147754994|^|226#FAILED to revoke tags.FAILED: CheckWriterStatus().#2147754994|^|

앞의 예에서 **2147754994는** 이 문장 다음에 오는 오류에 대해 알려주는 오류 코드입니다.

#### <a name="vss-writer-is-not-installed---error-2147221164"></a>VSS 라이터가 설치되지 않음 - 오류 2147221164

**해결 방법**: 응용 프로그램 일관성 태그를 생성하기 위해 Azure 사이트 복구는 VSS(볼륨 섀도우 복사 서비스)를 사용합니다. 사이트 복구는 VSS 공급자를 설치하여 앱 일관성 스냅숏을 생성합니다. Azure 사이트 복구는 이 VSS 공급자를 서비스로 설치합니다. VSS 공급자가 설치되지 않은 경우 응용 프로그램 일관성 스냅숏 생성에 실패합니다. 오류 ID 0x80040154 "클래스가 등록되지 않음"을 표시합니다. [VSS 기록기 설치 문제 해결에](https://docs.microsoft.com/azure/site-recovery/vmware-azure-troubleshoot-push-install#vss-installation-failures)대한 문서를 참조하십시오.

#### <a name="vss-writer-is-disabled---error-2147943458"></a>VSS 라이터를 사용할 수 없습니다 - 오류 2147943458

**해결 방법**: 응용 프로그램 일관성 태그를 생성하기 위해 Azure 사이트 복구는 VSS를 사용합니다. 사이트 복구는 VSS 공급자를 설치하여 앱 일관성 스냅숏을 생성합니다. 이 VSS 공급자는 서비스로 설치됩니다. VSS 공급자 서비스를 사용하도록 설정하지 않은 경우 응용 프로그램 일관성 스냅숏 생성에 실패합니다. "지정된 서비스가 비활성화되어 시작할 수 없음(0x80070422)" 오류가 표시됩니다.

VSS를 사용하지 않도록 설정한 경우:

- VSS 공급자 서비스의 시작 유형이 **자동**으로 설정되어 있는지 확인합니다.
- 다음 서비스를 다시 시작하십시오.
   - VSS 서비스
   - Azure Site Recovery VSS 공급자
   - VDS 서비스

#### <a name="vss-provider-not_registered---error-2147754756"></a>VSS 공급자 NOT_REGISTERED - 오류 2147754756

**해결 방법**: 응용 프로그램 일관성 태그를 생성하기 위해 Azure 사이트 복구는 VSS를 사용합니다. Azure 사이트 복구 VSS 공급자 서비스가 설치되어 있는지 확인합니다.

VSS 공급자를 다시 설치하려면 다음 명령을 사용합니다.
1. 기존 공급자 제거: C:\프로그램 파일 (x86)\Microsoft Azure 사이트 복구\에이전트\InMageVSSProvider_Uninstall.cmd
1. VSS 공급자 다시 설치: C:\프로그램 파일 (x86)\마이크로소프트 Azure 사이트 복구\에이전트\InMageVSSProvider_Install.cmd

VSS 공급자 서비스의 시작 유형이 **자동**으로 설정되어 있는지 확인합니다.

다음 서비스를 다시 시작하십시오.
- VSS 서비스
- Azure Site Recovery VSS 공급자
- VDS 서비스
