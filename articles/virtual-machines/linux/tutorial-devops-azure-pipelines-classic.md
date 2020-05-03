---
title: 자습서 - Azure Linux Virtual Machines에 대한 롤링 배포 구성
description: 이 자습서에서는 롤링 배포 전략을 사용하여 Azure Linux Virtual Machines 그룹을 증분 업데이트하는 CD(연속 배포) 파이프라인을 설정하는 방법을 알아봅니다.
author: moala
manager: jpconnock
tags: azure-devops-pipelines
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: azure-pipelines
ms.workload: infrastructure
ms.date: 4/10/2020
ms.author: moala
ms.custom: devops
ms.openlocfilehash: 75888b1ebbda33891296fe0b54c5d204955e32a3
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82113489"
---
# <a name="tutorial---configure-rolling-deployment-strategy-for-azure-linux-virtual-machines"></a>자습서 - Azure Linux Virtual Machines에 대한 롤링 배포 전략 구성

Azure DevOps는 모든 Azure 리소스에 대한 지속적인 통합과 지속적인 업데이트를 사용하여 DevOps 프로세스의 각 부분을 자동화하는 기본 제공 Azure 서비스입니다.
앱에서 가상 머신, 웹앱, Kubernetes 또는 기타 리소스를 사용하는지 여부에 관계없이 Azure와 Azure DevOps를 사용하여 코드 기반 인프라, 연속 통합, 지속적인 테스트, 지속적인 업데이트 및 지속적인 모니터링을 구현할 수 있습니다.  

![AzDevOps_portalView](media/tutorial-devops-azure-pipelines-classic/azdevops-view.png) 


