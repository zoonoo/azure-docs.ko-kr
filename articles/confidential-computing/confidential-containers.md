---
title: Azure Kubernetes 서비스의 기밀 컨테이너 (AKS)
description: 기밀 컨테이너에서 수정 되지 않은 컨테이너 지원에 대해 알아봅니다.
services: container-service
author: agowdamsft
ms.topic: article
ms.date: 2/11/2020
ms.author: amgowda
ms.service: container-service
ms.openlocfilehash: a5db93f096c73679c1b6b6ae464897db843f2e8b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101706268"
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
- 호스트 관리자, Kubernetes 관리자, 트러스트 경계에서 하이퍼바이저 제거

하드웨어 기반의 티 (신뢰할 수 있는 실행 환경)는 TCB (신뢰할 수 있는 컴퓨팅 기반) 구성 요소의 하드웨어 및 소프트웨어 측정치를 통해 강력한 보증을 제공 하는 데 사용 되는 중요 한 구성 요소입니다. 이러한 측정의 확인은 예상 된 계산의 유효성을 검사 하 고 컨테이너 앱의 변조를 확인 하는 데 도움이 됩니다.

기밀 컨테이너는 Python, Java, NODE.JS 등을 사용 하 여 개발 된 사용자 지정 응용 프로그램을 지원 **하거나 NGINX, Redis Cache, MemCache 등의 패키지 된 컨테이너 응용 프로그램** 을 지원 하 여 기밀 컴퓨팅 노드가 있는 AKS에서 수정 되지 않은 상태로 실행 됩니다.

기밀 컨테이너는 컨테이너 기밀성의 가장 빠른 경로 이며 기존 docker 컨테이너 응용 프로그램의 다시 패키지 필요 하며 응용 프로그램 코드를 변경 하지 않아도 됩니다. 기밀 컨테이너는 티에서 실행 되도록 패키지 된 docker 컨테이너 응용 프로그램입니다. 또한 일부 기밀 컨테이너 조력자는 저장소 및 전송 중에 컨테이너 코드를 보호 하는 데 도움이 되는 컨테이너 암호화와 호스트에 탑재 된 상태를 제공 합니다. 컨테이너 암호화를 사용 하 여 추가 하 고, 해당 암호 해독 키를 사용 하 여 컨테이너에 패키지 된 코드/모델을 보호할 수 있습니다.

다음은 개발에서 배포로의 기밀 컨테이너를 처리 하는 ![ 방법입니다.](./media/confidential-containers/how-to-confidential-container.png)

## <a name="confidential-container-enablers"></a>기밀 컨테이너 Enablers
수정 되지 않은 기존 docker 컨테이너를 실행 하려면 응용 프로그램이를 호출할 때 사용 가능한 특수 CPU 명령 집합을 사용 하 여 연결 노출 영역을 낮출 수 있으며 게스트 OS에 대 한 종속성을 갖지 않습니다. SGX 런타임 소프트웨어로 래핑된 컨테이너는 보호 된 enclaves에서 자동으로 시작 되므로 트러스트 경계에서 게스트 OS, 호스트 OS 또는 하이퍼바이저가 제거 됩니다. 하드웨어에서 지원 되는 메모리 데이터 암호화를 사용 하 여 노드 (가상 머신)에서 이러한 격리 된 실행은 전반적인 노출 공격 영역을 줄이고 운영 체제 또는 하이퍼바이저 계층과 관련 된 취약성을 줄입니다.

기밀 컨테이너는 AKS에서 완벽 하 게 지원 되며 Azure 파트너 및 OSS (Open-Source 소프트웨어) 프로젝트를 통해 사용할 수 있습니다. 개발자는 기능, Azure 서비스 및 도구 지원에 대 한 통합을 기반으로 소프트웨어 공급자를 선택할 수 있습니다.

## <a name="partner-enablers"></a>파트너 Enablers
> [!NOTE]
> 아래 솔루션은 Azure 파트너를 통해 제공 되며 라이선스 요금을 발생 시킬 수 있습니다. 파트너 소프트웨어 용어를 독립적으로 확인 하세요. 

### <a name="anjuna"></a>Anjuna

