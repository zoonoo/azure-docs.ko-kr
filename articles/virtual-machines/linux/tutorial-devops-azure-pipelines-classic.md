---
title: 자습서 - Azure의 IaaS 및 PaaS를 위한 통합 DevOps
description: 이 자습서에서는 Azure Pipelines를 사용하여 앱의 CI(지속적인 통합) 및 CD(지속적인 배포)를 Azure VM에 설정하는 방법을 알아봅니다.
author: ushan
manager: jpconnock
tags: azure-devops-pipelines
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: azure-pipelines
ms.workload: infrastructure
ms.date: 1/16/2020
ms.author: ushan
ms.custom: devops
ms.openlocfilehash: 5707a99b329915b35131fe793b0dfabd02348677
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/28/2020
ms.locfileid: "77912531"
---
# <a name="tutorial-integrated-devops-for-iaas-and-paas-on-azure"></a>자습서: Azure의 IaaS 및 PaaS를 위한 통합 DevOps

Azure에서 엔드투엔드 솔루션을 사용하는 경우 팀은 각 애플리케이션 수명 주기 단계(계획, 개발, 제공 및 운영)에서 DevOps 사례를 구현할 수 있습니다. 

다음은 클라우드 워크로드를 간소화하고 놀랍도록 강력한 시나리오를 지원하기 위해 결합할 수 있는 몇 가지 Azure 서비스입니다.
이러한 기술을 사용자 및 프로세스와 결합하여 팀에서 고객에게 가치를 지속적으로 제공할 수 있습니다. 

- Azure: https://portal.azure.com - 클라우드 워크로드를 빌드하기 위한 포털입니다. 간단한 웹앱에서 복잡한 클라우드 애플리케이션까지 모든 항목을 관리하고 모니터링합니다. 
- Azure DevOps: https://dev.azure.com - 최신 개발 서비스 세트를 사용하여 더 스마트하게 계획하고, 더 효율적으로 협업하며, 더 빠르게 제공합니다. 
- Azure Machine Learning studio: https://ml.azure.com - 데이터를 준비하고, 기계 학습 모델을 학습시키고 배포합니다. 
 

Azure DevOps는 모든 Azure 리소스에 대한 지속적인 통합과 지속적인 업데이트를 사용하여 DevOps 프로세스의 각 부분을 자동화하는 기본 제공 Azure 서비스입니다.
앱에서 가상 머신, 웹앱, Kubernetes 또는 기타 리소스를 사용하는지 여부에 관계없이 Azure와 Azure DevOps를 사용하여 코드 기반 인프라, 지속적인 통합, 지속적인 테스트, 지속적인 업데이트 및 지속적인 모니터링을 구현할 수 있습니다.  
![AzDevOps_portalView](media/tutorial-devops-azure-pipelines-classic/azdevops-view.png) 
 
 
## <a name="iaas---configure-cicd"></a>IaaS- CI/CD 구성 
Azure Pipelines는 가상 머신에 배포하는 데 사용할 수 있는 완전한 기능을 갖춘 CI/CD 자동화 도구 세트를 제공합니다. Azure Portal에서 Azure VM에 대한 지속적인 업데이트 파이프라인을 직접 구성할 수 있습니다. 이 문서에는 Azure Portal에서 다중 머신을 배포하기 위한 CI/CD 파이프라인 설정과 관련된 단계가 포함되어 있습니다. Virtual Machines에서 CI/CD를 구성합니다.

