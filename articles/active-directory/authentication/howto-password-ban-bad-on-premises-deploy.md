---
title: Azure AD 암호 보호 미리 보기 배포
description: Azure AD 암호 보호 미리 보기를 배포하여 온-프레미스에서 잘못된 암호를 사용할 수 없도록 금지합니다.
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 10/30/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: jsimmons
ms.openlocfilehash: 02c2b7560a0a609f6d902af78877d5f0236615d3
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2018
ms.locfileid: "51011496"
---
# <a name="preview-deploy-azure-ad-password-protection"></a>미리 보기: Azure AD 암호 보호 배포

|     |
| --- |
| Azure AD 암호 보호는 Azure Active Directory의 공개 미리 보기 기능입니다. 미리 보기에 대한 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.|
|     |

이제 [Windows Server Active Directory에 Azure AD 암호 보호를 적용하는 방법](concept-password-ban-bad-on-premises.md)에 대해 알아보았으므로 다음 단계는 배포를 계획하고 실행하는 것입니다.

## <a name="deployment-strategy"></a>배포 전략

모든 배포는 감사 모드에서 시작하는 것이 좋습니다. 감사 모드는 암호를 계속 설정할 수 있고 차단되는 암호가 이벤트 로그에 항목을 만드는 기본 초기 설정입니다. 일단 프록시 서버 및 DC 에이전트가 감사 모드에서 완전히 배포되면 정책이 적용되는 경우 암호 정책 적용이 사용자 및 환경에 미치는 영향을 확인하기 위해 모니터링을 정기적으로 수행해야 합니다.

많은 조직에서는 감사 단계에서 다음을 확인합니다.

* 더 안전한 암호를 사용하도록 기존 운영 프로세스를 개선해야 합니다.
* 사용자는 안전하지 않은 암호를 정기적으로 선택하는 데 익숙합니다.
* 보안 적용의 향후 변경, 이로 인해 사용자에게 미치는 영향에 대해 사용자에게 알리고, 더 안전한 암호를 선택하는 방법을 더 잘 이해할 수 있도록 해야 합니다.

기능이 적절한 시간 동안 감사 모드에서 실행되면 적용 구성을 **감사**에서 **적용**으로 전환하여 더 안전한 암호를 요구할 수 있습니다. 이 시간 동안 집중적으로 모니터링하는 것이 좋습니다.

## <a name="deployment-requirements"></a>배포 요구 사항

* Azure AD 암호 보호 DC 에이전트 서비스가 설치될 모든 도메인 컨트롤러에서는 Windows Server 2012 이상을 실행해야 합니다.

   > [!NOTE]
   > 서버 코어 기반 운영 체제는 현재 지원되지 않습니다. 조지아에서 지원될 예정입니다.

* Azure AD 암호 보호 프록시 서비스가 설치될 모든 머신에서는 Windows Server 2012 R2 이상을 실행해야 합니다.

   > [!NOTE]
   > 서버 코어 기반 운영 체제는 현재 지원되지 않습니다. 조지아에서 지원될 예정입니다.

