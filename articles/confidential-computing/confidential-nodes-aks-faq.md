---
title: Azure Kubernetes 서비스 (AKS)에서 기밀 노드에 대 한 질문과 대답
description: Azure Kubernetes Service (ACC) 노드 지원 & AKS (Azure Service)에 대 한 일반적인 질문에 대 한 답변을 찾습니다.
author: agowdamsft
ms.service: container-service
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: amgowda
ms.openlocfilehash: b4b6c04a1e35031387a006b6350ebf6cbe502fee
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90997900"
---
# <a name="frequently-asked-questions-about-confidential-computing-nodes-on-azure-kubernetes-service-aks"></a>Azure Kubernetes 서비스 (AKS)의 기밀 컴퓨팅 노드에 대 한 질문과 대답

이 문서에서는 Azure Kubernetes 서비스 (AKS)의 Intel SGX 기반 기밀 컴퓨팅 노드에 대해 자주 묻는 질문을 해결 합니다. 추가 질문이 있으면 전자 메일을 보내세요 acconaks@microsoft.com .

## <a name="what-service-level-agreement-sla-and-azure-support-is-provided-during-the-preview"></a>미리 보기 중에 제공 되는 Service Level Agreement(서비스 수준 약정) (SLA) 및 Azure 지원 이란 무엇 인가요? 

