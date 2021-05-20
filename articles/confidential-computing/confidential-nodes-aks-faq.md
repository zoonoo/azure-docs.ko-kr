---
title: AKS(Azure Kubernetes Service)에서 기밀 노드 지원에 대한 질문과 대답
description: AKS(Azure Kubernetes Service) 및 ACC(Azure Confidential Computing) 노드 지원에 대한 일반적인 질문에 대한 답변을 찾습니다.
author: agowdamsft
ms.service: container-service
ms.subservice: confidential-computing
ms.topic: conceptual
ms.date: 02/09/2020
ms.author: amgowda
ms.openlocfilehash: 94b585078ce5e78a658fd8f110f09963200010b6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105933562"
---
# <a name="frequently-asked-questions-about-confidential-computing-nodes-on-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)의 기밀 컴퓨팅 노드에 대한 질문과 대답

이 문서에서는 AKS(Azure Kubernetes Service)의 Intel SGX 기반 기밀 컴퓨팅 노드에 대해 자주 묻는 질문을 해결합니다. 추가 질문이 있는 경우 **acconaks@microsoft.com** 으로 문의하세요.

<a name="1"></a>
### <a name="are-the-confidential-computing-nodes-on-aks-in-ga"></a>GA에 AKS의 기밀 컴퓨팅 노드가 있나요? ###
예

<a name="2"></a>
### <a name="what-is-attestation-and-how-can-we-do-attestation-of-apps-running-in-enclaves"></a>증명이란 무엇이며, Enclave에서 실행되는 앱의 증명을 어떻게 할 수 있나요? ###
증명은 특정 하드웨어 플랫폼에서 소프트웨어의 일부가 올바르게 인스턴스화되었는지 확인하고 유효성을 검사하는 프로세스입니다. 또한 보안 플랫폼에서 실행 중이고 변조되지 않았다는 보증을 제공하기 위해 증명 정보를 확인할 수 있도록 합니다. Enclave 앱에 대해 증명을 수행하는 방법에 대해 [자세히 알아보세요](attestation.md).

<a name="3"></a>
### <a name="can-i-enable-accelerated-networking-with-azure-confidential-computing-aks-clusters"></a>Azure 기밀 컴퓨팅 AKS 클러스터를 통해 가속화된 네트워킹을 사용할 수 있나요? ###
아니요. AKS의 기밀 컴퓨팅 노드를 구성하는 DCSv2 가상 머신에서 가속화된 네트워킹을 지원하지 않습니다. 

<a name="4"></a>
### <a name="can-i-bring-my-existing-containerized-applications-and-run-it-on-aks-with-azure-confidential-computing"></a>기존 컨테이너화된 애플리케이션을 가져와 Azure 기밀 컴퓨팅을 사용하여 AKS에서 실행할 수 있나요? ###
예, 플랫폼 인에이블러에 대한 자세한 내용은 [기밀 컨테이너 페이지](confidential-containers.md)를 검토합니다.

<a name="5"></a>
### <a name="what-version-of-intel-sgx-driver-version-is-on-the-aks-image-for-confidential-nodes"></a>기밀 노드에 대한 AKS 이미지에 있는 Intel SGX Driver 버전의 버전은 무엇입니까? ### 
현재 Azure 기밀 컴퓨팅 DCSv2 VM은 Intel SGX DCAP 1.33과 함께 설치됩니다. 

