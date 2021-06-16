---
title: Azure Service Fabric 동적 노드 태그
description: Azure Service Fabric을 사용하면 노드 태그를 동적으로 추가하고 제거할 수 있습니다.
author: yu-supersonic
ms.topic: conceptual
ms.date: 04/05/2021
ms.author: branim
ms.custom: devx-track-csharp
ms.openlocfilehash: b20ab4720f9f172ef9248d3314b25922896eb8bd
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/02/2021
ms.locfileid: "110796933"
---
# <a name="introduction-to-dynamic-node-tags"></a>동적 노드 태그 소개
노드 태그를 사용하면 노드의 태그를 동적으로 추가하거나 제거하여 서비스 배치에 영향을 줄 수 있습니다. 노드 태그 지정은 매우 유연하며 애플리케이션이나 클러스터를 업그레이드하지 않고도 서비스 배치를 변경하는 데 사용할 수 있습니다. 노드에서 언제든 태그를 추가하거나 제거할 수 있고 서비스에서 태그를 만들 때 특정 태그의 요구 사항을 지정할 수 있습니다. 또한 서비스가 실행 중인 동안에도 해당 태그 요구 사항을 동적으로 업데이트할 수 있습니다.

노드 태그 지정은 [배치 제약 조건](service-fabric-cluster-resource-manager-configure-services.md)과 유사하며 일반적으로 서비스가 실행되는 노드를 제어하는 데 사용됩니다. 각 Service Fabric 서비스는 태그를 배치해야 하거나 계속 실행되도록 구성할 수 있습니다.

노드 태그 지정은 모든 Service Fabric 호스티드 서비스 유형(Reliable Services, 게스트 실행 파일, 컨테이너)에 지원됩니다. 노드 태그 지정을 사용하려면 Service Fabric 런타임 버전 8.0 이상을 실행하고 있어야 합니다.

이 문서의 나머지 부분에서는 노드 태그 지정을 사용하거나 사용하지 않도록 설정하는 방법을 설명하고 이 기능을 사용하는 방법의 예제를 제공합니다.


## <a name="describing-dynamic-node-tags"></a>동적 노드 태그 설명
서비스에서 필요한 태그를 지정할 수 있습니다. 다음과 같이 두 가지 유형의 태그가 있습니다.
* **배치에 필요한 태그** 는 서비스 배치에만 필요한 태그 세트를 설명합니다. 복제본이 배치되면 서비스를 중단하지 않고도 이러한 태그를 제거할 수 있습니다. 노드에서 이러한 태그가 제거되어도 서비스 복제본은 계속 작동하며 Service Fabric에서 서비스를 제거하지 않습니다.

* **실행하는 데 필요한 태그** 는 서비스의 배치와 실행 모두에 필요한 태그 세트를 설명합니다. 필요한 실행 태그가 제거되면 Service Fabric은 해당 태그가 지정된 다른 노드로 서비스를 이동합니다.

예: 배치에 필요한 태그는 일종의 컨테이너 활성자 서비스를 사용할 때 활용할 수 있으며, 컨테이너에 대해 해당 서비스를 배치해야 하고, 컨테이너가 활성화되는 즉시 활성자가 더 이상 필요하지 않으며 컨테이너와 연결된 태그를 제거해도 컨테이너가 계속 실행됩니다.
실행에 필요한 태그는 사용자용 서비스와 함께 배치하면 유용한 청구 서비스가 있는 경우 사용할 수 있습니다. 노드에서 청구 서비스가 실패할 경우 서비스와 연결된 태그를 제거하면 사용자용 서비스가 청구 서비스와 해당 태그가 있는 다른 노드로 이동됩니다.

단일 노드에서 C# API, REST API, PowerShell 명령 등과 같은 표준 Service Fabric 인터페이스 메커니즘을 사용하여 태그 또는 태그 세트를 추가, 업데이트, 제거할 수 있습니다.

> [!NOTE]
> Service Fabric은 노드 태그를 사용할 때 UD/FD 배포를 유지하지 않습니다. 노드 태그를 적절하게 관리하세요. 그래야만 도메인 간에 잘못된 태그 배포로 인해 FD/UD 위반이 발생하지 않습니다.

## <a name="enabling-dynamic-node-tags"></a>동적 노드 태그 사용
이 기능이 작동하려면 클러스터 매니페스트의 PlacementAndLoadBalancing 섹션에서 XML 또는 JSON을 사용하여 NodeTaggingEnabled 구성을 사용하도록 설정해야 합니다.

