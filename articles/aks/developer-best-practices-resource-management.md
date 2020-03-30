---
title: 개발자 모범 사례 - AKS(Azure Kubernetes Services)의 리소스 관리
description: AKS(Azure Kubernetes Services)의 리소스 관리를 위한 애플리케이션 개발자 모범 사례 알아보기
services: container-service
author: zr-msft
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: zarhoads
ms.openlocfilehash: 66676c90e73d1886c86d8afda8cbbecce239a005
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259527"
---
# <a name="best-practices-for-application-developers-to-manage-resources-in-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)에서 리소스를 관리하기 위한 애플리케이션 개발자 모범 사례

AKS(Azure Kubernetes Service)에서 애플리케이션을 개발 빛 실행할 경우 몇 가지 주요 영역을 고려해야 합니다. 애플리케이션 배포를 관리하는 방법은 사용자가 제공하는 서비스의 최종 사용자 환경에 부정적인 영향을 줄 수 있습니다. 성공하려면 AKS에서 애플리케이션을 개발하고 실행할 때 따를 수 있는 몇 가지 모범 사례를 고려하세요.

이 모범 사례 문서는 애플리케이션 개발자 관점에서 클러스터 및 워크로드를 실행하는 방법을 중점적으로 설명합니다. 관리 모범 사례에 대한 자세한 내용은 [AKS(Azure Kubernetes Service)의 격리 및 리소스 관리에 대한 클러스터 운영자 모범 사례][operator-best-practices-isolation]를 참조하세요. 이 문서에서는 다음에 대해 알아봅니다.

> [!div class="checklist"]
> * Pod 리소스 요청 및 한도의 개념
> * Dev Spaces 및 Visual Studio Code를 사용하여 애플리케이션을 개발 및 배포하는 방법
> * `kube-advisor` 도구를 사용하여 배포 관련 문제를 확인하는 방법

## <a name="define-pod-resource-requests-and-limits"></a>Pod 리소스 요청 및 한도 정의

**모범 사례 가이드** - YAML 매니페스트의 모든 Pod에서 Pod 요청 및 한도를 설정합니다. AKS 클러스터에서 ‘리소스 할당량’을 사용하는 경우 이러한 값을 정의하지 않으면 배포가 거부될 수 있습니다.**

AKS 클러스터 내에서 컴퓨팅 리소스를 관리하는 기본 방법은 Pod 요청 및 한도를 사용하는 것입니다. 이러한 요청 및 한도를 사용하면 Kubernetes 스케줄러가 Pod에 할당해야 하는 컴퓨팅 리소스를 알 수 있습니다.

* **포드 CPU/메모리 요청은** 포드에 정기적으로 필요한 설정된 양의 CPU와 메모리를 정의합니다.
    * Kubernetes 스케줄러가 노드에 포드를 배치하려고 하면 포드 요청이 스케줄링에 사용할 수 있는 충분한 리소스가 있는 노드를 결정하는 데 사용됩니다.
    * pod 요청을 설정하지 않는 것은 정의된 제한으로 기본값으로 설정됩니다.
    * 이러한 요청을 조정하려면 응용 프로그램의 성능을 모니터링하는 것이 매우 중요합니다. 요청이 충분하지 않은 경우 노드 를 과도하게 예약하여 응용 프로그램의 성능이 저하될 수 있습니다. 요청이 과대 평가되면 응용 프로그램이 예약하는 데 어려움이 증가했을 수 있습니다.
* **포드 CPU/메모리 제한은** 포드에서 사용할 수 있는 최대 CPU 및 메모리 양입니다. 이러한 제한은 리소스 부족으로 인해 노드 불안정이 발생할 경우 어떤 포드를 죽일지 정의하는 데 도움이 됩니다. 적절한 제한없이 설정 포드는 자원 압력이 해제 될 때까지 살해됩니다.
    * 포드 제한은 포드가 리소스 소비에 대한 제어권을 잃어버린 시기를 정의하는 데 도움이 됩니다. 제한을 초과하면 노드 상태를 유지하고 노드를 공유하는 포드에 미치는 영향을 최소화하기 위해 죽임을 위해 포드의 우선 순위가 지정됩니다.
    * 포드 제한을 설정하지 않는 것은 지정된 노드에서 사용 가능한 가장 높은 값으로 기본값을 설정합니다.
    * 노드에서 지원할 수 있는 것보다 높은 Pod 한도를 설정하지 마세요. 각 AKS 노드는 핵심 Kubernetes 구성 요소의 설정된 CPU 및 메모리 크기를 예약합니다. 애플리케이션이 노드에서 너무 많은 리소스를 사용하려고 하여 다른 Pod가 성공적으로 실행되지 않을 수 있습니다.
    * 다시 말하지만, 낮이나 일주일 동안 다른 시간에 응용 프로그램의 성능을 모니터링하는 것이 매우 중요합니다. 최대 수요 시기를 확인하고 포드 제한을 응용 프로그램의 최대 요구 사항을 충족하는 데 필요한 리소스에 맞춥니다.

