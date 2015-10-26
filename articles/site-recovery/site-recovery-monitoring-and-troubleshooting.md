<properties
	pageTitle="VMM 및 Hyper-v 사이트 보호에 대한 모니터링 및 문제 해결 가이드" 
	description="Azure Site Recovery는 온-프레미스 서버에 있는 가상 컴퓨터와 Azure 또는 보조 데이터센터 간의 복제, 장애 조치(Failover) 및 복구를 조정합니다. 이 문서를 사용하여 VMM 또는 Hyper-V 사이트 보호를 모니터링하고 문제를 해결합니다." 
	services="site-recovery" 
	documentationCenter="" 
	authors="anbacker" 
	manager="mkjain" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery" 
	ms.date="09/01/2015" 
	ms.author="anbacker"/>
	
# VMware, VMM, Hyper-V 및 물리적 사이트를 위한 보호 모니터링 및 문제 해결

이 모니터링 및 문제 해결 가이드를 사용하면 복제 상태를 추적하고 Azure 사이트 복구에 대 한 문제해결 기술에 대해 배울 수 있습니다.

## 구성 요소 이해

### 온-프레미스와 Azure 간 복제를 위한 VMware/물리적 사이트 배포
온-프레미스 VMware/물리적 컴퓨터 간에 DR을 설정하려면 구성 서버, 마스터 대상 및 프로세스 서버를 구성해야 합니다. 원본 서버에 대한 보호를 사용하도록 설정하는 동안 Azure Site Recovery는 모바일 서비스를 설치합니다. 원본 서버가 Azure에 장애 조치되면 온-프레미스가 중단되어, 고객들은 Azure 및 마스터 대상 서버 온-프레미스에서 프로세스 서버를 다시 설정하여 원본 서버를 다시 보호하고 온-프레미스를 다시 빌드합니다.

![온-프레미스와 Azure 간 복제를 위한 VMware/물리적 사이트 배포](media/site-recovery-monitoring-and-troubleshooting/image18.png)

### 온-프레미스 사이트 간 복제를 위한 VMM 사이트 배포

두 개의 온-프레미스 위치 간 DR 설정의 일부로, Azure 사이트 복구 공급자를 다운로드하고 VMM 서버에 설치해야 합니다. 공급자는 Azure 포털에서 트리거된 모든 작업이 보호 사용, 장애 조치의 일부로서 기본측 가상 컴퓨터의 종료와 같은 온-프레미스 작업으로 변환되도록 보장하기 위해 인터넷 연결이 필요합니다.

![온-프레미스 사이트 간 복제를 위한 VMM 사이트 배포](media/site-recovery-monitoring-and-troubleshooting/image1.png)

### 온-프레미스와 Azure 간 복제를 위한 VMM 사이트 배포

온-프레미스 및 Azure 간 DR 설정의 일부로 각 Hyper-v 호스트에 설치해야 하는 Azure 복구 서비스 에이전트와 함께 Azure 사이트 복구 공급자를 다운로드하여 VMM 서버에 설치해야 합니다. 자세한 내용은 [Azure 보호에 대한 사이트 이해](./site-recovery-understanding-site-to-azure-protection.md)를 참조하세요.

![온-프레미스와 Azure 간 복제를 위한 VMM 사이트 배포](media/site-recovery-monitoring-and-troubleshooting/image2.png)

### 온-프레미스와 Azure 간 복제를 위한 Hyper-V 사이트 배포

이것은 VMM 배포와 동일합니다. 서로 다른 공급자와 에이전트만 Hyper-v 호스트 자체에 설치됩니다. 자세한 내용은 [Azure 보호에 대한 사이트 이해](./site-recovery-understanding-site-to-azure-protection.md)를 참조하세요.

## 구성, 보호 및 복구 작업 모니터링

ASR에서 모든 작업이 감사되며 "JOBS(작업)" 탭 아래에서 추적됩니다. 구성, 보호 또는 복구 오류가 발생할 경우 작업 탭으로 이동한 다음 오류가 있는지 확인합니다.

