---
title: 로그 분석 게이트웨이를 사용하여 컴퓨터 연결 | 마이크로 소프트 문서
description: Log Analytics 게이트웨이를 사용하여 장치 및 운영 관리자가 모니터링하는 컴퓨터를 연결하여 인터넷에 액세스할 수 없는 경우 Azure 자동화 및 로그 분석 서비스로 데이터를 보냅니다.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 12/24/2019
ms.openlocfilehash: a92e96a835f24ac54fa55b05086a35b9a91d609e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298342"
---
# <a name="connect-computers-without-internet-access-by-using-the-log-analytics-gateway-in-azure-monitor"></a>Azure 모니터에서 로그 분석 게이트웨이를 사용하여 인터넷에 액세스하지 않고 컴퓨터를 연결합니다.

>[!NOTE]
>OMS(Microsoft 운영 관리 제품군)가 Microsoft Azure 모니터로 전환됨에 따라 용어가 변경되고 있습니다. 이 문서에서는 OMS 게이트웨이를 Azure Log Analytics 게이트웨이라고 합니다. 
>

이 문서에서는 직접 연결되거나 Operations Manager에서 모니터링하는 컴퓨터가 인터넷에 액세스할 수 없는 경우 Log Analytics 게이트웨이를 사용하여 Azure 자동화 및 Azure Monitor와의 통신을 구성하는 방법을 설명합니다. 

로그 분석 게이트웨이는 HTTP CONNECT 명령을 사용하여 HTTP 터널링을 지원하는 HTTP 전달 프록시입니다. 이 게이트웨이는 인터넷에 직접 연결할 수 없는 컴퓨터를 대신하여 Azure 자동화 및 Azure Monitor의 로그 분석 작업 영역으로 데이터를 보냅니다. 

Log Analytics 게이트웨이는 다음을 지원합니다.

* 뒤에 있는 각 에이전트에 구성되고 Azure 자동화 하이브리드 Runbook 작업자로 구성된 동일한 로그 분석 작업 영역까지 보고합니다.  
* Microsoft 모니터링 에이전트가 Azure 모니터의 로그 분석 작업 영역에 직접 연결되어 있는 Windows 컴퓨터입니다.
* Linux용 로그 분석 에이전트가 Azure 모니터의 로그 분석 작업 영역에 직접 연결되는 Linux 컴퓨터입니다.  
* UR7을 갖춘 시스템 센터 운영 관리자 2012 SP1, UR3가 있는 운영 관리자 2012 R2 또는 로그 분석과 통합된 운영 관리자 2016 이상의 관리 그룹입니다.  

일부 IT 보안 정책은 네트워크 컴퓨터에 대한 인터넷 연결을 허용하지 않습니다. 예를 들어, 이러한 연결되지 않은 컴퓨터는 POS(판매 시점) 장치 또는 IT 서비스를 지원하는 서버일 수 있습니다. 이러한 장치를 관리 및 모니터링할 수 있도록 이러한 장치를 Azure 자동화 또는 로그 분석 작업 영역에 연결하려면 로그 분석 게이트웨이와 직접 통신하도록 구성합니다. Log Analytics 게이트웨이는 구성 정보를 수신하고 해당 게이트웨이를 대신하여 데이터를 전달할 수 있습니다. 컴퓨터가 Log Analytics 작업 영역에 직접 연결하도록 Log Analytics 에이전트로 구성된 경우 컴퓨터는 대신 Log Analytics 게이트웨이와 통신합니다.  

Log Analytics 게이트웨이는 에이전트에서 서비스로 데이터를 직접 전송합니다. 전송 중 데이터를 분석하지 않으며 게이트웨이는 서비스와의 연결이 끊어지면 데이터를 캐시하지 않습니다. 게이트웨이가 서비스와 통신할 수 없는 경우 에이전트는 계속 실행되고 모니터링되는 컴퓨터의 디스크에서 수집된 데이터를 큐에 넣습니다. 연결이 복원되면 에이전트는 수집된 캐시된 데이터를 Azure Monitor로 보냅니다.

운영 관리자 관리 그룹이 Log Analytics와 통합되면 관리 서버를 Log Analytics 게이트웨이에 연결하여 구성 정보를 수신하고 활성화한 솔루션에 따라 수집된 데이터를 전송하도록 구성할 수 있습니다. .  운영 관리자 에이전트는 관리 서버에 일부 데이터를 보냅니다. 예를 들어 에이전트는 Operations Manager 경고, 구성 평가 데이터, 인스턴스 공간 데이터 및 용량 데이터를 보낼 수 있습니다. IIS(인터넷 정보 서비스) 로그, 성능 데이터 및 보안 이벤트와 같은 기타 대용량 데이터는 Log Analytics 게이트웨이로 직접 전송됩니다. 