## <a name="iaas---configure-cicd"></a>IaaS- CI/CD 구성 
Azure Pipelines는 가상 머신에 배포하는 데 사용할 수 있는 완전한 기능을 갖춘 CI/CD 자동화 도구 세트를 제공합니다. Azure Portal에서 Azure VM에 대한 지속적인 업데이트 파이프라인을 직접 구성할 수 있습니다. 이 문서에는 Azure Portal에서 다중 머신 배포를 롤링하기 위한 CI/CD 파이프라인 설정과 관련된 단계가 포함되어 있습니다. Azure Portal에서 기본적으로 지원되는 [카나리아](https://aka.ms/AA7jdrz) 및 [청녹색](https://aka.ms/AA83fwu)과 같은 다른 전략을 살펴볼 수도 있습니다. 


**Virtual Machines에서 CI/CD 구성**

가상 머신을 [배포 그룹](https://docs.microsoft.com/azure/devops/pipelines/release/deployment-groups)에 대상으로 추가하고 다중 머신 업데이트를 대상으로 지정할 수 있습니다. 배포가 완료되면 배포 그룹 내의 **배포 기록**은 VM, 파이프라인, 커밋의 순서로 추적할 수 있는 기능을 제공합니다. 
 

**롤링 배포**: 롤링 배포는 이전 버전의 애플리케이션 인스턴스를 각 반복에서 고정된 머신 세트(롤링 세트)에 있는 새 버전의 애플리케이션 인스턴스로 바꿉니다. 가상 머신에 대한 롤링 업데이트를 구성하는 방법을 연습해 보겠습니다.  
지속적인 업데이트 옵션을 사용하여 Azure Portal 내에서 "**가상 머신**"에 대한 롤링 업데이트를 구성할 수 있습니다. 

단계별 연습은 다음과 같습니다. 
1. Azure Portal에 로그인하고, 가상 머신으로 이동합니다. 
2. VM의 왼쪽 창에서  **지속적인 업데이트** 메뉴로 이동합니다. 그런 다음,  **구성**을 클릭합니다. 

   ![AzDevOps_configure](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png) 
3. 구성 패널에서 "Azure DevOps 조직"을 클릭하여 기존 계정을 선택하거나 새로 만듭니다. 그런 다음, 파이프라인을 구성할 프로젝트를 선택합니다.  


   ![AzDevOps_project](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png) 
4. 배포 그룹은 물리적 환경을 나타내는 배포 대상 머신의 논리적 세트입니다(예: "Dev", "Test", "UAT" 및 "Production"). 새 배포 그룹을 만들거나 기존 배포 그룹을 선택할 수 있습니다. 
5. 가상 머신에 배포할 패키지를 게시하는 빌드 파이프라인을 선택합니다. 게시된 패키지는 패키지 루트의 `deployscripts` 폴더에 _deploy.ps1_ 또는 _deploy.sh_ 배포 스크립트가 있어야 합니다. 이 배포 스크립트는 런타임 시 Azure DevOps 파이프라인에서 실행됩니다.
6. 원하는 배포 전략을 선택합니다. 여기서는 '롤링'을 선택하겠습니다.
7. 필요에 따라 머신에 역할을 태그로 지정할 수 있습니다. 예를 들어 'web', 'db' 등이 있습니다. 이렇게 하면 특정 역할만 있는 VM을 대상으로 지정할 수 있습니다.
8. 대화 상자에서 **확인**을 클릭하여 지속적인 업데이트 파이프라인을 구성합니다. 
9. 완료되면 가상 머신에 배포하도록 지속적인 업데이트 파이프라인이 구성됩니다.  


   ![AzDevOps_pipeline](media/tutorial-devops-azure-pipelines-classic/azure-devops-deployment-history.png)
10. 가상 머신에 대한 배포가 진행되고 있음을 확인할 수 있습니다. 링크를 클릭하여 파이프라인으로 이동할 수 있습니다. **Release-1**을 클릭하여 배포를 확인합니다. 또는 **편집**을 클릭하여 릴리스 파이프라인 정의를 수정할 수 있습니다. 
11. 여러 개의 VM을 구성하는 경우 배포 그룹에 추가할 다른 VM에 대해 2-4단계를 반복합니다. 파이프라인 실행이 이미 있는 배포 그룹을 선택하는 경우 새 파이프라인을 만들지 않고 VM이 배포 그룹에 추가됩니다. 
12. 완료되면 파이프라인 정의를 클릭하고, Azure DevOps 조직으로 이동한 다음, 릴리스 파이프라인 **편집**을 클릭합니다. 
   ![AzDevOps_edit_pipeline](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling-pipeline.png)
13. **dev** 단계에서 **1 작업, 1 태스크** 링크를 클릭합니다. **배포** 단계를 클릭합니다.
   ![AzDevOps_deploymentGroup](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling-pipeline-tasks.png)
14. 오른쪽의 구성 창에서 각 반복에 병렬로 배포할 머신 수를 지정할 수 있습니다. 한 번에 여러 머신에 배포하려는 경우 슬라이더를 사용하여 백분율로 지정할 수 있습니다.  

15. 배포 스크립트 실행 작업은 기본적으로 게시된 패키지의 루트 디렉터리에 있는 'deployscripts' 폴더의 _deploy.ps1_ 또는 _deploy.sh_ 배포 스크립트를 실행합니다.  
![AzDevOps_publish_package](media/tutorial-deployment-strategy/package.png)

## <a name="other-deployment-strategies"></a>기타 배포 전략

- [카나리아 배포 전략 구성](https://aka.ms/AA7jdrz)
- [청록색 배포 전략 구성](https://aka.ms/AA83fwu)

 
## <a name="azure-devops-project"></a>Azure DevOps 프로젝트 
어느 때보다 더 쉽게 Azure를 시작할 수 있습니다.
 
DevOps 프로젝트를 사용하면 모든 Azure 서비스에서 애플리케이션 언어, 런타임 및 Azure 서비스를 선택하는 세 단계만으로 애플리케이션을 실행할 수 있습니다.
 
[자세히 알아보기](https://azure.microsoft.com/features/devops-projects/ ).
 
## <a name="additional-resources"></a>추가 리소스 
- [DevOps 프로젝트를 사용하여 Azure Virtual Machines에 배포](https://docs.microsoft.com/azure/devops-project/azure-devops-project-vms)
- [Azure Virtual Machine Scale Set에 대한 앱의 지속적인 배포 구현](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)