![구성, 보호 및 복구 작업 모니터링](media/site-recovery-monitoring-and-troubleshooting/image3.png)

작업 보기에서 오류를 찾으면 해당 작업을 선택하고 해당 작업에 대한 오류 세부 정보를 클릭합니다.

![구성, 보호 및 복구 작업 모니터링](media/site-recovery-monitoring-and-troubleshooting/image4.png)

오류 세부 정보는 해당 문제에 대한 가능한 원인 및 권장 사항을 식별해줍니다.

![구성, 보호 및 복구 작업 모니터링](media/site-recovery-monitoring-and-troubleshooting/image5.png)

위의 경우 보호 구성의 실패 원인이 되는 다른 작업이 진행 중인 것으로 보입니다. 이 문제를 권장 사항에 따라 해결한 후 다시 시작을 클릭하여 작업을 다시 시작합니다.

![구성, 보호 및 복구 작업 모니터링](media/site-recovery-monitoring-and-troubleshooting/image6.png)

다시 시작 옵션은 모든 작업에 사용할 수 있는 것은 아닙니다. 다시 시작 옵션이 없는 경우에는 해당 개체로 돌아가서 다시 한 번 작업을 수행하세요. 모든 작업은 진행 중일 때도 언제든지 취소 단추를 사용하여 취소할 수 있습니다.

![구성, 보호 및 복구 작업 모니터링](media/site-recovery-monitoring-and-troubleshooting/image7.png)

## 가상 컴퓨터에 대한 복제 상태 모니터링

ASR는 보호되는 각 엔터티에 대한 Azure 포털을 통해 중앙 및 원격 모니터링을 제공합니다. 보호된 항목으로 이동한 후 VMM 클라우드 또는 보호 그룹을 선택합니다. VMM 클라우드 탭은 VMM 기반 배포 전용이며 모든 다른 시나리오는 보호 그룹 탭 아래에 보호된 엔터티를 가지고 있습니다.

![가상 컴퓨터에 대한 복제 상태 모니터링](media/site-recovery-monitoring-and-troubleshooting/image8.png)

그 다음 각 클라우드 또는 보호 그룹 아래에서 보호된 엔터티를 선택합니다. 보호된 엔터티를 선택하면 허용되는 모든 작업이 아래쪽 창에 표시됩니다.

![가상 컴퓨터에 대한 복제 상태 모니터링](media/site-recovery-monitoring-and-troubleshooting/image9.png)

위에서처럼 가상 컴퓨터의 상태가 중요일 경우 경우에는 아래쪽에 있는 오류 세부 정보 단추를 클릭하여 오류를 볼 수 있습니다. "가능한 원인"을 기반으로 하며 "권장 사항"에서 언급된 문제를 해결합니다.

![가상 컴퓨터에 대한 복제 상태 모니터링](media/site-recovery-monitoring-and-troubleshooting/image10.png)

![가상 컴퓨터에 대한 복제 상태 모니터링](media/site-recovery-monitoring-and-troubleshooting/image11.png)

참고: 진행 중이거나 실패한 활성 작업이 있는 경우에는 앞서 언급한 대로 작업 보기로 이동하여 작업별 오류를 확인하세요.

## 온-프레미스 Hyper-V 문제 해결

온-프레미스 Hyper-V 관리자 콘솔에 연결하고 가상 컴퓨터를 선택하고 복제 상태를 확인합니다.

![온-프레미스 Hyper-V 문제 해결](media/site-recovery-monitoring-and-troubleshooting/image12.png)

이 경우 *복제 상태*가 중요로 표시됩니다. 자세한 내용은 *복제 상태 보기*를 참조하세요.

![온-프레미스 Hyper-V 문제 해결](media/site-recovery-monitoring-and-troubleshooting/image13.png)

