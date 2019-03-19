---
title: Azure Log Analytics 에이전트를 사용하여 로그 데이터 수집 | Microsoft Docs
description: 이 항목에서는 Log Analytics를 사용하여 Azure, 온-프레미스 또는 여타 클라우드 환경에 호스트된 컴퓨터를 모니터링하고 데이터를 수집하는 방법을 설명합니다.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: magoedte
ms.openlocfilehash: e9df83ef81c2656bf94002feb79d7e4d99ed7954
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57841109"
---
# <a name="collect-log-data-with-the-azure-log-analytics-agent"></a>Azure Log Analytics 에이전트를 사용하여 로그 데이터 수집

Azure Log Analytics 에이전트(이전 명칭은 MMA(Microsoft Monitoring Agent) 또는 OMS Linux 에이전트)는 온-프레미스 머신, [System Center Operations Manager](https://docs.microsoft.com/system-center/scom/)로 모니터링되는 컴퓨터, 클라우드에 있는 가상 머신을 종합적으로 관리하기 위해 개발되었습니다. Windows 에이전트와 Linux 에이전트가 Log Analytics 작업 영역에 연결되어 다양한 소스에서 데이터 및 (모니터링 솔루션에 정의된) 고유 로그 또는 메트릭을 수집합니다. 

이 문서에서는 에이전트, 시스템 및 네트워크 요구 사항과 다양한 배포 모델을 상세히 살펴봅니다.   

## <a name="overview"></a>개요

![Log Analytics 에이전트 통신 다이어그램](./media/log-analytics-agent/log-analytics-agent-01.png)

수집된 데이터를 분석하고 그에 대한 조치를 취하기 전에 먼저 Log Analytics 서비스에 데이터를 보내려는 모든 머신에 대한 에이전트를 설치 및 연결해야 합니다. Windows 및 Linux에서는 Azure Log Analytics VM 확장을 사용하여 Azure VM에 에이전트를 설치할 수 있습니다. 하이브리드 환경에 있는 머신의 경우 설정, 명령줄 또는 Azure Automation의 DSC(필요한 상태 구성)를 사용하여 에이전트를 설치할 수 있습니다. 

Linux 및 Windows용 에이전트는 TCP 포트 443을 통해 Log Analytics 서비스로 아웃바운드 통신을 수행합니다. 머신이 인터넷을 통해 통신하기 위해 방화벽 또는 프록시 서버에 연결하는 경우, 아래의 요구 사항을 검토하여 필요한 네트워크 구성을 파악하세요. IT 보안 정책에는 네트워크의 컴퓨터가 인터넷에 연결할 수 있도록 허용하지 않을 경우 [Log Analytics 게이트웨이](gateway.md)를 설정한 후 에이전트가 게이트웨이를 통해 Log Analytics에 연결하도록 구성할 수 있습니다. 그러면 에이전트는 구성 정보를 수신하고, 사용하도록 설정한 데이터 수집 규칙 및 모니터링 솔루션에 따라 수집된 데이터를 보낼 수 있습니다. 

System Center Operations Manager 2012 R2를 사용하여 컴퓨터를 모니터링하는 경우 Log Analytics 서비스와 멀티홈으로 구성되어 데이터를 수집하고 서비스로 전달할 수 있고 [Operations Manager](../../azure-monitor/platform/om-agents.md)에서 계속 모니터링될 수도 있습니다. Log Analytics와 통합된 Operations Manager 관리 그룹에서 모니터링되는 Linux 컴퓨터는 데이터 원본에 대한 구성을 수신하거나 관리 그룹을 통해 수집된 데이터를 전달하지 않습니다. Windows 에이전트는 최대 4개의 Log Analytics 작업 영역을 보고할 수 있는 반면 Linux 에이전트는 단일 작업 영역에 대한 보고만 지원합니다.  

Linux 및 Windows용 에이전트는 Log Analytics에 연결할 뿐만 아니라, [변경 내용 추적](../../automation/automation-change-tracking.md) 및 [업데이트 관리](../../automation/automation-update-management.md)와 같은 Hybrid Runbook Worker 역할 및 기타 서비스를 호스트하도록 Azure Automation도 지원합니다. Hybrid Runbook Worker 역할에 대한 자세한 내용은 [Azure Automation Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md)를 참조하세요.  

## <a name="supported-windows-operating-systems"></a>지원되는 Windows 운영 체제
Windows 에이전트에 대해 다음 버전의 Windows 운영 체제가 공식적으로 지원됩니다.

* Windows Server 2008 R2 이상
* Windows 7 SP1 이상.

## <a name="supported-linux-operating-systems"></a>지원되는 Linux 운영 체제
이 섹션에서는 지원되는 Linux 배포판에 대한 정보를 제공합니다.    

2018년 8월 이후에 출시된 버전부터 지원 모델이 다음과 같이 변경됩니다.  

* 서버 버전만 지원되고 클라이언트 버전은 지원되지 않습니다.  
* 새 버전의 [Azure Linux 보증 배포판](../../virtual-machines/linux/endorsed-distros.md)은 항상 지원됩니다.  
* 나열된 각 주 버전의 모든 부 버전이 지원됩니다.
* 제조업체의 지원 종료 날짜가 지난 버전은 지원되지 않습니다.  
* 새 AMI 버전은 지원되지 않습니다.  
* 기본적으로 SSL 1.x를 실행하는 버전만 지원됩니다.

현재 지원되지 않고 Microsoft 지원 모델에 맞지 않는 배포판 또는 버전을 사용하는 경우 이 리포지토리를 포크하고, Microsoft 기술 지원에서 포크된 에이전트 버전에 대한 지원을 제공하지 않는다는 것을 확인하는 것이 좋습니다.

* Amazon Linux 2017.09(x64)
* CentOS Linux 6(x86/x64) 및 7(x64)  
* Oracle Linux 6 및 7(x86/x64) 
* Red Hat Enterprise Linux Server 6(x86/x64) 및 7(x64)
* Debian GNU/Linux 8 및 9(x86/x64)
* Ubuntu 14.04 LTS(x86/x64), 16.04 LTS(x86/x64) 및 18.04 LTS(x64)
* SUSE Linux Enterprise Server 12(x64)

>[!NOTE]
>OpenSSL 1.1.0은 x86_x64 플랫폼(64비트)에서만 지원되고, OpenSSL 1.x 미만은 어떤 플랫폼에서도 지원되지 않습니다.
>

## <a name="tls-12-protocol"></a>TLS 1.2 프로토콜
Log Analytics로 전송 중인 데이터를 보호하려면 적어도 TLS(전송 계층 보안) 1.2 이상을 사용하도록 에이전트를 구성하는 것이 좋습니다. 이전 버전의 TLS/SSL(Secure Sockets Layer)은 취약한 것으로 나타났으며, 여전히 이전 버전과 호환되지만 **사용하지 않는 것이 좋습니다**.  자세한 내용은 [TLS 1.2를 사용하여 안전하게 데이터 보내기](../../azure-monitor/platform/data-security.md#sending-data-securely-using-tls-12)를 검토하세요. 

## <a name="network-firewall-requirements"></a>네트워크 방화벽 요구 사항
아래 정보는 Linux 및 Windows 에이전트가 Log Analytics와 통신하는 데 필요한 프록시 및 방화벽 구성 정보를 나열합니다.  

|에이전트 리소스|포트 |방향 |HTTPS 검사 무시|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |포트 443 |아웃바운드|예 |  
|*.oms.opinsights.azure.com |포트 443 |아웃바운드|예 |  
|\*.blob.core.windows.net |포트 443 |아웃바운드|예 |  
|* .azure-automation.net |포트 443 |아웃바운드|예 |  

Azure Government에 대 한 필요한 방화벽 정보를 참조 하세요 [Azure Government 관리](../../azure-government/documentation-government-services-monitoringandmanagement.md#azure-monitor-logs)합니다. 

Azure Automation Hybrid Runbook Worker를 사용하여 사용자 환경에서 Runbook을 사용하기 위해 Automation 서비스에 연결하고 등록하려면 [Hybrid Runbook Worker에 대한 네트워크 구성](../../automation/automation-hybrid-runbook-worker.md#network-planning)에 설명된 URL 및 포트 번호에 대한 액세스 권한이 있어야 합니다. 

Windows 및 Linux 에이전트는 HTTPS 프로토콜을 사용하여 프록시 서버 또는 Log Analytics 게이트웨이를 통해 Log Analytics 서비스와 통신하도록 지원합니다.  익명 및 기본 인증(사용자 이름/암호)이 둘 다 지원됩니다.  서비스에 직접 연결된 Windows 에이전트의 경우, 프록시 구성은 설치 중이나 제어판 또는 PowerShell을 사용하여 [배포 후](agent-manage.md#update-proxy-settings)에 지정됩니다.  

Linux 에이전트의 경우, 설치 중에 또는 [설치 후에](agent-manage.md#update-proxy-settings) proxy.conf 구성 파일을 수정하여 프록시 서버를 지정할 수 있습니다.  Linux 에이전트 프록시 구성 값은 다음 구문을 포함합니다.

`[protocol://][user:password@]proxyhost[:port]`

> [!NOTE]
> 프록시 서버에 인증할 필요가 없는 경우에도 Linux 에이전트는 의사 사용자/암호를 제공해야 합니다. 이는 사용자 이름 또는 암호일 수 있습니다.

|자산| 설명 |
|--------|-------------|
|프로토콜 | https |
|사용자 | 프록시 인증을 위한 선택적 사용자 이름 |
|암호 | 프록시 인증을 위한 선택적 암호 |
|proxyhost | 프록시 서버/Log Analytics 게이트웨이의 주소 또는 FQDN |
|포트 | 프록시 서버/Log Analytics 게이트웨이 대한 선택적 포트 번호 |

예: `https://user01:password@proxy01.contoso.com:30443`

> [!NOTE]
> 암호에 “\@”과 같은 특수 문자를 사용하는 경우 값이 잘못 구문 분석되므로 프록시 연결 오류가 발생합니다.  이 문제를 해결하려면 [URLDecode](https://www.urldecoder.org/)와 같은 도구를 사용하여 URL에서 암호를 인코드합니다.  

## <a name="install-and-configure-agent"></a>에이전트 설치 및 구성 
Azure 구독 또는 하이브리드 환경에 포함된 머신을 Azure Log Analytics에 직접 연결하려면 요구 사항에 따라 다양한 방법을 사용할 수 있습니다. 다음 표는 조직에서 어떤 방법이 가장 적합한지 결정하기 위해 각 방법을 설명합니다.

|원본 | 방법 | 설명|
|-------|-------------|-------------|
|Azure VM| - Azure CLI 또는 Azure Resource Manager 템플릿을 사용한 [Windows](../../virtual-machines/extensions/oms-windows.md) 또는 [Linux](../../virtual-machines/extensions/oms-linux.md)용 Log Analytics VM 확장<br>- [Azure Portal에서 수동으로 연결합니다](../../azure-monitor/learn/quick-collect-azurevm.md?toc=/azure/azure-monitor/toc.json). | 이 확장은 Azure Virtual Machines에 Log Analytics 에이전트를 설치하고 기존 Azure Monitor 작업 영역에 등록합니다.|
| 하이브리드 Windows 컴퓨터|- [수동 설치](agent-windows.md)<br>- [Azure Automation DSC](agent-windows.md#install-the-agent-using-dsc-in-azure-automation)<br>- [Azure Stack을 사용한 Resource Manager 템플릿](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win) |Azure Automation DSC, [System Center Configuration Manager](https://docs.microsoft.com/sccm/apps/deploy-use/deploy-applications) 또는 Azure Resource Manager 템플릿(데이터 센터에 Microsoft Azure Stack을 배포한 경우)과 같은 자동화된 방법을 사용하거나 명령줄에서 Microsoft Monitoring Agent를 설치합니다.| 
| 하이브리드 Linux 컴퓨터| [수동 설치](../../azure-monitor/learn/quick-collect-linux-computer.md)|GitHub에 호스트된 래퍼 스크립트를 호출하는 Linux용 에이전트를 설치합니다. | 
| System Center Operations Manager|[Operations Manager를 Log Analytics와 통합](../../azure-monitor/platform/om-agents.md) | Linux 및 Windows 컴퓨터 보고에서 수집된 데이터를 관리 그룹에 전달하도록 Operations Manager와 Log Analytics 간에 통합을 구성합니다.|  

## <a name="next-steps"></a>다음 단계

* [데이터 원본](../../azure-monitor/platform/agent-data-sources.md)을 검토하여 Windows 또는 Linux 시스템에서 데이터를 수집할 수 있는 데이터 원본을 이해하세요. 

* 데이터 원본 및 솔루션에서 수집한 데이터를 분석하는 [로그 쿼리](../../azure-monitor/log-query/log-query-overview.md)에 대해 알아봅니다. 

* Azure Monitor에 기능을 추가하고 Log Analytics 작업 영역으로 데이터를 수집하는 [모니터링 솔루션](../../azure-monitor/insights/solutions.md)에 대해 알아봅니다.
