---
title: Azure Key Vault 키, 비밀 및 인증서 정보 - Azure Key Vault
description: 키, 비밀 및 인증서에 대한 Azure Key Vault REST 인터페이스 및 개발자 정보의 개요입니다.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: overview
ms.date: 04/17/2020
ms.author: mbaldwin
ms.openlocfilehash: 627dfee81cb10e4e442b3cefb10d786d87d5c81d
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2020
ms.locfileid: "83005885"
---
# <a name="about-keys-secrets-and-certificates"></a>키, 비밀 및 인증서 정보

Azure Key Vault는 Microsoft Azure 애플리케이션 및 사용자가 여러 가지 종류의 비밀/키 데이터를 저장하고 사용할 수 있게 도와줍니다.

- 암호화 키: 여러 키 유형과 알고리즘을 지원하며, 고부가 가치 키에 HSM(하드웨어 보안 모듈)을 사용할 수 있습니다. 자세한 내용은 [키 정보](../keys/about-keys.md)를 참조하세요.
- 비밀: 암호, 데이터베이스 연결 문자열 등의 비밀을 안전하게 스토리지합니다. 자세한 내용은 [비밀 정보](../secrets/about-secrets.md)를 참조하세요.
- Certificates: 인증서를 지원합니다. 인증서는 키와 비밀을 기반으로 하며 자동 갱신 기능을 추가합니다. 자세한 내용은 [인증서 정보](../certificates/about-certificates.md)를 참조하세요.
- Azure Storage: 관리자를 대신하여 Azure Storage 계정의 키를 관리할 수 있습니다. 내부적으로 Key Vault는 Azure Storage 계정을 사용하여 키를 나열(동기화)하고, 주기적으로 키를 다시 생성(회전)할 수 있습니다. 자세한 내용은 [Key Vault를 사용하여 스토리지 계정 키 관리](../secrets/overview-storage-keys.md)를 참조하세요.

Key Vault에 대한 일반적 내용은 [Azure Key Vault 정보](overview.md)를 참조하세요.

## <a name="data-types"></a>데이터 형식

키, 암호화 및 서명에 대한 관련 데이터 형식은 JOSE 사양을 참조하세요.  

-   **알고리즘** - 키 작업에 지원되는 알고리즘(예: RSA1_5)  
-   **암호 텍스트 값** - Base64URL을 사용하여 인코딩된 암호 텍스트 옥텟  
-   **다이제스트 값** - Base64URL을 사용하여 인코딩된 해시 알고리즘의 출력  
-   **키 유형** - 지원되는 키 유형 중 하나(예: RSA(Rivest-Shamir-Adleman))  
-   **일반 텍스트 값** - Base64URL을 사용하여 인코딩된 일반 텍스트 옥텟  
-   **서명 값** - Base64URL을 사용하여 인코딩된 서명 알고리즘의 출력  
-   **base64URL** - Base64URL[RFC4648]을 사용하여 인코딩된 이진 값  
-   **부울** - true 또는 false 중 하나  
-   **ID** - AAD(Azure Active Directory)의 ID  
-   **IntDate** - 1970-01-01T0:0:0Z UTC에서 지정된 UTC 날짜/시간까지의 초 수를 나타내는 JSON 10진수 값입니다. 일반적 날짜/시간, 특히 UTC에 대한 자세한 내용은 RFC3339를 참조하세요.  

## <a name="objects-identifiers-and-versioning"></a>개체, 식별자 및 버전 관리

Key Vault에 저장된 개체는 개체의 새 인스턴스가 만들어질 때마다 버전 관리됩니다. 각 버전에는 고유 식별자 및 URL이 할당됩니다. 개체가 처음으로 만들어지면 고유한 버전 식별자가 지정되고 개체의 현재 버전으로 표시됩니다. 개체 이름이 동일한 새 인스턴스를 만들면 새 개체에 고유한 버전 식별자가 제공되어 현재 버전이 됩니다.  

Key Vault의 개체는 버전을 지정하거나 현재 버전의 개체에서 작업할 버전을 생략하여 해결할 수 있습니다. 예를 들어 이름이 `MasterKey`인 키가 지정되는 경우 버전을 지정하지 않고 작업을 수행하면 시스템에서 사용 가능한 최신 버전을 사용할 수 있습니다. 버전별 식별자를 사용하여 작업을 수행하면 시스템에서 해당 특정 버전의 개체를 사용하게 됩니다.  

개체는 URL을 사용하여 Key Vault 내에서 고유하게 식별됩니다. 지리적 위치에 관계 없이, 시스템의 두 개체가 동일한 URL을 가질 수 없습니다. 개체의 전체 URL을 개체 식별자라고 합니다. URL은 Key Vault를 식별하는 접두사, 개체 형식, 사용자가 입력한 개체 이름 및 개체 버전으로 구성됩니다. 개체 이름은 대/소문자를 구분하지 않으며 변경할 수 없습니다. 개체 버전이 포함되지 않은 식별자를 기본 식별자라고 합니다.  

자세한 내용은 [인증, 요청 및 응답](authentication-requests-and-responses.md)을 참조하세요.

개체 식별자의 일반적인 형식은 다음과 같습니다.  

`https://{keyvault-name}.vault.azure.net/{object-type}/{object-name}/{object-version}`  

위치:  

|||  
|-|-|  
|`keyvault-name`|Microsoft Azure Key Vault 서비스의 키 자격 증명 모음에 대한 이름입니다.<br /><br /> Key Vault 이름은 사용자가 선택하며 전역적으로 고유합니다.<br /><br /> Key Vault 이름은 0~9, a~z, A~Z 및 -만 포함된 3-24자 길이의 문자열이어야 합니다.|  
|`object-type`|개체의 유형, "키", "비밀" 또는 '인증서'.|  
|`object-name`|`object-name`은 사용자가 제공한 이름이며 Key Vault 내에서 고유해야 합니다. 이름은 문자로 시작하고 0~9, a~z, A~Z 및 -만 포함된 1-127자 길이의 문자열이어야 합니다.|  
|`object-version`|`object-version`은 시스템에서 생성된 32자의 문자열 식별자이며, 필요에 따라 고유한 버전의 개체를 처리하는 데 사용됩니다.|  

## <a name="next-steps"></a>다음 단계

- [키 정보](../keys/about-keys.md)
- [비밀 정보](../secrets/about-secrets.md)
- [인증서 정보](../certificates/about-certificates.md)
- [인증, 요청 및 응답](../general/authentication-requests-and-responses.md)
- [Key Vault 개발자 가이드](../general/developers-guide.md)
