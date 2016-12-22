---
title: "Windows 보안을 사용하여 Windows에서 실행되는 클러스터 보안 | Microsoft Docs"
description: "Windows 보안을 사용하여 Windows에서 실행되는 독립 실행형 클러스터에서 노드 간 및 클라이언트-노드 보안을 구성하는 방법을 알아봅니다."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: ce3bf686-ffc4-452f-b15a-3c812aa9e672
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/09/2016
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: 08024aedb0889c91909f12aa1e38c91b11d3090f
ms.openlocfilehash: 142b76f053adc273e2e071f169f8f647fbd1c241


---
# <a name="secure-a-standalone-cluster-on-windows-using-windows-security"></a>Windows 보안을 사용하여 독립 실행형 클러스터 보호
특히 실행 중인 프로덕션 워크로드가 있는 경우 서비스 패브릭 클러스터에 대한 무단 액세스를 방지하기 위해 보안을 설정해야 합니다. 이 문서에서는 *ClusterConfig.JSON* 파일에서 Windows 보안을 사용하여 노드 간 및 클라이언트-노드 보안을 구성하는 방법을 설명합니다. 이 문서는 [Windows에서 실행되는 독립 실행형 클러스터 만들기](service-fabric-cluster-creation-for-windows-server.md)의 보안 구성 단계에 해당합니다. Service Fabric에서 Windows 보안을 사용하는 방법에 대한 자세한 내용은 [클러스터 보안 시나리오](service-fabric-cluster-security.md)를 참조하세요.

> [!NOTE]
> 한 보안 선택에서 다른 보안 선택으로 클러스터 업그레이드가 지원되지 않으므로 노드 간 보안에 대한 보안 선택을 신중하게 고려해야 합니다. 보안 선택을 변경하려면 전체 클러스터를 다시 구축해야 합니다.
> 
> 

## <a name="configure-windows-security"></a>Windows 보안 구성
[Microsoft.Azure.ServiceFabric.WindowsServer.<version>.zip](http://go.microsoft.com/fwlink/?LinkId=730690) 독립 실행형 클러스터 패키지와 함께 다운로드된 샘플 *ClusterConfig.Windows.JSON* 구성 파일은 Windows 보안 구성을 위한 템플릿을 포함합니다.  **Properties** 섹션에서 다음과 같이 Windows 보안을 구성합니다.

```
"security": {
            "ClusterCredentialType": "Windows",
            "ServerCredentialType": "Windows",
            "WindowsIdentities": {
                "ClusterIdentity" : "[domain\machinegroup]",
                "ClientIdentities": [{
                    "Identity": "[domain\username]",
                    "IsAdmin": true
                }]
            }
        }
```

| **구성 설정** | **설명** |
| --- | --- |
| ClusterCredentialType |**ClusterCredentialType** 매개 변수를 *Windows*로 설정하여 Windows 보안을 사용하도록 설정합니다. |
| ServerCredentialType |**ServerCredentialType** 매개 변수를 *Windows*로 설정하여 클라이언트에 대한 Windows 보안을 사용하도록 설정합니다. 클러스터의 클라이언트와 클러스터 자체를 가리키며 Active Directory 도메인 내에서 실행됩니다. |
| WindowsIdentities |클러스터와 클라이언트 ID를 포함합니다. |
| ClusterIdentity |노드 간 보안을 구성합니다. 컴퓨터 그룹 이름. |
| ClientIdentities |클라이언트-노드 보안을 구성합니다. 클라이언트 사용자 계정의 배열입니다. |
| ID |클라이언트 ID, 도메인 사용자입니다. |
| IsAdmin |도메인 사용자가 관리자 클라이언트 액세스 권한을 갖는 경우 true이며, 사용자 클라이언트 액세스 권한을 갖는 경우 false를 지정합니다. |

[노드 간 보안](service-fabric-cluster-security.md#node-to-node-security)은 **ClusterIdentity**를 사용하여 설정하는 방식으로 구성합니다. 노드 간의 신뢰 관계를 구축하기 위해 서로를 인식하도록 만들어야 합니다. 클러스터에 모든 노드를 포함하는 도메인 그룹을 만들어 수행할 수 있습니다. 이 그룹 이름은 **ClusterIdentity**로 지정해야 합니다. 자세한 내용은 [Active Directory에 그룹 만들기](https://msdn.microsoft.com/en-us/library/aa545347(v=cs.70).aspx)를 참조하세요.

[클라이언트 및 노드 간 보안](service-fabric-cluster-security.md#client-to-node-security)은 **ClientIdentities**를 사용하여 구성합니다. 클라이언트와 클러스터 간에 트러스트를 설정하기 위해 신뢰할 수 있는 클라이언트 ID를 파악하도록 클러스터를 구성해야 합니다. 이 작업은 두 가지 방법으로 수행할 수 있습니다. 연결할 수 있는 도메인 그룹 사용자를 지정하거나 연결할 수 있는 도메인 노드 사용자를 지정합니다. 서비스 패브릭은 서비스 패브릭 클러스터에 연결된 클라이언트에 대해 관리자 및 사용자 액세스 제어 형식을 지원합니다. 액세스 제어는 클러스터 관리자가 사용자 그룹마다 특정 형식의 클러스터 작업에 대한 액세스를 제한하는 기능을 제공하여 클러스터의 보안을 강화할 수 있습니다.  관리자는 관리 기능(읽기/쓰기 기능만 포함)에 대한 모든 권한을 가집니다. 사용자는 기본적으로 관리 기능(예: 쿼리 기능)에 대한 읽기 권한 및 응용 프로그램과 서비스를 확인하는 기능만 갖습니다.

다음 예제 **security** 섹션에서는 Windows 보안을 구성하고 *ServiceFabric\\ClusterNodes* 컴퓨터 그룹의 컴퓨터가 클러스터의 일부이며 *CONTOSO\usera*에 관리자 클라이언트 액세스 권한이 있음을 지정합니다.

```
"security": {
    "ClusterCredentialType": "Windows",
    "ServerCredentialType": "Windows",
    "WindowsIdentities": {
        "ClusterIdentity" : "ServiceFabric\\ClusterNodes",
        "ClientIdentities": [{
            "Identity": "CONTOSO\\usera",
        "IsAdmin": true
        }]
    }
},
```

## <a name="next-steps"></a>다음 단계
*ClusterConfig.JSON* 파일에서 Windows 보안을 구성한 후에 [Windows에서 실행되는 독립 실행형 클러스터 만들기](service-fabric-cluster-creation-for-windows-server.md)에 나와 있는 클러스터 만들기 프로세스를 다시 시작합니다.

노드 간 보안, 클라이언트 및 노드 간 보안 및 역할 기반 액세스 제어 방법에 대한 자세한 내용은 [클러스터 보안 시나리오](service-fabric-cluster-security.md)를 참조하세요.

PowerShell 또는 FabricClient를 사용한 연결에 대한 예제는 [보안 클러스터에 연결](service-fabric-connect-to-secure-cluster.md) 을 참조하세요.




<!--HONumber=Nov16_HO4-->


