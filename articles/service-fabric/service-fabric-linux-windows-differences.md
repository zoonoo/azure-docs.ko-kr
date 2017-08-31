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
ms.date: 8/9/2017
ms.author: subramar
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: 7b80bb7d4a4e6a1b4cf47ce87200f47339785c53
ms.contentlocale: ko-kr
ms.lasthandoff: 08/24/2017

---
# <a name="differences-between-service-fabric-on-linux-preview-and-windows-generally-available"></a>Linux(미리 보기)와 Windows(일반적으로 사용 가능)의 Service Fabric 간의 차이점

Linux의 Service Fabric이 미리 보기이므로 일부 기능은 Windows에서는 지원되지만 Linux에서는 지원되지 않습니다. 결국 Linux의 Service Fabric이 일반적으로 사용할 수 있게 되는 경우 기능 집합이 동등해 집니다. 향후 릴리스에서는 이 기능 차이가 축소됩니다. 제공되는 최신 릴리스(즉, Windows에서 버전 5.6 및 Linux에서 버전 5.5) 간에는 다음과 같은 차이점이 있습니다. 

* Reliable Collections(및 Reliable Stateful Services) 
* ReverseProxy 
* 독립 실행형 설치 관리자 
* 매니페스트 파일에 대한 XML 스키마 유효성 검사 
* 콘솔 리디렉션 
* FAS(오류 분석 서비스)
* Docker Compose 및 볼륨, 컨테이너에 대한 로깅 드라이버 
* 컨테이너 및 서비스에 대한 리소스 관리 
* DNS 서비스
* Azure Active Directory 지원
* 특정 Powershell 명령에 있는 동일한 CLI 명령 
* PowerShell 명령의 하위 집합만을 (다음 섹션에서 확장된 대로) Linux 클러스터에서 실행할 수 있습니다.

>[!NOTE]
>콘솔 리디렉션은 Windows의 경우에도 프로덕션 클러스터에서 지원되지 않습니다.

Windows와 Linux의 개발 도구도 서로 다릅니다. VisualStudio, Powershell, VSTS 및 ETW는 Windows에서 사용되고 Yeoman, Eclipse, Jenkins 및 LTTng는 Linux에서 사용됩니다.

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
* [Service Fabric CLI를 사용하여 응용 프로그램 관리](service-fabric-application-lifecycle-sfctl.md)

