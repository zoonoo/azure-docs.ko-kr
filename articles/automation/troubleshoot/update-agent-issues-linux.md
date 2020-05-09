---
title: Azure Automation 업데이트 관리에서 Linux 업데이트 에이전트 문제 해결
description: 업데이트 관리 솔루션을 사용 하 여 Linux Windows 업데이트 에이전트 문제를 해결 하 고 문제를 해결 하는 방법을 알아봅니다.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 12/03/2019
ms.topic: conceptual
ms.service: automation
ms.subservice: update-management
manager: carmonm
ms.openlocfilehash: 1f9c8d449fb060d5b1a5f810f9e387057eac3252
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82927975"
---
# <a name="troubleshoot-linux-update-agent-issues"></a>Linux 업데이트 에이전트 문제 해결

업데이트 관리에서 컴퓨터가 준비 (정상) 상태로 표시 되지 않는 여러 가지 이유가 있을 수 있습니다. Linux Hybrid Runbook Worker 에이전트의 상태를 확인 하 여 근본적인 문제를 확인할 수 있습니다. 다음은 컴퓨터에 대 한 세 가지 준비 상태입니다.

* 준비: Hybrid Runbook Worker 배포 되었으며 마지막으로 1 시간 이내에 표시 되었습니다.
* 연결 끊김: Hybrid Runbook Worker 배포 되었으며 마지막으로 1 시간 전에 표시 되었습니다.
* 구성 되지 않음: Hybrid Runbook Worker을 찾을 수 없거나 등록을 완료 하지 못했습니다.

> [!NOTE]
> Azure Portal 표시 되는 내용과 컴퓨터의 현재 상태 사이에 약간의 지연이 있을 수 있습니다.

