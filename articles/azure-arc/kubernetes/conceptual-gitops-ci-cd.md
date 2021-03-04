---
title: GitOps를 사용 하는 CI/CD 워크플로-Azure Arc enabled Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: conceptual
author: tcare
ms.author: tcare
description: 이 문서에서는 GitOps를 사용 하 여 CI/CD 워크플로에 대 한 개념적 개요를 제공 합니다.
keywords: GitOps, Kubernetes, K8s, Azure, 투구, Arc, AKS, Azure Kubernetes Service, 컨테이너, CI, CD, Azure DevOps
ms.openlocfilehash: a51a9f2b32f1088cec390dc4d74300a38f37b160
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102121782"
---
# <a name="cicd-workflow-using-gitops---azure-arc-enabled-kubernetes"></a>GitOps를 사용 하는 CI/CD 워크플로-Azure Arc enabled Kubernetes

최신 Kubernetes 배포에는 여러 응용 프로그램, 클러스터 및 환경이 있습니다. GitOps를 사용 하 여 이러한 복잡 한 기능을 보다 쉽게 관리할 수 있으며 Git를 사용 하 여 Kubernetes 환경의 원하는 상태를 선언적으로 추적할 수 있습니다. 일반적인 Git 도구를 사용 하 여 클러스터 상태를 추적 하면 책임을 늘리고, 오류 조사를 용이 하 게 하 고, 자동화를 통해 환경을 관리할 수 있습니다.

