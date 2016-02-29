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
   ms.date="02/12/2016"
   ms.author="chackdan"/>


# 가상 컴퓨터를 추가하거나 제거하여 서비스 패브릭 클러스터 크기 조정

가상 컴퓨터를 추가하거나 제거하여 수요에 맞게 Azure 서비스 패브릭 클러스터의 크기를 조정할 수 있습니다.

>[AZURE.NOTE] 사용자의 구독에 이 클러스터를 형성할 새 VM을 추가하기에 충분한 코어가 있어야 합니다.

## 수동으로 서비스 패브릭 클러스터의 크기 조정

### 크기를 조정할 노드 유형 선택

클러스터에 여러 노드 유형이 있는 경우 VM을 특정 노드 유형에 추가하거나 제거해야 합니다. 방법은 다음과 같습니다.

1. [Azure 포털](https://portal.azure.com/)에 로그인합니다.

2. **서비스 패브릭 클러스터**로 이동합니다. ![Azure 포털에서 서비스 패브릭 클러스터 페이지입니다.][BrowseServiceFabricClusterResource]

3. 확장 또는 축소하려는 클러스터를 선택합니다.

4. 클러스터 대시보드에서 **설정** 블레이드로 이동합니다. **설정** 블레이드가 표시되지 않으면 클러스터 대시보드의 필수 부분에서 **모든 설정**을 클릭합니다.

5. **NodeTypes**를 클릭하여 **NodeTypes 목록** 블레이드를 엽니다.

6. 확장하거나 축소하려는 노드 유형을 클릭하여 **NodeType 세부 정보** 블레이드를 엽니다.

### 노드를 추가하여 확장

VM 수를 원하는 대로 조정하고 저장합니다. 배포가 완료되면 노드/VM이 추가됩니다.

### 노드를 제거하여 규모 축소

노드 제거는 2단계 프로세스로 이루어집니다.

1. VM 수를 원하는 대로 조정하고 저장합니다. 슬라이더의 아래쪽 끝에 해당 노드 유형에 필요한 최소 VM 수가 표시됩니다.

    >[AZURE.NOTE] 주 노드 유형의 경우 VM 수가 5개 이상이어야 합니다.

2. 해당 배포가 완료되면 지금 삭제할 수 있는 VM 이름의 알림이 표시됩니다. 그런 다음 VM 리소스를 이동하고 삭제해야 합니다.

    a. 클러스터 대시보드로 돌아와서 **리소스 그룹**을 클릭합니다. **리소스 그룹** 블레이드가 열립니다.

    b. **요약**에서 찾거나 "**...**"를 클릭하여 리소스 목록을 엽니다.

    c. 시스템에서 삭제 가능한 것으로 표시한 VM 이름을 클릭합니다.

    d. **삭제** 아이콘을 클릭하여 VM을 삭제합니다.

>[AZURE.NOTE] 가용성을 유지하고 상태를 보존하기 위해 서비스 패브릭 클러스터에서 특정 수의 노드가 항상 작동 상태를 유지해야 하며, 이 숫자를 "유지 관리 쿼럼"이라고 합니다. 따라서 [상태 전체 백업](service-fabric-reliable-services-backup-restore.md)을 처음으로 수행하는 경우를 제외하고 일반적으로 클러스터의 모든 컴퓨터를 종료하는 행위는 안전하지 않습니다.

## 자동 크기 조정 서비스 패브릭 클러스터

현재 서비스 패브릭 클러스터는 자동 크기 조정을 지원하지 않습니다. 조만간 가상 컴퓨터 크기 집합에 클러스터가 빌드될 예정입니다. 그러면 자동 크기 조정이 가능해지고 클라우드 서비스의 자동 크기 조정 동작과 비슷하게 작동할 것입니다.

## PowerShell/CLI를 사용하여 클러스터 크기 조정

이 문서에서는 포털을 사용하여 클러스터의 크기를 조정하는 방법에 대해 다룹니다. 그러나 클러스터 리소스의 Azure 리소스 관리자 명령을 사용하여 명령줄에서 동일한 작업을 수행할 수 있습니다. 클러스터 리소스의 GET 응답을 사용하면 사용되지 않는 노드 목록이 표시됩니다.

## 다음 단계

- [클러스터 업그레이드 알아보기](service-fabric-cluster-upgrade.md)
- [최대 크기를 위해 상태 저장 서비스를 분할하는 방법 알아보기](service-fabric-concepts-partitioning.md)

<!--Image references-->
[BrowseServiceFabricClusterResource]: ./media/service-fabric-cluster-scale-up-down/BrowseServiceFabricClusterResource.png

<!---HONumber=AcomDC_0218_2016-->