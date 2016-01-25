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
   ms.date="01/11/2016"
   ms.author="chackdan"/>

# VM(가상 컴퓨터)을 추가하거나 제거하여 서비스 패브릭 클러스터 크기 조정

가상 컴퓨터를 추가하거나 제거하여 수요에 맞게 서비스 패브릭 클러스터의 크기를 조정할 수 있습니다.

>[AZURE.NOTE]여기에서는 충분한 코어가 구독에 있어 이 클러스터를 구성하는 새 VM을 추가할 수 있다고 가정합니다.


## 수동으로 서비스 패브릭 클러스터 크기 조정

클러스터에 여러 노드 형식이 있는 경우 특정 노드 형식으로/에서 VM을 추가하거나 제거해야 합니다. 동일한 배포 내에서 하나의 노드 형식에 VM을 추가하고 다른 노드 형식에서 VM을 제거할 수 있습니다.

### 포털을 사용하여 배포한 클러스터 업그레이드

이 단계는 복잡한 프로세스이므로 사용자를 위해 Git Repo에 업로드된 powershell 모듈이 있습니다.

**1단계**: 이 폴더를 이 [Git 리포지토리](https://github.com/ChackDan/Service-Fabric/tree/master/Scripts/ServiceFabricRPHelpers)에서 컴퓨터로 복사합니다.

**2단계**: 컴퓨터에 Azure SDK 1.0 이상이 설치되어 있는지 확인합니다.

**3단계**: Powershell 창을 열고 ServiceFabricRPHelpers.psm을 가져옵니다.

```
Remove-Module ServiceFabricRPHelpers
```

방금 복사한 poweshell 모듈을 가져옵니다. 다음 cmd를 복사하고 컴퓨터의 .psm1로 경로를 변경할 수 있습니다.

```
Import-Module "C:\Users\chackdan\Documents\GitHub\Service-Fabric\Scripts\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1"
```

 **4단계**: Azure 계정에 로그인

```
Login-AzureRmAccount
```

Invoke-ServiceFabricRPClusterScaleUpgrade 명령을 실행하여 리소스 그룹 이름 및 구독이 올바른지 확인합니다.

```
Invoke-ServiceFabricRPClusterScaleUpgrade -ResourceGroupName <string> -SubscriptionId <string>
```

다음은 동일한 PS 명령의 작성된 예제입니다.

```
Invoke-ServiceFabricRPClusterScaleUpgrade -ResourceGroupName chackod02 -SubscriptionId 18ad2z84-84fa-4798-ad71-e70c07af879f
```

  **5단계**: 이제 명령은 클러스터 정보를 검색하고 먼저 해당 노드 유형에 대한 현재 VM/노드 수를 나타내는 모든 노드 유형을 안내한 다음 새 VM/노드 수를 제공하도록 요청합니다.

 **이 노드 유형을 확장하려면** 현재 VM 수보다 더 큰 숫자를 지정합니다.

**이 노드 유형을 축소하려면** 현재 VM 수보다 더 작은 숫자를 지정합니다.

이러한 프롬프트는 이제 모든 노드 유형에 대해 반복합니다. 클러스터에 하나의 노드 유형이 있는 경우 프롬프트가 한 번만 나타납니다.
 
  **6단계**: 새 VM을 추가하는 경우 추가하는 VM에 대한 원격 데스크톱 사용자 ID 및 암호를 제공하라는 메시지가 나타납니다.
 
**7단계**: 출력 창에 배포 진행률을 알리는 메시지가 표시됩니다. 배포가 완료되면 클러스터는 5단계에서 지정한 노드의 수가 있어야 합니다.


![ScaleupDownPSOut][ScaleupDownPSOut]


**8단계**: 축소 요청이었던 경우 VM을 삭제하는 하나의 단계가 더 있습니다. 스크립트는 제거 요청한 모든 VM을 비활성화합니다. 즉, 이러한 노드/VM에는 응용 프로그램 또는 시스템 복제본이 없습니다. 따라서 이러한 VM을 삭제해도 됩니다.

**참고** - 비활성화된 노드는 SF 클러스터에서 더 이상 사용되지 않지만 이에 대해 청구되지 않도록 비활성화된 VM을 삭제해야 합니다.

**8.1단계**: Azure 포털([http://aka.ms/servicefabricportal](http://aka.ms/servicefabricportal))에 로그온합니다.

**8.2단계**: 축소한 클러스터 리소스로 이동하고 필수 웹 파트에서 "모든 설정"을 클릭합니다.

**8.3단계**: 노드 유형을 클릭하면 비활성화된 노드의 목록이 표시됩니다. 이 그림에서 chackodnt15, chackodnt24, chackodnt25 및 chackodnt26은 삭제해야 하는 VM입니다.

![DeactivatedNodeList][DeactivatedNodeList]

**8.4단계**: PS 또는 포털을 통해 해당 VM을 삭제합니다. 삭제하면 클러스터의 축소가 완료된 것입니다.

```
Remove-AzureRmResource -ResourceName <Deactivated Node name without the understore> -ResourceType Microsoft.Compute/virtualMachines -ResourceGroupName <Resource Group name> -ApiVersion <Api version>
```
다음은 해당 명령의 작성된 예제입니다.

```
Remove-AzureRmResource -ResourceName chackodnt26 -ResourceType Microsoft.Compute/virtualMachines -ResourceGroupName chackod02 -ApiVersion 2015-05-01-preview
```

### ARM PowerShell/CLI를 사용하여 배포한 클러스터 업그레이드

처음에 ARM 템플릿을 사용하여 클러스터를 배포했을 경우 지정된 노드 유형에 대한 VM의 수 및 VM을 지원하는 모든 리소스를 수정해야 합니다. 주 노드 유형에 대해 허용된 VM의 최소 수는 5이며(비 테스트 클러스터용) 테스트 클러스터용의 경우 최소 수는 3입니다.

새 템플릿이 있는 경우 업그레이드하는 클러스터와 동일한 리소스 그룹을 사용하여 ARM을 통해 배포할 수 있습니다.


## 자동으로 서비스 패브릭 클러스터 크기 조정

현재 서비스 패브릭 클러스터는 자동 크기 조정을 지원하지 않습니다. 조만간 VMSS(가상 컴퓨터 크기 집합)에 클러스터가 빌드될 예정입니다. 그러면 자동 크기 조정이 가능해지고 클라우드 서비스의 자동 크기 조정 동작과 비슷하게 작동할 것입니다.


## 다음 단계

- [클러스터 업그레이드 알아보기](service-fabric-cluster-upgrade.md)
- [최대 크기를 위해 상태 저장 서비스를 분할하는 방법 알아보기](service-fabric-concepts-partitioning.md)


<!--Image references-->
[ScaleupDownPSOut]: ./media/service-fabric-cluster-scale-up-down/ScaleupDownPSOut.png
[DeactivatedNodeList]: ./media/service-fabric-cluster-scale-up-down/DeactivatedNodeList.png

<!---HONumber=AcomDC_0114_2016-->