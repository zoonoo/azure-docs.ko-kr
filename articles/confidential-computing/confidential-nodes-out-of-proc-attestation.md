---
title: Azure에서 Intel SGX Quote 도우미인 DaemonSet를 사용하여 증명 지원(미리 보기)
description: DaemonSet는 Intel SGX 애플리케이션 프로세스 외부에서 Quote를 생성합니다. 이 문서에서는 Out Of Process 증명 기능을 컨테이너 내에서 실행되는 기밀 워크로드에 제공하는 방법을 설명합니다.
ms.service: container-service
ms.subservice: confidential-computing
author: agowdamsft
ms.topic: overview
ms.date: 2/12/2021
ms.author: amgowda
ms.openlocfilehash: 849fd7afa3f9365f31ee8e03d9f9cc2174d64304
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484407"
---
# <a name="platform-software-management-with-intel-sgx-quote-helper-daemonset-preview"></a>Intel SGX Quote 도우미인 DaemonSet를 사용하는 플랫폼 소프트웨어 관리(미리 보기)

원격 증명을 수행하는 [enclave 애플리케이션](confidential-computing-enclaves.md)에는 생성된 Quote가 필요합니다. 이 Quote는 enclave가 실행되는 환경뿐만 아니라 애플리케이션의 ID와 상태에 대한 암호화 증명도 제공합니다. Quote를 생성하려면 Intel PSW(플랫폼 소프트웨어 구성 요소)에 포함된 신뢰할 수 있는 소프트웨어 구성 요소가 필요합니다.

## <a name="overview"></a>개요
 
Intel은 quote 생성을 실행하는 두 가지 증명 모드를 지원합니다.

- *In Process* 는 enclave 애플리케이션 프로세스 내부에서 신뢰할 수 있는 소프트웨어 구성 요소를 호스트합니다.

- *Out Of Process* 는 enclave 애플리케이션 외부에서 신뢰할 수 있는 소프트웨어 구성 요소를 호스트합니다.
 
Open Enclave SDK를 사용하여 작성된 Intel SGX(Intel Software Guard Extension) 애플리케이션은 기본적으로 In Process 증명 모드를 사용합니다. 그러나 Intel SGX 기반 애플리케이션은 Out Of Process 증명 모드를 허용합니다. 이 모드를 사용하려면 추가 호스팅이 필요하며, 애플리케이션 외부에 AESM(Advanced Encryption Standard Manager)과 같은 필수 구성 요소를 노출해야 합니다.

이 기능은 Intel 플랫폼 업데이트 또는 DCAP 드라이버 업데이트 중에 enclave 앱의 가동 시간을 향상시킵니다. 이러한 이유로 이 기능을 사용하는 것이 좋습니다.

AKS(Azure Kubernetes Service) 클러스터에서 이 기능을 사용하도록 설정하려면 기밀 컴퓨팅 추가 기능을 사용하도록 설정할 때 Azure CLI에 `--enable-sgxquotehelper` 명령을 추가합니다. 

```azurecli-interactive
# Create a new AKS cluster with system node pool with Confidential Computing addon enabled and SGX Quote Helper
az aks create -g myResourceGroup --name myAKSCluster --generate-ssh-keys --enable-addon confcom --enable-sgxquotehelper
```

자세한 내용은 [빠른 시작: Azure CLI를 사용하여 기밀 컴퓨팅 노드가 있는 AKS 클러스터 배포](confidential-nodes-aks-get-started.md)를 참조하세요.

## <a name="benefits-of-the-out-of-process-mode"></a>Out Of Process 모드의 이점

다음 목록에서는 이 증명 모드의 주요 이점 중 일부에 대해 설명합니다.

-   컨테이너화된 각 애플리케이션에는 PSW의 Quote 생성 구성 요소에 대한 업데이트가 필요하지 않습니다. 컨테이너 소유자가 컨테이너 내에서 업데이트를 관리할 필요가 없습니다. 대신 컨테이너 소유자는 컨테이너 외부에서 중앙 서비스를 호출하는 공급자 인터페이스를 사용합니다. 컨테이너는 공급자가 업데이트하고 관리합니다.

-   오래된 PSW 구성 요소로 인한 증명 실패에 대해서는 걱정할 필요가 없습니다. 공급자가 이러한 구성 요소에 대한 업데이트를 관리합니다.

-   Out Of Process 모드는 In Process 모드에서 수행하는 것보다 더 효율적인 EPC 메모리 사용률을 제공합니다. In Process 증명 모드에서 각 enclave 애플리케이션은 원격 증명을 위해 QE 및 PCE의 복사본을 인스턴스화해야 합니다. Out Of Process 모드를 사용하면 컨테이너에서 이러한 enclave를 호스트할 필요가 없으므로 컨테이너 할당량에서 enclave 메모리를 사용하지 않습니다.

-   Intel SGX 드라이버를 Linux 커널에 업스트림으로 적용하는 경우 enclave에 더 높은 권한을 부여하는 것이 좋습니다. 이 권한을 통해 enclave에서 PCE를 호출할 수 있습니다. 이때 In Process 모드에서 실행되는 enclave 애플리케이션은 중단됩니다. 기본적으로 enclave에는 이 권한이 부여되지 않습니다. 이 권한을 enclave 애플리케이션에 부여하려면 애플리케이션 설치 프로세스를 변경해야 합니다. 반대로 Out Of Process 모드에서 Out Of Process 요청을 처리하는 서비스 공급자는 이 권한을 사용하여 서비스가 설치되도록 합니다.

