<properties
   pageTitle="Operations Management Suite 보안 및 감사 솔루션의 리소스 모니터링 | Microsoft Azure"
   description="이 문서는 OMS 보안 및 감사 기능을 사용하여 리소스를 모니터링하고 보안 문제를 식별하는 방법을 설명합니다."
   services="operations-management-suite"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="operations-management-suite"
   ms.topic="article" 
   ms.devlang="na"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/18/2016"
   ms.author="yurid"/>


# <a name="monitoring-resources-in-operations-management-suite-security-and-audit-solution"></a>Operations Management Suite 보안 및 감사 솔루션의 리소스 모니터링

이 문서는 OMS 보안 및 감사 기능을 사용하여 리소스를 모니터링하고 보안 문제를 식별하는 방법을 설명합니다.

## <a name="what-is-oms?"></a>OMS란?

Microsoft Operations Management Suite(OMS)란 온-프레미스 및 클라우드 인프라를 관리 및 보호하도록 도와주는 Microsoft의 클라우드 기반 IT 관리 솔루션입니다. OMS에 대한 자세한 내용은 [Operations Management Suite](https://technet.microsoft.com/library/mt484091.aspx)문서를 참조하세요.

## <a name="monitoring-resources"></a>리소스 모니터링

보안 문제는 가능하면 처음부터 발생하지 않도록 예방해야 합니다. 하지만 모든 보안 문제를 방지하는 것은 가능하지 않습니다. 보안 문제가 발생하면 영향을 최소화하도록 해야 합니다.  보안 문제의 수와 영향을 최소화하기 위해 세 가지 중요 권장 사항을 사용할 수 있습니다.

- 사용자 환경의 취약성을 정기적으로 평가합니다.
- 모든 컴퓨터 시스템과 네트워크 장치를 정기적으로 검사하여 최신 패치가 모두 설치되었는지 확인합니다.
- 운영 시스템 이벤트 로그, 응용 프로그램별 로그, 침입 검색 시스템 로그를 포함한 모든 로그와 로깅 메커니즘을 정기적으로 검사합니다.

OMS 보안 및 감사 솔루션을 사용하면 IT가 모든 리소스를 능동적으로 모니터링하여 보안 문제의 영향을 최소화할 수 있습니다. OMS 보안 및 감사에는 리소스 모니터링에 사용할 수 있는 보안 도메인이 있습니다. 보안 도메인을 통해 옵션에 빠르게 액세스할 수 있으며, 여기서는 보안 모니터링과 관려된 다음 도메인에 대해 자세히 살펴봅니다.

- 맬웨어 평가
- 업데이트 평가
- ID 및 액세스

> [AZURE.NOTE] 모든 옵션에 대한 개요는 [Operations Management Suite 보안 및 감사 솔루션 시작](oms-security-getting-started.md)을 참조하세요.

### <a name="monitoring-system-protection"></a>시스템 보호 모니터링

심층 방어 수단에서는 자산의 전반적 보안 상태를 위해 모든 보호 계층이 중요합니다. 위협이 검색된 컴퓨터와 적절히 보호되지 않은 컴퓨터는 보안 도메인의 맬웨어 평가 타일에 표시됩니다. 맬웨어 평가의 정보를 사용하면 보호가 필요한 서버를 보호하는 계획을 식별할 수 있습니다. 이 옵션에 액세스하려면 다음 단계를 따릅니다.

1. **Microsoft Operations Management Suite** 기본 대시보드에서 **보안 및 감사** 타일을 클릭합니다.

    ![보안 및 감사](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig1.png)

2. **보안 및 감사** 대시보드에서 **보안 도메인**의 **맬웨어 평가**를 클릭합니다. **맬웨어 평가** 대시보드가 아래와 같이 나타납니다.

![맬웨어 평가](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig2-ga.png)

**맬웨어 평가** 대시보드를 사용하여 다음과 같은 보안 문제를 식별할 수 있습니다.

- **미해결 위협**: 손상된 상태이며 시스템에 미해결 위협이 있는 컴퓨터
- **해결된 위협**: 손상되었지만 위협이 해결된 컴퓨터
- **서명 만료**: 맬웨어 보호를 사용하지만 서명이 만료된 컴퓨터
- **실시간 보호하지 않음**: 맬웨어 방지 프로그램이 설치되지 않은 컴퓨터

### <a name="monitoring-updates"></a>업데이트 모니터링 

최신 보안 업데이트를 적용하는 것이 보안 모범 사례이며 업데이트 관리 전략에 포함해야 합니다. Microsoft Monitoring Agent 서비스(HealthService.exe)는 모니터링되는 컴퓨터에서 업데이트 정보를 읽은 다음 업데이트된 이 정보를 클라우드의 OMS 서비스로 전송하여 처리합니다. Microsoft Monitoring Agent 서비스는 자동 서비스로 구성되며 대상 컴퓨터에 항상 실행되고 있어야 합니다.

![업데이트 모니터링](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig3.png)

논리는 업데이트 데이터에 적용되며 클라우드 서비스는 데이터를 기록합니다. 누락된 업데이트가 발견되면 **업데이트** 대시보드에 표시됩니다. **업데이트** 대시보드를 사용하여 누락된 업데이트를 작업하고 필요로 하는 서버에 적용하도록 계획할 수 있습니다. 아래 단계를 따라 **업데이트** 대시보드에 액세스합니다.

1. **Microsoft Operations Management Suite** 기본 대시보드에서 **보안 및 감사** 타일을 클릭합니다.
2. **보안 및 감사** 대시보드에서 **보안 도메인**의 **업데이트 평가**를 클릭합니다. 업데이트 대시보드가 아래와 같이 표시됩니다.

![업데이트 평가](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig4.png)

이 대시보드에서 업데이트 평가를 수행하여 컴퓨터의 현재 상태를 확인하고 가장 위험한 위협을 해결할 수 있습니다. IT 관리자는 **위험 또는 중요 업데이트** 타일을 사용하여 다음과 같이 누락된 업데이트에 대한 상세 정보에 액세스할 수 있습니다.

![검색 결과](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig5.png)

이 보고서에는 이 시스템이 취약한 위협의 유형을 식별하는 데 사용할 수 있는 중요 정보가 포함됩니다. 예를 들면 보안 업데이트와 관련된 Microsoft KB 문서, 취약성에 대한 상세 정보가 포함된 MS 게시판이 있습니다.

### <a name="monitoring-identity-and-access"></a>ID 및 액세스 모니터링

사용자들이 어디에서나 다양한 장치를 사용하여 많은 클라우드 및 온-프레미스 앱에 액세스함에 따라 자격 증명을 보호하는 것이 매우 중요해졌습니다. 자격 증명 도난 공격은 공격자가 처음에 일반 사용자의 자격 증명에 대한 액세스 권한을 얻은 다음 네트워크 내 시스템에 액세스하는 공격입니다. 대부분의 경우 이러한 최초 공격은 네트워크에 대한 액세스 권한을 얻기 위한 한 가지 방법에 불과하며 궁극적 목적은 권한이 있는 계정을 발견하는 것입니다. 

공격자는 네트워크 안에 남아 도구를 자유롭게 사용하면서 로그온된 다른 계정의 세션에서 자격 증명을 추출합니다. 시스템 구성에 따라 이러한 자격 증명은 해시, 티켓으로 추출하거나 심지어 일반 텍스트 비밀번호의 형태로도 추출할 수 있습니다.  

> [AZURE.NOTE] 인터넷에 직접 노출된 컴퓨터에서는 모든 종류의 잘 알려진 사용자 이름(예: Administrator)을 사용하여 로그인하려는 시도가 여러 번 실패하는 것을 볼 수 있습니다. 대부분의 경우 잘 알려진 사용자 이름을 사용하지 않고 강력한 비밀번호를 사용하는 것이 좋습니다.

침입자가 권한이 있는 계정을 손상하기 전에 그러한 침입자를 식별하는 것이 가능합니다. **OMS 보안 및 감사 솔루션** 을 사용하여 ID 및 액세스를 모니터링할 수 있습니다. 아래 단계를 따라 **ID 및 액세스** 대시보드에 액세스합니다.

1. **Microsoft Operations Management Suite** 기본 대시보드에서 보안 및 감사 타일을 클릭합니다.
2. **보안 및 감사** 대시보드에서 **보안 도메인**의 **ID 및 액세스**를 클릭합니다. **ID 및 액세스** 대시보드가 아래와 같이 표시됩니다.

![ID 및 액세스](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig6-ga.png)

일반 모니터링 전략에 반드시 ID 모니터링을 포함해야 합니다. IT 관리자는 유효한 특정 사용자 이름에 여러 번의 시도가 있었는지 살펴보아야 합니다. 그럴 경우 실제 사용자 이름을 확보한 공격자가 무차별 공격을 시도하고 있거나 자동 도구가 만료된 하드 코딩 비밀번호를 사용하는 있는 경우일 수 있습니다.

IT는 이 대시보드를 사용하여 회사 리소스에 잠재적 위협이 될 수 있는 ID 및 액세스를 빠르게 식별할 수 있습니다. 잠재적 동향을 식별하는 것도 특히 중요합니다. 예를 들어 시간별 로그온 타일에서는 실패한 로그온 시도가 수행된 시간 및 횟수를 확인할 수 있습니다. 이 경우에는 **FileServer** 컴퓨터에 35회의 로그온 시도가 있었습니다. 이 컴퓨터를 클릭하여 자세한 정보를 확인할 수 있습니다. 

![자세한 내용](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig7-new.png)

이 컴퓨터에 대해 생성된 보고서는 이 패턴에 대해 중요한 정보를 보여줍니다. **ACCOUNT** 열에는 시스템에 액세스를 시도하는 데 사용된 사용자 계정, **TIMEGENERATED** 열에는 시도가 수행된 시간 간격, **LOGONTYPENAME** 열에는 이 시도가 수행된 위치가 표시됩니다. 이러한 시도가 시스템 안에서 프로그램에 의해 로컬로 수행된 경우 **PROCESS** 열에 프로세스 이름이 표시됩니다. 프로그램이 로그온 시도를 하는 시나리오에서는 이미 프로세스 이름을 알고 있으므로 대상 시스템에서 추가 조사를 수행할 수 있습니다.

## <a name="see-also"></a>참고 항목

이 문서에서는 OMS 보안 및 감사 솔루션을 사용하여 리소스를 모니터링하는 방법을 배웠습니다. OMS 보안에 대해 자세히 알아보려면 다음 문서를 참조하세요.

- [OMS(Operations Management Suite) 개요](operations-management-suite-overview.md)
- [Operations Management Suite 보안 및 감사 솔루션 시작](oms-security-getting-started.md)
- [Operations Management Suite 보안 및 감사 솔루션의 보안 경고 모니터링 및 응답](oms-security-responding-alerts.md)


<!--HONumber=Oct16_HO2-->


