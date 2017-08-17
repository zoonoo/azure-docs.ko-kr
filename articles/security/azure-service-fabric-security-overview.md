---
title: "Azure Service Fabric 보안 개요 | Microsoft Docs"
description: "이 문서는 Azure Service Fabric 보안의 개요를 제공합니다."
services: security
documentationcenter: na
author: unifycloud
manager: swadhwa
editor: tomsh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/04/2017
ms.author: tomsh
ms.translationtype: HT
ms.sourcegitcommit: 0aae2acfbf30a77f57ddfbaabdb17f51b6938fd6
ms.openlocfilehash: 0445df244848469cc2071681bec4b8f4c5fceeac
ms.contentlocale: ko-kr
ms.lasthandoff: 08/09/2017

---
# <a name="azure-service-fabric-security-overview"></a>Azure Service Fabric 보안 개요
[Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-overview)은 손쉽게 패키지하고 배포하며 확장 가능하고 안정성이 뛰어난 마이크로 서비스를 관리하는 분산된 시스템 플랫폼입니다. 또한 서비스 패브릭은 클라우드 응용 프로그램 개발 및 관리에서 발생하는 중요한 과제를 해결합니다. 개발자와 관리자가 복잡한 인프라 문제를 피하고 업무 수행에 필수적인 까다로운 워크로드를 확장 가능하고 신뢰할 수 있으며 관리가 가능하도록 구현하는 데 집중할 수 있습니다.

이 Azure Service Fabric 보안 개요 문서는 다음 영역에 중점을 둡니다.

-   클러스터 보안 설정
-   모니터링 및 진단
-   인증서를 사용한 보안
-   RBAC(역할 기반 액세스 제어)
-   Windows 보안을 사용한 클러스터 보안
-   Service Fabric에서 응용 프로그램 보안 구성
-   Azure Service Fabric 보안에서 서비스에 대한 통신 보호 

## <a name="securing-your-cluster"></a>클러스터 보안 설정
Azure Service Fabric은 컴퓨터 클러스터 전체의 서비스를 조정하며, 실행 중인 프로덕션 작업이 있을 때 권한 없는 사용자가 클러스터에 연결하지 못하도록 하여 클러스터의 보안을 유지해야 합니다. 보안이 되지 않은 클러스터를 만들 수 있지만, 이렇게 하면 공용 인터넷에 관리 끝점이 노출될 경우 익명 사용자가 클러스터에 연결할 수 있게 됩니다.

이 섹션에서는 Azure 또는 독립 실행형에서 실행되는 클러스터에 대한 보안 시나리오의 개요와 이러한 시나리오를 구현하는 데 사용되는 다양한 기술에 대해 설명합니다. 클러스터 보안 시나리오:

-   노드 간 보안
-   클라이언트-노드 보안

### <a name="node-to-node-security"></a>노드 간 보안
클러스터의 VM 또는 컴퓨터 간 통신을 보호합니다. 이렇게 하면 클러스터에 가입하도록 인증된 컴퓨터만 호스팅 응용 프로그램 및 클러스터의 서비스에 참여할 수 있습니다.

