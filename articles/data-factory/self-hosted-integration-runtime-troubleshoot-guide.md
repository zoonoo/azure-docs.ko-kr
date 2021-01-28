---
title: Azure Data Factory에서 자체 호스팅 Integration Runtime 문제 해결
description: Azure Data Factory에서 자체 호스팅 Integration Runtime 문제를 해결하는 방법을 알아봅니다.
services: data-factory
author: lrtoyou1223
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 01/25/2021
ms.author: lle
ms.openlocfilehash: 8a722550d12d019e25ff39de27cc0df2c2762a01
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98942049"
---
# <a name="troubleshoot-self-hosted-integration-runtime"></a>자체 호스팅 Integration Runtime 문제 해결

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory의 자체 호스팅 IR (통합 런타임)에 대 한 일반적인 문제 해결 방법을 살펴봅니다.

## <a name="gather-self-hosted-ir-logs-from-azure-data-factory"></a>Azure Data Factory에서 자체 호스팅 IR 로그 수집

자체 호스팅 IR 또는 공유 IR에서 실행 되는 실패 한 작업의 경우 Azure Data Factory는 오류 로그 보기 및 업로드를 지원 합니다. 오류 보고서 ID를 얻으려면 여기에 설명 된 지침을 따르고 보고서 ID를 입력 하 여 알려진 관련 문제를 검색 합니다.

1. Data Factory에서 **파이프라인 실행** 을 선택 합니다.

1. 다음 스크린샷에 표시 된 것 처럼 **활동 실행** 의 **오류** 열에서 강조 표시 된 단추를 선택 하 여 활동 로그를 표시 합니다.

    !["모든 파이프라인 실행" 창에 있는 "작업 실행" 섹션의 스크린샷](media/self-hosted-integration-runtime-troubleshoot-guide/activity-runs-page.png)

    활동 로그는 실패 한 활동 실행에 대해 표시 됩니다.

    ![실패 한 작업에 대 한 활동 로그의 스크린샷](media/self-hosted-integration-runtime-troubleshoot-guide/send-logs.png) 
    
1. 추가 지원이 필요 하면 **로그 보내기** 를 선택 합니다.
 
   **Microsoft windows를 사용 하 여 자체 호스팅 IR (통합 런타임) 로그 공유가** 열립니다.

    !["Microsoft로 자체 호스팅 IR (통합 런타임) 로그 공유" 창을 스크린샷 합니다.](media/self-hosted-integration-runtime-troubleshoot-guide/choose-logs.png)

1. 전송 하려는 로그를 선택 합니다. 
    * *자체 호스팅 ir* 의 경우 실패 한 작업 또는 자체 호스팅 ir 노드의 모든 로그와 관련 된 로그를 업로드할 수 있습니다. 
    * *공유 IR* 의 경우 실패 한 작업과 관련 된 로그만 업로드할 수 있습니다.

1. 로그를 업로드 하는 경우 나중에 사용할 수 있도록 보고서 ID에 대 한 기록을 유지 하 여 문제를 해결 하는 데 도움이 필요 합니다.

    ![IR 로그의 업로드 진행률 창에 표시 된 보고서 ID의 스크린샷](media/self-hosted-integration-runtime-troubleshoot-guide/upload-logs.png)

> [!NOTE]
> 로그 보기 및 업로드 요청은 모든 온라인 자체 호스팅 IR 인스턴스에서 실행 됩니다. 로그가 없는 경우 모든 자체 호스팅 IR 인스턴스가 온라인 상태 인지 확인 합니다. 


## <a name="self-hosted-ir-general-failure-or-error"></a>자체 호스팅 IR 일반 실패 또는 오류

### <a name="out-of-memory-issue"></a>메모리 부족 문제

#### <a name="symptoms"></a>증상

OutOfMemoryException (OOM) 오류는 연결 된 IR 또는 자체 호스팅 IR을 사용 하 여 조회 작업을 실행 하려고 할 때 발생 합니다.

#### <a name="cause"></a>원인

IR 컴퓨터에서 메모리가 너무 많이 사용 되는 경우 새 작업에서 OOM 오류가 발생할 수 있습니다. 이 문제는 많은 수의 동시 작업으로 인해 발생할 수 있으며, 오류는 의도적입니다.

#### <a name="resolution"></a>해결 방법

IR 노드에서 리소스 사용 및 동시 작업 실행을 확인 합니다. 단일 IR 노드에서 동시에 너무 많이 실행 되지 않도록 활동 실행의 내부 및 트리거 시간을 조정 합니다.

### <a name="concurrent-jobs-limit-issue"></a>동시 작업 제한 문제

#### <a name="symptoms"></a>증상

Azure Data Factory 인터페이스에서 동시 작업 제한을 늘릴 때 프로세스가 상태 *업데이트* 를 중단 합니다.

예제 시나리오: 최대 동시 작업 수 값은 현재 24로 설정 되어 있으며 작업을 더 빠르게 실행할 수 있도록 개수를 늘려야 합니다. 입력할 수 있는 최 솟 값은 3이 고 최대값은 32입니다. 값을 24에서 32으로 높인 다음 **업데이트** 단추를 선택 합니다. 프로세스는 다음 스크린샷에 표시 된 것 처럼 상태를 *업데이트 하* 는 동안 중단 됩니다. 페이지를 새로 고치면 값이 24로 계속 표시 됩니다. 예상 대로 32으로 업데이트 되지 않았습니다.

!["업데이트 중" 상태에서 중단 된 프로세스를 표시 하는 통합 런타임의 노드 창 스크린샷](media/self-hosted-integration-runtime-troubleshoot-guide/updating-status.png)

#### <a name="cause"></a>원인

동시 작업 수에 대 한 제한은 컴퓨터의 논리 코어 및 메모리에 따라 달라 집니다. 값을 24와 같은 값으로 조정 하 고 결과를 확인 합니다.

