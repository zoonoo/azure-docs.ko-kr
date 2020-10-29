---
title: Azure Data Factory에서 자체 호스팅 Integration Runtime 문제 해결
description: Azure Data Factory에서 자체 호스팅 Integration Runtime 문제를 해결하는 방법을 알아봅니다.
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 10/29/2020
ms.author: lle
ms.openlocfilehash: ca8d359638d97f77377f02d47d824fa216acdcc8
ms.sourcegitcommit: dd45ae4fc54f8267cda2ddf4a92ccd123464d411
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/29/2020
ms.locfileid: "92928113"
---
# <a name="troubleshoot-self-hosted-integration-runtime"></a>자체 호스팅 Integration Runtime 문제 해결

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory에서 자체 호스팅 Integration Runtime에 대한 일반적인 문제 해결 방법을 살펴봅니다.

## <a name="gather-self-hosted-ir-logs-from-azure-data-factory"></a>Azure Data Factory에서 자체 호스팅 IR 로그 수집

자체 호스팅 IR/공유 IR에서 실행 되는 실패 한 작업의 경우 Azure Data Factory에서 오류 로그 보기 및 업로드를 지원 합니다. 아래 단계에 따라 오류 보고서 ID를 가져온 다음 보고서 ID를 입력 하 여 관련 알려진 문제를 찾을 수 있습니다.

1. **작업 실행** 페이지로 이동 합니다.

1. **오류** 열 아래에서 아래 단추를 클릭 합니다.

    ![작업 실행 페이지](media/self-hosted-integration-runtime-troubleshoot-guide/activity-runs-page.png)

1. 실패 한 작업 실행에 대 한 관련 로그가 표시 됩니다. 추가 지원이 필요 하면 **로그 보내기** 단추를 클릭 합니다.

    ![로그 보내기](media/self-hosted-integration-runtime-troubleshoot-guide/send-logs.png)

1. 보내려는 로그를 선택할 수 있습니다. *자체 호스팅 ir* 의 경우 실패 한 작업 또는 자체 호스팅 ir 노드의 모든 로그와 관련 된 로그를 업로드할 수 있습니다. *공유 IR* 의 경우 실패 한 작업에 관련 된 로그만 업로드할 수 있습니다.

    ![로그 선택](media/self-hosted-integration-runtime-troubleshoot-guide/choose-logs.png)

1. 로그를 업로드 하는 경우 문제 해결을 위한 추가 지원이 필요한 경우에는 보고서 ID를 기록해 둡니다.

    ![로그 업로드](media/self-hosted-integration-runtime-troubleshoot-guide/upload-logs.png)

> [!NOTE]
> 로그 보기 및 업로드 요청은 모든 온라인 자체 호스팅 IR 인스턴스에서 실행 됩니다. 로그가 누락 되는 경우 자체 호스팅 IR 인스턴스가 모두 온라인 상태 인지 확인 하세요. 


## <a name="self-hosted-ir-general-failure-or-error"></a>자체 호스팅 IR 일반 실패 또는 오류

### <a name="tlsssl-certificate-issue"></a>TLS/SSL 인증서 문제

#### <a name="symptoms"></a>증상

**자체 호스팅 IR 구성 관리자** -> **인트라넷에서 원격 액세스** 에서 TLS/SSL 인증서(고급)를 사용하려고 설정할 때 TLS/SSL 인증서를 선택하면 아래 오류가 표시됩니다.

`Remote access settings are invalid. Identity check failed for outgoing message. The expected DNS identity of the remote endpoint was ‘abc.microsoft.com’ but the remote endpoint provided DNS claim ‘microsoft.com’. If this is a legitimate remote endpoint, you can fix the problem by explicitly specifying DNS identity ‘microsoft.com’ as the Identity property of EndpointAddress when creating channel proxy.`

위의 경우 사용자는 마지막 항목으로 "microsoft.com"이 포함된 인증서를 사용하고 있습니다.

#### <a name="cause"></a>원인

이는 WCF의 알려진 문제입니다. WCF TLS/SSL 유효성 검사는 SAN의 마지막 DNSName만 확인합니다. 

#### <a name="resolution"></a>해결 방법

와일드카드 인증서는 Azure Data Factory v2 자체 호스팅 IR에서 지원됩니다. 이 문제는 일반적으로 SSL 인증서가 올바르지 않기 때문에 발생합니다. SAN의 마지막 DNSName이 유효해야 합니다. 아래 단계를 수행하여 확인합니다. 
1.  관리 콘솔을 열고 인증서 세부 정보에서 *주체* 및 *주체 대체 이름* 둘 다를 두 번 확인 합니다. 예를 들어 위의 경우에는 "DNS 이름 = microsoft.com.com" 인 *주체 대체 이름의* 마지막 항목이 합법적이 지 않습니다.
2.  잘못 된 DNS 이름을 제거 하려면 인증서 문제 회사에 문의 하세요.

### <a name="concurrent-jobs-limit-issue"></a>동시 작업 제한 문제

#### <a name="symptoms"></a>증상

Azure Data Factory UI에서 동시 작업 제한을 늘리려고 하면 *업데이트 중* 상태로 영원히 중단됩니다.
동시 작업의 최댓값이 24로 설정되었으며 작업을 더 빠르게 실행할 수 있도록 개수를 늘리려고 합니다. 입력할 수 있는 최솟값은 3이고 입력할 수 있는 최댓값은 32입니다. 아래에서 볼 *수 있듯이 업데이트* 에 걸린 UI에서 24에서 32 사이의 값을 높이고 *업데이트* 단추를 누르면 됩니다. 새로 고침 후에도 고객은 여전히 값을 24로 확인했으며 이 값은 32로 업데이트되지 않았습니다.

![상태 업데이트 중](media/self-hosted-integration-runtime-troubleshoot-guide/updating-status.png)