SLA는 [여기](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에 정의 된 제품 미리 보기 중에는 제공 되지 않습니다. 그러나 제품 지원은 Azure 지원을 통해 제공 됩니다.

## <a name="what-is-attestation-and-how-can-we-do-attestation-of-apps-running-in-enclaves"></a>증명 이란 무엇 이며, enclaves에서 실행 되는 앱의 증명을 어떻게 할 수 있나요? 

증명은 특정 하드웨어 플랫폼에서 소프트웨어의 일부가 올바르게 인스턴스화 되었는지 확인 하 고 유효성을 검사 하는 프로세스입니다. 또한 보안 플랫폼에서 실행 중이 고 변조 되지 않았다는 보증을 제공 하기 위해 증명 정보를 확인할 수 있도록 합니다. Enclave apps에 대해 증명을 수행 하는 방법에 대해 [자세히](attestation.md) 알아보세요.

## <a name="can-i-bring-my-existing-containerized-applications-and-run-it-on-aks-with-azure-confidential-computing"></a>기존 컨테이너 화 된 응용 프로그램을 가져와 Azure 기밀 컴퓨팅을 사용 하 여 AKS에서 실행할 수 있나요? 

예, 플랫폼 enablers에 대 한 자세한 내용은 [기밀 컨테이너 페이지](confidential-containers.md) 를 검토 합니다.

## <a name="what-intel-sgx-driver-version-is-installed-in-the-aks-image"></a>AKS 이미지에 설치 된 Intel SGX Driver 버전은 무엇 인가요? 

현재 Azure 기밀 컴퓨팅 DCSv2 Vm은 Intel SGX d CAP 1.33와 함께 설치 됩니다. 

## <a name="can-i-open-an-azure-support-ticket-if-i-run-into-issues"></a>문제가 발생 하는 경우 Azure 지원 티켓을 열 수 있나요? 

예. Azure 지원은 미리 보기 중에 제공 됩니다. 제품이 미리 보기 단계에 있기 때문에 SLA가 연결 되어 있지 않습니다.

## <a name="can-i-inject-post-install-scriptscustomize-drivers-to-the-nodes-provisioned-by-aks"></a>사후 설치 스크립트를 삽입 하거나 AKS에서 프로 비전 된 노드에 드라이버를 사용자 지정할 수 있나요? 

아니요. [AKS 엔진 기반 기밀 컴퓨팅 노드](https://github.com/Azure/aks-engine/blob/master/docs/topics/sgx.md) 는 사용자 지정 설치를 허용 하는 기밀 컴퓨팅 노드를 지원 합니다.

## <a name="should-i-be-using-a-docker-base-image-to-get-started-on-enclave-applications"></a>Docker 기본 이미지를 사용 하 여 enclave 응용 프로그램을 시작 해야 하나요? 

다양 한 조력자 (isv 및 OSS 프로젝트)는 기밀 컨테이너를 사용 하도록 설정 하는 방법을 제공 합니다. [기밀 컨테이너 페이지](confidential-containers.md) 에서 구현에 대 한 자세한 내용과 개별 참조를 검토 합니다.

## <a name="can-i-run-acc-nodes-with-other-standard-aks-skus-build-a-heterogenous-node-pool-cluster"></a>다른 표준 AKS Sku (다른 유형의 node pool 클러스터 빌드)를 사용 하 여 ACC 노드를 실행할 수 있나요? 

예, ACC 노드를 포함 하 여 동일한 AKS 클러스터 내에서 다른 노드 풀을 실행할 수 있습니다. 특정 노드 풀에서 enclave 응용 프로그램을 대상으로 지정 하려면 노드 선택기를 추가 하거나 EPC 제한을 적용 하는 것이 좋습니다. [여기](confidential-nodes-aks-get-started.md)에서 기밀 노드의 빠른 시작에 대 한 자세한 내용을 참조 하세요.

## <a name="can-i-run-windows-nodes-and-windows-containers-with-acc"></a>ACC로 Windows 노드와 windows 컨테이너를 실행할 수 있나요? 

지금은 없습니다. Windows 노드나 컨테이너 요구 사항이 있는 경우 문의해 주세요. 

## <a name="what-if-my-container-size-is-more-than-available-epc-memory"></a>컨테이너 크기가 사용 가능한 EPC 메모리 보다 많은 경우는 어떻게 되나요? 

EPC 메모리는 enclave에서 실행 하도록 프로그래밍 된 응용 프로그램의 일부에 적용 됩니다. 컨테이너의 총 크기는 사용 가능한 최대 EPC 메모리와 비교할 수 있는 올바른 방법이 아닙니다. 실제로 DCSv2를 사용 하는 컴퓨터는 신뢰할 수 없는 응용 프로그램 부분이 활용 하는 32 GB의 최대 VM 메모리를 허용 합니다. 그러나 컨테이너가 사용 가능한 EPC 메모리를 많이 사용 하는 경우 enclave에서 실행 되는 프로그램 부분의 성능이 영향을 받을 수 있습니다.

작업자 노드에서 EPC 메모리를 보다 효율적으로 관리 하려면 Kubernetes를 통해 EPC 메모리 기반 제한 관리를 고려 하십시오. 아래 예제를 참조로 수행 합니다.

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
            kubernetes.azure.com/sgx_epc_mem_in_MiB: 10 # This limit will automatically place the job into confidential computing node. Alternatively you can target deployment to nodepools
      restartPolicy: Never
  backoffLimit: 0
```

## <a name="what-happens-if-my-enclave-consumes-more-than-maximum-available-epc-memory"></a>Enclave에서 사용 가능한 최대 EPC 메모리를 사용 하는 경우 어떻게 되나요? 

사용 가능한 총 EPC 메모리는 동일한 Vm 또는 작업자 노드의 enclave 응용 프로그램 간에 공유 됩니다. 응용 프로그램에서 사용 가능한 것 보다 많은 EPC 메모리를 사용 하는 경우 응용 프로그램 성능에 영향을 줄 수 있습니다. 이러한 이유로 위의 예제에 표시 된 것 처럼 작업자 노드당 사용 가능한 EPC 메모리를 보다 효율적으로 관리 하려면 배포 yaml 파일에서 응용 프로그램당 toleration를 설정 하는 것이 좋습니다. 또는 언제 든 지 작업자 노드 풀 VM 크기를 위로 이동 하거나 노드를 더 추가 하도록 선택할 수 있습니다. 

## <a name="why-cant-i-do-forks--and-exec-to-run-multiple-processes-in-my-enclave-application"></a>Enclave 응용 프로그램에서 여러 프로세스를 실행 하기 위해 포크 () 및 exec를 수행할 수 없는 이유는 무엇입니까? 

현재 Azure 기밀 컴퓨팅 DCsv2 SKU Vm은 enclave에서 실행 되는 프로그램에 대해 단일 주소 공간을 지원 합니다. 단일 프로세스는 높은 보안을 중심으로 설계 된 현재 제한 사항입니다. 그러나 비밀 컨테이너 조력자에는 이러한 제한을 극복 하기 위한 대체 구현이 있을 수 있습니다.

## <a name="do-you-automatically-install-any-additional-daemonsets-to-expose-the-sgx-drivers"></a>모든 추가 daemonsets를 자동으로 설치 하 여 SGX 드라이버를 표시 하나요? 

예. Daemonset 이름은 sgx-장치-플러그 인 및 sgx-도우미입니다. [여기](confidential-nodes-aks-overview.md)에서 해당 목적에 대해 자세히 알아보세요.  

## <a name="what-is-the-vm-sku-i-should-be-choosing-for-confidential-computing-nodes"></a>기밀 컴퓨팅 노드에 대해 어떤 VM SKU를 선택 해야 하나요? 

DCSv2 Sku [DCSv2 sku](../virtual-machines/dcv2-series.md) 는 [지원 되는 지역](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines&regions=all)에서 사용할 수 있습니다.

## <a name="can-i-still-schedule-and-run-non-enclave-containers-on-confidential-computing-nodes"></a>기밀 컴퓨팅 노드에서 여전히 enclave 컨테이너를 예약 하 고 실행할 수 있나요? 

예. 또한 Vm에는 표준 컨테이너 작업을 실행할 수 있는 일반 메모리가 있습니다. 배포 모델을 결정 하기 전에 응용 프로그램의 보안 및 위협 모델을 고려해 야 합니다.

## <a name="can-i-provision-aks-with-dcsv2-node-pools-through-azure-portal"></a>Azure Portal를 통해 DCSv2 노드 풀로 AKS를 프로 비전 할 수 있나요? 

예. [여기](confidential-nodes-aks-get-started.md)에 설명 된 대로 Azure CLI 사용할 수도 있습니다.

## <a name="what-ubuntu-version-and-vm-generation-is-supported"></a>지원 되는 Ubuntu 버전 및 VM 생성은 무엇 인가요? 

Gen 2에서 18.04. 

## <a name="can-we-change-the-current-intel-sgx-dcap-diver-version-on-aks"></a>AKS의 현재 Intel SGX (SGX) 버전을 변경할 수 있나요? 

아니요. 사용자 지정 설치를 수행 하려면 [AKS-Engine 기밀 컴퓨팅 작업자 노드](https://github.com/Azure/aks-engine/blob/master/docs/topics/sgx.md) 배포를 선택 하는 것이 좋습니다. 

## <a name="what-version-of-kubernetes-do-you-support-and-recommend"></a>어떤 버전의 Kubernetes을 지원 하 고 권장 하나요? 

Kubernetes 버전 1.16 이상을 지원 하 고 권장 합니다. 

## <a name="what-are-the-known-current-limitation-or-technical-limitations-of-the-product-in-preview"></a>미리 보기로 제공 되는 제품의 알려진 현재 제한 또는 기술적 제한은 무엇 인가요? 

- Ubuntu 18.04 Gen 2 VM 노드만 지원 합니다. 
- Windows 노드가 지원 되지 않거나 Windows 컨테이너 지원
- EPC 메모리 기반 수평 Pod 자동 크기 조정을 지원 하지 않습니다. CPU 및 일반 메모리 기반 크기 조정이 지원 됩니다.
- 기밀 앱에 대 한 AKS의 개발 공간은 현재 지원 되지 않습니다.

## <a name="next-steps"></a>다음 단계
기밀 컨테이너에 대 한 자세한 내용은 [기밀 컨테이너 페이지](confidential-containers.md) 를 참조 하세요.