-   따라서 사용자가 이전 PSW 및 DCAP 버전과의 호환성을 확인할 필요가 없습니다. PSW의 quote 생성 구성 요소에 대한 업데이트는 업데이트하기 전에 공급자가 이전 버전과의 호환성에 대한 유효성을 검사합니다. 이렇게 하면 기밀 워크로드에 대한 업데이트를 배포하기 전에 호환성 문제를 처리할 수 있습니다.

## <a name="confidential-workloads"></a>기밀 워크로드

Quote 요청자 및 Quote 생성은 개별적으로 실행되지만 동일한 물리적 시스템에서 실행됩니다. Quote 생성은 중앙 집중식으로 이루어지며 모든 엔터티의 Quote에 대한 요청을 처리합니다. 엔터티에서 Quote를 요청하려면 인터페이스를 적절히 정의하고 검색할 수 있어야 합니다.

![Quote 요청자, Quote 생성, 인터페이스 간의 관계를 보여 주는 다이어그램입니다.](./media/confidential-nodes-out-of-proc-attestation/aesmmanager.png)

이 추상 모델은 이미 사용 가능한 AESM 서비스를 활용하여 기밀 워크로드 시나리오에 적용됩니다. AESM은 컨테이너화되고 Kubernetes 클러스터에서 DaemonSet로 배포됩니다. Kubernetes는 Pod로 래핑된 AESM 서비스 컨테이너의 단일 인스턴스가 각 에이전트 노드에 배포되도록 보장합니다. AESM 서비스 컨테이너에서 QE 및 PCE enclave를 시작하기 위해 sgx-device-plugin에서 EPC 메모리를 요청하므로 새 Intel SGX Quote DaemonSet는 sgx-device-plugin DaemonSet에 종속됩니다.

각 컨테이너는 만드는 동안 `SGX_AESM_ADDR=1` 환경 변수를 설정하여 Out Of Process Quote 생성을 사용하도록 옵트인해야 합니다. 또한 컨테이너에는 요청을 기본 Unix 도메인 소켓으로 전달해야 하는 libsgx-quote-ex 패키지가 포함되어야 합니다.

애플리케이션은 이전과 같이 In Process 증명을 계속 사용할 수 있지만, 애플리케이션 내에서 In Process 증명과 Out Of Process 증명을 동시에 사용할 수는 없습니다. Out Of Process 인프라는 기본적으로 사용할 수 있으며 리소스를 소비합니다.

## <a name="sample-implementation"></a>샘플 구현

다음 Docker 파일은 Open Enclave 기반 애플리케이션에 대한 샘플입니다. Docker 파일 또는 배포 파일에서 `SGX_AESM_ADDR=1` 환경 변수를 설정합니다. 다음 샘플에서는 Docker 파일 및 배포에 대한 세부 정보를 제공합니다. 

  > [!Note] 
  > Out Of Process 증명이 제대로 작동하려면 Intel의 libsgx-quote-ex를 애플리케이션 컨테이너에 패키지해야 합니다.
    
```yaml
# Refer to Intel_SGX_Installation_Guide_Linux for details
FROM ubuntu:18.04 as sgx_base
RUN apt-get update && apt-get install -y \
    wget \
    gnupg

# Add the repository to sources, and add the key to the list of
# trusted keys used by the apt to authenticate packages
RUN echo "deb [arch=amd64] https://download.01.org/intel-sgx/sgx_repo/ubuntu bionic main" | tee /etc/apt/sources.list.d/intel-sgx.list \
    && wget -qO - https://download.01.org/intel-sgx/sgx_repo/ubuntu/intel-sgx-deb.key | apt-key add -
# Add Microsoft repo for az-dcap-client
RUN echo "deb [arch=amd64] https://packages.microsoft.com/ubuntu/18.04/prod bionic main" | tee /etc/apt/sources.list.d/msprod.list \
    && wget -qO - https://packages.microsoft.com/keys/microsoft.asc | apt-key add -

FROM sgx_base as sgx_sample
RUN apt-get update && apt-get install -y \
    clang-7 \
    libssl-dev \
    gdb \
    libprotobuf10 \
    libsgx-dcap-ql \
    libsgx-quote-ex \
    az-dcap-client \
    open-enclave
WORKDIR /opt/openenclave/share/openenclave/samples/remote_attestation
RUN . /opt/openenclave/share/openenclave/openenclaverc \
    && make build
# This sets the flag for out-of-process attestation mode. Alternatively you can set this flag on the deployment files.
ENV SGX_AESM_ADDR=1 

CMD make run
```
또는 배포. yaml 파일에서 Out Of Process 증명 모드를 설정할 수 있습니다. 방법은 다음과 같습니다.

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: sgx-test
spec:
  template:
    spec:
      containers:
      - name: sgxtest
        image: <registry>/<repository>:<version>
        env:
        - name: SGX_AESM_ADDR
          value: 1
        resources:
          limits:
            kubernetes.azure.com/sgx_epc_mem_in_MiB: 10
        volumeMounts:
        - name: var-run-aesmd
          mountPath: /var/run/aesmd
      restartPolicy: "Never"
      volumes:
      - name: var-run-aesmd
        hostPath:
          path: /var/run/aesmd
```

## <a name="next-steps"></a>다음 단계

[빠른 시작: Azure CLI를 사용하여 기밀 컴퓨팅 노드가 있는 AKS 클러스터 배포](./confidential-nodes-aks-get-started.md)

[빠른 시작 샘플 기밀 컨테이너](https://github.com/Azure-Samples/confidential-container-samples)

[DCsv2 SKU](../virtual-machines/dcv2-series.md)