> [!TIP] 
> - 논리 코어 수와 컴퓨터의 논리 코어 수를 확인 하는 방법에 대 한 자세한 내용은 [Windows 10의 CPU에서 코어 수를 확인 하는 4 가지 방법](https://www.top-password.com/blog/find-number-of-cores-in-your-cpu-on-windows-10/)을 참조 하세요.
> - Math .log를 계산 하는 방법을 알아보려면 로그 [계산기](https://www.rapidtables.com/calc/math/Log_Calculator.html)로 이동 합니다.


### <a name="self-hosted-ir-high-availability-ha-ssl-certificate-issue"></a>자체 호스팅 IR HA (고가용성) SSL 인증서 문제

#### <a name="symptoms"></a>증상

자체 호스팅 IR 작업 노드에서 다음 오류를 보고 했습니다.

"주 노드에서 공유 상태를 가져오지 못했습니다. tcp://abc.cloud.corp.Microsoft.com: 8060/ExternalService .svc/. 활동 ID: x.509 certificate CN = XXXXX, OU = test, O = Microsoft chain 빌드에 실패 했습니다. 사용한 인증서에 확인할 수 없는 신뢰 체인이 포함되어 있습니다. 인증서를 바꾸거나 certificateValidationMode를 변경하십시오. 해지 서버가 오프 라인 상태 이므로 해지 함수가 해지를 확인할 수 없습니다. "

#### <a name="cause"></a>원인

SSL/TLS 핸드셰이크와 관련 된 사례를 처리 하는 경우 인증서 체인 확인과 관련 된 몇 가지 문제가 발생할 수 있습니다. 

#### <a name="resolution"></a>해결 방법

- 다음은 x.509 인증서 체인 빌드 실패 문제를 해결 하는 빠르고 직관적인 방법입니다.
 
    1. 확인해야 하는 인증서를 내보냅니다. 이렇게 하려면 다음을 수행합니다.
    
       a. Windows에서 **시작** 을 선택 하 고 **인증서** 입력을 시작한 후 **컴퓨터 인증서 관리** 를 선택 합니다.
       
       b. 파일 탐색기의 왼쪽 창에서 확인할 인증서를 검색 하 고 마우스 오른쪽 단추를 클릭 한 다음 **모든 작업**  >  **내보내기** 를 선택 합니다.
    
        !["컴퓨터 인증서 관리" 창에서 인증서에 대 한 "모든 작업" > "내보내기" 제어의 스크린샷](media/self-hosted-integration-runtime-troubleshoot-guide/export-tasks.png)

    2. 내보낸 인증서를 클라이언트 컴퓨터에 복사 합니다. 
    3. 클라이언트 쪽의 명령 프롬프트 창에서 다음 명령을 실행 합니다. *\<certificate path>* 및를 *\<output txt file path>* 실제 경로로 바꾸어야 합니다.
    
        ```
        Certutil -verify -urlfetch    <certificate path>   >     <output txt file path> 
        ```

        예를 들면 다음과 같습니다.

        ```
        Certutil -verify -urlfetch c:\users\test\desktop\servercert02.cer > c:\users\test\desktop\Certinfo.txt
        ```
    4. 출력 TXT 파일에서 오류를 확인 합니다. TXT 파일의 끝에서 오류 요약을 찾을 수 있습니다.

        예를 들면 다음과 같습니다. 

        ![TXT 파일의 끝에 있는 오류 요약의 스크린샷](media/self-hosted-integration-runtime-troubleshoot-guide/error-summary.png)

        다음 스크린샷에 표시 된 것 처럼 로그 파일의 끝에 오류가 표시 되지 않으면 인증서 체인이 클라이언트 컴퓨터에서 성공적으로 작성 된 것을 고려할 수 있습니다.
        
        ![오류를 표시 하지 않는 로그 파일의 스크린샷](media/self-hosted-integration-runtime-troubleshoot-guide/log-file.png)      

- 인증서 파일에서 AIA (기관 정보 액세스), CDP (CRL 배포 지점) 또는 OCSP (온라인 인증서 상태 프로토콜) 파일 이름 확장명이 구성 된 경우 보다 직관적인 방법으로 확인할 수 있습니다.
 
    1. 다음 스크린샷에 표시 된 것 처럼 인증서 세부 정보를 확인 하 여이 정보를 가져옵니다.
    
        ![인증서 세부 정보 스크린샷](media/self-hosted-integration-runtime-troubleshoot-guide/certificate-detail.png)
    
    1. 다음 명령을 실행합니다. 을 *\<certificate path>* 인증서의 실제 경로로 바꾸어야 합니다.
    
        ```
          Certutil   -URL    <certificate path> 
        ```
    
        URL 검색 도구가 열립니다. 
        
    1. AIA, CDP 및 OCSP 파일 이름 확장명을 사용 하 여 인증서를 확인 하려면 **검색** 을 선택 합니다.

        ![URL 검색 도구의 스크린샷 및 검색 단추](media/self-hosted-integration-runtime-troubleshoot-guide/retrieval-button.png)
 
        AIA의 인증서 상태가 *확인* 되 고 CDP 또는 OCSP의 인증서 상태가 *확인* 되 면 인증서 체인을 성공적으로 빌드 했습니다.

        AIA 또는 CDP를 검색 하려고 할 때 실패 하는 경우 네트워크 팀과 협력 하 여 클라이언트 컴퓨터를 대상 URL에 연결할 수 있습니다. HTTP 경로 또는 LDAP (Lightweight Directory Access Protocol) 경로를 확인할 수 있으면 충분 합니다.

### <a name="self-hosted-ir-could-not-load-file-or-assembly"></a>자체 호스팅 IR에서 파일 또는 어셈블리를 로드할 수 없음

#### <a name="symptoms"></a>증상

다음과 같은 오류 메시지가 발생합니다.

"파일 또는 어셈블리 ' XXXXXXXXXXXXXXXX, Version = 4.0.2.0, Culture = 중립, PublicKeyToken = XXXXXXXXX ' 또는 해당 종속성 중 하나를 로드할 수 없습니다. 시스템은 지정된 파일을 찾을 수 없습니다. 활동 ID: 92693b45-b4bf-4fc8-89da-2d3dc56f27c3 "
 
보다 구체적인 오류 메시지는 다음과 같습니다. 

"파일 또는 어셈블리 ' System.valuetuple, Version = 4.0.2.0, Culture = 중립, PublicKeyToken = XXXXXXXXX ' 또는 해당 종속성 중 하나를 로드할 수 없습니다. 시스템은 지정된 파일을 찾을 수 없습니다. 활동 ID: 92693b45-b4bf-4fc8-89da-2d3dc56f27c3 "

#### <a name="cause"></a>원인

프로세스 모니터에서 다음과 같은 결과를 볼 수 있습니다.

[![프로세스 모니터의 경로 목록 스크린샷](media/self-hosted-integration-runtime-troubleshoot-guide/process-monitor.png)](media/self-hosted-integration-runtime-troubleshoot-guide/process-monitor.png#lightbox)

> [!TIP] 
> 프로세스 모니터에서 다음 스크린샷에 표시 된 것 처럼 필터를 설정할 수 있습니다.
>
> 위의 오류 메시지에는 DLL System.valuetuple이 관련 GAC ( *전역 어셈블리 캐시* ) 폴더, *C:\program Files\Microsoft integration Runtime\4.0\Gateway* 폴더 또는 *c:\program Files\Microsoft integration Runtime\4.0\Shared* 폴더에 있지 않다는 메시지가 표시 됩니다.
>
> 기본적으로 프로세스는 먼저 *GAC* 폴더에서 DLL을 로드 한 다음 *공유* 폴더에서, 마지막으로 *게이트웨이* 폴더에서 DLL을 로드 합니다. 따라서 유용한 경로에서 DLL을 로드할 수 있습니다.

<br>

![DLL에 대 한 필터를 나열 하는 "프로세스 모니터 필터" 페이지의 스크린샷](media/self-hosted-integration-runtime-troubleshoot-guide/set-filters.png)

#### <a name="resolution"></a>해결 방법

*C:\Program Files\Microsoft Integration Runtime\4.0\Gateway\DataScan* 폴더에서 *System.ValueTuple.dll* 파일을 찾을 수 있습니다. 이 문제를 해결 하려면 *System.ValueTuple.dll* 파일을 *C:\Program Files\Microsoft Integration Runtime\4.0\Gateway* 폴더에 복사 합니다.

동일한 방법을 사용 하 여 누락 된 다른 파일 또는 어셈블리 문제를 해결할 수 있습니다.

#### <a name="more-information-about-this-issue"></a>이 문제에 대 한 자세한 정보

*%Windir%\Microsoft.NET\assembly* 및 *%windir%\assembly* 아래에 *System.ValueTuple.dll* 표시 되는 이유는이는 .net 동작 이라고 하는 것입니다. 

다음 오류에서 *system.valuetuple* 어셈블리가 누락 된 것을 명확 하 게 알 수 있습니다. 이 문제는 응용 프로그램이 *System.ValueTuple.dll* 어셈블리를 확인 하려고 할 때 발생 합니다.
 
" \<LogProperties> \<ErrorInfo> [{" Code ": 0," Message ":" ' Npgsql. poolmanager '의 형식 이니셜라이저가 예외를 throw 했습니다. "," EventType ": 0," Category ": 5," Data ": {} ," MsgId ": Null," exceptiontype ":" system.typeinitializationexception "," Source ":" npgsql "," StackTrace ":" "," InnerEventInfos ": [{" Code ": 0," Message ":" 파일 또는 어셈블리 ' system.valuetuple, Version = 4.0.2.0, Culture = 중립, PublicKeyToken = XXXXXXXXX ' 또는 해당 종속성 중 하나를 로드할 수 없습니다. 시스템이 지정 된 파일을 찾을 수 없습니다. "," EventType ": 0," Category ": 5," Data ": {} ," msgid":null,"ExceptionType":"System "," Source ":" Npgsql "," StackTrace ":" "," InnerEventInfos ": []}]}] \</ErrorInfo> \</LogProperties> "
 
GAC에 대 한 자세한 내용은 [전역 어셈블리 캐시](/dotnet/framework/app-domains/gac)를 참조 하세요.


### <a name="self-hosted-integration-runtime-authentication-key-is-missing"></a>자체 호스팅 통합 런타임 인증 키가 누락 되었습니다.

#### <a name="symptoms"></a>증상

자체 호스팅 통합 런타임은 인증 키 없이 갑자기 오프 라인 상태가 되 고 이벤트 로그에 다음과 같은 오류 메시지가 표시 됩니다. 

"인증 키가 아직 할당 되지 않았습니다."

![인증 키가 아직 할당 되지 않은 것을 보여 주는 통합 런타임 이벤트 창의 스크린샷](media/self-hosted-integration-runtime-troubleshoot-guide/key-missing.png)

#### <a name="cause"></a>원인

- 자체 호스팅 IR 노드나 Azure Portal의 논리 자체 호스팅 IR이 삭제 되었습니다.
- 새로 제거가 수행 되었습니다.

#### <a name="resolution"></a>해결 방법

위의 원인이 모두 적용 되지 않는 경우 *%Programdata%\Microsoft\Data Transfer\DataManagementGateway* 폴더로 이동 하 여 *구성* 파일이 삭제 되었는지 여부를 확인할 수 있습니다. 삭제 된 경우 Netwrix 문서 [Windows 파일 서버에서 파일을 삭제 한 사람 검색](https://www.netwrix.com/how_to_detect_who_deleted_file.html)의 지침을 따릅니다.

![구성 파일을 확인 하기 위한 이벤트 로그 세부 정보 창의 스크린샷](media/self-hosted-integration-runtime-troubleshoot-guide/configurations-file.png)


### <a name="cant-use-self-hosted-ir-to-bridge-two-on-premises-datastores"></a>자체 호스팅 IR을 사용 하 여 두 개의 온-프레미스 데이터 저장소에 연결할 수 없습니다.

#### <a name="symptoms"></a>증상

원본 및 대상 데이터 저장소 모두에 대해 자체 호스트 된 IRs를 만든 후에 두 IRs를 연결 하 여 복사 작업을 완료 합니다. 데이터 저장소가 다른 가상 네트워크에 구성 되어 있거나 데이터 저장소에서 게이트웨이 메커니즘을 인식할 수 없는 경우 다음 오류 중 하나가 표시 됩니다. 

* "대상 IR에서 원본 드라이버를 찾을 수 없습니다."
* "대상 IR에서 원본에 액세스할 수 없습니다."
 
#### <a name="cause"></a>원인

자체 호스팅 IR은 각 데이터 저장소에 대해 설치 해야 하는 클라이언트 에이전트가 아니라 복사 작업의 중앙 노드로 설계 되었습니다.
 
이 경우 ir을 사용 하 여 각 데이터 저장소에 대 한 연결 된 서비스를 만들어야 합니다. 그러면 IR는 네트워크를 통해 두 데이터 저장소에 액세스할 수 있어야 합니다. IR이 원본 데이터 저장소 또는 대상 데이터 저장소 또는 세 번째 컴퓨터에 설치 되어 있는지 여부는 중요 하지 않습니다. 서로 다른 IRs를 사용 하 여 두 개의 연결 된 서비스를 만들었지만 동일한 복사 작업에서 사용 하는 경우 대상 IR이 사용 되며 대상 IR 컴퓨터에 두 데이터 저장소에 대 한 드라이버를 설치 해야 합니다.

#### <a name="resolution"></a>해결 방법

대상 IR에 원본 및 대상 데이터 저장소의 드라이버를 모두 설치 하 고 원본 데이터 저장소에 액세스할 수 있는지 확인 합니다.
 
두 개의 데이터 저장소 간에 네트워크를 통해 트래픽을 전달할 수 없는 경우 (예를 들어 두 가상 네트워크에서 구성 된 경우), IR이 설치 된 경우에도 하나의 작업에서 복사가 완료 되지 않을 수 있습니다. 단일 작업으로 복사를 완료할 수 없는 경우 각각 환풍구에 두 개의 IRs를 사용 하 여 두 개의 복사 작업을 만들 수 있습니다. 
* 데이터 저장소 1에서 Azure Blob Storage로 IR을 복사 합니다.
* Azure Blob Storage에서 다른 IR을 v데이터 저장소 2로 복사 합니다. 

이 솔루션은 IR을 사용 하 여 연결이 끊어진 두 데이터 저장소를 연결 하는 브리지를 만드는 요구 사항을 시뮬레이트할 수 있습니다.


### <a name="credential-sync-issue-causes-credential-loss-from-ha"></a>자격 증명 동기화 문제로 인해 HA에서 자격 증명 손실이 발생 합니다.

#### <a name="symptoms"></a>증상

데이터 원본 자격 증명 "XXXXXXXXXX"이 페이로드를 사용 하 여 현재 integration runtime 노드에서 삭제 되 면 다음과 같은 오류 메시지가 나타납니다.

"Azure Portal에서 링크 서비스를 삭제 하거나 작업에 잘못 된 페이로드가 있으면 자격 증명을 사용 하 여 새 링크 서비스를 다시 만드십시오."

#### <a name="cause"></a>원인

자체 호스팅 IR은 두 노드를 사용 하 여 HA 모드로 기본 제공 되지만 노드는 자격 증명 동기화 상태가 아닙니다. 즉, 디스패처 노드에 저장 된 자격 증명이 다른 작업자 노드와 동기화 되지 않습니다. 디스패처 노드에서 작업자 노드에 대 한 장애 조치 (failover)가 발생 하 고 자격 증명이 이전 디스패처 노드에만 있는 경우 자격 증명에 액세스 하려고 할 때 작업이 실패 하 고 이전 오류가 표시 됩니다.

#### <a name="resolution"></a>해결 방법

이 문제를 방지 하는 유일한 방법은 두 노드가 자격 증명 동기화 상태에 있는지 확인 하는 것입니다. 동기화 되지 않은 경우 새 디스패처에 대 한 자격 증명을 다시 입력 해야 합니다.


### <a name="cant-choose-the-certificate-because-the-private-key-is-missing"></a>개인 키가 없어서 인증서를 선택할 수 없습니다.

#### <a name="symptoms"></a>증상

* PFX 파일을 인증서 저장소로 가져왔습니다.
* IR Configuration Manager UI를 통해 인증서를 선택 하면 다음과 같은 오류 메시지가 표시 됩니다.

   "인트라넷 통신 암호화 모드를 변경 하지 못했습니다. 인증서 ' \<*certificate name*> '에 키 교환이 가능한 개인 키가 없거나 프로세스에 개인 키에 대 한 액세스 권한이 없는 것일 수 있습니다. 자세한 내용은 내부 예외를 참조 하십시오. "

    !["개인 키 누락" 오류 메시지를 표시 하는 Integration Runtime Configuration Manager 설정 창의 스크린샷](media/self-hosted-integration-runtime-troubleshoot-guide/private-key-missing.png)

#### <a name="cause"></a>원인

- 사용자 계정의 권한 수준이 낮고 개인 키에 액세스할 수 없습니다.
- 인증서가 서명으로 생성 되었지만 키 교환이 아닙니다.

#### <a name="resolution"></a>해결 방법

* UI를 작동 하려면 개인 키에 액세스할 수 있는 적절 한 권한이 있는 계정을 사용 합니다.  
* 다음 명령을 실행 하 여 인증서를 가져옵니다.
    
    ```
    certutil -importpfx FILENAME.pfx AT_KEYEXCHANGE
    ```

## <a name="self-hosted-ir-setup"></a>자체 호스팅 IR 설정

### <a name="integration-runtime-registration-error"></a>Integration runtime 등록 오류 

#### <a name="symptoms"></a>증상

경우에 따라 다음과 같은 이유 중 하나로 다른 계정으로 자체 호스팅 IR을 실행할 수 있습니다.
- 회사 정책은 서비스 계정을 허용 하지 않습니다.
- 일부 인증이 필요 합니다.

서비스 창에서 서비스 계정을 변경한 후에는 integration runtime의 작동이 중지 되 고 다음과 같은 오류 메시지가 표시 될 수 있습니다.

"등록 하는 동안 Integration Runtime(자체 호스팅) 노드에 오류가 발생 했습니다. Integration Runtime(자체 호스팅) 호스트 서비스에 연결할 수 없습니다. "

![IR 등록 오류를 표시 하는 Integration Runtime Configuration Manager 창의 스크린샷](media/self-hosted-integration-runtime-troubleshoot-guide/ir-registration-error.png)

#### <a name="cause"></a>원인

대부분의 리소스는 서비스 계정에만 부여 됩니다. 서비스 계정을 다른 계정으로 변경 하는 경우 모든 종속 리소스의 권한은 변경 되지 않은 상태로 유지 됩니다.

#### <a name="resolution"></a>해결 방법

Integration runtime 이벤트 로그로 이동 하 여 오류를 확인 합니다.

![런타임 오류가 발생 했음을 보여 주는 IR 이벤트 로그의 스크린샷](media/self-hosted-integration-runtime-troubleshoot-guide/ir-event-log.png)

* 이벤트 로그의 오류가 "System.unauthorizedaccessexception" 인 경우 다음을 수행 하십시오.

    1. Windows 서비스 패널에서 *Diahostservice* 로그온 서비스 계정을 확인 합니다.

        ![로그온 서비스 계정 속성 창의 스크린샷](media/self-hosted-integration-runtime-troubleshoot-guide/logon-service-account.png)

    1. 로그온 서비스 계정에 *%programdata%\Microsoft\DataTransfer\DataManagementGateway* 폴더에 대 한 읽기/쓰기 권한이 있는지 확인 하십시오.

        - 기본적으로 서비스 로그온 계정이 변경 되지 않은 경우 읽기/쓰기 권한이 있어야 합니다.

            ![서비스 사용 권한 창의 스크린샷](media/self-hosted-integration-runtime-troubleshoot-guide/service-permission.png)

        - 서비스 로그온 계정을 변경한 경우 다음을 수행 하 여 문제를 완화 합니다.
 
            a. 현재 자체 호스팅 IR을 완전히 제거 합니다.   
            b. 자체 호스팅 IR 비트를 설치 합니다.  
            c. 다음을 수행 하 여 서비스 계정을 변경 합니다.  

             i. 자체 호스팅 IR 설치 폴더로 이동한 다음 *Microsoft Integration Runtime\4.0\Shared* 폴더로 전환 합니다.  
             ii. 상승 된 권한을 사용 하 여 명령 프롬프트 창을 엽니다. 및를 사용자 *\<user>* *\<password>* 이름 및 암호로 바꾸고 다음 명령을 실행 합니다.   
                `dmgcmd.exe -SwitchServiceAccount "<user>" "<password>"`  
             iii. LocalSystem 계정으로 변경 하려면이 계정에 올바른 형식을 사용 해야 합니다. `dmgcmd.exe -SwitchServiceAccount "NT Authority\System" ""`  
                다음 형식을 사용 *하지* 마십시오. `dmgcmd.exe -SwitchServiceAccount "LocalSystem" ""`     
             iv. 필요에 따라 로컬 시스템의 권한이 관리자 보다 높기 때문에 "서비스"에서 직접 변경할 수도 있습니다.  
             v. IR 서비스 로그온 계정에 로컬/도메인 사용자를 사용할 수 있습니다.            

            d. Integration runtime을 등록 합니다.

* 오류가 "서비스 ' Integration Runtime 서비스 ' (DIAHostService)를 시작 하지 못했습니다. 시스템 서비스를 시작할 수 있는 충분 한 권한이 있는지 확인 하 고, 다음을 수행 합니다.

    1. Windows 서비스 패널에서 *Diahostservice* 로그온 서비스 계정을 확인 합니다.
    
        ![서비스 계정에 대 한 "로그온" 창의 스크린샷](media/self-hosted-integration-runtime-troubleshoot-guide/logon-service-account.png)

    1. Windows 서비스를 시작 하려면 로그온 서비스 계정에 **서비스로 로그온** 권한이 있는지 확인 하십시오.

        !["서비스로 로그온" 속성 창의 스크린샷](media/self-hosted-integration-runtime-troubleshoot-guide/logon-as-service.png)

#### <a name="more-information"></a>자세한 정보

사용자의 경우 위의 두 해결 패턴 중 어느 것도 적용 되지 않으면 다음 Windows 이벤트 로그를 수집 해 보십시오. 
- 응용 프로그램 및 서비스 로그 > Integration Runtime
- Windows 로그 > 응용 프로그램

### <a name="cant-find-the-register-button-to-register-a-self-hosted-ir"></a>자체 호스팅 IR을 등록 하기 위한 등록 단추를 찾을 수 없습니다.    

#### <a name="symptoms"></a>증상

자체 호스팅 IR을 등록 하면 Configuration Manager 창에 **등록** 단추가 표시 되지 않습니다.

![통합 런타임 노드가 등록 되지 않은 메시지를 표시 하는 Configuration Manager 창의 스크린샷](media/self-hosted-integration-runtime-troubleshoot-guide/no-register-button.png)

#### <a name="cause"></a>원인

Integration Runtime 3.0의 릴리스를 기준으로 기존 통합 런타임 노드의 **등록** 단추를 제거 하 여 더 깔끔하고 안전 하 게 환경을 사용할 수 있습니다. 노드가 온라인 상태 인지 여부에 관계 없이 integration runtime에 등록 된 경우 이전 노드를 제거 하 여 다른 통합 런타임에 다시 등록 한 다음 노드를 설치 하 고 등록 합니다.

#### <a name="resolution"></a>해결 방법

1. 제어판에서 기존 통합 런타임을 제거 합니다.

    > [!IMPORTANT] 
    > 다음 프로세스에서 **예** 를 선택 합니다. 제거 프로세스 동안 데이터를 유지 하지 않습니다.

    ![통합 런타임에서 모든 사용자 데이터를 삭제 하는 "예" 단추의 스크린샷](media/self-hosted-integration-runtime-troubleshoot-guide/delete-data.png)

1. Integration runtime 설치 관리자 MSI 파일이 없는 경우 [다운로드 센터](https://www.microsoft.com/en-sg/download/details.aspx?id=39717) 로 이동 하 여 최신 통합 런타임을 다운로드 합니다.
1. MSI 파일을 설치 하 고 통합 런타임을 등록 합니다.


### <a name="unable-to-register-the-self-hosted-ir-because-of-localhost"></a>Localhost로 인해 자체 호스팅 IR을 등록할 수 없습니다.    

#### <a name="symptoms"></a>증상

Get_LoopbackIpOrName를 사용 하는 경우 새 컴퓨터에 자체 호스팅 IR을 등록할 수 없습니다.

**디버그:** 런타임 오류가 발생 했습니다.
'Microsoft.DataTransfer.DIAgentHost.DataSourceCache'의 형식 이니셜라이저가 예외를 throw했습니다.
데이터베이스 조회를 수행 하는 동안 복구할 수 없는 오류가 발생 했습니다.
 
**예외 정보:** System.typeinitializationexception: ' DataTransfer. DataSourceCache '에 대 한 형식 이니셜라이저가 예외를 throw 했습니다. ---> GetAddrInfo: 시스템에서 데이터베이스를 조회 하는 동안 복구할 수 없는 오류가 발생 했습니다 (문자열 이름).

#### <a name="cause"></a>원인

이 문제는 일반적으로 localhost를 확인할 때 발생 합니다.

#### <a name="resolution"></a>해결 방법

Localhost IP 주소 127.0.0.1을 사용 하 여 파일을 호스팅하고 문제를 해결 합니다.

### <a name="self-hosted-setup-failed"></a>자체 호스팅 설치 실패    

#### <a name="symptoms"></a>증상

기존 IR을 제거 하거나, 새 IR을 설치 하거나, 기존 IR을 새 IR로 업그레이드할 수 없습니다.

#### <a name="cause"></a>원인

Integration runtime 설치는 Windows Installer 서비스에 따라 달라 집니다. 다음과 같은 이유로 설치 문제가 발생할 수 있습니다.
- 사용 가능한 디스크 공간이 부족 합니다.
- 권한이 부족 합니다.
- Windows NT 서비스가 잠겨 있습니다.
- CPU 사용률이 너무 높습니다.
- MSI 파일은 저속 네트워크 위치에서 호스팅됩니다.
- 일부 시스템 파일 또는 레지스트리를 실수로 처리 했습니다.

### <a name="the-ir-service-account-failed-to-fetch-certificate-access"></a>IR 서비스 계정에서 인증서 액세스를 가져오지 못했습니다.

#### <a name="symptoms"></a>증상

Microsoft Integration Runtime Configuration Manager를 통해 자체 호스팅 IR을 설치 하면 신뢰할 수 있는 CA (인증 기관)가 포함 된 인증서가 생성 됩니다. 두 노드 간의 통신을 암호화 하기 위해 인증서를 적용할 수 없으며 다음과 같은 오류 메시지가 표시 됩니다. 

"인트라넷 통신 암호화 모드를 변경 하지 못했습니다. ' ' 인증서에 대 한 액세스를 Integration Runtime 서비스 계정에 부여 하지 못했습니다 \<*certificate name*> . 오류 코드 103 "

!["..." 라는 오류 메시지를 표시 하는 스크린샷 Integration Runtime 서비스 계정 인증서 액세스를 부여 하지 못했습니다. "](media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-account-certificate-error.png)

#### <a name="cause"></a>원인

인증서가 아직 지원 되지 않는 KSP (키 저장소 공급자) 저장소를 사용 하 고 있습니다. 즉, 자체 호스팅 IR은 CSP (암호화 서비스 공급자) 저장소만 지원 합니다.

#### <a name="resolution"></a>해결 방법

이 경우 CSP 인증서를 사용 하는 것이 좋습니다.

**해결 방법 1** 

인증서를 가져오려면 다음 명령을 실행 합니다.

`Certutil.exe -CSP "CSP or KSP" -ImportPFX FILENAME.pfx`

![인증서를 가져오기 위한 certutil 명령의 스크린샷](media/self-hosted-integration-runtime-troubleshoot-guide/use-certutil.png)

**해결 방법 2** 

인증서를 변환 하려면 다음 명령을 실행 합니다.

`openssl pkcs12 -in .\xxxx.pfx -out .\xxxx_new.pem -password pass: <EnterPassword>`
`openssl pkcs12 -export -in .\xxxx_new.pem -out xxxx_new.pfx`

변환 전후:

![인증서 변환 전의 결과 스크린샷](media/self-hosted-integration-runtime-troubleshoot-guide/before-certificate-change.png)

![인증서 변환 후 결과의 스크린샷](media/self-hosted-integration-runtime-troubleshoot-guide/after-certificate-change.png)

### <a name="self-hosted-integration-runtime-version-5x"></a>자체 호스팅 integration runtime 버전 5.x
Azure Data Factory 자체 호스팅 통합 런타임의 버전 5.x로 업그레이드 하는 경우 **.NET Framework runtime 4.7.2** 이상이 필요 합니다. 다운로드 페이지에서 최신 4.x 버전 및 최신 버전의 5. x 버전에 대 한 다운로드 링크를 찾을 수 있습니다. 

Azure Data Factory v2 고객의 경우:
- 자동 업데이트가 설정 되어 있고 이미 .NET Framework 런타임을 4.7.2 이상으로 업그레이드 한 경우 자체 호스팅 통합 런타임이 최신 .x 버전으로 자동 업그레이드 됩니다.
- 자동 업데이트가 설정 되어 있고 .NET Framework 런타임을 4.7.2 이상으로 업그레이드 하지 않은 경우 자체 호스팅 통합 런타임이 최신 .x 버전으로 자동 업그레이드 되지 않습니다. 자체 호스팅 통합 런타임은 현재 4.x 버전에 그대로 유지 됩니다. 포털 및 자체 호스팅 통합 런타임 클라이언트에서 .NET Framework 런타임 업그레이드에 대 한 경고를 볼 수 있습니다.
- 자동 업데이트가 꺼져 있고 이미 .NET Framework 런타임을 4.7.2 이상으로 업그레이드 한 경우에는 최신 5.x를 수동으로 다운로드 하 여 컴퓨터에 설치할 수 있습니다.
- 자동 업데이트가 해제 되어 .NET Framework 런타임을 4.7.2 이상으로 업그레이드 하지 않은 경우 자체 호스팅 integration runtime 5.x를 수동으로 설치 하 고 키를 등록 하려고 하면 .NET Framework 런타임 버전을 먼저 업그레이드 해야 합니다.


Azure Data Factory v1 고객의 경우:
- 자체 호스팅 integration runtime 5.x는 Azure Data Factory v1을 지원 하지 않습니다.
- 자체 호스팅 통합 런타임은 최신 버전인 4.x로 자동 업그레이드 됩니다. 최신 버전의 4.x는 만료 되지 않습니다. 
- 자체 호스팅 integration runtime 5.x를 수동으로 설치 하 고 키를 등록 하는 경우 자체 호스팅 integration runtime 5.x는 Azure Data Factory v1을 지원 하지 않는다는 알림이 표시 됩니다.


## <a name="self-hosted-ir-connectivity-issues"></a>자체 호스팅 IR 연결 문제

### <a name="self-hosted-integration-runtime-cant-connect-to-the-cloud-service"></a>자체 호스팅 integration runtime이 클라우드 서비스에 연결할 수 없음

#### <a name="symptoms"></a>증상

자체 호스팅 통합 런타임을 등록 하려고 하면 Configuration Manager 다음 오류 메시지가 표시 됩니다.

"등록 하는 동안 Integration Runtime(자체 호스팅) 노드에 오류가 발생 했습니다."

!["등록 하는 동안 Integration Runtime(자체 호스팅) 노드에 오류가 발생 했습니다." 메시지의 스크린샷](media/self-hosted-integration-runtime-troubleshoot-guide/unable-to-connect-to-cloud-service.png)

#### <a name="cause"></a>원인 

자체 호스팅 IR은 Azure Data Factory service 백 엔드에 연결할 수 없습니다. 이 문제는 일반적으로 방화벽의 네트워크 설정에 의해 발생 합니다.

#### <a name="resolution"></a>해결 방법

1. Integration runtime 서비스가 실행 중인지 확인 하십시오. 그렇다면 2 단계로 이동 합니다.
    
   ![자체 호스팅 IR 서비스가 실행 중임을 보여 주는 스크린샷](media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-running-status.png)
    
1. 자체 호스팅 IR (기본 설정)에 프록시가 구성 되어 있지 않으면 자체 호스팅 통합 런타임이 설치 된 컴퓨터에서 다음 PowerShell 명령을 실행 합니다.

    ```powershell
    (New-Object System.Net.WebClient).DownloadString("https://wu2.frontend.clouddatahub.net/")
    ```
        
   > [!NOTE]     
   > 서비스 URL은 데이터 팩터리 인스턴스의 위치에 따라 다를 수 있습니다. 서비스 url을 찾으려면 **ADF UI**  >  **연결**  >  **통합 런타임**  >  **자체 호스팅 IR**  >  **노드** 편집  >  **서비스 url 보기** 를 선택 합니다.
            
    예상되는 응답은 다음과 같습니다.
            
    ![PowerShell 명령 응답의 스크린샷](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)
            
1. 예상한 응답이 수신 되지 않으면 다음 방법 중 하나를 적절 하 게 사용 합니다.
            
    * "원격 이름을 확인할 수 없습니다"라는 메시지가 표시되는 경우 DNS(Domain Name System) 문제가 있는 것입니다. 네트워크 팀에 문의 하 여 문제를 해결 하십시오.
    * "Ssl/tls 인증서를 신뢰할 수 없습니다." 라는 메시지가 표시 되 면 [인증서를 확인](https://wu2.frontend.clouddatahub.net/) 하 여 컴퓨터에서 신뢰할 수 있는지 여부를 확인 한 다음 인증서 관리자를 사용 하 여 공용 인증서를 설치 합니다. 이 작업을 수행하면 문제가 완화됩니다.
    * **Windows**  >  **이벤트 뷰어 (로그)**  >  **응용 프로그램 및 서비스 로그**  >  **Integration Runtime** 로 이동 하 고 DNS, 방화벽 규칙 또는 회사 네트워크 설정에 의해 발생 한 오류를 확인 합니다. 이러한 오류가 발견 되 면 연결을 강제로 닫습니다. 모든 회사에는 고유한 사용자 지정 네트워크 설정이 있으므로 네트워크 팀에 문의 하 여 이러한 문제를 해결 하세요.

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
            
![필요한 Powershell 명령 응답의 스크린샷](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)

> [!NOTE] 
> 프록시 고려 사항:
> * 프록시 서버를 안전한 받는 사람 목록에 배치 해야 하는지 여부를 확인 합니다. 추가해야 하는 경우 [이러한 도메인](./data-movement-security-considerations.md#firewall-requirements-for-on-premisesprivate-network)이 수신 허용 - 받는 사람 목록에 있는지 확인합니다.
> * 프록시 서버에서 SSL/TLS 인증서 "wu2.frontend.clouddatahub.net/"를 신뢰할 수 있는지 여부를 확인 합니다.
> * 프록시에서 Active Directory 인증을 사용하는 경우 서비스 계정을 프록시에 "Integration Runtime Service"로 액세스할 수 있는 사용자 계정으로 변경합니다.

### <a name="error-message-self-hosted-integration-runtime-nodelogical-self-hosted-ir-is-in-inactive-running-limited-state"></a>오류 메시지: 자체 호스팅 통합 런타임 노드/논리 자체 호스팅 IR은 비활성/"실행 중 (제한 됨)" 상태입니다.

#### <a name="cause"></a>원인 

다음 스크린샷에 표시 된 것 처럼 자체 호스팅 통합 런타임 노드에는 **비활성** 상태가 있을 수 있습니다.

![비활성 상태의 자체 호스팅 통합 런타임 노드 스크린샷](media/self-hosted-integration-runtime-troubleshoot-guide/inactive-self-hosted-ir-node.png)

이 동작은 노드가 서로 통신할 수 없는 경우에 발생합니다.

#### <a name="resolution"></a>해결 방법

1. 노드 호스트 VM (가상 컴퓨터)에 로그인 합니다. **응용 프로그램 및 서비스 로그**  >  **Integration Runtime** 에서 이벤트 뷰어를 열고 오류 로그를 필터링 합니다.

1. 오류 로그에 다음 오류가 포함 되어 있는지 확인 하십시오. 
    
    ```
    System.ServiceModel.EndpointNotFoundException: Could not connect to net.tcp://xxxxxxx.bwld.com:8060/ExternalService.svc/WorkerManager. The connection attempt lasted for a time span of 00:00:00.9940994. TCP error code 10061: No connection could be made because the target machine actively refused it 10.2.4.10:8060. 
    System.Net.Sockets.SocketException: No connection could be made because the target machine actively refused it. 
    10.2.4.10:8060
    at System.Net.Sockets.Socket.DoConnect(EndPoint endPointSnapshot, SocketAddress socketAddress)
    at System.Net.Sockets.Socket.Connect(EndPoint remoteEP)
    at System.ServiceModel.Channels.SocketConnectionInitiator.Connect(Uri uri, TimeSpan timeout)
    ```
       
1. 이 오류가 표시 되 면 명령 프롬프트 창에서 다음 명령을 실행 합니다. 

   ```
   telnet 10.2.4.10 8060
   ```
   
1. 다음 스크린샷에 표시 된 "호스트에 대 한 연결을 열 수 없습니다." 명령줄 오류를 수신 하는 경우이 문제를 해결 하려면 IT 부서에 문의 하세요. 성공적으로 텔넷 된 후에도 integration runtime 노드 상태에 문제가 있는 경우 Microsoft 지원에 문의 하세요.
        
   !["호스트에 대 한 연결을 열 수 없습니다." 명령줄 오류 스크린샷](media/self-hosted-integration-runtime-troubleshoot-guide/command-line-error.png)
        
1. 오류 로그에 다음 항목이 포함 되어 있는지 확인 하십시오.

    ```
    Error log: Cannot connect to worker manager: net.tcp://xxxxxx:8060/ExternalService.svc/ No DNS entries exist for host azranlcir01r1. No such host is known Exception detail: System.ServiceModel.EndpointNotFoundException: No DNS entries exist for host xxxxx. ---> System.Net.Sockets.SocketException: No such host is known at System.Net.Dns.GetAddrInfo(String name) at System.Net.Dns.InternalGetHostByName(String hostName, Boolean includeIPv6) at System.Net.Dns.GetHostEntry(String hostNameOrAddress) at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri) --- End of inner exception stack trace --- Server stack trace: at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri)
    ```
    
1. 이 문제를 해결 하려면 다음 방법 중 하나 또는 모두를 시도 합니다.
    - 모든 노드를 동일한 도메인에 배치 합니다.
    - 호스트 된 모든 VM의 호스트 파일에 있는 호스트에 IP를 추가 합니다.

### <a name="connectivity-issue-between-the-self-hosted-ir-and-your-data-factory-instance-or-the-self-hosted-ir-and-the-data-source-or-sink"></a>자체 호스팅 IR과 데이터 팩터리 인스턴스 또는 자체 호스팅 IR 및 데이터 원본 또는 싱크 간의 연결에 문제가 있습니다.

네트워크 연결 문제를 해결 하려면 네트워크 추적을 수집 하 고,이를 사용 하는 방법을 이해 하 고, netmon [(Microsoft 네트워크 모니터) 추적을 분석](#analyze-the-netmon-trace) 하 여 자체 호스팅 IR에서 실제 사례에 netmon 도구를 적용 하는 방법을 알고 있어야 합니다.

#### <a name="symptoms"></a>증상

다음 스크린샷에 표시 된 것과 같이 자체 호스팅 IR 및 데이터 팩터리 인스턴스 간의 특정 연결 문제를 해결 해야 하는 경우도 있습니다. 또는 자체 호스팅 IR과 데이터 원본 또는 싱크 사이에서 문제를 해결 해야 할 수도 있습니다. 

!["처리 된 HTTP 요청 실패" 메시지의 스크린샷](media/self-hosted-integration-runtime-troubleshoot-guide/http-request-error.png)

두 경우 모두 다음과 같은 오류가 발생할 수 있습니다.

* "다음 오류가 발생 하 여 복사 하지 못했습니다. DataTransfer. HybridDeliveryException, Message = SQL Server에 연결할 수 없습니다. ' IP 주소 '"

* "하나 이상의 오류가 발생 했습니다. 요청을 보내는 중 오류가 발생했습니다. 기본 연결이 닫혔습니다. 수신에서 예기치 않은 오류가 발생 했습니다. 전송 연결에서 데이터를 읽을 수 없습니다. 기존 연결이 원격 호스트에 의해 강제로 끊겼습니다. 기존 연결이 원격 호스트 작업 ID에 의해 강제로 닫혔습니다. "

#### <a name="resolution"></a>해결 방법

위의 오류가 발생 하면이 섹션의 지침에 따라 문제를 해결 합니다.

- 분석을 위해 Netmon 추적을 수집 합니다. 

    1. 필터를 설정 하 여 서버에서 클라이언트 쪽으로 다시 설정 하는 것을 볼 수 있습니다. 다음 예제 스크린샷에서 서버 쪽이 Data Factory 서버 임을 확인할 수 있습니다.

        ![데이터 팩터리 서버의 스크린샷.](media/self-hosted-integration-runtime-troubleshoot-guide/data-factory-server.png)

    1. 다시 설정 패키지를 가져오는 경우 TCP (전송 제어 프로토콜)를 따라 대화를 찾을 수 있습니다.

        ![TCP 대화의 스크린샷](media/self-hosted-integration-runtime-troubleshoot-guide/find-conversation.png)

    1. 필터를 제거 하 여 아래 클라이언트와 Data Factory 서버 간의 대화를 가져옵니다.

        ![대화 정보의 스크린샷](media/self-hosted-integration-runtime-troubleshoot-guide/get-conversation.png)

- 수집 된 Netmon 추적의 분석은 TTL (Time to Live) 합계가 64 임을 보여 줍니다. 다음 목록에서 추출 된 [IP TTL (Time To Live) 및 홉 제한 기본](https://packetpushers.net/ip-time-to-live-and-hop-limit-basics/) 문서에 설명 된 값에 따라 패키지를 재설정 하 고 연결을 끊는 Linux 시스템 임을 확인할 수 있습니다.

    기본 TTL 및 홉 제한 값은 다음과 같이 운영 체제 마다 다릅니다.
    - Linux 커널 2.4 (년경 2001): TCP, UDP (사용자 데이터 그램 프로토콜) 및 ICMP (Internet Control Message Protocol)에 대 한 255
    - Linux 커널 4.10 (2015): TCP, UDP 및 ICMP 용 64
    - Windows XP (2001): 128 (TCP, UDP 및 ICMP)
    - Windows 10 (2015): 128 (TCP, UDP 및 ICMP)
    - Windows Server 2008: TCP, UDP 및 ICMP 용 128
    - Windows Server 2019 (2018): TCP, UDP 및 ICMP 용 128
    - macOS (2001): TCP, UDP 및 ICMP 용 64

    ![TTL 값 61을 보여 주는 스크린샷](media/self-hosted-integration-runtime-troubleshoot-guide/ttl-61.png)
    
    앞의 예제에서 TTL은 64 대신 61으로 표시 됩니다. 네트워크 패키지가 대상에 도달 하면 라우터 또는 네트워크 장치와 같은 다양 한 홉을 통과 해야 하기 때문입니다. 라우터 또는 네트워크 장치 수는 최종 TTL을 공제 수 있습니다.
    
    이 경우 TTL (64)을 사용 하 여 Linux 시스템에서 재설정이 전송 될 수 있다는 것을 알 수 있습니다.

-  다시 설정 장치를 가져올 수 있는 위치를 확인 하려면 자체 호스팅 IR에서 네 번째 홉을 확인 합니다.
 
    *Linux System A의 네트워크 패키지 TTL 64-> B TTL 64-1 = 63-> C TTL 63-1 = 62-> TTL 62 빼기 1 = 61 자체 호스팅 IR*

- 이상적인 상황에서 TTL 홉 수는 128입니다. 즉, Windows 운영 체제에서 데이터 팩터리 인스턴스를 실행 하 고 있음을 의미 합니다. 다음 예제와 같이 *128 마이너스 107 = 21 홉* 을 의미 합니다 .이는 패키지에 대 한 21 개의 홉이 TCP 3 핸드셰이크 중에 data factory 인스턴스에서 자체 호스팅 IR로 전송 되었음을 의미 합니다.
 
    ![TTL 값 107을 보여 주는 스크린샷](media/self-hosted-integration-runtime-troubleshoot-guide/ttl-107.png)

    따라서 네트워크 팀과 협력 하 여 네 번째 홉이 자체 호스팅 IR에 있는지 확인 해야 합니다. Linux 시스템과 마찬가지로 방화벽 인 경우 로그를 확인 하 여 TCP 3 핸드셰이크 후 장치에서 패키지를 다시 설정 하는 이유를 확인 합니다. 
    
    조사할 위치를 모를 경우 문제가 있는 시간에 자체 호스팅 IR 및 방화벽에서 Netmon 추적을 가져오려고 시도 합니다. 이 방법은 패키지를 재설정 하 고 연결을 끊은 장치를 파악 하는 데 도움이 됩니다. 이 경우 네트워크 팀과 협력 하 여 앞으로 이동 해야 합니다.

### <a name="analyze-the-netmon-trace"></a>Netmon 추적 분석

> [!NOTE] 
> 다음 지침은 Netmon 추적에 적용 됩니다. Netmon 추적이 현재 지원 되지 않기 때문에이를 위해 Wireshark를 사용할 수 있습니다.

수집 된 Netmon 추적을 사용 하 여 **8.8.8.8 888** 를 텔넷 하려고 하면 다음 스크린샷에 추적이 표시 됩니다.

!["포트 888에서 호스트에 대 한 연결을 열 수 없습니다." 라는 오류 메시지를 보여 주는 스크린샷](media/self-hosted-integration-runtime-troubleshoot-guide/netmon-trace-1.png)

![Netmon 추적에 대 한 설명을 보여 주는 스크린샷](media/self-hosted-integration-runtime-troubleshoot-guide/netmon-trace-2.png)
 

위의 이미지는 포트 **888** 에서 **8.8.8.8** 서버 쪽에 TCP 연결을 만들 수 없다는 것을 보여 주기 때문에 두 개의 **sy재전송** 추가 패키지를 볼 수 있습니다. 원본 **자체 host2.contoso.com** 는 첫 번째 패키지를 사용 하 여 **8.8.8.8** 에 연결할 수 없으므로 연결을 계속 시도 합니다.

> [!TIP]
> 이 연결을 만들려면 다음 해결 방법을 시도해 보세요.
> 1. **부하 필터**  >  **표준 필터**  >  **주소**  >  **IPv4 주소** 를 선택 합니다.
> 1. 필터를 적용 하려면 **IPv4. Address = = 8.8.8.8** 를 입력 한 다음 **적용** 을 선택 합니다. 그런 다음 로컬 컴퓨터에서 대상 **8.8.8.8** 통신을 확인 해야 합니다.

![필터 주소를 보여 주는 스크린샷](media/self-hosted-integration-runtime-troubleshoot-guide/filter-addresses-1.png)
        
![더 많은 필터 주소를 보여 주는 스크린샷](media/self-hosted-integration-runtime-troubleshoot-guide/filter-addresses-2.png)

성공적인 시나리오는 다음 예제에 나와 있습니다. 

- 문제 없이 텔넷 **8.8.8.8 53** 을 사용할 수 있는 경우 tcp 3 핸드셰이크가 성공 하 고 세션이 tcp 4 핸드셰이크로 완료 됩니다.

    ![성공적인 연결 시나리오를 보여 주는 스크린샷](media/self-hosted-integration-runtime-troubleshoot-guide/good-scenario-1.png)
     
    ![성공적인 연결 시나리오의 세부 정보를 보여 주는 스크린샷](media/self-hosted-integration-runtime-troubleshoot-guide/good-scenario-2.png)

- 위의 TCP 3 핸드셰이크는 다음 워크플로를 생성 합니다.

    ![TCP 3 핸드셰이크 워크플로 다이어그램](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-3-handshake-workflow.png)
 
- 세션을 완료 하는 TCP 4 핸드셰이크는 다음 워크플로에 설명 되어 있습니다.

    ![TCP 4 핸드셰이크 세부 정보의 스크린샷](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-4-handshake.png)

    ![TCP 4 핸드셰이크 워크플로를 다이어그램으로 만듭니다.](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-4-handshake-workflow.png) 

### <a name="microsoft-email-notification-about-updating-your-network-configuration"></a>네트워크 구성 업데이트에 대 한 Microsoft 전자 메일 알림

다음 전자 메일 알림을 받을 수 있습니다 .이는 네트워크 구성을 업데이트 하 여 Azure Data Factory에 대 한 새 IP 주소와의 통신을 허용 하는 2020 11 월입니다.

   ![네트워크 구성 업데이트를 요청 하는 Microsoft 전자 메일 알림 스크린샷](media/self-hosted-integration-runtime-troubleshoot-guide/email-notification.png)

#### <a name="determine-whether-this-notification-affects-you"></a>이 알림이 사용자에 게 영향을 주는지 여부 결정

이 알림은 다음 시나리오에 적용 됩니다.

##### <a name="scenario-1-outbound-communication-from-a-self-hosted-integration-runtime-thats-running-on-premises-behind-a-corporate-firewall"></a>시나리오 1: 회사 방화벽 뒤에서 온-프레미스로 실행 되는 자체 호스팅 통합 런타임과의 아웃 바운드 통신

영향을 받는지 여부를 확인 하는 방법:

- [IP 주소에 대 한 방화벽 구성 설정 및 허용 목록](data-movement-security-considerations.md#firewall-configurations-and-allow-list-setting-up-for-ip-address-of-gateway)에 설명 된 방법을 사용 하는 fqdn (정규화 된 도메인 이름)을 기반으로 하는 방화벽 규칙을 정의 하는 경우에는 영향을 받지 *않습니다* .

- 회사 방화벽에서 아웃 바운드 Ip에 대해 허용 목록을 명시적으로 사용 하도록 설정 *하는 경우 영향을* 받습니다.

   영향을 받는 경우 다음 작업을 수행 합니다. 11 월 8 2020, 네트워크 인프라 팀에 최신 데이터 팩터리 IP 주소를 사용 하도록 네트워크 구성을 업데이트 하도록 알립니다. 최신 IP 주소를 다운로드 하려면 [다운로드 가능한 JSON 파일을 사용 하 여 서비스 태그 검색](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)으로 이동 합니다.

##### <a name="scenario-2-outbound-communication-from-a-self-hosted-integration-runtime-thats-running-on-an-azure-vm-inside-a-customer-managed-azure-virtual-network"></a>시나리오 2: 고객이 관리 하는 Azure virtual network 내에서 Azure VM에서 실행 되는 자체 호스팅 통합 런타임과의 아웃 바운드 통신

영향을 받는지 여부를 확인 하는 방법:

- 자체 호스팅 통합 런타임을 포함 하는 개인 네트워크에 아웃 바운드 NSG (네트워크 보안 그룹) 규칙이 있는지 확인 합니다. 아웃 바운드 제한이 없는 경우에는 영향을 받지 않습니다.

- 아웃 바운드 규칙 제한이 있는 경우 서비스 태그를 사용 하 고 있는지 확인 하십시오. 서비스 태그를 사용 하는 경우에는 영향을 받지 않습니다. 새 IP 범위는 기존 서비스 태그 아래에 있기 때문에 아무 것도 변경 하거나 추가할 필요가 없습니다. 

  ![DataFactory를 대상으로 표시 하는 대상 검사의 스크린샷](media/self-hosted-integration-runtime-troubleshoot-guide/destination-check.png)

- Azure 가상 네트워크의 NSG 규칙에서 아웃 바운드 IP 주소에 대 한 허용 목록을 명시적으로 사용 하도록 설정 *하는 경우 영향을* 받습니다.

   영향을 받는 경우 다음 작업을 수행 합니다. 11 월 8 2020, Azure 가상 네트워크 구성에 대 한 NSG 규칙을 업데이트 하 여 최신 데이터 팩터리 IP 주소를 사용 하도록 네트워크 인프라 팀에 알립니다. 최신 IP 주소를 다운로드 하려면 [다운로드 가능한 JSON 파일을 사용 하 여 서비스 태그 검색](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)으로 이동 합니다.

##### <a name="scenario-3-outbound-communication-from-ssis-integration-runtime-in-a-customer-managed-azure-virtual-network"></a>시나리오 3: 고객이 관리 하는 Azure virtual network에서 SSIS Integration Runtime의 아웃 바운드 통신

영향을 받는지 여부를 확인 하는 방법:

- SQL Server Integration Services (SSIS) Integration Runtime를 포함 하는 개인 네트워크에 아웃 바운드 NSG 규칙이 있는지 확인 합니다. 아웃 바운드 제한이 없는 경우에는 영향을 받지 않습니다.

- 아웃 바운드 규칙 제한이 있는 경우 서비스 태그를 사용 하 고 있는지 확인 하십시오. 서비스 태그를 사용 하는 경우에는 영향을 받지 않습니다. 새 IP 범위는 기존 서비스 태그 아래에 있기 때문에 아무 것도 변경 하거나 추가할 필요가 없습니다.

- Azure 가상 네트워크의 NSG 규칙에서 아웃 바운드 IP 주소에 대 한 허용 목록을 명시적으로 사용 하도록 설정 *하는 경우 영향을* 받습니다.

  영향을 받는 경우 다음 작업을 수행 합니다. 11 월 8 2020, Azure 가상 네트워크 구성에 대 한 NSG 규칙을 업데이트 하 여 최신 데이터 팩터리 IP 주소를 사용 하도록 네트워크 인프라 팀에 알립니다. 최신 IP 주소를 다운로드 하려면 [다운로드 가능한 JSON 파일을 사용 하 여 서비스 태그 검색](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)으로 이동 합니다.

### <a name="couldnt-establish-a-trust-relationship-for-the-ssltls-secure-channel"></a>SSL/TLS 보안 채널에 대 한 트러스트 관계를 설정할 수 없습니다. 

#### <a name="symptoms"></a>증상

자체 호스팅 IR을 Azure Data Factory 서비스에 연결할 수 없습니다.

CustomLogEvent 테이블에서 자체 호스팅 IR 이벤트 로그 또는 클라이언트 알림 로그를 확인 하면 다음과 같은 오류 메시지가 나타납니다.

"기본 연결이 닫혔습니다. SSL/TLS 보안 채널에 대 한 트러스트 관계를 설정할 수 없습니다. 유효성 검사 절차에 따르면 원격 인증서가 잘못되었습니다."

Data Factory 서비스의 서버 인증서를 확인 하는 가장 간단한 방법은 브라우저에서 Data Factory 서비스 URL을 여는 것입니다. 예를 들어 자체 호스팅 IR이 설치 된 컴퓨터에서 [서버 인증서 확인 링크](https://eu.frontend.clouddatahub.net/) 를 열고 서버 인증서 정보를 확인 합니다.

  ![Azure Data Factory 서비스의 서버 인증서 확인 창의 스크린샷](media/self-hosted-integration-runtime-troubleshoot-guide/server-certificate.png)

  ![서버 인증 경로를 확인 하는 창의 스크린샷](media/self-hosted-integration-runtime-troubleshoot-guide/certificate-path.png)

#### <a name="cause"></a>원인

이 문제에 대 한 두 가지 가능한 이유는 다음과 같습니다.

- 이유 1: Data Factory 서비스 서버 인증서의 루트 CA가 자체 호스팅 IR이 설치 된 컴퓨터에서 트러스트 되지 않았습니다. 
- 이유 2: 사용자 환경에서 프록시를 사용 하 고 Data Factory 서비스의 서버 인증서가 프록시로 바뀌고 대체 된 서버 인증서가 자체 호스팅 IR이 설치 된 컴퓨터에서 트러스트 되지 않습니다.

#### <a name="resolution"></a>해결 방법

- 이유 1: 자체 호스팅 IR이 설치 된 컴퓨터에서 Data Factory 서버 인증서와 인증서 체인을 신뢰할 수 있는지 확인 합니다.
- 이유 2: 자체 호스팅 IR 컴퓨터에서 대체 된 루트 CA를 신뢰 하거나 Data Factory 서버 인증서를 대체 하지 않도록 프록시를 구성 합니다.

Windows에서 인증서를 신뢰 하는 방법에 대 한 자세한 내용은 [신뢰할 수 있는 루트 인증서 설치](/skype-sdk/sdn/articles/installing-the-trusted-root-certificate)를 참조 하세요.

#### <a name="additional-information"></a>추가 정보
DigiCert에서 서명 된 새 SSL 인증서를 출시 했습니다. DigiCert Global Root G2가 신뢰할 수 있는 루트 CA에 있는지 확인 하십시오.

  ![신뢰할 수 있는 루트 인증 기관 디렉터리에 있는 DigiCert Global Root G2 폴더를 보여 주는 스크린샷](media/self-hosted-integration-runtime-troubleshoot-guide/trusted-root-ca-check.png)

신뢰할 수 있는 루트 CA에 없으면 여기에서 [다운로드](http://cacerts.digicert.com/DigiCertGlobalRootG2.crt )합니다. 


## <a name="self-hosted-ir-sharing"></a>자체 호스팅 IR 공유

### <a name="sharing-a-self-hosted-ir-from-a-different-tenant-is-not-supported"></a>다른 테 넌 트에서 자체 호스팅 IR 공유는 지원 되지 않습니다. 

#### <a name="symptoms"></a>증상

Azure Data Factory UI에서 자체 호스팅 IR을 공유 하려고 하지만 다른 테 넌 트에 있는 다른 데이터 팩터리를 확인할 수 있지만 다른 테 넌 트에 있는 데이터 팩터리에서 공유할 수는 없습니다.

#### <a name="cause"></a>원인

자체 호스팅 IR은 테 넌 트 간에 공유 될 수 없습니다.

## <a name="next-steps"></a>다음 단계

문제 해결에 대 한 자세한 내용은 다음 리소스를 참조 하세요.

*  [Data Factory 블로그](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory 기능 요청](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure 비디오](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&페이지](/answers/topics/azure-data-factory.html)
*  [Data Factory에 대 한 스택 오버플로 포럼](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Data Factory에 대한 Twitter 정보](https://twitter.com/hashtag/DataFactory)
*  [데이터 흐름 매핑 성능 가이드](concepts-data-flow-performance.md)