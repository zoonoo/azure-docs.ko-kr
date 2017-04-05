---
title: "Windows Server용 Azure Service Fabric 독립 실행형 패키지 | Microsoft Docs"
description: "Windows Server용 Azure Service Fabric 독립 실행형 패키지의 설명 및 내용입니다."
services: service-fabric
documentationcenter: .net
author: maburlik
manager: timlt
editor: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/15/2017
ms.author: chackdan;maburlik
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: a09ee1955717d7e042c1df3382c4cecd40069e3a
ms.lasthandoff: 03/29/2017


---

# <a name="package-contents-of-service-fabric-standalone-package-for-windows-server"></a>Windows Server용 Service Fabric 독립 실행형 패키지의 패키지 내용
[다운로드된](http://go.microsoft.com/fwlink/?LinkId=730690) Service Fabric 독립 실행형 패키지에서 다음 파일을 찾을 수 있습니다.

| **파일 이름** | **간단한 설명** |
| --- | --- |
| CreateServiceFabricCluster.ps1 |ClusterConfig.json에서 설정을 사용하여 클러스터를 만드는 PowerShell 스크립트입니다. |
| RemoveServiceFabricCluster.ps1 |ClusterConfig.json에서 설정을 사용하여 클러스터를 제거하는 PowerShell 스크립트입니다. |
| AddNode.ps1 |현재 컴퓨터의 기존 배포된 클러스터에 노드를 추가하는 PowerShell 스크립트입니다. |
| RemoveNode.ps1 |현재 컴퓨터의 기존 배포된 클러스터에서 노드를 제거하는 PowerShell 스크립트입니다. |
| CleanFabric.ps1 |현재 컴퓨터에서 독립 실행형 Service Fabric 설치를 정리하기 위한 PowerShell 스크립트입니다. 이전 MSI 설치는 자체 연결된 제거 프로그램을 사용하여 제거됩니다. |
| TestConfiguration.ps1 |Cluster.json에 지정된 대로 인프라를 분석하는 PowerShell 스크립트입니다. |
| DownloadServiceFabricRuntimePackage.ps1 |배포 컴퓨터가 인터넷에 연결되지 않은 시나리오에 대한 대역 외 최신 런타임 패키지 다운로드에 사용되는 PowerShell 스크립트입니다. |
| DeploymentComponentsAutoextractor.exe |독립 실행형 패키지 스크립트에서 사용하는 배포 구성 요소를 포함하는 자동 압축 풀기 아카이브입니다. |
| EULA_ENU.txt |Microsoft Azure Service Fabric 독립 실행형 Windows Server 패키지를 사용하기 위한 사용 조건입니다. 현재 [EULA 사본을 다운로드](http://go.microsoft.com/fwlink/?LinkID=733084)할 수 있습니다. |
| Readme.txt |릴리스 정보 및 기본 설치 지침에 연결합니다. 이 문서에 포함된 지침의 일부입니다. |
| ThirdPartyNotice.rtf |패키지에 있는 타사 소프트웨어에 대한 고지입니다. |

**템플릿** 
| **파일 이름** | **간단한 설명** |
| --- | --- |
| ClusterConfig.Unsecure.DevCluster.json |클러스터에 있는 각 노드에 대한 정보를 포함하여 3개 노드의 비보안 단일 컴퓨터(또는 가상 컴퓨터) 개발 클러스터에 대한 설정이 포함된 클러스터 구성 샘플 파일입니다. |
| ClusterConfig.Unsecure.MultiMachine.json |클러스터에 있는 각 컴퓨터에 대한 정보를 포함하여 비보안 다중 컴퓨터(또는 가상 컴퓨터) 클러스터에 대한 설정이 포함된 클러스터 구성 샘플 파일입니다. |
| ClusterConfig.Windows.DevCluster.json |클러스터에 있는 각 노드에 대한 정보를 포함하여 3개 노드의 보안 단일 컴퓨터(또는 가상 컴퓨터) 개발 클러스터에 대한 모든 설정이 포함된 클러스터 구성 샘플 파일입니다. 클러스터는 [Windows ID](https://msdn.microsoft.com/library/ff649396.aspx)를 사용하여 보안이 유지됩니다. |
| ClusterConfig.Windows.MultiMachine.json |보안 클러스터에 있는 각 컴퓨터에 대한 정보를 포함하여 보안 다중 컴퓨터(또는 가상 컴퓨터) 클러스터에 대한 모든 설정이 포함된 클러스터 구성 샘플 파일입니다. 클러스터는 [Windows ID](https://msdn.microsoft.com/library/ff649396.aspx)를 사용하여 보안이 유지됩니다. |
| ClusterConfig.x509.DevCluster.json |클러스터에 있는 각 노드에 대한 정보를 포함하여 3개 노드의 보안 단일 컴퓨터(또는 가상 컴퓨터) 개발 클러스터에 대한 모든 설정이 포함된 클러스터 구성 샘플 파일입니다. 클러스터는 x509 인증서를 사용하여 보안이 유지됩니다. |
| ClusterConfig.x509.MultiMachine.json |보안 클러스터의 각 노드에 대한 정보를 포함하여 보안 다중 컴퓨터(또는 가상 컴퓨터) 클러스터에 대한 모든 설정이 포함된 클러스터 구성 샘플 파일입니다. 클러스터는 x509 인증서를 사용하여 보안이 유지됩니다. |
| ClusterConfig.gMSA.Windows.MultiMachine.json |보안 클러스터의 각 노드에 대한 정보를 포함하여 보안 다중 컴퓨터(또는 가상 컴퓨터) 클러스터에 대한 모든 설정이 포함된 클러스터 구성 샘플 파일입니다. 클러스터는 [그룹 관리 서비스 계정](https://technet.microsoft.com/en-us/library/jj128431(v=ws.11).aspx)을 사용하여 보안됩니다. |

# <a name="cluster-configuration-samples"></a>클러스터 구성 샘플
최신 버전의 클러스터 구성 템플릿은 [독립 실행형 클러스터 구성 샘플](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples) GitHub 페이지에서 찾을 수 있습니다.

## <a name="related"></a>관련 항목
* [독립 실행형 Azure Service Fabric 클러스터 만들기](service-fabric-cluster-creation-for-windows-server.md)
* [Service Fabric 클러스터 보안 시나리오](service-fabric-windows-cluster-windows-security.md)