[Anjuna](https://www.anjuna.io/) 는 AKS에서 수정 되지 않은 컨테이너를 실행할 수 있도록 하는 SGX 플랫폼 소프트웨어를 제공 합니다. 이 기능에 대해 자세히 알아보고 [여기](https://www.anjuna.io/microsoft-azure-confidential-computing-aks-lp)에서 샘플 응용 프로그램을 확인 하세요.

[여기](https://www.anjuna.io/microsoft-azure-confidential-computing-aks-lp) 에서 샘플 Redis Cache 및 Python 사용자 지정 응용 프로그램을 시작 하세요.

![Anjuna 프로세스](./media/confidential-containers/anjuna-process-flow.png)

### <a name="fortanix"></a>Fortanix

[Fortanix](https://www.fortanix.com/) 는 개발자에 게 응용 프로그램을 수정 하거나 다시 컴파일하지 않고도 컨테이너 화 된 응용 프로그램을 가져와서 SGX 지원 기밀 컨테이너로 변환할 수 있는 포털 및 CLI 기반 환경을 선택할 수 있습니다. Fortanix는 기존 응용 프로그램, 새로운 enclave 응용 프로그램 및 사전 패키지 된 응용 프로그램을 포함 하 여 광범위 한 응용 프로그램을 실행 하 고 관리할 수 있는 유연성을 제공 합니다. 사용자는 Azure Kubernetes 서비스에 대 한 [빠른 시작](https://support.fortanix.com/hc/en-us/articles/360049658291-Fortanix-Confidential-Container-on-Azure-Kubernetes-Service) 가이드에 따라 기밀 컨테이너를 만들 수 있도록 [기밀 컴퓨팅 관리자](https://em.fortanix.com/) UI 또는 [REST api](https://www.fortanix.com/api/em/) 로 시작할 수 있습니다.

![Fortanix 배포 프로세스](./media/confidential-containers/fortanix-confidential-containers-flow.png)

### <a name="scone-scontain"></a>Scone (Scone)

[Scone](https://scontain.com/index.html?lang=en) 는 인증서, 키 및 비밀을 생성할 수 있는 보안 정책을 지원 하 고 응용 프로그램의 증명 된 서비스에만 표시 되도록 합니다. 이러한 방식으로 응용 프로그램 또는 TLS를 수정할 필요 없이 응용 프로그램의 서비스는 TLS를 통해 서로를 자동으로 증명 합니다. 여기에 대 한 자세한 설명은 여기에서 간단한 Flask 응용 프로그램을 참조 하세요. https://sconedocs.github.io/flask_demo/  

SCONE는 응용 프로그램을 변경 하거나 해당 응용 프로그램을 다시 컴파일할 필요 없이 기존 대부분의 이진 파일을 enclaves 내부에서 실행 되는 응용 프로그램으로 변환할 수 있습니다. 또한 SCONE은 Python 코드 파일 뿐만 아니라 데이터 파일을 모두 **암호화** 하 여 python과 같은 해석 된 언어도 보호 합니다. SCONE 보안 정책의 도움을 통해 암호화 된 파일을 무단 액세스, 수정 및 롤백 으로부터 보호할 수 있습니다. [여기](https://sconedocs.github.io/sconify_image/) 에서 기존 Python 응용 프로그램을 "sconify" 하는 방법에 대해 설명 합니다.

![Scontain 흐름](./media/confidential-containers/scone-workflow.png)

AKS를 사용 하는 기밀 컴퓨팅 노드의 scone 배포는 완전히 지원 되 고 다른 Azure 서비스와 통합 됩니다. 여기에서 샘플 응용 프로그램을 시작 하세요. https://sconedocs.github.io/aks/


## <a name="oss-enablers"></a>OSS Enablers 
> [!NOTE]
> 아래 솔루션은 Open-Source 프로젝트를 통해 제공 되며, Azure 기밀 컴퓨팅 (ACC) 또는 Microsoft와 직접 관련 되지 않습니다.  

### <a name="graphene"></a>Graphene

[Graphene](https://grapheneproject.io/) 는 최소한의 호스트 요구 사항으로 단일 Linux 응용 프로그램을 실행 하도록 설계 된 경량 게스트 OS입니다. Graphene는 완전 한 OS를 실행 하는 것과 유사한 이점을 제공 하는 격리 된 환경에서 응용 프로그램을 실행할 수 있으며 기존 docker 컨테이너 응용 프로그램을 Graphene 차폐 컨테이너 (GSC)로 변환할 수 있는 훌륭한 도구를 지원 합니다

[여기](https://graphene.readthedocs.io/en/latest/cloud-deployment.html#azure-kubernetes-service-aks) 에서 AKS에 대 한 샘플 응용 프로그램 및 배포를 시작 하세요.

### <a name="occlum"></a>Occlum
[Occlum](https://occlum.io/) 는 Intel SGX 용 메모리 안전, 다중 프로세스 라이브러리 OS (LibOS)입니다. 이를 통해 레거시 응용 프로그램을 소스 코드를 수정 하지 않고도 SGX에서 실행할 수 있습니다. Occlum는 사용자 작업의 기밀성을 투명 하 게 보호 하는 동시에 기존 docker 응용 프로그램을 쉽게 리프트 하 고 이동할 수 있도록 합니다.

Occlum는 AKS 배포를 지원 합니다. [여기](https://github.com/occlum/occlum/blob/master/docs/azure_aks_deployment_guide.md) 에서 다양 한 샘플 앱을 사용 하 여 배포 지침을 따릅니다.


## <a name="confidential-containers-demo"></a>기밀 컨테이너 데모
기밀 컨테이너를 사용 하 여 기밀 의료 데모를 봅니다. 샘플은 [여기](/azure/architecture/example-scenario/confidential/healthcare-inference)에서 사용할 수 있습니다. 

> [!VIDEO https://www.youtube.com/embed/PiYCQmOh0EI]


## <a name="get-in-touch"></a>터치 받기

구현에 대 한 질문이 있거나 수단이 되 고 싶으세요? 제품 팀에 전자 메일 보내기 **acconaks@microsoft.com**

## <a name="reference-links"></a>참조 링크

[Microsoft Azure Attestation](../attestation/overview.md)

[DCsv2 Virtual Machines](virtual-machine-solutions.md)

[AKS(Azure Kubernetes Service)](../aks/intro-kubernetes.md)