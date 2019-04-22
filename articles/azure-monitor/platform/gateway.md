---
title: Log Analytics 게이트웨이 사용 하 여 컴퓨터 연결 | Microsoft Docs
description: Log Analytics 게이트웨이가 인터넷 액세스가 없는 경우 Azure Automation 및 Log Analytics 서비스에 데이터를 전송 하 여 장치 및 Operations Manager 모니터링 컴퓨터를 연결 합니다.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ae9a1623-d2ba-41d3-bd97-36e65d3ca119
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: magoedte
ms.openlocfilehash: b0b221a9fe6c6482e8759664c297dbd25d0ee776
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59699273"
---
# <a name="connect-computers-without-internet-access-by-using-the-log-analytics-gateway-in-azure-monitor"></a>Azure Monitor에서 Log Analytics 게이트웨이 사용 하 여 인터넷 액세스 없이 컴퓨터 연결

>[!NOTE]
>Microsoft Operations Management Suite (OMS) Microsoft Azure Monitor로 전환 하는 대로 용어 변경 됩니다. 이 문서에서는 Azure Log Analytics 게이트웨이는 OMS 게이트웨이를 나타냅니다. 
>

이 문서에는 인터넷 액세스가 불가능 컴퓨터 직접 연결 된 또는 Operations Manager에서 모니터링 되는 경우 Log Analytics 게이트웨이 사용 하 여 Azure Automation 및 Azure Monitor를 사용 하 여 통신을 구성 하는 방법을 설명 합니다. 

Log Analytics 게이트웨이 HTTP CONNECT 명령을 사용 하 여 HTTP 터널링을 지 원하는 HTTP 전달 프록시입니다. 이 게이트웨이 데이터를 수집 하 고 Azure Automation 및 인터넷에 연결 되지 않은 컴퓨터를 대신 하 여 Azure Monitor에서 Log Analytics 작업 영역에 게 보낼 수 있습니다.  

Log Analytics 게이트웨이는 다음을 지원합니다.

* 최대 4 개의 동일한 Log Analytics 작업 영역의 에이전트 뒤 있으며 Azure Automation Hybrid Runbook Worker를 사용 하 여 구성 된 보고 합니다.  
* Windows 컴퓨터는 Microsoft Monitoring Agent에 직접 연결 된 Azure Monitor에서 Log Analytics 작업 영역입니다.
* Linux 컴퓨터는 Linux 용 Log analytics에 직접 연결 된 Azure Monitor에서 Log Analytics 작업 영역입니다.  
* System Center Operations Manager 2012 SP1 UR7, UR3에 Operations Manager 2012 R2 이상 버전에서 Operations Manager 2016 관리 그룹을 사용 하 여 Log Analytics와 통합 됩니다.  

일부 IT 보안 정책이 네트워크 컴퓨터에 대 한 인터넷 연결을 허용 하지 않습니다. 이러한 연결 되지 않은 컴퓨터에는 POS (sale) 장치 또는 예를 들어, IT 서비스를 지 원하는 서버 지점이 될 수 있습니다. Azure Automation 또는 Log Analytics 작업 영역을 관리할 수 있도록 및 모니터에 이러한 장치를 연결할 하면 구성 Log Analytics 게이트웨이와 직접 통신 하도록 합니다. Log Analytics 게이트웨이 구성 정보와 사용자 대신에 데이터를 전달 받을 수 있습니다. 컴퓨터를 Log Analytics 작업 영역에 직접 연결 하도록 Log Analytics 에이전트를 사용 하 여 구성 된 경우 컴퓨터는 Log Analytics 게이트웨이와 대신 통신 합니다.  

Log Analytics 게이트웨이 데이터는 에이전트에서 서비스로 직접 전송 합니다. 전송 중인 데이터를 분석 하지 않습니다.

Operations Manager 관리 그룹에 Log Analytics와 통합 하는 경우 구성 정보를 수신 하도록 설정한 솔루션에 따라 수집 된 데이터를 전송 하는 Log Analytics 게이트웨이의 연결할 관리 서버를 구성할 수 있습니다. .  Operations Manager 에이전트는 관리 서버에 일부 데이터를 보냅니다. 예를 들어, 에이전트는 Operations Manager 경고, 구성 평가 데이터, 인스턴스 공간 데이터 및 용량 데이터를 보낼 수 있습니다. 인터넷 정보 서비스 (IIS) 로그, 성능 데이터 및 보안 이벤트 같은 기타 대용량 데이터를 Log Analytics 게이트웨이에 직접 전송 됩니다. 

하나 이상의 Operations Manager 게이트웨이 서버에 배포 하는 경계 네트워크 또는 격리 된 네트워크에서 신뢰할 수 없는 시스템을 모니터링 하는 경우 해당 서버를 Log Analytics 게이트웨이와 통신할 수 없습니다.  Operations Manager 게이트웨이 서버는 관리 서버에만 보고할 수 있습니다.  프록시 구성 정보는 Azure Monitor에 대 한 로그 데이터를 수집 하도록 구성 된 모든 에이전트 관리 컴퓨터에 자동으로 배포 하 고 Operations Manager 관리 그룹이 Log Analytics 게이트웨이와 통신 하도록 구성 되 면 설정이 비어 경우에 있습니다.    

