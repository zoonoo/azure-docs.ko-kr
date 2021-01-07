---
title: Azure의 증명 enclaves
description: 증명을 사용 하 여 기밀 컴퓨팅 신뢰할 수 있는 환경이 안전한 지 확인 하는 방법을 알아봅니다.
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 9/22/2020
ms.author: JenCook
ms.openlocfilehash: 86856d1f66b5b7d723c907c17d7179ffcd2b82ab
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94565673"
---
# <a name="attesting-sgx-enclaves"></a>증명 SGX Enclaves

Azure의 기밀 컴퓨팅은 코드 또는 데이터의 일부를 격리할 수 있는 Intel SGX 기반 가상 머신을 제공 합니다. 이러한 [enclaves](confidential-computing-enclaves.md)작업할 때 신뢰할 수 있는 환경이 안전한 지 확인 하 고 유효성 검사를 수행할 수 있습니다. 이 확인은 증명 프로세스입니다. 

## <a name="overview"></a>개요 

증명을 통해 신뢰 당사자는 (1) 자신의 소프트웨어가 enclave에서 실행되고 (2) enclave가 최신 상태이고 안전하다는 확신을 높일 수 있습니다. 예를 들어 enclave는 기본 하드웨어에서 enclave가 플랫폼에 존재한다는 증명을 포함하는 자격 증명을 생성하도록 이 기본 하드웨어에 요청합니다. 그런 다음, 동일한 플랫폼에서 보고서가 생성되었는지 확인하는 두 번째 enclave에 보고서를 제공할 수 있습니다.

![enclave의 코드를 증명 합니다.](media/attestation/attestation.png)



증명은 시스템 소프트웨어 및 실리콘과 호환되는 보안 증명 서비스를 사용하여 구현해야 합니다. 사용할 수 있는 서비스의 몇 가지 예는 다음과 같습니다.

- [Microsoft Azure 증명 (미리 보기)](../attestation/overview.md) 또는
- [Intel의 증명 및 프로 비전 서비스](https://software.intel.com/sgx/attestation-services)


둘 다 Azure 기밀 컴퓨팅 Intel SGX 인프라와 호환 됩니다. 

## <a name="next-steps"></a>다음 단계
[Enclave 인식 앱에 대 한 Microsoft Azure 증명 샘플](/samples/azure-samples/microsoft-azure-attestation/sample-code-for-intel-sgx-attestation-using-microsoft-azure-attestation/)을 사용해 보세요.