---
title: 'AI(인공 지능) 응용 프로그램에 대한 DevOps: Docker, Kubernetes 및 Python Flask 응용 프로그램을 사용하여 Azure에서 지속적인 통합 파이프라인 만들기'
description: 'AI(인공 지능) 응용 프로그램에 대한 DevOps: Docker 및 Kubernetes를 사용하여 Azure에서 지속적인 통합 파이프라인 만들기'
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 05/22/2018
ms.author: tdsp
ms.custom: (previous author=jainr, ms.author=jainr)
ms.openlocfilehash: c232680d5d1bf0eb761ff974ebf6608b67922f33
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/28/2018
ms.locfileid: "52496755"
---
# <a name="devops-for-artificial-intelligence-ai-applications-creating-continuous-integration-pipeline-on-azure-using-docker-and-kubernetes"></a>AI(인공 지능) 응용 프로그램에 대한 DevOps: Docker 및 Kubernetes를 사용하여 Azure에서 지속적인 통합 파이프라인 만들기
AI 응용 프로그램의 경우 대개 Machine Learning 모델을 빌드하는 데이터 과학자와 응용 프로그램을 빌드하고 사용할 최종 사용자에게 노출하는 앱 개발자라는 두 가지 작업 스트림이 있습니다. 이 문서에서는 AI 응용 프로그램을 위한 CI(지속적인 통합)/CD(지속적인 배포) 파이프라인을 구현하는 방법을 설명합니다. AI 응용 프로그램은 미리 학습된 ML(Machine Learning) 모델이 포함된 응용 프로그램 코드의 조합입니다. 이 문서의 경우 개인 Azure blob 저장소 계정에서 미리 학습된 모델을 페치합니다. 이는 AWS S3 계정에도 적용될 수 있습니다. 문서에서는 간단한 python flask 웹 응용 프로그램을 사용합니다.

> [!NOTE]
> 이는 CI/CD를 수행할 수 있는 몇 가지 방법 중 하나입니다. 아래에 언급된 도구 및 기타 필수 구성 요소에 대한 대안들이 있습니다. 추가 콘텐츠를 개발하는 대로 게시할 예정입니다.
>
>

## <a name="github-repository-with-document-and-code"></a>문서 및 코드가 포함된 GitHub 리포지토리
소스 코드를 [GitHub](https://github.com/Azure/DevOps-For-AI-Apps)에서 다운로드할 수 있습니다. [자세한 자습서](https://github.com/Azure/DevOps-For-AI-Apps/blob/master/Tutorial.md)도 제공됩니다.

## <a name="pre-requisites"></a>필수 조건
다음은 아래에 설명된 CI/CD 파이프라인을 수행하기 위한 필수 구성 요소입니다.
* [Azure DevOps 조직](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student)
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
* [Kubernetes를 실행하는 AKS(Azure Container Service) 클러스터](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-tutorial-kubernetes-deploy-cluster)
* [ACR(Azure Container Registry) 계정](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-portal)
* [Kubernetes 클러스터에 대해 명령을 실행하는 Kubectl을 설치합니다.](https://kubernetes.io/docs/tasks/tools/install-kubectl/) 이는 ACS 클러스터에서 구성을 페치하기 위해 필요합니다. 
* GitHub 계정으로 리포지토리를 포크합니다.

## <a name="description-of-the-cicd-pipeline"></a>CI/CD 파이프라인의 설명
파이프라인은 각 새로운 커밋에 대해 시작하고, 테스트 도구 모음을 실행하며, 테스트 통과가 최신 빌드를 사용하는 경우 Docker 컨테이너에 해당 항목을 패키지합니다. 그런 다음, 컨테이너는 ACS(Azure Container Service)를 사용하여 배포되고 이미지는 ACR(Azure Container Registry)에 안전하게 저장됩니다. ACS는 컨테이너 클러스터를 관리하기 위해 Kubernetes를 실행하지만 사용자가 Docker Swarm 또는 Mesos를 선택할 수도 있습니다.

응용 프로그램이 Azure Storage 계정에서 최신 모델을 안전하게 끌어오고 응용 프로그램의 일부로 패키지합니다. 배포된 응용 프로그램에는 앱 코드와 단일 컨테이너로 패키지된 ML 모델이 있습니다. 이는 프로덕션 앱이 항상 최신 ML 모델의 최신 코드를 실행할 수 있도록 앱 개발자와 데이터 과학자를 분리시킵니다.

파이프라인 아키텍처는 다음과 같습니다. 

![아키텍처](./media/ci-cd-flask/Architecture.PNG?raw=true)

## <a name="steps-of-the-cicd-pipeline"></a>CI/CD 파이프라인의 단계
1. 개발자는 응용 프로그램 코드 중 선택한 IDE에서 작업합니다.
2. 선택한 소스 제어에 대한 코드를 커밋합니다(Azure DevOps에 다양한 소스 제어를 위한 지원이 있음).
3. 이와 별도로, 데이터 과학자는 자신의 모델을 개발합니다.
4. 만족스러우면 모델을 모델 리포지토리에 게시합니다. 이 경우 blob 저장소 계정을 사용합니다. 
5. 빌드가 GitHub의 커밋에 따라 Azure DevOps에서 시작됩니다.
6. Azure DevOps 빌드 파이프라인은 Blob 컨테이너에서 최신 모델을 끌어와 컨테이너를 만듭니다.
7. Azure DevOps는 이미지를 Azure Container Registry의 개인 이미지 리포지토리에 푸시합니다.
8. 지정된 일정(야간)에 따라 릴리스 파이프라인이 시작됩니다.
9. ACR에서 최신 이미지를 끌어오고 ACS에서 Kubernetes 클러스터 전체에 배포합니다.
10. 앱에 대한 사용자 요청은 DNS 서버를 통해 이동합니다.
11. DNS 서버는 부하 분산 장치에 요청을 전달하고 사용자에게 다시 응답을 보냅니다.

## <a name="next-steps"></a>다음 단계
* [자습서](https://github.com/Azure/DevOps-For-AI-Apps/blob/master/Tutorial.md)를 참조하여 세부 정보를 수행하고 사용자의 응용 프로그램에 대한 사용자 소유의 CI/CD 파이프라인을 구현합니다.

## <a name="references"></a>참조
* [TDSP(Team Data Science Process)](https://aka.ms/tdsp)
* [AML(Azure Machine Learning)](https://docs.microsoft.com/azure/machine-learning/service/)
* [Azure DevOps](https://www.visualstudio.com/vso/)
* [AKS(Azure Kubernetes Services)](https://docs.microsoft.com/azure/aks/intro-kubernetes)