연결에 대 한 고가용성을 직접 제공 하거나 게이트웨이 통해 Log Analytics 작업 영역을 사용 하 여 통신 하는 Operations Management 그룹을 리디렉션하고 트래픽을 분산 (NLB) 여러 게이트웨이 서버로 분산 하는 네트워크 부하를 사용 합니다. 이런 방식으로 한 게이트웨이 서버가 다운 되 면 트래픽이 사용 가능한 다른 노드로로 리디렉션 됩니다.  

Log Analytics 게이트웨이 실행 하는 컴퓨터에 게이트웨이 사용 하 여 통신 해야 하는 서비스 끝점을 식별 하는 Log Analytics Windows 에이전트가 필요 합니다. 게이트웨이 동일한 작업 영역에 보고를 보내기 위해 해야 에이전트는 에이전트 또는 Operations Manager 관리 그룹이 게이트웨이 뒤으로 구성 됩니다. 이 구성은 게이트웨이 및 해당 할당 된 작업 영역을 사용 하 여 통신 하도록 에이전트를 허용 합니다.

게이트웨이 최대 4 개의 작업 영역에는 멀티홈 수 있습니다. Windows 에이전트를 지 원하는 작업 영역의 총 수입니다.  

각 에이전트는 게이트웨이에서 데이터를 자동으로 송수신할 수 있도록 게이트웨이에 네트워크 연결이 있어야 합니다. 도메인 컨트롤러에 게이트웨이 설치 하지 마세요.

다음 다이어그램은 Azure Automation 및 Log Analytics로 게이트웨이 통해 직접 에이전트에서 데이트를 보여줍니다. 에이전트 프록시 구성을 사용 하 여 Log Analytics 게이트웨이가 구성 되는 포트를 일치 해야 합니다.  

![서비스를 사용 하 여 에이전트의 직접 통신 다이어그램](./media/gateway/oms-omsgateway-agentdirectconnect.png)

다음 다이어그램에서는 Operations Manager 관리 그룹에서 Log Analytics로의 데이터 흐름을 보여 줍니다.   

![Log Analytics와 Operations Manager의 통신 다이어그램](./media/gateway/log-analytics-agent-opsmgrconnect.png)

## <a name="set-up-your-system"></a>시스템 설정

Log Analytics 게이트웨이를 실행할 지정 된 컴퓨터에는 다음과 같은 구성이 있어야 합니다.

* Windows 10, Windows 8.1, Windows 7
* Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 또는 Windows Server 2008
* Microsoft .NET Framework 4.5
* 최소한 4 코어 프로세서 및 8GB 메모리 
* A [Windows에 대 한 Log Analytics 에이전트](agent-windows.md) 게이트웨이 통해 통신 하는 에이전트와 동일한 작업 영역에 보고 하도록 구성 된

### <a name="language-availability"></a>사용 가능한 언어

Log Analytics 게이트웨이 이러한 언어로 제공 됩니다.

- 중국어 (간체)
- 중국어 (번체)
- 체코어
- 네덜란드어
- 영어
- 프랑스어
- 독일어
- 헝가리어
- 이탈리아어
- 일본어
- 한국어
- 폴란드어
- 포르투갈어(브라질)
- 포르투갈어(포르투갈)
- 러시아어
- 스페인어 (국제)

### <a name="supported-encryption-protocols"></a>지원되는 암호화 프로토콜
Log Analytics 게이트웨이 전송 계층 보안 (TLS) 1.0, 1.1 및 1.2를 지원 합니다.  Secure Sockets Layer (SSL) 지원 하지 않습니다.  Log Analytics로 전송 되에서는 데이터의 보안을 위해 이상을 사용 하려면 게이트웨이 구성 합니다. TLS 1.2입니다. 이전 버전의 TLS 또는 SSL는 취약 합니다. 현재 이전 버전과 호환성을 수 있지만 사용 하지 마세요.  

