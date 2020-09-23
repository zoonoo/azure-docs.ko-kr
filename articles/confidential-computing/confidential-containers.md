---
title: Azure Kubernetes 서비스의 기밀 컨테이너 (AKS)
description: 기밀 컨테이너에서 수정 되지 않은 컨테이너 지원에 대해 알아봅니다.
services: container-service
author: agowdamsft
ms.topic: article
ms.date: 9/22/2020
ms.author: amgowda
ms.service: container-service
ms.openlocfilehash: 528b843e24e1d63e4822c253b3636ef490e8fe8e
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90997804"
---
# <a name="confidential-containers"></a>기밀 컨테이너

## <a name="overview"></a>개요

개발자가 **기존 docker 응용 프로그램 (신규 또는 기존)** 을 가져와서 기밀 컴퓨팅 노드 지원을 통해 Azure Kubernetes SERVICE (AKS)에서 안전 하 게 실행할 수 있습니다.

기밀 컨테이너를 보호 하는 데 도움이 됩니다.

- 데이터 무결성(data integrity) 
- 데이터 기밀성
- 코드 무결성
- 암호화를 통한 컨테이너 코드 보호
- 하드웨어 기반 보증
- 기존 앱 실행 허용
- 신뢰의 하드웨어 루트 만들기

하드웨어 기반의 티 (신뢰할 수 있는 실행 환경)는 TCB (신뢰할 수 있는 컴퓨팅 기반) 구성 요소의 하드웨어 및 소프트웨어 측정치를 통해 강력한 보증을 제공 하는 데 사용 되는 중요 한 구성 요소입니다. 이러한 측정의 확인은 예상 된 계산의 유효성을 검사 하 고 컨테이너 앱의 변조를 확인 하는 데 도움이 됩니다.

기밀 컨테이너는 Python, Java, NODE.JS 등을 사용 하 여 개발 된 사용자 지정 응용 프로그램을 지원 **하거나 NGINX, Redis Cache, MemCache 등과 같은 패키지 된 소프트웨어 응용 프로그램**을 지원 하 여 AKS에서 수정 되지 않은 상태로 실행할 수 있습니다.

기밀 컨테이너는 암호화를 통한 컨테이너 보호를 비롯 한 컨테이너 기밀성의 가장 빠른 경로 이며 비즈니스 논리에 대 한 변경 내용 없이 리프트 앤 시프트를 사용 하도록 설정 합니다.

![기밀 컨테이너 변환](./media/confidential-containers/conf-con-deploy-process.jpg)


## <a name="confidential-container-enablers"></a>기밀 컨테이너 Enablers

기존 docker 컨테이너를 실행 하려면 기밀 컴퓨팅 노드의 응용 프로그램에는 특수 CPU 명령 집합을 활용 하기 위한 추상화 계층 또는 SGX 소프트웨어가 필요 합니다. 또한 SGX 소프트웨어를 사용 하 여 중요 한 응용 프로그램 코드를 보호 하 고 CPU에 대 한 직접 실행을 만들어 게스트 OS, 호스트 OS 또는 하이퍼바이저를 제거할 수 있습니다. 이러한 보호는 전체 노출 공격 영역 및 운영 체제 또는 하이퍼바이저 계층과 관련 된 취약성을 줄입니다.

기밀 컨테이너는 AKS에서 완벽 하 게 지원 되며 Azure 파트너와 OSS (오픈 소스 소프트웨어) 프로젝트를 통해 사용할 수 있습니다. 개발자는 기능, Azure 서비스 및 도구 지원에 대 한 통합을 기반으로 소프트웨어 공급자를 선택할 수 있습니다.

## <a name="partner-enablers"></a>파트너 Enablers
> [!NOTE]
> 아래 솔루션은 Azure 파트너를 통해 제공 되며 라이선스 요금을 발생 시킬 수 있습니다. 파트너 소프트웨어 용어를 독립적으로 확인 하세요. 

### <a name="fortanix"></a>Fortanix

