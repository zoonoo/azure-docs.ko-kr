---
title: Azure Data Factory에서 자체 호스팅 통합 런타임 문제 해결
description: Azure Data Factory에서 자체 호스팅 통합 런타임 문제를 해결 하는 방법에 대해 알아봅니다.
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 11/07/2019
ms.author: abnarain
ms.openlocfilehash: 9adbc3d7d30aeb8c7cb2b89c326ac2b39a2e8d2b
ms.sourcegitcommit: 6dec090a6820fb68ac7648cf5fa4a70f45f87e1a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2019
ms.locfileid: "73907283"
---
# <a name="troubleshoot-self-hosted-integration-runtime"></a>자체 호스팅 integration runtime 문제 해결

이 문서에서는 Azure Data Factory에서 자체 호스팅 통합 런타임에 대 한 일반적인 문제 해결 방법을 살펴봅니다.

## <a name="common-errors-and-resolutions"></a>일반적인 오류 및 해결 방법

### <a name="error-message-self-hosted-integration-runtime-is-unable-to-connect-to-cloud-service"></a>오류 메시지: 자체 호스팅 통합 런타임이 클라우드 서비스에 연결할 수 없습니다.

- **증상**: 

    ![자체 호스팅 IR 연결 문제](media/self-hosted-integration-runtime-troubleshoot-guide/unable-to-connect-to-cloud-service.png)

- **원인**: 자체 호스팅 통합 런타임이 데이터 팩터리 서비스 (백 엔드)에 연결할 수 없습니다. 방화벽의 네트워크 설정으로 인해 발생 하는 경우가 가장 많습니다.

