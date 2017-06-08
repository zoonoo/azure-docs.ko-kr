---
ms.assetid: 
title: "Azure Key Vault 보안 권역 | Microsoft Docs"
ms.service: key-vault
author: BrucePerlerMS
ms.author: bruceper
manager: mbaldwin
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 5e92b1b234e4ceea5e0dd5d09ab3203c4a86f633
ms.openlocfilehash: 6477e8d91b71361ef91763418b22596deb216f02
ms.contentlocale: ko-kr
ms.lasthandoff: 05/10/2017

---
# <a name="azure-key-vault-security-worlds-and-geographic-boundaries"></a>Azure Key Vault 보안 권역 및 지리적 경계

Azure Key Vault는 다중 테넌트 서비스이며, 각 Azure 위치의 HSM(하드웨어 보안 모듈) 풀을 사용합니다. 

동일한 지역의 Azure 위치에 있는 모든 HSM은 동일한 암호화 경계(Thales 보안 권역)를 공유합니다. 예를 들어 미국 동부 및 미국 서부는 미국 지리적 위치에 속하기 때문에 동일한 보안 권역을 공유합니다. 일본의 모든 Azure 위치는 동일한 보안 권역을 공유하고 오스트레일리아, 인도 등의 모든 Azure 위치도 마찬가지입니다. 

다음 조건이 둘 다 true이기만 하면 특정 Azure 위치의 주요 자격 증명 모음에서 생성한 키 백업을 다른 Azure 위치의 주요 자격 증명 모음으로 복원할 수 있습니다.

- 두 Azure 위치는 동일한 지리적 위치에 속합니다.
- 두 주요 자격 증명 모음이 동일한 Azure 구독에 속합니다.

예를 들어 인도 서부에 있는 주요 자격 증명 모음의 키에 대해 지정된 구독이 생성한 백업은 동일한 구독 및 지리적 위치(인도 서부, 인도 중부 또는 인도 남부)에 있는 다른 주요 자격 증명 모음으로만 복원할 수 있습니다. 



