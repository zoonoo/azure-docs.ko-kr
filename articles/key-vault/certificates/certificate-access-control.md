---
title: Azure Key Vault 인증서 액세스 제어 정보
description: Azure Key Vault 인증서 액세스 제어 개요
services: key-vault
author: sebansal
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: overview
ms.date: 10/12/2020
ms.author: sebansal
ms.openlocfilehash: 1308debb34d724f93526b776f19e0cbf1914d945
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92128577"
---
# <a name="certificate-access-control"></a>인증서 액세스 제어

 인증서에 대한 액세스 제어는 Key Vault를 통해 관리되고, 해당 인증서를 포함하는 Key Vault를 통해 제공됩니다. 인증서에 대한 액세스 제어 정책은 동일한 Key Vault의 키 및 비밀에 대한 액세스 제어 정책과 다릅니다. 사용자는 인증서를 보관할 하나 이상의 자격 증명 모음을 만들어서 시나리오를 적절하게 세분화하고 인증서를 관리할 수 있습니다.  

 키 자격 증명 모음의 비밀 액세스 제어 항목에서 보안 주체별로 사용할 수 있고 비밀 개체에 허용되는 작업과 매우 비슷한 권한은 다음과 같습니다.  

- 인증서 관리 작업에 필요한 권한
  - **get** : 현재 인증서 버전 또는 모든 인증서 버전 가져오기
  - **list** : 현재 인증서 또는 인증서 버전 나열  
  - **update** : 인증서 업데이트
  - **create** : Key Vault 인증서 만들기
  - **import** : 인증서 자료를 Key Vault 인증서로 가져오기
  - **delete** : 인증서, 해당 정책 및 모든 해당 버전 삭제  
  - **recover** : 삭제된 인증서 복구
  - **backup** : 인증서를 키 자격 증명 모음에 백업
  - **restore** : 키 자격 증명 모음에 백업된 인증서 복원
  - **managecontacts** : Key Vault 인증서 연락처 관리  
  - **manageissuers** : Key Vault 인증서 기관/발급자 관리
  - **getissuers** : 인증서 기관/발급자 가져오기
  - **listissuers** : 인증서 기관/발급자 나열  
  - **setissuers** : Key Vault 인증서의 기관/발급자 만들기 또는 업데이트  
  - **deleteissuers** : Key Vault 인증서의 기관/발급자 삭제  
 
- 권한 있는 작업에 필요한 권한
  - **purge** : 삭제된 인증서 제거(영구적으로 삭제)

자세한 내용은 [Key Vault REST API 참조에서 인증서 작업](/rest/api/keyvault)을 참조하세요. 권한 설정에 대한 내용은 [자격 증명 모음 - 액세스 정책 업데이트](/rest/api/keyvault/vaults/updateaccesspolicy)를 참조하세요.

## <a name="troubleshoot"></a>문제 해결
액세스 정책 누락으로 인해 오류가 발생할 수 있습니다. 예: ```Error type : Access denied or user is unauthorized to create certificate``` 이 오류를 해결하려면 인증서/만들기 권한을 추가해야 합니다.

## <a name="next-steps"></a>다음 단계

- [Key Vault 정보](../general/overview.md)
- [키, 비밀 및 인증서에 대한 정보](../general/about-keys-secrets-certificates.md)
- [인증, 요청 및 응답](../general/authentication-requests-and-responses.md)
- [Key Vault 개발자 가이드](../general/developers-guide.md)