pod 사양에서 위의 정보를 기반으로 이러한 요청 및 제한을 정의하는 것이 **가장 중요하며 매우 중요합니다.** 이러한 값을 포함하지 않으면 Kubernetes 스케줄러는 응용 프로그램이 일정을 결정하는 데 필요한 리소스를 고려할 수 없습니다.

스케줄러가 리소스가 부족한 노드에 포드를 배치하면 응용 프로그램 성능이 저하됩니다. 클러스터 관리자는 리소스 요청 및 제한을 설정해야 하는 네임스페이스에 리소스 *할당량을* 설정하는 것이 좋습니다. 자세한 내용은 [AKS 클러스터의 리소스 할당량][resource-quotas]을 참조하세요.

CPU 요청 또는 한도를 정의하는 경우 값은 CPU 단위로 측정됩니다. 
* *1.0* CPU는 노드에 있는 하나의 기본 가상 CPU 코어와 동일합니다. 
* GPU에도 동일한 측정이 사용됩니다.
* 밀리코어로 측정된 분수를 정의할 수 있습니다. 예를 들어 *100m는* 기본 vCPU 코어의 *0.1입니다.*

단일 NGINX Pod에 대한 다음 기본 예제에서 Pod는 *100m* CPU 시간 및 *128Mi* 메모리를 요청합니다. Pod의 리소스 한도는 *250m* CPU 및 *256Mi* 메모리로 설정됩니다.

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypod
spec:
  containers:
  - name: mypod
    image: nginx:1.15.5
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
```

리소스 측정 및 할당에 대한 자세한 내용은 [컨테이너의 컴퓨팅 리소스 관리][k8s-resource-limits]를 참조하세요.

## <a name="develop-and-debug-applications-against-an-aks-cluster"></a>AKS 클러스터에서 애플리케이션 개발 및 디버그

**모범 사례 가이드** - 개발 팀은 Dev Spaces를 사용하여 AKS 클러스터에서 배포 및 디버그해야 합니다. 이 개발 모델에서는 앱이 프로덕션 환경에 배포되기 전에 역할 기반 액세스 제어, 네트워크 또는 스토리지 요구 사항이 구현되도록 합니다.

Azure Dev Spaces를 사용하면 AKS 클러스터에서 직접 애플리케이션을 개발, 디버그 및 테스트합니다. 팀 내의 개발자들은 애플리케이션 수명 주기 내내 함께 빌드하고 테스트합니다. Visual Studio 또는 Visual Studio Code와 같은 기존 도구를 계속 사용할 수 있습니다. 확장은 AKS 클러스터에서 애플리케이션을 실행하고 디버그하는 옵션을 제공하는 Dev Spaces용으로 설치됩니다.

![Dev Spaces를 사용하여 AKS 클러스터에서 애플리케이션 디버그](media/developer-best-practices-resource-management/dev-spaces-debug.png)

Dev Spaces를 사용하는 이 통합 개발 및 테스트 프로세스를 적용하면 로컬 테스트 환경(예: [minikube][minikube])의 필요성이 줄어듭니다. 대신 AKS 클러스터에서 개발하고 테스트합니다. 네임스페이스를 사용하여 클러스터를 논리적으로 격리하는 방법에 대한 이전 섹션의 설명대로 이 클러스터를 보호 및 격리할 수 있습니다. 앱을 프로덕션 환경에 배포할 준비가 되면 실제 AKS 클러스터에서 배포가 모두 완료되었으므로 자신 있게 배포할 수 있습니다.

Azure 개발자 공간은 Linux 포드 및 노드에서 실행되는 응용 프로그램과 함께 사용하기 위한 것입니다.

## <a name="use-the-visual-studio-code-extension-for-kubernetes"></a>Kubernetes용 Visual Studio Code 확장 사용

**모범 사례 가이드** - YAML 매니페스트를 작성할 때 Kubernetes용 VS Code 확장을 설치하고 사용합니다. 통합 배포 솔루션에 확장을 사용할 수도 있고, 이는 AKS 클러스터를 드물게 조작하는 애플리케이션 소유자에게 도움이 될 수 있습니다.

[Kubernetes용 Visual Studio Code 확장][vscode-kubernetes]은 애플리케이션을 개발하고 AKS에 배포하는 데 도움이 됩니다. 이 확장은 Kubernetes 리소스와 Helm 차트 및 템플릿에 대한 intellisense를 제공합니다. VS Code 내에서 Kubernetes 리소스를 검색, 배포 및 편집할 수도 있습니다. 이 확장은 Pod 사양에서 설정되는 리소스 요청 또는 한도에 대한 intellisense 검사도 제공합니다.

![메모리 한도 누락에 대한 Kubernetes용 VS 코드 확장 경고](media/developer-best-practices-resource-management/vs-code-kubernetes-extension.png)

## <a name="regularly-check-for-application-issues-with-kube-advisor"></a>kube-advisor를 사용하여 애플리케이션 문제를 정기적으로 확인

**모범 사례 지침** - 최신 `kube-advisor` 버전의 오픈 소스 도구를 정기적으로 실행하여 클러스터의 문제를 검색합니다. 기존 AKS 클러스터에서 리소스 할당량을 적용하는 경우 먼저 `kube-advisor`를 실행하여 리소스 요청 및 한도가 정의되지 않은 Pod를 찾습니다.

[kube-advisor][kube-advisor] 도구는 Kubernetes 클러스터를 스캔하고 발견한 문제에 대해 보고하는 관련 AKS 오픈 소스 프로젝트입니다. 한 가지 유용한 검사는 리소스 요청 및 한도가 없는 Pod를 식별하는 것입니다.

kube-advisor 도구는 리소스 요청에 보고할 수 있으며 Windows 응용 프로그램뿐만 아니라 Linux 응용 프로그램에 대한 PodSpecs에서 누락된 제한을 보고할 수 있지만 kube-advisor 도구 자체는 Linux 포드에서 예약되어야 합니다. 포드 구성의 노드 선택기를 사용하여 특정 OS가 있는 노드 풀에서 [실행되도록 포드를][k8s-node-selector] 예약할 수 있습니다.

많은 개발 팀과 애플리케이션을 호스트하는 AKS 클러스터에서는 이러한 리소스 요청 및 한도가 설정되지 않은 Pod를 추적하기 어려울 수 있습니다. AKS 클러스터에서 `kube-advisor`를 정기적으로 실행하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

이 모범 사례 문서는 클러스터 운영자 관점에서 클러스터 및 워크로드를 실행하는 방법을 중점적으로 설명했습니다. 관리 모범 사례에 대한 자세한 내용은 [AKS(Azure Kubernetes Service)의 격리 및 리소스 관리에 대한 클러스터 운영자 모범 사례][operator-best-practices-isolation]를 참조하세요.

이러한 일부 모범 사례를 구현하려면 다음 문서를 참조하세요.

* [Dev Spaces를 사용하여 개발][dev-spaces]
* [kube-advisor를 사용하여 문제 확인][aks-kubeadvisor]

<!-- EXTERNAL LINKS -->
[k8s-resource-limits]: https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/
[vscode-kubernetes]: https://github.com/Azure/vscode-kubernetes-tools
[kube-advisor]: https://github.com/Azure/kube-advisor
[minikube]: https://kubernetes.io/docs/setup/minikube/

<!-- INTERNAL LINKS -->
[aks-kubeadvisor]: kube-advisor-tool.md
[dev-spaces]: ../dev-spaces/get-started-netcore.md
[operator-best-practices-isolation]: operator-best-practices-cluster-isolation.md
[resource-quotas]: operator-best-practices-scheduler.md#enforce-resource-quotas
[k8s-node-selector]: concepts-clusters-workloads.md#node-selectors
