---
title: 자습서 - Azure Linux Virtual Machines에 대한 카나리아 배포 구성
description: 이 자습서에서는 파란색-녹색 배포 전략을 사용하여 Azure 가상 머신 그룹을 업데이트하는 CD(지속적인 배포) 파이프라인을 설정하는 방법을 알아봅니다.
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
ms.openlocfilehash: b1a57245434bb188ffaab56a8891b4b0ee27f044
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82120551"
---
# <a name="tutorial---configure-blue-green-deployment-strategy-for-azure-linux-virtual-machines"></a>자습서 - Azure Linux Virtual Machines에 대한 파란색-녹색 배포 전략 구성


## <a name="iaas---configure-cicd"></a>IaaS- CI/CD 구성 
Azure Pipelines는 가상 머신에 배포하는 데 사용할 수 있는 완전한 기능을 갖춘 CI/CD 자동화 도구 세트를 제공합니다. Azure Portal에서 Azure VM에 대한 지속적인 업데이트 파이프라인을 직접 구성할 수 있습니다. 이 문서에는 다중 머신 배포를 위한 파란색-녹색 전략을 사용하는 CI/CD 파이프라인 설정과 관련된 단계가 포함되어 있습니다. Azure Portal에서 기본적으로 지원되는 [롤링](https://aka.ms/AA7jlh8) 및 [카나리아](https://aka.ms/AA7jdrz) 같은 다른 전략을 살펴볼 수도 있습니다. 

 
 **Virtual Machines에서 CI/CD 구성**

가상 머신을 [배포 그룹](https://docs.microsoft.com/azure/devops/pipelines/release/deployment-groups)에 대상으로 추가하고 다중 머신 업데이트를 대상으로 지정할 수 있습니다. 배포가 완료되면 배포 그룹 내의 **배포 기록**에서는 VM, 파이프라인, 커밋의 순서로 추적할 수 있는 기능을 제공합니다. 
 
  
**파란색-녹색 배포**: 파란색-녹색 배포는 동일한 대기 환경을 사용하여 가동 중지 시간을 줄입니다. 언제든지 환경 중 하나가 라이브입니다. 새 릴리스를 준비할 때 녹색 환경에서 최종 테스트 단계를 완료합니다. 소프트웨어가 녹색 환경에서 작동하면 들어오는 모든 요청이 녹색 환경으로 이동하도록 트래픽을 전환합니다. 이제 파란색 환경은 유휴 상태입니다.
지속적인 업데이트 옵션을 사용하여 Azure Portal에서 "**가상 머신**"에 대한 파란색-녹색 배포를 구성할 수 있습니다. 

단계별 연습은 다음과 같습니다.

1. Azure Portal에 로그인하고, 가상 머신으로 이동합니다. 
2. VM 왼쪽 창에서 **지속적인 업데이트**로 이동합니다. **구성**을 클릭합니다. 

   ![AzDevOps_configure](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png) 
3. 구성 패널에서 **Azure DevOps 조직**을 클릭하여 기존 계정을 선택하거나 새로 만듭니다. 그런 다음, 파이프라인을 구성할 프로젝트를 선택합니다.  


   ![AzDevOps_project](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png) 
4. 배포 그룹은 물리적 환경을 나타내는 배포 대상 머신의 논리적 세트입니다(예: "Dev", "Test", "UAT" 및 "Production"). 새 배포 그룹을 만들거나 기존 배포 그룹을 선택할 수 있습니다. 
5. 가상 머신에 배포할 패키지를 게시하는 빌드 파이프라인을 선택합니다. 게시된 패키지는 패키지 루트의 `deployscripts` 폴더에 _deploy.ps1_ 또는 _deploy.sh_ 배포 스크립트가 있습니다. 이 배포 스크립트는 런타임에 Azure DevOps 파이프라인에서 실행됩니다.
6. 원하는 배포 전략을 선택합니다. **파란색-녹색**을 선택합니다.
7. 파란색-녹색 배포에 포함할 VM에 "파란색" 또는 "녹색" 태그를 추가합니다. VM이 대기 역할인 경우 "녹색" 태그, 대기 역할이 아닌 경우 "파란색" 태그를 지정합니다.
![AzDevOps_bluegreen_configure](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-configure.png)

8. **확인**을 클릭하여 지속적인 업데이트 파이프라인을 구성합니다. 이제 가상 머신에 배포하도록 지속적인 업데이트 파이프라인이 구성됩니다.
![AzDevOps_bluegreen_pipeline](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-pipeline.png)


9. Azure DevOps에서 **편집** 릴리스 파이프라인을 클릭하여 파이프라인 구성을 확인합니다. 파이프라인은 세 단계로 구성됩니다. 첫 번째 단계는 배포 그룹 단계이며, _녹색_ 태그가 지정된 VM(대기 VM)에 배포합니다. 두 번째 단계에서는 파이프라인을 일시 중지하고 수동 개입을 통해 실행이 다시 시작될 때까지 대기합니다. 사용자는 배포가 만족할 정도로 안정적이면 트래픽을 _녹색_ VM으로 리디렉션하고 파이프라인 실행을 다시 시작할 수 있습니다. 그러면 VM에서 _파란색_ 태그와 _녹색_ 태그를 바꿀 수 있습니다. 이렇게 하면 이전 애플리케이션 버전을 사용하는 VM에 _녹색_ 태그가 지정되고 다음 파이프라인 실행 시에 배포됩니다.
![AzDevOps_bluegreen_task](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-tasks.png)


10. 배포 스크립트 실행 작업은 기본적으로 게시된 패키지의 루트 디렉터리에 있는 `deployscripts` 폴더의 _deploy.ps1_ 또는 _deploy.sh_ 배포 스크립트를 실행합니다. 선택한 빌드 파이프라인이 이것을 패키지의 루트 폴더에 게시하는지 확인합니다.
![AzDevOps_publish_package](media/tutorial-deployment-strategy/package.png)




## <a name="other-deployment-strategies"></a>기타 배포 전략
- [롤링 배포 전략 구성](https://aka.ms/AA7jlh8)
- [카나리아 배포 전략 구성](https://aka.ms/AA7jdrz)

## <a name="azure-devops-project"></a>Azure DevOps 프로젝트 
어느 때보다 더 쉽게 Azure를 시작할 수 있습니다.
 
DevOps 프로젝트를 사용하면 모든 Azure 서비스에서 애플리케이션 언어, 런타임 및 Azure 서비스를 선택하는 세 단계만으로 애플리케이션을 실행할 수 있습니다.
 
[자세히 알아보기](https://azure.microsoft.com/features/devops-projects/ ).
 
## <a name="additional-resources"></a>추가 리소스 
- [DevOps 프로젝트를 사용하여 Azure Virtual Machines에 배포](https://docs.microsoft.com/azure/devops-project/azure-devops-project-vms)
- [Azure Virtual Machine Scale Set에 대한 앱의 지속적인 배포 구현](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)
