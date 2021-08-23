---
title: Azure Key Vault 보안 권역 | Microsoft Docs
description: Azure Key Vault은 다중 테넌트 서비스입니다. 각 Azure 지역에서 HSM 풀을 사용합니다. 지리적 지역의 모든 지역은 암호화 경계를 공유합니다.
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
author: msmbaldwin
ms.author: mbaldwin
ms.date: 07/03/2017
ms.openlocfilehash: 0753108df52f0dcaea1d6c79eab266eeb000889a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536716"
---
# <a name="azure-key-vault-security-worlds-and-geographic-boundaries"></a>Azure Key Vault 보안 권역 및 지리적 경계

Azure 제품은 여러 [Azure 지역](https://azure.microsoft.com/global-infrastructure/geographies/)에서 사용할 수 있으며, 각 지역에는 하나 이상의 지역이 포함됩니다. 예를 들어 유럽 지리에는 북유럽과 서유럽의 두 지역이 포함되어 있지만 브라질 지리의 유일한 지역은 브라질 남부입니다.

Azure Key Vault는 HSM(하드웨어 보안 모듈) 풀을 사용하는 다중 테넌트 서비스입니다. 한 지리 내의 모든 HSM은 "보안 권역"이라고 하는 동일한 암호화 경계를 공유합니다. 모든 지리는 단일 보안 권역에 해당하며 그 반대의 경우도 마찬가지입니다.

미국 동부 및 미국 서부는 지역(미국)에 속하기 때문에 동일한 보안 권역을 공유합니다. 일본의 모든 Azure 지역은 동일한 보안 권역을 공유하고 오스트레일리아 등의 모든 Azure 지역도 마찬가지입니다.

>[!NOTE]
> 예외로, 미국 국방부 동부 및 미국 국방부 중부에는 자체 보안 권역이 있습니다.

## <a name="backup-and-restore-behavior"></a>백업 및 복원 동작

다음 조건이 둘 다 true이기만 하면 특정 Azure 지역의 키 자격 증명 모음에서 생성한 키 백업을 다른 Azure 지역의 키 자격 증명 모음으로 복원할 수 있습니다.

- 두 Azure 지역은 동일한 지리에 속합니다.
- 두 키 자격 증명 모음이 동일한 Azure 구독에 속합니다.

예를 들어 인도 서부 키 자격 증명 모음에서 키를 사용하는 백업은 인도 지리(인도 서부, 인도 중부, 인도 남부 지역)에서 동일한 구독의 다른 키 자격 증명 모음으로 복원할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [지역별 Microsoft 제품](https://azure.microsoft.com/regions/services/)을 참조하세요.