<a name="6"></a>
### <a name="can-i-inject-post-install-scriptscustomize-drivers-to-the-nodes-provisioned-by-aks"></a>사후 설치 스크립트를 삽입하거나 AKS에서 프로비전된 노드에 드라이버를 사용자 지정할 수 있나요? ###
아니요. [AKS 엔진 기반 기밀 컴퓨팅 노드](https://github.com/Azure/aks-engine/blob/master/docs/topics/sgx.md)는 사용자 지정 설치를 허용하고 Kubernetes 컨트롤 플레인에 대한 모든 권한을 보유하는 기밀 컴퓨팅 노드를 지원합니다.
<a name="7"></a>

### <a name="should-i-be-using-a-docker-base-image-to-get-started-on-enclave-applications"></a>Docker 기본 이미지를 사용하여 enclave 애플리케이션을 시작해야 하나요? ###
다양한 인에이블러(ISV 및 OSS 프로젝트)는 기밀 컨테이너를 사용하도록 설정하는 방법을 제공합니다. [기밀 컨테이너 페이지](confidential-containers.md)에서 구현에 대한 자세한 내용과 개별 참조를 검토합니다.

<a name="8"></a>
### <a name="can-i-run-acc-nodes-with-other-standard-aks-skus-build-a-heterogenous-node-pool-cluster"></a>다른 표준 AKS SKU(다른 유형의 노드 풀 클러스터 빌드)를 사용하여 ACC 노드를 실행할 수 있나요? ###

예, ACC 노드를 포함하여 동일한 AKS 클러스터 내에서 다른 노드 풀을 실행할 수 있습니다. 특정 노드 풀에서 enclave 애플리케이션을 대상으로 지정하려면 노드 선택기를 추가하거나 EPC 제한을 적용하는 것이 좋습니다. [여기](confidential-nodes-aks-get-started.md)에서 기밀 노드의 빠른 시작에 대한 자세한 내용을 참조하세요.

<a name="9"></a>
### <a name="can-i-run-windows-nodes-and-windows-containers-with-acc"></a>ACC로 Windows 노드와 Windows 컨테이너를 실행할 수 있나요? ###
지금은 없습니다. Windows 노드나 컨테이너가 필요한 경우 *acconaks@microsoft.com* 에서 제품 팀에 문의하세요. 

<a name="10"></a>
### <a name="what-if-my-container-size-is-more-than-available-epc-memory"></a>컨테이너 크기가 사용 가능한 EPC 메모리보다 많은 경우는 어떻게 되나요? ###
EPC 메모리는 enclave에서 실행하도록 프로그래밍된 애플리케이션의 일부에 적용됩니다. 컨테이너의 총 크기는 사용 가능한 최대 EPC 메모리와 비교할 수 있는 올바른 방법이 아닙니다. 실제로 SGX를 사용하는 DCSv2 머신은 신뢰할 수 없는 애플리케이션 부분이 활용하는 32GB의 최대 VM 메모리를 허용합니다. 그러나 컨테이너가 사용 가능한 EPC 메모리 이상을 사용하는 경우 enclave에서 실행되는 프로그램 부분의 성능이 영향을 받을 수 있습니다.

작업자 노드에서 EPC 메모리를 보다 효율적으로 관리하려면 Kubernetes를 통해 EPC 메모리 기반 제한 관리를 고려합니다. 아래 예제를 참조로 사용합니다.

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: sgx-test
  labels:
    app: sgx-test
spec:
  template:
    metadata:
      labels:
        app: sgx-test
    spec:
      containers:
      - name: sgxtest
        image: oeciteam/sgx-test:1.0
        resources:
          limits:
            kubernetes.azure.com/sgx_epc_mem_in_MiB: 10 # This limit will automatically place the job into confidential computing node. Alternatively, you can target deployment to nodepools
      restartPolicy: Never
  backoffLimit: 0
```
<a name="11"></a>
### <a name="what-happens-if-my-enclave-consumes-more-than-maximum-available-epc-memory"></a>enclave에서 사용 가능한 최대 EPC 메모리 이상을 사용하는 경우 어떻게 되나요? ###

사용 가능한 총 EPC 메모리는 동일한 VM 또는 작업자 노드의 enclave 애플리케이션 간에 공유됩니다. 애플리케이션에서 사용 가능한 것보다 많은 EPC 메모리를 사용하는 경우 애플리케이션 성능에 영향을 줄 수 있습니다. 이러한 이유로 위의 예제에 표시된 것처럼 작업자 노드당 사용 가능한 EPC 메모리를 보다 효율적으로 관리하려면 배포 yaml 파일에서 애플리케이션당 toleration을 설정하는 것이 좋습니다. 또는 언제든지 작업자 노드 풀 VM 크기를 위로 이동하거나 노드를 더 추가하도록 선택할 수 있습니다. 

<a name="12"></a>
### <a name="why-cant-i-do-forks--and-exec-to-run-multiple-processes-in-my-enclave-application"></a>enclave 애플리케이션에서 여러 프로세스를 실행하기 위해 포크() 및 exec를 수행할 수 없는 이유는 무엇입니까? ###

현재 Azure 기밀 컴퓨팅 DCsv2 SKU VM은 enclave에서 실행되는 프로그램에 대해 단일 주소 공간을 지원합니다. 단일 프로세스는 높은 보안을 중심으로 설계된 현재 제한 사항입니다. 그러나 기밀 컨테이너 인에이블러에는 이러한 제한을 극복하기 위한 대체 구현이 있을 수 있습니다.
<a name="13"></a>
### <a name="do-you-automatically-install-any-additional-daemonset-to-expose-the-sgx-drivers"></a>모든 추가 daemonset을 자동으로 설치하여 SGX 드라이버를 표시하나요? ###

예. daemonset의 이름은 sgx-디바이스-플러그 인입니다. [여기](confidential-nodes-aks-overview.md)에서 해당 목적에 대해 자세히 알아보세요.  

<a name="14"></a>
### <a name="what-is-the-vm-sku-i-should-be-choosing-for-confidential-computing-nodes"></a>기밀 컴퓨팅 노드에 대해 어떤 VM SKU를 선택해야 하나요? ###

DCSv2 SKU [DCSv2 SKU](../virtual-machines/dcv2-series.md)는 [지원되는 지역](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines&regions=all)에서 사용할 수 있습니다.

<a name="15"></a>
### <a name="can-i-still-schedule-and-run-non-enclave-containers-on-confidential-computing-nodes"></a>기밀 컴퓨팅 노드에서 여전이 비 enclave 컨테이너를 예약하고 실행할 수 있나요? ###

예. 또한 VM에는 표준 컨테이너 작업을 실행할 수 있는 일반 메모리가 있습니다. 배포 모델을 결정하기 전에 애플리케이션의 보안 및 위협 모델을 고려해야 합니다.
<a name="16"></a>

### <a name="can-i-provision-aks-with-dcsv2-node-pools-through-azure-portal"></a>Azure Portal을 통해 DCSv2 노드 풀로 AKS를 프로비전할 수 있나요? ###

예. [여기](confidential-nodes-aks-get-started.md)에 설명된 대로 Azure CLI를 사용할 수도 있습니다.

<a name="17"></a>
### <a name="what-ubuntu-version-and-vm-generation-is-supported"></a>지원되는 Ubuntu 버전 및 VM 생성은 무엇인가요? ###
Gen 2에서 18.04 

<a name="18"></a>
### <a name="can-we-change-the-current-intel-sgx-dcap-diver-version-on-aks"></a>AKS의 현재 Intel SGX DCAP 드라이버 버전을 변경할 수 있나요? ###

아니요. 사용자 지정 설치를 수행하려면 [AKS 엔진 기밀 컴퓨팅 작업자 노드](https://github.com/Azure/aks-engine/blob/master/docs/topics/sgx.md) 배포를 선택하는 것이 좋습니다. 

<a name="19"></a>

### <a name="what-version-of-kubernetes-do-you-support-and-recommend"></a>어떤 버전의 Kubernetes를 지원하고 권장하나요? ###

Kubernetes 버전 1.16 이상을 지원하고 권장합니다. 

<a name="20"></a>
### <a name="what-are-the-known-current-limitations-of-the-product"></a>제품의 알려진 현재 제한은 무엇인가요? ###

- Ubuntu 18.04 Gen 2 VM 노드만 지원합니다. 
- Windows 노드 지원 또는 Windows 컨테이너 지원 없음
- EPC 메모리 기반 수평 Pod 자동 크기 조정을 지원하지 않습니다. CPU 및 일반 메모리 기반 크기 조정이 지원됩니다.
- 기밀 앱에 대한 AKS의 개발 공간은 현재 지원되지 않습니다.

<a name="21"></a>
### <a name="will-only-signed-and-trusted-images-be-loaded-in-the-enclave-for-confidential-computing"></a>기밀 컴퓨팅을 위해 서명되고 신뢰할 수 있는 이미지만 enclave에 로드되나요? ###
enclave 초기화 중에는 기본적으로 아니지만 증명 프로세스 서명을 통해 유효성을 검사할 수 있습니다. [여기](../attestation/basic-concepts.md#benefits-of-policy-signing)를 참조하세요. 

### <a name="next-steps"></a>다음 단계
기밀 컨테이너에 대한 자세한 내용은 [기밀 컨테이너 페이지](confidential-containers.md)를 참조하세요.