- **해결 방법**: 

    1. Windows 서비스 "Integration Runtime 서비스"가 실행 중인지 확인 합니다.
    
        ![자체 호스팅 IR 서비스 실행 상태](media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-running-status.png)
    
    2. [1]에 표시 된 windows 서비스가 실행 되 고 있는 경우 다음 지침을 적절 하 게 따르세요.

        1. 자체 호스팅 integration runtime에서 "프록시"가 구성 되지 않은 경우 (기본 설정이 프록시 구성이 아님) 자체 호스팅 통합 런타임이 설치 된 컴퓨터에서 아래 PowerShell 명령을 실행 합니다. 
            
            ```powershell
            (New-Object System.Net.WebClient).DownloadString("https://wu2.frontend.clouddatahub.net/")
            ```
            > [!NOTE] 
            > 서비스 URL은 데이터 팩터리 위치에 따라 달라질 수 있습니다. ADF UI > 연결에서 서비스 URL을 찾을 수 있습니다-> Integration runtime-> 자체 호스팅 IR > 노드 편집-> 서비스 Url 보기
            
            예상 되는 응답은 다음과 같습니다.
            
            ![Powershell 명령 응답](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)
            
            응답이 다른 경우 적절 한 다음 지침을 따르세요.
            
            * "원격 이름을 확인할 수 없습니다." 오류가 발생 하는 경우 DNS에 문제가 있는 것입니다. DNS 확인 문제를 해결 하려면 네트워크 팀에 연락 하세요. 
            * "Ssl/tls 인증서를 신뢰할 수 없습니다" 오류 메시지가 표시 되 면 "https://wu2.frontend.clouddatahub.net/"에 대 한 인증서를 컴퓨터에서 신뢰할 수 있는지 확인 하세요. 그러면 인증서 관리자를 사용 하 여 공용 인증서를 설치 하 여이 문제를 완화 해야 합니다.
            * Windows > 이벤트 뷰어 (로그)-> 응용 프로그램 및 서비스 로그-> Integration Runtime는 대부분 DNS, 방화벽 규칙 및 회사의 네트워크 설정에 의해 발생 합니다 (Forcedly close 연결). 이 문제를 해결 하려면 모든 회사에서 네트워크 설정을 사용자 지정 했기 때문에 추가 트러블 슈팅을 위해 네트워크 팀에 참여 하세요.

        2. 자체 호스팅 통합 런타임에 "프록시"가 구성 된 경우 프록시 서버에서 서비스 끝점에 액세스할 수 있는지 여부를 확인 합니다. 샘플 명령의 경우 [이](https://stackoverflow.com/questions/571429/powershell-web-requests-and-proxies)를 참조 하세요.    
                
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

            예상 되는 응답은 다음과 같습니다.
            
            ![Powershell 명령 응답 2](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)

            > [!NOTE] 
            > 프록시 고려 사항:
            > * 프록시 서버에 허용 목록가 필요한 지 확인 합니다. 그렇다면 [이러한 도메인](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations#firewall-requirements-for-on-premisesprivate-network) 을 허용 목록 합니다.
            > * "Wu2.frontend.clouddatahub.net/"에 대 한 TLS/SSL 인증서가 프록시 서버에서 신뢰 되는지 확인 합니다.
            > * 프록시에서 active directory 인증을 사용 하는 경우 "Integration Runtime Service"로 프록시에 액세스할 수 있는 사용자 계정으로 서비스 계정을 변경 합니다.

### <a name="error-message-self-hosted-integration-runtime-node-logical-shir-is-in-inactive-running-limited-state"></a>오류 메시지: 자체 호스팅 통합 런타임 노드/논리적 SHIR이 비활성/"실행 중 (제한 됨)" 상태입니다.

- **원인**: 아래 스크린샷에 표시 된 것 처럼, 비활성 상태의 자체 호스팅 IR 노드가 표시 될 수 있습니다.

    ![비활성 자체 호스팅 IR 노드](media/self-hosted-integration-runtime-troubleshoot-guide/inactive-self-hosted-ir-node.png)

    이는 노드가 서로 통신할 수 없는 경우에 발생 합니다. 

- **해결 방법**: 

    노드에 호스팅된 VM에 로그인 하 고 이벤트 보기를 열고 응용 프로그램 및 서비스 로그-> Integration Runtime에서 모든 오류 로그를 필터링 합니다. 

     1. 오류 로그에 다음이 포함 된 경우: 
    
        **오류 로그**: system.servicemodel.endpointnotfoundexception: net.tcp://xxxxxxx.bwld.com: 8060/externalservice. .svc/작업 관리자에 연결할 수 없습니다. 시간 범위의 00:00:00.9940994에 대 한 연결 시도가 지속 되었습니다. TCP 오류 코드 10061: 대상 컴퓨터에서 적극적으로 10.2.4.10를 거부 하 여 연결을 설정할 수 없습니다. 8060.  --->. 10.2.4.10 예외: 대상 컴퓨터에서 현재이를 거부 하 여 연결을 설정할 수 없습니다. 8060
    
           시스템 .Net. p. p. n e t.
           
           at 시스템 .Net. remoteEP (끝점 연결)
           
           at System.servicemodel. SocketConnectionInitiator. Connect (Uri uri, TimeSpan 시간 제한)
    
        **해결 방법:** 명령줄 시작: telnet 10.2.4.10 8060
        
        아래 오류가 발생 하면이 문제를 해결 하는 데 도움이 필요 하면 IT 담당자에 게 문의 하세요. 성공적으로 텔넷 된 후에도 IR 노드 상태에 대 한 문제가 계속 되 면 Microsoft 지원에 문의 하세요.
        
        ![명령줄 오류](media/self-hosted-integration-runtime-troubleshoot-guide/command-line-error.png)
        
     2. 오류 로그에 다음이 포함 된 경우:
     
        **오류 로그:** 작업자 관리자에 연결할 수 없습니다. net.tcp://xxxxxx: 8060/ExternalService. s i o n/호스트 azranlcir01r1에 대 한 DNS 항목이 없습니다. 해당 호스트에 알려진 예외 정보가 없습니다. System.servicemodel.endpointnotfoundexception: 호스트 xxxxx에 대 한 DNS 항목이 없습니다. --->. 소켓이 아닌 예외: 시스템 .net. InternalGetHostByName (문자열 호스트 이름, 부울 includeIPv6)의 시스템 GetAddrInfo (문자열 이름)에서 해당 호스트를 알 수 없습니다. System.net.dns.gethostentry (문자열 hostNameOrAddress )에서 DnsCache (Uri uri)---내부 예외 스택 추적의 끝---서버 스택 추적: at DnsCache (Uri uri)입니다. 
    
        **해결 방법:** 아래 두 작업 중 하나를 통해 문제를 해결할 수 있습니다.
         1. 모든 노드를 동일한 도메인에 배치 합니다.
         2. 모든 호스팅된 VM 호스트 파일의 호스트 매핑에 IP를 추가 합니다.


## <a name="next-steps"></a>다음 단계

문제 해결에 대 한 자세한 내용은 다음 리소스를 참조 하세요.

*  [Data Factory 블로그](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory 기능 요청](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure 비디오](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN 포럼](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Data Factory에 대 한 Stack Overflow 포럼](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Data Factory에 대 한 Twitter 정보](https://twitter.com/hashtag/DataFactory)
*  [ADF 매핑 데이터 흐름 성능 가이드](concepts-data-flow-performance.md)