[Fortanix](https://www.fortanix.com/) 는 개발자에 게 응용 프로그램을 수정 하거나 다시 컴파일하지 않고도 컨테이너 화 된 응용 프로그램을 가져와서 SGX 지원 기밀 컨테이너로 변환할 수 있는 포털 및 CLI 기반 환경을 선택할 수 있습니다. Fortanix는 기존 응용 프로그램, 새로운 enclave 응용 프로그램 및 사전 패키지 된 응용 프로그램을 포함 하 여 광범위 한 응용 프로그램을 실행 하 고 관리할 수 있는 유연성을 제공 합니다. 사용자는 [Enclave Manager](https://em.fortanix.com/) UI 또는 [REST api](https://www.fortanix.com/api/em/) 를 시작 하 여 Azure Kubernetes 서비스에 대 한 [빠른 시작](https://support.fortanix.com/hc/en-us/articles/360049658291-Fortanix-Confidential-Container-on-Azure-Kubernetes-Service) 가이드에 따라 기밀 컨테이너를 만들 수 있습니다.

![Fortanix 배포 프로세스](./media/confidential-containers/fortanix-confidential-containers-flow.png)

### <a name="scone-scontain"></a>Scone (Scone)

[Scone](https://scontain.com/index.html?lang=en) 는 인증서, 키 및 비밀을 생성할 수 있는 보안 정책을 지원 하 고 응용 프로그램의 증명 된 서비스에만 표시 되도록 합니다. 이러한 방식으로 응용 프로그램 또는 TLS를 수정할 필요 없이 응용 프로그램의 서비스는 TLS를 통해 서로를 자동으로 증명 합니다. 여기에 대 한 자세한 설명은 여기에서 간단한 Flask 응용 프로그램을 참조 하세요. https://sconedocs.github.io/flask_demo/  

SCONE는 응용 프로그램을 변경 하거나 해당 응용 프로그램을 다시 컴파일할 필요 없이 기존 대부분의 이진 파일을 enclaves 내부에서 실행 되는 응용 프로그램으로 변환할 수 있습니다. 또한 SCONE은 Python 코드 파일 뿐만 아니라 데이터 파일을 모두 암호화 하 여 Python과 같은 해석 된 언어도 보호 합니다. SCONE 보안 정책의 도움을 통해 암호화 된 파일을 무단 액세스, 수정 및 롤백 으로부터 보호할 수 있습니다. [여기](https://sconedocs.github.io/sconify_image/) 에서 기존 Python 응용 프로그램을 "sconify" 하는 방법에 대해 설명 합니다.

![Scontain 흐름](./media/confidential-containers/scone-workflow.png)

AKS를 사용 하는 기밀 컴퓨팅 노드의 scone 배포는 완전히 지원 되 고 통합 됩니다. 여기에서 샘플 응용 프로그램을 시작 하세요. https://sconedocs.github.io/aks/

### <a name="anjuna"></a>Anjuna

[Anjuna](https://www.anjuna.io/) 는 AKS에서 수정 되지 않은 컨테이너를 실행할 수 있도록 하는 SGX 플랫폼 소프트웨어를 제공 합니다. **예정** 된 기능 및 사용자에 대 한 자세한 내용은 [여기](https://www.anjuna.io/microsoft-azure-confidential-computing-aks-lp)를 참조 하세요.

[여기](https://www.anjuna.io/microsoft-azure-confidential-computing-aks-lp) 에서 샘플 Redis Cache 및 Python 사용자 지정 응용 프로그램을 시작 하세요.

![Anjuna 프로세스](./media/confidential-containers/anjuna-process-flow.png)

## <a name="oss-enablers"></a>OSS Enablers 
> [!NOTE]
> 아래 솔루션은 오픈 소스 프로젝트를 통해 제공 되며, Azure 기밀 컴퓨팅 (ACC) 또는 Microsoft와 직접 관련이 없습니다.  

### <a name="graphene"></a>Graphene

[Graphene](https://grapheneproject.io/) 는 최소한의 호스트 요구 사항으로 단일 Linux 응용 프로그램을 실행 하도록 설계 된 경량 게스트 OS입니다. Graphene는 완전 한 OS를 실행 하는 것과 유사한 이점을 제공 하는 격리 된 환경에서 응용 프로그램을 실행할 수 있으며 기존 docker 컨테이너 응용 프로그램을 Graphene 차폐 컨테이너 (GSC)로 변환할 수 있는 훌륭한 도구를 지원 합니다

[여기](https://graphene.readthedocs.io/en/latest/cloud-deployment.html#azure-kubernetes-service-aks) 에서 AKS에 대 한 샘플 응용 프로그램 및 배포를 시작 하세요.

### <a name="occlum"></a>Occlum
[Occlum](https://occlum.io/) 는 Intel SGX 용 메모리 안전, 다중 프로세스 라이브러리 OS (LibOS)입니다. 이를 통해 레거시 응용 프로그램을 소스 코드를 수정 하지 않고도 SGX에서 실행할 수 있습니다. Occlum는 사용자 작업의 기밀성을 투명 하 게 보호 하는 동시에 기존 docker 응용 프로그램을 쉽게 리프트 하 고 이동할 수 있도록 합니다.

Occlum는 AKS 배포를 지원 합니다. [여기](https://github.com/occlum/occlum/blob/master/docs/azure_aks_deployment_guide.md) 에서 다양 한 샘플 앱을 사용 하 여 배포 지침을 따릅니다.


## <a name="confidential-containers-demo"></a>기밀 컨테이너 데모
기밀 컨테이너를 사용 하 여 기밀 의료 데모를 봅니다. 샘플은 [여기](https://github.com/Azure-Samples/confidential-container-samples/blob/main/confidential-healthcare-scone-confinf-onnx/README.md)에서 사용할 수 있습니다. 

> [!VIDEO https://www.youtube.com/embed/PiYCQmOh0EI]


## <a name="get-in-touch"></a>터치 받기

구현에 대 한 질문이 있거나 수단이 되 고 싶으세요? acconaks@microsoft.com으로 이메일 보내기

## <a name="reference-links"></a>참조 링크

[Microsoft Azure 증명](../attestation/overview.md)

[DCsv2 Virtual Machines](virtual-machine-solutions.md)

[AKS(Azure Kubernetes Service)](../aks/intro-kubernetes.md)
