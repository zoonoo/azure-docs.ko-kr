---
title: Azure 보고 도구로 개인 데이터의 문서 보호 | Microsoft Docs
description: Azure 보고 서비스를 사용하여 이니셔티브에서 개인 데이터의 개인 정보를 보호함으로써 GDPR(일반 데이터 보호 규정)을 준수합니다.
services: security
documentationcenter: na
author: barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/06/2018
ms.author: barclayn
ms.openlocfilehash: fc859f3aa34531133b7a21e36591b011c63d511c
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="document-protection-of-personal-data-with-azure-reporting-tools"></a>Azure 보고 도구로 개인 데이터의 문서 보호

이 문서에서는 Azure 보고 도구 서비스 및 기술을 사용하여 개인 데이터의 프라이버시를 보호하는 방법을 설명합니다. 이 정보는 GDPR(일반 데이터 보호 규정)과 같은 업계 및 정부 요구 사항을 준수하기 위해 사용될 수 있습니다.
## <a name="scenario"></a>시나리오

미국에 본사를 둔 대형 크루즈 회사는 영국 제도뿐만 아니라 지중해, 아드리아해 및 발트해의 여정을 제공하기 위해 운영을 확대하고 있습니다. 이러한 노력의 일환으로 이탈리아, 독일, 덴마크 및 영국에 기반을 둔 여러 개의 소형 크루즈 라인을 인수했습니다.

이 회사는 회사 데이터의 처리 및 저장에 Microsoft Azure를 사용합니다. 여기에는 식별 가능한 개인 정보 즉, 이름, 주소, 전화 번호 및 글로벌 고객층의 신용 카드 정보가 포함됩니다. 주소, 전화 번호, 납세자 번호 및 모든 위치의 회사 직원에 대한 기타 정보와 같이 기존의 인적 자원 정보도 포함됩니다. 또한 크루즈 라인에서 현재 및 과거 고객과의 관계를 추적하기 위해 개인 정보가 포함된 보상 및 충성도 프로그램 구성원에 대한 대규모 데이터베이스를 유지하고 있습니다.

회사 직원은 회사의 원격 사무실에서 네트워크에 액세스하고 전 세계에 위치한 여행사는 일부 회사 리소스에 액세스할 수 있습니다.

## <a name="problem-statement"></a>문제 설명

회사는 Azure 관리 및 보안 기능을 사용하는 다중 계층 보안 전략을 통해 고객 및 직원의 프라이버시와 개인 데이터를 보호하여 개인 데이터의 처리 및 액세스를 엄격히 통제하고 내부 및 외부 감사인에 대해 보호 조치를 입증할 수 있어야 합니다.

## <a name="company-goal"></a>회사 목표

심층 방어 보안 전략의 일환으로, 개인 데이터에 대한 모든 액세스 및 처리를 추적하고 개인 데이터에 대한 적절한 개인 정보 보호 설명서가 마련되어 제공되는지 확인하는 것이 회사의 목표입니다.

## <a name="solutions"></a>솔루션

Microsoft Azure는 포괄적인 모니터링, 로깅 및 진단 도구를 제공하여 개인 데이터의 액세스 및 처리, 데이터의 지리적 흐름 및 개인 데이터에 대한 타사 액세스와 관련된 활동 및 이벤트를 추적하고 기록합니다. 클라우드에서는 개인 데이터의 보안이 공동 책임이므로 Microsoft는 고객에게 다음 사항도 제공합니다.

- 고객 데이터의 자체적인 처리에 대한 자세한 정보

- Microsoft에서 관리하는 보안 조치

- 고객의 데이터를 보낼 위치와 방법

- Microsoft의 개인 정보 검토 프로세스의 세부 정보

### <a name="azure-active-directory"></a>Azure Active Directory

