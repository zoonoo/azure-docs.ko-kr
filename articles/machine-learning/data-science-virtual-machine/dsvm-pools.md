---
title: Data Science Virtual Machine 풀 - Azure | Microsoft Docs
description: 팀의 공유 리소스로 Data Science VM 풀 배포
keywords: 딥 러닝, AI, 데이터 과학 도구, 데이터 과학 가상 머신, 지리 공간적 분석, 팀 데이터 과학 프로세스
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: gokuma
ms.openlocfilehash: 0740ff7542d066442146b8e80e188ad5ba49a2b5
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/21/2018
ms.locfileid: "36309401"
---
# <a name="create-a-shared-pool-of-data-science-virtual-machines"></a>Data Science Virtual Machines의 공유 풀 만들기

이 아티클에서는 팀에서 사용할 DSVM(Data Science Virtual Machines)의 공유 풀을 만들 수 있는 방법을 설명합니다. 공유 풀을 사용하는 경우 이점은 리소스 사용률이 개선되고, 공유 및 공동 작업이 원활하고, DSVM 리소스를 더 효과적으로 관리할 수 있다는 것입니다. 

다양한 메서드 및 기술을 사용하여 DSVM의 풀을 만들 수 있습니다. 이 아티클은 일괄 처리 및 대화형 VM의 풀에 중점을 둡니다.