가상 컴퓨터에 대한 복제가 일시 중지하는 경우 마우스 오른쪽 단추로 클릭하여 *복제*->*복제 다시 시작*을 선택합니다. ![온-프레미스 Hyper-V 문제 해결](media/site-recovery-monitoring-and-troubleshooting/image19.png)

가상 컴퓨터가 ASR을 통해 구성된 새 Hyper-V 호스트(클러스터 내 또는 독립 실행형 컴퓨터)를 마이그레이션하는 경우 가상 컴퓨터에 대한 복제는 영향을 받지 않습니다. 새 Hyper-v 호스트가 모든 요구를 충족하고 ASR을 사용하여 구성되었는지 확인합니다.

### 이벤트 로그

| 이벤트 원본 | 세부 정보 |
|-------------------------	|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------	|
| **응용 프로그램 및 서비스 로그/Microsoft/VirtualMachineManager/Server/Admin**(VMM 서버) | 다양한 VMM 문제를 해결하는 데 유용한 로깅을 제공합니다. |
| **응용 프로그램 및 서비스 로그/MicrosoftAzureRecoveryServices/Replication**(Hyper-V 호스트) | 다양한 Microsoft Azure 복구 서비스 에이전트 문제를 해결하는 데 유용한 로깅을 제공합니다. <br/> ![Hyper-V 호스트에 대한 이벤트 소스](media/site-recovery-monitoring-and-troubleshooting/eventviewer03.png) |
| **응용 프로그램 및 서비스 로그/Microsoft/Azure Site Recovery/Provider/Operational**(Hyper-V 호스트) | 다양한 Microsoft Azure Site Recovery 서비스 문제를 해결하는 데 유용한 로깅을 제공합니다. <br/> ![Hyper-V 호스트에 대한 이벤트 소스](media/site-recovery-monitoring-and-troubleshooting/eventviewer02.png) |
| **응용 프로그램 및 서비스 로그/Microsoft/Windows/Hyper-V-VMMS/Admin**(Hyper-V 호스트) | 다양한 Hyper-V 가상 컴퓨터 관리 문제를 해결하는 데 유용한 로깅을 제공합니다. <br/> ![Hyper-V 호스트에 대한 이벤트 소스](media/site-recovery-monitoring-and-troubleshooting/eventviewer01.png) |


### Hyper-V 복제 로깅 옵션

Hyper-V 복제본에 관련된 모든 이벤트는 **응용 프로그램 및 서비스 로그\\Microsoft\\Windows** 아래에 있는 Hyper-V-VMMS\\Admin에 기록됩니다. 또한, Hyper-V-VMMS에 대해 분석 로그를 사용할 수 있습니다. 이 로그를 사용하려면 먼저 이벤트 뷰어에서 분석 및 디버그 로그를 볼 수 있게 만드세요. 이벤트 뷰어를 연 다음 **보기 메뉴**에서 **분석 및 디버그 로그 표시**를 클릭합니다.

![온-프레미스 Hyper-V 문제 해결](media/site-recovery-monitoring-and-troubleshooting/image14.png)

분석 로그가 Hyper-V-VMMS 아래에 표시됩니다.

![온-프레미스 Hyper-V 문제 해결](media/site-recovery-monitoring-and-troubleshooting/image15.png)

**작업** 창에서 **로그 사용**을 클릭합니다. 그러면 **성능 모니터**에서 **데이터 수집기 집합** 아래에 있는 이벤트 추적 세션으로 나타납니다.

![온-프레미스 Hyper-V 문제 해결](media/site-recovery-monitoring-and-troubleshooting/image16.png)

수집된 정보를 보려면 먼저 로그를 사용하지 않도록 설정하여 추적 세션을 중지하고 로그를 저장한 다음 다시 이벤트 뷰어에서 열거나, 다른 도구를 사용하여 원하는 대로 변환합니다.



## Microsoft 지원을 위한 연락

### 로그 수집

