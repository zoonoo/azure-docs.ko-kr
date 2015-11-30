<properties
   pageTitle="서비스 패브릭 클러스터 크기 조정 | Microsoft Azure"
   description="가상 컴퓨터 노드를 추가하거나 제거하여 수요에 맞게 서비스 패브릭 클러스터의 크기를 조정합니다."
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="11/03/2015"
   ms.author="chackdan"/>

# VM(가상 컴퓨터)을 추가하거나 제거하여 서비스 패브릭 클러스터 크기 조정

가상 컴퓨터를 추가하거나 제거하여 수요에 맞게 서비스 패브릭 클러스터의 크기를 조정할 수 있습니다.

>[AZURE.NOTE]여기에서는 충분한 코어가 구독에 있어 이 클러스터를 구성하는 새 VM을 추가할 수 있다고 가정합니다.


## 수동으로 서비스 패브릭 클러스터 크기 조정

### 크기를 조정할 노드 형식 선택

클러스터에 여러 노드 형식이 있는 경우 특정 노드 형식에서 VM을 추가하거나 제거해야 합니다.

1. Azure 포털 [http://aka.ms/servicefabricportal](http://aka.ms/servicefabricportal)에 로그온합니다.

2. 서비스 패브릭 클러스터 ![BrowseServiceFabricClusterResource][BrowseServiceFabricClusterResource]로 이동합니다.

3. 크기를 조정할 클러스터를 선택합니다.

4. 클러스터 대시보드에서 설정 블레이드로 이동합니다. 설정 블레이드가 표시되지 않으면 클러스터 대시보드의 필수 부분에서 "모든 설정"을 클릭합니다.

5. NodeTypes를 클릭하여 NodeTypes 목록 블레이드를 엽니다.

7. 크기를 조정할 노드 형식을 클릭하면 노드 형식 세부 정보 블레이드가 열립니다.

### 노드를 추가하여 확장

VM 수를 원하는 대로 조정하고 저장합니다. 배포가 완료되면 노드/VM이 추가됩니다.

### 노드를 제거하여 축소

노드 제거는 2단계 프로세스로 이루어집니다.

1. VM 수를 원하는 대로 조정하고 저장합니다. 슬라이더의 아래쪽 끝에 해당 NodeType의 최소 VM 요구 사항이 표시됩니다.

  >[AZURE.NOTE]주 노드 형식의 경우 VM 수가 5개 이상이어야 합니다.

	Once that deployment is complete, you will get notified of the VM names that can now be deleted. You now need to navigate to the VM resource and delete it.

2. 클러스터 대시보드로 돌아와서 리소스 그룹을 클릭합니다. 리소스 그룹 블레이드가 열립니다.

3. 요약에서 찾거나 "..."를 클릭하여 리소스 목록을 엽니다.

4. 시스템에서 삭제 가능한 것으로 지정한 VM 이름을 클릭합니다.

5. 삭제 아이콘을 클릭하여 VM을 삭제합니다.

## 자동으로 서비스 패브릭 클러스터 크기 조정

현재 서비스 패브릭 클러스터는 자동 크기 조정을 지원하지 않습니다. 조만간 VMSS(가상 컴퓨터 크기 집합)에 클러스터가 빌드될 예정입니다. 그러면 자동 크기 조정이 가능해지고 클라우드 서비스의 자동 크기 조정 동작과 비슷하게 작동할 것입니다.

## PowerShell/CLI를 사용하여 크기 조정

이 문서에서는 포털을 사용하여 클러스터의 크기를 조정하는 방법에 대해 다룹니다. 그러나 클러스터 리소스의 ARM 명령을 사용하여 명령줄에서 동일한 작업을 수행할 수 있습니다. ClusterResource의 GET 명령을 사용하면 사용되지 않는 노드 목록이 표시됩니다.

## 다음 단계

- [클러스터 업그레이드 알아보기](service-fabric-cluster-upgrade.md)
- [최대 크기를 위해 상태 저장 서비스를 분할하는 방법 알아보기](service-fabric-concepts-partitioning.md)


<!--Image references-->
[BrowseServiceFabricClusterResource]: ./media/service-fabric-cluster-scale-up-down/BrowseServiceFabricClusterResource.png

<!---HONumber=Nov15_HO4-->