Azure에서 실행되는 클러스터 또는 Windows에서 실행되는 독립 실행형 클러스터는 Windows Server 컴퓨터에 대한 [인증서 보안](https://msdn.microsoft.com/library/ff649801.aspx) 또는 [Windows 보안](https://msdn.microsoft.com/library/ff649396.aspx)을 사용할 수 있습니다.

**노드 간 인증서 보안**

서비스 패브릭은 클러스터를 만들 때 노드 유형 구성의 일부로 지정하는 X.509 서버 인증서를 사용합니다. 증서 정보 및 [인증서 획득 또는 생성 방법](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/working-with-certificates)에 대한 빠른 개요는 이 문서에서 제공됩니다.

Azure Portal, Azure Resource Manager 템플릿 또는 독립 실행형 JSON 템플릿을 통해 클러스터를 만드는 동안 인증서 보안이 구성됩니다. 기본 인증서 및 인증서 롤오버에 사용되는 선택적 보조 인증서를 지정할 수 있습니다. 지정한 기본 및 보조 인증서는 관리 클라이언트 및 [클라이언트-노드 보안](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-cluster-security)에 대해 지정한 읽기 전용 클라이언트 인증서와 다릅니다.

### <a name="client-to-node-security"></a>클라이언트-노드 보안
클라이언트-노드 보안은 클라이언트 ID를 사용하여 구성합니다. 클라이언트와 클러스터 간에 트러스트를 설정하기 위해 신뢰할 수 있는 클라이언트 ID를 파악하도록 클러스터를 구성해야 합니다. 이 작업은 두 가지 방법으로 수행할 수 있습니다.

-   연결할 수 있는 도메인 그룹 사용자를 지정합니다.
-   연결할 수 있는 도메인 노드 사용자를 지정합니다.

Service Fabric은 Service Fabric 클러스터에 연결된 클라이언트에 대해 두 가지 액세스 제어 형식을 지원합니다.

-   관리자
-   사용자

액세스 제어는 클러스터 관리자가 사용자 그룹마다 특정 형식의 클러스터 작업에 대한 액세스를 제한하는 기능을 제공하여 클러스터의 보안을 강화할 수 있습니다. 관리자는 관리 기능(읽기/쓰기 기능만 포함)에 대한 모든 권한을 가집니다. 사용자는 기본적으로 관리 기능(예: 쿼리 기능)에 대한 읽기 권한 및 응용 프로그램과 서비스를 확인하는 기능만 갖습니다.

**클라이언트-노드 인증서 보안**

클라이언트-노드 인증서 보안은 관리 클라이언트 인증서 및/또는 사용자 클라이언트 인증서를 지정하여 Azure Portal, Resource Manager 템플릿 또는 독립 실행형 JSON 템플릿을 통해 클러스터를 만드는 동안 구성됩니다. 지정한 관리 클라이언트 인증서 및 사용자 클라이언트 인증서는 노드 간 보안에 대해 지정한 기본 및 보조 인증서와 다릅니다.

관리 인증서를 사용하여 클러스터에 연결하는 클라이언트에는 관리 기능에 대한 모든 권한이 있습니다. 읽기 전용 사용자 클라이언트 인증서를 사용하여 클러스터에 연결하는 클라이언트는 관리 기능에 대한 읽기 전용 액세스 권한만 있습니다. 즉, 이러한 인증서는 역할 기반 액세스 제어(RBAC)에 사용됩니다.

Azure의 경우 클러스터에서 인증서 보안을 구성하는 방법을 알아보려면 [Azure Resource Manager 템플릿을 사용하여 클러스터 설정](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) 을 참조하세요.

**Azure에서 클라이언트-노드 AAD(Azure Active Directory) 보안**

Azure에서 실행되는 클라이언트는 AAD(Azure Active Directory)를 사용하여 관리 끝점에 안전하게 액세스할 수 있습니다. 필요한 AAD 아티팩트를 만드는 방법, 클러스터를 만들 때 이러한 아티팩트를 채우는 방법 및 나중에 해당 클러스터에 연결하는 방법에 대한 자세한 내용은 [Azure Resource Manager 템플릿을 사용하여 클러스터 설정](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) 을 참조하세요.

AAD를 통해 조직(테넌트라고도 함)에서 웹 기반 로그인 UI를 사용하는 응용 프로그램과 네이티브 클라이언트 환경을 사용하는 응용 프로그램으로 구분되는 응용 프로그램에 대한 사용자 액세스를 관리할 수 있습니다.

Service Fabric 클러스터는 웹 기반 Service Fabric Explorer 및 Visual Studio를 포함하여 관리 기능에 대한 여러 진입점을 제공합니다. 결과적으로 두 개의 AAD 응용 프로그램(웹 응용 프로그램과 네이티브 응용 프로그램)을 만들어 클러스터에 대한 액세스를 제어합니다.
Azure 클러스터의 경우 AAD 보안을 사용하여 노드 간 보안에 대해 클라이언트 및 인증서를 인증하는 것이 좋습니다.

독립 실행형 Windows Server 클러스터의 경우 Windows Server 2012 R2 및 Active Directory가 있는 경우 GMA(그룹 관리 계정)로 Windows 보안을 사용하는 것이 좋습니다. 그렇지 않은 경우 여전히 Windows 계정으로 Windows 보안을 사용합니다.

## <a name="monitoring-and-diagnostics-for-azure-service-fabric"></a>Azure Service Fabric 모니터링 및 진단
[모니터링 및 진단](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-overview)은 모든 환경에서 응용 프로그램 및 서비스를 개발, 테스트 및 배포하는 데 중요합니다. Service Fabric 솔루션은 로컬 개발 환경 또는 프로덕션 환경에서 응용 프로그램과 서비스가 예상대로 작동하는지 확인하는 데 도움이 되는 모니터링 및 진단을 계획하고 구현할 때 가장 효과적입니다.

보안의 관점에서 볼 때 모니터링 및 진단의 주요 목표는 다음과 같습니다.

-   보안 이벤트에 기인한 것일 수 있는 하드웨어 및 인프라 문제를 검색 및 진단합니다.
-   손상의 지표(IoC)를 제공할 수 있는 소프트웨어와 앱 문제를 검색합니다.
-   우발적인 서비스 거부를 방지할 수 있게 리소스 사용량을 파악합니다.

모니터링 및 진단의 전체 워크플로는 다음 세 단계로 구성됩니다.

-   **이벤트 생성**: 인프라(클러스터) 및 응용 프로그램/서비스 수준의 이벤트(로그, 추적, 사용자 지정 이벤트)가 모두 포함됩니다. 제공되는 계측 및 계측을 추가하는 방법은 [인프라 수준 이벤트](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-infra) 및 [응용 프로그램 수준 이벤트](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-app)를 참조하세요.
-   **이벤트 집계**: 생성된 이벤트를 표시하려면 먼저 수집하고 집계해야 합니다. 일반적으로 [Azure 진단](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-aggregation-wad)(에이전트 기반 로그 수집과 더 유사) 또는 [EventFlow](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-aggregation-eventflow)(프로세스 내 로그 수집)를 사용하는 것이 좋습니다.
-   **분석**: 필요에 따라 분석하고 표시할 수 있도록 이벤트를 특정 형식으로 시각화하고 액세스할 수 있어야 합니다. 모니터링 및 진단 데이터의 분석 및 시각화와 관련하여 시장에 몇 가지 훌륭한 플랫폼이 나와 있습니다. 권장되는 두 가지는 Service Fabric과 더 잘 통합되는 [OMS](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-analysis-oms) 및 [Application Insights](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-analysis-appinsights)입니다.

[Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview)로도 Service Fabric 클러스터의 기반이 되는 다양한 Azure 리소스를 모니터링할 수 있습니다.

Watchdog은 서비스 전체에서 상태와 부하를 감시하고 상태 모델 계층 구조 상의 모든 항목에 대한 상태를 보고할 수 있는 별도의 서비스입니다. 이렇게 하면 단일 서비스 보기를 기반으로 하여 감지되지 않는 오류를 방지할 수 있습니다. Watchdog은 사용자 상호 작용 없이 교정 작업을 수행하는 코드(예: 특정 시간 간격으로 저장소의 로그 파일 정리)를 호스트하는 데 적합합니다. [여기](https://azure.microsoft.com/resources/samples/service-fabric-watchdog-service/)에서 샘플 watchdog 서비스 구현을 찾을 수 있습니다.

## <a name="secure-using-certificates"></a>인증서를 사용한 보안
인증서를 사용하면 독립 실행형 Windows 클러스터의 다양한 노드 간에 통신을 보호하는 방법 뿐만 아니라 X.509 인증서를 사용하여 이 클러스터에 연결된 클라이언트를 인증할 수 있습니다. 이렇게 하면 권한이 있는 사용자만 클러스터 및 배포된 응용 프로그램에 액세스할 수 있으며 관리 작업을 수행할 수 있습니다. 인증서 보안은 클러스터가 만들어지기 전에 클러스터에서 사용되어야 합니다.

### <a name="x509-certificates-and-service-fabric"></a>X.509 인증서 및 서비스 패브릭
X.509 디지털 인증서는 클라이언트 및 서버를 인증하고 암호화하고 디지털로 메시지를 서명하는 데 일반적으로 사용됩니다.

다음 테이블에서는 클러스터 설치에 필요한 인증서를 나열합니다.

|인증서 정보 설정 |설명|
|-------------------------------|-----------|
|ClusterCertificate|    이 인증서는 클러스터에서 노드 간의 통신을 보호해야 합니다. 업그레이드에 두 개의 다른 인증서, 기본 및 보조 인증서를 사용할 수 있습니다.|
|ServerCertificate| 이 인증서가 클러스터에 연결하려고 시도할 때 클라이언트에 표시됩니다. 업그레이드에 두 개의 다른 서버 인증서, 기본 및 보조 인증서를 사용할 수 있습니다.|
|ClientCertificateThumbprints|  인증된 클라이언트에 설치하려는 인증서의 집합입니다.|
|ClientCertificateCommonNames|  CertificateCommonName에 대한 첫 번째 클라이언트 인증서의 일반 이름을 설정합니다. CertificateIssuerThumbprint는 이 인증서의 발급자 지문입니다.|
|ReverseProxyCertificate|   이는 [역방향 프록시](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy)를 보호하려는 경우 지정될 수 있는 선택적인 인증서입니다.|

인증서 보호에 대한 자세한 내용은 [여기를 클릭](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security)합니다.

## <a name="role-based-access-control-rbac"></a>RBAC(역할 기반 액세스 제어)
액세스 제어를 사용하면 클러스터 관리자가 사용자 그룹마다 특정 클러스터 작업에 대한 액세스를 제한하여 클러스터의 보안을 강화할 수 있습니다. 클러스터에 연결하는 클라이언트에 대해 두 가지 액세스 제어 유형인 관리자 역할 및 사용자 역할이 지원됩니다.

관리자는 관리 기능(읽기/쓰기 기능만 포함)에 대한 모든 권한을 가집니다. 사용자는 기본적으로 관리 기능(예: 쿼리 기능)에 대한 읽기 권한 및 응용 프로그램과 서비스를 확인하는 기능만 갖습니다.

클러스터를 만들 때 각각에 대해 개별 ID(인증서, AAD 등)를 제공하여 관리자 및 사용자 클라이언트 역할을 지정합니다. 기본 액세스 제어 설정 및 기본 설정을 변경하는 방법에 대한 자세한 내용은 [Service Fabric 클라이언트에 대한 역할 기반 액세스 제어](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-roles)를 참조하세요.

## <a name="secure-standalone-cluster-using-windows-security"></a>Windows 보안을 사용하여 독립 실행형 클러스터 보안
Service Fabric 클러스터에 대한 무단 액세스를 방지하려면 클러스터를 보호해야 합니다. 클러스터에서 프로덕션 작업을 실행하는 경우 특히 보안이 중요합니다. ClusterConfig.JSON 파일에서 Windows 보안을 사용하여 노드 간 및 클라이언트-노드 간 보안을 구성하는 방법을 설명합니다.

**gMSA를 사용하여 Windows 보안 구성**

노드 간 보안은 서비스 패브릭이 gMSA에서 실행되어야 하는 경우 [ClustergMSAIdentity](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-windows-security)를 설정하여 구성됩니다. 노드 간의 신뢰 관계를 구축하기 위해 서로를 인식하도록 만들어야 합니다.

클라이언트-노드 보안은 ClientIdentities를 사용하여 구성합니다. 클라이언트와 클러스터 간에 트러스트를 설정하기 위해 신뢰할 수 있는 클라이언트 ID를 파악하도록 클러스터를 구성해야 합니다.

**컴퓨터 그룹을 사용하여 Windows 보안 구성**

Active Directory 도메인 내에서 컴퓨터 그룹을 사용하려는 경우 노드 간 보안은 ClusterIdentity 사용을 설정하여 구성됩니다. 자세한 내용은 [Active Directory에 컴퓨터 그룹 만들기](https://msdn.microsoft.com/library/aa545347)를 참조하세요.

클라이언트-노드 보안은 클라이언트 ID를 사용하여 구성됩니다. 클라이언트와 클러스터 간에 트러스트를 설정하기 위해 클라이언트가 신뢰할 수 있는 클라이언트 ID를 파악하도록 클러스터를 구성해야 합니다. 다음 두 가지 방법으로 트러스트를 설정할 수 있습니다.

-   연결할 수 있는 도메인 그룹 사용자를 지정합니다.
-   연결할 수 있는 도메인 노드 사용자를 지정합니다.

## <a name="configure-application-security-in-service-fabric"></a>Service Fabric에서 응용 프로그램 보안 구성
### <a name="managing-secrets-in-service-fabric-applications"></a>서비스 패브릭 응용 프로그램의 비밀 관리
이 메서드는 Service Fabric 응용 프로그램에서의 암호 관리에 도움이 됩니다. 저장소 연결 문자열, 암호, 일반 텍스트로 처리하면 안 되는 값 등 모든 민감한 정보를 비밀로 처리할 수 있습니다.

이 방법에서는 [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis)를 사용하여 키와 암호를 관리합니다. 그러나 응용 프로그램에서의 암호 사용은 클라우드 플랫폼에 구애를 받지 않으므로 호스팅 위치에 관계없이 클러스터에 응용 프로그램을 배포할 수 있습니다. 이 흐름은 주요 네 단계로 구성됩니다.

-   데이터 암호화 인증서를 가져옵니다.
-   클러스터에 인증서를 설치합니다.
-   인증서를 사용하여 응용 프로그램을 배포할 때 비밀 값을 암호화하여 서비스의 Settings.xml 구성 파일에 삽입합니다.
-   동일한 암호화 인증서로 암호를 해독하여 Settings.xml 파일에서 암호화된 값을 읽습니다.

>[!Note]
>[Service Fabric 응용 프로그램의 암호 관리](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management)에 대해 자세히 알아보세요.

### <a name="configure-security-policies-for-your-application"></a>응용 프로그램에 대한 보안 정책 구성
Azure Service Fabric 보안을 사용하여 다른 사용자 계정으로 클러스터에서 실행 중인 응용 프로그램을 보호할 수 있습니다. 또한 Service Fabric 보안은 배포 시 파일, 디렉터리, 인증서 등과 같은 사용자 계정을 통해 응용 프로그램에서 사용하는 리소스도 보호합니다. 따라서 공유되는 호스티드 환경에서도 서로 더욱 안전하게 응용 프로그램을 실행할 수 있습니다.
단계는 다음과 같습니다.

-   서비스 설치 진입점에 대한 정책 구성
-   설치 진입점에서 PowerShell 명령 시작
-   로컬 디버깅에 대한 콘솔 리디렉션 사용
-   서비스 코드 패키지에 대한 정책 구성
-   HTTP 및 HTTPS 끝점에 보안 액세스 정책 할당

## <a name="secure-communication-for-services-in-azure-service-fabric-security"></a>Azure Service Fabric 보안에서 서비스에 대한 통신 보호 
통신의 가장 중요한 측면 중 하나는 보안입니다. Reliable Services 응용 프로그램 프레임워크는 보안을 향상시키는 데 사용할 수 있는 미리 빌드된 통신 스택 및 도구 몇 가지를 제공합니다.

-   [서비스 원격 기능을 사용하는 경우 서비스 보호 도움말](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-secure-communication)
-   [WCF 기반 통신 스택을 사용하는 경우 서비스 보호 방법](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-secure-communication#help-secure-a-service-when-youre-using-a-wcf-based-communication-stack)

## <a name="next-steps"></a>다음 단계
- 클러스터 보안에 대한 개념 정보는 [Resource Manager 템플릿을 사용하여 Azure에 클러스터 만들기](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) 및 [Azure Portal](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-portal)을 참조하세요.
- 자세한 내용은 [Service Fabric 클러스터 보안](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security)을 참조하세요.

