---
title: Azure AD Connect 클라우드 프로 비전 문제 해결
description: 이 문서에서는 클라우드 프로 비전 에이전트에서 발생할 수 있는 문제를 해결 하는 방법을 설명 합니다.
author: billmath
ms.author: billmath
manager: daveba
ms.date: 12/02/2019
ms.topic: article
ms.prod: windows-server-threshold
ms.technology: identity-adfs
ms.openlocfilehash: 4100886e0a24fa961b9085bd507ae3f4ebfdd6eb
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74794238"
---
# <a name="cloud-provisioning-troubleshooting"></a>클라우드 프로 비전 문제 해결

클라우드 프로 비전은 다양 한 작업을 수행 하 고 다양 한 종속성을 포함 합니다.  이렇게 하면 다양 한 문제를 쉽게 파악할 수 있습니다.  이 문서는 이러한 문제 해결을 시작할 수 있도록 설계 되었습니다.  이 문서에서는 초점을 맞춘 일반적인 영역, 추가 정보를 수집 하는 방법 및 문제를 추적 하는 데 사용할 수 있는 여러 가지 방법을 소개 합니다.  


## <a name="common-troubleshooting-areas"></a>일반적인 문제 해결 영역

|name|설명|
|-----|-----|
|[에이전트 문제](#agent-issues)|에이전트가 올바르게 설치 되었으며 Azure AD와 통신 하 고 있는지 확인 합니다.|
|[개체 동기화 문제](#object-synchronization-issues)|프로 비전 로그를 사용 하 여 개체 동기화 문제를 해결 합니다.|
|[격리 문제 프로 비전](#provisioning-quarantined-issues)|프로 비전 격리 문제 및 해결 방법 이해|


## <a name="agent-issues"></a>에이전트 문제
에이전트를 사용 하 여 확인 하려는 첫 번째 작업은 다음과 같습니다.


-  설치 되어 있나요?
-  에이전트가 로컬로 실행 되 고 있습니까?
-  에이전트가 포털에 있나요?
-  에이전트가 정상으로 표시 되어 있나요?  

이러한 항목은 Azure Portal 및 에이전트를 실행 하는 로컬 서버에서 확인할 수 있습니다.

### <a name="azure-portal-agent-verification"></a>Azure Portal 에이전트 확인

에이전트가 Azure에서 표시 되 고 있고 정상 상태 인지 확인 하려면 다음 단계를 수행 합니다.

1. Azure 포털에 로그인합니다.
2. 왼쪽에서 **Azure Active Directory**를 선택 하 고 **Azure AD Connect** 를 클릭 한 다음 센터에서 **프로 비전 관리 (미리 보기)** 를 선택 합니다.
3.  **AZURE AD 프로 비전 (미리 보기)** 화면에서 **모든 에이전트 검토**를 클릭 합니다.
 Azure AD 프로 비전](media/how-to-install/install7.png) ![</br>
 
4. **온-프레미스 프로 비전 에이전트 화면** 에서 설치한 에이전트가 표시 됩니다.  해당 에이전트가 있고 **정상**으로 표시 되어 있는지 확인 합니다.
 ![프로 비전 에이전트](media/how-to-install/install8.png)</br>

### <a name="verify-the-port"></a>포트 확인

Azure가 포트 443에서 수신 대기 하 고 에이전트와 통신할 수 있는지 확인 하려면 다음 도구를 사용할 수 있습니다.

https://aadap-portcheck.connectorporttest.msappproxy.net/ 

이 테스트는 에이전트가 포트 443을 통해 Azure와 통신할 수 있는지 확인 합니다.  브라우저를 열고 에이전트가 설치 된 서버에서 위의 URL로 이동 합니다.
 ![서비스](media/how-to-install/verify2.png)

### <a name="on-the-local-server"></a>로컬 서버에서

에이전트가 실행 되 고 있는지 확인 하려면 다음 단계를 수행 합니다.

1.  에이전트가 설치 된 서버에서 또는 시작/실행/services.msc로 이동 하 여 **서비스** 를 엽니다.
2.  **서비스**에서 **Microsoft Azure AD connect 에이전트 업데이트** 프로그램 및 **Microsoft Azure AD 연결 프로 비전 에이전트가** 있고 상태가 **실행**중인지 확인 합니다.
 ![서비스](media/how-to-troubleshoot/troubleshoot1.png)

### <a name="common-agent-installation-issues"></a>일반적인 에이전트 설치 문제

다음은 몇 가지 일반적인 에이전트 설치 문제 및 일반적인 해결 방법입니다.

#### <a name="agent-failed-to-start"></a>에이전트를 시작 하지 못했습니다.

다음 오류 메시지가 표시 되는 경우:

**서비스 ' 마이크로 Soft Azure AD Connect 프로 비전 에이전트 '를 시작 하지 못했습니다.  시스템 서비스를 시작할 수 있는 충분 한 권한이 있는지 확인 하십시오.** 

이 문제는 일반적으로 설치 관리자가 만든 로컬 NT 서비스 "로그온 계정"에 사용 권한을 적용 하지 못하도록 하는 그룹 정책에 의해 발생 합니다. (NT SERVICE\AADConnectProvisioningAgent) 이러한 사용 권한은 서비스를 시작 하는 데 필요 합니다.

이 문제를 해결 하려면 다음 단계를 사용 합니다.

1.  관리자 계정으로 서버에 로그온 합니다.
2.  서비스를 탐색 하거나 Start/Run/services.msc로 이동 하 여 **서비스** 를 엽니다.
3.  **서비스** 에서 **Microsoft Azure AD 프로 비전 에이전트 연결** 을 두 번 클릭 합니다.
4. 탭에서 "로그온 계정"을 도메인 관리자로 변경 하 고 서비스를 다시 시작 합니다. 

 ![Services](media/how-to-troubleshoot/troubleshoot3.png)

#### <a name="agent-times-out-or-certificate-is-invalid"></a>에이전트 시간 제한 또는 인증서가 잘못 되었습니다.

에이전트를 등록 하려고 하면 다음과 같은 오류가 발생할 수 있습니다.

 ![Services](media/how-to-troubleshoot/troubleshoot4.png)

이 문제는 일반적으로 에이전트가 하이브리드 Id 서비스에 연결할 수 없고 HTTP 프록시를 구성 해야 하는 경우에 발생 합니다.  이 문제를 해결 하려면 아웃 바운드 프록시를 구성 합니다. 

프로비전 에이전트는 아웃바운드 프록시 사용을 지원합니다. Files\Microsoft 에이전트 구성 파일 **C:\Program Azure AD Connect 프로 비전 Agent\AADConnectProvisioningAgent.exe.config**을 편집 하 여 구성할 수 있습니다. 닫는 `</configuration>` 태그 바로 앞에 있는 파일의 끝에 다음 줄을 추가 합니다.
[proxy-server] 및 [proxy-port] 변수를 프록시 서버 이름 및 포트 값으로 바꿉니다.

```xml
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy
                usesystemdefault="true"
                proxyaddress="http://[proxy-server]:[proxy-port]"
                bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```

#### <a name="agent-registration-fails-with-security-error"></a>보안 오류로 인해 에이전트를 등록 하지 못했습니다.

클라우드 프로 비전 에이전트를 설치할 때 다음과 같은 오류가 표시 될 수 있습니다.

일반적으로 에이전트는 로컬 PowerShell 실행 정책으로 인해 PowerShell 등록 스크립트를 실행할 수 없기 때문에 발생 합니다.

이 문제를 해결 하려면 서버에서 PowerShell 실행 정책을 변경 합니다. 컴퓨터 및 사용자 정책이 "Undefined" 또는 "RemoteSigned"로 표시 되어야 합니다. "제한 없음" 이면이 오류가 표시 됩니다.  자세한 내용은 [PowerShell 실행 정책](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6)을 참조 하세요. 

### <a name="log-files"></a>로그 파일

기본적으로 에이전트는 매우 적은 오류 메시지 및 스택 추적 정보를 내보냅니다. 다음 폴더에서 이러한 추적 로그를 찾을 수 있습니다. **C:\PROGRAMDATA\MICROSOFT\AZURE AD Connect 프로 비전 Agent\Trace**

에이전트 관련 문제를 해결 하기 위한 추가 정보를 수집 하려면 다음 단계를 사용 합니다.

1. **프로 비전 에이전트를 연결 Microsoft Azure AD** 서비스를 중지 합니다.
2. 원래 구성 파일의 복사본을 만듭니다. **C:\Program Files\Microsoft Azure AD Connect 프로 비전 Agent\AADConnectProvisioningAgent.exe.config**
3. 기존 < diagnostics > 섹션을 다음으로 바꿉니다. 그러면 모든 추적 메시지가 ProvAgentTrace 파일로 이동 합니다 **.**

      ```xml
        <system.diagnostics>
            <sources>
            <source name="AAD Connect Provisioning Agent">
                <listeners>
                <add name="console"/>
                <add name="etw"/>
                <add name="textWriterListener"/>
                </listeners>
            </source>
            </sources>
            <sharedListeners>
            <add name="console" type="System.Diagnostics.ConsoleTraceListener" initializeData="false"/>
            <add name="etw" type="System.Diagnostics.EventLogTraceListener" initializeData="Azure AD Connect Provisioning Agent">
                <filter type="System.Diagnostics.EventTypeFilter" initializeData="All"/>
            </add>
            <add name="textWriterListener" type="System.Diagnostics.TextWriterTraceListener" initializeData="C:/ProgramData/Microsoft/Azure AD Connect Provisioning Agent/Trace/ProvAgentTrace.log"/>
            </sharedListeners>
        </system.diagnostics>

      ```
4. **프로 비전 에이전트를 연결 Microsoft Azure AD** 서비스를 시작 합니다.
5. 다음 명령을 사용 하 여 파일을 마무리 하 고 문제를 디버깅할 수 있습니다. 
    ```
    Get-Content “C:/ProgramData/Microsoft/Azure AD Connect Provisioning Agent/Trace/ProvAgentTrace.log” -Wait
    ```
## <a name="object-synchronization-issues"></a>개체 동기화 문제

다음 섹션에는 개체 동기화 문제 해결에 대 한 정보가 포함 되어 있습니다.

### <a name="provisioning-logs"></a>프로비저닝 로그

Azure Portal에서 프로 비전 로그를 사용 하 여 개체 동기화 문제를 추적 하 고 해결할 수 있습니다.  로그를 보려면 **로그**를 선택 합니다.
 ![프로 비전 로그](media/how-to-troubleshoot/log1.png)

프로 비전 로그는 온-프레미스 AD 환경과 Azure 간에 동기화 되는 개체의 상태에 대 한 다양 한 정보를 제공 합니다.

 ![프로비저닝 로그](media/how-to-troubleshoot/log2.png)

페이지 맨 위에 있는 드롭다운을 사용 하 여 특정 문제, 날짜 등에서 뷰를 0으로 필터링 할 수 있습니다.  개별 이벤트를 두 번 클릭 하면 추가 세부 정보가 제공 됩니다.

 ![프로비저닝 로그](media/how-to-troubleshoot/log3.png)

이 정보는 자세한 단계와 동기화 문제가 발생 한 위치를 제공 합니다.  따라서를 0으로 만들어 문제의 정확한 지점을 정확 하 게 파악할 수 있습니다.


## <a name="provisioning-quarantined-issues"></a>격리 된 문제 프로 비전

클라우드 프로 비전은 구성 상태를 모니터링 하 고 비정상 개체를 "격리" 상태에 배치 합니다. 오류 (예: 잘못 된 관리자 자격 증명)로 인해 대상 시스템에 대해 수행 된 대부분의 호출이 일관 되 게 실패 하는 경우 프로 비전 작업은 격리 상태로 표시 됩니다.

 ![격리](media/how-to-troubleshoot/quarantine1.png)

상태를 클릭 하 여 격리에 대 한 추가 정보를 볼 수 있습니다.  오류 코드 및 메시지를 가져올 수도 있습니다.

 ![격리](media/how-to-troubleshoot/quarantine2.png)

### <a name="resolving-a-quarantine"></a>격리 해결

- Azure Portal를 사용 하 여 프로 비전 작업을 다시 시작 합니다. 에이전트 구성 페이지에서 **프로 비전 다시 시작**을 선택 합니다.

  ![격리](media/how-to-troubleshoot/quarantine3.png)

- Microsoft Graph를 사용 하 여 [프로 비전 작업을 다시 시작](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http)합니다. 다시 시작 하는 작업을 완전히 제어할 수 있습니다. Escrows (격리 상태를 적용 하는 에스크로 카운터를 다시 시작 하려면)를 선택 취소 하거나 격리 (격리에서 응용 프로그램을 제거 하려면)를 지우거 나 워터 마크를 지울 수 있습니다. 다음 요청을 사용합니다.
 
  `POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart`

## <a name="next-steps"></a>다음 단계 

- [프로 비전 이란?](what-is-provisioning.md)
- [클라우드 프로 비전 Azure AD Connect 이란?](what-is-cloud-provisioning.md)