이 개념적 개요에서는 Azure Arc, Azure Repos 및 Azure Pipelines를 사용 하 여 전체 응용 프로그램 변경 수명 주기의 현실로 GitOps를 설명 합니다. GitOps 제어 Kubernetes 환경에 대 한 단일 응용 프로그램 변경의 [예로 이동](#example-workflow) 합니다.

## <a name="architecture"></a>Architecture

하나 이상의 Kubernetes 환경에 배포 된 응용 프로그램을 고려 합니다.

![GitOps CI/CD 아키텍처](./media/gitops-arch.png)

### <a name="application-repo"></a>응용 프로그램 리포지토리
응용 프로그램 리포지토리에는 개발자가 내부 루프 중에 작업 하는 응용 프로그램 코드가 포함 되어 있습니다. 이 리포지토리에는 응용 프로그램의 배포 템플릿이 투구 또는 Kustomize과 같은 일반 형식으로 있습니다. 환경 관련 값은 저장 되지 않습니다. 이 리포지토리의 변경 내용은 배포 프로세스를 시작 하는 PR 또는 CI 파이프라인을 호출 합니다.
### <a name="container-registry"></a>Container Registry
Container registry는 Kubernetes 환경에 사용 되는 모든 자사 및 타사 이미지를 보관 합니다. 사용자가 읽을 수 있는 태그 및 이미지를 빌드하는 데 사용 되는 Git 커밋을 사용 하 여 자사 응용 프로그램 이미지에 태그를 적용 합니다. 보안, 속도 및 복원 력을 위해 타사 이미지를 캐시 합니다. 적시에 테스트 하 고 보안 업데이트를 통합 하기 위한 계획을 설정 합니다. 자세한 내용은 [ACR 사용 및 유지 관리 공용 콘텐츠](https://docs.microsoft.com/azure/container-registry/tasks-consume-public-content) 가이드를 참조 하세요.
### <a name="pr-pipeline"></a>PR 파이프라인
응용 프로그램 리포지토리의 Pr은 PR 파이프라인이 성공적으로 실행 될 때 제어 됩니다. 이 파이프라인은 응용 프로그램 코드에 대 한 lint 및 단위 테스트와 같은 기본 품질 게이트를 실행 합니다. 파이프라인은 Kubernetes 환경에 배포 하는 데 사용 되는 응용 프로그램 및 lints Dockerfiles 및 투구 템플릿을 테스트 합니다. Docker 이미지를 빌드하고 테스트 해야 하지만 푸시 하지는 않습니다. 신속 하 게 반복할 수 있도록 파이프라인 기간을 비교적 짧게 유지 합니다.
### <a name="ci-pipeline"></a>CI 파이프라인
응용 프로그램 CI 파이프라인은 모든 PR 파이프라인 단계를 실행 하 고 테스트 및 배포 검사를 확장 합니다. 파이프라인은 각 커밋에 대해 실행 하거나 커밋 그룹을 사용 하는 일반 흐름에서 실행할 수 있습니다. 이 단계에서는 PR 파이프라인에 대해 너무 긴 응용 프로그램 테스트를 수행 합니다. 배포 준비를 마친 후 Docker 이미지를 Container Registry에 푸시합니다. 대체 된 템플릿은 테스트 값 집합을 사용 하 여 linted 수 있습니다. 서비스 런타임에 사용 되는 이미지는 linted, 빌드 및 테스트를 거쳐야 합니다. 구체적으로 CI 빌드에서는 배포 준비에 사용할 CD 단계에 대 한 아티팩트가 게시 됩니다.
### <a name="flux"></a>Flux
Flux는 각 클러스터에서 실행 되며 원하는 상태를 유지 관리 해야 하는 서비스입니다. 서비스는 클러스터에 대 한 변경 내용에 대 한 GitOps 리포지토리를 자주 폴링하여 적용 합니다.
### <a name="cd-pipeline"></a>CD 파이프라인
성공적인 CI 빌드에 의해 CD 파이프라인이 자동으로 트리거됩니다. 이전에 게시 된 템플릿을 사용 하 고 환경 값을 대체 하며 GitOps 리포지토리로 PR을 열어 하나 이상의 Kubernetes 클러스터에 대 한 원하는 상태 변경을 요청 합니다. 클러스터 관리자는 상태 변경 PR을 검토 하 고 GitOps 리포지토리에 병합을 승인 합니다. 그러면 파이프라인은 PR이 완료 될 때까지 대기 하므로 Flux가 상태 변경을 선택할 수 있습니다.
### <a name="gitops-repo"></a>GitOps 리포지토리
GitOps 리포지토리는 클러스터의 모든 환경에 대 한 현재 desired 상태를 나타냅니다. 이 리포지토리에 대 한 모든 변경 내용은 각 클러스터의 Flux 서비스에 의해 선택 되 고 배포 됩니다. Pr는 원하는 상태를 변경 하 고, 검토 하 고, 병합 하 여 만듭니다. 이러한 Pr에는 배포 템플릿과 그 결과 렌더링 된 Kubernetes 매니페스트 모두에 대 한 변경 내용이 포함 되어 있습니다. 낮은 수준으로 렌더링 된 매니페스트를 사용 하면 일반적으로 템플릿 수준에서 보이지 않는 변경 내용 검사를 보다 신중 하 게 수행할 수 있습니다.
### <a name="kubernetes-clusters"></a>Kubernetes 클러스터
하나 이상의 Azure Arc 사용 Kubernetes 클러스터는 응용 프로그램에 필요한 여러 환경에 사용 됩니다. 예를 들어 단일 클러스터는 서로 다른 네임 스페이스를 통해 개발 및 QA 환경을 모두 제공할 수 있습니다. 두 번째 클러스터는 환경 및 보다 세분화 된 제어를 더 쉽게 분리할 수 있습니다.
## <a name="example-workflow"></a>예제 워크플로
응용 프로그램 개발자로 서 Alice는 다음을 수행 합니다.
* 응용 프로그램 코드를 작성 합니다.
* Docker 컨테이너에서 응용 프로그램을 실행 하는 방법을 결정 합니다.
* Kubernetes 클러스터에서 컨테이너 및 종속 서비스를 실행 하는 템플릿을 정의 합니다.

Alice는 응용 프로그램이 여러 환경에서 실행할 수 있어야 하는 기능을 알고 있지만 각 환경에 대 한 특정 설정은 알지 못합니다.

응용 프로그램 배포 템플릿에 사용 되는 Docker 이미지를 변경 하는 응용 프로그램을 변경 하려고 한다고 가정 합니다.

1. Alice는 배포 템플릿을 변경 하 고 응용 프로그램 리포지토리의 원격 분기에 푸시하고 검토할 PR을 엽니다.
2. Alice는 팀에 변경 내용을 검토 하도록 요청 합니다.
    * PR 파이프라인은 유효성 검사를 실행 합니다.
    * 파이프라인이 성공적으로 실행 되 면 팀은 로그 오프 하 고 변경 내용을 병합 합니다.
3. CI 파이프라인은 Alice의 변경 내용에 대 한 유효성을 검사 하 고 성공적으로 완료 됩니다.
    * 변경 내용은 클러스터에 배포 해도 안전 하며, 아티팩트는 CI 파이프라인 실행에 저장 됩니다.
4. Alice의 변경 내용은 CD 파이프라인을 병합 하 고 트리거합니다.
    * CD 파이프라인은 Alice의 CI 파이프라인 실행에 의해 저장 된 아티팩트를 선택 합니다.
    * CD 파이프라인은 템플릿을 환경 특정 값으로 대체 하 고 GitOps 리포지토리의 기존 클러스터 상태에 대 한 모든 변경 내용을 준비 합니다.
    * CD 파이프라인은 클러스터 상태에 대 한 원하는 변경 내용을 사용 하 여 GitOps 리포지토리에 PR을 만듭니다.
5. Alice의 팀은 PR을 검토 하 고 승인 합니다.
    * 변경 내용은 환경에 해당 하는 대상 분기로 병합 됩니다.
6. 몇 분 내에 Flux는 GitOps 리포지토리를 변경 하 고 Alice의 변경 내용을 가져옵니다.
    * Docker 이미지 변경으로 인해 응용 프로그램 pod에 업데이트가 필요 합니다.
    * Flux는 클러스터에 변경 내용을 적용 합니다.
7. Alice는 응용 프로그램 끝점을 테스트 하 여 배포가 성공적으로 완료 되었는지 확인 합니다.
   > [!NOTE]
   > 배포 대상으로 지정 된 추가 환경의 경우 CD 파이프라인은 다음 환경의 PR을 만들고 4-7 단계를 반복 합니다. 이 프로세스에는 보안 관련 변경 또는 프로덕션 환경과 같은 riskier 배포 또는 환경에 대 한 추가 승인이 필요 합니다.
8.  모든 환경에서 성공적인 배포를 수신 하면 파이프라인이 완료 됩니다.

## <a name="next-steps"></a>다음 단계
[Azure Arc Enabled Kubernetes를 사용 하 여](./conceptual-configurations.md) 클러스터와 Git 리포지토리 간의 연결을 구성 리소스로 만드는 방법에 대해 자세히 알아보세요.