VMM 사이트 보호에 대해서는 [지원 진단 플랫폼(SDP) 도구를 사용한 ASR 로그 수집](http://social.technet.microsoft.com/wiki/contents/articles/28198.asr-data-collection-and-analysis-using-the-vmm-support-diagnostics-platform-sdp-tool.aspx)을 참조하여 필요한 로그를 수집합니다.

Hyper-V 사이트 보호의 경우에는 [도구](https://dcupload.microsoft.com/tools/win7files/DIAG_ASRHyperV_global.DiagCab)를 다운로드하고 Hyper-V 호스트에서 실행하여 로그를 수집합니다.

VMware/실제 시나리오의 경우에는 [VMware 및 실제 사이트 보호를 위한 Azure 사이트 복구 로그 수집](http://social.technet.microsoft.com/wiki/contents/articles/30677.azure-site-recovery-log-collection-for-vmware-and-physical-site-protection.aspx)을 참조하여 필요한 로그를 수집합니다.

도구는 **%LocalAppData%\\ElevatedDiagnostics**에서 임의로 이름이 지정된 하위 폴더 아래에 로그를 로컬로 수집합니다.

![Hyper-V 사이트 보호에서 표시된 샘플 단계입니다.](media/site-recovery-monitoring-and-troubleshooting/animate01.gif)

### 지원 티켓 열기

ASR에 대한 지원 티켓을 모으려면 <http://aka.ms/getazuresupport> URL을 사용하여 Azure 지원에 연락하세요.

## 기술 자료 문서

-   [장애 조치 또는 Azure로 마이그레이션된 보호된 가상 컴퓨터에 대한 드라이브 문자를 유지하는 방법](http://support.microsoft.com/kb/3031135)
-   [Azure 보호 네트워크 대역폭 사용에 대한 온-프레미스 관리 방법](https://support.microsoft.com/kb/3056159)
-   [ASR: 가상 컴퓨터에 대한 보호를 사용하도록 설정하려고 할 때 "클러스터 리소스를 찾을 수 없습니다." 오류](http://support.microsoft.com/kb/3010979)
-   [Hyper-V 복제본 가이드 이해 및 문제 해결](http://www.microsoft.com/en-in/download/details.aspx?id=29016) 

## 일반적 ARS 오류 및 해결 방법

다음은 흔히 발생할 수 있는 오류 및 해결 방법입니다. 각각의 오류는 개별 WIKI 페이지에 설명되어 있습니다.

### 일반
-   <span style="color:green;">신규</span> [작업이 실패하고 "작업이 진행 중입니다." 오류가 표시됩니다. 오류 505, 514, 532](http://social.technet.microsoft.com/wiki/contents/articles/32190.azure-site-recovery-jobs-failing-with-error-an-operation-is-in-progress-error-505-514-532.aspx)
-   <span style="color:green;">신규</span> [작업이 실패하고 "서버가 인터넷에 연결되어 있지 않습니다." 오류가 표시됩니다. 오류 25018](http://social.technet.microsoft.com/wiki/contents/articles/32192.azure-site-recovery-jobs-failing-with-error-server-isn-t-connected-to-the-internet-error-25018.aspx)

### 설정
-   [내부 오류로 인해 VMM 서버를 등록할 수 없습니다. 오류에 대한 자세한 내용은 Site Recovery 포털에서 작업 보기를 참조하세요. 다시 설정을 실행하여 서버를 등록합니다.](http://social.technet.microsoft.com/wiki/contents/articles/25570.the-vmm-server-cannot-be-registered-due-to-an-internal-error-please-refer-to-the-jobs-view-in-the-site-recovery-portal-for-more-details-on-the-error-run-setup-again-to-register-the-server.aspx)
-   [Hyper-V 복구 관리자 자격 증명 모음에 연결할 수 없습니다. 프록시 설정을 확인하거나 나중에 다시 시도하세요.](http://social.technet.microsoft.com/wiki/contents/articles/25571.a-connection-cant-be-established-to-the-hyper-v-recovery-manager-vault-verify-the-proxy-settings-or-try-again-later.aspx)

### 구성
-   [보호 그룹을 만들 수 없습니다. 서버 목록을 검색하는 동안 오류가 발생했습니다.](http://blogs.technet.com/b/somaning/archive/2015/08/12/unable-to-create-the-protection-group-in-azure-site-recovery-portal.aspx)
-   [Hyper-V 호스트 클러스터에는 하나 이상의 정적 네트워크 어댑터가 있습니다. 그렇지 않을 경우 연결된 어댑터 중 DHCP를 사용하도록 구성된 어댑터가 없는 것입니다.](http://social.technet.microsoft.com/wiki/contents/articles/25498.hyper-v-host-cluster-contains-at-least-one-static-network-adapter-or-no-connected-adapters-are-configured-to-use-dhcp.aspx)
-   [VMM에는 작업을 완료할 수 있는 권한이 없습니다.](http://social.technet.microsoft.com/wiki/contents/articles/31110.vmm-does-not-have-permissions-to-complete-an-action.aspx)
-   [보호를 구성하는 동안 구독 내에서 저장소 계정을 선택할 수 없습니다.](http://social.technet.microsoft.com/wiki/contents/articles/32027.can-t-select-the-storage-account-within-the-subscription-while-configuring-protection.aspx)

### 보호
- <span style="color:green;">신규</span> [보호를 사용하도록 설정하지 못했으며 "가상 컴퓨터에 대해 보호를 구성할 수 없습니다." 오류가 표시됩니다. 오류 60007, 40003](http://social.technet.microsoft.com/wiki/contents/articles/32194.azure-site-recovery-enable-protection-failing-with-error-protection-couldn-t-be-configured-for-the-virtual-machine-error-60007-40003.aspx)
- <span style="color:green;">신규</span> [보호를 사용하도록 설정하지 못했으며 "가상 컴퓨터에 대해 보호를 사용하도록 설정할 수 없습니다." 오류가 표시됩니다. 오류 70094](http://social.technet.microsoft.com/wiki/contents/articles/32195.azure-site-recovery-enable-protection-failing-with-error-protection-couldn-t-be-enabled-for-the-virtual-machine-error-70094.aspx)
- <span style="color:green;">신규</span> [실시간 마이그레이션 오류 23848 - 가상 컴퓨터가 실시간 유형을 사용하여 이동됩니다. 가상 컴퓨터의 복구 보호 상태를 손상시킬 수 있습니다.](http://social.technet.microsoft.com/wiki/contents/articles/32021.live-migration-error-23848-the-virtual-machine-is-going-to-be-moved-using-type-live-this-could-break-the-recovery-protection-status-of-the-virtual-machine.aspx) 
- [호스트 컴퓨터에 에이전트가 설치되어 있지 않아 보호를 사용하도록 설정하지 못했습니다.](http://social.technet.microsoft.com/wiki/contents/articles/31105.enable-protection-failed-since-agent-not-installed-on-host-machine.aspx)
- [계산 리소스가 부족하여 복제 가상 컴퓨터의 적절한 호스트를 찾을 수 없습니다.](http://social.technet.microsoft.com/wiki/contents/articles/25501.a-suitable-host-for-the-replica-virtual-machine-can-t-be-found-due-to-low-compute-resources.aspx)
- [논리 네트워크가 연결되어 있지 않아 복제 가상 컴퓨터의 적절한 호스트를 찾을 수 없습니다.](http://social.technet.microsoft.com/wiki/contents/articles/25502.a-suitable-host-for-the-replica-virtual-machine-can-t-be-found-due-to-no-logical-network-attached.aspx)
- [복제 호스트 컴퓨터에 연결할 수 없습니다. 연결하지 못했습니다.](http://social.technet.microsoft.com/wiki/contents/articles/31106.cannot-connect-to-the-replica-host-machine-connection-could-not-be-established.aspx)


### 복구
- VMM에서 호스트 작업을 완료할 수 없습니다.
    -   [가상 컴퓨터에 대해 선택된 복제 지점으로 장애 조치(Failover): 일반 액세스 거부 오류.](http://social.technet.microsoft.com/wiki/contents/articles/25504.fail-over-to-the-selected-recovery-point-for-virtual-machine-general-access-denied-error.aspx)
    -   [Hyper-V가 가상 컴퓨터에 대해 선택된 복구 지점으로 장애 조치(failover)하지 못했습니다. 작업이 중단되었습니다. 최근 복구 지점을 사용하세요. (0x80004004)](http://social.technet.microsoft.com/wiki/contents/articles/25503.hyper-v-failed-to-fail-over-to-the-selected-recovery-point-for-virtual-machine-operation-aborted-try-a-more-recent-recovery-point-0x80004004.aspx)
    -   서버와 연결할 수 없습니다.(0x00002EFD)
        -   [Hyper-V가 가상 컴퓨터에 대한 역방향 복제를 사용하도록 설정하지 못했습니다.](http://social.technet.microsoft.com/wiki/contents/articles/25505.a-connection-with-the-server-could-not-be-established-0x00002efd-hyper-v-failed-to-enable-reverse-replication-for-virtual-machine.aspx)
        -   [Hyper-V가 가상 컴퓨터에 대한 복제를 사용하도록 설정하지 못했습니다.](http://social.technet.microsoft.com/wiki/contents/articles/25506.a-connection-with-the-server-could-not-be-established-0x00002efd-hyper-v-failed-to-enable-replication-for-virtual-machine-virtual-machine.aspx)
    -   [가상 컴퓨터에 장애 조치를 커밋하지 못했습니다.](http://social.technet.microsoft.com/wiki/contents/articles/25508.could-not-commit-failover-for-virtual-machine.aspx)
-   [복구 계획에 계획된 장애 조치를 실행할 수 없는 가상 컴퓨터가 포함되어 있습니다.](http://social.technet.microsoft.com/wiki/contents/articles/25509.the-recovery-plan-contains-virtual-machines-which-are-not-ready-for-planned-failover.aspx)
-   [가상 컴퓨터에 계획된 장애 조치(Failover)를 실행할 수 없습니다.](http://social.technet.microsoft.com/wiki/contents/articles/25507.the-virtual-machine-isn-t-ready-for-planned-failover.aspx)
-   [가상 컴퓨터가 실행되고 있지 않으며 전원이 꺼져 있습니다.](http://social.technet.microsoft.com/wiki/contents/articles/25510.virtual-machine-is-not-running-and-is-not-powered-off.aspx)
-   [가상 컴퓨터에 대역 외 작업이 발생하고 장애 조치(failover)를 커밋하지 못했습니다.](http://social.technet.microsoft.com/wiki/contents/articles/25507.the-virtual-machine-isn-t-ready-for-planned-failover.aspx)
-   테스트 장애 조치(Failover)
    -   [테스트 장애 조치(Failover)가 진행 중이므로 장애 조치(Failover)를 시작할 수 없습니다.](http://social.technet.microsoft.com/wiki/contents/articles/31111.failover-could-not-be-initiated-since-test-failover-is-in-progress.aspx)

### 구성 서버, 프로세스 서버, 마스터 대상
CS(구성 서버), PS(프로세스 서버), MT(마스터 대상) - [PS/CS가 VM으로 호스트된 ESXi 호스트가 실패하고 퍼플 스크린이 표시되었습니다.](http://social.technet.microsoft.com/wiki/contents/articles/31107.vmware-esxi-host-experiences-a-purple-screen-of-death.aspx)

### 장애 조치(Failover) 후 원격 데스크톱 문제 해결
-   많은 고객이 Azure에서 장애 조치(Failover)된 VM에 연결하는 데 문제에 직면합니다. [문제 해결 문서를 사용하여 VM에 RDP를 연결합니다.](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)

<!---HONumber=Oct15_HO3-->