자세한 내용은 [TLS 1.2를 사용하여 안전하게 데이터 보내기](../../azure-monitor/platform/data-security.md#sending-data-securely-using-tls-12)를 검토하세요. 

### <a name="supported-number-of-agent-connections"></a>지원되는 에이전트 연결 수
다음 표에서 약 에이전트 수는 게이트웨이 서버와 통신할 수 있습니다. 지원은 6 초 마다 약 200kb 이하의 데이터를 업로드 하는 에이전트를 기반으로 합니다. 테스트 각 에이전트에 대 한 데이터 볼륨은 하루 약 2.7GB입니다.

|게이트웨이 |에이전트 지원 (근사치)|  
|--------|----------------------------------|  
|CPU: Intel Xeon 프로세서 E5 2660 v3 \@ 2.6ghz 2 코어<br> 메모리: 4GB<br> 네트워크 대역폭: 1Gbps| 600|  
|CPU: Intel Xeon 프로세서 E5 2660 v3 \@ 2.6ghz 4 코어<br> 메모리: 8GB<br> 네트워크 대역폭: 1Gbps| 1000|  

## <a name="download-the-log-analytics-gateway"></a>Log Analytics 게이트웨이 다운로드

Log Analytics 게이트웨이 설치 파일의 최신 버전을 가져오려면 [Microsoft 다운로드 센터](https://www.microsoft.com/download/details.aspx?id=54443) 또는 Azure portal.

Azure portal에서 Log Analytics 게이트웨이 가져오려면 다음이 단계를 수행 합니다.

1. 서비스 목록을 찾아본 다음 **Log Analytics**를 선택합니다. 
1. 작업 영역을 선택합니다.
1. **일반**의 작업 영역 블레이드에서 **빠른 시작**을 선택합니다. 
1. **작업 영역에 연결하는 데이터 원본 선택**에서 **컴퓨터**를 선택합니다.
1. 에 **직접 에이전트** 블레이드에서 **Log Analytics 다운로드 게이트웨이**합니다.
 
   ![Log Analytics 게이트웨이 다운로드 하려면 단계 스크린샷](./media/gateway/download-gateway.png)

또는 

1. **설정** 아래 작업 영역 블레이드에서 **고급 설정**을 선택합니다.
1. 로 이동 **연결 된 원본** > **Windows 서버** 선택한 **Log Analytics 다운로드 게이트웨이**합니다.

## <a name="install-log-analytics-gateway-using-setup-wizard"></a>설치 마법사를 사용 하 여 Log Analytics 게이트웨이 설치

설치 마법사를 사용 하 여 게이트웨이 설치 하려면 다음이 단계를 수행 합니다. 

1. 대상 폴더에서 **Log Analytics gateway.msi**를 두 번 클릭합니다.
1. **Welcome** 페이지에서 **다음**을 선택합니다.

   ![게이트웨이 설치 마법사의 스크린 샷의 시작 페이지](./media/gateway/gateway-wizard01.png)

1. 에 **사용권 계약** 페이지에서 **사용권 계약에 동의** Microsoft 소프트웨어 사용 조건에 동의 하 여 선택한 **다음**합니다.
1. **포트 및 프록시 주소** 페이지에서 다음 단계를 수행합니다.

   a. 게이트웨이에 사용할 TCP 포트 번호를 입력 합니다. 설치는이 포트 번호를 사용 하 여 Windows 방화벽에서 인바운드 규칙을 구성 합니다.  기본값은 8080입니다.
      유효한 포트 번호의 범위는 1에서 65535 까지입니다. 입력한 내용이 이 범위를 벗어나면 오류 메시지가 표시됩니다.

   b. 게이트웨이 설치한 서버를 프록시를 통해 통신 해야 하는 경우는 프록시 주소를 입력 게이트웨이에서 연결 해야 합니다. 예를 들어 `http://myorgname.corp.contoso.com:80`을 입력합니다.  이 필드를 비워 두면 게이트웨이 직접 인터넷에 연결 하려고 합니다.  프록시 서버에 인증이 필요한 경우 사용자 이름과 암호를 입력합니다.

   다. **다음**을 선택합니다.

   ![게이트웨이 프록시에 대 한 구성의 스크린샷](./media/gateway/gateway-wizard02.png)

1. 없는 경우 Microsoft Update 사용, Microsoft 업데이트 페이지가 나타나고 사용 하도록 선택할 수 있습니다. 항목을 선택 하 고 선택한 **다음**합니다. 그렇지 않은 경우 다음 단계를 계속 진행합니다.
1. 에 **대상 폴더** 페이지에서 기본 폴더 C:\Program Files\OMS Gateway를 그대로 유지 하거나 게이트웨이 설치 하려는 위치를 입력 합니다. 그런 후 **다음**을 선택합니다.
1. **설치 준비 완료** 페이지에서 **설치**를 선택합니다. 설치 권한을 요청 하는 사용자 계정 컨트롤을 선택 **예**합니다.
1. 설치가 완료 되 면 선택 **완료**합니다. 서비스가 실행 되 고 있는지를 확인 하려면 services.msc 스냅인을 열고 확인 **OMS 게이트웨이** 서비스 목록에 표시 되 고 해당 상태가 **실행**.

   ![OMS 게이트웨이 실행 되 고 있는지 보여 주는 스크린 샷의 로컬 서비스](./media/gateway/gateway-service.png)

## <a name="install-the-log-analytics-gateway-using-the-command-line"></a>명령줄을 사용 하 여 Log Analytics 게이트웨이 설치
게이트웨이에 대 한 에이전트용된 파일은 명령줄 또는 다른 자동화 된 메서드에서 자동 설치를 지 원하는 Windows 설치 관리자 패키지입니다. Windows Installer에 대 한 표준 명령줄 옵션을 사용 하 여 잘 모르는 경우 [명령줄 옵션](https://docs.microsoft.com/windows/desktop/Msi/command-line-options)합니다.   

다음 표에서 설치를 지 원하는 매개 변수를 강조 표시 합니다.

|매개 변수| 메모|
|----------|------| 
|PORTNUMBER | 게이트웨이에 대 한 TCP 포트 번호에서 수신 대기를 |
|프록시 | 프록시 서버의 IP 주소 |
|INSTALLDIR | 게이트웨이 소프트웨어 파일의 설치 디렉터리를 지정 하려면 정규화 된 경로 |
|사용자 이름 | 프록시 서버를 사용 하 여 인증 하려면 사용자 Id |
|암호 | 프록시를 사용 하 여 인증 하는 Id 사용자의 암호 |
|LicenseAccepted | 값을 지정 **1** 사용권 계약에 동의 확인 하려면 |
|HASAUTH | 값을 지정 **1** 사용자 이름/암호 매개 변수를 지정 하는 경우 |
|HASPROXY | 값을 지정 **1** 에 대 한 IP 주소를 지정 하는 경우 **프록시** 매개 변수 |

자동으로 게이트웨이 설치 하 고 특정 프록시 주소, 포트 번호를 사용 하 여 구성 하려면 다음을 입력 합니다.

```dos
Msiexec.exe /I “oms gateway.msi” /qn PORTNUMBER=8080 PROXY=”10.80.2.200” HASPROXY=1 LicenseAccepted=1 
```

설치를 숨깁니다 /qn 명령줄 옵션을 사용 하 여, /qb 자동 설치 하는 동안 설치 프로그램을 보여 줍니다.  

프록시를 사용 하 여 인증할 때 자격 증명을 제공 해야 할 경우 다음을 입력 합니다.

```dos
Msiexec.exe /I “oms gateway.msi” /qn PORTNUMBER=8080 PROXY=”10.80.2.200” HASPROXY=1 HASAUTH=1 USERNAME=”<username>” PASSWORD=”<password>” LicenseAccepted=1 
```

설치 후 확인할 수 있습니다 설정이 허용 (exlcuding 사용자 이름 및 암호) 다음 PowerShell cmdlet을 사용 하 여:

- **Get-OMSGatewayConfig** – 게이트웨이가에서 수신 하도록 구성 된 TCP 포트를 반환 합니다.
- **Get-OMSGatewayRelayProxy** –와 통신 하도록 구성 된 프록시 서버의 IP 주소를 반환 합니다.

## <a name="configure-network-load-balancing"></a>네트워크 부하 분산 구성 
네트워크 부하 분산 (NLB) Microsoft를 사용 하 여 사용 하 여 고가용성을 위해 게이트웨이 구성할 수 있습니다 [네트워크 로드 균형 조정 (NLB)](https://docs.microsoft.com/windows-server/networking/technologies/network-load-balancing)하십시오 [Azure Load Balancer](../../load-balancer/load-balancer-overview.md), 또는 하드웨어 기반 부하 분산 장치. 부하 분산 장치는 노드 전반에 걸쳐 Log Analytics 에이전트 또는 Operations Manager 관리 서버에서 요청된 연결을 리디렉션하여 트래픽을 관리합니다. 게이트웨이 서버가 하나 다운되면 트래픽은 다른 노드로 리디렉션됩니다.

### <a name="microsoft-network-load-balancing"></a>Microsoft 네트워크 부하 분산
Windows Server 2016 네트워크 부하 분산 클러스터를 설계하고 배포하는 방법을 알아보려면 [네트워크 부하 분산](https://docs.microsoft.com/windows-server/networking/technologies/network-load-balancing)을 참조하세요. 다음 단계에서는 Microsoft 네트워크 부하 분산 클러스터를 구성하는 방법에 대해 설명합니다.  

1. NLB 클러스터의 구성원인 Windows 서버에 관리 계정으로 로그인합니다.  
2. [서버 관리자]에서 [네트워크 부하 분산 관리자]를 열고, **도구**를 클릭한 다음 **네트워크 부하 분산 관리자**를 클릭합니다.
3. Log Analytics 게이트웨이 서버를 설치되어 있는 Microsoft Monitoring Agent와 연결하려면 클러스터의 IP 주소를 마우스 오른쪽 단추로 클릭한 다음, **클러스터에 호스트 추가**를 클릭합니다. 

    ![네트워크 부하 분산 관리자 – 클러스터에 호스트를 추가](./media/gateway/nlb02.png)
 
4. 연결하려는 게이트웨이 서버의 IP 주소를 입력합니다. 

    ![네트워크 부하 분산 관리자 - 클러스터에 호스트 추가: 연결](./media/gateway/nlb03.png) 

### <a name="azure-load-balancer"></a>Azure Load Balancer
참조 디자인 및 Azure Load Balancer를 배포 하는 방법에 알아보려면 [Azure Load Balancer 란?](../../load-balancer/load-balancer-overview.md)합니다. 기본 load balancer를 배포 하려면이 단계를 수행 [퀵 스타트](../../load-balancer/quickstart-create-basic-load-balancer-portal.md) 섹션에 설명 된 단계를 제외한 **백 엔드 서버를 만들**합니다.   

> [!NOTE]
> 사용 하 여 Azure Load Balancer를 구성 합니다 **기본 SKU**,에서는 Azure 가상 머신을 가용성 집합에 속해야 합니다. 가용성 집합에 대 한 자세한 내용은 참조 하세요 [Azure에서 Windows 가상 머신의 가용성 관리](../../virtual-machines/windows/manage-availability.md)합니다. 가용성 집합에 기존 가상 컴퓨터를 추가, 참조 [Azure Resource Manager VM 가용성 집합](https://gallery.technet.microsoft.com/Set-Azure-Resource-Manager-f7509ec4)합니다.
> 

부하 분산 장치를 만든 후 백 엔드 풀을 만들어야, 하나 이상의 게이트웨이 서버에 트래픽을 분산 하는 합니다. 빠른 시작 문서 섹션에 설명 된 단계를 따릅니다 [부하 분산 장치에 대 한 리소스를 만들](../../load-balancer/quickstart-create-basic-load-balancer-portal.md#create-resources-for-the-load-balancer)합니다.  

>[!NOTE]
>상태 프로브를 구성 하는 경우에 게이트웨이 서버의 TCP 포트를 사용 하도록 구성 되어야 합니다. 상태 프로브는 동적으로 추가 하거나 게이트웨이 서버 상태 검사에 대 한 해당 응답에 따라 부하 분산 장치 순환에서 제거 합니다. 
>

## <a name="configure-the-log-analytics-agent-and-operations-manager-management-group"></a>Log Analytics 에이전트 및 Operations Manager 관리 그룹 구성
이 섹션에서는 Azure Automation 또는 Log Analytics와 통신 하도록 Log Analytics 게이트웨이 사용 하 여 직접 연결 된 Log Analytics 에이전트, Operations Manager 관리 그룹, 또는 Azure Automation Hybrid Runbook Workers를 구성 하는 방법에 배웁니다.  

### <a name="configure-a-standalone-log-analytics-agent"></a>독립 실행형 Log Analytics 에이전트를 구성 합니다.
Log Analytics 에이전트를 구성 하는 경우 Log Analytics 게이트웨이 서버와 해당 포트 번호의 IP 주소를 사용 하 여 프록시 서버 값을 대체 합니다. Log Analytics 에이전트 프록시 구성은 부하 분산 장치 뒤에 여러 게이트웨이 서버를 배포한 경우 부하 분산 장치의 가상 IP 주소입니다.  

>[!NOTE]
>Log Analytics 에이전트를 Log Analytics에 직접 연결 된 Windows 컴퓨터에 게이트웨이 설치 하려면 [Azure에서 Log Analytics 서비스에 연결 하는 Windows 컴퓨터](agent-windows.md)합니다. Linux 컴퓨터에 연결 하려면을 참조 하세요 [하이브리드 환경에서 Linux 컴퓨터에 대 한 Log Analytics 에이전트를 구성](../../azure-monitor/learn/quick-collect-linux-computer.md)합니다. 
>

게이트웨이 서버에서 에이전트를 설치한 후에 게이트웨이와 통신 하는 작업 영역의 에이전트를 작업 영역에 보고 하도록 구성 합니다. Log Analytics Windows 에이전트에서 게이트웨이 설치 되어 있지 않으면, 300 이벤트가 에이전트를 설치 해야 함을 나타내는 OMS 게이트웨이 이벤트 로그에 기록 됩니다. 에이전트를 설치 하지만 통해 통신 하는 에이전트와 동일한 작업 영역에 보고 하도록 구성 되지 않은 경우 105 이벤트 로그에 기록 됩니다는 동일한, 게이트웨이에서 에이전트가 에이전트와 동일한 작업 영역에 보고 하도록 구성 해야 함을 나타내는 해당 co 게이트웨이 사용 하 여 mmunicate입니다.

구성을 완료 한 후 변경 내용을 적용 하려면 OMS 게이트웨이 서비스를 다시 시작 합니다. 그렇지 않으면 게이트웨이 105 OMS 게이트웨이 이벤트 로그에 이벤트를 보고는 Log Analytics와 통신을 시도 하는 에이전트를 거부 합니다. 또한 추가 하거나 게이트웨이 서버에 에이전트 구성에서 작업 영역을 제거할 때 발생 합니다.   

Automation Hybrid Runbook Worker와 관련 된 정보를 참조 하세요 [Hybrid Runbook Worker를 사용 하 여 데이터 센터 또는 클라우드 리소스를 자동화할](../../automation/automation-hybrid-runbook-worker.md)합니다.

### <a name="configure-operations-manager-where-all-agents-use-the-same-proxy-server"></a>Operations Manager에서 모든 에이전트에서 동일한 프록시 서버를 사용 하는 위치를 구성 합니다.
설정이 비어 있더라도 Operations Manager 프록시 구성은 Operations Manager에 보고하는 모든 에이전트에 자동으로 적용됩니다.  

Operations Manager를 지원 하도록 OMS 게이트웨이 사용 하려면 다음이 있어야 합니다.

* Microsoft Monitoring Agent (버전 8.0.10900.0 이상)는 동일한 Log Analytics 작업 영역 관리 그룹에 보고 하도록 구성 되어 있는지를 통해 OMS 게이트웨이 서버의 설치 및 구성 합니다.
* 인터넷에 연결 합니다. 또는 OMS 게이트웨이 인터넷에 연결 되어 있는 프록시 서버에 연결 되어야 합니다.

> [!NOTE]
> 게이트웨이에 대 한 값을 지정 하는 경우 빈 값을 모든 에이전트에 푸시됩니다.
>

처음으로 Operations Manager 관리 그룹을 Log Analytics 작업 영역을 사용 하 여 등록 하는 경우에 운영 콘솔에서 관리 그룹에 대 한 프록시 구성을 지정 하는 옵션을 볼 수 없습니다. 이 옵션은 관리 그룹 서비스를 사용 하 여 등록 된 경우에 사용할 수 있습니다.  

통합을 구성 하려면 관리 그룹의 모든 관리 서버 및 운영 콘솔을 실행 중인 시스템에서 Netsh를 사용 하 여 시스템 프록시 구성을 업데이트 합니다. 다음 단계를 수행하세요.

1. 관리자 권한 명령 프롬프트를 엽니다.

   a. 선택 **시작** enter **cmd**합니다.  

   b. 마우스 오른쪽 단추로 클릭 **명령 프롬프트** 선택한 **관리자 권한으로 실행**합니다.  

1. 다음 명령을 입력합니다.

   `netsh winhttp set proxy <proxy>:<port>`

Log Analytics와의 통합을 완료 한 후 실행 하 여 변경 내용을 제거할 `netsh winhttp reset proxy`합니다. 그런 다음 운영 콘솔을 사용 하는 **프록시 서버 구성** Log Analytics 게이트웨이 서버를 지정 하는 옵션입니다. 

1. Operations Manager 콘솔에서 아래 **Operations Management Suite**를 선택 **연결**를 선택한 후 **프록시 서버 구성**합니다.

   ![스크린 샷 Operations Manager의 프록시 서버 구성 선택 영역을 표시](./media/gateway/scom01.png)

1. 선택 **프록시 서버를 Operations Management Suite에 액세스 하는 데** 다음 Log Analytics 게이트웨이 서버의 IP 주소 또는 부하 분산 장치의 가상 IP 주소를 입력 합니다. 접두사로 시작 주의 `http://`합니다.

   ![스크린 샷 Operations Manager의 프록시 서버 주소를 보여 주는](./media/gateway/scom02.png)

1. **마침**을 선택합니다. 이제 Operations Manager 관리 그룹이 게이트웨이 서버를 통해 Log Analytics 서비스와 통신하도록 구성되었습니다.

### <a name="configure-operations-manager-where-specific-agents-use-a-proxy-server"></a>Operations Manager에서 특정 에이전트에서 프록시 서버를 사용 하는 위치를 구성 합니다.
대규모 또는 복잡 한 환경에만 특정 서버 (또는 그룹) Log Analytics 게이트웨이 서버를 사용 하도록 좋습니다.  이러한 서버에 대해 업데이트할 수 없습니다 Operations Manager 에이전트를 직접 관리 그룹에 대 한 전역 값으로이 값을 덮어씁니다.  대신, 이러한 값을 푸시하는 데 사용 하는 규칙을 재정의 합니다.  

> [!NOTE] 
> 이 구성 기술을 사용 하 여 환경의 여러 Log Analytics 게이트웨이 서버에 대해 허용 하려는 경우.  예를 들어 지역 단위로 지정 하는 특정 Log Analytics 게이트웨이 서버를 요구할 수 있습니다.
>

특정 서버 또는 Log Analytics 게이트웨이 서버를 사용 하는 그룹 구성: 

1. Operations Manager 콘솔을 열고 **작성** 작업 영역을 선택합니다.  
1. 제작 작업 영역에서 **규칙**을 선택합니다. 
1. Operations Manager 도구 모음에서 선택 합니다 **범위** 단추입니다. 이 단추를 사용할 수 없는 경우에 폴더가 아니라 개체를 선택 했는지 확인 합니다 **모니터링** 창입니다. **관리 팩 개체 범위 지정** 대화 상자에서 일반적인 대상 클래스, 그룹 또는 개체의 목록을 표시합니다. 
1. 에 **검색할** 필드를 입력 합니다 **Health Service** 목록에서 선택 합니다. **확인**을 선택합니다.  
1. 검색할 **Advisor 프록시 설정 규칙**합니다. 
1. Operations Manager 도구 모음에서 선택 **재정의** 를 차례로 **Rule\For 클래스의 특정 개체를 재정의 합니다. Health Service** 목록에서 개체를 선택 합니다.  하거나이 재정의를 적용 하려는 서버의 상태 관리 서비스 개체를 포함 하는 사용자 지정 그룹을 만듭니다. 다음 사용자 지정 그룹에 재정의 적용 합니다.
1. 에 **재정의 속성** 대화 상자에서 확인란을 추가 합니다 **재정의** 열 옆에 **WebProxyAddress** 매개 변수.  에 **재정의 값** 필드 Log Analytics 게이트웨이 서버의 URL을 입력 합니다. 접두사로 시작 주의 `http://`합니다.  

    >[!NOTE]
    > 규칙을 사용하도록 설정하지 않아도 됩니다. 이미 되 자동으로 Microsoft System Center Advisor 보안 참조 재정의 관리 팩에 재정의 사용 하 여를 대상으로 하는 Microsoft System Center Advisor 모니터링 서버 그룹입니다.
    > 

1. 관리 팩을 선택 합니다 **대상 관리 팩 선택** 목록 또는 새로운 봉인 되지 않은 관리 팩을 선택 하 여 만들 **새로 만들기**합니다. 
1. 완료하면 **확인**을 선택합니다. 

### <a name="configure-for-automation-hybrid-runbook-workers"></a>Automation Hybrid Runbook Worker에 대 한 구성
사용자 환경에 Automation Hybrid Runbook Worker를 있다면 수동 임시 해결 방법 작업자를 지원 하도록 OMS 게이트웨이 구성 하려면 다음이 단계를 따릅니다.

이 섹션의 단계를 수행 하려면 Automation 계정이 있는 Azure 지역을 알아야 합니다. 해당 위치를 찾으려면:

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. Azure Automation 서비스를 선택합니다.
1. 적절한 Azure Automation 계정을 선택합니다.
1. **위치** 아래에서 해당 지역을 확인합니다.

   ![Azure portal에서 Automation 계정 위치는 스크린샷](./media/gateway/location.png)

다음 표에서를 사용 하 여 각 위치에 대 한 URL을 확인 합니다.

**작업 런타임 데이터 서비스 Url**

| **위치**: | **URL** |
| --- | --- |
| 미국 중북부 |ncus-jobruntimedata-prod-su1.azure-automation.net |
| 서유럽 |we-jobruntimedata-prod-su1.azure-automation.net |
| 미국 중남부 |scus-jobruntimedata-prod-su1.azure-automation.net |
| 미국 동부 2 |eus2-jobruntimedata-prod-su1.azure-automation.net |
| 캐나다 중부 |cc-jobruntimedata-prod-su1.azure-automation.net |
| 유럽 북부 |ne-jobruntimedata-prod-su1.azure-automation.net |
| 동남아시아 |sea-jobruntimedata-prod-su1.azure-automation.net |
| 중앙 인도 |cid-jobruntimedata-prod-su1.azure-automation.net |
| 일본 |jpe-jobruntimedata-prod-su1.azure-automation.net |
| 오스트레일리아 |ase-jobruntimedata-prod-su1.azure-automation.net |

**에이전트 서비스 URL**

| **위치**: | **URL** |
| --- | --- |
| 미국 중북부 |ncus-agentservice-prod-1.azure-automation.net |
| 서유럽 |we-agentservice-prod-1.azure-automation.net |
| 미국 중남부 |scus-agentservice-prod-1.azure-automation.net |
| 미국 동부 2 |eus2-agentservice-prod-1.azure-automation.net |
| 캐나다 중부 |cc-agentservice-prod-1.azure-automation.net |
| 유럽 북부 |ne-agentservice-prod-1.azure-automation.net |
| 동남아시아 |sea-agentservice-prod-1.azure-automation.net |
| 중앙 인도 |cid-agentservice-prod-1.azure-automation.net |
| 일본 |jpe-agentservice-prod-1.azure-automation.net |
| 오스트레일리아 |ase-agentservice-prod-1.azure-automation.net |

컴퓨터가 Hybrid Runbook Worker로 자동으로 등록 되 면 업데이트 관리 솔루션을 사용 하 여 패치를 관리할 수 있습니다. 다음 단계를 수행하세요.

1. 작업 런타임 데이터 서비스 URL을 Log Analytics 게이트웨이의 허용된 호스트 목록에 추가합니다. 예: `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
1. `Restart-Service OMSGatewayService` PowerShell cmdlet을 사용하여 Log Analytics 게이트웨이 서비스를 다시 시작합니다.

컴퓨터에 가입 되어 Azure Automation Hybrid Runbook Worker 등록 cmdlet을 사용 하 여 다음이 단계를 수행 합니다.

1. 에이전트 서비스 등록 URL을 Log Analytics 게이트웨이의 허용된 호스트 목록에 추가합니다. 예: `Add-OMSGatewayAllowedHost ncus-agentservice-prod-1.azure-automation.net`
1. 작업 런타임 데이터 서비스 URL을 Log Analytics 게이트웨이의 허용된 호스트 목록에 추가합니다. 예: `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
1. Log Analytics 게이트웨이 서비스를 다시 시작합니다.
    `Restart-Service OMSGatewayService`

## <a name="useful-powershell-cmdlets"></a>유용한 PowerShell cmdlet
Log Analytics 게이트웨이의 구성 설정을 업데이트 하는 작업을 완료 하려면 cmdlet을 사용할 수 있습니다. Cmdlet을 사용 해야 합니다.

1. Log Analytics 게이트웨이 (Microsoft Windows Installer) 설치 합니다.
1. PowerShell 콘솔 창을 엽니다.
1. 이 명령을 입력 하 여 모듈을 가져옵니다. `Import-Module OMSGateway`
1. 이전 단계에서 오류가 발생하지 않은 경우 모듈을 성공적으로 가져왔으며 cmdlet을 사용할 수 있습니다. `Get-Module OMSGateway`을 입력합니다.
1. Cmdlet을 사용 하 여 변경 후에 OMS 게이트웨이 서비스를 다시 시작 합니다.

모듈을 가져오지 않았습니다 의미 하는 3 단계에서 오류가 발생 합니다. PowerShell 모듈을 찾을 수 없는 경우 오류가 발생할 수 있습니다. OMS 게이트웨이 설치 경로에서 모듈을 찾을 수 있습니다. *C:\Program Files\Microsoft OMS Gateway\PowerShell\OmsGateway*.

| **Cmdlet** | **매개 변수** | **설명** | **예제** |
| --- | --- | --- | --- |  
| `Get-OMSGatewayConfig` |키 |서비스 구성 가져오기 |`Get-OMSGatewayConfig` |  
| `Set-OMSGatewayConfig` |키(필수) <br> 값 |서비스 구성 변경 |`Set-OMSGatewayConfig -Name ListenPort -Value 8080` |  
| `Get-OMSGatewayRelayProxy` | |릴레이(업스트림) 프록시 주소 가져오기 |`Get-OMSGatewayRelayProxy` |  
| `Set-OMSGatewayRelayProxy` |주소<br> 사용자 이름<br> 암호 |릴레이(업스트림) 프록시 주소(및 자격 증명) 설정 |1. 릴레이 프록시 및 자격 증명 설정:<br> `Set-OMSGatewayRelayProxy`<br>`-Address http://www.myproxy.com:8080`<br>`-Username user1 -Password 123` <br><br> 2. 인증이 필요 없는 릴레이 프록시 설정: `Set-OMSGatewayRelayProxy`<br> `-Address http://www.myproxy.com:8080` <br><br> 3. 릴레이 프록시 설정 지우기:<br> `Set-OMSGatewayRelayProxy` <br> `-Address ""` |  
| `Get-OMSGatewayAllowedHost` | |(자동으로 다운로드 로컬로 구성 되어 허용 된 호스트, 호스트 허용)에 현재 허용 된 호스트 가져오기 |`Get-OMSGatewayAllowedHost` | 
| `Add-OMSGatewayAllowedHost` |호스트(필수) |호스트를 허용 목록에 추가 |`Add-OMSGatewayAllowedHost -Host www.test.com` |  
| `Remove-OMSGatewayAllowedHost` |호스트(필수) |호스트를 허용 목록에서 제거 |`Remove-OMSGatewayAllowedHost`<br> `-Host www.test.com` |  
| `Add-OMSGatewayAllowedClientCertificate` |주체(필수) |클라이언트 인증서 주체를 허용 목록에 추가 |`Add-OMSGatewayAllowed`<br>`ClientCertificate` <br> `-Subject mycert` |  
| `Remove-OMSGatewayAllowedClientCertificate` |주체(필수) |클라이언트 인증서 주체를 허용 목록에서 제거 |`Remove-OMSGatewayAllowed` <br> `ClientCertificate` <br> `-Subject mycert` |  
| `Get-OMSGatewayAllowedClientCertificate` | |현재 허용 된 클라이언트 인증서 주체 가져오기를 (자동으로 다운로드 하는 로컬로 구성 되어 허용 된 주체는 허용 된 주체만)만 가져옵니다. |`Get-`<br>`OMSGatewayAllowed`<br>`ClientCertificate` |  

## <a name="troubleshooting"></a>문제 해결
게이트웨이에서 기록한 이벤트를 수집 하려면 Log Analytics로 에이전트를 설치 해야 합니다.

![Log Analytics 게이트웨이 로그에서 이벤트 뷰어 목록 스크린샷](./media/gateway/event-viewer.png)

### <a name="log-analytics-gateway-event-ids-and-descriptions"></a>Log Analytics 게이트웨이 이벤트 Id 및 설명

다음 표에서 이벤트 Id 및 Log Analytics 게이트웨이 로그 이벤트에 대 한 설명을 보여 줍니다.

| **ID** | **설명** |
| --- | --- |
| 400 |모든 응용 프로그램 오류 없는 특정 ID가 있습니다. |
| 401 |잘못된 구성. 예를 들어 listenPort = 정수 대신 "text"입니다. |
| 402 |TLS 핸드셰이크 메시지 구문 분석 중 예외 |
| 403 |네트워킹 오류. 예를 들어, 대상 서버에 연결할 수 없습니다. |
| 100 |일반 정보 |
| 101 |서비스가 시작됨 |
| 102 |서비스가 중지됨 |
| 103 |클라이언트로부터 HTTP CONNECT 명령은 수신함을 받았습니다. |
| 104 |HTTP CONNECT 명령이 아님 |
| 105 |대상 서버가 허용된 목록에 있지 없거나 대상 포트가 보안 (443). <br> <br> OMS 게이트웨이 서버의 MMA 에이전트 및 OMS 게이트웨이와 통신 하는 에이전트가 동일한 Log Analytics 작업 영역에 연결 되어 있는지 확인 합니다. |
| 105 |ERROR TcpConnection – 클라이언트 인증서가 잘못됨: CN=Gateway. <br><br> OMS 게이트웨이 버전 1.0.395.0 사용 하는 경우 확인 이상. 또한 OMS 게이트웨이 서버의 MMA 에이전트 및 OMS 게이트웨이와 통신 하는 에이전트가 동일한 Log Analytics 작업 영역에 연결 되어 있는지 확인 합니다. |
| 106 |지원 되지 않는 TLS/SSL 프로토콜 버전입니다.<br><br> Log Analytics 게이트웨이 TLS 1.0, TLS 1.1 및 1.2를 지원합니다. SSL은 지원하지 않습니다.|
| 107 |TLS 세션이 확인되었습니다. |

### <a name="performance-counters-to-collect"></a>수집할 성능 카운터

다음 테이블은 Log Analytics 게이트웨이에 사용할 수 있는 성능 카운터를 보여줍니다. 성능 모니터를 사용 하 여 카운터를 추가 합니다.

| **Name** | **설명** |
| --- | --- |
| Log Analytics 게이트웨이/활성 클라이언트 연결 |활성 클라이언트 네트워크(TCP) 연결의 수 |
| Log Analytics 게이트웨이/오류 수 |오류 수 |
| Log Analytics 게이트웨이/연결된 클라이언트 |연결된 클라이언트 수 |
| Log Analytics 게이트웨이/오류 수 |TLS 유효성 검사 오류로 인한 거부 횟수 |

![Log Analytics의 스크린 샷 게이트웨이 인터페이스를 성능 카운터를 표시 합니다.](./media/gateway/counters.png)

## <a name="assistance"></a>지원
Azure portal에 로그인 하는 경우에 Log Analytics 게이트웨이 또는 다른 Azure 서비스 또는 기능에 대 한 도움을 얻을 수 있습니다.
도움말을 보려면 선택한 포털의 오른쪽 위 모서리에서 물음표 아이콘을 선택 **새 지원 요청**합니다. 새 지원 요청 양식을 완성 합니다.

![새 지원 요청 스크린샷](./media/gateway/support.png)

## <a name="next-steps"></a>다음 단계
[데이터 원본을 추가](../../azure-monitor/platform/agent-data-sources.md) 연결 된 원본에서 데이터를 수집 하 여 Log Analytics 작업 영역에 데이터를 저장 합니다.