이 문서에서는 [오프 라인 시나리오](#troubleshoot-offline)에서 Azure Portal 및 비 azure 컴퓨터에서 azure 컴퓨터에 대 한 문제 해결사를 실행 하는 방법을 설명 합니다. 

> [!NOTE]
> 문제 해결사 스크립트는 현재 프록시 서버를 통해 트래픽을 라우팅 하지 않습니다 (구성 된 경우).

## <a name="start-the-troubleshooter"></a>문제 해결사 시작

Azure 컴퓨터의 경우 포털의 **에이전트 준비 상태 업데이트** 열에서 **문제 해결** 링크를 선택 하 여 업데이트 에이전트 문제 해결 페이지를 엽니다. 비 Azure 컴퓨터의 경우 링크를 통해이 문서를 볼 수 있습니다. 비 Azure 컴퓨터의 문제를 해결 하려면 "오프 라인 문제 해결" 섹션의 지침을 참조 하세요.

![VM 목록 페이지](../media/update-agent-issues-linux/vm-list.png)

> [!NOTE]
> 검사를 수행하려면 VM이 실행되고 있어야 합니다. VM이 실행 되 고 있지 않으면 **Vm 시작** 이 표시 됩니다.

업데이트 에이전트 문제 해결 페이지에서 **실행 검사** 를 선택 하 여 문제 해결사를 시작 합니다. 문제 해결사는 [실행 명령을](../../virtual-machines/linux/run-command.md) 사용 하 여 컴퓨터에서 종속성을 확인 하는 스크립트를 실행 합니다. 문제 해결사가 완료되면 검사 결과를 반환합니다.

![문제 해결 페이지](../media/update-agent-issues-linux/troubleshoot-page.png)

검사가 완료 되 면 결과는 창에 반환 됩니다. 검사 섹션에서는 각 검사가 찾으려는 정보를 제공합니다.

![업데이트 에이전트 검사 페이지](../media/update-agent-issues-linux/update-agent-checks.png)

## <a name="prerequisite-checks"></a>필수 구성 요소 확인

### <a name="operating-system"></a>운영 체제

운영 체제 검사는 Hybrid Runbook Worker에서 다음 운영 체제 중 하나를 실행 하 고 있는지 확인 합니다.

|운영 체제  |메모  |
|---------|---------|
|CentOS 6(x86/x64) 및 7(x64)      | Linux 에이전트에는 업데이트 리포지토리에 대한 액세스 권한이 있어야 합니다. 분류 기반 패치를 적용 하려면 CentOS가 없는 보안 데이터를 반환 하는 ' yum '이 필요 합니다.         |
|Red Hat Enterprise 6(x86/x64) 및 7(x64)     | Linux 에이전트에는 업데이트 리포지토리에 대한 액세스 권한이 있어야 합니다.        |
|SUSE Linux Enterprise Server 11(x86/x64) 및 12(x64)     | Linux 에이전트에는 업데이트 리포지토리에 대한 액세스 권한이 있어야 합니다.        |
|Ubuntu 14.04 LTS, 16.04 LTS 및 18.04 LTS(x86/x64)      |Linux 에이전트에는 업데이트 리포지토리에 대한 액세스 권한이 있어야 합니다.         |

## <a name="monitoring-agent-service-health-checks"></a>에이전트 서비스 상태 검사 모니터링

### <a name="log-analytics-agent"></a>Log Analytics 에이전트

이렇게 하면 Linux 용 Log Analytics 에이전트가 설치 되어 있는지 확인할 수 있습니다. 이 에이전트를 설치하는 방법에 대한 지침은 [Linux용 에이전트 설치](../../azure-monitor/learn/quick-collect-linux-computer.md#install-the-agent-for-linux)를 참조하세요.

### <a name="log-analytics-agent-status"></a>Log Analytics 에이전트 상태

이렇게 하면 Linux 용 Log Analytics 에이전트가 실행 되 고 있는지 확인 됩니다. 에이전트가 실행되지 않는 경우 다음 명령을 실행하여 다시 시작할 수 있습니다. 에이전트 문제 해결에 대 한 자세한 내용은 [Linux Hybrid Runbook Worker 문제 해결](hybrid-runbook-worker.md#linux)을 참조 하세요.

```bash
sudo /opt/microsoft/omsagent/bin/service_control restart
```

### <a name="multihoming"></a>멀티 호밍

이 검사에서는 에이전트가 여러 작업 영역에 보고하는지 확인합니다. 멀티 호 밍는 업데이트 관리에서 지원 되지 않습니다.

### <a name="hybrid-runbook-worker"></a>Hybrid Runbook Worker

이 검사는 Linux 용 Log Analytics 에이전트에 Hybrid Runbook Worker 패키지가 있는지 확인 합니다. 이 패키지에는 작업할 업데이트 관리가 필요합니다.

### <a name="hybrid-runbook-worker-status"></a>Hybrid Runbook Worker 상태

이 검사에서는 Hybrid Runbook Worker가 머신에서 실행되고 있는지 확인합니다. Hybrid Runbook Worker가 제대로 실행되는 경우 다음 프로세스가 있어야 합니다. 자세히 알아보려면 [Linux 용 Log Analytics 에이전트 문제 해결](hybrid-runbook-worker.md#oms-agent-not-running)을 참조 하세요.

> [!NOTE]
> Hybrid Runbook Worker 실행 되 고 있지 않고 작업 끝점이 실패 한 경우 업데이트가 실패할 수 있습니다. 업데이트 관리는 작업 끝점에서 hybrid worker 패키지를 다운로드 합니다.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

## <a name="connectivity-checks"></a>연결 검사

### <a name="general-internet-connectivity"></a>일반적인 인터넷 연결

이 검사에서는 머신이 인터넷에 대한 액세스 권한이 있는지 확인합니다.

### <a name="registration-endpoint"></a>등록 엔드포인트

이 검사는 Hybrid Runbook Worker Log Analytics 작업 영역의 Azure Automation와 제대로 통신할 수 있는지 여부를 확인 합니다.

프록시 및 방화벽 구성에서는 Hybrid Runbook Worker 에이전트가 등록 엔드포인트와 통신하도록 허용해야 합니다. 열 주소 및 포트 목록은 [Hybrid worker에 대 한 네트워크 계획](../automation-hybrid-runbook-worker.md#network-planning)을 참조 하세요.

### <a name="operations-endpoint"></a>작업 엔드포인트

이 검사는 에이전트가 작업 런타임 데이터 서비스와 제대로 통신할 수 있는지를 확인합니다.

프록시 및 방화벽 구성에서는 Hybrid Runbook Worker 에이전트가 작업 런타임 데이터 서비스와 통신하도록 허용해야 합니다. 열 주소 및 포트 목록은 [Hybrid worker에 대 한 네트워크 계획](../automation-hybrid-runbook-worker.md#network-planning)을 참조 하세요.

### <a name="log-analytics-endpoint-1"></a>Log Analytics 엔드포인트 1

이 검사에서는 머신이 Log Analytics 에이전트에 필요한 엔드포인트에 대해 액세스 권한이 있는지 확인합니다.

### <a name="log-analytics-endpoint-2"></a>Log Analytics 엔드포인트 2

이 검사에서는 머신이 Log Analytics 에이전트에 필요한 엔드포인트에 대해 액세스 권한이 있는지 확인합니다.

### <a name="log-analytics-endpoint-3"></a>Log Analytics 엔드포인트 3

이 검사에서는 머신이 Log Analytics 에이전트에 필요한 엔드포인트에 대해 액세스 권한이 있는지 확인합니다.

## <a name="troubleshoot-offline"></a><a name="troubleshoot-offline"></a>오프라인으로 문제 해결

이 스크립트를 로컬로 실행하여 Hybrid Runbook Worker에서 오프라인으로 문제 해결사를 사용할 수 있습니다. Python 스크립트 [update_mgmt_health_check py](https://gallery.technet.microsoft.com/scriptcenter/Troubleshooting-utility-3bcbefe6)는 스크립트 센터에서 찾을 수 있습니다. 이 스크립트의 출력 예제는 다음 예제에 표시됩니다.

```output
Debug: Machine Information:   Static hostname: LinuxVM2
         Icon name: computer-vm
           Chassis: vm
        Machine ID: 00000000000000000000000000000000
           Boot ID: 00000000000000000000000000000000
    Virtualization: microsoft
  Operating System: Ubuntu 16.04.5 LTS
            Kernel: Linux 4.15.0-1025-azure
      Architecture: x86-64


Passed: Operating system version is supported

Passed: Microsoft Monitoring agent is installed

Debug: omsadmin.conf file contents:
        WORKSPACE_ID=00000000-0000-0000-0000-000000000000
        AGENT_GUID=00000000-0000-0000-0000-000000000000
        LOG_FACILITY=local0
        CERTIFICATE_UPDATE_ENDPOINT=https://00000000-0000-0000-0000-000000000000.oms.opinsights.azure.com/ConfigurationService.Svc/RenewCertificate
        URL_TLD=opinsights.azure.com
        DSC_ENDPOINT=https://scus-agentservice-prod-1.azure-automation.net/Accou            nts/00000000-0000-0000-0000-000000000000/Nodes\(AgentId='00000000-0000-0000-0000-000000000000'\)
        OMS_ENDPOINT=https://00000000-0000-0000-0000-000000000000.ods.opinsights            .azure.com/OperationalData.svc/PostJsonDataItems
        AZURE_RESOURCE_ID=/subscriptions/00000000-0000-0000-0000-000000000000/re            sourcegroups/myresourcegroup/providers/microsoft.compute/virtualmachines/linuxvm            2
        OMSCLOUD_ID=0000-0000-0000-0000-0000-0000-00
        UUID=00000000-0000-0000-0000-000000000000


Passed: Microsoft Monitoring agent is running

Passed: Machine registered with log analytics workspace:['00000000-0000-0000-0000-000000000000']

Passed: Hybrid worker package is present

Passed: Hybrid worker is running

Passed: Machine is connected to internet

Passed: TCP test for {scus-agentservice-prod-1.azure-automation.net} (port 443)             succeeded

Passed: TCP test for {eus2-jobruntimedata-prod-su1.azure-automation.net} (port 4            43) succeeded

Passed: TCP test for {00000000-0000-0000-0000-000000000000.ods.opinsights.azure.            com} (port 443) succeeded

Passed: TCP test for {00000000-0000-0000-0000-000000000000.oms.opinsights.azure.            com} (port 443) succeeded

Passed: TCP test for {ods.systemcenteradvisor.com} (port 443) succeeded

```

## <a name="next-steps"></a>다음 단계

Hybrid Runbook Worker의 추가 문제를 해결 하려면 [Hybrid Runbook Worker 문제 해결](hybrid-runbook-worker.md)을 참조 하세요.