가상 머신을 대상으로 [배포 그룹](https://docs.microsoft.com/azure/devops/pipelines/release/deployment-groups)에 추가할 수 있으며 다중 머신 롤링 업데이트를 대상으로 지정할 수 있습니다. 배포 그룹 내의 배포 기록 보기는 VM, 파이프라인, 커밋의 순서로 추적할 수 있는 기능을 제공합니다. 
 
**롤링 업데이트**: 롤링 배포는 이전 버전의 애플리케이션 인스턴스를 각 반복에서 고정된 머신 세트(롤링 세트)에 있는 새 버전의 애플리케이션 인스턴스로 바꿉니다. 가상 머신에 대한 롤링 업데이트를 구성하는 방법을 연습해 보겠습니다.  
지속적인 업데이트 옵션을 사용하여 Azure Portal 내에서 "**가상 머신**"에 대한 롤링 업데이트를 구성할 수 있습니다. 

단계별 연습은 다음과 같습니다. 
1. Azure Portal에 로그인하고, 가상 머신으로 이동합니다. 
2. VM의 왼쪽 창에서  **지속적인 업데이트** 메뉴로 이동합니다. 그런 다음,  **구성**을 클릭합니다. 
   ![AzDevOps_configure](media/tutorial-devops-azure-pipelines-classic/azdevops-configure.png) 
3. 구성 패널에서 "Azure DevOps 조직"을 클릭하여 기존 계정을 선택하거나 새로 만듭니다. 그런 다음, 파이프라인을 구성할 프로젝트를 선택합니다.  
   ![AzDevOps_project](media/tutorial-devops-azure-pipelines-classic/azdevops-project.png) 
4. 배포 그룹은 물리적 환경을 나타내는 배포 대상 머신의 논리적 세트입니다(예: "Dev", "Test", "UAT" 및 "Production"). 새 배포 그룹을 만들거나 기존 배포 그룹을 선택할 수 있습니다. 필요에 따라 역할을 사용하여 태그를 머신에 지정할 수 있습니다. 예를 들어 'web', 'db' 등이 있습니다.  
5. 대화 상자에서 **확인**을 클릭하여 지속적인 업데이트 파이프라인을 구성합니다. 
6. 완료되면 가상 머신에 배포하도록 지속적인 업데이트 파이프라인이 구성됩니다.  
   ![AzDevOps_pipeline](media/tutorial-devops-azure-pipelines-classic/azdevops-pipeline.png)
7. 가상 머신에 대한 배포가 진행되고 있음을 확인할 수 있습니다. 링크를 클릭하여 파이프라인으로 이동할 수 있습니다. **Release-1**을 클릭하여 배포를 확인합니다. 또는 **편집**을 클릭하여 릴리스 파이프라인 정의를 수정할 수 있습니다. 
8. 여러 개의 VM을 구성하는 경우 배포 그룹에 추가할 다른 VM에 대해 2-5단계를 반복합니다. 
9. 완료되면 파이프라인 정의를 클릭하고, Azure DevOps 조직으로 이동한 다음, 릴리스 파이프라인 **편집**을 클릭합니다. 
   ![AzDevOps_edit_pipeline](media/tutorial-devops-azure-pipelines-classic/azdevops-edit-pipeline.png)
10. **dev** 단계에서 **1 작업, 1 태스크** 링크를 클릭합니다. **배포** 단계를 클릭합니다.  
   ![AzDevOps_deploymentGroup](media/tutorial-devops-azure-pipelines-classic/azdevops-deployment-group.png)
11. 오른쪽의 구성 창에서 기본적으로 파이프라인이 모든 대상에 대해 롤링 업데이트를 병렬로 수행하도록 구성되어 있음을 확인할 수 있습니다. 슬라이더를 사용하여 한 번에 하나씩 또는 백분율 단위로 배포하도록 구성할 수 있습니다.  
  
  
**카나리아**는 작은 사용자 하위 세트에 대한 변경 내용을 천천히 롤아웃하여 위험을 줄입니다. 새 버전을 더 신뢰할 수 있으므로 이 버전을 인프라의 더 많은 서버에 릴리스하고 더 많은 사용자를 해당 버전으로 라우팅할 수 있습니다. 지속적인 업데이트 옵션을 사용하여 Azure Portal 내에서 "**가상 머신**"에 대한 카나리아 배포를 구성할 수 있습니다. 단계별 연습은 다음과 같습니다. 
1. Azure Portal에 로그인하고, 가상 머신으로 이동합니다. 
2. 이전 섹션의 2-5단계에 따라 여러 VM을 배포 그룹에 추가합니다. 
3. 사용자 지정 태그를 카나리아 배포의 일부가 될 VM에 추가합니다. 예를 들어, "canary"입니다.
4. VM에 대한 파이프라인이 구성되면 파이프라인을 클릭하고, Azure DevOps 조직을 시작하고, 파이프라인을 **편집**한 다음, **dev** 단계로 이동합니다. "canary" 태그를 필터에 추가합니다. 
5. 다른 배포 그룹 단계를 추가하고, 태그를 사용하여 배포 그룹의 나머지 VM을 대상으로 지정하는 단계를 구성합니다.  
6. 필요에 따라 카나리아 배포를 승격/거부할 수 있는 수동 유효성 검사 단계를 구성합니다. 
   ![AzDevOps_Canary](media/tutorial-devops-azure-pipelines-classic/azdevops-canary-deploy.png)

**파란색-녹색**은 동일한 대기 환경을 사용하여 배포 가동 중지 시간을 줄입니다. 언제든지 환경 중 하나가 라이브입니다. 새 릴리스를 준비할 때 녹색 환경에서 최종 테스트 단계를 수행합니다. 소프트웨어가 녹색 환경에서 작동하면 들어오는 모든 요청이 녹색 환경으로 이동하도록 트래픽을 전환합니다. 이제 파란색 환경은 유휴 상태입니다.
지속적인 업데이트 옵션을 사용하여 Azure Portal에서 "**가상 머신**"에 대한 파란색-녹색 배포를 구성할 수 있습니다. 

단계별 연습은 다음과 같습니다. 

1. Azure Portal에 로그인하고, Virtual Machine으로 이동합니다. 
2. **롤링 업데이트** 섹션 아래의 2-5단계에 따라 여러 VM을 배포 그룹에 추가합니다. 사용자 지정 태그를 파란색-녹색 배포의 일부가 될 VM에 추가합니다. 예를 들어 대기 역할에 해당하는 VM의 경우 "파란색" 또는 "녹색"입니다. 
3. VM에 대한 파이프라인이 구성되면 파이프라인을 클릭하고, Azure DevOps 조직을 시작하고, 파이프라인을 **편집**한 다음, **dev** 단계로 이동합니다. "green" 태그를 필터에 추가합니다. 
4. 에이전트 없는 단계를 추가하고, 수동 유효성 검사 단계 및 호출-REST API 단계를 사용하여 태그를 교환하는 단계를 구성합니다. 
   ![AzDevOps_BlueGreen](media/tutorial-devops-azure-pipelines-classic/azdevops-blue-green-deploy.png)
 
 
## <a name="azure-devops-project"></a>Azure DevOps 프로젝트 
어느 때보다 더 쉽게 Azure를 시작할 수 있습니다.
 
DevOps 프로젝트를 사용하면 모든 Azure 서비스에서 애플리케이션 언어, 런타임 및 Azure 서비스를 선택하는 세 단계만으로 애플리케이션을 실행할 수 있습니다.
 
[자세히 알아보기](https://azure.microsoft.com/features/devops-projects/ ).
 
## <a name="additional-resources"></a>추가 리소스 
- [DevOps 프로젝트를 사용하여 Azure Virtual Machines에 배포](https://docs.microsoft.com/azure/devops-project/azure-devops-project-vms)
- [Azure Virtual Machine Scale Set에 대한 앱의 지속적인 배포 구현](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)
