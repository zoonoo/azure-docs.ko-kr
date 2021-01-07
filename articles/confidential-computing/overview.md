---
title: Azure 기밀 컴퓨팅 개요
description: ACC(Azure 기밀 컴퓨팅) 개요
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.subservice: workloads
ms.topic: overview
ms.date: 09/22/2020
ms.author: JenCook
ms.openlocfilehash: 44479db6b29075e87b0d2dcef6f0d9bc1881738c
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94560828"
---
# <a name="confidential-computing-on-azure"></a>Azure의 기밀 컴퓨팅

Azure 기밀 컴퓨팅을 사용하면 클라우드에서 처리하는 동안 중요한 데이터를 격리할 수 있습니다. 많은 업계에서 기밀 컴퓨팅을 사용하여 기밀 컴퓨팅을 통해 데이터를 보호합니다.

- 금융 데이터 보안
- 환자 정보 보호
- 중요한 정보에 대한 기계 학습 프로세스 실행
- 여러 원본에서 암호화된 데이터 세트에 대한 알고리즘 수행


## <a name="overview"></a>개요
<p><p>


> [!VIDEO https://www.youtube.com/embed/rT6zMOoLEqI]

클라우드 데이터 보안이 중요하다는 것을 알고 있습니다. 관심 사항에도 주의를 기울이고 있습니다. 중요한 워크로드를 클라우드로 이동할 때 고객이 가질 수 있는 몇 가지 질문은 다음과 같습니다. 

- Microsoft에서 암호화되지 않은 데이터에 액세스하지 못하도록 하려면 어떻게 해야 하나요?
- 회사 내에서 권한이 있는 관리자의 보안 위협을 방지하려면 어떻게 해야 하나요?
- 타사에서 중요한 고객 데이터에 액세스하지 못하도록 방지하는 다른 방법은 무엇인가요?

Microsoft Azure를 통해 공격 표면을 최소화하여 더 강력한 데이터 보호를 확보할 수 있습니다. Azure는 클라이언트 쪽 암호화 및 서버 쪽 암호화와 같은 모델을 통해 [**미사용 데이터**](../security/fundamentals/encryption-atrest.md)를 보호할 수 있는 많은 도구를 이미 제공하고 있습니다. 또한 Azure는 TLS 및 HTTPS와 같은 보안 프로토콜을 통해 [**전송 중 데이터**](../security/fundamentals/data-encryption-best-practices.md#protect-data-in-transit)를 암호화하는 메커니즘도 제공합니다. 이 페이지에서는 데이터 암호화의 세 번째 단계인 **사용 중 데이터** 암호화를 소개합니다.

## <a name="introduction-to-confidential-computing"></a>기밀 컴퓨팅 소개  

기밀 컴퓨팅은 기밀 컴퓨팅의 채택을 정의 및 가속화하기 위한 기반인 [CCC(기밀 컴퓨팅 컨소시엄)](https://confidentialcomputing.io/)에서 정의한 업계 용어입니다. CCC는 다음과 같이 기밀 컴퓨팅을 정의합니다. 하드웨어 기반 TEE(신뢰할 수 있는 실행 환경)에서 계산을 수행하여 사용 중인 데이터 보호

TEE는 권한 있는 코드만 실행하는 환경입니다. TEE의 모든 데이터는 해당 환경 외부의 코드에서 읽거나 변경할 수 없습니다. 

### <a name="lessen-the-need-for-trust"></a>신뢰의 필요성 완화
클라우드에서 워크로드를 실행하려면 트러스트가 필요합니다. 애플리케이션의 다양한 구성 요소를 사용하는 다양한 공급자에게 이 트러스트를 제공합니다.


**앱 소프트웨어 공급 업체**: 오픈 소스를 사용하거나 사내 애플리케이션 소프트웨어를 빌드하여 온-프레미스를 배포하여 소프트웨어를 신뢰합니다.

**하드웨어 공급 업체**: 온-프레미스 하드웨어 또는 사내 하드웨어를 사용하여 하드웨어를 신뢰합니다. 

**인프라 공급자**: 클라우드 공급자를 신뢰하거나 고유한 온-프레미스 데이터 센터를 관리합니다.


Azure 기밀 컴퓨팅을 사용하면 컴퓨팅 클라우드 인프라의 다양한 측면에서 신뢰 필요성을 줄여 클라우드 공급자를 더 쉽게 신뢰할 수 있습니다. Azure 기밀 컴퓨팅은 호스트 OS 커널, 하이퍼바이저, VM 관리자 및 호스트 관리자에 대한 트러스트를 최소화합니다.

### <a name="reducing-the-attack-surface"></a>공격 노출 영역 축소
TCB(신뢰할 수 있는 컴퓨팅 기반)는 보안 환경을 제공하는 시스템의 모든 하드웨어, 펌웨어 및 소프트웨어 구성 요소를 나타냅니다. TCB 내의 구성 요소는 "위험"으로 간주됩니다. TCB 내의 한 구성 요소가 손상되면 전체 시스템 보안이 위태로워질 수 있습니다. 

낮은 TCB는 더 높은 수준의 보안을 의미합니다. 다양한 취약점, 맬웨어, 공격 및 악의적인 사용자에게 노출될 위험이 줄어듭니다. Azure 기밀 컴퓨팅은 TEE를 제공하여 클라우드 워크로드에 대한 TCB를 낮추는 것을 목표로 합니다. TEE는 신뢰할 수 있는 런타임 이진 파일, 코드 및 라이브러리에 대한 TCB를 축소합니다. 기밀 컴퓨팅에 Azure 인프라 및 서비스를 사용하는 경우 TCB에서 모든 Microsoft를 제거할 수 있습니다.


## <a name="using-azure-for-cloud-based-confidential-computing"></a>클라우드 기반 기밀 컴퓨팅에 Azure 사용 <a id="cc-on-azure"></a>

Azure 기밀 컴퓨팅을 사용하면 가상화된 환경에서 기밀 컴퓨팅 기능을 활용할 수 있습니다. 이제 도구, 소프트웨어 및 클라우드 인프라를 사용하여 보안 하드웨어를 기반으로 하여 빌드할 수 있습니다.  

**무단 액세스 방지**: 클라우드에서 중요한 데이터를 실행합니다. Azure는 가능한 한 최적의 데이터 보호 기능을 제공하며, 지금은 어떤 것을 변경하지 않아도 됩니다.

**규정 준수**: 클라우드로 마이그레이션하고 개인 정보를 보호하기 위해 정부 규정을 충족하고 조직 IP를 보호하도록 모든 데이터를 완벽하게 제어합니다.

**안전하고 신뢰할 수 없는 협업**: 다양한 데이터 분석 및 더욱 심층적인 인사이트를 활용하기 위해 여러 조직에서(경쟁업체에서도) 데이터를 결합함으로써 업계 전체의 작업 규모 문제를 해결합니다.

**격리된 처리**: 블라인드 처리를 사용하여 개인 데이터에 대한 책임을 없애는 새로운 제품을 제공합니다. 서비스 공급자는 사용자 데이터를 검색할 수도 없습니다. 

## <a name="get-started"></a>시작하기
### <a name="azure-compute"></a>Azure Compute
Azure에서 기밀 컴퓨팅 IaaS 제품을 기반으로 애플리케이션을 빌드합니다.
- VM(Virtual Machines): [DCsv2 시리즈](confidential-computing-enclaves.md)
- AKS(Azure Kubernetes): [기밀 컨테이너 오케스트레이션](confidential-nodes-aks-overview.md)

### <a name="azure-security"></a>Azure Security 
확인 방법 및 하드웨어 바인딩 키 관리를 통해 워크로드의 보안을 유지합니다. 
- 증명: [Microsoft Azure Attestation(미리 보기)](../attestation/overview.md)
- 키 관리: 관리형 HSM(미리 보기)

### <a name="develop"></a>개발
enclave 인식 애플리케이션 개발 사용을 시작하고 기밀 추론 프레임워크를 사용하여 비밀 알고리즘을 배포합니다.
- DCsv2 VM에서 실행할 애플리케이션을 작성합니다. [enclave SDK 열기](https://github.com/openenclave/openenclave)
- ONNX 런타임의 기밀 ML 모델: [기밀 추론(베타)](https://aka.ms/confidentialinference)

## <a name="next-steps"></a>다음 단계

DCsv2 시리즈 가상 머신을 배포하고 여기에 OE SDK를 설치합니다.

> [!div class="nextstepaction"]
> [Azure Marketplace에서 기밀 컴퓨팅 VM 배포](quick-create-marketplace.md)