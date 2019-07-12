---
title: Azure Site Recovery를 사용한 VMware VM 및 물리적 서버와 Azure 간 재해 복구에 대한 복제 문제 해결 | Microsoft Docs
description: 이 문서에서는 Azure Site Recovery를 사용한 VMware VM 및 물리적 서버와 Azure 간 재해 복구 중에 일반적인 복제 문제를 해결하는 방법을 제공합니다.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 06/27/2019
ms.author: mayg
ms.openlocfilehash: ed04c21fc5f3aecb91483dbd1eb7ca5fbf47c3e9
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67805972"
---
# <a name="troubleshoot-replication-issues-for-vmware-vms-and-physical-servers"></a>VMware VM 및 실제 서버에 대한 복제 문제 해결

이 문서에서는 몇 가지 일반적인 문제 및 사용 하 여 Azure에 온-프레미스 VMware Vm 및 물리적 서버를 복제 하는 경우 발생할 수 있는 특정 오류를 설명 [Site Recovery](site-recovery-overview.md)합니다.

## <a name="step-1-monitor-process-server-health"></a>1단계: 프로세스 서버 상태 모니터

사이트 복구 사용 합니다 [프로세스 서버](vmware-physical-azure-config-process-server-overview.md#process-server) 및 수신 하 고, 복제 된 데이터를 최적화 하 고, Azure로 전송 하려면.

포털에서 연결 되어 및 프로세스 서버에 연결 된 원본 컴퓨터에 대 한 진행 되 고 제대로 작동 하 고 해당 복제 되도록 프로세스 서버 상태를 모니터링 하는 것이 좋습니다.

- [에 대 한 자세한](vmware-physical-azure-monitor-process-server.md) 프로세스 서버를 모니터링 합니다.
- [모범 사례 검토](vmware-physical-azure-troubleshoot-process-server.md#best-practices-for-process-server-deployment)
- [문제 해결](vmware-physical-azure-troubleshoot-process-server.md#check-process-server-health) 서버 상태를 처리 합니다.

## <a name="step-2-troubleshoot-connectivity-and-replication-issues"></a>2단계: 연결 및 복제 문제 해결

종종 초기 및 지속적인 복제 실패 원본 서버와 프로세스 서버 간 또는 프로세스 서버와 Azure 간의 연결 문제로 인해 발생 합니다. 

이러한 문제를 해결 하기 위해 [연결 및 복제 문제 해결](vmware-physical-azure-troubleshoot-process-server.md#check-connectivity-and-replication)합니다.




## <a name="step-3-troubleshoot-source-machines-that-arent-available-for-replication"></a>3단계: 원본 컴퓨터가 복제에 사용할 수 없는 문제 해결

Site Recovery를 사용하여 복제를 사용하도록 설정할 원본 컴퓨터를 선택하려고 하면 다음과 같은 이유 중 하나로 컴퓨터를 계속 사용하지 못할 수 있습니다.

* **인스턴스 UUID가 같은 두 개의 가상 머신**: vCenter에 인스턴스 UUID가 같은 두 개의 가상 머신이 있는 경우 구성 서버에서 첫 번째로 검색된 가상 머신이 Azure Portal에 표시됩니다. 이 문제를 해결하려면 두 개의 가상 머신에 동일한 인스턴스 UUID가 없는지 확인합니다. 이 시나리오는 일반적으로 인스턴스 백업 VM이 활성화 하 고가 검색 기록에 따르면에 로그인 하는 위치에 표시 됩니다. [Azure Site Recovery VMware-Azure: 중복되거나 오래된 항목을 정리하는 방법](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx)을 참조하세요.
* **잘못된 vCenter 사용자 자격 증명**: OVF 템플릿 또는 통합 설정을 사용하여 구성 서버를 설정하는 동안 올바른 vCenter 자격 증명을 추가했는지 확인합니다. 설치 중에 추가한 자격 증명을 확인하려면 [자동 검색에 대한 자격 증명 수정](vmware-azure-manage-configuration-server.md#modify-credentials-for-automatic-discovery)을 참조하세요.
* **vCenter 권한 부족**: vCenter에 액세스하기 위해 제공된 권한에 필요한 사용 권한이 없으면 가상 머신을 검색하지 못할 수 있습니다. [자동 검색용 계정 준비](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery)에 설명된 사용 권한을 vCenter 사용자 계정에 추가해야 합니다.
* **Azure Site Recovery 관리 서버**: 구성 서버/스케일 아웃 프로세스 서버/마스터 대상 서버 역할 중 하나 이상에서 가상 머신이 관리 서버로 사용되는 경우 포털에서 가상 머신을 선택할 수 없습니다. 관리 서버는 복제할 수 없습니다.
* **Azure Site Recovery 서비스를 통해 이미 보호/장애 조치(Failover)됨**: Site Recovery를 통해 가상 머신을 이미 보호 또는 장애 조치하는 경우 포털에서 보호하기 위해 가상 머신을 선택할 수 없습니다. 포털에서 찾고 있는 가상 머신이 다른 사용자 또는 다른 구독을 통해 아직 보호되고 있지 않은지 확인합니다.
* **연결되지 않은 vCenter** VCenter가 연결된 상태인지 확인합니다. 확인하려면 [Recovery Services 가격 증명 모음] > [Site Recovery 인프라] > [구성 서버]로 이동하고 > 해당 구성 서버를 클릭하면 > 연결된 서버의 세부 정보와 함께 블레이드가 오른쪽에 열립니다. VCenter가 연결되었는지 확인합니다. 인 경우 "연결 된" 상태에서 문제를 해결 한 다음 [구성 서버를 새로 고칠](vmware-azure-manage-configuration-server.md#refresh-configuration-server) 포털에서 합니다. 그러면 포털에 가상 머신이 나열됩니다.
* **ESXi 전원 꺼짐**: 가상 머신이 상주하는 ESXi 호스트의 전원이 꺼진 상태이면 가상 머신이 Azure Portal에 나열되지 않거나 선택할 수 없습니다. ESXi 호스트 전원을 켜고 포털에서 [구성 서버를 새로 고칩니다](vmware-azure-manage-configuration-server.md#refresh-configuration-server). 그러면 포털에 가상 머신이 나열됩니다.
* **보류 중인 재부팅**: 가상 머신에 보류 중인 재부팅이 있는 경우 Azure Portal에서 머신을 선택할 수 없습니다. 보류 중인 재부팅 활동을 완료하고 [구성 서버를 새로 고쳐야 합니다](vmware-azure-manage-configuration-server.md#refresh-configuration-server). 그러면 포털에 가상 머신이 나열됩니다.
* **IP 찾을 수 없음**: 가상 머신에 연결된 유효한 IP 주소가 없는 경우 Azure Portal에서 머신을 선택할 수 없습니다. 가상 머신에 유효한 IP 주소를 할당하고 [구성 서버를 새로 고칩니다](vmware-azure-manage-configuration-server.md#refresh-configuration-server). 그러면 포털에 가상 머신이 나열됩니다.

### <a name="troubleshoot-protected-virtual-machines-greyed-out-in-the-portal"></a>포털에서 회색으로 표시 하는 보호 된 가상 머신 문제 해결

Site Recovery에서 복제된 가상 머신은 시스템에 중복된 항목이 있으면 Azure Portal에서 사용할 수 없습니다. 오래된 항목을 삭제하고 이 문제를 해결하는 방법에 대한 자세한 내용은 [Azure Site Recovery VMware-Azure: 중복되거나 오래된 항목을 정리하는 방법](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx)을 참조하세요.

## <a name="common-errors-and-solutions"></a>일반적인 오류 및 해결 방법

### <a name="initial-replication-issues-error-78169"></a>초기 복제 문제 [error 78169]

있는지는 위 확인을 통해 없음 연결, 대역폭이 나 시간이 동기화 관련된 문제, 되어 있는지 확인:

- 바이러스 백신 소프트웨어 없음은 Azure Site Recovery를 차단 합니다. 에 대해 알아봅니다 [자세한](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) Azure Site Recovery에 필요한 폴더 제외 합니다.

### <a name="missing-app-consistent-recovery-points-error-78144"></a>누락 된 앱 일치 복구 지점의 [error 78144]

 볼륨 섀도 복사본 서비스 (VSS)를 사용 하 여 문제로 인해 발생합니다. 이 문제를 해결하려면: 
 
- Azure Site Recovery 에이전트의 설치 된 버전과 9.22.2 이상 인지 확인 합니다. 
- Windows 서비스에서 서비스로 서 VSS 공급자가 설치 되어 있는지 확인 하 고 또한 Azure Site Recovery VSS 공급자가 표시 되는지 확인 하려면 구성 요소 서비스 MMC를 확인 합니다.
- VSS 공급자 설치 되어 있지 않으면 참조를 [설치 오류 문제 해결 문서](vmware-azure-troubleshoot-push-install.md#vss-installation-failures)합니다.

- VSS를 사용 하지 않도록 설정 하는 경우
    - VSS 공급자 서비스의 시작 유형이 설정 되어 있는지 확인 **자동**합니다.
    - 다음 서비스를 다시 시작 합니다.
        - VSS 서비스
        - Azure Site Recovery VSS 공급자
        - VDS 서비스

- Exchange 또는 SQL 워크 로드를 실행 하는 경우 오류에 대 한 이러한 응용 프로그램 작성자의 로그를 확인 합니다. 다음 문서에 자주 오류 및 해결이 캡처됩니다.
    -  [SQL Server 데이터베이스의 자동 닫기 옵션이 TRUE로 설정 된](https://support.microsoft.com/help/4504104)
    - [다시 시도할 수 없는 오류를 throw 하는 SQL Server 2008 R2](https://support.microsoft.com/help/4504103)
    - [SQL Server 2016 및 2017 알려진된 문제](https://support.microsoft.com/help/4493364)
    - [Exchange 서버 2013 및 2016을 사용 하 여 일반적인 문제](https://support.microsoft.com/help/4037535)


### <a name="source-machines-with-high-churn-error-78188"></a>높은 변동 [error 78188]를 사용 하 여 원본 컴퓨터

가능한 원인:
- 나열 된 디스크가 가상 컴퓨터의 데이터 변경 률 (쓰기 바이트/초)은 보다 [Azure Site Recovery 지원 제한](site-recovery-vmware-deployment-planner-analyze-report.md#azure-site-recovery-limits) 복제 대상 저장소 계정 유형에 대 한 합니다.
- 변동률에 급증 하는 부분는 많은 양의 데이터 인해 보류 중인 업로드 합니다.

이 문제를 해결하려면
- 소스에서 이탈 비율 요구 사항에 따라 대상 저장소 계정 유형 (Standard 또는 Premium)은 프로 비전 되었는지 확인 합니다.
- 관찰 된 변동을 임시 인 경우 보류 중인 데이터 업로드 캐치 업 하 고 복구 지점을 만들 수는 몇 시간까지 기다립니다.
- 문제가 계속 지속 되는 경우 Site Recovery를 사용 하 여 [배포 플래너](site-recovery-deployment-planner.md#overview) 복제를 계획할 수 있습니다.

### <a name="source-machines-with-no-heartbeat-error-78174"></a>하트 비트 없음 [error 78174]를 사용 하 여 원본 컴퓨터

원본 컴퓨터에 Azure Site Recovery 모바일 에이전트를 구성 서버 (CS)와 통신 하지 하는 경우 발생 합니다.

문제를 해결 하려면 구성 서버에 원본 VM에서에서 네트워크 연결을 확인 하려면 다음 단계를 사용 합니다.

1. 원본 컴퓨터가 실행 되 고 있는지 확인 합니다.
2. 관리자 권한이 있는 계정을 사용 하 여 원본 컴퓨터에 로그인 합니다.
3. 다음 서비스가 실행 되 고 있는지 확인 하지 서비스를 다시 시작 하는 경우:
   - Svagents (InMage Scout VX Agent)
   - InMage Scout Application 서비스
4. 원본 컴퓨터의 오류 세부 정보에 대 한 위치에 로그를 검사 합니다.

       C:\Program Files (X86)\Microsoft Azure Site Recovery\agent\svagents*log
    
### <a name="process-server-with-no-heartbeat-error-806"></a>하트 비트 없음 [error 806]를 사용 하 여 프로세스 서버
프로세스 서버 (PS)에서 하트 비트가 없습니다 인 경우 다음을 확인 합니다.
1. PS VM이 작동 및 실행
2. 오류 세부 정보에 대 한 PS에서 다음 로그를 확인 합니다.

       C:\ProgramData\ASR\home\svsystems\eventmanager*.log
       and
       C:\ProgramData\ASR\home\svsystems\monitor_protection*.log

### <a name="master-target-server-with-no-heartbeat-error-78022"></a>하트 비트 없음 [error 78022]를 사용 하 여 마스터 대상 서버

마스터 대상에 Azure Site Recovery 모바일 에이전트 구성 서버와 통신 하지 않는 경우 발생 합니다.

문제를 해결 하려면 서비스 상태를 확인 하려면 다음 단계를 사용 합니다.

1. 마스터 대상 VM 실행 되 고 있는지 확인 합니다.
2. 관리자 권한이 있는 계정을 사용 하 여 마스터 대상 VM에 로그인 합니다.
    - Svagents 서비스가 실행 되 고 있는지 확인 합니다. 실행 중인 서비스를 다시 시작
    - 오류 세부 정보에 대 한 위치에 로그를 확인 합니다.
        
          C:\Program Files (X86)\Microsoft Azure Site Recovery\agent\svagents*log

## <a name="error-id-78144---no-app-consistent-recovery-point-available-for-the-vm-in-the-last-xxx-minutes"></a>오류 ID 78144-지난 'XXX' 분 동안에서 VM에 대 한 사용 가능한 앱 일치 복구 지점 없음

가장 일반적인 문제 중 일부는 다음과 같습니다.

#### <a name="cause-1-known-issue-in-sql-server-20082008-r2"></a>원인 1: 에서 알려진 문제 SQL server 2008/2008 R2 
**해결 방법** : 2008/2008 R2 SQL server 사용 하 여 알려진된 문제가 없습니다. 이 기술 자료 문서를 참조 하십시오 [Azure Site Recovery Agent 또는 다른 구성 요소가 아닌 VSS 백업 SQL Server 2008 R2를 호스팅하는 서버에 대 한 실패](https://support.microsoft.com/help/4504103/non-component-vss-backup-fails-for-server-hosting-sql-server-2008-r2)

#### <a name="cause-2-azure-site-recovery-jobs-fail-on-servers-hosting-any-version-of-sql-server-instances-with-autoclose-dbs"></a>원인 2: 모든 버전의 AUTO_CLOSE Db를 사용 하 여 SQL Server 인스턴스를 호스팅하는 서버에서 azure Site Recovery 작업 실패 
**해결 방법** : 기술 자료를 참조 하세요. [문서](https://support.microsoft.com/help/4504104/non-component-vss-backups-such-as-azure-site-recovery-jobs-fail-on-ser) 


#### <a name="cause-3-known-issue-in-sql-server-2016-and-2017"></a>원인 3: SQL Server 2016 및 2017 알려진된 문제
**해결 방법** : 기술 자료를 참조 하세요. [문서](https://support.microsoft.com/help/4493364/fix-error-occurs-when-you-back-up-a-virtual-machine-with-non-component) 


### <a name="more-causes-due-to-vss-related-issues"></a>VSS 인해 자세한 원인은 관련 문제:

추가로 문제를 해결 하려면 실패에 대 한 정확한 오류 코드를 가져올 원본 컴퓨터의 파일을 확인 합니다.
    
    C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\Application Data\ApplicationPolicyLogs\vacp.log

파일에서 오류를 찾으려면 어떻게 하나요?
편집기에서 vacp.log 파일을 열어 "vacpError" 문자열을 검색 합니다.
        
    Ex: vacpError:220#Following disks are in FilteringStopped state [\\.\PHYSICALDRIVE1=5, ]#220|^|224#FAILED: CheckWriterStatus().#2147754994|^|226#FAILED to revoke tags.FAILED: CheckWriterStatus().#2147754994|^|

위의 예에서 **2147754994** 는 아래와 같이 오류에 대 한 알려 주는 오류 코드

#### <a name="vss-writer-is-not-installed---error-2147221164"></a>VSS 기록기가 설치 되지 않음-오류 2147221164 

*해결 방법*: Azure Site Recovery는 응용 프로그램 일관성 태그를 생성 하려면 Microsoft 볼륨 섀도 복사본 서비스 (VSS)를 사용 합니다. 응용 프로그램 일관성 스냅숏을 해당 작업에 대 한 VSS 공급자를 설치 합니다. 이 VSS 공급자 서비스로 설치 됩니다. VSS 공급자를 설치 되지 않은 경우 응용 프로그램 일관성 스냅숏 만들기 실패 0x80040154 오류 id를 사용 하 여 "클래스가 등록 되지 않았습니다." 합니다. </br>
참조 [VSS 기록기 설치 문제 해결에 대 한 문서](https://docs.microsoft.com/azure/site-recovery/vmware-azure-troubleshoot-push-install#vss-installation-failures) 

#### <a name="vss-writer-is-disabled---error-2147943458"></a>VSS 기록기를 사용 하는 사용 안 함-오류 2147943458

**해결 방법**: Azure Site Recovery는 응용 프로그램 일관성 태그를 생성 하려면 Microsoft 볼륨 섀도 복사본 서비스 (VSS)를 사용 합니다. 응용 프로그램 일관성 스냅숏을 해당 작업에 대 한 VSS 공급자를 설치 합니다. 이 VSS 공급자 서비스로 설치 됩니다. VSS 공급자 서비스를 사용 하지 않도록 설정 하는 경우 "지정한 서비스 비활성화 되 고 started(0x80070422) 일 수 없습니다." 오류 id를 사용 하 여 응용 프로그램 일관성 스냅숏 만들기 실패 합니다. </br>

- VSS를 사용 하지 않도록 설정 하는 경우
    - VSS 공급자 서비스의 시작 유형이 설정 되어 있는지 확인 **자동**합니다.
    - 다음 서비스를 다시 시작 합니다.
        - VSS 서비스
        - Azure Site Recovery VSS 공급자
        - VDS 서비스

####  <a name="vss-provider-notregistered---error-2147754756"></a>VSS 공급자 NOT_REGISTERED-2147754756 오류

**해결 방법**: Azure Site Recovery는 응용 프로그램 일관성 태그를 생성 하려면 Microsoft 볼륨 섀도 복사본 서비스 (VSS)를 사용 합니다. Azure Site Recovery VSS 공급자를 설치 하는 경우를 확인 합니다. </br>

- 다음 명령을 사용 하 여 공급자 설치를 다시 시도 합니다.
- 기존 공급자를 제거 합니다. C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Uninstall.cmd
- 다시 설치 합니다. C:\Program 파일 (x86) \Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd
 
VSS 공급자 서비스의 시작 유형이 설정 되어 있는지 확인 **자동**합니다.
    - 다음 서비스를 다시 시작 합니다.
        - VSS 서비스
        - Azure Site Recovery VSS 공급자
        - VDS 서비스

## <a name="next-steps"></a>다음 단계

도움이 필요한 경우 [Azure Site Recovery 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)에 질문을 게시하세요. 활발히 유지되는 커뮤니티가 있으며 엔지니어 중 하나가 도움을 줄 수 있습니다.