## <a name="batch-processing-pool"></a>일괄 처리 풀
오프라인에서 작업을 일괄 처리로 실행하도록 DSVM의 풀을 설정하려는 경우 [Azure Batch AI](https://docs.microsoft.com/azure/batch-ai/) 또는 [Azure Batch](https://docs.microsoft.com/azure/batch/) 서비스를 사용할 수 있습니다. 이 아티클에서는 Azure Batch AI에 초점을 맞춥니다.

DSVM의 Ubuntu 판은 Azure Batch AI에서 이미지 중 하나로 지원됩니다. Azure Batch AI 클러스터를 만드는 Azure CLI 또는 Python SDK에서 `image` 매개 변수를 지정하고 이를 `UbuntuDSVM`으로 설정할 수 있습니다. Azure에서 사용할 수 있는 [폭 넓은 VM 인스턴스 선택 항목](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)에서 원하는 종류의 처리 노드(GPU 기반 인터페이스 및 CPU 전용 인스턴스, CPU 수 및 메모리)를 선택할 수 있습니다. 

GPU 기반 노드를 포함한 Batch AI에서 Ubuntu DSVM 이미지를 사용하는 경우, 모든 필요한 GPU 드라이버와 딥 러닝 프레임워크는 미리 설치되어 있습니다. 사전 설치로 인해 일괄 처리 노드를 준비할 때 많은 시간이 절약됩니다. 사실 Ubuntu DSVM에서 대화형으로 개발하는 경우, Batch AI 노드가 환경과 똑같은 설정 및 구성이라는 것을 확인할 수 있습니다. 

일반적으로 Batch AI 클러스터를 만들 때 모든 노드에서 탑재하는 파일 공유도 만들게 됩니다. 파일 공유는 일괄 작업 코드/스크립트 저장뿐만 아니라 데이터의 입력 및 출력에도 사용됩니다. 

Batch AI 클러스터를 만든 후에 동일한 CLI 또는 Python SDK를 사용하여 실행할 작업을 제출할 수 있습니다. 일괄 작업을 실행하는 데 사용된 시간에 대해서만 요금을 지불합니다. 

자세한 내용은 다음을 참조하세요.
* [Azure CLI](https://docs.microsoft.com/azure/batch-ai/quickstart-cli)를 사용하여 Batch AI를 관리하는 단계별 연습
* [Python](https://docs.microsoft.com/azure/batch-ai/quickstart-python)을 사용하여 Batch AI를 관리하는 단계별 연습
* [Batch AI 레시피](https://github.com/Azure/BatchAI)는 Batch AI를 사용하여 다양한 AI 및 심층 학습 프레임워크를 사용하는 방법을 설명합니다.

## <a name="interactive-vm-pool"></a>대화형 VM 풀

전체 AI/데이터 과학 팀에서 공유되는 대화형 VM의 풀을 사용하면 각 사용자 집합에 대해 전용 인스턴스를 가질 필요 없이 사용자가 DSVM의 사용 가능한 인스턴스에 로그인할 수 있습니다. 이 설정을 통해 가용성을 개선하고 리소스를 더 효과적으로 활용할 수 있습니다. 

대화형 VM 풀을 만드는 데 사용하는 기술은 [Azure 가상 머신 확장 집합](https://docs.microsoft.com/azure/virtual-machine-scale-sets/)입니다. 확장 집합을 사용하여 동일한 그룹의 부하 분산된 자동 크기 조정 VM을 만들고 관리할 수 있습니다. 

사용자는 주 풀의 IP 또는 DNS 주소에 로그인합니다. 확장 집합은 확장 집합에서 사용 가능한 DSVM으로 세션을 자동으로 라우팅합니다. 사용자가 로그인한 VM과 상관없이 유사한 환경을 원하기 때문에 확장 집합에 있는 VM의 모든 인스턴스는 Azure Files 공유 또는 NFS 공유와 같은 공유 네트워크 드라이브를 탑재합니다. 사용자의 공유 작업 영역은 일반적으로 각 인스턴스에 탑재된 공유 파일 저장소에 유지됩니다. 

Ubuntu DSVM 인스턴스를 사용하여 확장 집합을 만드는 샘플 Azure Resource Manager 템플릿은 [GitHub](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json)에서 찾을 수 있습니다. Azure Resource Manager 템플릿의 [매개 변수 파일](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.parameters.json) 샘플도 같은 위치에 있습니다. 

Azure CLI에서 매개 변수 파일에 값을 지정하여 Azure Resource Manager 템플릿의 확장 집합을 만들 수 있습니다. 

```
az group create --name [[NAME OF RESOURCE GROUP]] --location [[ Data center. For eg: "West US 2"]
az group deployment create --resource-group  [[NAME OF RESOURCE GROUP ABOVE]]  --template-uri https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json --parameters @[[PARAMETER JSON FILE]]
```
이전 명령에서는 다음 항목이 있다고 가정합니다.
* 확장 집합의 인스턴스에 지정된 값을 포함한 매개 변수 파일의 복사본
* VM 인스턴스 수
* Azure Files 공유에 대한 포인터
* 각 VM에 탑재될 저장소 계정의 자격 증명 

매개 변수 파일은 명령에서 로컬로 참조됩니다. 매개 변수를 인라인으로 전달하거나 스크립트에서 그에 대한 프롬프트를 표시할 수도 있습니다.  

이전 템플릿은 프런트 엔드 확장 집합부터 Ubuntu DSVM의 백 엔드 풀까지의 SSH 및 JupyterHub 포트를 사용할 수 있습니다. 사용자는 정상적인 방법으로 SSH 또는 JupyterHub의 VM에 로그인하면 됩니다. VM 인스턴스는 동적으로 규모 확장 또는 축소할 수 있기 때문에 모든 상태는 탑재된 Azure Files 공유에 저장되어야 합니다. 같은 방식을 사용하여 Windows DSVM의 풀을 만들 수 있습니다. 

[Azure Files 공유를 탑재하는 스크립트](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Extensions/General/mountazurefiles.sh)는 Azure Data Science VM 리포지토리에서도 GitHub로 사용할 수 있습니다. 스크립트는 매개 변수 파일의 지정된 탑재 지점에서 Azure Files 공유를 탑재합니다. 또한 스크립트는 초기 사용자의 홈 디렉터리에서 탑재된 드라이브에 대한 소프트 링크를 만듭니다. Azure Files 공유 내의 사용자 지정 Notebook 디렉터리는 `$HOME/notebooks/remote` 디렉터리에 소프트 링크되어 사용자가 해당 Jupyter Notebook에 액세스하고, 이를 실행하고, 저장할 수 있습니다. VM에 추가 사용자를 만들 때 동일한 규칙을 사용하여 Azure Files 공유에 대한 각 사용자의 Jupyter 작업 영역을 가리킬 수 있습니다. 

가상 머신 확장 집합은 자동 크기 조정 기능을 지원합니다. 추가 인스턴스를 만드는 경우 및 인스턴스를 축소하는 경우에 규칙을 설정할 수 있습니다. 예를 들어 VM을 전혀 사용하지 않는 경우 클라우드 하드웨어 사용 비용을 절약하기 위해 0개의 인스턴스로 축소할 수 있습니다. 가상 머신 확장 집합의 설명서 페이지는 [자동 크기 조정](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview)에 대한 자세한 단계를 제공합니다.

## <a name="next-steps"></a>다음 단계

* [일반적인 ID 설정](dsvm-common-identity.md)
* [클라우드 리소스에 액세스하기 위한 자격 증명을 안전하게 저장](dsvm-secure-access-keys.md)















