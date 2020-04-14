---
title: Azure Site Recovery를 사용한 VMware VM 및 물리적 서버와 Azure 간 재해 복구에 대한 복제 문제 해결 | Microsoft Docs
description: 이 문서에서는 Azure Site Recovery를 사용한 VMware VM 및 물리적 서버와 Azure 간 재해 복구 중에 일반적인 복제 문제를 해결하는 방법을 제공합니다.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 08/2/2019
ms.author: mayg
ms.openlocfilehash: f91ee5654b4add37d3cce4f875be1f9c2b398ab9
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81259496"
---
# <a name="troubleshoot-replication-issues-for-vmware-vms-and-physical-servers"></a>VMware VM 및 실제 서버에 대한 복제 문제 해결

이 문서에서는 [사이트 복구를](site-recovery-overview.md)사용하여 온-프레미스 VM웨어 VM 및 물리적 서버를 Azure에 복제할 때 발생할 수 있는 몇 가지 일반적인 문제와 특정 오류에 대해 설명합니다.

## <a name="step-1-monitor-process-server-health"></a>1단계: 프로세스 서버 상태 모니터링

사이트 복구는 [프로세스 서버를](vmware-physical-azure-config-process-server-overview.md#process-server) 사용하여 복제된 데이터를 수신 및 최적화하고 Azure로 보냅니다.

포털에서 프로세스 서버의 상태를 모니터링하여 프로세스가 제대로 연결되고 제대로 작동하는지, 프로세스 서버와 연결된 원본 컴퓨터에 대해 복제가 진행되고 있는지 확인하는 것이 좋습니다.

- 프로세스 서버 [모니터링에 대해 자세히 알아봅니다.](vmware-physical-azure-monitor-process-server.md)
- [모범 사례 검토](vmware-physical-azure-troubleshoot-process-server.md#best-practices-for-process-server-deployment)
- 프로세스 서버 상태 [문제를 해결합니다.](vmware-physical-azure-troubleshoot-process-server.md#check-process-server-health)

## <a name="step-2-troubleshoot-connectivity-and-replication-issues"></a>2단계: 연결 및 복제 문제 해결

초기 및 진행 중인 복제 오류는 원본 서버와 프로세스 서버 간의 연결 문제 또는 프로세스 서버와 Azure 간의 연결 문제로 인해 발생하는 경우가 많습니다.

이러한 문제를 해결하려면 [연결 및 복제 문제를 해결합니다.](vmware-physical-azure-troubleshoot-process-server.md#check-connectivity-and-replication)




## <a name="step-3-troubleshoot-source-machines-that-arent-available-for-replication"></a>3단계: 복제에 사용할 수 없는 소스 컴퓨터 문제 해결

Site Recovery를 사용하여 복제를 사용하도록 설정할 원본 컴퓨터를 선택하려고 하면 다음과 같은 이유 중 하나로 컴퓨터를 계속 사용하지 못할 수 있습니다.

* **UUID 인스턴스가 동일한 두 가상 컴퓨터**: vCenter 아래의 두 가상 컴퓨터가 동일한 인스턴스 UUID를 사용하는 경우 구성 서버에서 검색한 첫 번째 가상 시스템이 Azure Portal에 표시됩니다. 이 문제를 해결하려면 두 개의 가상 머신에 동일한 인스턴스 UUID가 없는지 확인합니다. 이 시나리오는 일반적으로 백업 VM이 활성화되어 검색 레코드에 기록되는 경우에 발생합니다. Azure [사이트 복구 VMware-to-Azure: 중복 또는 부실 항목을 정리하여](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx) 해결하는 방법을 참조하십시오.
* **잘못된 vCenter 사용자 자격 증명**: OVF 템플릿 또는 통합 설정을 사용하여 구성 서버를 설정할 때 올바른 vCenter 자격 증명을 추가해야 합니다. 설치 중에 추가한 자격 증명을 확인하려면 [자동 검색에 대한 자격 증명 수정](vmware-azure-manage-configuration-server.md#modify-credentials-for-automatic-discovery)을 참조하세요.
* **vCenter 부족 한 권한**: vCenter액세스에 제공된 사용 권한이 필요한 권한이 없는 경우 가상 컴퓨터를 검색하지 못할 수 있습니다. [자동 검색용 계정 준비](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery)에 설명된 사용 권한을 vCenter 사용자 계정에 추가해야 합니다.
* **Azure 사이트 복구 관리 서버**: 가상 시스템이 다음 역할 중 하나 이상에서 관리 서버로 사용되는 경우 구성 서버 /확장 프로세스 서버 / 마스터 대상 서버는 포털에서 가상 컴퓨터를 선택할 수 없습니다. 관리 서버는 복제할 수 없습니다.
* **Azure 사이트 복구 서비스를 통해 이미 보호/실패:** 가상 시스템이 이미 보호되거나 사이트 복구를 통해 실패한 경우 포털에서 보호를 위해 가상 컴퓨터를 선택할 수 없습니다. 포털에서 찾고 있는 가상 머신이 다른 사용자 또는 다른 구독을 통해 아직 보호되고 있지 않은지 확인합니다.
* **vCenter 연결되지 않음**: vCenter가 연결된 상태인지 확인합니다. 확인하려면 [Recovery Services 가격 증명 모음] > [Site Recovery 인프라] > [구성 서버]로 이동하고 > 해당 구성 서버를 클릭하면 > 연결된 서버의 세부 정보와 함께 블레이드가 오른쪽에 열립니다. VCenter가 연결되었는지 확인합니다. "연결되지 않음" 상태인 경우 문제를 해결한 다음 [포털에서 구성 서버를 새로 고칩니다.](vmware-azure-manage-configuration-server.md#refresh-configuration-server) 그러면 포털에 가상 머신이 나열됩니다.
* **ESXi 전원 끄기**: 가상 시스템이 있는 ESXi 호스트가 전원이 꺼진 상태인 경우 가상 시스템이 나열되지 않거나 Azure 포털에서 선택할 수 없습니다. ESXi 호스트 전원을 켜고 포털에서 [구성 서버를 새로 고칩니다](vmware-azure-manage-configuration-server.md#refresh-configuration-server). 그러면 포털에 가상 머신이 나열됩니다.
* **재부팅 보류 중**: 가상 컴퓨터에 재부팅 보류 중인 경우 Azure Portal에서 컴퓨터를 선택할 수 없습니다. 보류 중인 재부팅 활동을 완료하고 [구성 서버를 새로 고쳐야 합니다](vmware-azure-manage-configuration-server.md#refresh-configuration-server). 그러면 포털에 가상 머신이 나열됩니다.
* **IP를 찾을 수 없습니다**: 가상 시스템에 연결된 유효한 IP 주소가 없는 경우 Azure Portal에서 컴퓨터를 선택할 수 없습니다. 가상 머신에 유효한 IP 주소를 할당하고 [구성 서버를 새로 고칩니다](vmware-azure-manage-configuration-server.md#refresh-configuration-server). 그러면 포털에 가상 머신이 나열됩니다.

### <a name="troubleshoot-protected-virtual-machines-greyed-out-in-the-portal"></a>포털에서 회색으로 지정된 보호된 가상 시스템 문제 해결

Site Recovery에서 복제된 가상 머신은 시스템에 중복된 항목이 있으면 Azure Portal에서 사용할 수 없습니다. 부실 항목을 삭제하고 문제를 해결하는 방법을 알아보려면 [Azure 사이트 복구 VMware-to-Azure: 중복 항목 또는 오래된 항목을 정리하는 방법을](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx)참조하십시오.

## <a name="no-crash-consistent-recovery-point-available-for-the-vm-in-the-last-xxx-minutes"></a>마지막 'XXX' 분에서 VM에 사용할 수 있는 충돌 일관된 복구 지점 없음

가장 일반적인 문제 중 일부는 다음과 같습니다.

### <a name="initial-replication-issues-error-78169"></a>초기 복제 문제 [오류 78169]

위의 경우 연결, 대역폭 또는 시간 동기화 관련 문제가 없는지 확인합니다.

- 바이러스 백신 소프트웨어가 Azure 사이트 복구를 차단하지 않습니다. Azure 사이트 복구에 필요한 폴더 제외에 대해 [자세히](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) 알아봅니다.

### <a name="source-machines-with-high-churn-error-78188"></a>이탈이 높은 소스 컴퓨터 [오류 78188]

가능한 원인:
- 가상 시스템의 나열된 디스크에 있는 데이터 변경 률(바이트/초 쓰기)은 복제 대상 저장소 계정 유형에 대해 [지원되는 Azure 사이트 복구 지원 한도보다](site-recovery-vmware-deployment-planner-analyze-report.md#azure-site-recovery-limits) 큽니다.
- 업로드대기 중인 데이터의 양이 많기 때문에 이탈률이 급격히 상승합니다.

이 문제를 해결하려면
- 원본의 이탈률 요구 사항에 따라 대상 저장소 계정 유형(표준 또는 프리미엄)이 프로비전되었는지 확인합니다.
- 이미 프리미엄 관리 디스크(asrseeddisk 유형)로 복제중인 경우 디스크 크기가 사이트 복구 제한에 따라 관찰된 이탈률을 지원하는지 확인합니다. 필요한 경우 asrseeddisk의 크기를 늘릴 수 있습니다. 아래 단계를 수행하세요.
    - 영향을 받는 복제된 컴퓨터의 디스크 블레이드로 이동하여 복제디스크 이름을 복사합니다.
    - 이 복제본 관리 디스크로 이동
    - 개요 블레이드에 SAS URL이 생성되었다는 배너가 표시될 수 있습니다. 이 배너를 클릭하고 내보내기를 취소합니다. 배너가 표시되지 않으면 이 단계를 무시합니다.
    - SAS URL이 해지되는 즉시 관리디스크의 구성 블레이드로 이동하여 ASR이 소스 디스크에서 관찰된 이탈률을 지원하도록 크기를 늘립니다.
- 관찰된 변동이 일시적인 경우 보류 중인 데이터가 업로드될 때까지 몇 시간 동안 기다렸다가 복구 지점을 만듭니다.
- 디스크에 임시 로그, 테스트 데이터 등과 같은 중요하지 않은 데이터가 포함되어 있는 경우 이 데이터를 다른 곳으로 이동하거나 이 디스크를 복제에서 완전히 제외하십시오.
- 문제가 계속 지속되면 사이트 복구 [배포 플래너를](site-recovery-deployment-planner.md#overview) 사용하여 복제계획을 수립합니다.

### <a name="source-machines-with-no-heartbeat-error-78174"></a>하트비트가 없는 소스 컴퓨터 [오류 78174]

이 문제는 원본 컴퓨터의 Azure 사이트 복구 이동성 에이전트가 CS(구성 서버)와 통신하지 않는 경우에 발생합니다.

이 문제를 해결하려면 다음 단계를 사용하여 원본 VM에서 Config Server로의 네트워크 연결을 확인합니다.

1. 원본 컴퓨터가 실행 중인지 확인합니다.
2. 관리자 권한이 있는 계정을 사용하여 소스 컴퓨터에 로그인합니다.
3. 다음 서비스가 실행 중인지 확인하고 서비스를 다시 시작하지 않는 경우
   - Svagents(InMage Scout VX 에이전트)
   - InMage Scout Application 서비스
4. 원본 컴퓨터에서 위치의 로그에 오류 세부 정보를 검사합니다.

       C:\Program Files (X86)\Microsoft Azure Site Recovery\agent\svagents*log

### <a name="process-server-with-no-heartbeat-error-806"></a>하트비트가 없는 프로세스 서버[오류 806]
프로세스 서버(PS)의 하트비트가 없는 경우 다음을 확인하십시오.
1. PS VM이 실행 중입니다.
2. 오류 세부 정보는 PS의 다음 로그를 확인합니다.

       C:\ProgramData\ASR\home\svsystems\eventmanager*.log
       and
       C:\ProgramData\ASR\home\svsystems\monitor_protection*.log

### <a name="master-target-server-with-no-heartbeat-error-78022"></a>하트비트가 없는 마스터 대상 서버[오류 78022]

이 문제는 마스터 대상의 Azure 사이트 복구 이동성 에이전트가 구성 서버와 통신하지 않을 때 발생합니다.

이 문제를 해결하려면 다음 단계를 사용하여 서비스 상태를 확인합니다.

1. 마스터 대상 VM이 실행 되고 있는지 확인합니다.
2. 관리자 권한이 있는 계정을 사용하여 마스터 대상 VM에 로그인합니다.
    - 스에이전트 서비스가 실행 중인지 확인합니다. 실행 중인 경우 서비스를 다시 시작합니다.
    - 오류 세부 정보가 있는지 위치의 로그를 확인합니다.

          C:\Program Files (X86)\Microsoft Azure Site Recovery\agent\svagents*log
3. 구성 서버로 마스터 대상을 등록하려면 **폴더 %PROGRAMDATA%\ASR\Agent로**이동하여 명령 프롬프트에서 다음을 실행합니다.
   ```
   cmd
   cdpcli.exe --registermt

   net stop obengine

   net start obengine

   exit
   ```

## <a name="error-id-78144---no-app-consistent-recovery-point-available-for-the-vm-in-the-last-xxx-minutes"></a>오류 ID 78144 - 마지막 'XXX' 분에서 VM에 사용할 수 있는 앱 일관된 복구 지점이 없습니다.

VSS 설치 실패 동작을 처리하기 위해 이동성 에이전트 [9.23](vmware-physical-mobility-service-overview.md#mobility-service-agent-version-923-and-higher) & [9.27](site-recovery-whats-new.md#update-rollup-39) 버전에서 개선되었습니다. VSS 오류 해결에 대한 최상의 지침을 위해 최신 버전에 있는지 확인합니다.

가장 일반적인 문제 중 일부는 다음과 같습니다.

#### <a name="cause-1-known-issue-in-sql-server-20082008-r2"></a>원인 1: SQL 서버 2008/2008 R2에서 알려진 문제
**해결 방법** : SQL 서버 2008/2008 R2에 알려진 문제가 있습니다. [SQL Server 2008 R2를 호스팅하는 서버에 대해](https://support.microsoft.com/help/4504103/non-component-vss-backup-fails-for-server-hosting-sql-server-2008-r2) 이 KB 문서 Azure 사이트 복구 에이전트 또는 기타 구성 요소가 아닌 VSS 백업실패를 참조하십시오.

#### <a name="cause-2-azure-site-recovery-jobs-fail-on-servers-hosting-any-version-of-sql-server-instances-with-auto_close-dbs"></a>원인 2: Azure 사이트 복구 작업 AUTO_CLOSE DB를 사용 하 고 모든 버전의 SQL Server 인스턴스를 호스팅 하는 서버에서 실패 합니다.
**해결 방법** : KB [기사](https://support.microsoft.com/help/4504104/non-component-vss-backups-such-as-azure-site-recovery-jobs-fail-on-ser) 참조


#### <a name="cause-3-known-issue-in-sql-server-2016-and-2017"></a>원인 3: SQL Server 2016 및 2017에서 알려진 문제
**해결 방법** : KB [기사](https://support.microsoft.com/help/4493364/fix-error-occurs-when-you-back-up-a-virtual-machine-with-non-component) 참조


### <a name="more-causes-due-to-vss-related-issues"></a>VSS 관련 문제로 인한 더 많은 원인:

추가 문제 해결을 위해 소스 컴퓨터의 파일을 확인하여 오류에 대한 정확한 오류 코드를 얻습니다.

    C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\Application Data\ApplicationPolicyLogs\vacp.log

파일의 오류를 찾는 방법은 무엇입니까?
편집기에서 vacp.log 파일을 열어 문자열 "vacpError"를 검색합니다.

    Ex: vacpError:220#Following disks are in FilteringStopped state [\\.\PHYSICALDRIVE1=5, ]#220|^|224#FAILED: CheckWriterStatus().#2147754994|^|226#FAILED to revoke tags.FAILED: CheckWriterStatus().#2147754994|^|

위의 **예2147754994는** 아래와 같이 오류에 대해 알려주는 오류 코드입니다.

#### <a name="vss-writer-is-not-installed---error-2147221164"></a>VSS 라이터가 설치되지 않음 - 오류 2147221164

*해결 방법*: 응용 프로그램 일관성 태그를 생성하기 위해 Azure 사이트 복구는 VSS(Microsoft 볼륨 섀도우 복사 서비스)를 사용합니다. VSS 공급자를 설치하여 앱 일관성 스냅숏을 만듭니다. 이 VSS 공급자는 서비스로 설치됩니다. VSS 공급자 서비스가 설치되지 않은 경우 오류 ID 0x80040154 "클래스가 등록되지 않음"으로 응용 프로그램 일관성 스냅숏 생성에 실패합니다. </br>
[VSS 기록기 설치 문제 해결을 위한 문서](https://docs.microsoft.com/azure/site-recovery/vmware-azure-troubleshoot-push-install#vss-installation-failures) 참조

#### <a name="vss-writer-is-disabled---error-2147943458"></a>VSS 라이터를 사용할 수 없습니다 - 오류 2147943458

**해결 방법**: 응용 프로그램 일관성 태그를 생성하기 위해 Azure 사이트 복구는 VSS(Microsoft 볼륨 섀도우 복사 서비스)를 사용합니다. VSS 공급자를 설치하여 앱 일관성 스냅숏을 만듭니다. 이 VSS 공급자는 서비스로 설치됩니다. VSS 공급자 서비스를 사용하지 않도록 설정하면 응용 프로그램 일관성 스냅숏 생성에 오류 ID "지정된 서비스가 비활성화되어 시작할 수 없습니다(0x80070422)"라는 오류 ID로 실패합니다. </br>

- VSS를 사용하지 않도록 설정한 경우
    - VSS 공급자 서비스의 시작 유형이 **자동**으로 설정되어 있는지 확인합니다.
    - 다음 서비스를 다시 시작하십시오.
        - VSS 서비스
        - Azure Site Recovery VSS 공급자
        - VDS 서비스

####  <a name="vss-provider-not_registered---error-2147754756"></a>VSS 공급자 NOT_REGISTERED - 오류 2147754756

**해결 방법**: 응용 프로그램 일관성 태그를 생성하기 위해 Azure 사이트 복구는 VSS(Microsoft 볼륨 섀도우 복사 서비스)를 사용합니다.
Azure 사이트 복구 VSS 공급자 서비스가 설치되어 있는지 확인합니다. </br>

- 다음 명령을 사용하여 공급자 설치를 다시 시도하십시오.
- 기존 공급자 제거: C:\프로그램 파일 (x86)\Microsoft Azure 사이트 복구\에이전트\InMageVSSProvider_Uninstall.cmd
- 다시 설치: C:\프로그램 파일 (x86)\마이크로소프트 Azure 사이트 복구\에이전트\InMageVSSProvider_Install.cmd

VSS 공급자 서비스의 시작 유형이 **자동**으로 설정되어 있는지 확인합니다.
    - 다음 서비스를 다시 시작하십시오.
        - VSS 서비스
        - Azure Site Recovery VSS 공급자
        - VDS 서비스

## <a name="next-steps"></a>다음 단계

도움이 필요한 경우 [Azure Site Recovery 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)에 질문을 게시하세요. 활발히 유지되는 커뮤니티가 있으며 엔지니어 중 하나가 도움을 줄 수 있습니다.