* 도메인 컨트롤러를 포함하여 Azure AD 암호 보호 구성 요소가 설치된 모든 머신에는 Universal C 런타임이 설치되어야 합니다.
가급적 Windows Update를 통해 머신에 완벽하게 패치를 적용하여 완수합니다. 그렇지 않은 경우 적절한 OS 특정 업데이트 패키지가 설치될 수 있습니다 - [Windows의 Universal C 런타임 업데이트](https://support.microsoft.com/help/2999226/update-for-universal-c-runtime-in-windows) 참조
* 각 도메인의 하나 이상의 도메인 컨트롤러와 Azure AD 암호 보호 프록시 서비스를 호스팅하는 하나 이상의 서버간에 네트워크 연결이 존재해야 합니다. 이 연결을 통해 도메인 컨트롤러가 프록시 서비스의 RPC 엔드포인트 매퍼 포트(135) 및 RPC 서버 포트에 액세스할 수 있어야 합니다.  RPC 서버 포트는 기본적으로 동적 RPC 포트이지만 정적 포트를 사용하도록 구성될 수 있습니다(아래 참조).
* Azure AD 암호 보호 프록시 서비스를 호스팅하는 모든 머신은 다음 엔드포인트에 대한 네트워크 액세스 권한이 있어야 합니다.

    |엔드포인트 |목적|
    | --- | --- |
    |`https://login.microsoftonline.com`|인증 요청|
    |`https://enterpriseregistration.windows.net`|Azure AD 암호 보호 기능|

* Azure AD 암호 보호 프록시 서비스 및 포리스트를 Azure AD에 등록하기 위한 글로벌 관리자 계정
* Windows Server Active Directory 포리스트를 Azure AD에 등록하기 위한 포리스트 루트 도메인의 Active Directory 도메인 관리자 권한이 있는 계정
* DC 에이전트 서비스 소프트웨어를 실행하는 모든 Active Directory 도메인은 sysvol 복제에 DFSR을 사용해야 합니다.

## <a name="single-forest-deployment"></a>단일 포리스트 배포

Azure AD 암호 보호 미리 보기는 프록시 서비스를 통해 최대 두 개의 서버에 배포되며, DC 에이전트 서비스는 Active Directory 포리스트의 모든 도메인 컨트롤러에 증분 방식으로 배포할 수 있습니다.

![Azure AD 암호 보호 구성 요소가 함께 작동하는 방법](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

### <a name="download-the-software"></a>소프트웨어 다운로드

Azure AD 암호 보호에 필요한 두 가지 설치 관리자가 있으며, [Microsoft 다운로드 센터](https://www.microsoft.com/download/details.aspx?id=57071)에서 다운로드할 수 있습니다.

### <a name="install-and-configure-the-azure-ad-password-protection-proxy-service"></a>Azure AD 암호 보호 프록시 서비스 설치 및 구성

1. Azure AD 암호 보호 프록시 서비스를 호스팅할 서버를 하나 이상 선택합니다.
   * 이러한 각 서비스는 단일 포리스트에 대한 암호 정책만 제공할 수 있으며, 호스트 컴퓨터는 해당 포리스트의 도메인(루트 및 자식 모두 동일하게 지원됨)에 조인되어야 합니다. Azure AD 암호 보호 프록시 서비스에서 임무를 수행하려면 포리스트의 각 도메인에 있는 하나 이상의 DC와 Azure AD 암호 보호 프록시 호스트 컴퓨터 간에 네트워크 연결이 있어야 합니다.
   * 테스트를 위해 Azure AD 암호 보호 프록시 서비스를 도메인 컨트롤러에 설치하고 실행할 수 있지만 도메인 컨트롤러에는 인터넷 연결이 필요합니다.

   > [!NOTE]
   > 공개 미리 보기에서는 포리스트당 최대 2개의 프록시 서버를 지원합니다.

2. AzureADPasswordProtectionProxy.msi MSI 패키지를 사용하여 암호 정책 프록시 서비스 소프트웨어를 설치합니다.
   * 소프트웨어가 설치되면 다시 부팅할 필요가 없습니다. 소프트웨어 설치는 표준 MSI 절차(예: `msiexec.exe /i AzureADPasswordProtectionProxy.msi /quiet /qn`)를 사용하여 자동화할 수 있습니다.

3. 관리자 권한으로 PowerShell 창을 엽니다.
   * Azure AD 암호 보호 프록시 소프트웨어에는 AzureADPasswordProtection이라는 새 PowerShell 모듈이 포함되어 있습니다. 다음 단계는 이 PowerShell 모듈에서 다양한 cmdlet을 실행하는 것을 기반으로 하며, 새 PowerShell 창을 열고 다음과 같이 새 모듈을 가져왔다고 가정합니다.
      * `Import-Module AzureADPasswordProtection`

      > [!NOTE]
      > 설치 소프트웨어는 호스트 컴퓨터의 PSModulePath 환경 변수를 수정합니다. AzureADPasswordProtection powershell 모듈을 가져와서 사용할 수 있도록 이 변경 내용을 적용하려면 완전히 새로운 PowerShell 콘솔 창을 열어야 할 수 있습니다.

   * 다음 PowerShell 명령을 사용하여 서비스가 실행되고 있는지 확인합니다. `Get-Service AzureADPasswordProtectionProxy | fl`
      * "실행 중" **상태**를 반환하는 결과가 생성되어야 합니다.

4. 프록시를 등록합니다.
   * 3단계가 완료되면 Azure AD 암호 보호 프록시 서비스가 컴퓨터에서 실행 중이지만 Azure AD와 통신하는 데 필요한 자격 증명이 아직 없습니다. `Register-AzureADPasswordProtectionProxy` PowerShell cmdlet을 사용하여 해당 기능을 사용하도록 설정하려면 Azure AD에 등록해야 합니다. 이 cmdlet을 사용하려면 Azure 테넌트에 대한 전역 관리자 자격 증명과 포리스트 루트 도메인의 온-프레미스 Active Directory 도메인 관리자 권한이 필요합니다. 일단 지정된 프록시 서비스에 대해 성공하면 `Register-AzureADPasswordProtectionProxy`의 추가 호출이 계속 성공하지만 필요한 것은 아닙니다.
      * cmdlet은 다음과 같이 실행할 수 있습니다.

         ```
         $tenantAdminCreds = Get-Credential
         Register-AzureADPasswordProtectionProxy -AzureCredential $tenantAdminCreds
         ```

         이 예제는 현재 로그인한 사용자가 루트 도메인에 대한 Active Directory 도메인 관리자이기도 한 경우에만 작동합니다. 다른 방법은 `-ForestCredential` 매개 변수를 통해 필요한 도메인 자격 증명을 제공하는 것입니다.

   > [!NOTE]
   > Internet Explorer 향상된 보안 구성이 사용되면 이 작업에 실패할 수 있습니다. 해결 방법은 IESC를 사용하지 않고, 프록시를 다시 등록한 다음, IESC를 다시 사용하는 것입니다.

   > [!NOTE]
   > Azure AD 암호 보호 프록시 서비스의 등록은 서비스 수명 중의 일회성 단계로 요구됩니다. 프록시 서비스는 이 시점부터 다른 필요한 유지 관리 기능을 자동으로 수행합니다. 지정된 포리스트에 대해 성공하면 'Register-AzureADPasswordProtectionProxy'의 추가 호출이 계속 성공하지만 필요한 것은 아닙니다.

   > [!TIP]
   > cmdlet이 실행을 완료하기 전에 지정된 Azure 테넌트에 대해 처음 실행될 때 상당한 지연(수 초)이 발생할 수 있습니다. 오류가 보고되지 않는 한 이 지연은 경고로 간주되어서는 안됩니다.

5. 포리스트를 등록합니다.
   * 온-프레미스 Active Directory 포리스트는 `Register-AzureADPasswordProtectionForest` Powershell cmdlet을 사용하여 Azure와 통신하는 데 필요한 자격 증명으로 초기화해야 합니다. 이 cmdlet을 사용하려면 Azure 테넌트에 대한 전역 관리자 자격 증명과 포리스트 루트 도메인의 온-프레미스 Active Directory 도메인 관리자 권한이 필요합니다. 이 단계는 포리스트마다 한 번씩 실행됩니다.
      * cmdlet은 다음과 같이 실행할 수 있습니다.

         ```
         $tenantAdminCreds = Get-Credential
         Register-AzureADPasswordProtectionForest -AzureCredential $tenantAdminCreds
         ```

         이 예제는 현재 로그인한 사용자가 루트 도메인에 대한 Active Directory 도메인 관리자이기도 한 경우에만 작동합니다. 다른 방법은 -ForestCredential 매개 변수를 통해 필요한 도메인 자격 증명을 제공하는 것입니다.

         > [!NOTE]
         > Internet Explorer 향상된 보안 구성이 사용되면 이 작업에 실패할 수 있습니다. 해결 방법은 IESC를 사용하지 않고, 프록시를 다시 등록한 다음, IESC를 다시 사용하는 것입니다.

         > [!NOTE]
         > 사용자 환경에 여러 프록시 서버가 설치되어있는 경우, 위의 절차에서 지정하는 프록시 서버는 중요하지 않습니다.

         > [!TIP]
         > cmdlet이 실행을 완료하기 전에 지정된 Azure 테넌트에 대해 처음 실행될 때 상당한 지연(수 초)이 발생할 수 있습니다. 오류가 보고되지 않는 한 이 지연은 경고로 간주되어서는 안됩니다.

   > [!NOTE]
   > Active Directory 포리스트 등록은 포리스트 수명 중의 일회성 단계로 요구됩니다. 포리스트에서 실행되는 도메인 컨트롤러 에이전트는 이 시점부터 필요한 다른 유지 관리를 자동으로 수행합니다. 일단 지정된 포리스트에 대해 성공하면 `Register-AzureADPasswordProtectionForest`의 추가 호출이 계속 성공하지만 필요한 것은 아닙니다.

   > [!NOTE]
   > `Register-AzureADPasswordProtectionForest`가 최소 Windows Server 2012 이상에서 성공하려면 도메인 컨트롤러를 프록시 서버의 도메인에서 사용할 수 있어야 합니다. 그러나 이 단계 전에 DC 에이전트 소프트웨어가 모든 도메인 컨트롤러에 설치돼야 한다는 요구 사항은 없습니다.

6. 옵션: Azure AD 암호 보호 프록시 서비스가 특정 포트에서 수신 대기하도록 구성합니다.
   * TCP를 통한 RPC는 도메인 컨트롤러의 Azure AD 암호 보호 DC 에이전트 소프트웨어에서 Azure AD 암호 보호 프록시 서비스와 통신하는 데 사용됩니다. 기본적으로 Azure AD 암호 보호 암호 정책 프록시 서비스는 사용 가능한 모든 동적 RPC 엔드포인트에서 수신 대기합니다. 네트워킹 토폴로지 또는 방화벽 요구 사항에 따라 필요한 경우 특정 TCP 포트에서 수신 대기하도록 서비스를 대신 구성할 수 있습니다.
      * 정적 포트에서 실행되도록 서비스를 구성하려면 `Set-AzureADPasswordProtectionProxyConfiguration` cmdlet을 사용합니다.
         ```
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort <portnumber>
         ```

         > [!WARNING]
         > 이러한 변경 내용을 적용하려면 서비스를 중지했다가 다시 시작해야 합니다.

      * 동적 포트에서 실행되도록 서비스를 구성하려면 동일한 절차를 사용하지만 다음과 같이 StaticPort를 0으로 다시 설정합니다.
         ```
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort 0
         ```

         > [!WARNING]
         > 이러한 변경 내용을 적용하려면 서비스를 중지했다가 다시 시작해야 합니다.

   > [!NOTE]
   > Azure AD 암호 보호 프록시 서비스는 포트 구성을 변경한 후에 수동으로 다시 시작해야 합니다. 이러한 성격의 구성을 변경한 후에는 도메인 컨트롤러에서 실행되는 DC 에이전트 서비스 소프트웨어를 다시 시작할 필요가 없습니다.

   * 다음 예제와 같이 `Get-AzureADPasswordProtectionProxyConfiguration` cmdlet을 사용하여 서비스의 현재 구성을 쿼리할 수 있습니다.

      ```
      Get-AzureADPasswordProtectionProxyConfiguration | fl

      ServiceName : AzureADPasswordProtectionProxy
      DisplayName : Azure AD password protection Proxy
      StaticPort  : 0 
      ```

### <a name="install-the-azure-ad-password-protection-dc-agent-service"></a>Azure AD 암호 보호 DC 에이전트 서비스 설치

* `AzureADPasswordProtectionDCAgent.msi` MSI 패키지를 사용하여 Azure AD 암호 보호 DC 에이전트 서비스 소프트웨어를 설치합니다.
   * 다시 부팅할 때 암호 필터 DLL만 로드되거나 언로드되는 운영 체제 요구 사항으로 인해 소프트웨어 설치는 설치 제거 시 다시 부팅을 요구합니다.
   * 아직 도메인 컨트롤러가 아닌 컴퓨터에 DC 에이전트 서비스를 설치할 수 있습니다. 이 경우 서비스가 시작되고 실행되지만, 그렇지 않은 경우 컴퓨터가 도메인 컨트롤러로 승격될 때까지 비활성 상태가 됩니다.

   소프트웨어 설치는 표준 MSI 절차(예: `msiexec.exe /i AzureADPasswordProtectionDCAgent.msi /quiet /qn`)를 사용하여 자동화할 수 있습니다.

   > [!WARNING]
   > msiexec 명령 예제를 사용하면 즉시 다시 부팅하게 됩니다. `/norestart` 플래그를 지정하면 이를 방지할 수 있습니다.

일단 도메인 컨트롤러에 설치하고 다시 부팅하면 Azure AD 암호 보호 DC 에이전트 소프트웨어 설치가 완료됩니다. 다른 구성은 필요하지 않거나 가능하지 않습니다.

## <a name="multiple-forest-deployments"></a>다중 포리스트 배포

Azure AD 암호 보호를 여러 포리스트에 배포하기 위한 추가 요구 사항은 없습니다. 각 포리스트는 단일 포리스트 배포 섹션에서 설명한 대로 독립적으로 구성됩니다. Azure AD 암호 보호 프록시는 조인된 포리스트의 도메인 컨트롤러만 지원할 수 있습니다. 지정된 포리스트의 Azure AD 암호 보호 소프트웨어는 Active Directory 신뢰 구성과 관계없이 다른 포리스트에 배포된 Azure AD 암호 보호 소프트웨어를 인식하지 못합니다.

## <a name="read-only-domain-controllers"></a>읽기 전용 도메인 컨트롤러

암호 변경내용/집합은 RODC(읽기 전용 도메인 컨트롤러)에서 처리 및 유지되지 않습니다. 대신 쓰기 가능한 도메인 컨트롤러로 전달됩니다. 따라서 DC 에이전트 소프트웨어를 RODC에 설치할 필요가 없습니다.

## <a name="high-availability"></a>고가용성

Azure AD 암호 보호의 고가용성 보장과 관련된 주요 문제는 포리스트의 도메인 컨트롤러가 Azure에서 새 정책 또는 다른 데이터를 다운로드하려고 할 때 프록시 서버를 사용할 수 있는 가용성에 대한 것입니다. 공개 미리 보기는 포리스트당 최대 두 개의 프록시 서버를 지원합니다. 각 DC 에이전트에서 호출할 프록시 서버를 결정할 때 간단한 라운드 로빈 방식 알고리즘을 사용하고, 응답하지 않는 프록시 서버는 건너뜁니다.
고가용성과 관련된 일반적인 문제는 DC 에이전트 소프트웨어의 설계로 완화됩니다. DC 에이전트는 가장 최근에 다운로드한 암호 정책의 로컬 캐시를 유지 관리합니다. 어떤 이유로든 등록된 프록시 서버를 모두 사용할 수 없게 된 경우에도 DC 에이전트는 캐시된 암호 정책을 계속 적용합니다. 대규모 배포에서 암호 정책에 대한 적절한 업데이트 빈도는 일반적으로 시간 이하의 단위가 아닌 일 단위의 순서로 되어 있습니다.   따라서 프록시 서버를 잠시 중단하면 Azure AD 암호 보호 기능의 작업 또는 보안상의 이점에 큰 영향을 주지 않습니다.

## <a name="next-steps"></a>다음 단계

이제 온-프레미스 서버에 Azure AD 암호 보호에 필요한 서비스를 설치했으므로 [설치 후 구성을 완료하고 보고 정보를 수집](howto-password-ban-bad-on-premises-operations.md)하여 배포를 완료합니다.

[Azure AD 암호 보호에 대한 개념적 개요](concept-password-ban-bad-on-premises.md)