``` xml
<Section Name="PlacementAndLoadBalancing">
     <Parameter Name="NodeTaggingEnabled" Value="true" />
</Section>
```

독립 실행형 배포의 경우 ClusterConfig.json 또는 Azure 호스티드 클러스터의 경우 Template.json를 통해 수행됩니다.

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "NodeTaggingEnabled",
          "value": "true"
      }
    ]
  }
]
```

## <a name="setting-dynamic-node-tags"></a>동적 노드 태그 설정

### <a name="using-powershell"></a>PowerShell 사용

노드에 노드 태그 추가:

```posh
Add-ServiceFabricNodeTags -NodeName "DB.1" -NodeTags @("SampleTag1", "SampleTag2")
```
이 명령은 DB.1 노드에 “SampleTag1” 및 “SampleTag2” 태그를 추가합니다.

노드에서 노드 태그 제거:

```posh
Remove-ServiceFabricNodeTags -NodeName "DB.1" -NodeTags @("SampleTag1", "SampleTag2")
```
이 명령은 DB.1 노드에서 “SampleTag1” 및 “SampleTag2” 태그를 제거합니다.

### <a name="using-c-apis"></a>C# API 사용

노드에 노드 태그 추가:

```csharp
FabricClient fabricClient = new FabricClient();
List<string> nodeTagsList = new List<string>();
nodeTagsList.Add("SampleTag1");
nodeTagsList.Add("SampleTag2");
await fabricClient.ClusterManager.AddNodeTagsAsync("DB.1", nodeTagsList);
```

노드에서 노드 태그 제거:

```csharp
FabricClient fabricClient = new FabricClient();
List<string> nodeTagsList = new List<string>();
nodeTagsList.Add("SampleTag1");
nodeTagsList.Add("SampleTag2");
await fabricClient.ClusterManager.RemoveNodeTagsAsync("DB.1", nodeTagsList);
```

## <a name="setting-required-tags-for-services"></a>서비스의 필수 태그 설정

### <a name="using-powershell"></a>PowerShell 사용

새 서비스 만들기:

```posh
New-ServiceFabricService -ApplicationName fabric:/HelloWorld -ServiceName fabric:/HelloWorld/svc1 -ServiceTypeName HelloWorldStateful -Stateful -PartitionSchemeSingleton -TargetReplicaSetSize 5 -MinReplicaSetSize 3 -TagsRequiredToRun @("SampleTag1") - TagsRequiredToPlace @("SampleTag2")
```
이 명령은 서비스를 만듭니다. 서비스를 노드에 배치하기 위해서는 노드에 “SampleTag2”가 있어야 하고 해당 노드에서 서비스가 계속 실행되기 위해서는 “SampleTag1”이 있어야 합니다.

기존 서비스 업데이트:

```posh
Update-ServiceFabricService -Stateful -ServiceName fabric:/myapp/test -TagsRequiredToRun @("SampleTag1") -TagsRequiredToPlace @("SampleTag2")
```
이 명령은 서비스를 업데이트합니다. 서비스를 노드에 배치하기 위해서는 노드에 “SampleTag2”가 있어야 하고 해당 노드에서 서비스가 계속 실행되기 위해서는 “SampleTag1”이 있어야 합니다.

### <a name="using-c-apis"></a>C# API 사용

새 서비스 만들기:

```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
//set up the rest of the ServiceDescription
ServiceTags serviceTags = new ServiceTags();
serviceTags.TagsRequiredToPlace.Add("SampleTag1");
serviceTags.TagsRequiredToRun.Add("SampleTag2");
serviceDescription.ServiceTags = serviceTags;
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

기존 서비스 업데이트:

```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceUpdateDescription serviceUpdate = new StatefulServiceUpdateDescription();
ServiceTags serviceTags = new ServiceTags();
serviceTags.TagsRequiredToPlace.Add("SampleTag1");
serviceTags.TagsRequiredToRun.Add("SampleTag2");
serviceUpdate.ServiceTags = serviceTags;
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/AppName/ServiceName"), serviceUpdate);
```

이러한 모든 명령은 상태 비저장 서비스에 동일하게 적용됩니다.

## <a name="next-steps"></a>다음 단계
[배치 제약 조건](service-fabric-cluster-resource-manager-configure-services.md)에 대한 자세한 정보
