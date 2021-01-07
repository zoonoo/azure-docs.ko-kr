---
title: Azure Attestation 워크플로
description: Azure Attestation의 워크플로입니다.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 09d793f3d8ed544a386a362677f24be6d18673d7
ms.sourcegitcommit: 003ac3b45abcdb05dc4406661aca067ece84389f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/07/2020
ms.locfileid: "96748735"
---
# <a name="workflow"></a>워크플로

Microsoft Azure Attestation은 enclaves에서 증거를 수신하고 Azure 보안 기준 및 구성 가능한 정책에 대한 증거를 평가합니다. 성공적으로 확인되면 Azure Attestation은 증명 토큰을 생성하여 enclave를 신뢰할 수 있는지 확인합니다.

Azure Attestation 작업 흐름과 관련된 행위자는 다음과 같습니다.

- **신뢰 당사자**: enclave 유효성을 확인하기 위해 Azure Attestation에 의존하는 구성 요소입니다. 
- **클라이언트**: enclave에서 정보를 수집하고 Azure Attestation에 요청을 보내는 구성 요소입니다. 
- **Azure Attestation**: 클라이언트에서 enclave 증명 정보를 수락하고, 유효성을 검사하고, 증명 토큰을 클라이언트에 반환하는 구성 요소입니다.


## <a name="intel-software-guard-extensions-sgx-enclave-validation-work-flow"></a>Intel® SGX(Software Guard Extensions) enclave 유효성 검사 워크플로

일반적인 SGX enclave 증명 워크플로(Azure Attestation 사용)의 일반적인 단계는 다음과 같습니다.

1. 클라이언트는 enclave에서 증거를 수집합니다. 증거는 enclave 환경 및 enclave 내에서 실행되는 클라이언트 라이브러리에 대한 정보입니다.
1. 클라이언트에는 Azure Attestation 인스턴스를 참조하는 URI가 있습니다. 클라이언트는 Azure Attestation에 증거를 보냅니다. 공급자에게 제출되는 정확한 정보는 enclave 유형에 따라 달라집니다.
1. Azure Attestation은 제출된 정보의 유효성을 검사하고 구성된 정책에 대해 평가합니다. 검증에 성공하면 Azure Attestation은 증명 토큰을 발급하여 클라이언트에 반환합니다. 이 단계가 실패하면 Azure Attestation은 클라이언트에 오류를 보고합니다. 
1. 클라이언트는 신뢰 당사자에게 증명 토큰을 보냅니다. 신뢰 당사자는 Azure Attestation의 공개 키 메타데이터 엔드포인트를 호출하여 서명 인증서를 검색합니다. 그런 다음, 신뢰 당사자는 증명 토큰의 서명을 확인하고 enclave 신뢰성을 보장합니다. 

![SGX enclave 유효성 검사 흐름](./media/sgx-validation-flow.png)

> [!Note]
> [2018-09-01-preview](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/attestation/data-plane/Microsoft.Attestation/stable/2018-09-01-preview) API 버전에서 증명 요청을 보내는 경우 클라이언트는 Azure AD 액세스 토큰과 함께 증거를 Azure Attestation에 보내야 합니다.

## <a name="next-steps"></a>다음 단계
- [증명 정책을 작성하고 서명하는 방법](author-sign-policy.md)
- [PowerShell을 사용하여 Azure Attestation 설정](quickstart-powershell.md)
