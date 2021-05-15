---
title: Azure 기밀 컴퓨팅 가상 머신
description: 기밀 컴퓨팅 워크로드를 사용할 수 있게 하는 Intel SGX 하드웨어에 대해 알아봅니다.
services: virtual-machines
author: JenCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 9/3/2020
ms.author: JenCook
ms.openlocfilehash: 554260b2a2760380d3bb2d91ee25b4a03bf2f1ae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102551371"
---
# <a name="azure-confidential-computing-virtual-machines-vms-overview"></a>Azure 기밀 컴퓨팅 가상 머신(VM) 개요


Azure는 가상화된 환경에서 기밀 컴퓨팅을 제공하는 최초의 클라우드 공급자입니다. 하드웨어와 애플리케이션 간의 추상화 계층으로 작동하는 가상 머신이 개발되었습니다. 중복성 및 가용성 옵션을 사용하여 규모에 맞게 워크로드를 실행할 수 있습니다.  

## <a name="intel-sgx-enabled-virtual-machines"></a>Intel SGX 사용 Virtual Machines

Azure 기밀 컴퓨팅 가상 머신에서 CPU 하드웨어의 일부는 애플리케이션의 일부 코드 및 데이터를 위해 예약됩니다. 이 제한된 부분이 enclave입니다. 

![VM 모델](media/overview/hardware-backed-enclave.png)

Azure 기밀 컴퓨팅 인프라는 현재 VM(가상 머신)의 특수 SKU로 구성되어 있습니다. 이러한 VM은 Intel SGX(Software Guard Extension)가 있는 Intel 프로세서에서 실행됩니다. [Intel SGX](https://intel.com/sgx)는 기밀 컴퓨팅을 통해 더 강화된 보호를 허용하는 구성 요소입니다. 

현재 Azure는 하드웨어 기반 enclave를 만들기 위한 Intel SGX 기술을 기반으로 하는 [DCsv2 시리즈](../virtual-machines/dcv2-series.md)를 제공하고 있습니다. DCsv2 시리즈 VM에서 실행하는 보안 enclave 기반 애플리케이션을 빌드하여 사용 중인 애플리케이션 데이터와 코드를 보호할 수 있습니다. 

신뢰할 수 있는 하드웨어 기반 enclave를 사용하여 Azure 기밀 컴퓨팅 가상 머신을 배포하는 방법에 대해 [자세히 알아봅니다](virtual-machine-solutions.md).

## <a name="enclaves"></a>Enclave

Enclave는 하드웨어의 프로세서 및 메모리에 대한 보안 부분입니다. 디버거를 사용하는 경우에도 enclave 내부의 데이터 또는 코드를 볼 수 있는 방법이 없습니다. 신뢰할 수 없는 코드에서 enclave 메모리의 콘텐츠를 수정하려고 하면 환경이 사용하지 않도록 설정되고 작업이 거부됩니다.

기본적으로 enclave는 보안 박스로 간주됩니다. 암호화된 코드와 데이터를 박스에 넣습니다. 박스 외부에서는 아무것도 볼 수 없습니다. 데이터를 해독할 수 있는 키를 enclave에 지정합니다. 그러면 데이터를 처리하고 다시 암호화한 후에 enclave에서 내보냅니다.

각 enclave에는 각 enclave가 보유할 수 있는 메모리 양을 결정하는 EPC(암호화된 페이지 캐시)의 크기가 설정되어 있습니다. 더 큰 DCsv2 가상 머신에는 더 많은 EPC 메모리가 있습니다. VM 크기당 최대 EPC에 대한 자세한 내용은 [DCsv2 사양](../virtual-machines/dcv2-series.md) 페이지를 참조하세요.



### <a name="developing-applications-to-run-inside-enclaves"></a>Enclaves 내에서 실행할 애플리케이션 개발
애플리케이션을 개발할 때 [소프트웨어 도구](application-development.md)를 사용하여 enclave 내의 코드 및 데이터 부분을 보호할 수 있습니다. 이러한 도구는 신뢰할 수 있는 환경 외부의 사용자가 코드와 데이터를 보거나 수정할 수 없도록 합니다. 

## <a name="next-steps"></a>다음 단계
- Azure 기밀 컴퓨팅 가상 머신에 솔루션을 배포하기 위한 [모범 사례를 참조하세요](virtual-machine-solutions.md).
- [DCsv2 시리즈 가상 머신 배포](quick-create-portal.md)
- OE SDK를 사용하여 [enclave 인식 애플리케이션 개발](application-development.md)