둘 레퍼런스 네트워크 또는 격리된 네트워크에서 신뢰할 수 없는 시스템을 모니터링하기 위해 하나 이상의 Operations Manager 게이트웨이 서버가 배포된 경우 해당 서버는 Log Analytics 게이트웨이와 통신할 수 없습니다.  운영 관리자 게이트웨이 서버는 관리 서버에만 보고할 수 있습니다.  운영 관리자 관리 그룹이 Log Analytics 게이트웨이와 통신하도록 구성된 경우 프록시 구성 정보는 Azure Monitor에 대한 로그 데이터를 수집하도록 구성된 모든 에이전트 관리 컴퓨터에 자동으로 배포됩니다. 설정이 비어 있는 경우에도 마찬가지입니다.

게이트웨이를 통해 Log Analytics 작업 영역과 통신하는 직접 연결된 또는 운영 관리 그룹에 대한 고가용성을 제공하려면 NLB(네트워크 로드 분산)를 사용하여 여러 게이트웨이 서버에서 트래픽을 리디렉션하고 배포합니다. 이렇게 하면 한 게이트웨이 서버가 다운되면 트래픽이 사용 가능한 다른 노드로 리디렉션됩니다.  

Log Analytics 게이트웨이를 실행하는 컴퓨터에서는 로그 애널리틱스 Windows 에이전트가 게이트웨이와 통신해야 하는 서비스 끝점을 식별해야 합니다. 또한 에이전트는 게이트웨이 뒤에 있는 에이전트 또는 운영 관리자 관리 그룹이 구성된 동일한 작업 영역에 보고하도록 게이트웨이를 지시해야 합니다. 이 구성을 사용하면 게이트웨이와 에이전트가 할당된 작업 영역과 통신할 수 있습니다.

게이트웨이는 최대 4개의 작업 영역까지 멀티홈할 수 있습니다. Windows 에이전트가 지원하는 총 작업 영역 수입니다.  

