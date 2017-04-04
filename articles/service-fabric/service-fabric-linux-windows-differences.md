---
title: "Linux와 Windows 간의 Azure Service Fabric 차이점 | Microsoft Docs"
description: "Linux의 Azure Service Fabric 미리 보기와 Windows의 Azure Service Fabric의 차이점입니다."
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: d552c8cd-67d1-45e8-91dc-871853f44fc6
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/23/2017
ms.author: subramar
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 5faf7dc0b544f6fe2f83565cc368e218c6df35af
ms.lasthandoff: 03/29/2017


---
# <a name="differences-between-service-fabric-on-linux-preview-and-windows-generally-available"></a>Linux(미리 보기)와 Windows(일반적으로 사용 가능)의 Service Fabric 간의 차이점

Linux의 Service Fabric이 미리 보기 상태이므로 Linux가 아닌 Windows에서만 지원되는 몇 가지 기능도 있습니다. 결국 Linux의 Service Fabric이 일반적으로 사용할 수 있게 되는 경우 기능 집합이 동등해 집니다.

* 신뢰할 수 있는 컬렉션(및 신뢰할 수 있는 상태 저장 서비스)는 Linux에서 지원되지 않습니다.
* ReverseProxy는 Linux에서 사용할 수 없습니다.
* 독립 실행형 설치 관리자는 Linux에서 사용할 수 없습니다.
* 매니페스트 파일에 대한 XML 스키마 유효성 검사는 Linux에서 수행되지 않습니다. 
* 콘솔 리디렉션은 Linux에서 지원되지 않습니다. 
* FAS(오류 분석 서비스)는 Linux에서 사용할 수 없습니다.
* Azure Active Directory는 Linux에서 지원되지 않습니다.
* PowerShell 명령에 있는 동일한 일부 CLI 명령을 사용할 수 없습니다.
* PowerShell 명령의 하위 집합만을 (다음 섹션에서 확장된 대로) Linux 클러스터에서 실행할 수 있습니다.

>[!NOTE]
>콘솔 리디렉션은 Windows의 경우에도 프로덕션 클러스터에서 지원되지 않습니다.

개발 도구는 Windows에서 사용되는 Visual Studio, PowerShell, VSTS 및 ETW와 Linux에서 사용되는 Yeoman, Eclipse, Jenkins 및 LTTng의 경우가 다릅니다.

## <a name="powershell-cmdlets-that-do-not-work-against-a-linux-service-fabric-cluster"></a>Linux Service Fabric 클러스터에서 작동하지 않는 PowerShell cmdlet

* Invoke-ServiceFabricChaosTestScenario
* Invoke-ServiceFabricFailoverTestScenario
* Invoke-ServiceFabricPartitionDataLoss
* Invoke-ServiceFabricPartitionQuorumLoss
* Restart-ServiceFabricPartition
* Start-ServiceFabricNode
* Stop-ServiceFabricNode
* Get-ServiceFabricImageStoreContent
* Get-ServiceFabricChaosReport
* Get-ServiceFabricNodeTransitionProgress
* Get-ServiceFabricPartitionDataLossProgress
* Get-ServiceFabricPartitionQuorumLossProgress
* Get-ServiceFabricPartitionRestartProgress
* Get-ServiceFabricTestCommandStatusList
* Remove-ServiceFabricTestState
* Start-ServiceFabricChaos
* Start-ServiceFabricNodeTransition
* Start-ServiceFabricPartitionDataLoss
* Start-ServiceFabricPartitionQuorumLoss
* Start-ServiceFabricPartitionRestart
* Stop-ServiceFabricChaos
* Stop-ServiceFabricTestCommand
* Cmd
* Get-ServiceFabricNodeConfiguration
* Get-ServiceFabricClusterConfiguration
* Get-ServiceFabricClusterConfigurationUpgradeStatus
* Get-ServiceFabricPackageDebugParameters
* New-ServiceFabricPackageDebugParameter
* New-ServiceFabricPackageSharingPolicy
* Add-ServiceFabricNode
* Copy-ServiceFabricClusterPackage
* Get-ServiceFabricRuntimeSupportedVersion
* Get-ServiceFabricRuntimeUpgradeVersion
* New-ServiceFabricCluster
* New-ServiceFabricNodeConfiguration
* Remove-ServiceFabricCluster
* Remove-ServiceFabricClusterPackage
* Remove-ServiceFabricNodeConfiguration
* Test-ServiceFabricClusterManifest
* Test-ServiceFabricConfiguration
* Update-ServiceFabricNodeConfiguration
* Approve-ServiceFabricRepairTask
* Complete-ServiceFabricRepairTask
* Get-ServiceFabricRepairTask
* Invoke-ServiceFabricDecryptText
* Invoke-ServiceFabricEncryptSecret
* Invoke-ServiceFabricEncryptText
* Invoke-ServiceFabricInfrastructureCommand
* Invoke-ServiceFabricInfrastructureQuery
* Remove-ServiceFabricRepairTask
* Start-ServiceFabricRepairTask
* Stop-ServiceFabricRepairTask
* Update-ServiceFabricRepairTaskHealthPolicy



## <a name="next-steps"></a>다음 단계
* [Linux에서 개발 환경 준비](service-fabric-get-started-linux.md)
* [OSX에서 개발 환경 준비](service-fabric-get-started-mac.md)
* [Yeoman을 사용하여 Linux에서 첫 번째 Service Fabric Java 응용 프로그램 만들기 및 배포](service-fabric-create-your-first-linux-application-with-java.md)
* [Eclipse용 Service Fabric 플러그 인을 사용하여 Linux에서 첫 번째 Service Fabric Java 응용 프로그램 만들기 및 배포](service-fabric-get-started-eclipse.md)
* [Linux에서 첫 번째 CSharp 응용 프로그램 만들기](service-fabric-create-your-first-linux-application-with-csharp.md)
* [Azure CLI를 사용하여 Service Fabric 응용 프로그램 관리](service-fabric-azure-cli.md)

