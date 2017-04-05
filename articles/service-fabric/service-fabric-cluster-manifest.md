---
title: "독립 실행형 클러스터 구성 | Microsoft Docs"
description: "이 문서에서는 독립 실행형 또는 개인 Service Fabric 클러스터를 구성하는 방법을 설명합니다."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 0c5ec720-8f70-40bd-9f86-cd07b84a219d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/17/2017
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: b4802009a8512cb4dcb49602545c7a31969e0a25
ms.openlocfilehash: 8192f9e36ebadd41d93ec3c2fa61b05e342d5bc1
ms.lasthandoff: 03/29/2017


---
# <a name="configuration-settings-for-standalone-windows-cluster"></a>독립 실행형 Windows 클러스터에 대한 구성 설정
이 문서에서는 ***ClusterConfig.JSON*** 파일을 사용하여 독립 실행형 Service Fabric 클러스터를 구성하는 방법을 설명합니다. 이 파일을 사용하여 독립 실행형 클러스터에 대한 Service Fabric 노드 및 해당 IP 주소, 클러스터의 다른 노드 형식, 보안 구성에 대한 정보와 장애/업그레이드 도메인과 관련된 네트워크 토폴로지에 대한 정보를 지정할 수 있습니다.

[독립 실행형 Service Fabric 패키지를 다운로드](service-fabric-cluster-creation-for-windows-server.md#downloadpackage)하면 ClusterConfig.JSON 파일의 몇 가지 샘플이 작업 컴퓨터에 다운로드됩니다. 이름에 *DevCluster*가 있는 샘플은 논리 노드처럼 세 노드가 모두 동일한 컴퓨터에 있는 클러스터를 만드는 데 도움이 됩니다. 세 노드 중 하나 이상은 주 노드로 표시되어야 합니다. 이 클러스터는 개발 또는 테스트 환경에 유용하며, 프로덕션 클러스터로 지원되지 않습니다. 이름에 *MultiMachine*이 있는 샘플은 각 노드가 별도의 컴퓨터에 있는 프로덕션 품질 클러스터를 만드는 데 도움이 됩니다. 이러한 클러스터의 주 노드 수는 [안정성 수준](#reliability)에 따라 결정됩니다.

1. *ClusterConfig.Unsecure.DevCluster.JSON* 및 *ClusterConfig.Unsecure.MultiMachine.JSON*은 각각 보안되지 않은 테스트 또는 프로덕션 클러스터를 만드는 방법을 보여 줍니다. 
2. *ClusterConfig.Windows.DevCluster.JSON* 및 *ClusterConfig.Windows.MultiMachine.JSON*은 [Windows 보안](service-fabric-windows-cluster-windows-security.md)을 사용하여 보안이 유지되는 테스트 또는 프로덕션 클러스터를 만드는 방법을 보여 줍니다.
3. *ClusterConfig.X509.DevCluster.JSON* 및 *ClusterConfig.X509.MultiMachine.JSON*은 [X509 인증서 기반 보안](service-fabric-windows-cluster-x509-security.md)을 사용하여 보안이 유지되는 테스트 또는 프로덕션 클러스터를 만드는 방법을 보여 줍니다. 

이제 아래와 같이 ***ClusterConfig.JSON*** 파일의 여러 섹션을 검토해 보겠습니다.

## <a name="general-cluster-configurations"></a>일반 클러스터 구성
여기서는 아래의 JSON 코드 조각에 표시된 광범위한 클러스터별 구성을 살펴봅니다.

    "name": "SampleCluster",
    "clusterConfigurationVersion": "1.0.0",
    "apiVersion": "2016-09-26",

Service Fabric 클러스터를 **name** 변수에 할당하여 이름 지정할 수 있습니다. **clusterConfigurationVersion**은 클러스터의 버전 번호이며, Service Fabric 클러스터를 업그레이드할 때마다 증가해야 합니다. 그러나 **apiVersion**은 기본값으로 그대로 두어야 합니다.

<a id="clusternodes"></a>

## <a name="nodes-on-the-cluster"></a>클러스터의 노드
다음 코드 조각처럼 **nodes** 섹션을 사용하여 Service Fabric 클러스터에서 노드를 구성할 수 있습니다.

    "nodes": [{
        "nodeName": "vm0",
        "iPAddress": "localhost",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r0",
        "upgradeDomain": "UD0"
    }, {
        "nodeName": "vm1",
        "iPAddress": "localhost",
        "nodeTypeRef": "NodeType1",
        "faultDomain": "fd:/dc1/r1",
        "upgradeDomain": "UD1"
    }, {
        "nodeName": "vm2",
        "iPAddress": "localhost",
        "nodeTypeRef": "NodeType2",
        "faultDomain": "fd:/dc1/r2",
        "upgradeDomain": "UD2"
    }],

Service Fabric 클러스터에는 세 개 이상의 노드가 있어야 합니다. 설정에 따라 이 섹션에 노드를 더 추가할 수 있습니다. 다음 표에서는 각 노드의 구성 설정에 대해 설명합니다.

| **노드 구성** | **설명** |
| --- | --- |
| nodeName |노드에 이름을 지정할 수 있습니다. |
| iPAddress |명령 창을 열고 `ipconfig`를 입력하여 노드의 IP 주소를 찾습니다. IPV4 주소를 적어둔 후 **iPAddress** 변수에 할당합니다. |
| nodeTypeRef |노드마다 다른 노드 형식을 할당할 수 있습니다. [노드 형식](#nodetypes) 은 아래 섹션에 정의됩니다. |
| faultDomain |장애 도메인은 클러스터 관리자가 공유되는 물리적 종속성으로 인해 동시에 장애를 경험할 가능성이 있는 실제 노드를 정의할 수 있도록 합니다. |
| upgradeDomain |업그레이드 도메인은 Service Fabric 업그레이드를 위해 거의 같은 시간에 종료된 노드 집합을 나타냅니다. 물리적 요구 사항에 따라 제한되지 않으므로 어떤 업그레이드 도메인에 어떤 노드를 할당할지 선택할 수 있습니다. |

## <a name="cluster-properties"></a>클러스터 속성
ClusterConfig.JSON의 **properties** 섹션은 다음과 같이 클러스터를 구성하는 데 사용됩니다.

<a id="reliability"></a>

### <a name="reliability"></a>안정성
**reliabilityLevel** 섹션은 클러스터의 주 노드에서 실행될 수 있는 시스템 서비스의 복사본 수를 정의합니다. 이를 통해 이러한 서비스의 안정성 및 클러스터의 안정성이 향상됩니다. 이 변수를 이러한 서비스 복사본 3개, 5개, 7개 또는 9개에 각각 해당하는 *Bronze*, *Silver*, *Gold* 또는 *Platinum*으로 설정할 수 있습니다. 아래 예제를 참조하세요.

    "reliabilityLevel": "Bronze",

주 노드에서 시스템 서비스의 단일 복사본이 실행되므로 각 안정성 수준에 대한 주 노드 수가 *Bronze*의 경우 최소 3개, *Silver*의 경우 최소 5개, *Gold*의 경우 최소 7개, *Platinum*의 경우 최소 9개가 필요합니다.

### <a name="diagnostics"></a>진단
다음 코드 조각과 같이 **diagnosticsStore** 섹션을 사용하여 진단 및 문제 해결 노드와 클러스터 오류를 사용하도록 매개 변수를 구성할 수 있습니다. 

    "diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "FileShare",
        "IsEncrypted": "false",
        "connectionstring": "c:\\ProgramData\\SF\\DiagnosticsStore"
    }

**metadata** 는 클러스터 진단에 대한 설명으로 설정에 따라 지정될 수 있습니다. 이러한 변수는 성능 카운터는 물론 ETW 추적 로그, 크래시 덤프를 수집하는 데 유용합니다. ETW 추적 로그에 대한 자세한 내용은 [Tracelog](https://msdn.microsoft.com/library/windows/hardware/ff552994.aspx) 및 [ETW 추적](https://msdn.microsoft.com/library/ms751538.aspx)을 읽어보세요. [크래시 덤프](https://blogs.technet.microsoft.com/askperf/2008/01/08/understanding-crash-dump-files/) 및 [성능 카운터](https://msdn.microsoft.com/library/windows/desktop/aa373083.aspx)를 포함하는 모든 로그는 컴퓨터의 **connectionString** 폴더로 보내질 수 있습니다. 진단을 저장하는 데 *AzureStorage* 를 사용할 수도 있습니다. 샘플 코드 조각에 대해서는 아래를 참조하세요.

    "diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "AzureStorage",
        "IsEncrypted": "false",
        "connectionstring": "xstore:DefaultEndpointsProtocol=https;AccountName=[AzureAccountName];AccountKey=[AzureAccountKey]"
    }

### <a name="security"></a>보안
**security** 섹션은 보안 독립 실행형 Service Fabric 클러스터에 필요합니다. 다음 코드 조각에서는 이 섹션의 일부를 보여 줍니다.

    "security": {
        "metadata": "This cluster is secured using X509 certificates.",
        "ClusterCredentialType": "X509",
        "ServerCredentialType": "X509",
        . . .
    }

**metadata** 는 보안 클러스터에 대한 설명으로 설정에 따라 설정될 수 있습니다. **ClusterCredentialType** 및 **ServerCredentialType**은 클러스터 및 노드가 구현하는 보안 유형을 결정합니다. 인증서 기반 보안의 경우는 *X509*로, Azure Active Directory 기반 보안의 경우는 *Windows*로 설정될 수 있습니다. **security** 섹션의 나머지는 보안 유형을 기준으로 합니다. 나머지 **security** 섹션을 채우는 방법에 대한 자세한 내용은 [독립 실행형 클러스터의 인증서 기반 보안](service-fabric-windows-cluster-x509-security.md) 또는 [독립 실행형 클러스터의 Windows 보안](service-fabric-windows-cluster-windows-security.md)을 읽어보세요.

<a id="nodetypes"></a>

### <a name="node-types"></a>노드 형식
**nodeTypes** 섹션에서는 클러스터가 갖는 노드 형식에 대해 설명합니다. 아래 코드 조각처럼 클러스터에 대해 노드 형식을 하나 이상 지정해야 합니다. 

    "nodeTypes": [{
        "name": "NodeType0",
        "clientConnectionEndpointPort": "19000",
        "clusterConnectionEndpointPort": "19001",
        "leaseDriverEndpointPort": "19002"
        "serviceConnectionEndpointPort": "19003",
        "httpGatewayEndpointPort": "19080",
        "reverseProxyEndpointPort": "19081",
        "applicationPorts": {
            "startPort": "20575",
            "endPort": "20605"
        },
        "ephemeralPorts": {
            "startPort": "20606",
            "endPort": "20861"
        },
        "isPrimary": true
    }]

**name** 은 이 특정 노드 형식의 이름입니다. 이 노드 형식의 노드를 만들려면 [위](#clusternodes)에 설명된 대로 해당 이름을 해당 노드의 **nodeTypeRef**에 할당합니다. 각 노드 형식에 대해 사용되는 연결 끝점을 정의합니다. 이 클러스터의 다른 끝점과 충돌하지 않는 한, 이러한 연결 끝점에 대해 어떤 포트 번호도 선택할 수 있습니다. 다중 노드 클러스터에는 [**reliabilityLevel**](#reliability)에 따라 하나 이상의 주 노드가 있습니다(즉, **isPrimary**가 *true*로 설정됨). **nodeTypes** 및 **reliabilityLevel** 값에 대한 자세한 내용과 주 노드 형식 및 주 노드가 아닌 다른 노드 형식에 대한 자세한 내용은 [Service Fabric 클러스터 용량 계획 고려 사항](service-fabric-cluster-capacity.md)을 참조하세요. 

#### <a name="endpoints-used-to-configure-the-node-types"></a>노드 형식을 구성하는 데 사용되는 끝점
* *clientConnectionEndpointPort*는 클라이언트 API를 사용할 때 클라이언트에서 클러스터에 연결하는 데 사용되는 포트입니다. 
* *clusterConnectionEndpointPort*는 노드 간에 서로 통신하는 데 사용되는 포트입니다.
* *leaseDriverEndpointPort*는 노드가 여전히 활성 상태인지 확인하기 위해 클러스터에서 드라이버를 임대하는 데 사용되는 포트입니다. 
* *serviceConnectionEndpointPort*는 노드에 배포된 서비스 및 응용 프로그램에서 해당 특정 노드의 Service Fabric 클라이언트와 통신하는 데 사용되는 포트입니다.
* *httpGatewayEndpointPort*는 Service Fabric Explorer에서 클러스터에 연결하는 데 사용되는 포트입니다.
* *ephemeralPorts*는 [OS에서 사용되는 동적 포트](https://support.microsoft.com/kb/929851)를 재정의합니다. Service Fabric은 이 중 일부를 응용 프로그램 포트로 사용하고, 나머지를 OS에 사용합니다. 또한 이 범위를 OS에 있는 기존 범위에 매핑하므로 지정된 매핑을 JSON 파일에서 어떤 용도로든 사용할 수 있습니다. 시작 포트와 끝 포트 간의 차이가 255 이상인지 확인해야 합니다. 이 차이가 매우 낮으면 이 범위가 운영 체제에서 공유되므로 충돌이 발생할 수 있습니다. `netsh int ipv4 show dynamicport tcp`를 실행하여 구성된 동적 포트 범위를 참조하세요.
* *applicationPorts*는 Service Fabric 응용 프로그램에서 사용되는 포트입니다. 응용 프로그램 포트 범위는 응용 프로그램의 끝점 요구 사항을 충족할 수 있을 만큼 충분히 커야 합니다. 이 범위는 컴퓨터의 동적 포트 범위, 즉 구성에 설정된 대로 *ephemeralPorts* 범위에서 제외되어야 합니다.  Service Fabric은 새 포트가 필요할 때마다 이를 사용하며 이러한 포트의 방화벽 열기를 처리합니다. 
* *reverseProxyEndpointPort*는 선택적 역방향 프록시 끝점입니다. 자세한 내용은 [Service Fabric 역방향 프록시](service-fabric-reverseproxy.md)를 참조하세요. 

### <a name="log-settings"></a>로그 설정
**fabricSettings** 섹션에서는 Service Fabric 데이터 및 로그에 대한 루트 디렉터리를 설정할 수 있습니다. 이러한 디렉터리는 초기 클러스터 생성 중에만 사용자 지정할 수 있습니다. 이 섹션의 샘플 코드 조각에 대해서는 아래를 참조하세요.

    "fabricSettings": [{
        "name": "Setup",
        "parameters": [{
            "name": "FabricDataRoot",
            "value": "C:\\ProgramData\\SF"
        }, {
            "name": "FabricLogRoot",
            "value": "C:\\ProgramData\\SF\\Log"
    }]

OS가 아닌 드라이브를 사용하면 OS 충돌 시에도 더 큰 안정성을 제공하므로 이러한 드라이브를 FabricDataRoot 및 FabricLogRoot로 사용하는 것이 좋습니다. 데이터 루트만 사용자 지정하는 경우 로그 루트가 데이터 루트에서 한 수준 아래에 배치됩니다.

### <a name="stateful-reliable-service-settings"></a>상태 저장 신뢰할 수 있는 서비스 설정
**KtlLogger** 섹션에서는 Reliable Services에 대한 전역 구성 설정을 설정할 수 있습니다. 이러한 설정에 대한 자세한 내용 [상태 저장 신뢰할 수 있는 서비스 구성](service-fabric-reliable-services-configuration.md)을 참조하세요.
아래 예제에서는 상태 저장 서비스에 대한 모든 신뢰할 수 있는 컬렉션 백업을 만드는 공유 트랜잭션 로그를 변경하는 방법을 보여 줍니다.

    "fabricSettings": [{
        "name": "KtlLogger",
        "parameters": [{
            "name": "SharedLogSizeInMB",
            "value": "4096"
        }]
    }]

## <a name="next-steps"></a>다음 단계
독립 실행형 클러스터 설치에 따라 완전한 ClusterConfig.JSON 파일을 구성한 경우 [독립 실행형 Service Fabric 클러스터 만들기](service-fabric-cluster-creation-for-windows-server.md) 문서에 따라 클러스터를 배포한 다음 [Service Fabric Explorer로 클러스터 시각화](service-fabric-visualizing-your-cluster.md)를 계속 진행할 수 있습니다.