#### <a name="cause"></a>원인

값은 컴퓨터 logicCore 및 메모리에 따라 달라지므로 설정에 제한이 있으며 24와 같은 더 작은 값으로 조정해야 결과를 볼 수 있습니다.

> [!TIP] 
> - 논리 코어 수와 컴퓨터의 논리 코어 수를 찾는 방법에 대 한 자세한 내용은 [이 문서](https://www.top-password.com/blog/find-number-of-cores-in-your-cpu-on-windows-10/)를 참조 하세요.
> - Math .log를 계산 하는 방법에 대 한 자세한 내용은 [이 문서](https://www.rapidtables.com/calc/math/Log_Calculator.html)를 참조 하세요.


### <a name="self-hosted-ir-ha-ssl-certificate-issue"></a>자체 호스팅 IR HA SSL 인증서 문제

#### <a name="symptoms"></a>증상

자체 호스팅 IR 작업 노드에서 아래 오류를 보고했습니다.

`Failed to pull shared states from primary node net.tcp://abc.cloud.corp.Microsoft.com:8060/ExternalService.svc/. Activity ID: XXXXX The X.509 certificate CN=abc.cloud.corp.Microsoft.com, OU=test, O=Microsoft chain building failed. The certificate that was used has a trust chain that cannot be verified. Replace the certificate or change the certificateValidationMode. The revocation function was unable to check revocation because the revocation server was offline.`

#### <a name="cause"></a>원인

SSL/TLS 핸드셰이크와 관련된 사례를 처리할 때 인증서 체인 확인과 관련된 몇 가지 문제가 발생할 수 있습니다. 

#### <a name="resolution"></a>해결 방법

- 다음은 x.509 인증서 체인 빌드 실패 문제를 해결 하는 빠르고 직관적인 방법입니다.
 
    1. 확인해야 하는 인증서를 내보냅니다. 컴퓨터 인증서 관리로 이동하여 확인하려는 인증서를 찾아 마우스 오른쪽 단추로 클릭한 다음 **모든 작업** -> **내보내기** 를 클릭합니다.
    
        ![작업 내보내기](media/self-hosted-integration-runtime-troubleshoot-guide/export-tasks.png)

    2. 내보낸 인증서를 클라이언트 컴퓨터에 복사 합니다. 
    3. 클라이언트 쪽의 CMD에서 아래 명령을 실행합니다. 아래 *\<certificate path>* 와 *\<output txt file path>* 자리 표시자를 관련 경로로 바꾸어야 합니다.
    
        ```
        Certutil -verify -urlfetch    <certificate path>   >     <output txt file path> 
        ```

        예를 들면 다음과 같습니다.

        ```
        Certutil -verify -urlfetch c:\users\test\desktop\servercert02.cer > c:\users\test\desktop\Certinfo.txt
        ```
    4. 출력 txt 파일에 오류가 있는지 확인합니다. txt 파일의 끝에서 오류 요약을 찾을 수 있습니다.

        예를 들면 다음과 같습니다. 

        ![오류 요약](media/self-hosted-integration-runtime-troubleshoot-guide/error-summary.png)

        아래와 같이 로그 파일의 끝에 오류가 표시 되지 않으면 클라이언트 컴퓨터에서 성공적으로 구축 된 인증서 체인을 고려할 수 있습니다.
        
        ![로그 파일에 오류가 없습니다.](media/self-hosted-integration-runtime-troubleshoot-guide/log-file.png)      

- 인증서 파일에 구성 된 AIA, CDP 및 OCSP가 있는 경우. 좀 더 직관적인 방법으로 확인할 수 있습니다.
 
    1. 인증서의 세부 정보를 확인 하 여이 정보를 가져올 수 있습니다.
    
        ![인증서 세부 정보](media/self-hosted-integration-runtime-troubleshoot-guide/certificate-detail.png)
    1. 아래 명령을 실행합니다. *\<certificate path>* 자리 표시자를 인증서의 관련 경로로 대체 했는지 확인 합니다.
    
        ```
          Certutil   -URL    <certificate path> 
        ```
    1. 그런 다음 **URL 검색 도구** 가 열립니다. **검색** 단추를 클릭하여 AIA, CDP 및 OCSP에서 인증서를 확인할 수 있습니다.

        ![검색 단추](media/self-hosted-integration-runtime-troubleshoot-guide/retrieval-button.png)
 
        AIA의 인증서가 "확인됨"이고 CDP 또는 OCSP의 인증서가 "확인됨"인 경우 인증서 체인이 성공적으로 구축될 수 있습니다.

        AIA, CDP를 검색할 때 오류가 표시되는 경우 네트워크 팀과 협력하여 클라이언트 컴퓨터를 대상 URL에 연결할 준비를 합니다. http 경로 또는 ldap 경로를 확인할 수 있으면 충분합니다.

### <a name="self-hosted-ir-could-not-load-file-or-assembly"></a>자체 호스팅 IR에서 파일 또는 어셈블리를 로드할 수 없음

#### <a name="symptoms"></a>증상

`Could not load file or assembly 'XXXXXXXXXXXXXXXX, Version=4.0.2.0, Culture=neutral, PublicKeyToken=XXXXXXXXX' or one of its dependencies. The system cannot find the file specified. Activity ID: 92693b45-b4bf-4fc8-89da-2d3dc56f27c3`
 
예를 들면 다음과 같습니다. 

`Could not load file or assembly 'System.ValueTuple, Version=4.0.2.0, Culture=neutral, PublicKeyToken=XXXXXXXXX' or one of its dependencies. The system cannot find the file specified. Activity ID: 92693b45-b4bf-4fc8-89da-2d3dc56f27c3`

#### <a name="cause"></a>원인

프로세스 모니터를 사용 하는 경우 다음과 같은 결과를 볼 수 있습니다.

[![프로세스 모니터](media/self-hosted-integration-runtime-troubleshoot-guide/process-monitor.png)](media/self-hosted-integration-runtime-troubleshoot-guide/process-monitor.png#lightbox)

> [!TIP] 
> 아래 스크린샷에 표시 된 것 처럼 필터를 설정할 수 있습니다.
> **System.valuetuple** DLL은 GAC 관련 폴더 또는 *C:\program Files\Microsoft integration Runtime\4.0\Gateway* 또는 *c:\program Files\Microsoft integration Runtime\4.0\Shared* 폴더에 있지 않음을 나타냅니다.
> 기본적으로 *GAC* 폴더에서 먼저 dll을 로드하고, 그다음 *Shared* 에서, 마지막으로 *Gateway* 폴더에서 dll을 로드합니다. 따라서 도움이 될 수 있는 경로에 dll을 배치할 수 있습니다.

![필터 설정](media/self-hosted-integration-runtime-troubleshoot-guide/set-filters.png)

#### <a name="resolution"></a>해결 방법

**System.ValueTuple.dll** *C:\Program Files\Microsoft Integration Runtime\4.0\Gateway\DataScan* 폴더에 있는 것을 확인할 수 있습니다. **System.ValueTuple.dll** 를 *C:\Program Files\Microsoft Integration Runtime\4.0\Gateway* 폴더에 복사 하 여 문제를 해결 합니다.

동일한 방법을 사용하여 다른 파일 또는 어셈블리 누락 문제를 해결할 수 있습니다.

#### <a name="more-information"></a>추가 정보

*%Windir%\Microsoft.NET\assembly* 및 *%windir%\assembly* 아래에 System.ValueTuple.dll 표시 되는 이유는 .net 동작 이라고 하는 것입니다. 

아래 오류에서 어셈블리 시스템을 명확 하 게 볼 수 있습니다 *. system.valuetuple* 가 없습니다. 이러한 문제는 응용 프로그램이 어셈블리 *System.ValueTuple.dll* 를 확인 하려고 할 때 발생 합니다.
 
`<LogProperties><ErrorInfo>[{"Code":0,"Message":"The type initializer for 'Npgsql.PoolManager' threw an exception.","EventType":0,"Category":5,"Data":{},"MsgId":null,"ExceptionType":"System.TypeInitializationException","Source":"Npgsql","StackTrace":"","InnerEventInfos":[{"Code":0,"Message":"Could not load file or assembly 'System.ValueTuple, Version=4.0.2.0, Culture=neutral, PublicKeyToken=XXXXXXXXX' or one of its dependencies. The system cannot find the file specified.","EventType":0,"Category":5,"Data":{},"MsgId":null,"ExceptionType":"System.IO.FileNotFoundException","Source":"Npgsql","StackTrace":"","InnerEventInfos":[]}]}]</ErrorInfo></LogProperties>`
 
GAC에 대 한 자세한 내용은 [이 문서](/dotnet/framework/app-domains/gac)를 참조 하세요.


### <a name="how-to-audit-self-hosted-ir-key-missing"></a>자체 호스팅 IR 키 누락을 감사하는 방법

#### <a name="symptoms"></a>증상

자체 호스팅 통합 런타임이 키 없이 갑자기 오프라인으로 전환되며, 아래 오류 메시지가 이벤트 로그에 표시됩니다. `Authentication Key is not assigned yet`

![인증 키가 없습니다.](media/self-hosted-integration-runtime-troubleshoot-guide/key-missing.png)

#### <a name="cause"></a>원인

- 포털에서 자체 호스팅 IR 노드나 논리 자체 호스팅 IR이 삭제되었습니다.
- 완전한 제거 작업이 완료되었습니다.

#### <a name="resolution"></a>해결 방법

위의 원인이 모두 적용 되지 않는 경우 *%Programdata%\Microsoft\Data Transfer\DataManagementGateway* 폴더로 이동 하 여 **구성** 파일이 삭제 되었는지 여부를 확인할 수 있습니다. 삭제된 경우 [여기](https://www.netwrix.com/how_to_detect_who_deleted_file.html) 지침에 따라 누가 파일을 삭제했는지 감사합니다.

![구성 파일 확인](media/self-hosted-integration-runtime-troubleshoot-guide/configurations-file.png)


### <a name="cannot-use-self-hosted-ir-to-bridge-two-on-premises-data-stores"></a>자체 호스팅 IR을 사용하여 두 개의 온-프레미스 데이터 저장소에 연결할 수 없음

#### <a name="symptoms"></a>증상

원본 및 대상 데이터 저장소에 대해 자체 호스팅 IR을 만든 후 두 IR을 함께 연결하여 복사본을 완성하려고 합니다. 데이터 저장소가 다른 Vnet에 구성 되어 있거나 게이트웨이 메커니즘을 이해할 수 없는 경우 다음과 같은 오류가 발생 합니다. *대상 IR에서 원본 드라이버를 찾을 수 없습니다* . *대상 IR에서 원본에 액세스할 수 없습니다* .
 
#### <a name="cause"></a>원인

자체 호스팅 IR이 각 데이터 저장소에 대해 설치해야 하는 클라이언트 에이전트가 아니라 복사 작업의 중앙 노드로 설계되었습니다.
 
위의 경우에서 각 데이터 저장소에 대한 연결된 서비스는 동일한 IR을 사용하여 만들어야 하며 IR은 네트워크를 통해 두 데이터 저장소에 모두 액세스할 수 있어야 합니다. 원본 데이터 저장소, 대상 데이터 저장소 또는 세 번째 컴퓨터에 IR을 설치하는 것과 관계없이 두 개의 연결된 서비스가 다른 IR로 만들어졌지만 동일한 복사 작업에서 사용되는 경우 대상 IR을 사용하고 두 데이터 저장소의 드라이버를 대상 IR 컴퓨터에 설치해야 합니다.

#### <a name="resolution"></a>해결 방법

대상 IR에 원본 및 대상 모두에 대한 드라이버를 설치하고 원본 데이터 저장소에 액세스할 수 있는지 확인합니다.
 
두 데이터 저장소 간에 네트워크를 통해 트래픽을 전달할 수 없는 경우(예: 두 개의 VNET에 구성된 경우) IR이 설치된 경우에도 한 번의 작업으로 복사를 완료할 수 없습니다. 이 경우 VENT에 각각 두 개의 IR을 사용하여 두 개의 복사 작업을 만들 수 있습니다. 하나의 IR은 데이터 저장소 1에서 Azure Blob Storage로 복사하고, 다른 하나는 Azure Blob Storage에서 데이터 저장소 2로 복사합니다. 이 경우 IR을 사용하여 연결이 끊어진 두 데이터 저장소를 연결하는 브리지를 만드는 데 필요한 요구 사항을 시뮬레이션할 수 있습니다.


### <a name="credential-sync-issue-causes-credential-lost-from-ha"></a>자격 증명 동기화 문제로 인해 HA에서 자격 증명이 손실됨

#### <a name="symptoms"></a>증상

데이터 원본 자격 증명 "XXXXXXXXXX"가 "Azure Portal에서 링크 서비스를 삭제할 때 페이로드가 있는 현재 Integration Runtime 노드에서 삭제되었거나, 작업에 잘못된 페이로드가 있습니다. 자격 증명을 사용하여 새 링크 서비스를 다시 만드세요".

#### <a name="cause"></a>원인

자체 호스팅 IR은 두 노드를 사용하여 HA 모드로 기본 제공되지만 자격 증명 동기화 상태에 있지 않습니다. 즉, 디스패처 노드에 저장된 자격 증명이 다른 작업자 노드와 동기화되지 않습니다. 디스패처 노드에서 작업자 노드에 대한 장애 조치(failover)가 발생하지만 자격 증명이 이전 디스패처 노드에만 존재하는 경우 자격 증명에 액세스하려고 하면 작업이 실패하고 위의 오류가 발생합니다.

#### <a name="resolution"></a>해결 방법

이 문제를 방지하는 유일한 방법은 두 노드가 자격 증명 동기화 상태에 있는지 확인하는 것입니다. 그렇지 않으면 새 디스패처에 대한 자격 증명을 다시 입력해야 합니다.


### <a name="cannot-choose-the-certificate-due-to-private-key-missing"></a>프라이빗 키가 없어 인증서를 선택할 수 없음

#### <a name="symptoms"></a>증상

1.  인증서 저장소에 PFX 파일을 가져옵니다.
2.  IR Configuration Manager UI를 통해 인증서를 선택하는 경우 아래 오류가 발생할 수 있습니다.

    ![개인 키가 없음](media/self-hosted-integration-runtime-troubleshoot-guide/private-key-missing.png)

#### <a name="cause"></a>원인

- 사용자 계정의 권한이 낮으므로 프라이빗 키에 액세스할 수 없습니다.
- 인증서가 서명으로 생성되었지만 키 교환으로 생성되지 않았습니다.

#### <a name="resolution"></a>해결 방법

1.  UI를 작동하기 위해 프라이빗 키에 액세스할 수 있는 권한 있는 계정을 사용합니다.
2.  아래 명령을 실행하여 인증서를 가져옵니다.
    
    ```
    certutil -importpfx FILENAME.pfx AT_KEYEXCHANGE
    ```


## <a name="self-hosted-ir-setup"></a>자체 호스팅 IR 설정

### <a name="the-integration-runtime-registration-error"></a>Integration Runtime 등록 오류 

#### <a name="symptoms"></a>증상

다른 계정에서 다음과 같은 이유로 자체 호스팅 IR을 실행 하려는 경우가 있습니다.
- 회사 정책은 서비스 계정을 허용 하지 않습니다.
- 일부 인증이 필요 합니다.

서비스 패널에서 서비스 계정을 변경한 후 Integration Runtime 작동이 중지 될 수 있습니다.

![IR 등록 오류](media/self-hosted-integration-runtime-troubleshoot-guide/ir-registration-error.png)

#### <a name="cause"></a>원인

서비스 계정에만 부여 되는 많은 리소스가 있습니다. 서비스 계정을 다른 계정으로 변경 하는 경우 모든 종속 리소스의 권한은 동일 하 게 유지 됩니다.

#### <a name="resolution"></a>해결 방법

Integration Runtime 이벤트 로그로 이동 하 여 오류를 확인 합니다.

![IR 이벤트 로그](media/self-hosted-integration-runtime-troubleshoot-guide/ir-event-log.png)

오류가 위의 *system.unauthorizedaccessexception* 표시 되는 경우 아래 지침을 따르세요.


1. Windows 서비스 패널에서 *Diahostservice* 로그온 서비스 계정을 확인 합니다.

    ![로그온 서비스 계정](media/self-hosted-integration-runtime-troubleshoot-guide/logon-service-account.png)

2. 로그온 서비스 계정에 *%programdata%\Microsoft\DataTransfer\DataManagementGateway* 폴더에 대 한 R/W 권한이 있는지 확인 합니다.

    - 기본적으로 서비스 로그온 계정이 변경 되지 않은 경우에는 R/W의 사용 권한이 있어야 합니다.

        ![서비스 권한](media/self-hosted-integration-runtime-troubleshoot-guide/service-permission.png)

    - 서비스 로그온 계정을 변경한 경우 아래 단계에 따라 문제를 완화 합니다.
        1. 현재 자체 호스팅 IR을 완전히 제거 합니다.
        1. 자체 호스팅 IR 비트를 설치 합니다.
        1. 서비스 계정을 변경 하려면 아래 지침을 따르세요. 
            1. Selfhosted IR의 설치 폴더로 이동 하 여 *Microsoft Integration Runtime\4.0\Shared* 폴더로 전환 합니다.
            1. 승격 된 권한을 사용 하 여 명령줄을 시작 합니다. 및를 사용자 *\<user>* *\<password>* 이름 및 암호로 바꾸고 아래 명령을 실행 합니다.
                       
                ```
                dmgcmd.exe -SwitchServiceAccount "<user>" "<password>"
                ```
            1. LocalSystem 계정으로 변경 하려면이 계정에 올바른 형식을 사용 해야 합니다. 다음은 올바른 형식의 예입니다.

                ```
                dmgcmd.exe -SwitchServiceAccount "NT Authority\System" ""
                ```         
                아래와 같이 형식을 사용 **하지** 마십시오.

                ```
                dmgcmd.exe -SwitchServiceAccount "LocalSystem" ""
                ```              
            1. 대신 로컬 시스템의 권한이 관리자 보다 높기 때문에 "서비스"에서 직접 변경할 수도 있습니다.
            1. IR 서비스 로그온 계정에 로컬/도메인 사용자를 사용할 수 있습니다.            
        1. Integration Runtime를 등록 합니다.

오류가로 표시 되는 경우: *서비스 ' Integration Runtime 서비스 ' (DIAHostService)을 (를) 시작 하지 못했습니다. 시스템 서비스를 시작할 수 있는 권한이 있는지 확인* 하 고 아래 지침을 따르세요.

1. Windows 서비스 패널에서 *Diahostservice* 로그온 서비스 계정을 확인 합니다.
   
    ![로그온 서비스 계정](media/self-hosted-integration-runtime-troubleshoot-guide/logon-service-account.png)

2. 로그온 서비스 계정에 Windows 서비스를 시작할 수 있는 **서비스로** 로그온 권한이 있는지 확인 합니다.

    ![서비스로 로그온](media/self-hosted-integration-runtime-troubleshoot-guide/logon-as-service.png)

#### <a name="more-information"></a>추가 정보

해당 하는 경우 위의 두 패턴 중 어느 것도 적용 되지 않으면 아래 Windows 이벤트 로그를 수집 해 보십시오. 
- 응용 프로그램 및 서비스 로그-> Integration Runtime
- Windows 로그-> 응용 프로그램

### <a name="cannot-find-register-button-to-register-a-self-hosted-ir"></a>자체 호스팅 IR을 등록 하기 위한 등록 단추를 찾을 수 없습니다.    

#### <a name="symptoms"></a>증상

자체 호스팅 IR을 등록할 때 Configuration Manager UI에서 **등록** 단추를 찾을 수 없습니다.

![등록 단추 없음](media/self-hosted-integration-runtime-troubleshoot-guide/no-register-button.png)

#### <a name="cause"></a>원인

*3.0 Integration Runtime* 릴리스 이후에는 기존 Integration Runtime 노드의 **등록** 단추가 제거 되어 더 깔끔하고 안전 하 게 환경을 사용할 수 있습니다. 노드가 온라인 상태인지 여부에 관계없이 Integration Runtime에 등록되어 있는 경우 다른 Integration Runtime에 다시 등록하려면 이전 노드를 제거한 다음 노드를 설치하고 등록해야 합니다.

#### <a name="resolution"></a>해결 방법

1. 제어판으로 이동 하 여 기존 Integration Runtime를 제거 합니다.

    > [!IMPORTANT] 
    > 아래 프로세스에서 예를 선택 합니다. 제거 프로세스 동안 데이터를 유지 하지 않습니다.

    ![데이터 삭제](media/self-hosted-integration-runtime-troubleshoot-guide/delete-data.png)

1. Integration runtime 설치 관리자 MSI가 없는 경우 [다운로드 센터](https://www.microsoft.com/en-sg/download/details.aspx?id=39717) 로 이동 하 여 최신 Integration Runtime를 다운로드 합니다.
1. MSI를 설치 하 고 Integration Runtime 등록 합니다.


### <a name="unable-to-register-the-self-hosted-ir-due-to-localhost"></a>localhost로 인해 자체 호스팅 IR을 등록할 수 없음    

#### <a name="symptoms"></a>증상

Get_LoopbackIpOrName 때 새 컴퓨터에 자체 호스팅 IR을 등록할 수 없습니다.

**디버그:** 런타임 오류가 발생 했습니다.
' DataTransfer '에 대 한 형식 이니셜라이저가 예외를 throw 했습니다.
데이터베이스 조회를 수행 하는 동안 복구할 수 없는 오류가 발생 했습니다.
 
**예외 정보:** System.typeinitializationexception: ' DataTransfer. DataSourceCache '에 대 한 형식 이니셜라이저가 예외를 throw 했습니다. ---> GetAddrInfo: 시스템에서 데이터베이스를 조회 하는 동안 복구할 수 없는 오류가 발생 했습니다 (문자열 이름).

#### <a name="cause"></a>원인

이 문제는 일반적으로 localhost를 확인할 때 발생 합니다.

#### <a name="resolution"></a>해결 방법

Localhost 127.0.0.1을 사용 하 여 파일을 호스트 하 고 이러한 문제를 해결 합니다.


### <a name="self-hosted-setup-failed"></a>자체 호스팅 설치 실패    

#### <a name="symptoms"></a>증상

기존 IR을 제거 하거나 새 IR을 설치 하거나 기존 IR을 새 IR로 업그레이드할 수 없습니다.

#### <a name="cause"></a>원인

설치는 Windows Installer 서비스에 따라 달라 집니다. 설치 문제를 일으킬 수 있는 이유는 다음과 같습니다.
- 디스크 공간이 부족 합니다.
- 권한 부족
- 어떤 이유로 NT 서비스가 잠겼습니다.
- CPU 사용률이 너무 높습니다.
- MSI 파일이 저속 네트워크 위치에서 호스트 됨
- 일부 시스템 파일 또는 레지스트리를 실수로 작업 했습니다.


## <a name="self-hosted-ir-connectivity-issues"></a>자체 호스팅 IR 연결 문제

### <a name="self-hosted-integration-runtime-cant-connect-to-cloud-service"></a>자체 호스팅 integration runtime이 클라우드 서비스에 연결할 수 없음

#### <a name="symptoms"></a>증상

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
   > 서비스 URL은 Data Factory 위치에 따라 다를 수 있습니다. **ADF UI** > **연결** > **Integration Runtime** > **셀프 호스팅 IR 편집** > **노드** > **서비스 URL 보기** 에서 서비스 URL을 찾을 수 있습니다.
            
    예상되는 응답은 다음과 같습니다.
            
    ![PowerShell 명령 응답](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)
            
1. 예상되는 응답을 받지 못한 경우에는 상황에 따라 다음 방법 중 하나를 사용합니다.
            
    * "원격 이름을 확인할 수 없습니다"라는 메시지가 표시되는 경우 DNS(Domain Name System) 문제가 있는 것입니다. 이 문제를 해결하려면 네트워크 팀에 문의하세요.
    * "ssl/tls 인증서를 신뢰할 수 없습니다"라는 메시지가 표시되면 머신에서 https://wu2.frontend.clouddatahub.net/ 에 대한 인증서를 신뢰할 수 있는지 확인하고 인증서 관리자를 사용하여 퍼블릭 인증서를 설치합니다. 이 작업을 수행하면 문제가 완화됩니다.
    * **Windows** > **이벤트 뷰어(로그)**  > **애플리케이션 및 서비스 로그** > **Integration Runtime** 으로 이동하고 DNS, 방화벽 규칙 또는 회사 네트워크 설정으로 인해 발생한 오류를 확인합니다. 이러한 오류가 발생하면 연결을 강제로 닫습니다. 모든 회사에서 네트워크 설정을 사용자 지정했으므로 네트워크 팀에 문의하여 이러한 문제를 해결하세요.

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
> *    수신 허용 - 받는 사람 목록에 프록시 서버를 추가해야 하는지 여부를 확인합니다. 추가해야 하는 경우 [이러한 도메인](./data-movement-security-considerations.md#firewall-requirements-for-on-premisesprivate-network)이 수신 허용 - 받는 사람 목록에 있는지 확인합니다.
> *    프록시 서버에서 TLS/SSL 인증서 "wu2.frontend.clouddatahub.net/"을 신뢰할 수 있는지 확인합니다.
> *    프록시에서 Active Directory 인증을 사용하는 경우 서비스 계정을 프록시에 "Integration Runtime Service"로 액세스할 수 있는 사용자 계정으로 변경합니다.

### <a name="error-message-self-hosted-integration-runtime-node-logical-shir-is-in-inactive-running-limited-state"></a>오류 메시지: 자체 호스팅 Integration Runtime 노드/논리적 SHIR이 비활성/"실행 중(제한됨)" 상태입니다.

#### <a name="cause"></a>원인 

다음 스크린샷에 표시된 것처럼 자체 호스팅 Integration Runtime 노드가 **비활성** 상태일 수 있습니다.

![비활성 자체 호스팅 IR 노드](media/self-hosted-integration-runtime-troubleshoot-guide/inactive-self-hosted-ir-node.png)

이 동작은 노드가 서로 통신할 수 없는 경우에 발생합니다.

#### <a name="resolution"></a>해결 방법

1. 노드 호스팅 VM에 로그인합니다. **애플리케이션 및 서비스 로그** > **Integration Runtime** 에서 이벤트 뷰어을 열고 모든 오류 로그를 필터링합니다.

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

### <a name="connectivity-issue-between-self-hosted-ir-and-data-factory-or-self-hosted-ir-and-data-sourcesink"></a>자체 호스팅 IR과 Data Factory 또는 자체 호스팅 IR 및 데이터 원본/싱크에 대 한 연결 문제

네트워크 연결 문제를 해결 하려면 네트워크 추적을 수집 하 고,이를 사용 하는 방법을 이해 하 고, 실제 사례에서 Netmon 도구를 자체 호스팅 IR에서 적용 하기 전에 [netmon 추적을 분석](#how-to-analyze-netmon-trace) 하는 방법을 알고 있어야 합니다.

#### <a name="symptoms"></a>증상

경우에 따라 자체 호스팅 IR 및 Data Factory 간의 연결 문제를 해결 하는 경우는 다음과 같습니다. 

![HTTP 요청 실패](media/self-hosted-integration-runtime-troubleshoot-guide/http-request-error.png)

또는 자체 호스팅 IR 및 데이터 원본/싱크 사이에 있는 경우 다음 오류가 발생 합니다.

`Copy failed with error:Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Cannot connect to SQL Server: ‘IP address’`

`One or more errors occurred. An error occurred while sending the request. The underlying connection was closed: An unexpected error occurred on a receive. Unable to read data from the transport connection: An existing connection was forcibly closed by the remote host. An existing connection was forcibly closed by the remote host Activity ID.`

#### <a name="resolution"></a>해결 방법:

위의 문제가 발생할 경우 다음 지침을 참조 하 여 문제를 해결 하십시오.

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

- 이상적인 상황에서 TTL은 128이 되며,이는 Windows 시스템에서 Data Factory를 실행 하는 것을 의미 합니다. 아래 예제에 표시 된 것 처럼 *128 – 107 = 21 홉* 이 있습니다. 즉, TCP 3 핸드셰이크 중에 패키지에 대 한 21 개의 홉을 Data Factory에서 자체 호스팅 IR로 보냈습니다.
 
    ![TTL 107](media/self-hosted-integration-runtime-troubleshoot-guide/ttl-107.png)

    따라서 네트워크 팀과 협력 하 여 네 번째 홉이 자체 호스팅 IR에 있는지 확인 해야 합니다. Linux 시스템의 방화벽 인 경우 장치에서 TCP 3 핸드셰이크 후에 패키지를 다시 설정 하는 이유에 대 한 로그를 확인 합니다. 그러나 조사를 수행할 위치를 모를 경우에는 문제가 있는 시간에 자체 호스트 된 IR 및 방화벽에서 netmon 추적을 가져와이 패키지를 다시 설정 하 고 연결을 끊는 장치를 파악 합니다. 이 경우 네트워크 팀과 협력 하 여 앞으로 이동 해야 합니다.

### <a name="how-to-analyze-netmon-trace"></a>Netmon 추적을 분석 하는 방법

> [!NOTE] 
> 아래 지침은 netmon 추적에 적용 됩니다. Netmon 추적이 현재 지원 되지 않으므로 wireshark를 동일한 것으로 활용할 수 있습니다.

Netmon 추적을 수집 하 여 **8.8.8.8 888** 를 텔넷 하려고 하면 아래 추적을 확인할 수 있습니다.

![netmon 추적 1](media/self-hosted-integration-runtime-troubleshoot-guide/netmon-trace-1.png)

![netmon 추적 2](media/self-hosted-integration-runtime-troubleshoot-guide/netmon-trace-2.png)
 

즉, 포트 **888** 을 기반으로 **8.8.8.8** 서버 쪽에 TCP 연결을 설정할 수 없으므로 두 개의 **sy재전송** 추가 패키지를 볼 수 있습니다. 원본 **자체 host2.contoso.com** 는 첫 번째 패키지에서 **8.8.8.8** 에 연결할 수 없기 때문에 연결 하는 동안 유지 됩니다.

> [!TIP]
> - **부하 필터**  ->  **표준 필터**  ->  **주소**  ->  **IPv4 주소** 를 클릭 하면 됩니다.
> - 입력 **IPv4. Address = = 8.8.8.8** as filter를 입력 하 고 **적용** 을 클릭 합니다. 그 후에는 로컬 컴퓨터에서 대상 **8.8.8.8** 에 대 한 통신만 표시 됩니다.

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


### <a name="receiving-email-to-update-the-network-configuration-to-allow-communication-with-new-ip-addresses"></a>새 IP 주소와의 통신을 허용 하도록 네트워크 구성을 업데이트 하는 전자 메일 받기

#### <a name="email-notification-from-microsoft"></a>Microsoft의 전자 메일 알림

2020 년 11 월 8 일에 Azure Data Factory에 대 한 새 IP 주소와의 통신을 허용 하도록 네트워크 구성을 업데이트 하는 것이 좋습니다.

   ![메일 알림](media/self-hosted-integration-runtime-troubleshoot-guide/email-notification.png)

#### <a name="how-to-determine-if-you-are-impacted-by-this-notification"></a>이 알림의 영향을 받는지 확인 하는 방법

이 알림은 다음과 같은 시나리오에 영향을 줍니다.
##### <a name="scenario-1-outbound-communication-from-self-hosted-integration-runtime-running-on-premises-behind-the-corporate-firewall"></a>시나리오 1: 온-프레미스에서 실행 되는 자체 호스트 된 Integration Runtime의 아웃 바운드 통신을 회사 방화벽 뒤에서 실행
영향을 받는지 확인 하는 방법:
- 이 문서에서 설명 하는 방법을 사용 하 여 FQDN 이름에 따라 방화벽 규칙을 정의 하는 경우에는 영향을 받지 않습니다. [ip 주소에 대 한 방화벽 구성 및 허용 목록 설정](data-movement-security-considerations.md#firewall-configurations-and-allow-list-setting-up-for-ip-address-of-gateway)입니다.
- 회사 방화벽에서 아웃 바운드 Ip에 허용 목록을 명시적으로 사용 하도록 설정 하는 경우에는 영향을 받습니다.

영향을 받는 경우 수행할 작업: 2020 년 11 월 8 일에 최신 Data Factory IP 주소를 사용 하도록 네트워크 구성을 업데이트 하도록 네트워크 인프라 팀에 게 알립니다.  최신 IP 주소를 다운로드 하려면 [서비스 태그 IP 범위 다운로드 링크](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)로 이동 합니다.

##### <a name="scenario-2-outbound-communication-from-self-hosted-integration-runtime-running-on-an-azure-vm-inside-customer-managed-azure-virtual-network"></a>시나리오 2: 고객이 관리 하는 Azure virtual network 내에서 Azure VM에서 실행 되는 자체 호스팅 Integration Runtime의 아웃 바운드 통신
영향을 받는지 확인 하는 방법:
- 개인 네트워크에 자체 호스팅 Integration Runtime 포함 된 아웃 바운드 NSG 규칙이 있는지 확인 합니다. 아웃 바운드 제한이 없는 경우에는 영향을 주지 않습니다.
- 아웃 바운드 규칙 제한이 있는 경우 서비스 태그를 사용 하는지 여부를 확인 합니다. 서비스 태그를 사용 하는 경우 기존 서비스 태그 아래에 새 IP 범위를 변경 하거나 추가할 필요가 없습니다. 
 ![대상 검사](media/self-hosted-integration-runtime-troubleshoot-guide/destination-check.png)
- 그러나 Azure 가상 네트워크의 NSG 규칙에서 아웃 바운드 IP 주소에 대 한 허용 목록을 명시적으로 사용 하도록 설정 하는 경우에는 영향을 받습니다.

영향을 받는 경우 수행할 작업: 2020 년 11 월 8 일에 최신 Data Factory IP 주소를 사용 하도록 네트워크 인프라 팀에 Azure 가상 네트워크 구성에 대 한 NSG 규칙을 업데이트 하도록 알립니다.  최신 IP 주소를 다운로드 하려면 [서비스 태그 IP 범위 다운로드 링크](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)로 이동 합니다.

##### <a name="scenario-3-outbound-communication-from-ssis-integration-runtime-in-customer-managed-azure-virtual-network"></a>시나리오 3: 고객이 관리 하는 Azure virtual network에서 SSIS Integration Runtime의 아웃 바운드 통신
- SSIS Integration Runtime를 포함 하는 아웃 바운드 NSG 규칙이 개인 네트워크에 있는지 확인 합니다. 아웃 바운드 제한이 없는 경우에는 영향을 주지 않습니다.
- 아웃 바운드 규칙 제한이 있는 경우 서비스 태그를 사용 하는지 여부를 확인 합니다. 서비스 태그를 사용 하는 경우 기존 서비스 태그 아래에 새 IP 범위를 변경 하거나 추가할 필요가 없습니다.
- 그러나 Azure 가상 네트워크의 NSG 규칙에서 아웃 바운드 IP 주소에 대 한 허용 목록을 명시적으로 사용 하도록 설정 하는 경우에는 영향을 받습니다.

영향을 받는 경우 수행할 작업: 2020 년 11 월 8 일에 최신 Data Factory IP 주소를 사용 하도록 네트워크 인프라 팀에 Azure 가상 네트워크 구성에 대 한 NSG 규칙을 업데이트 하도록 알립니다.  최신 IP 주소를 다운로드 하려면 [서비스 태그 IP 범위 다운로드 링크](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)로 이동 합니다.

### <a name="could-not-establish-trust-relationship-for-the-ssltls-secure-channel"></a>SSLTLS 보안 채널에 대 한 트러스트 관계를 설정할 수 없습니다. 

#### <a name="symptoms"></a>증상

자체 호스팅 IR은 ADF 서비스에 연결할 수 없습니다.

CustomLogEvent 테이블에서 SHIR 이벤트 로그 또는 클라이언트 알림 로그를 확인 하 여 다음과 같은 오류 메시지를 찾을 수 있습니다.

`The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.The remote certificate is invalid according to the validation procedure.`

ADF 서비스의 서버 인증서를 확인 하는 방법:

브라우저에서 ADF 서비스 URL을 여는 것이 가장 간단한 방법입니다. 예를 들어 https://eu.frontend.clouddatahub.net/ SHIR가 설치 된 컴퓨터에서를 열고 서버 인증서 정보를 볼 수 있습니다.

  ![ADF 서비스의 서버 인증서 확인](media/self-hosted-integration-runtime-troubleshoot-guide/server-certificate.png)

  ![서버 인증서 경로 확인](media/self-hosted-integration-runtime-troubleshoot-guide/certificate-path.png)

#### <a name="cause"></a>원인

이 문제가 발생 하는 두 가지 가능한 이유는 다음과 같습니다.

- SHIR이 설치 된 컴퓨터에서 ADF 서비스 서버 인증서의 루트 CA를 신뢰할 수 없습니다. 
- 사용자 환경에서 프록시를 사용 하 고 있으며, ADF 서비스의 서버 인증서가 프록시로 바뀌고, 대체 된 서버 인증서는 SHIR이 설치 된 컴퓨터에서 트러스트 되지 않습니다.

#### <a name="solution"></a>솔루션

- 이유 1에서는 SHIR이 설치 된 컴퓨터에서 ADF 서버 인증서와 인증서 체인을 신뢰할 수 있는지 확인 합니다.
- 이유 2에서는 SHIR 컴퓨터에서 대체 된 루트 CA를 신뢰 하거나 ADF 서버 인증서를 교체 하지 않도록 프록시를 구성 합니다.

Windows에서 인증서를 신뢰 하는 방법에 대 한 자세한 내용은 [이 문서](https://docs.microsoft.com/skype-sdk/sdn/articles/installing-the-trusted-root-certificate) 를 참조 하세요.

#### <a name="additional-info"></a>추가 정보
DigiCert에서 서명 된 새 SSL 인증서를 배포 하는 중입니다. DigiCert Global Root G2가 신뢰할 수 있는 루트 CA에 있는지 확인 하세요.

  ![DigiCert Global Root G2](media/self-hosted-integration-runtime-troubleshoot-guide/trusted-root-ca-check.png)

그렇지 않은 경우 [여기](http://cacerts.digicert.com/DigiCertGlobalRootG2.crt )에서 다운로드 합니다. 

## <a name="self-hosted-ir-sharing"></a>자체 호스팅 IR 공유

### <a name="share-self-hosted-ir-from-a-different-tenant-is-not-supported"></a>다른 테 넌 트에서 자체 호스팅 IR 공유는 지원 되지 않습니다. 

#### <a name="symptoms"></a>증상

Azure Data Factory UI에서 자체 호스팅 IR을 공유 하는 동안 다른 데이터 팩터리가 다른 테 넌 트에 있지만 다른 테 넌 트에 있는 데이터 팩터리에서 자체 호스팅 IR을 공유할 수는 없습니다.

#### <a name="cause"></a>원인

자체 호스팅 IR은 교차 테 넌 트를 공유할 수 없습니다.


## <a name="next-steps"></a>다음 단계

문제 해결에 대 한 자세한 내용은 다음 리소스를 참조 하세요.

*  [Data Factory 블로그](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory 기능 요청](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure 비디오](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&A 질문 페이지](/answers/topics/azure-data-factory.html)
*  [Data Factory에 대 한 스택 오버플로 포럼](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Data Factory에 대한 Twitter 정보](https://twitter.com/hashtag/DataFactory)
*  [데이터 흐름 매핑 성능 가이드](concepts-data-flow-performance.md)