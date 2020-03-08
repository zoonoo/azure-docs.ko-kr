---
title: Log Analytics 에이전트 개요
description: 이 항목에서는 Log Analytics를 사용하여 Azure, 온-프레미스 또는 여타 클라우드 환경에 호스트된 컴퓨터를 모니터링하고 데이터를 수집하는 방법을 설명합니다.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/04/2020
ms.openlocfilehash: 99083cd08db98a6d6ac56d4cf55aaf9d0e64e2b4
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78362324"
---
# <a name="log-analytics-agent-overview"></a>Log Analytics 에이전트 개요
Azure Log Analytics 에이전트는 모든 클라우드, 온-프레미스 컴퓨터 및 [System Center Operations Manager](https://docs.microsoft.com/system-center/scom/)에서 모니터링 하는 가상 컴퓨터에서 종합적인 관리를 위해 개발 되었습니다. Windows 및 Linux 에이전트는 다양 한 원본에서 수집 된 데이터를 Azure Monitor의 Log Analytics 작업 영역으로 보내고, 모니터링 솔루션에 정의 된 대로 고유한 로그 나 메트릭을 전송 합니다. 또한 Log Analytics 에이전트는 Azure Monitor에서 [VM용 Azure Monitor](../insights/vminsights-enable-overview.md), [Azure Security Center](/azure/security-center/), [Azure Automation](../../automation/automation-intro.md)등의 기타 서비스와 정보를 지원 합니다.

이 문서에서는 에이전트, 시스템 및 네트워크 요구 사항과 다양한 배포 모델을 상세히 살펴봅니다.

> [!NOTE]
> Log Analytics 에이전트가 Microsoft Monitoring Agent (MMA) 또는 OMS Linux 에이전트로 참조 될 수도 있습니다.

> [!NOTE]
> Azure 진단 확장은 계산 리소스의 게스트 운영 체제에서 모니터링 데이터를 수집 하는 데 사용할 수 있는 에이전트 중 하나입니다. 다른 에이전트에 대 한 설명과 요구 사항에 적합 한 에이전트를 선택 하는 방법에 대 한 지침은 [Azure Monitor 에이전트 개요](agents-overview.md) 를 참조 하세요.

## <a name="comparison-to-azure-diagnostics-extension"></a>Azure 진단 확장과 비교
Azure Monitor의 [azure 진단 확장](diagnostics-extension-overview.md) 을 사용 하 여 azure 가상 컴퓨터의 게스트 운영 체제에서 모니터링 데이터를 수집할 수도 있습니다. 요구 사항에 따라 둘 중 하나 또는 둘 모두를 사용 하도록 선택할 수 있습니다. Azure Monitor 에이전트의 자세한 비교는 [Azure Monitor 에이전트 개요](agents-overview.md) 를 참조 하세요. 

고려해 야 할 주요 차이점은 다음과 같습니다.

- Azure 진단 확장은 Azure 가상 컴퓨터에만 사용할 수 있습니다. Log Analytics 에이전트는 Azure, 다른 클라우드 및 온-프레미스의 가상 컴퓨터와 함께 사용할 수 있습니다.
- Azure 진단 확장 Azure Storage, [Azure Monitor 메트릭](data-platform-metrics.md) (Windows에만 해당) 및 Event Hubs에 데이터를 보냅니다. Log Analytics 에이전트는 [Azure Monitor 로그](data-platform-logs.md)에 데이터를 수집 합니다.
- Log Analytics 에이전트는 [솔루션](../monitor-reference.md#insights-and-core-solutions), [VM용 Azure Monitor](../insights/vminsights-overview.md)및 [Azure Security Center](/azure/security-center/)와 같은 기타 서비스에 필요 합니다.

## <a name="costs"></a>비용
Log Analytics 에이전트에 대 한 비용은 없지만 데이터 수집에 대 한 요금이 발생할 수 있습니다. Log Analytics 작업 영역에서 수집 된 데이터에 대 한 가격 책정에 대 한 자세한 내용은 [Azure Monitor 로그를 사용 하 여 사용량 및 비용 관리](manage-cost-storage.md) 를 확인 하세요.

## <a name="data-collected"></a>수집되는 데이터
다음 표에서는 연결 된 모든 에이전트에서 수집 하도록 Log Analytics 작업 영역을 구성할 수 있는 데이터의 유형을 보여 줍니다. Log Analytics 에이전트를 사용 하 여 다른 종류의 데이터를 수집 하는 정보, 솔루션 및 기타 솔루션 목록은 [Azure Monitor에서 모니터링 되는 내용](../monitor-reference.md) 을 참조 하세요.

| 데이터 원본 | Description |
| --- | --- |
| [Windows 이벤트 로그](data-sources-windows-events.md) | Windows 이벤트 로깅 시스템으로 전송되는 정보입니다. |
| [Syslog](data-sources-syslog.md)                     | Linux 이벤트 로깅 시스템으로 전송 되는 정보입니다. |
| [성능](data-sources-performance-counters.md)  | 숫자 값은 운영 체제 및 작업의 여러 측면에 대 한 성능을 측정 합니다. |
| [IIS 로그](data-sources-iis-logs.md)                 | 게스트 운영 체제에서 실행 되는 IIS 웹 사이트에 대 한 사용 정보입니다. |
| [사용자 지정 로그](data-sources-custom-logs.md)           | Windows 및 Linux 컴퓨터의 텍스트 파일에서 발생 하는 이벤트입니다. |

## <a name="data-destinations"></a>데이터 대상
Log Analytics 에이전트는 Azure Monitor의 Log Analytics 작업 영역으로 데이터를 보냅니다. Windows 에이전트는 다중 작업 영역 및 System Center Operations Manager 관리 그룹으로 데이터를 보내기 위해 멀티홈 일 수 있습니다. Linux 에이전트는 단일 대상 으로만 보낼 수 있습니다.

## <a name="other-services"></a>기타 서비스
Linux 및 Windows 용 에이전트는 Azure Monitor에 연결 하는 경우에만 사용할 수 있으며, [변경 내용 추적](../../automation/change-tracking.md), [업데이트 관리](../../automation/automation-update-management.md), [Azure Security Center](../../security-center/security-center-intro.md)와 같은 Hybrid Runbook worker 역할 및 기타 서비스를 호스트 하는 Azure Automation도 지원 합니다. Hybrid Runbook Worker 역할에 대한 자세한 내용은 [Azure Automation Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md)를 참조하세요.  

## <a name="installation-and-configuration"></a>설치 및 구성

Log Analytics 에이전트를 사용 하 여 데이터를 수집 하는 경우 에이전트 배포를 계획 하려면 다음 사항을 이해 해야 합니다.

* Windows 에이전트에서 데이터를 수집 하려면 System Center Operations Manager 관리 그룹에 보고 하는 동안에도 [각 에이전트가 하나 이상의 작업 영역에 보고 하도록 구성할](agent-windows.md)수 있습니다. Windows 에이전트는 최대 4 개의 작업 영역을 보고할 수 있습니다.
* Linux 에이전트는 멀티 호 밍을 지원 하지 않으며 단일 작업 영역에만 보고할 수 있습니다.
* Windows 에이전트는 [FIPS 140 표준을](https://docs.microsoft.com/windows/security/threat-protection/fips-140-validation)지원 하지만 Linux 에이전트는이를 지원 하지 않습니다.  

System Center Operations Manager 2012 R2 이상을 사용 하는 경우:

* 각 Operations Manager 관리 그룹은 [하나의 작업 영역에만 연결할](om-agents.md)수 있습니다.
* 관리 그룹에 보고 하는 Linux 컴퓨터는 Log Analytics 작업 영역에 직접 보고 하도록 구성 되어야 합니다. Linux 컴퓨터가 이미 작업 영역에 직접 보고 하 고 Operations Manager를 사용 하 여 모니터링 하려는 경우 다음 단계에 따라 [Operations Manager 관리 그룹에 보고](agent-manage.md#configure-agent-to-report-to-an-operations-manager-management-group)합니다.
* Windows 컴퓨터에 Log Analytics Windows 에이전트를 설치 하 고 작업 영역과 함께 통합 된 Operations Manager와 다른 작업 영역에 보고 하도록 할 수 있습니다.


Log Analytics 에이전트를 설치 하 고 사용자의 요구 사항에 따라 Azure Monitor에 컴퓨터를 연결 하는 방법에는 여러 가지가 있습니다. 다음 표는 조직에서 어떤 방법이 가장 적합한지 결정하기 위해 각 방법을 설명합니다.

|원본 | 방법 | Description|
|-------|-------------|-------------|
|Azure VM| [Azure Portal에서 수동으로](../../azure-monitor/learn/quick-collect-azurevm.md?toc=/azure/azure-monitor/toc.json) | Log Analytics 작업 영역에서 배포할 Vm을 지정 합니다. |
| | Azure CLI 또는 Azure Resource Manager 템플릿을 사용 하 여 [Windows](../../virtual-machines/extensions/oms-windows.md) 또는 [Linux](../../virtual-machines/extensions/oms-linux.md) 용 VM 확장 Log Analytics | 이 확장은 Azure Virtual Machines에 Log Analytics 에이전트를 설치하고 기존 Azure Monitor 작업 영역에 등록합니다. |
| | [VM용 Azure Monitor](../insights/vminsights-enable-overview.md) | VM용 Azure Monitor를 사용 하 여 모니터링을 사용 하도록 설정 하면 Log Analytics 확장 및 에이전트가 설치 됩니다. |
| | [자동 프로 비전 Azure Security Center](../../security-center/security-center-enable-data-collection.md) | 지원 되는 모든 Azure Vm 및 보안 취약점 및 위협에 대 한 모니터링을 사용 하도록 설정 하는 경우 생성 되는 새 Vm에서 Log Analytics 에이전트를 프로 비전 할 수 Azure Security Center. 사용 하도록 설정 하면 설치 된 에이전트가 없는 기존 또는 새 VM이 프로 비전 됩니다. |
| 하이브리드 Windows 컴퓨터| [수동 설치](agent-windows.md) | 명령줄에서 Microsoft Monitoring agent를 설치 합니다. |
| | [Azure Automation DSC](agent-windows.md#install-the-agent-using-dsc-in-azure-automation) | Azure Automation DSC를 사용 하 여 설치를 자동화 합니다. |
| | [Azure Stack 리소스 관리자 템플릿](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win) | 데이터 센터에 Microsoft Azure Stack를 배포한 경우 Azure Resource Manager 템플릿을 사용 합니다.| 
| 하이브리드 Linux 컴퓨터| [수동 설치](../../azure-monitor/learn/quick-collect-linux-computer.md)|GitHub에 호스트된 래퍼 스크립트를 호출하는 Linux용 에이전트를 설치합니다. | 
| System Center Operations Manager|[Operations Manager를 Log Analytics와 통합](../../azure-monitor/platform/om-agents.md) | Operations Manager와 Azure Monitor 로그 간의 통합을 구성 하 여 Windows 컴퓨터 보고에서 수집 된 데이터를 관리 그룹으로 전달 합니다.|  


## <a name="supported-windows-operating-systems"></a>지원되는 Windows 운영 체제

Windows 에이전트에 대해 다음 버전의 Windows 운영 체제가 공식적으로 지원됩니다.

* Windows Server 2019
* Windows Server 2008 SP2 (x64), 2008 R2, 2012, 2012 R2, 2016, 버전 1709 및 1803
* Windows 7 SP1, Windows 8 Enterprise 및 Pro, Windows 10 Enterprise 및 Pro

>[!NOTE]
>Windows 용 Log Analytics 에이전트가 서버 모니터링 시나리오를 지원 하도록 설계 되었지만 서버 운영 체제에 대해 구성 되 고 최적화 된 작업을 지원 하기 위해 Windows 클라이언트를 실행할 수 있습니다. 에이전트는 Windows 클라이언트를 지원 하지만 명시적으로 지정 하지 않는 한 모니터링 솔루션은 클라이언트 모니터링 시나리오에 중점을 두지 않습니다.

## <a name="supported-linux-operating-systems"></a>지원되는 Linux 운영 체제

이 섹션에서는 지원되는 Linux 배포판에 대한 정보를 제공합니다.

2018년 8월 이후에 출시된 버전부터 지원 모델이 다음과 같이 변경됩니다.  

* 서버 버전만 지원되고 클라이언트 버전은 지원되지 않습니다.  
* [Azure Linux 보증](../../virtual-machines/linux/endorsed-distros.md)배포판에서 집중 지원. Azure Linux 보증 새 배포판/버전 간에 약간의 지연이 있을 수 있으며 Log Analytics Linux 에이전트에 대해 지원 됩니다.
* 나열된 각 주 버전의 모든 부 버전이 지원됩니다.
* 제조업체의 지원 종료 날짜가 지난 버전은 지원되지 않습니다.  
* 새 AMI 버전은 지원되지 않습니다.  
* 기본적으로 SSL 1.x를 실행하는 버전만 지원됩니다.

>[!NOTE]
>현재 지원되지 않고 Microsoft 지원 모델에 맞지 않는 배포판 또는 버전을 사용하는 경우 이 리포지토리를 포크하고, Microsoft 기술 지원에서 포크된 에이전트 버전에 대한 지원을 제공하지 않는다는 것을 확인하는 것이 좋습니다.

* Amazon Linux 2017.09(x64)
* CentOS Linux 6(x86/x64) 및 7(x64)  
* Oracle Linux 6 및 7(x86/x64) 
* Red Hat Enterprise Linux Server 6(x86/x64) 및 7(x64)
* Debian GNU/Linux 8 및 9(x86/x64)
* Ubuntu 14.04 LTS(x86/x64), 16.04 LTS(x86/x64) 및 18.04 LTS(x64)
* SUSE Linux Enterprise Server 12 (x64) 및 15 (x64)

>[!NOTE]
>OpenSSL 1.1.0은 x86_x64 플랫폼(64비트)에서만 지원되고, OpenSSL 1.x 미만은 어떤 플랫폼에서도 지원되지 않습니다.
>

### <a name="agent-prerequisites"></a>에이전트 필수 구성 요소

다음 표에서는 에이전트가 설치 될 지원 되는 Linux 배포판에 필요한 패키지를 강조 표시 합니다.

|필수 패키지 |Description |최소 버전 |
|-----------------|------------|----------------|
|Glibc |    GNU C 라이브러리 | 2.5-12 
|Openssl    | OpenSSL 라이브러리 | 1.0. x 또는 1.1. x |
|Curl | cURL 웹 클라이언트 | 7.15.5 |
|Python-ctypes | | 
|PAM | 플러그형 인증 모듈 | | 

>[!NOTE]
>Syslog 메시지를 수집하려면 rsyslog 또는 syslog-ng가 필요합니다. Red Hat Enterprise Linux 버전 5, CentOS 및 Oracle Linux 버전(sysklog)에서는 syslog 이벤트 수집을 위한 기본 syslog 디먼이 지원되지 않습니다. 이 배포의 해당 버전에서 syslog 데이터를 수집하려면 rsyslog 디먼을 설치하고 sysklog를 대체하도록 구성해야 합니다.

## <a name="tls-12-protocol"></a>TLS 1.2 프로토콜

Azure Monitor 로그로 전송 되는 데이터의 보안을 보장 하려면 TLS (전송 계층 보안) 1.2 이상을 사용 하도록 에이전트를 구성 하는 것이 좋습니다. 이전 버전의 TLS/SSL(Secure Sockets Layer)은 취약한 것으로 나타났으며, 여전히 이전 버전과 호환되지만 **사용하지 않는 것이 좋습니다**.  자세한 내용은 [TLS 1.2를 사용하여 안전하게 데이터 보내기](data-security.md#sending-data-securely-using-tls-12)를 검토하세요. 


## <a name="network-requirements"></a>네트워크 요구 사항
Linux 및 Windows 용 에이전트는 TCP 포트 443을 통해 Azure Monitor 서비스로 아웃 바운드를 전달 하 고, 컴퓨터가 인터넷을 통해 통신 하기 위해 방화벽 또는 프록시 서버를 통해 연결 하는 경우 아래 요구 사항을 검토 하 여 네트워크 구성을 이해 합니다. 필수. IT 보안 정책이 네트워크의 컴퓨터에서 인터넷에 연결할 수 있도록 허용 하지 않는 경우 [Log Analytics 게이트웨이](gateway.md) 를 설정한 다음 게이트웨이를 통해 Azure Monitor 로그에 연결 하도록 에이전트를 구성할 수 있습니다. 그러면 에이전트에서 구성 정보를 수신 하 고 작업 영역에서 사용 하도록 설정한 데이터 수집 규칙 및 모니터링 솔루션에 따라 수집 된 데이터를 보낼 수 있습니다.

![Log Analytics 에이전트 통신 다이어그램](./media/log-analytics-agent/log-analytics-agent-01.png)


## <a name="network-firewall-requirements"></a>네트워크 방화벽 요구 사항
아래 정보는 Linux 및 Windows 에이전트가 Azure Monitor 로그와 통신 하는 데 필요한 프록시 및 방화벽 구성 정보를 나열 합니다.  

|에이전트 리소스|포트 |Direction |HTTPS 검사 무시|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |포트 443 |아웃바운드|yes |  
|*.oms.opinsights.azure.com |포트 443 |아웃바운드|yes |  
|\*.blob.core.windows.net |포트 443 |아웃바운드|yes |  

Azure Government에 필요한 방화벽 정보는 [Azure Government management](../../azure-government/documentation-government-services-monitoringandmanagement.md#azure-monitor-logs)를 참조 하세요. 

Azure Automation Hybrid Runbook Worker를 사용 하 여 사용자 환경에서 runbook 또는 관리 솔루션을 사용 하기 위해 자동화 서비스에 연결 하 고 등록 하려는 경우에 [는 Hybrid Runbook Worker에 대 한 네트워크 구성](../../automation/automation-hybrid-runbook-worker.md#network-planning)에 설명 된 포트 번호 및 url에 대 한 액세스 권한이 있어야 합니다. 

Windows 및 Linux 에이전트는 HTTPS 프로토콜을 사용 하 여 Azure Monitor에 대 한 프록시 서버 또는 Log Analytics 게이트웨이를 통해 통신 하는 것을 지원 합니다.  익명 및 기본 인증(사용자 이름/암호)이 둘 다 지원됩니다.  서비스에 직접 연결된 Windows 에이전트의 경우, 프록시 구성은 설치 중이나 제어판 또는 PowerShell을 사용하여 [배포 후](agent-manage.md#update-proxy-settings)에 지정됩니다.  

Linux 에이전트의 경우, 설치 중에 또는 [설치 후에](agent-manage.md#update-proxy-settings) proxy.conf 구성 파일을 수정하여 프록시 서버를 지정할 수 있습니다.  Linux 에이전트 프록시 구성 값은 다음 구문을 포함합니다.

`[protocol://][user:password@]proxyhost[:port]`

> [!NOTE]
> 프록시 서버에 인증할 필요가 없는 경우에도 Linux 에이전트는 의사 사용자/암호를 제공해야 합니다. 이는 사용자 이름 또는 암호일 수 있습니다.

|속성| Description |
|--------|-------------|
|프로토콜 | https |
|사용자 | 프록시 인증을 위한 선택적 사용자 이름 |
|password | 프록시 인증을 위한 선택적 암호 |
|proxyhost | 프록시 서버/Log Analytics 게이트웨이의 주소 또는 FQDN |
|포트 | 프록시 서버/Log Analytics 게이트웨이 대한 선택적 포트 번호 |

예: `https://user01:password@proxy01.contoso.com:30443`

> [!NOTE]
> 암호에 “\@”과 같은 특수 문자를 사용하는 경우 값이 잘못 구문 분석되므로 프록시 연결 오류가 발생합니다.  이 문제를 해결하려면 [URLDecode](https://www.urldecoder.org/)와 같은 도구를 사용하여 URL에서 암호를 인코드합니다.  



## <a name="next-steps"></a>다음 단계

* [데이터 원본](agent-data-sources.md)을 검토하여 Windows 또는 Linux 시스템에서 데이터를 수집할 수 있는 데이터 원본을 이해하세요. 
* 데이터 원본 및 솔루션에서 수집한 데이터를 분석하는 [로그 쿼리](../log-query/log-query-overview.md)에 대해 알아봅니다. 
* Azure Monitor에 기능을 추가하고 Log Analytics 작업 영역으로 데이터를 수집하는 [모니터링 솔루션](../insights/solutions.md)에 대해 알아봅니다.

