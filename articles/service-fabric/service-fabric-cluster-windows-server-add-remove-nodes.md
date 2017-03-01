---
title: "독립 실행형 Service Fabric 클러스터에 노드 추가 또는 제거 | Microsoft Docs"
description: "Windows Server를 실행하는 실제 또는 가상 컴퓨터에서 Azure 서비스 패브릭 클러스터에 노드를 추가하거나 제거하는 방법(온-프레미스 또는 클라우드에 위치 가능)을 알아봅니다."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: bc6b8fc0-d2af-42f8-a164-58538be38d02
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/02/2017
ms.author: chackdan
translationtype: Human Translation
ms.sourcegitcommit: af121309be44852ee51f34130330533adf19d586
ms.openlocfilehash: 68474b24519a46db71fe59b5d0574cc4700efccb
ms.lasthandoff: 02/16/2017


---
# <a name="add-or-remove-nodes-to-a-standalone-service-fabric-cluster-running-on-windows-server"></a>Windows Server가 실행되는 독립 실행형 서비스 패브릭 클러스터에 노드 추가 또는 제거 | Microsoft Azure
[Windows Server 컴퓨터에 독립 실행형 서비스 패브릭 클러스터를 만든](service-fabric-cluster-creation-for-windows-server.md)후 비즈니스 요구가 변경될 수 있으므로 클러스터에 여러 노드를 추가 또는 제거해야 할 수 있습니다. 이 문서에서는 이 목표를 달성하는 자세한 단계를 제공합니다.

## <a name="add-nodes-to-your-cluster"></a>클러스터에 노드 추가
1. [클러스터 배포를 위한 필수 조건을 충족하는 컴퓨터 준비](service-fabric-cluster-creation-for-windows-server.md) 섹션에 나와 있는 단계에 따라 클러스터에 추가할 VM/컴퓨터를 준비합니다.
2. 이 VM/컴퓨터를 추가하려는 장애 도메인 및 업그레이드 도메인을 계획합니다.
3. 클러스터에서 추가하려는 VM/컴퓨터로 RDP(원격 데스크톱)를 수행합니다.
4. 이 VM/컴퓨터에 [Windows Server용 서비스 패브릭에 대한 독립 실행형 패키지를 복사 또는 다운로드](http://go.microsoft.com/fwlink/?LinkId=730690) 하고 패키지 압축을 풉니다.
5. Powershell을 관리자로 실행하고 압축을 푼 패키지의 위치로 이동합니다.
6. 추가할 새 노드를 설명하는 매개 변수를 사용하여 *AddNode.ps1* Powershell을 실행합니다. 아래 예제에서는 NodeType0 유형, IP 주소 182.17.34.52를 사용하여 VM5라는 새 노드를 UD1 및 fd:/dc1/r0에 추가합니다. *ExistingClusterConnectionEndPoint* 는 기존 클러스터에 이미 있는 노드에 대한 연결 끝점입니다. 이 끝점의 경우 클러스터에서 *모든* 노드의 IP 주소를 선택할 수 있습니다.

```
.\AddNode.ps1 -NodeName VM5 -NodeType NodeType0 -NodeIPAddressorFQDN 182.17.34.52 -ExistingClientConnectionEndpoint 182.17.34.50:19000 -UpgradeDomain UD1 -FaultDomain fd:/dc1/r0 -AcceptEULA

```
cmdlet [Get-ServiceFabricNode](https://docs.microsoft.com/powershell/servicefabric/vlatest/Get-ServiceFabricNode)를 실행하여 새 노드가 추가되었는지 확인할 수 있습니다.


## <a name="remove-nodes-from-your-cluster"></a>클러스터에서 노드 제거
클러스터에 대해 선택한 안정성 수준에 따라 주 노드 유형의 처음 n(3/5/7/9) 노드는 제거할 수 없습니다. 또한 개발 클러스터에서 RemoveNode 명령의 실행은 지원되지 않습니다.

1. 클러스터에서 제거하려는 VM/컴퓨터로 RDP(원격 데스크톱).
2. [Windows Server용 서비스 패브릭에 대한 독립 실행형 패키지를 복사 또는 다운로드](http://go.microsoft.com/fwlink/?LinkId=730690) 하고 이 VM/컴퓨터에 패키지 압축을 풉니다.
3. Powershell을 관리자로 실행하고 압축을 푼 패키지의 위치로 이동합니다.
4. Powershell에서 *RemoveNode.ps1*을 실행합니다. 아래 예제에서는 클러스터에서 현재 노드를 제거합니다. *ExistingClientConnectionEndpoint*는 클러스터에 유지되는 모든 노드에 대한 클라이언트 연결 끝점입니다. 클러스터에서 *원하는* **다른 노드**의 IP 주소 및 끝점 포트를 선택합니다. 이 **다른 노드**는 제거된 노드에 대한 클러스터 구성을 업데이트합니다. 

```

.\RemoveNode.ps1 -ExistingClientConnectionEndpoint 182.17.34.50:19000

```

> [!NOTE]
> 시스템 서비스 종속성으로 인해 일부 노드가 제거될 수 있습니다. 이러한 노드는 주 노드이며 `Get-ServiceFabricClusterManifest`를 사용하는 클러스터 매니페스트를 쿼리하고 `IsSeedNode=”true”`가 표시된 노드 항목을 찾아 식별할 수 있습니다. 
> 
> 

노드를 제거한 후에도 쿼리와 SFX에 종료 중인 것으로 표시되는 경우 이는 알려진 결함입니다. 이는 향후 릴리스에서 수정될 예정입니다. 


## <a name="remove-node-types-from-your-cluster"></a>클러스터에서 노드 유형 제거
노드 유형을 제거하는 데에는 특별한 주의가 필요합니다. 노드 유형을 제거하기 전에 노드 유형을 참조하는 노드가 있는지 다시 확인합니다.


## <a name="replace-primary-nodes-of-your-cluster"></a>클러스터의 주 노드 교체
주 노드 교체는 일괄 처리로 제거한 다음 추가하는 대신 차례로 수행되어야 합니다.


## <a name="next-steps"></a>다음 단계
* [독립 실행형 Windows 클러스터에 대한 구성 설정](service-fabric-cluster-manifest.md)
* [X509 인증서를 사용하여 Windows에서 독립 실행형 클러스터 보호](service-fabric-windows-cluster-x509-security.md)
* [Windows를 실행하는 Azure VM에서 독립 실행형 서비스 패브릭 클러스터 만들기](service-fabric-cluster-creation-with-windows-azure-vms.md)