[Azure Active Directory](https://azure.microsoft.com/services/active-directory/)는 Microsoft의 클라우드 기반, 다중 테넌트 디렉터리 및 ID 관리 서비스입니다. 서비스의 로그인 및 감사 보고 기능은 고객 및 직원의 개인 데이터에 대한 적절한 액세스를 모니터링하고 확인할 수 있도록 자세한 로그인 및 응용 프로그램 사용 활동 정보를 제공합니다.

활동 보고서에는 다음 두 가지 유형이 있습니다.

- [감사 활동 보고서/로그](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs)는 시스템 활동/작업에 대한 자세한 레코드를 제공합니다.

- [로그인 활동 보고서/로그](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins)는 감사 보고서에 나열된 각 활동을 수행한 사용자를 보여 줍니다.

둘을 함께 사용하면 수행한 모든 작업의 기록과 각각을 수행한 사용자를 추적할 수 있습니다. 두 보고서 유형 모두 사용자 지정이 가능하고 필터링할 수 있습니다.

#### <a name="how-do-i-access-the-audit-and-security-logs"></a>감사 및 보안 로그에 액세스하려면 어떻게 할까요?

감사 및 보안 로그는 세 가지 방법으로 Active Directory 포털에서 액세스할 수 있습니다. **활동** 섹션(**감사 로그** 또는 **로그인** 중 하나 선택)을 통하거나 Active Directory의 **관리** 아래 **사용자 및 그룹** 또는 **엔터프라이즈 응용 프로그램**에서 액세스할 수 있습니다. 또한 Azure Active Directory 보고 API를 통해서도 보고서에 액세스할 수 있습니다. 

1. Azure Portal에서 **Azure Active Directory**를 선택합니다.

2. **활동** 섹션에서 **감사 로그**를 선택합니다.

    ![](media/protection-personal-data-azure-reporting-tools/image001.png)

3. 도구 모음에서 **열**을 클릭하여 목록 보기를 사용자 지정합니다.

4.  목록 보기에서 항목을 선택하면 사용할 수 있는 세부 정보를 모두 볼 수 있습니다.

    ![](media/protection-personal-data-azure-reporting-tools/image003.png)

또한 Azure Active Directory 보고에는 **위험 플래그가 지정된 사용자** 및 **위험한 로그인**이라는 두 가지 유형의 보안 보고서가 포함되어 Azure 환경에서 잠재적인 위험을 모니터링하는 데 도움이 될 수 있습니다.

보고 서비스에 대한 자세한 내용은 [Azure Active Directory 보고](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal)를 참조하세요.

Azure Active Directory에서 제공되는 보고서에 대한 자세한 내용을 보려면 [Azure Active Directory 작업 보고서](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal#activity-reports)를 방문하세요. 이 사이트에는 포털에서 [감사 로그 활동 보고서](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs) 및 [로그인 활동 보고서](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins)에 액세스하고 사용하는 방법에 대한 자세한 내용이 포함됩니다. 또한 [위험에 대한 플래그가 지정된 사용자](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-security-user-at-risk) 및 [위험한 로그인](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-security-risky-sign-ins) 보안 보고서에 대한 정보도 포함됩니다.

프로그래밍 방식으로 Azure Directory 보고에 연결하는 방법에 대한 자세한 내용을 보려면 [Azure Active Directory 감사 API 참조](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-audit-reference) 사이트를 방문하세요.

### <a name="log-analytics"></a>Log Analytics

[Log Analytics](https://azure.microsoft.com/services/log-analytics/)는 [Azure Monitor의 데이터를 수집](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage)하여 다른 데이터와 상호 연결하고 추가 분석을 제공할 수 있습니다. Azure Monitor는 Azure 환경에 대한 모니터링 데이터를 수집하고 분석합니다. 

로그 검색, 뷰 및 솔루션과 같은 Log Analytics의 분석 도구는 전체 사용자 환경의 중앙 집중식 분석을 제공하는 모든 수집된 데이터에 대해 작동합니다. Log Analytics는 Windows 이벤트 로그, IIS 로그 및 Syslog를 집계 및 분석하여 개인 데이터가 권한이 없는 사용자에게 공개될 수 있는 잠재적인 개인 데이터 위반을 감지하는 데 도움을 줄 수 있습니다.

#### <a name="how-do-i-use-log-analytics"></a>Log Analytics를 사용하려면 어떻게 할까요?

Log Analytics는 OMS 포털 또는 Azure Portal을 통해 모든 웹 브라우저에서 액세스할 수 있습니다. Log Analytics는 Log Analytics 작업 영역의 데이터를 신속하게 검색하고 통합할 수 있는 쿼리 언어를 포함합니다. 로그 검색을 만들고 저장하여 포털에서 데이터를 직접 분석할 수 있습니다.

Azure Portal에서 Log Analytics 작업 영역을 만들려면 다음을 수행합니다.

1. Marketplace의 서비스 목록에서 **Log Analytics**를 선택합니다.

2. **만들기**를 선택한 후 Log Analytics 작업 영역 이름을 지정하고 구독, 리소스 그룹, 위치 및 가격 책정 계층을 선택합니다.

3. **확인**을 클릭하면 작업 영역 목록이 표시됩니다.

4. 세부 정보를 보려면 작업 영역을 선택합니다.

    ![](media/protection-personal-data-azure-reporting-tools/image004.png)

서비스에 대한 자세한 정보를 보려면 [Log Analytics 설명서](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)를 방문하세요.

평가 작업 영역을 만들고 서비스를 사용하는 방법에 대한 기본 사항을 보려면 [Log Analytics 작업 영역 시작](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started) 자습서를 방문하세요.

위에 설명된 로그와 함께 Log Analytics를 사용하도록 연결하는 방법에 대한 자세한 내용을 보려면 다음 웹 페이지를 방문하세요.

[Log Analytics의 Windows 이벤트 로그 데이터 원본](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-windows-events)

[Log Analytics의 IIS 로그](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-iis-logs)

[Log Analytics의 Syslog 데이터 원본](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-syslog)

### <a name="azure-monitorazure-activity-log"></a>Azure Monitor/Azure 활동 로그 

[Azure Monitor](https://azure.microsoft.com/services/monitor/)는 대부분의 Microsoft Azure 서비스에 대한 기본 수준의 인프라 메트릭과 로그를 제공합니다.
모니터링을 통해 Azure 응용 프로그램에 대한 깊은 통찰력을 얻을 수 있습니다. Azure Monitor는 Azure 진단 확장(Windows 또는 Linux)을 사용하여 대부분의 응용 프로그램 수준 메트릭과 로그를 수집합니다. [Azure 활동 로그](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)는 Azure Monitor로 볼 수 있는 리소스 중 하나입니다. 모든 API 호출을 추적하고 [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)에서 발생하는 활동에 대한 다양한 정보를 제공합니다.
Azure 인프라에 표시되는 대로 리소스에 대한 자세한 내용은 활동 로그(이전에 작업 또는 감사 로그라고도 함)를 검색할 수 있습니다. 

활동 로그에 기록된 대부분의 정보는 성능 및 서비스 상태와 관련되지만 데이터 보호와 관련된 정보도 있습니다. 활동 로그를 통해 Azure 구독의 리소스에 대한 모든 쓰기 작업(PUT, POST, DELETE)에서 “무엇을, 누가, 언제”를 판단할 수 있습니다.

예를 들어 관리자가 개인 데이터 보호에 영향을 줄 수 있는 네트워크 보안 그룹을 삭제할 경우 레코드를 제공합니다. 활동 로그 항목은 Azure Monitor에 90일 동안 저장됩니다.

#### <a name="how-do-i-use-the-data-collected-by-azure-monitor"></a>Azure Monitor로 수집된 데이터는 어떻게 사용하나요?

활동 로그 및 기타 Azure Monitor 리소스의 데이터를 사용하는 방법은 여러 가지가 있습니다.

- 데이터를 실제 위치에서 다른 위치로 실시간으로 스트리밍할 수 있습니다.

- [Azure Storage 계정](https://docs.microsoft.com/azure/storage/common/storage-introduction)을 사용하고 보존 정책을 설정하여 기본값보다 더 오랫동안 데이터를 저장할 수 있습니다.

- [Azure Portal](https://azure.microsoft.com/features/azure-portal/), [Azure Application Insights](https://azure.microsoft.com/services/application-insights/), [Microsoft PowerBI](https://powerbi.microsoft.com/) 또는 타사 시각화 도구를 사용하여 그래픽 및 차트로 데이터를 시각화할 수 있습니다.

- Azure Monitor REST API, CLI 명령, [PowerShell](https://docs.microsoft.com/powershell/) cmdlet 또는 .NET SDK를 사용하여 데이터를 쿼리할 수 있습니다.

Azure Monitor를 시작하려면 Azure Portal에서 **모든 서비스**를 선택합니다.

1. **모니터링 및 관리** 섹션에서 **모니터**로 아래로 스크롤합니다.

    ![](media/protection-personal-data-azure-reporting-tools/image005.png)

2.  모니터가 **활동 로그** 보기에 열립니다.

    ![](media/protection-personal-data-azure-reporting-tools/image007.png)

공통 필터에 대한 쿼리를 만들어 저장한 다음 가장 중요한 쿼리를 포털 대시보드에 고정하면 기준에 부합하는 이벤트가 발생할 경우 항상 알 수 있습니다.

1. 리소스 그룹, timespan 및 이벤트 범주에 따라 보기를 필터링할 수 있습니다.

    ![](media/protection-personal-data-azure-reporting-tools/image008.png)

2. 그런 다음 **고정** 단추를 클릭하여 포털 대시보드에 쿼리를 고정할 수 있습니다. 이렇게 하면 서비스에 대한 운영 데이터 정보를 제공하는 단일 출처를 만들 수 있습니다. 쿼리 이름 및 결과 수가 대시보드에 표시됩니다.

또한 모니터를 사용하여 모든 Azure 리소스에 대한 메트릭을 보고 진단 설정 및 경고를 구성하며 로그를 검색할 수도 있습니다. Azure Monitor 및 활동 로그를 사용하는 방법에 대한 자세한 내용은 [Azure Monitor 시작](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-get-started)을 참조하세요.

### <a name="azure-diagnostics"></a>Azure 진단 

Azure의 진단 기능을 통해 여러 원본에서 데이터 수집이 가능합니다. 보안 로그를 포함하는 Windows 이벤트 로그는 개인 데이터 보호를 추적하고 문서화하는 데 특히 유용할 수 있습니다. 보안 로그는 로그온 성공 및 실패 이벤트는 물론 사용 권한 변경, 특정 유형의 공격을 나타내는 패턴 검색, 보안 관련 정책 변경, 보안 그룹 멤버 자격 변경 등을 추적합니다.

예를 들어 이벤트 ID 4695는 감사 가능한 보호된 데이터의 보호되지 않은 시도에 대해 경고합니다. 이는 개인 키, 저장된 자격 증명 및 기타 기밀 정보와 같은 데이터를 보호하는 데 도움이 되는 DPAPI(데이터 보호 API)와 관련이 있습니다.

#### <a name="how-do-i-enable-the-diagnostics-extension-for-windows-vms"></a>Windows VM에 대해 진단 확장을 사용하려면 어떻게 할까요?

PowerShell을 사용하여 Windows VM에 대해 진단 확장을 사용하도록 설정하여 로그 데이터를 수집할 수 있습니다. 이렇게 하는 단계는 사용하는 배포 모델(리소스 관리자 또는 클래식)에 따라 다릅니다. 리소스 관리자 배포 모델을 통해 만든 기존 VM에서 진단 확장을 사용하도록 설정하려면 [Set-AzureRMVMDiagnosticsExtension PowerShell cmdlet](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmdiagnosticsextension?view=azurermps-4.3.1)을 사용할 수 있습니다.

   ![](media/protection-personal-data-azure-reporting-tools/image009.png)

*\$diagnosticsconfig_path*는 XML의 진단 구성이 포함된 파일의 경로입니다. VM에 Azure 진단을 사용하도록 설정하는 방법은 [PowerShell을 사용하여 Windows를 실행하는 가상 머신에서 Azure 진단을 사용하도록 설정](https://docs.microsoft.com/azure/virtual-machines/windows/ps-extensions-diagnostics)을 참조하세요.

Azure 진단 확장은 수집된 데이터를 Azure Storage 계정에 전송하거나 Application Insights와 같은 서비스에 보낼 수 있습니다. 그런 다음 감사를 위해 데이터를 사용할 수 있습니다.

#### <a name="how-do-i-store-and-view-diagnostic-data"></a>진단 데이터를 저장하고 보려면 어떻게 하나요?

진단 데이터를 Microsoft Azure Storage 에뮬레이터 또는 Azure Storage에 전송하지 않는 한 진단 데이터는 영구적으로 저장되지 않는다는 것을 유념해야 합니다. Azure Storage에서 진단 데이터를 저장하고 보려면 다음 단계를 따르세요.

1. ServiceConfiguration.cscfg 파일에서 저장소 계정을 지정합니다. Azure 진단에서는 데이터 형식에 따라 Blob service 또는 Table service를 사용할 수 있습니다. Windows 이벤트 로그는 테이블 형식으로 저장됩니다.

2. 데이터를 전송합니다. 구성 파일을 통해 진단 데이터 전송을 요청할 수 있습니다. SDK 2.4 이하에서는 프로그래밍 방식으로 요청을 만들 수도 있습니다.

3. [Azure Storage 탐색기](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer), Visual Studio의 [서버 탐색기](https://docs.microsoft.com/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage) 또는 Azure Management Studio의 [Azure 진단 관리자](https://www.cerebrata.com/products/azure-diagnostics-manager)를 사용하여 데이터를 확인합니다.

이러한 각 단계를 수행하는 방법에 대한 자세한 내용은 [Azure Storage에서 진단 데이터 저장 및 보기](https://docs.microsoft.com/azure/cloud-services/cloud-services-dotnet-diagnostics-storage)를 참조하세요.

### <a name="azure-storage-analytics"></a>Azure Storage 분석 

저장소 분석은 Storage 서비스에 대해 성공한 요청과 실패한 요청 관련 상세 정보를 기록합니다. 이 정보는 개별 요청을 모니터링하는 데 사용할 수 있으며 서비스에 저장된 개인 데이터에 대한 액세스를 문서화하는 데 도움이 됩니다. 하지만 저장소 분석 로깅은 Storage 계정에 대해 기본적으로 사용하지 않도록 설정됩니다. Azure Portal에서 활성화할 수 있습니다.

#### <a name="how-do-i-configure-monitoring-for-a-storage-account"></a>저장소 계정에 대한 모니터링은 어떻게 구성하나요?

저장소 계정에 대한 모니터링을 구성하려면 다음을 수행합니다.

1. Azure Portal에서 **Storage 계정**을 선택한 후 모니터링하려는 계정 이름을 선택합니다.

    ![](media/protection-personal-data-azure-reporting-tools/image011.png)

2. **모니터링** 섹션에서 **진단**을 선택합니다.

3.  각 서비스에 대해 모니터링하려는 메트릭 데이터의 **형식**을 선택합니다(Blob, 테이블, 파일). Azure Storage에서 Blob, 테이블 및 큐 서비스에 대한 읽기, 쓰기 및 삭제 요청에 대한 진단 로그를 저장하도록 지시하려면 **Blob 로그, 테이블 로그** 및 **큐 로그**를 선택합니다.

    ![](media/protection-personal-data-azure-reporting-tools/image013.png)

4. 아래에 있는 슬라이더를 사용하여 **보존** 정책을 일 단위(1-365 값)로 설정합니다. 기본값은 7일입니다.

5. **저장**을 선택하여 구성 설정을 적용합니다.

저장소 로깅 로그 항목에는 개별 요청에 대 한 다음 정보가 포함됩니다.

- 시작 시간, 종단 간 대기 시간, 서버 대기 시간 등의 타이밍 정보입니다.

- 작업 유형, 클라이언트가 액세스하는 저장소 개체의 키, 성공 또는 실패 여부, 클라이언트로 반환된 HTTP 상태 코드와 같은 저장소 작업에 대한 세부 정보입니다.

- 클라이언트에서 사용한 인증 유형과 같은 인증 세부 정보입니다.

- ETag 값과 마지막으로 수정된 타임스탬프와 같은 동시성 정보입니다.

- 요청 및 응답 메시지의 크기입니다.

저장소 분석 로깅을 사용하도록 설정하는 방법에 대한 자세한 내용은 [Azure Portal에서 Storage 계정 모니터링](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account)을 참조하세요.

### <a name="azure-security-center"></a>Azure Security Center 

[Azure Security Center](https://azure.microsoft.com/services/security-center/)는 위협을 예방 및 감지하고 대응 권장 사항을 제공하기 위해 Azure 리소스의 보안 상태를 모니터링합니다. 개인 데이터의 프라이버시를 보호하는 보안 조치를 문서화하는 데 도움이 되는 다양한 방법을 제공합니다.

보안 상태 모니터링을 통해 보안 정책 준수를 보장할 수 있습니다. 보안 모니터링은 리소스를 감사하여 조직의 표준 또는 모범 사례를 충족하지 않는 시스템을 식별하는 사전 예방 전략입니다. 다음 리소스의 보안 상태를 모니터링할 수 있습니다.

- Compute(가상 머신 및 클라우드 서비스)

- 네트워킹(가상 네트워크)

- 저장소 및 데이터(서버와 데이터베이스 감사 및 위협 감지, TDE, 저장소 암호화)

- 응용 프로그램(잠재적 보안 문제)

이러한 범주의 보안 문제는 개인 데이터의 프라이버시에 위협이 될 수 있습니다.

#### <a name="how-do-i-view-the-security-state-of-my-azure-resources"></a>Azure 리소스의 보안 상태를 보려면 어떻게 할까요?

Security Center에서는 Azure 리소스의 보안 상태를 주기적으로 분석합니다. 대시보드의 **방지** 섹션에서 식별된 잠재적인 보안 취약성을 볼 수 있습니다.

   ![](media/protection-personal-data-azure-reporting-tools/image014.png)

1. **방지** 섹션에서 **Compute** 타일을 선택합니다. 여기에는 **개요**와 함께, 모든 VM 및 해당 보안 상태가 포함된 **가상 머신** 목록, Security Center에서 모니터링된 웹 및 작업자 역할의 **클라우드 서비스** 목록이 표시됩니다.

2. **개요** 탭에는 추가 정보를 볼 권장 사항이 제안됩니다.

3. **가상 머신** 탭에서 VM을 선택하여 추가 세부 정보를 봅니다.

Azure Security Center에서 데이터 수집을 사용하도록 설정하면 Microsoft Monitoring Agent가 배포되는 모든 지원되는 새 가상 머신과 기존 가상 머신에 자동으로 프로비전됩니다. 이 에이전트에서 수집된 데이터는 구독 또는 새 작업 영역에 연결된 기존 [Log Analytics](https://azure.microsoft.com/services/log-analytics/) 작업 영역 중 하나에 저장됩니다.

[위협 인텔리전스 보고서](https://docs.microsoft.com/azure/security-center/security-center-threat-report)는 Security Center에서 제공됩니다. 이 보고서는 공격자의 ID, 목표, 현재 및 과거의 공격 캠페인, 사용된 전술, 도구 및 절차를 식별하는 데 도움이 되는 유용한 정보를 제공합니다. 마이그레이션 및 수정 정보도 포함됩니다.

이러한 위협 보고서의 주요 목적은 즉각적인 위협에 효과적으로 대응하고 후속 조치를 통해 문제를 완화하는 것입니다. 보고서에 포함된 정보는 보고 및 감사용으로 사고 대응을 문서화할 때도 유용할 수 있습니다.

위협 인텔리전스 보고서는 .PDF 형식으로 표시되며 Azure Security Center에서 각 보안 경고에 대한 **의심스러운 프로세스 실행** 블레이드의 **보고서** 필드에 있는 링크를 통해 액세스합니다.

위협 인텔리전스 보고서를 보고 사용하는 방법에 대한 자세한 내용은 [Azure Security Center 위협 인텔리전스 보고서](https://docs.microsoft.com/azure/security-center/security-center-threat-report)를 참조하세요.

## <a name="next-steps"></a>다음 단계:

[Azure Active Directory 보고 API 시작](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal)

[Log Analytics란?](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)

[Microsoft Azure의 모니터링 개요](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview)

[Azure 활동 로그 소개(비디오)](https://azure.microsoft.com/resources/videos/intro-activity-log/)
