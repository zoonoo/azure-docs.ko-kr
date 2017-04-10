---
title: "Service Fabric 응용 프로그램 추적 저장소와 같은 Elasticsearch 사용 | Microsoft Docs"
description: "서비스 패브릭 응용 프로그램이 Elasticsearch 및 Kibana를 사용하여 응용 프로그램 추적을 통해 저장, 인덱싱 및 검색하는 방법에 대해 설명합니다."
services: service-fabric
documentationcenter: .net
author: karolz-ms
manager: adegeo
editor: 
ms.assetid: e59b0c39-e468-4d9e-b453-d5f2a8ad20d8
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/07/2017
ms.author: karolz@microsoft.com
redirect_url: /azure/service-fabric/service-fabric-diagnostic-collect-logs-without-an-agent
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 86c1b4dd0cc8833404bd7b66804fa67bbf0d119a
ms.lasthandoff: 12/08/2016


---
# <a name="use-elasticsearch-as-a-service-fabric-application-trace-store"></a>서비스 패브릭 응용 프로그램 추적 저장소로 ElasticSearch 사용
## <a name="introduction"></a>소개
이 문서에서는 [Azure Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric/) 응용 프로그램이 프로그램 추적 저장소, 인덱싱 및 검색을 위해 **Elasticsearch** 및 **Kibana**를 사용할 수 있는 방법에 대해 설명합니다. [Elasticsearch](https://www.elastic.co/guide/index.html)는 이 작업에 적합한 공개 소스, 분산 및 확장 가능한 실시간 검색 및 분석 엔진이며 Microsoft Azure에서 실행되는 Windows 및 Linux 가상 컴퓨터에 설치할 수 있습니다. Elasticsearch는 **ETW(Windows용 이벤트 추적)**와 같은 기술을 사용하여 생성된 *구조화된* 추적을 효율적으로 처리할 수 있습니다.

ETW는 서비스 패브릭 런타임에서 진단 정보(추적)를 얻는 데 사용됩니다. 이는 서비스 패브릭 응용 프로그램에서 응용 프로그램의 진단 정보를 얻는 데에도 권장되는 방법입니다. 동일한 메커니즘을 사용하면 런타임 제공 및 응용 프로그램 제공 추적 사이의 상관관계를 허용하고 문제를 보다 쉽게 해결할 수 있습니다. Visual Studio의 서비스 패브릭 프로젝트 템플릿에는 기본적으로 ETW 추적을 내보내는 로깅 API(.NET **EventSource** 클래스 기반)가 포함됩니다. ETW를 사용한 서비스 패브릭 응용 프로그램 추적에 대한 일반적인 개요는 [로컬 컴퓨터 개발 설정에서의 모니터링 및 진단 서비스](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)를 참조하세요.

Elasticsearch에 추적을 표시하려면 실시간으로 서비스 패브릭 클러스터 노드에서 캡처하고(응용 프로그램 실행 중) Elasticsearch 끝점에 전송해야 합니다. 추적 캡처에 대한 두 가지 주요 옵션이 있습니다.

* **In Process 추적 캡처**  
   응용 프로그램 또는 보다 정확하게 서비스 프로세스는 추적 저장소에 진단 데이터를 전송하는 것을 담당합니다(Elasticsearch).
* **Out of Process 추적 캡처**  
   별도 에이전트를 서비스 프로세스에서 추적 캡처하고 추적 저장소에 보냅니다.

아래에서 Azure에 Elasticsearch를 설정하는 방법을 설명하고 캡처 옵션의 장단점에 대해 알아보고 Elasticsearch에 데이터를 전송하도록 서비스 패브릭 서비스를 구성하는 방법을 설명합니다.

## <a name="set-up-elasticsearch-on-azure"></a>Azure에 Elasticsearch 설정
Azure에 Elasticsearch 서비스를 설정하는 가장 간단한 방법은 [**Azure Resource Manager 템플릿**](../azure-resource-manager/resource-group-overview.md)을 통하는 것입니다. 포괄적인 [Elasticsearch에 대한 빠른 시작 Azure 리소스 관리자 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/elasticsearch) 은 Azure 빠른 시작 템플릿 리포지토리에서 사용할 수 있습니다. 이 템플릿에서는 규모 단위(노드 그룹)에 대한 별도의 저장소 계정을 사용합니다. 또한 서로 다른 구성 및 여러 개의 연결된 데이터 디스크를 사용하여 별개의 클라이언트 및 서버 노드를 프로비전할 수 있습니다.

여기에서 **Azure 진단 도구 저장소** 의 [ES-MultiNode](https://github.com/Azure/azure-diagnostics-tools)라는 다른 템플릿을 사용합니다. 이 템플릿은 사용하기 쉬우며 HTTP 기본 인증으로 보호되는 Elasticsearch 클러스터를 만듭니다. 계속하기 전에 리포지토리를 복제하거나 zip 파일을 다운로드하여 GitHub에서 컴퓨터로 리포지토리를 다운로드하세요. ES-MultiNode 템플릿은 동일한 이름의 폴더에 있습니다.

### <a name="prepare-a-machine-to-run-elasticsearch-installation-scripts"></a>Elasticsearch 설치 스크립트를 실행하도록 컴퓨터 준비
ES MultiNode 템플릿을 사용하는 가장 쉬운 방법은 `CreateElasticSearchCluster`라는 제공된 Azure PowerShell 스크립트를 통하는 것입니다. 이 스크립트를 사용하려면 PowerShell 모듈 및 **openssl**이라는 도구를 설치해야 합니다. 후자는 Elasticsearch 클러스터를 원격으로 관리하는 데 사용될 수 있는 SSH 키를 만들기 위해 필요합니다.

`CreateElasticSearchCluster` 스크립트는 Windows 컴퓨터에서 ES-MultiNode 템플릿을 보다 쉽게 사용할 수 있도록 디자인되었습니다. 비Windows 컴퓨터에서 템플릿을 사용할 수도 있지만 해당 시나리오가 이 문서의 범위를 벗어납니다.

1. 아직 설치하지 않은 경우 [**Azure PowerShell 모듈**](http://aka.ms/webpi-azps)을 설치합니다. 메시지가 표시되면 **실행**을 클릭한 다음 **설치**합니다. Azure PowerShell 1.3 이상이 필요합니다.
2. **openssl** 도구는 [**Windows용 Git**](http://www.git-scm.com/downloads)의 배포에 포함되어 있습니다. [Windows용 GIT](http://www.git-scm.com/downloads) 를 아직 설치하지 않은 경우 지금 설치하세요. (기본 설치 옵션도 적합합니다.)
3. Git가 설치되었지만 시스템 경로에 포함되지 않았다고 가정하면 Microsoft Azure PowerShell 창을 열고 다음 명령을 실행합니다.
   
    ```powershell
    $ENV:PATH += ";<Git installation folder>\usr\bin"
    $ENV:OPENSSL_CONF = "<Git installation folder>\usr\ssl\openssl.cnf"
    ```
   
    `<Git installation folder>`를 컴퓨터의 Git 위치로 대체합니다. 기본값은 **"C:\Program Files\Git"**입니다. 첫 번째 경로의 시작 부분에는 세미콜론 문자가 있습니다.
4. Azure에 로그온하고( [`Add-AzureRmAccount`](https://msdn.microsoft.com/library/mt619267.aspx) cmdlet를 통해) Elastic Search 클러스터를 만드는 데 사용되어야 하는 구독을 선택했는지 확인합니다. `Get-AzureRmContext` 및 `Get-AzureRmSubscription` cmdlet를 사용하여 올바른 구독을 선택했는지 확인할 수 있습니다.
5. 아직 수행하지 않은 경우 현재 디렉터리를 ES-MultiNode 폴더로 변경합니다.

### <a name="run-the-createelasticsearchcluster-script"></a>CreateElasticSearchCluster 스크립트 실행
스크립트를 실행하기 전에 `azuredeploy-parameters.json` 파일을 열고 스크립트 매개 변수에 대한 값을 확인하거나 제공합니다. 다음 매개 변수가 제공됩니다.

| 매개 변수 이름 | 설명 |
| --- | --- |
| dnsNameForLoadBalancerIP |Elasticsearch 클러스터에 대해 공개적으로 표시되는 DNS 이름을 만드는 데 사용되는 이름입니다(Azure 지역 도메인을 제공된 이름에 추가하여). 예를 들어 이 매개 변수 값이 "myBigCluster"인 경우 선택한 Azure 지역은 미국 서부이며 클러스터에 대한 결과 DNS 이름이 myBigCluster.westus.cloudapp.azure.com이 됩니다. <br /><br />이 이름은 데이터 노드 이름 같은 Elasticsearch 클러스터와 관련된 많은 아티팩트에 대한 이름의 루트로 제공됩니다. |
| adminUsername |Elasticsearch 클러스터를 관리하기 위한 관리자 계정 이름입니다(해당 SSH 키는 자동으로 생성됨). |
| dataNodeCount |Elasticsearch 클러스터의 노드 수입니다. 스크립트의 현재 버전은 데이터와 쿼리 노드를 구분하지 않습니다. 모든 노드는 두 역할을 모두 수행합니다. 기본 노드 수는 3개입니다. |
| dataDiskSize |각 데이터 노드에 할당되는 데이터 디스크의 크기(GB)입니다. 각 노드는 Elasticsearch 서비스 전용으로 4개의 데이터 디스크를 받게 됩니다. |
| region |Elasticsearch 클러스터가 위치해야 하는 Azure 지역의 이름입니다. |
| esUserName |ES 클러스터에 액세스하도록 구성할 사용자의 사용자 이름입니다(HTTP 기본 인증에 따라 달라짐). 암호는 매개 변수 파일의 일부가 아니며 `CreateElasticSearchCluster` 스크립트가 호출될 때 제공됩니다. |
| vmSizeDataNodes |Elasticsearch 클러스터 노드에 대한 Azure 가상 컴퓨터 크기입니다. 기본값은 Standard_D2입니다. |

이제 스크립트를 실행할 준비가 되었습니다. 다음 명령을 실행합니다.

```powershell
CreateElasticSearchCluster -ResourceGroupName <es-group-name> -Region <azure-region> -EsPassword <es-password>
```

여기서, 

| 매개 변수 이름 | 설명 |
| --- | --- |
| `<es-group-name>` |모든 Elasticsearch 클러스터 리소스를 포함하는 Azure 리소스 그룹의 이름입니다. |
| `<azure-region>` |Elasticsearch 클러스터를 만들어야 하는 Azure 지역의 이름입니다. |
| `<es-password>` |Elasticsearch 사용자의 암호입니다. |

> [!NOTE]
> Test-AzureResourceGroup cmdlet에서 NullReferenceException을 받는 경우 Azure에 로그온하는 것을 잊은 것입니다(`Add-AzureRmAccount`).
> 
> 

스크립트 실행에서 오류가 발생하고 잘못된 템플릿 매개 변수 값으로 오류가 발생한 것을 결정하는 경우 매개 변수 파일을 수정하고 다른 리소스 그룹 이름으로 스크립트를 다시 실행합니다. 동일한 리소스 그룹 이름을 다시 사용하고 `-RemoveExistingResourceGroup` 매개 변수를 스크립트 호출에 추가하여 스크립트에서 이전 것을 정리할 수 있습니다.

### <a name="result-of-running-the-createelasticsearchcluster-script"></a>CreateElasticSearchCluster 스크립트 실행 결과
`CreateElasticSearchCluster` 스크립트를 실행한 후 다음과 같은 주요 아티팩트가 생성됩니다. 이 예제에서는 `dnsNameForLoadBalancerIP` 매개 변수의 값에 대해 "myBigCluster"를 사용했고 클러스터를 만든 지역을 미국 서부라고 가정합니다.

| 아티팩트 | 이름, 위치 및 설명 |
| --- | --- |
| 원격 관리를 위한 SSH 키 |myBigCluster.key 파일(CreateElasticSearchCluster가 실행된 디렉터리에 있음). <br /><br />이 키 파일을 클러스터의 데이터 노드에 관리자 노드를 연결하는 데(관리자 노드를 통해) 사용할 수 있습니다. |
| 관리 노드 |myBigCluster-admin.westus.cloudapp.azure.com  <br /><br />원격 Elasticsearch 클러스터 관리를 위한 외부 SSH 연결을 허용하는 유일한 전용 VM입니다. 모든 Elasticsearch 클러스터 노드와 동일한 가상 네트워크에서 실행되지만 Elasticsearch 서비스를 실행하지 않습니다. |
| 데이터 노드 |myBigCluster1 … myBigCluster*N* <br /><br />Elasticsearch 및 Kibana 서비스를 실행하는 데이터 노드입니다. 각 노드에 SSH를 통해 연결할 수 있지만 관리자 노드를 통해서만 연결할 수 있습니다. |
| Elasticsearch 클러스터 |http://myBigCluster.westus.cloudapp.azure.com/es/ <br /><br />Elasticsearch 클러스터에 대한 기본 끝점입니다(/es 접미사). 기본 HTTP 인증으로 보호됩니다. 자격 증명은 ES-MultiNode 템플릿의 esUserName/esPassword 매개 변수로 지정됩니다. 클러스터에는 기본 클러스터 관리를 위한 헤드 플러그인(http://myBigCluster.westus.cloudapp.azure.com/es/_plugin/head)도 설치됩니다. |
| Kibana 서비스 |http://myBigCluster.westus.cloudapp.azure.com <br /><br />만든 Elasticsearch 클러스터에 데이터가 표시되도록 Kibana 서비스가 설정되었습니다. 클러스터 자체와 동일한 인증 자격 증명으로 보호됩니다. |

## <a name="in-process-versus-out-of-process-trace-capturing"></a>In-proces와 out-of-process 추적 캡처 비교
소개 부분에서 진단 데이터 수집의 두 가지 기본 방법(In Process 및 Out of Process)을 언급했습니다. 각각은 강점 및 약점이 있습니다.

다음은 **in-process 추적 캡처** 의 장점입니다.

1. *쉬운 구성 및 배포*
   
   * 진단 데이터 수집 구성은 응용 프로그램 구성의 일부입니다. 응용 프로그램 나머지와 항상 "동기화" 상태로 쉽게 유지할 수 있습니다.
   * 응용 프로그램당 또는 서비스당 구성은 쉽게 달성할 수 있습니다.
   * Out of Proces 추적 캡처는 일반적으로 추가 관리 작업 및 잠재적인 오류 소스인 진단 에이전트의 별도 배포 및 구성이 필요합니다. 특정 에이전트 기술은 가상 컴퓨터(노드) 당 하나의 에이전트 인스턴스만 허용하는 경우가 종종 있습니다. 이는 진단 구성 수집에 대한 구성이 해당 노드에서 실행되는 모든 응용 프로그램 및 서비스에서 공유된다는 것입니다.
2. *유연성*
   
   * 응용 프로그램은 대상 데이터 저장소 시스템을 지원하는 클라이언트 라이브러리가 있는 한 데이터를 필요한 어느 곳이든 보낼 수 있습니다. 새로운 싱크를 원하는 대로 추가할 수 있습니다.
   * 복잡한 캡처, 필터링 및 데이터 집계 규칙을 구현할 수 있습니다.
   * out-of-process 추적 캡처는 종종 에이전트가 지원하는 데이터 싱크에 의해 제한됩니다. 일부 에이전트는 확장할 수 있습니다.
3. *내부 응용 프로그램 데이터 및 컨텍스트에 대한 액세스*
   
   * 응용 프로그램/서비스 프로세스 내에서 실행되는 진단 하위 시스템은 컨텍스트 정보와 함께 추적을 쉽게 보강할 수 있습니다.
   * Out of Process 방식에서 ETW(Windows용 이벤트 추적)와 같은 일부 프로세스간 통신 메커니즘을 통해 에이전트에 데이터를 보내야 합니다. 이 메커니즘에 따라 추가 제한 사항이 적용될 수 있습니다.

다음은 **out-of-process 추적 캡처** 의 장점입니다.

1. *응용 프로그램을 모니터링하고 크래시 덤프를 수집하는 기능*
   
   * 응용 프로그램이 시작하지 못하거나 충돌하는 경우 In Process 추적 캡처는 실패할 수 있습니다. 독립 에이전트는 중요한 문제 해결 정보를 캡처할 가능성이 훨씬 큽니다.<br /><br />
2. *완성도, 견고성 및 검증된 성능*
   
   * 플랫폼 공급업체(예: Microsoft Azure 진단 에이전트)에서 개발한 에이전트는 엄격한 테스트 및 강화를 받습니다.
   * In Process 추적 캡처로 응용 프로그램 프로세스의 진단 데이터 전송 작업이 응용 프로그램 주요 작업을 방해하거나 타이밍 또는 성능 문제가 발생하지 않도록 처리되어야 합니다. 독립적으로 실행 중인 에이전트는 이러한 문제가 발생할 가능성이 작고 시스템에 해당 영향을 제한하도록 특별히 설계되었습니다.

두 가지 접근법을 결합하여 양쪽의 이점을 누릴 수도 있습니다. 실제로 많은 응용 프로그램에는 이 방법이 최적의 솔루션일 수 있습니다.

여기에서 **Microsoft.Diagnostic.Listeners 라이브러리** 및 In Process 추적 캡처를 사용하여 서비스 패브릭 응용 프로그램에서 Elasticsearch 클러스터에 데이터를 보냅니다.

## <a name="use-the-listeners-library-to-send-diagnostic-data-to-elasticsearch"></a>수신기 라이브러리를 사용하여 Elasticsearch에 진단 데이터 보내기
Microsoft.Diagnostic.Listeners 라이브러리는 PartyCluster 샘플 서비스 패브릭 응용 프로그램의 일부입니다. 이를 사용하려면:

1. GitHub에서 [PartyCluster 샘플](https://github.com/Azure-Samples/service-fabric-dotnet-management-party-cluster) 을 다운로드합니다.
2. PartyCluster 샘플 디렉터리에서 Microsoft.Diagnostics.Listeners 및 Microsoft.Diagnostics.Listeners.Fabric 프로젝트(전체 폴더)를 데이터를 Elasticsearch에 전송하는 응용 프로그램의 솔루션 폴더에 복사합니다.
3. 대상 솔루션을 열고 솔루션 탐색기에서 솔루션 노드를 마우스 오른쪽 단추로 클릭하고 **기존 프로젝트 추가**를 선택합니다. Microsoft.Diagnostics.Listeners 프로젝트를 솔루션에 추가합니다. Microsoft.Diagnostics.Listeners.Fabric 프로젝트에 대해 동일한 절차를 반복합니다.
4. 서비스 프로젝트의 프로젝트 참조를 두 개의 추가된 프로젝트에 추가합니다. (데이터를 Elasticsearch에 전송하는 각 서비스는 Microsoft.Diagnostics.EventListeners 및 Microsoft.Diagnostics.EventListeners.Fabric을 참조해야 합니다.)
   
    ![Microsoft.Diagnostics.EventListeners 및 Microsoft.Diagnostics.EventListeners.Fabric 라이브러리에 대한 프로젝트 참조][1]

### <a name="service-fabric-general-availability-release-and-microsoftdiagnosticstracing-nuget-package"></a>서비스 패브릭 일반 공급 릴리스 및 Microsoft.Diagnostics.Tracing Nuget 패키지
서비스 패브릭 일반 공급 릴리스(2.0.135, 2016년 3월 31일 출시)를 사용하여 빌드한 응용 프로그램은 **.NET Framework 4.5.2**를 대상으로 합니다. 이 버전은 GA 릴리스 시 Azure에서 지원하는 가장 높은 버전의 .NET Framework입니다. 아쉽게도 이 버전의 프레임워크는 Microsoft.Diagnostics.Listeners 라이브러리에서 필요한 특정 EventListener API가 부족합니다. EventSource(패브릭 응용 프로그램에서 API 로깅의 기본을 형성하는 구성 요소) 및 EventListener는 강력하게 결합되므로 Microsoft.Diagnostics.Listeners 라이브러리를 사용하는 모든 프로젝트는 EventSource의 대체 구현을 사용해야 합니다. 이 구현은 Microsoft에서 작성한 **Microsoft.Diagnostics.Tracing Nuget 패키지**에서 제공됩니다. 패키지는 프레임워크에 포함된 이전 버전인 EventSource와 완벽하게 호환되므로 참조된 네임스페이스 변경 이외의 다른 코드 변경이 필요하지 않습니다.

EventSource 클래스의 Microsoft.Diagnostics.Tracing 구현을 사용하여 시작하려면 Elasticsearch에 데이터를 보내야 하는 각 서비스 프로젝트에 대해 이 단계를 따릅니다.

1. 서비스 프로젝트를 마우스 오른쪽 단추로 클릭하고 **Nuget 패키지 관리**를 선택합니다.
2. nuget.org 패키지 원본으로 전환하고(선택되지 않은 경우) "**Microsoft.Diagnostics.Tracing**"을 검색합니다.
3. `Microsoft.Diagnostics.Tracing.EventSource` 패키지 및 해당 종속성을 설치합니다.
4. 서비스 프로젝트에서 **ServiceEventSource.cs** 또는 **ActorEventSource.cs** 파일을 열고 파일 상단의 `using System.Diagnostics.Tracing` 지시문을 `using Microsoft.Diagnostics.Tracing` 지시문으로 대체합니다.

이러한 단계는 **.NET Framework 4.6** 이 Microsoft Azure에서 지원되면 필요하지 않습니다.

### <a name="elasticsearch-listener-instantiation-and-configuration"></a>Elasticsearch 수신기 인스턴스화 및 구성
진단 데이터를 Elasticsearch에 보내는 마지막 단계는 `ElasticSearchListener` 의 인스턴스를 만들고 Elasticsearch 연결 데이터와 구성하는 것입니다. 수신기는 서비스 프로젝트에 정의된 EventSource 클래스를 통해 발생한 모든 이벤트를 자동으로 캡처합니다. 서비스 수명 동안 유지해야 하므로 서비스 초기화 코드에서 만드는 것이 적합합니다. 상태 비저장 서비스에 대한 초기화 코드는 필요한 변경 후 다음과 같이 보입니다( `****`(으)로 시작하는 주석에서 지적한 추가).

```csharp
using System;
using System.Diagnostics;
using System.Fabric;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.ServiceFabric.Services.Runtime;

// **** Add the following directives
using Microsoft.Diagnostics.EventListeners;
using Microsoft.Diagnostics.EventListeners.Fabric;

namespace Stateless1
{
    internal static class Program
    {
        /// <summary>
        /// This is the entry point of the service host process.
        /// </summary>        
        private static void Main()
        {
            try
            {
                // **** Instantiate ElasticSearchListener
                var configProvider = new FabricConfigurationProvider("ElasticSearchEventListener");
                ElasticSearchListener esListener = null;
                if (configProvider.HasConfiguration)
                {
                    esListener = new ElasticSearchListener(configProvider, new FabricHealthReporter("ElasticSearchEventListener"));
                }

                // The ServiceManifest.XML file defines one or more service type names.
                // Registering a service maps a service type name to a .NET type.
                // When Service Fabric creates an instance of this service type,
                // an instance of the class is created in this host process.

                ServiceRuntime.RegisterServiceAsync("Stateless1Type", 
                    context => new Stateless1(context)).GetAwaiter().GetResult();

                ServiceEventSource.Current.ServiceTypeRegistered(Process.GetCurrentProcess().Id, typeof(Stateless1).Name);

                // Prevents this host process from terminating so services keep running.
                Thread.Sleep(Timeout.Infinite);

                // **** Ensure that the ElasticSearchListner instance is not garbage-collected prematurely
                GC.KeepAlive(esListener);
            }
            catch (Exception e)
            {
                ServiceEventSource.Current.ServiceHostInitializationFailed(e);
                throw;
            }
        }
    }
}
```

Elasticsearch 연결 데이터는 서비스 구성 파일(**PackageRoot\Config\Settings.xml**)에서 별도 섹션에 넣어야 합니다. 섹션의 이름은 `FabricConfigurationProvider` 생성자에 전달된 값에 해당해야 합니다. 예는 다음과 같습니다.

```xml
<Section Name="ElasticSearchEventListener">
  <Parameter Name="serviceUri" Value="http://myBigCluster.westus.cloudapp.azure.com/es/" />
  <Parameter Name="userName" Value="(ES user name)" />
  <Parameter Name="password" Value="(ES user password)" />
  <Parameter Name="indexNamePrefix" Value="myapp" />
</Section>
```
`serviceUri`, `userName` 및 `password` 매개 변수 값은 각각 Elasticsearch 클러스터 끝점 주소, Elasticsearch 사용자 이름 및 암호에 해당합니다. `indexNamePrefix`는 Elasticsearch 인덱스의 접두사입니다. Microsoft.Diagnostics.Listeners 라이브러리는 매일 해당 데이터에 대한 새 인덱스를 만듭니다.

### <a name="verification"></a>확인
이것으로 끝입니다. 이제 서비스가 실행될 때마다 구성에 지정된 Elasticsearch 서비스에 추적을 보내기 시작합니다. 대상 Elasticsearch 인스턴스와 연결된 Kibana UI를 열어 이 작업을 확인할 수 있습니다. 이 예제에서는 페이지 주소가 http://myBigCluster.westus.cloudapp.azure.com/입니다. `ElasticSearchListener` 인스턴스에 대해 선택한 이름 접두사를 포함하는 인덱스가 실제로 만들어졌으며 데이터로 채워졌는지 확인합니다.

![PartyCluster 응용 프로그램 이벤트를 보여 주는 Kibana][2]

## <a name="next-steps"></a>다음 단계
* [서비스 패브릭 서비스 진단 및 모니터링에 대한 자세한 정보](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

<!--Image references-->
[1]: ./media/service-fabric-diagnostics-how-to-use-elasticsearch/listener-lib-references.png
[2]: ./media/service-fabric-diagnostics-how-to-use-elasticsearch/kibana.png

