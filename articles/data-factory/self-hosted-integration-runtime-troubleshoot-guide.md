---
title: Azure Data Factory에서 자체 호스팅 Integration Runtime 문제 해결
description: Azure Data Factory에서 자체 호스팅 Integration Runtime 문제를 해결하는 방법을 알아봅니다.
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 06/24/2020
ms.author: abnarain
ms.openlocfilehash: e77d621d5699c434e691de0a523e58e49166d8d6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85315143"
---
# <a name="troubleshoot-self-hosted-integration-runtime"></a>자체 호스팅 Integration Runtime 문제 해결

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory에서 자체 호스팅 Integration Runtime에 대한 일반적인 문제 해결 방법을 살펴봅니다.

## <a name="common-errors-and-resolutions"></a>일반적인 오류 및 해결 방법

### <a name="error-message"></a>오류 메시지: 

`Self-hosted integration runtime can't connect to cloud service`

![자체 호스팅 IR 연결 문제](media/self-hosted-integration-runtime-troubleshoot-guide/unable-to-connect-to-cloud-service.png)

#### <a name="cause"></a>원인 

자체 호스팅 Integration Runtime에서 Data Factory 서비스(백 엔드)에 연결할 수 없습니다. 이 문제는 일반적으로 방화벽의 네트워크 설정 때문에 발생합니다.

#### <a name="resolution"></a>해결 방법

1. Integration Runtime 서비스가 실행 중인지 확인합니다.
    
   ![자체 호스팅 IR 서비스 실행 상태](media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-running-status.png)
    
1. 이 서비스가 실행되는 경우에는 3단계를 계속 진행합니다.

1. 자체 호스팅 Integration Runtime(기본 설정)에 프록시가 구성되어 있지 않으면 자체 호스팅 Integration Runtime이 설치된 머신에서 다음 PowerShell 명령을 실행합니다.

    ```powershell
    (New-Object System.Net.WebClient).DownloadString("https://wu2.frontend.clouddatahub.net/")
    ```
        
   > [!NOTE]     
   > 서비스 URL은 Data Factory 위치에 따라 다를 수 있습니다. **ADF UI** > **연결** > **Integration Runtime** > **셀프 호스팅 IR 편집** > **노드** > **서비스 URL 보기**에서 서비스 URL을 찾을 수 있습니다.
            
    예상되는 응답은 다음과 같습니다.
            
    ![PowerShell 명령 응답](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)
            
1. 예상되는 응답을 받지 못한 경우에는 상황에 따라 다음 방법 중 하나를 사용합니다.
            
    * "원격 이름을 확인할 수 없습니다"라는 메시지가 표시되는 경우 DNS(Domain Name System) 문제가 있는 것입니다. 이 문제를 해결하려면 네트워크 팀에 문의하세요.
    * "ssl/tls 인증서를 신뢰할 수 없습니다"라는 메시지가 표시되면 머신에서 https://wu2.frontend.clouddatahub.net/ 에 대한 인증서를 신뢰할 수 있는지 확인하고 인증서 관리자를 사용하여 퍼블릭 인증서를 설치합니다. 이 작업을 수행하면 문제가 완화됩니다.
    * **Windows** > **이벤트 뷰어(로그)**  > **애플리케이션 및 서비스 로그** > **Integration Runtime**으로 이동하고 DNS, 방화벽 규칙 또는 회사 네트워크 설정으로 인해 발생한 오류를 확인합니다. 이러한 오류가 발생하면 연결을 강제로 닫습니다. 모든 회사에서 네트워크 설정을 사용자 지정했으므로 네트워크 팀에 문의하여 이러한 문제를 해결하세요.

