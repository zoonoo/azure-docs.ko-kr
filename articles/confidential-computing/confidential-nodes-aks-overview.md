---
title: AKS(Azure Kubernetes Service) 공개 미리 보기의 기밀 컴퓨팅 노드
description: AKS의 기밀 컴퓨팅 노드
services: virtual-machines
author: agowdamsft
ms.service: container-service
ms.topic: overview
ms.date: 9/22/2020
ms.author: amgowda
ms.openlocfilehash: a009cd7763b4a4dc0c502d4c47a20d6fdffe61d7
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92125444"
---
# <a name="confidential-computing-nodes-on-azure-kubernetes-service-public-preview"></a>Azure Kubernetes Service(공개 미리 보기)의 기밀 컴퓨팅 노드

[Azure 기밀 컴퓨팅](overview.md)을 사용하면 사용 중인 중요한 데이터를 보호할 수 있습니다. 기본 인프라는 하드웨어에서 지원되는 신뢰할 수 있는 실행 컨테이너 환경을 통해 다른 애플리케이션, 관리자 및 클라우드 공급자로부터 이 데이터를 보호합니다.

## <a name="overview"></a>개요

AKS(Azure Kubernetes Service)는 Intel SGX에서 제공되는 [DCsv2 기밀 컴퓨팅 노드](confidential-computing-enclaves.md) 추가를 지원합니다. 이러한 노드 실행은 사용자 수준 코드에서 프라이빗 메모리 영역을 할당할 수 있도록 하여 하드웨어 기반 TEE(신뢰할 수 있는 실행 환경) 내에서 중요한 워크로드를 실행할 수 있습니다. 이러한 프라이빗 메모리 영역을 enclave라고 합니다. enclave는 더 높은 권한으로 실행되는 프로세스로부터 코드와 데이터를 보호하도록 설계되었습니다. SGX 실행 모델은 게스트 OS, 호스트 OS 및 하이퍼바이저의 중간 계층을 제거합니다. *컨테이너 격리 실행별 하드웨어 기반* 모델을 사용하면 특수 메모리 블록을 암호화된 상태로 유지하면서 CPU로 애플리케이션을 직접 실행할 수 있습니다. 기밀 컴퓨팅 노드는 AKS에서 컨테이너 애플리케이션의 전반적인 보안 상태와 심층 방어 컨테이너 전략을 지원하는 데 도움이 됩니다. 

![SGX 노드 개요](./media/confidential-nodes-aks-overview/sgxaksnode.jpg)

## <a name="aks-confidential-nodes-features"></a>AKS 기밀 노드 기능

- SGX TEE(신뢰할 수 있는 실행 환경)를 통한 하드웨어 기반 및 프로세스 수준 컨테이너 격리 
- 형식이 다른 노드 풀 클러스터(기밀 및 비 기밀 노드 풀의 혼합)
- EPC(암호화된 페이지 캐시) 메모리 기반 Pod 예약
- SGX DCAP 드라이버 미리 설치
- Intel FSGS 패치 미리 설치
- CPU 사용량 기반 수평 Pod 자동 크기 조정 및 클러스터 자동 크기 조정 지원
- AKS 디먼 집합을 통한 out-of-proc 증명 도우미
- Ubuntu 18.04 Gen 2 VM 작업자 노드를 통한 Linux 컨테이너 지원

## <a name="aks-provided-daemon-sets-addon"></a>AKS에서 제공하는 디먼 집합(추가 기능)

#### <a name="sgx-device-plugin"></a>SGX 디바이스 플러그 인 <a id="sgx-plugin"></a>

SGX 디바이스 플러그 인은 EPC 메모리용 Kubernetes 디바이스 플러그 인 인터페이스를 구현합니다. 실제로 이 플러그 인은 EPC 메모리를 Kubernetes의 추가 리소스 종류로 만듭니다. 사용자는 다른 리소스와 마찬가지로 이 리소스에 대한 제한을 지정할 수 있습니다. 디바이스 플러그 인은 예약 기능 외에도 SGX 디바이스 드라이버 권한을 기밀 워크로드 컨테이너에 할당하는 데 도움이 됩니다. EPC 메모리 기반 배포(`kubernetes.azure.com/sgx_epc_mem_in_MiB`) 샘플의 샘플 구현은 [여기](https://github.com/Azure-Samples/confidential-computing/blob/main/containersamples/helloworld/helm/templates/helloworld.yaml)에 있습니다.

#### <a name="sgx-quote-helper-service"></a>SGX Quote 도우미 서비스 <a id="sgx-quote"></a>

원격 증명을 수행하는 enclave 애플리케이션은 QUOTE를 생성해야 합니다. QUOTE는 애플리케이션의 ID와 상태, enclave가 실행되는 환경에 대한 암호화 증명을 제공합니다. QUOTE 생성에는 SGX 플랫폼 소프트웨어 구성 요소(PSW/DCAP)의 일부인 Intel의 신뢰할 수 있는 특정 소프트웨어 구성 요소가 필요합니다. 이 PSW는 노드당 실행되는 디먼 집합으로 패키지되어 있습니다. enclave 앱에서 증명 QUOTE를 요청할 때 활용할 수 있습니다. AKS에서 제공하는 서비스를 사용하면 호스트의 PSW와 다른 SW 구성 요소 간의 호환성을 더 효율적으로 유지할 수 있습니다. 사용법 및 기능 세부 정보에 대해 [자세히 참조하세요](confidential-nodes-out-of-proc-attestation.md).

## <a name="programming--application-models"></a>프로그래밍 및 애플리케이션 모델

### <a name="confidential-containers"></a>기밀 컨테이너

[기밀 컨테이너](confidential-containers.md)는 소스 코드를 수정하거나 다시 컴파일하지 않고 기존 라이브러리 종속성과 함께 기존 프로그램 및 대부분의 **일반적인 프로그래밍 언어** 런타임(Python, Node, Java 등)을 실행합니다. 이 모델은 오픈 소스 프로젝트 및 Azure 파트너를 통해 사용하도록 설정되는 가장 빠른 기밀성 모델입니다. 보안 enclave에서 실행되도록 준비된 컨테이너 이미지를 기밀 컨테이너라고 합니다.

### <a name="enclave-aware-containers"></a>enclave 인식 컨테이너

AKS는 기밀 노드에서 실행되도록 프로그래밍된 애플리케이션을 지원하고, SDK 및 프레임워크를 통해 제공되는 **특수 명령 집합** 을 활용합니다. 이 애플리케이션 모델은 가장 낮은 TCB(신뢰할 수 있는 컴퓨팅 기반)를 사용하여 애플리케이션에 대한 대부분의 제어를 제공합니다. enclave 인식 컨테이너에 대해 [자세히 참조하세요](enclave-aware-containers.md).

## <a name="next-steps"></a>다음 단계

[기밀 컴퓨팅 노드를 사용하여 AKS 클러스터 배포](./confidential-nodes-aks-get-started.md)

[빠른 시작 기밀 컨테이너 샘플](https://github.com/Azure-Samples/confidential-container-samples)

[DCsv2 SKU 목록](https://docs.microsoft.com/azure/virtual-machines/dcv2-series)

<!-- LINKS - external -->
[Azure Attestation]: https://docs.microsoft.com/en-us/azure/attestation/


<!-- LINKS - internal -->
[DC Virtual Machine]: /confidential-computing/virtual-machine-solutions
