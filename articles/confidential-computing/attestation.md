---
title: Azure에서 Enclave 증명
description: 증명을 사용하여 기밀 컴퓨팅의 신뢰할 수 있는 환경이 안전한지 확인하는 방법을 알아봅니다.
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 9/22/2020
ms.author: JenCook
ms.openlocfilehash: a7b0ca65329016b0a73f612115d8caba43dfbe2a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102551354"
---
# <a name="attesting-sgx-enclaves"></a>SGX Enclave 증명

Azure의 기밀 컴퓨팅은 코드 또는 데이터의 일부를 격리할 수 있는 Intel SGX 기반 가상 머신을 제공합니다. 이러한 [Enclave](confidential-computing-enclaves.md)로 작업할 때 신뢰할 수 있는 환경의 보안을 확인하고 유효성을 검사하는 것이 좋습니다. 이 확인은 증명 프로세스입니다. 

## <a name="overview"></a>개요 

증명을 통해 신뢰 당사자는 (1) 자신의 소프트웨어가 enclave에서 실행되고 (2) enclave가 최신 상태이고 안전하다는 확신을 높일 수 있습니다. 예를 들어 enclave는 기본 하드웨어에서 enclave가 플랫폼에 존재한다는 증명을 포함하는 자격 증명을 생성하도록 이 기본 하드웨어에 요청합니다. 그런 다음, 동일한 플랫폼에서 보고서가 생성되었는지 확인하는 두 번째 enclave에 보고서를 제공할 수 있습니다.

![Enclave의 코드 증명](media/attestation/attestation.png)



증명은 시스템 소프트웨어 및 실리콘과 호환되는 보안 증명 서비스를 사용하여 구현해야 합니다. 사용할 수 있는 서비스의 몇 가지 예는 다음과 같습니다.

- [Microsoft Azure Attestation(미리 보기)](../attestation/overview.md)
- [Intel의 증명 및 프로비저닝 서비스](https://software.intel.com/sgx/attestation-services)


두 서비스 모두 Azure 기밀 컴퓨팅 Intel SGX 인프라와 호환됩니다. 

## <a name="next-steps"></a>다음 단계
[Enclave 인식 앱에 대한 Microsoft Azure Attestation 샘플](/samples/azure-samples/microsoft-azure-attestation/sample-code-for-intel-sgx-attestation-using-microsoft-azure-attestation/)을 사용해 보세요.