1. 자체 호스팅 Integration Runtime에 "프록시"가 구성된 경우 프록시 서버에서 서비스 엔드포인트에 액세스할 수 있는지 확인합니다. 샘플 명령은 [PowerShell, 웹 요청 및 프록시](https://stackoverflow.com/questions/571429/powershell-web-requests-and-proxies)를 참조하세요.    
                
    ```powershell
    $user = $env:username
    $webproxy = (get-itemproperty 'HKCU:\Software\Microsoft\Windows\CurrentVersion\Internet
    Settings').ProxyServer
    $pwd = Read-Host "Password?" -assecurestring
    $proxy = new-object System.Net.WebProxy
    $proxy.Address = $webproxy
    $account = new-object System.Net.NetworkCredential($user,[Runtime.InteropServices.Marshal]::PtrToStringAuto([Runtime.InteropServices.Marshal]::SecureStringToBSTR($pwd)), "")
    $proxy.credentials = $account
    $url = "https://wu2.frontend.clouddatahub.net/"
    $wc = new-object system.net.WebClient
    $wc.proxy = $proxy
    $webpage = $wc.DownloadData($url)
    $string = [System.Text.Encoding]::ASCII.GetString($webpage)
    $string
    ```

예상되는 응답은 다음과 같습니다.
            
![PowerShell 명령 응답 2](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)

> [!NOTE] 
> 프록시 고려 사항:
> *    수신 허용 - 받는 사람 목록에 프록시 서버를 추가해야 하는지 여부를 확인합니다. 추가해야 하는 경우 [이러한 도메인](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations#firewall-requirements-for-on-premisesprivate-network)이 수신 허용 - 받는 사람 목록에 있는지 확인합니다.
> *    프록시 서버에서 TLS/SSL 인증서 "wu2.frontend.clouddatahub.net/"을 신뢰할 수 있는지 확인합니다.
> *    프록시에서 Active Directory 인증을 사용하는 경우 서비스 계정을 프록시에 "Integration Runtime Service"로 액세스할 수 있는 사용자 계정으로 변경합니다.

### <a name="error-message"></a>오류 메시지: 
`Self-hosted integration runtime node/ logical SHIR is in Inactive/ "Running (Limited)" state`

#### <a name="cause"></a>원인 

다음 스크린샷에 표시된 것처럼 자체 호스팅 Integration Runtime 노드가 **비활성** 상태일 수 있습니다.

![비활성 자체 호스팅 IR 노드](media/self-hosted-integration-runtime-troubleshoot-guide/inactive-self-hosted-ir-node.png)

이 동작은 노드가 서로 통신할 수 없는 경우에 발생합니다.

#### <a name="resolution"></a>해결 방법

1. 노드 호스팅 VM에 로그인합니다. **애플리케이션 및 서비스 로그** > **Integration Runtime**에서 이벤트 뷰어을 열고 모든 오류 로그를 필터링합니다.

1. 오류 로그에 다음 오류가 포함되어 있는지 확인합니다. 
    
    ```
    System.ServiceModel.EndpointNotFoundException: Could not connect to net.tcp://xxxxxxx.bwld.com:8060/ExternalService.svc/WorkerManager. The connection attempt lasted for a time span of 00:00:00.9940994. TCP error code 10061: No connection could be made because the target machine actively refused it 10.2.4.10:8060. 
    System.Net.Sockets.SocketException: No connection could be made because the target machine actively refused it. 
    10.2.4.10:8060
    at System.Net.Sockets.Socket.DoConnect(EndPoint endPointSnapshot, SocketAddress socketAddress)
    at System.Net.Sockets.Socket.Connect(EndPoint remoteEP)
    at System.ServiceModel.Channels.SocketConnectionInitiator.Connect(Uri uri, TimeSpan timeout)
    ```
       
1. 이 오류가 표시 되 면 명령줄에서 다음을 실행 합니다. 

   ```
   telnet 10.2.4.10 8060
   ```
   
1. 다음 오류가 표시 되는 경우이 문제를 해결 하는 데 도움이 필요 하면 IT 부서에 문의 하세요. 성공적으로 텔넷 된 후에도 통합 런타임 노드 상태에 문제가 있는 경우 Microsoft 지원에 문의 하세요.
        
   ![명령줄 오류](media/self-hosted-integration-runtime-troubleshoot-guide/command-line-error.png)
        
1. 오류 로그에 다음이 포함 되어 있는지 확인 합니다.

    ```
    Error log: Cannot connect to worker manager: net.tcp://xxxxxx:8060/ExternalService.svc/ No DNS entries exist for host azranlcir01r1. No such host is known Exception detail: System.ServiceModel.EndpointNotFoundException: No DNS entries exist for host xxxxx. ---> System.Net.Sockets.SocketException: No such host is known at System.Net.Dns.GetAddrInfo(String name) at System.Net.Dns.InternalGetHostByName(String hostName, Boolean includeIPv6) at System.Net.Dns.GetHostEntry(String hostNameOrAddress) at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri) --- End of inner exception stack trace --- Server stack trace: at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri)
    ```
    
1. 이 문제를 해결 하려면 다음 방법 중 하나 또는 모두를 시도 합니다.
    - 모든 노드를 동일한 도메인에 배치 합니다.
    - 호스트 된 모든 VM의 호스트 파일에 있는 호스트에 IP를 추가 합니다.


## <a name="troubleshoot-connectivity-issue"></a>연결 문제 해결

### <a name="troubleshoot-connectivity-issue-between-self-hosted-ir-and-data-factory-or-self-hosted-ir-and-data-sourcesink"></a>자체 호스팅 IR과 Data Factory 또는 자체 호스팅 IR 및 데이터 원본/싱크 간의 연결 문제 해결

네트워크 연결 문제를 해결 하려면 [네트워크 추적을 수집](#how-to-collect-netmon-trace)하 고,이를 사용 하는 방법을 이해 하 고, 실제 사례에서 netmon 도구를 자체 호스팅 IR에서 적용 하기 전에 [netmon 추적을 분석](#how-to-analyze-netmon-trace) 하는 방법을 알고 있어야 합니다.

경우에 따라 자체 호스팅 IR 및 Data Factory 간의 연결 문제를 해결 하는 경우는 다음과 같습니다. 

![HTTP 요청 실패](media/self-hosted-integration-runtime-troubleshoot-guide/http-request-error.png)

또는 자체 호스팅 IR 및 데이터 원본/싱크 사이에 있는 경우 다음 오류가 발생 합니다.

**오류 메시지:**
`Copy failed with error:Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Cannot connect to SQL Server: ‘IP address’`

**오류 메시지:**
`One or more errors occurred. An error occurred while sending the request. The underlying connection was closed: An unexpected error occurred on a receive. Unable to read data from the transport connection: An existing connection was forcibly closed by the remote host. An existing connection was forcibly closed by the remote host Activity ID.`

**해결 방법:** 위의 문제가 발생할 경우 다음 지침을 참조 하 여 문제를 해결 하십시오.

Netmon 추적을 사용 하 고 추가로 분석 합니다.
- 먼저 필터를 설정 하 여 서버에서 클라이언트 쪽으로 다시 설정 하는 것을 볼 수 있습니다. 아래 예제에서 서버 쪽이 서버 Data Factory 것을 볼 수 있습니다.

    ![데이터 팩터리 서버](media/self-hosted-integration-runtime-troubleshoot-guide/data-factory-server.png)

- 다시 설정 패키지를 가져오는 경우 TCP를 따라 대화를 찾을 수 있습니다.

    ![대화 찾기](media/self-hosted-integration-runtime-troubleshoot-guide/find-conversation.png)

- 그런 다음 필터를 제거 하 여 클라이언트와 Data Factory 서버 간의 변환을 가져올 수 있습니다.

    ![대화 가져오기](media/self-hosted-integration-runtime-troubleshoot-guide/get-conversation.png)
- 수집 된 netmon 추적에 따라 TTL (TimeToLive) total이 64 임을 알 수 있습니다. [이 문서](https://packetpushers.net/ip-time-to-live-and-hop-limit-basics/) 에 언급 된 **기본 TTL 및 홉 제한 값** (아래 추출 된)에 따라 패키지를 다시 설정 하 고 연결을 끊는 Linux 시스템을 확인할 수 있습니다.

    기본 TTL 및 홉 제한 값은 다양 한 운영 체제에 따라 달라 집니다. 몇 가지 기본값은 다음과 같습니다.
    - Linux 커널 2.4 (년경 2001): TCP, UDP 및 ICMP 용 255
    - Linux 커널 4.10 (2015): TCP, UDP 및 ICMP 용 64
    - Windows XP (2001): 128 (TCP, UDP 및 ICMP)
    - Windows 10 (2015): 128 (TCP, UDP 및 ICMP)
    - Windows Server 2008: TCP, UDP 및 ICMP 용 128
    - Windows Server 2019 (2018): TCP, UDP 및 ICMP 용 128
    - macOS (2001): TCP, UDP 및 ICMP 용 64

    ![TTL 61](media/self-hosted-integration-runtime-troubleshoot-guide/ttl-61.png)
    
    그러나이는 네트워크 패키지가 대상에 도달할 때 라우터/네트워크 장치와 같은 다른 홉을 통과 해야 하기 때문에 위의 예제에서 64 대신 61로 표시 됩니다. 라우터/네트워크 장치의 수는 최종 TTL에서 공제 됩니다.
    이 경우 TTL 64을 사용 하 여 Linux 시스템에서 재설정이 전송 될 수 있다는 것을 알 수 있습니다.

- 다시 설정 장치를 가져올 수 있는 위치를 확인 하려면 자체 호스팅 IR에서 네 번째 홉을 확인 해야 합니다.
 
    *Linux System A의 네트워크 패키지 TTL 64-> B TTL 64-1 = 63-> C TTL 63-1 = 62-> TTL 62 빼기 1 = 61 자체 호스팅 IR*

- 이상적인 상황에서 TTL은 128이 되며,이는 Windows 시스템에서 Data Factory를 실행 하는 것을 의미 합니다. 아래 예제에 표시 된 것 처럼 *128 – 107 = 21 홉*이 있습니다. 즉, TCP 3 핸드셰이크 중에 패키지에 대 한 21 개의 홉을 Data Factory에서 자체 호스팅 IR로 보냈습니다.
 
    ![TTL 107](media/self-hosted-integration-runtime-troubleshoot-guide/ttl-107.png)

    따라서 네트워크 팀과 협력 하 여 네 번째 홉이 자체 호스팅 IR에 있는지 확인 해야 합니다. Linux 시스템의 방화벽 인 경우 장치에서 TCP 3 핸드셰이크 후에 패키지를 다시 설정 하는 이유에 대 한 로그를 확인 합니다. 그러나 조사를 수행할 위치를 모를 경우에는 문제가 있는 시간에 자체 호스트 된 IR 및 방화벽에서 netmon 추적을 가져와이 패키지를 다시 설정 하 고 연결을 끊는 장치를 파악 합니다. 이 경우 네트워크 팀과 협력 하 여 앞으로 이동 해야 합니다.

### <a name="how-to-collect-netmon-trace"></a>Netmon 추적을 수집 하는 방법

1.  [이 웹 사이트](https://www.microsoft.com/en-sg/download/details.aspx?id=4865)에서 Netmon 도구를 다운로드 하 고 서버 컴퓨터 (문제가 있는 서버) 및 클라이언트 (예: 자체 호스팅 IR)에 설치 합니다.

2.  다음 경로에서 폴더를 만듭니다. *D:\netmon*. 로그를 저장 하는 데 충분 한 공간이 있는지 확인 합니다.

3.  IP 및 포트 정보를 캡처합니다. 
    1. CMD 프롬프트를 시작 합니다.
    2. 관리자 권한으로 실행을 선택 하 고 다음 명령을 실행 합니다.
       
        ```
        Ipconfig /all >D:\netmon\IP.txt
        netstat -abno > D:\netmon\ServerNetstat.txt
        ```

4.  Netmon 추적 (네트워크 패키지)을 캡처합니다.
    1. CMD 프롬프트를 시작 합니다.
    2. 관리자 권한으로 실행을 선택 하 고 다음 명령을 실행 합니다.
        
        ```
        cd C:\Program Files\Microsoft Network Monitor 3
        ```
    3. 세 가지 명령을 사용 하 여 네트워크 페이지를 캡처할 수 있습니다.
        - 옵션 A: 라운드 로빈 File 명령 (이 경우 하나의 파일만 캡처하고 이전 로그를 덮어씁니다.)

            ```
            nmcap /network * /capture /file D:\netmon\ServerConnection.cap:200M
            ```         
        - 옵션 B: 연결 된 파일 명령 (200 MB에 도달 하면 새 파일이 생성 됨).
        
            ```
            nmcap /network * /capture /file D:\netmon\ServerConnection.chn:200M
            ```          
        - 옵션 C: 예약 된 파일 명령입니다.

            ```
            nmcap /network * /capture /StartWhen /Time 10:30:00 AM 10/28/2011 /StopWhen /Time 11:30:00 AM 10/28/2011 /file D:\netmon\ServerConnection.chn:200M
            ```  

5.  Netmon 추적 캡처를 중지 하려면 **ctrl + C** 를 누릅니다.
 
> [!NOTE]
> 클라이언트 컴퓨터에서 netmon 추적만 수집할 수 있는 경우 추적을 분석 하는 데 도움이 되는 서버 ip 주소를 확인 하세요.

### <a name="how-to-analyze-netmon-trace"></a>Netmon 추적을 분석 하는 방법

위의 netmon 추적을 수집 하 여 **8.8.8.8 888** 를 텔넷 하려고 하면 아래 추적을 볼 수 있습니다.

![netmon 추적 1](media/self-hosted-integration-runtime-troubleshoot-guide/netmon-trace-1.png)

![netmon 추적 2](media/self-hosted-integration-runtime-troubleshoot-guide/netmon-trace-2.png)
 

즉, 포트 **888**을 기반으로 **8.8.8.8** 서버 쪽에 TCP 연결을 설정할 수 없으므로 두 개의 **sy재전송** 추가 패키지를 볼 수 있습니다. 원본 **자체 host2.contoso.com** 는 첫 번째 패키지에서 **8.8.8.8** 에 연결할 수 없기 때문에 연결 하는 동안 유지 됩니다.

> [!TIP]
> - **부하 필터**  ->  **표준 필터**  ->  **주소**  ->  **IPv4 주소**를 클릭 하면 됩니다.
> - 입력 **IPv4. Address = = 8.8.8.8** as filter를 입력 하 고 **적용**을 클릭 합니다. 그 후에는 로컬 컴퓨터에서 대상 **8.8.8.8**에 대 한 통신만 표시 됩니다.

![필터 주소 1](media/self-hosted-integration-runtime-troubleshoot-guide/filter-addresses-1.png)
        
![필터 주소 2](media/self-hosted-integration-runtime-troubleshoot-guide/filter-addresses-2.png)

아래 예제에서는 좋은 시나리오의 모양을 보여 줍니다. 

- 텔넷 **8.8.8.8 53** 가 문제 없이 정상적으로 작동 하는 경우 tcp 3 핸드셰이크가 발생 한 다음 세션이 tcp 4 핸드셰이크로 완료 되는 것을 볼 수 있습니다.

    ![좋은 시나리오 예 1](media/self-hosted-integration-runtime-troubleshoot-guide/good-scenario-1.png)
     
    ![좋은 시나리오 예 2](media/self-hosted-integration-runtime-troubleshoot-guide/good-scenario-2.png)

- 위의 TCP 3 핸드셰이크를 기준으로 아래 워크플로를 볼 수 있습니다.

    ![TCP 3 핸드셰이크 워크플로](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-3-handshake-workflow.png)
 
- TCP 4 핸드셰이크는 세션을 완료 하 고 해당 작업 흐름은 다음과 같이 표시 됩니다.

    ![TCP 4 핸드셰이크](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-4-handshake.png)

    ![TCP 4 핸드셰이크 워크플로](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-4-handshake-workflow.png) 


## <a name="next-steps"></a>다음 단계

문제 해결에 대 한 자세한 내용은 다음 리소스를 참조 하세요.

*  [Data Factory 블로그](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory 기능 요청](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure 비디오](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&A 질문 페이지](https://docs.microsoft.com/answers/topics/azure-data-factory.html)
*  [Data Factory에 대 한 스택 오버플로 포럼](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Data Factory에 대한 Twitter 정보](https://twitter.com/hashtag/DataFactory)
*  [데이터 흐름 매핑 성능 가이드](concepts-data-flow-performance.md)