에이전트가 게이트웨이로 데이터를 자동으로 전송할 수 있도록 각 에이전트는 게이트웨이에 대한 네트워크 연결이 있어야 합니다. 도메인 컨트롤러에 게이트웨이를 설치하지 마십시오. 게이트웨이 서버 뒤에 있는 Linux 컴퓨터는 [래퍼 스크립트 설치](agent-linux.md#install-the-agent-using-wrapper-script) 방법을 사용하여 Linux용 로그 분석 에이전트를 설치할 수 없습니다. 게이트웨이는 앞에서 언급한 Azure 서비스와의 통신만 지원하기 때문에 에이전트를 수동으로 다운로드하고 컴퓨터에 복사한 다음 수동으로 설치해야 합니다.

다음 다이어그램은 직접 에이전트에서 게이트웨이를 통해 Azure 자동화 및 로그 분석으로 흐르는 데이터를 보여 주었습니다. 에이전트 프록시 구성은 Log Analytics 게이트웨이가 구성된 포트와 일치해야 합니다.  

![서비스와 직접 에이전트 통신의 다이어그램](./media/gateway/oms-omsgateway-agentdirectconnect.png)

다음 다이어그램에서는 Operations Manager 관리 그룹에서 Log Analytics로의 데이터 흐름을 보여 줍니다.   

![로그 분석과 운영 관리자 통신 의 다이어그램](./media/gateway/log-analytics-agent-opsmgrconnect.png)

## <a name="set-up-your-system"></a>시스템 설정

Log Analytics 게이트웨이를 실행하도록 지정된 컴퓨터에는 다음 구성이 있어야 합니다.

* 윈도우 10, 윈도우 8.1, 또는 윈도우 7
* 윈도우 서버 2019, 윈도우 서버 2016, 윈도우 서버 2012 R2, 윈도우 서버 2012, 윈도우 서버 2008 R2, 또는 윈도우 서버 2008
* Microsoft .NET Framework 4.5도 필요합니다
* 최소 4코어 프로세서 와 8GB 메모리 
* 게이트웨이를 통해 통신하는 에이전트와 동일한 작업 영역에 보고하도록 구성된 [Windows용 로그 분석 에이전트](agent-windows.md)

### <a name="language-availability"></a>사용 가능한 언어

로그 분석 게이트웨이는 다음 언어로 제공됩니다.

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

로그 분석 게이트웨이는 TLS(전송 계층 보안) 1.0, 1.1 및 1.2만 지원합니다.  보안 소켓 계층(SSL)은 지원하지 않습니다.  Log Analytics로 전송되는 데이터의 보안을 보장하려면 최소 TLS 1.2를 사용하도록 게이트웨이를 구성합니다. 이전 버전의 TLS 또는 SSL은 취약합니다. 현재 이전 버전과의 호환성을 허용하지만 사용하지 마십시오.  

자세한 내용은 [TLS 1.2를 사용하여 안전하게 데이터 보내기](../../azure-monitor/platform/data-security.md#sending-data-securely-using-tls-12)를 검토하세요. 

### <a name="supported-number-of-agent-connections"></a>지원되는 에이전트 연결 수

다음 표에서는 게이트웨이 서버와 통신할 수 있는 에이전트 수를 대략보여 두는 것입니다. 지원은 6초마다 약 200KB의 데이터를 업로드하는 에이전트를 기반으로 합니다. 테스트된 각 에이전트에 대해 데이터 볼륨은 하루에 약 2.7GB입니다.

|게이트웨이 |지원되는 에이전트(대략적인)|  
|--------|----------------------------------|  
|CPU: 인텔 제온 프로세서 E5-2660 v3 \@ 2.6 GHz 2 코어<br> 메모리: 4GB<br> 네트워크 대역폭: 1Gbps| 600|  
|CPU: 인텔 제온 프로세서 E5-2660 v3 \@ 2.6 GHz 4 코어<br> 메모리: 8GB<br> 네트워크 대역폭: 1Gbps| 1000|  

## <a name="download-the-log-analytics-gateway"></a>Log Analytics 게이트웨이 다운로드

Microsoft 다운로드[센터(다운로드 링크)](https://go.microsoft.com/fwlink/?linkid=837444)또는 Azure 포털에서 Log Analytics 게이트웨이 설치 파일의 최신 버전을 가져옵니다.

Azure 포털에서 Log Analytics 게이트웨이를 얻으려면 다음 단계를 따르십시오.

1. 서비스 목록을 찾아본 다음 **Log Analytics**를 선택합니다. 
1. 작업 영역을 선택합니다.
1. **일반**의 작업 영역 블레이드에서 **빠른 시작**을 선택합니다. 
1. **작업 영역에 연결하는 데이터 원본 선택**에서 **컴퓨터**를 선택합니다.
1. 직접 **에이전트** 블레이드에서 **로그 분석 게이트웨이 다운로드를**선택합니다.
 
   ![로그 분석 게이트웨이를 다운로드하는 단계의 스크린샷](./media/gateway/download-gateway.png)

또는 

1. **설정** 아래 작업 영역 블레이드에서 **고급 설정**을 선택합니다.
1. 연결된 **소스** > 로 이동**윈도우 서버** 및 **다운로드 로그 분석 게이트웨이를**선택합니다.

## <a name="install-log-analytics-gateway-using-setup-wizard"></a>설치 마법사를 사용하여 로그 분석 게이트웨이 설치

설치 마법사를 사용하여 게이트웨이를 설치하려면 다음 단계를 따르십시오. 

1. 대상 폴더에서 **Log Analytics gateway.msi**를 두 번 클릭합니다.
1. **Welcome** 페이지에서 **다음**을 선택합니다.

   ![게이트웨이 설치 마법사의 시작 페이지 스크린샷](./media/gateway/gateway-wizard01.png)

1. **사용권 계약** 페이지에서 **사용권 계약의 조건에 동의하여** Microsoft 소프트웨어 사용권 약관에 동의한 다음 **다음을**선택합니다.
1. **포트 및 프록시 주소** 페이지에서 다음 단계를 수행합니다.

   a. 게이트웨이에 사용할 TCP 포트 번호를 입력합니다. 설치 는 이 포트 번호를 사용하여 Windows 방화벽에서 인바운드 규칙을 구성합니다.  기본값은 8080입니다.
      포트 번호의 유효한 범위는 1부터 65535까지입니다. 입력한 내용이 이 범위를 벗어나면 오류 메시지가 표시됩니다.

   b. 게이트웨이가 설치된 서버가 프록시를 통해 통신해야 하는 경우 게이트웨이가 연결해야 하는 프록시 주소를 입력합니다. 예를 들어 다음과 같이 입력합니다. `http://myorgname.corp.contoso.com:80`  이 필드를 비워 두면 게이트웨이가 인터넷에 직접 연결하려고 시도합니다.  프록시 서버에 인증이 필요한 경우 사용자 이름과 암호를 입력합니다.

   다. **다음**을 선택합니다.

   ![게이트웨이 프록시에 대한 구성 스크린샷](./media/gateway/gateway-wizard02.png)

1. Microsoft 업데이트를 사용하도록 설정하지 않은 경우 Microsoft 업데이트 페이지가 나타나고 사용하도록 선택할 수 있습니다. 선택을 한 다음 **다음을**선택합니다. 그렇지 않은 경우 다음 단계를 계속 진행합니다.
1. 대상 **폴더** 페이지에서 기본 폴더 C:\프로그램 파일\OMS 게이트웨이를 그대로 두거나 게이트웨이를 설치하려는 위치를 입력합니다. 그런 다음 을 **선택합니다.**
1. 설치 **준비** 페이지에서 **설치를**선택합니다. 사용자 계정 제어에서 설치 권한을 요청하는 경우 **예**를 선택합니다.
1. 설정이 완료되면 **완료를**선택합니다. 서비스가 실행 중인지 확인하려면 services.msc 스냅인을 열고 **OMS 게이트웨이가** 서비스 목록에 나타나고 해당 상태가 **실행 중인지**확인합니다.

   ![OMS 게이트웨이가 실행 중임을 보여주는 로컬 서비스의 스크린샷](./media/gateway/gateway-service.png)

## <a name="install-the-log-analytics-gateway-using-the-command-line"></a>명령줄을 사용하여 로그 분석 게이트웨이 설치

게이트웨이에 대해 다운로드한 파일은 명령줄 또는 기타 자동화된 메서드에서 자동 설치를 지원하는 Windows Installer 패키지입니다. Windows Installer의 표준 명령줄 옵션에 익숙하지 않은 경우 [명령줄 옵션을](https://docs.microsoft.com/windows/desktop/Msi/command-line-options)참조하십시오.
 
다음 표에서는 설정에서 지원하는 매개 변수를 강조 표시됩니다.

|매개 변수| 메모|
|----------|------| 
|포트 넘버 | 게이트웨이에서 수신할 수 있는 TCP 포트 번호 |
|프록시 | 프록시 서버의 IP 주소 |
|INSTALLDIR | 게이트웨이 소프트웨어 파일의 설치 디렉토리를 지정하는 완전 검증된 경로 |
|USERNAME | 프록시 서버로 인증할 사용자 ID |
|PASSWORD | 프록시로 인증할 사용자 ID의 암호 |
|LicenseAccepted | **1** 값을 지정하여 사용권 계약에 동의하는지 확인합니다. |
|하소트 (주) | 사용자 이름/암호 매개 변수를 지정할 때 **1** 값 지정 |
|하스프록시 | **PROXY** 매개 변수에 대한 IP 주소를 지정할 때 **값 1** 지정 |

게이트웨이를 자동으로 설치하고 특정 프록시 주소로 구성하려면 포트 번호를 입력합니다.

```dos
Msiexec.exe /I "oms gateway.msi" /qn PORTNUMBER=8080 PROXY="10.80.2.200" HASPROXY=1 LicenseAccepted=1 
```

/qn 명령줄 옵션을 사용하면 설치가 숨겨지며/ qb는 자동 설치 중에 설정을 표시합니다.  

프록시를 인증하기 위해 자격 증명을 제공해야 하는 경우 다음을 입력합니다.

```dos
Msiexec.exe /I "oms gateway.msi" /qn PORTNUMBER=8080 PROXY="10.80.2.200" HASPROXY=1 HASAUTH=1 USERNAME="<username>" PASSWORD="<password>" LicenseAccepted=1 
```

설치 후 다음 PowerShell cmdlet을 사용하여 설정이 수락됨(사용자 이름 및 암호 제외)을 확인할 수 있습니다.

- **GET-OMSGatewayConfig** - 게이트웨이가 수신하도록 구성된 TCP 포트를 반환합니다.
- **GET-OMSGatewayRelayProxy** - 통신하도록 구성한 프록시 서버의 IP 주소를 반환합니다.

## <a name="configure-network-load-balancing"></a>네트워크 부하 분산 구성

NLB(Microsoft 네트워크 로드 밸런싱) [또는](../../load-balancer/load-balancer-overview.md)하드웨어 기반 로드 밸런서를 사용하여 네트워크 로드 [밸런싱(NLB)을](https://docs.microsoft.com/windows-server/networking/technologies/network-load-balancing)사용하여 고가용성에 대한 게이트웨이를 구성할 수 있습니다. 부하 분산 장치는 노드 전반에 걸쳐 Log Analytics 에이전트 또는 Operations Manager 관리 서버에서 요청된 연결을 리디렉션하여 트래픽을 관리합니다. 게이트웨이 서버가 하나 다운되면 트래픽은 다른 노드로 리디렉션됩니다.

### <a name="microsoft-network-load-balancing"></a>Microsoft 네트워크 부하 분산

Windows Server 2016 네트워크 부하 분산 클러스터를 설계하고 배포하는 방법을 알아보려면 [네트워크 부하 분산](https://docs.microsoft.com/windows-server/networking/technologies/network-load-balancing)을 참조하세요. 다음 단계에서는 Microsoft 네트워크 부하 분산 클러스터를 구성하는 방법에 대해 설명합니다.  

1. NLB 클러스터의 구성원인 Windows 서버에 관리 계정으로 로그인합니다.  
2. [서버 관리자]에서 [네트워크 부하 분산 관리자]를 열고, **도구**를 클릭한 다음 **네트워크 부하 분산 관리자**를 클릭합니다.
3. Log Analytics 게이트웨이 서버를 설치되어 있는 Microsoft Monitoring Agent와 연결하려면 클러스터의 IP 주소를 마우스 오른쪽 단추로 클릭한 다음, **클러스터에 호스트 추가**를 클릭합니다. 

    ![네트워크 부하 분산 관리자 – 클러스터에 호스트 추가](./media/gateway/nlb02.png)
 
4. 연결하려는 게이트웨이 서버의 IP 주소를 입력합니다. 

    ![네트워크 부하 분산 관리자 – 클러스터에 호스트 추가: 연결](./media/gateway/nlb03.png) 

### <a name="azure-load-balancer"></a>Azure Load Balancer

Azure 로드 밸런서를 디자인하고 배포하는 방법을 알아보려면 [Azure 로드 밸런서란 무엇입니까?](../../load-balancer/load-balancer-overview.md) 기본 로드 밸러커를 배포하려면 **백 엔드 서버 만들기**섹션에 설명된 단계를 제외한 이 빠른 [시작에](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) 설명된 단계를 따릅니다.   

> [!NOTE]
> **기본 SKU를**사용하여 Azure 로드 밸런서를 구성하려면 Azure 가상 컴퓨터가 가용성 집합에 속해야 합니다. 가용성 집합에 대한 자세한 내용은 [Azure의 Windows 가상 컴퓨터 가용성 관리를](../../virtual-machines/windows/manage-availability.md)참조하십시오. 가용성 집합에 기존 가상 컴퓨터를 추가하려면 [Azure 리소스 관리자 VM 가용성 집합 을](https://gallery.technet.microsoft.com/Set-Azure-Resource-Manager-f7509ec4)참조하십시오.
> 

로드 밸러버를 만든 후 하나 이상의 게이트웨이 서버에 트래픽을 분산하는 백 엔드 풀을 만들어야 합니다. 빠른 시작 문서 섹션에 설명된 단계를 [따르십시오로드 밸러터에 대한 리소스 만들기.](../../load-balancer/quickstart-load-balancer-standard-public-portal.md)  

>[!NOTE]
>상태 프로브를 구성할 때 게이트웨이 서버의 TCP 포트를 사용하도록 구성해야 합니다. 상태 프로브는 상태 확인에 대한 응답에 따라 부하 분산 장치 회전에서 게이트웨이 서버를 동적으로 추가하거나 제거합니다. 
>

## <a name="configure-the-log-analytics-agent-and-operations-manager-management-group"></a>로그 분석 에이전트 및 운영 관리자 관리 그룹 구성

이 섹션에서는 직접 연결된 로그 분석 에이전트, 운영 관리자 관리 그룹 또는 Azure 자동화 하이브리드 Runbook 작업자를 로그 분석 게이트웨이를 사용하여 Azure 자동화 또는 로그 분석과 통신하도록 구성하는 방법을 알아봅니다.  

### <a name="configure-a-standalone-log-analytics-agent"></a>독립 실행형 로그 분석 에이전트 구성

로그 분석 에이전트를 구성할 때 프록시 서버 값을 Log Analytics 게이트웨이 서버의 IP 주소와 포트 번호로 바꿉니다. 로드 밸런서 뒤에 여러 게이트웨이 서버를 배포한 경우 Log Analytics 에이전트 프록시 구성은 로드 밸런서의 가상 IP 주소입니다.  

>[!NOTE]
>로그 분석에 직접 연결하는 게이트웨이 및 Windows 컴퓨터에 로그 분석 에이전트를 설치하려면 [Windows 컴퓨터를 Azure의 로그 분석 서비스에 연결](agent-windows.md)참조하세요. Linux 컴퓨터를 연결하려면 [Linux 컴퓨터를 Azure 모니터에 연결합니다.](agent-linux.md) 
>

게이트웨이 서버에 에이전트를 설치한 후 게이트웨이와 통신하는 작업 영역 또는 작업 영역 에이전트에 보고하도록 구성합니다. Log Analytics Windows 에이전트가 게이트웨이에 설치되지 않은 경우 이벤트 300이 OMS 게이트웨이 이벤트 로그에 기록되어 에이전트를 설치해야 함을 나타냅니다. 에이전트가 설치되었지만 이를 통해 통신하는 에이전트와 동일한 작업 공간에 보고하도록 구성되지 않은 경우 이벤트 105가 동일한 로그에 기록되어 게이트웨이의 에이전트가 에이전트와 동일한 작업 영역에 보고하도록 구성되어야 합니다. 게이트웨이와 통신할 수 있습니다.

구성을 완료한 후 **OMS 게이트웨이** 서비스를 다시 시작하여 변경 내용을 적용합니다. 그렇지 않으면 게이트웨이는 Log Analytics와 통신을 시도하는 에이전트를 거부하고 OMS 게이트웨이 이벤트 로그에서 이벤트 105를 보고합니다. 게이트웨이 서버의 에이전트 구성에서 작업 영역을 추가하거나 제거할 때도 이 와같은 일이 발생합니다.

자동화 하이브리드 Runbook 작업자와 관련된 정보는 [하이브리드 Runbook Worker를 사용하여 데이터 센터 또는 클라우드의 리소스 자동화를](../../automation/automation-hybrid-runbook-worker.md)참조하십시오.

### <a name="configure-operations-manager-where-all-agents-use-the-same-proxy-server"></a>모든 에이전트가 동일한 프록시 서버를 사용하는 운영 관리자 구성

설정이 비어 있더라도 Operations Manager 프록시 구성은 Operations Manager에 보고하는 모든 에이전트에 자동으로 적용됩니다.  

OMS 게이트웨이를 사용하여 운영 관리자를 지원하려면 다음이 있어야 합니다.

* OMS 게이트웨이 서버에 설치된 Microsoft 모니터링 에이전트(버전 8.0.10900.0 이상)는 관리 그룹이 보고하도록 구성된 동일한 로그 분석 작업 영역으로 구성됩니다.
* 인터넷 연결. 또는 OMS 게이트웨이가 인터넷에 연결된 프록시 서버에 연결되어 있어야 합니다.

> [!NOTE]
> 게이트웨이에 대한 값을 지정하지 않으면 빈 값이 모든 에이전트에 푸시됩니다.
>

Operations Manager 관리 그룹이 처음으로 Log Analytics 작업 영역에 등록하는 경우 운영 콘솔에서 관리 그룹에 대한 프록시 구성을 지정하는 옵션이 표시되지 않습니다. 이 옵션은 관리 그룹이 서비스에 등록된 경우에만 사용할 수 있습니다.  

통합을 구성하려면 운영 콘솔을 실행하는 시스템 및 관리 그룹의 모든 관리 서버에서 Netsh를 사용하여 시스템 프록시 구성을 업데이트합니다. 다음 단계를 수행하세요.

1. 상승된 명령 프롬프트 를 엽니다.

   a. **시작을** 선택하고 **cmd를**입력합니다.  

   b. **명령 프롬프트를** 마우스 오른쪽 단추로 클릭하고 **관리자로 실행을**선택합니다.  

1. 다음 명령을 입력합니다.

   `netsh winhttp set proxy <proxy>:<port>`

로그 애널리틱스와의 통합을 완료한 후 을 실행하여 `netsh winhttp reset proxy`변경 을 제거합니다. 그런 다음 운영 콘솔에서 **프록시 서버 구성** 옵션을 사용하여 Log Analytics 게이트웨이 서버를 지정합니다. 

1. 운영 관리자 콘솔에서 **운영 관리 제품군에서** **연결을**선택한 다음 **프록시 서버 구성을**선택합니다.

   ![프록시 서버 구성 선택 표시 작업 관리자의 스크린샷](./media/gateway/scom01.png)

1. **프록시 서버 사용을 선택하여 운영 관리 제품군에 액세스한** 다음 Log Analytics 게이트웨이 서버의 IP 주소 또는 로드 밸런서의 가상 IP 주소를 입력합니다. 접두사로 `http://`시작하십시오.

   ![프록시 서버 주소를 표시하는 운영 관리자의 스크린샷](./media/gateway/scom02.png)

1. **마침**을 선택합니다. 이제 Operations Manager 관리 그룹이 게이트웨이 서버를 통해 Log Analytics 서비스와 통신하도록 구성되었습니다.

### <a name="configure-operations-manager-where-specific-agents-use-a-proxy-server"></a>특정 에이전트가 프록시 서버를 사용하는 운영 관리자 구성

대규모 또는 복잡한 환경의 경우 특정 서버(또는 그룹)만 Log Analytics 게이트웨이 서버를 사용하도록 할 수 있습니다.  이러한 서버의 경우 이 값이 관리 그룹의 전역 값으로 덮어쓰므로 Operations Manager 에이전트를 직접 업데이트할 수 없습니다.  대신 이러한 값을 푸시하는 데 사용되는 규칙을 재정의합니다.  

> [!NOTE] 
> 사용자 환경에서 여러 Log Analytics 게이트웨이 서버를 허용하려는 경우 이 구성 기술을 사용합니다.  예를 들어 특정 Log Analytics 게이트웨이 서버를 지역별로 지정하도록 요구할 수 있습니다.
>

Log Analytics 게이트웨이 서버를 사용하도록 특정 서버 또는 그룹을 구성하려면 다음을 수행하십시오. 

1. Operations Manager 콘솔을 열고 **작성** 작업 영역을 선택합니다.  
1. 제작 작업 영역에서 **규칙**을 선택합니다. 
1. 작업 관리자 도구 모음에서 **범위** 단추를 선택합니다. 이 단추를 사용할 수 없는 경우 **모니터링** 창에서 폴더가 아닌 개체를 선택했는지 확인합니다. **관리 팩 개체 범위 지정** 대화 상자에서 일반적인 대상 클래스, 그룹 또는 개체의 목록을 표시합니다. 
1. **찾기** 필드에서 **상태 서비스를** 입력하고 목록에서 선택합니다. **확인**을 선택합니다.  
1. 어드바이저 **프록시 설정 규칙을**검색합니다. 
1. 작업 관리자 도구 모음에서 **재정의를** 선택한 다음 **규칙\클래스의 특정 개체인 상태 서비스** 및 목록에서 개체를 선택하도록 가리킵니다.  또는 이 재정의를 적용할 서버의 상태 서비스 개체가 포함된 사용자 지정 그룹을 만듭니다. 그런 다음 사용자 지정 그룹에 재정의를 적용합니다.
1. 속성 **재정의** 대화 상자에서 **WebProxyAddress** 매개 변수 옆에 **재정의** 열에 확인 표시를 추가합니다.  값 **재정의** 필드에 로그 분석 게이트웨이 서버의 URL을 입력합니다. 접두사로 `http://`시작하십시오.  

    >[!NOTE]
    > 규칙을 사용하도록 설정하지 않아도 됩니다. Microsoft 시스템 센터 관리자 모니터링 서버 그룹을 대상으로 하는 Microsoft 시스템 센터 관리자 보안 참조 재정의 관리 팩에서 재정의를 통해 이미 자동으로 관리됩니다.
    > 

1. 대상 관리 팩 **선택** 목록에서 관리 팩을 선택하거나 **새**을 선택하여 봉인되지 않은 관리 팩을 새로 만듭니다. 
1. 완료하면 **확인**을 선택합니다. 

### <a name="configure-for-automation-hybrid-runbook-workers"></a>자동화 하이브리드 Runbook 작업자를 위한 구성

사용자 환경에 자동화 하이브리드 Runbook 작업자가 있는 경우 다음 단계에 따라 작업자를 지원하도록 게이트웨이를 구성합니다.

각 지역의 URL을 찾으려면 자동화 설명서의 [네트워크 구성](../../automation/automation-hybrid-runbook-worker.md#network-planning) 섹션을 참조하십시오.

컴퓨터가 하이브리드 Runbook 작업자로 자동으로 등록된 경우(예: 하나 이상의 VM에 대해 업데이트 관리 솔루션이 활성화된 경우) 다음 단계를 따르십시오.

1. 작업 런타임 데이터 서비스 URL을 Log Analytics 게이트웨이의 허용된 호스트 목록에 추가합니다. 예: `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
1. `Restart-Service OMSGatewayService` PowerShell cmdlet을 사용하여 Log Analytics 게이트웨이 서비스를 다시 시작합니다.

하이브리드 Runbook 작업자 등록 cmdlet을 사용하여 컴퓨터가 Azure 자동화에 조인된 경우 다음 단계를 따르십시오.

1. 에이전트 서비스 등록 URL을 Log Analytics 게이트웨이의 허용된 호스트 목록에 추가합니다. 예: `Add-OMSGatewayAllowedHost ncus-agentservice-prod-1.azure-automation.net`
1. 작업 런타임 데이터 서비스 URL을 Log Analytics 게이트웨이의 허용된 호스트 목록에 추가합니다. 예: `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
1. Log Analytics 게이트웨이 서비스를 다시 시작합니다.
    `Restart-Service OMSGatewayService`

## <a name="useful-powershell-cmdlets"></a>유용한 PowerShell cmdlet

cmdlet을 사용하여 작업을 완료하여 Log Analytics 게이트웨이의 구성 설정을 업데이트할 수 있습니다. cmdlet을 사용하기 전에 다음을 수행해야 합니다.

1. 로그 분석 게이트웨이(Microsoft Windows 설치 관리자)를 설치합니다.
1. PowerShell 콘솔 창을 엽니다.
1. 이 명령을 입력하여 모듈을 가져옵니다.`Import-Module OMSGateway`
1. 이전 단계에서 오류가 발생하지 않은 경우 모듈을 성공적으로 가져왔으며 cmdlet을 사용할 수 있습니다. `Get-Module OMSGateway`을 입력합니다.
1. cmdlet을 사용하여 변경한 후 OMS 게이트웨이 서비스를 다시 시작합니다.

3단계에서 오류가 발생하면 모듈이 가져오지 않았음을 의미합니다. PowerShell모듈을 찾을 수 없을 때 오류가 발생할 수 있습니다. OMS 게이트웨이 설치 경로에서 모듈을 찾을 수 *있습니다.*

| **Cmdlet** | **매개 변수** | **설명** | **예제** |
| --- | --- | --- | --- |  
| `Get-OMSGatewayConfig` |Key |서비스 구성 가져오기 |`Get-OMSGatewayConfig` |  
| `Set-OMSGatewayConfig` |키(필수) <br> 값 |서비스 구성 변경 |`Set-OMSGatewayConfig -Name ListenPort -Value 8080` |  
| `Get-OMSGatewayRelayProxy` | |릴레이(업스트림) 프록시 주소 가져오기 |`Get-OMSGatewayRelayProxy` |  
| `Set-OMSGatewayRelayProxy` |주소<br> 사용자 이름<br> 암호(보안 문자열) |릴레이(업스트림) 프록시 주소(및 자격 증명) 설정 |1. 릴레이 프록시 및 자격 증명 설정:<br> `Set-OMSGatewayRelayProxy`<br>`-Address http://www.myproxy.com:8080`<br>`-Username user1 -Password 123` <br><br> 2. 인증이 필요하지 않은 릴레이 프록시설정:`Set-OMSGatewayRelayProxy`<br> `-Address http://www.myproxy.com:8080` <br><br> 3. 릴레이 프록시 설정을 지웁니까?<br> `Set-OMSGatewayRelayProxy` <br> `-Address ""` |  
| `Get-OMSGatewayAllowedHost` | |현재 허용되는 호스트를 가져옵니다(로컬로 구성된 허용된 호스트만, 자동으로 다운로드된 허용 된 호스트). |`Get-OMSGatewayAllowedHost` | 
| `Add-OMSGatewayAllowedHost` |호스트(필수) |호스트를 허용 목록에 추가 |`Add-OMSGatewayAllowedHost -Host www.test.com` |  
| `Remove-OMSGatewayAllowedHost` |호스트(필수) |호스트를 허용 목록에서 제거 |`Remove-OMSGatewayAllowedHost`<br> `-Host www.test.com` |  
| `Add-OMSGatewayAllowedClientCertificate` |주체(필수) |클라이언트 인증서 주체를 허용 목록에 추가 |`Add-OMSGatewayAllowed`<br>`ClientCertificate` <br> `-Subject mycert` |  
| `Remove-OMSGatewayAllowedClientCertificate` |주체(필수) |클라이언트 인증서 주체를 허용 목록에서 제거 |`Remove-OMSGatewayAllowed` <br> `ClientCertificate` <br> `-Subject mycert` |  
| `Get-OMSGatewayAllowedClientCertificate` | |현재 허용되는 클라이언트 인증서 주체(로컬로 구성된 허용된 주체만, 허용된 주체를 자동으로 다운로드하지 않음) |`Get-`<br>`OMSGatewayAllowed`<br>`ClientCertificate` |  

## <a name="troubleshooting"></a>문제 해결

게이트웨이에서 기록된 이벤트를 수집하려면 Log Analytics 에이전트가 설치되어 있어야 합니다.

![로그 분석 게이트웨이 로그의 이벤트 뷰어 목록 스크린샷](./media/gateway/event-viewer.png)

### <a name="log-analytics-gateway-event-ids-and-descriptions"></a>로그 분석 게이트웨이 이벤트 아이디 및 설명

다음 표에서는 Log Analytics 게이트웨이 로그 이벤트에 대한 이벤트 아이디 및 설명을 보여 주었습니다.

| **ID** | **설명** |
| --- | --- |
| 400 |특정 ID가 없는 모든 응용 프로그램 오류입니다. |
| 401 |잘못된 구성. 예를 들어, listenPort = 정수 대신 "텍스트". |
| 402 |TLS 핸드셰이크 메시지 구문 분석 중 예외 |
| 403 |네트워킹 오류. 예를 들어 대상 서버에 연결할 수 없습니다. |
| 100 |일반 정보 |
| 101 |서비스가 시작됨 |
| 102 |서비스가 중지됨 |
| 103 |클라이언트에서 HTTP CONNECT 명령을 받았습니다. |
| 104 |HTTP CONNECT 명령이 아님 |
| 105 |대상 서버가 허용된 목록에 없거나 대상 포트가 안전하지 않습니다(443). <br> <br> OMS 게이트웨이 서버의 MMA 에이전트와 OMS 게이트웨이와 통신하는 에이전트가 동일한 로그 분석 작업 영역에 연결되어 있는지 확인합니다. |
| 105 |오류 TcpConnection - 잘못된 클라이언트 인증서: CN=게이트웨이. <br><br> OMS 게이트웨이 버전 1.0.395.0 이상을 사용하고 있는지 확인합니다. 또한 OMS 게이트웨이 서버의 MMA 에이전트와 OMS 게이트웨이와 통신하는 에이전트가 동일한 로그 분석 작업 영역에 연결되어 있는지 확인합니다. |
| 106 |지원되지 않는 TLS/SSL 프로토콜 버전입니다.<br><br> 로그 분석 게이트웨이는 TLS 1.0, TLS 1.1 및 1.2만 지원합니다. SSL은 지원하지 않습니다.|
| 107 |TLS 세션이 확인되었습니다. |

### <a name="performance-counters-to-collect"></a>수집할 성능 카운터

다음 테이블은 Log Analytics 게이트웨이에 사용할 수 있는 성능 카운터를 보여줍니다. 성능 모니터를 사용하여 카운터를 추가합니다.

| **이름** | **설명** |
| --- | --- |
| Log Analytics 게이트웨이/활성 클라이언트 연결 |활성 클라이언트 네트워크(TCP) 연결의 수 |
| Log Analytics 게이트웨이/오류 수 |오류 수 |
| Log Analytics 게이트웨이/연결된 클라이언트 |연결된 클라이언트 수 |
| Log Analytics 게이트웨이/오류 수 |TLS 유효성 검사 오류로 인한 거부 횟수 |

![성능 카운터를 보여주는 Log Analytics 게이트웨이 인터페이스의 스크린샷](./media/gateway/counters.png)

## <a name="assistance"></a>지원

Azure 포털에 로그인하면 Log Analytics 게이트웨이 또는 기타 Azure 서비스 또는 기능에 대한 도움을 받을 수 있습니다.
도움을 받으려면 포털의 오른쪽 상단에 있는 물음표 아이콘을 선택하고 **새 지원 요청을**선택합니다. 그런 다음 새 지원 요청 양식을 작성합니다.

![새 지원 요청의 스크린샷](./media/gateway/support.png)

## <a name="next-steps"></a>다음 단계

연결된 원본에서 데이터를 수집하고 로그 애널리틱스 작업 영역에 데이터를 저장하는 [데이터 원본을 추가합니다.](../../azure-monitor/platform/agent-data-sources.md)
