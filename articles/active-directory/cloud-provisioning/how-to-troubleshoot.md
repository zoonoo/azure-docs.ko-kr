---
title: Azure AD Connect 클라우드 프로비저닝 문제 해결
description: 이 문서에서는 클라우드 프로비전 에이전트에서 발생할 수 있는 문제를 해결하는 방법을 설명합니다.
author: billmath
ms.author: billmath
manager: daveba
ms.date: 12/02/2019
ms.topic: article
ms.prod: windows-server-threshold
ms.technology: identity-adfs
ms.openlocfilehash: e41be4b76245f2567015eb0ede317830120ee61a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75549488"
---
# <a name="cloud-provisioning-troubleshooting"></a>클라우드 프로비저닝 문제 해결

클라우드 프로비저닝은 여러 가지 를 다루며 다양한 종속성을 가지고 있습니다. 이 광범위한 범위는 다양한 문제를 야기할 수 있습니다. 이 문서에서는 이러한 문제를 해결하는 데 도움이 됩니다. 중점을 두는 일반적인 영역, 추가 정보를 수집하는 방법 및 문제를 추적하는 데 사용할 수 있는 다양한 기술을 소개합니다.


## <a name="common-troubleshooting-areas"></a>일반적인 문제 해결 영역

|이름|설명|
|-----|-----|
|[에이전트 문제](#agent-problems)|에이전트가 올바르게 설치되었는지, Azure Active Directory(Azure AD)와 통신하고 있는지 확인합니다.|
|[개체 동기화 문제](#object-synchronization-problems)|프로비저닝 로그를 사용하여 개체 동기화 문제를 해결합니다.|
|[격리된 문제 프로비저닝](#provisioning-quarantined-problems)|프로비저닝 격리 문제 및 이를 해결하는 방법을 이해합니다.|


## <a name="agent-problems"></a>에이전트 문제
에이전트와 함께 확인하려는 첫 번째 사항은 다음과 같습니다.

-  설치되어 있습니까?
-  에이전트가 로컬로 실행되고 있습니까?
-  포털의 에이전트가 있습니까?
-  에이전트가 정상으로 표시되어 있습니까?

이러한 항목은 Azure 포털 및 에이전트를 실행하는 로컬 서버에서 확인할 수 있습니다.

### <a name="azure-portal-agent-verification"></a>Azure Portal에서 에이전트 확인

Azure에서 에이전트가 보이고 정상인지 확인하려면 다음 단계를 따르십시오.

1. Azure Portal에 로그인합니다.
1. 왼쪽에서 Azure **Active Directory** > **Azure AD 연결**을 선택합니다. 가운데에서 **프로비저닝 관리(미리 보기)를 선택합니다.**
1. Azure **AD 프로비저닝(미리 보기)** 화면에서 **모든 에이전트 검토를**선택합니다.

   ![모든 에이전트 검토](media/how-to-install/install7.png)</br>
 
1. **온-프레미스 프로비저닝 에이전트** 화면에 설치한 에이전트가 표시됩니다. 해당 에이전트가 있는지 확인하고 *정상*으로 표시되어 있습니다.

   ![온-프레미스 프로비저닝 에이전트 화면](media/how-to-install/install8.png)</br>

### <a name="verify-the-port"></a>포트 확인

Azure가 포트 443에서 수신 대기 중이고 에이전트가 포트 와 통신할 수 있는지 확인하려면 다음 도구를 사용합니다.

https://aadap-portcheck.connectorporttest.msappproxy.net/ 

이 테스트는 에이전트가 포트 443을 통해 Azure와 통신할 수 있음을 확인합니다. 브라우저를 열고 에이전트가 설치된 서버에서 이전 URL로 이동합니다.

![포트 접근성 검증](media/how-to-install/verify2.png)

### <a name="on-the-local-server"></a>로컬 서버에서 에이전트 확인

에이전트가 실행 되고 있는지 확인하려면 다음 단계를 따르십시오.

1. 에이전트가 설치된 서버에서 **에이전트로** 이동하거나**실행** > 서비스 **로** > 이동하여 서비스를**엽니다.**
1. **서비스에서는**Microsoft **Azure AD 연결 에이전트 업데이트** 및 **Microsoft Azure AD 연결 프로비저닝 에이전트가** 있고 해당 상태가 *실행 중인지*확인합니다.

   ![서비스 화면](media/how-to-troubleshoot/troubleshoot1.png)

### <a name="common-agent-installation-problems"></a>일반적인 에이전트 설치 문제

다음 섹션에서는 몇 가지 일반적인 에이전트 설치 문제 및 일반적인 해결 방법설명입니다.

#### <a name="agent-failed-to-start"></a>에이전트를 시작하지 못했습니다.

다음과 같은 오류 메시지가 들릴 수 있습니다.

**서비스 'Microsoft Azure AD 연결 프로비저닝 에이전트'가 시작되지 못했습니다. 시스템 서비스를 시작하기에 충분한 권한이 있는지 확인합니다.** 

이 문제는 일반적으로 설치 관리자(NT SERVICE\AADConnectProvisioningAgent)에서 만든 로컬 NT 서비스 로그온 계정에 사용 권한이 적용되지 않도록 하는 그룹 정책으로 인해 발생합니다. 이러한 권한은 서비스를 시작하려면 필수입니다.

이 문제를 해결하려면 다음 단계를 따르십시오.

1. 관리자 계정으로 서버에 로그인합니다.
1. 로 이동하거나**실행** >  **서비스** **로** > 이동하여 서비스를**엽니다.**
1. **서비스에서**Microsoft Azure **AD 연결 프로비저닝 에이전트를**두 번 클릭합니다.
1. **로그온** 탭에서 **이 계정을** 도메인 관리자로 변경합니다. 그런 다음 서비스를 다시 시작합니다. 

   ![로그 온 탭](media/how-to-troubleshoot/troubleshoot3.png)

#### <a name="agent-times-out-or-certificate-is-invalid"></a>에이전트 시간 단축 또는 인증서가 잘못되었습니다.

에이전트를 등록하려고 할 때 다음과 같은 오류 메시지가 발생할 수 있습니다.

![시간 시간 오류 메시지](media/how-to-troubleshoot/troubleshoot4.png)

이 문제는 일반적으로 에이전트가 하이브리드 ID 서비스에 연결할 수 없고 HTTP 프록시를 구성해야 하기 때문에 발생합니다. 이 문제를 해결하려면 아웃바운드 프록시를 구성합니다. 

프로비저닝 에이전트는 아웃바운드 프록시 사용을 지원합니다. 에이전트 구성 파일 *C:\\프로그램 파일\Microsoft Azure AD 연결 프로비저닝 에이전트\AADConnectProvisioningAgent.exe.config를*편집하여 구성할 수 있습니다. 닫는 `</configuration>` 태그 바로 앞의 파일 끝을 향해 다음 줄을 추가합니다.
변수를 프록시 `[proxy-server]` `[proxy-port]` 서버 이름 및 포트 값으로 바꿉니다.

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

#### <a name="agent-registration-fails-with-security-error"></a>보안 오류로 에이전트 등록에 실패

클라우드 프로비저닝 에이전트를 설치할 때 오류 메시지가 발생할 수 있습니다.

이 문제는 일반적으로 에이전트가 로컬 PowerShell 실행 정책으로 인해 PowerShell 등록 스크립트를 실행할 수 없기 때문에 발생합니다.

이 문제를 해결하려면 서버의 PowerShell 실행 정책을 변경합니다. 컴퓨터 및 사용자 정책을 *정의되지 않은* 또는 *원격 서명으로*설정해야 합니다. *무제한으로*설정된 경우 이 오류가 표시됩니다. 자세한 내용은 [PowerShell 실행 정책을](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6)참조하십시오. 

### <a name="log-files"></a>로그 파일

기본적으로 에이전트는 최소한의 오류 메시지와 스택 추적 정보를 내보전합니다. *C:\ProgramData\Microsoft\Azure AD 연결 프로비저닝 에이전트\Trace에서*이러한 추적 로그를 찾을 수 있습니다.

에이전트 관련 문제 해결에 대한 추가 세부 정보를 수집하려면 다음 단계를 따르십시오.

1. **서비스 중지 마이크로 소프트 Azure AD 연결 프로비저닝 에이전트**.
1. 원래 구성 파일의 복사본 만들기: *C:\프로그램 파일\Microsoft Azure AD 연결 프로비저닝 에이전트\AADConnect프로비저닝에이전트.exe.config*.
1. 기존 `<system.diagnostics>` 섹션을 다음으로 바꾸면 모든 추적 메시지가 *ProvAgentTrace.log*파일로 이동합니다.

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
1. **서비스 Microsoft Azure AD 연결 프로비저닝 에이전트를**시작합니다.
1. 다음 명령을 사용하여 파일을 꼬리에 대고 문제를 디버깅합니다. 
    ```
    Get-Content “C:/ProgramData/Microsoft/Azure AD Connect Provisioning Agent/Trace/ProvAgentTrace.log” -Wait
    ```
## <a name="object-synchronization-problems"></a>개체 동기화 문제

다음 섹션에는 개체 동기화 문제 해결에 대한 정보가 포함되어 있습니다.

### <a name="provisioning-logs"></a>프로비저닝 로그

Azure 포털에서 프로비저닝 로그를 사용하여 개체 동기화 문제를 추적하고 문제를 해결할 수 있습니다. 로그를 보려면 **로그를 선택합니다.**

![로그 단추](media/how-to-troubleshoot/log1.png)

프로비저닝 로그는 온-프레미스 Active Directory 환경과 Azure 간에 동기화되는 개체의 상태에 대한 풍부한 정보를 제공합니다.

![로그 프로비저닝 화면](media/how-to-troubleshoot/log2.png)

페이지 상단의 드롭다운 상자를 사용하여 날짜와 같은 특정 문제에 대해 뷰를 0으로 필터링할 수 있습니다. 개별 이벤트를 두 번 클릭하여 추가 정보를 확인합니다.

![로그 드롭다운 상자 정보 프로비저닝](media/how-to-troubleshoot/log3.png)

이 정보는 자세한 단계와 동기화 문제가 발생하는 위치를 제공합니다. 이러한 방식으로 문제의 정확한 지점을 정확히 파악할 수 있습니다.


## <a name="provisioning-quarantined-problems"></a>격리된 문제 프로비저닝

클라우드 프로비저닝은 구성상태를 모니터링하고 비정상 개체를 격리 상태에 배치합니다. 예를 들어 잘못된 관리자 자격 증명과 같은 오류로 인해 대상 시스템에 대해 수행된 대부분의 또는 모든 호출이 일관되게 실패하면 프로비저닝 작업이 격리 된 것으로 표시됩니다.

![격리 상태](media/how-to-troubleshoot/quarantine1.png)

상태를 선택하면 격리에 대한 추가 정보를 볼 수 있습니다. 오류 코드와 메시지를 가져올 수도 있습니다.

![격리 상태 정보](media/how-to-troubleshoot/quarantine2.png)

### <a name="resolve-a-quarantine"></a>격리 해결

- Azure 포털을 사용하여 프로비저닝 작업을 다시 시작합니다. 에이전트 구성 페이지에서 **프로비저닝 다시 시작을 선택합니다.**

  ![프로비저닝 다시 시작](media/how-to-troubleshoot/quarantine3.png)

- Microsoft 그래프를 사용하여 [프로비저닝 작업을 다시 시작합니다.](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http) 다시 시작하는 내용을 완전히 제어할 수 있습니다. 다음을 선택취소할 수 있습니다.
  - 에스크로, 격리 상태로 발생하는 에스크로 카운터를 다시 시작합니다.
  - 격리, 격리에서 응용 프로그램을 제거합니다.
  - 워터 마크. 
  
  다음 요청을 사용합니다.
 
  `POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart`

## <a name="next-steps"></a>다음 단계 

- [프로비저닝이란?](what-is-provisioning.md)
- [Azure AD Connect 클라우드 프로비저닝이란?](what-is-cloud-provisioning.md)



