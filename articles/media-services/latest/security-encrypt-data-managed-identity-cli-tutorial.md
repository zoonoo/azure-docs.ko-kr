---
title: Key Vault 키를 사용하여 데이터를 Media Services 계정으로 암호화
description: Key Vault 키를 사용하여 Media Services에서 데이터를 암호화하려면 Media Services 계정에 Key Vault에 대한 ‘액세스 권한’이 부여되어야 합니다. 아래 단계에 따라 Media Services 계정에 대한 관리 ID를 만들고 Media Services CLI를 사용하여 이 ID에 Key Vault에 대한 액세스 권한을 부여합니다.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: tutorial
ms.date: 05/14/2021
ms.author: inhenkel
ms.openlocfilehash: 20b0ec0025fcac72005eb8b2343ede0ef9e5d9b4
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110464335"
---
# <a name="tutorial-use-a-key-vault-key-to-encrypt-data-into-a-media-services-account"></a>자습서: Key Vault 키를 사용하여 데이터를 Media Services 계정으로 암호화

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Key Vault 키를 사용하여 Media Services에서 데이터를 암호화하려면 Media Services 계정에 Key Vault에 대한 ‘액세스 권한’이 부여되어야 합니다. 아래 단계에 따라 Media Services 계정에 대한 관리 ID를 만들고 Media Services CLI를 사용하여 이 ID에 Key Vault에 대한 액세스 권한을 부여합니다.

:::image type="content" source="media/diagrams/managed-identities-scenario-keyvault-media-services-account.svg" alt-text="Media Services 계정은 관리 ID와 함께 Key Vault를 사용함":::



이 자습서에서는 2020-05-01 Media Services API를 사용합니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

이 문서의 명령을 사용하려면 먼저 사용하려는 구독에 로그인해야 합니다.

 [!INCLUDE [Sign in to Azure with the CLI](./includes/task-sign-in-azure-cli.md)]

### <a name="set-subscription"></a>구독 설정

이 명령을 사용하여 작업할 구독을 설정합니다.

[!INCLUDE [Sign in to Azure with the CLI](./includes/task-set-azure-subscription-cli.md)]

## <a name="resource-names"></a>리소스 이름

시작하기 전에 만들려는 리소스의 이름을 결정합니다.  하나의 집합으로 쉽게 식별할 수 있어야 합니다. 특히 테스트를 마친 후에 사용할 계획이 없다면 더 필요합니다. 명명 규칙은 다양한 리소스 유형에 따라 다르므로 모두 소문자를 사용하는 것이 가장 좋습니다. 예를 들어, 리소스 그룹 이름은 "mediatest1rg", 스토리지 계정 이름은 "mediatest1stor"를 사용합니다. 이 문서의 각 단계에 동일한 이름을 사용합니다.

아래 명령에서 이러한 이름이 참조된 것을 볼 수 있습니다.  필요한 리소스의 이름은 다음과 같습니다.

- your-resource-group-name
- your-storage-account-name
- your-media-services-account-name
- your-keyvault-name
- your-key-name
- your-region

> [!NOTE]
> 위의 하이픈은 안내 단어를 구분하는 데만 사용됩니다. Azure 서비스에서 리소스 이름 지정이 불일치하기 때문에 리소스 이름을 지정할 때는 하이픈을 사용하지 마십시오.
> 또한 지역 이름은 사용자가 만들지 않습니다.  지역 이름은 Azure에서 결정합니다.

### <a name="list-azure-regions"></a>Azure 지역 나열

사용할 실제 지역 이름을 잘 모르는 경우 다음 명령을 사용하여 목록을 표시합니다.

[!INCLUDE [List Azure regions with the CLI](./includes/task-list-azure-regions-cli.md)]

## <a name="sequence"></a>시퀀스

아래의 각 단계는 특정 순서로 수행됩니다. JSON 응답의 값 하나 이상이 시퀀스의 다음 단계에서 사용되기 때문입니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

직접 만들 리소스는 리소스 그룹에 속해야 합니다. 먼저 리소스 그룹을 만듭니다. Media Services 계정 만들기 단계 및 후속 단계에 `your-resource-group-name`을 사용합니다.

[!INCLUDE [Create a resource group with the CLI](./includes/task-create-resource-group-cli.md)]

## <a name="create-a-storage-account"></a>Storage 계정 만들기

직접 만들 Media Services 계정에는 스토리지 계정이 연결되어 있어야 합니다. 먼저 Media Services 계정에 대한 스토리지 계정을 만듭니다. 후속 단계에 `your-storage-account-name`을 사용합니다.

[!INCLUDE [Create a Storage account with the CLI](./includes/task-create-storage-account-cli.md)]

## <a name="create-a-media-services-account-with-a-service-principal-managed-identity"></a>서비스 주체(관리 ID)를 사용하여 Media Services 계정 만들기

이제 관리 ID라고도 하는 서비스 주체를 사용하여 Media Services 계정을 만듭니다.

> [!IMPORTANT]
> 명령에서 --mi 플래그를 사용하는 것을 기억해야 합니다.  그러지 않으면 이후 단계를 위한 `principalId`를 찾을 수 없습니다.

[!INCLUDE [Create a Media Services account with the CLI](./includes/task-create-media-services-account-managed-identity-cli.md)]

## <a name="create-a-key-vault"></a>주요 자격 증명 모음 만들기

Key Vault 만들기  Key Vault는 미디어 데이터를 암호화하는 데 사용됩니다. 후속 단계를 위해 `your-keyvault-name`을 사용하여 키를 만듭니다.

[!INCLUDE [Create a Media Services account with the CLI](./includes/task-create-key-vault-cli.md)]

## <a name="grant-the-media-services-system-assigned-managed-identity-access-to-the-key-vault"></a>Media Services 시스템 할당 관리 ID에 Key Vault에 대한 액세스 권한 부여

Media Services 관리 ID에 Key Vault에 대한 액세스 권한을 부여합니다. 해당 명령에는 다음 두 가지가 있습니다.

### <a name="get-show-the-managed-identity-of-the-media-services-account"></a>Media Services 계정의 관리 ID 가져오기(표시)

아래의 첫 번째 명령은 Media Services 계정의 관리 ID 즉, 명령에서 반환된 JSON에 나열된 `principalId`를 보여 줍니다.

[!INCLUDE [Show the Managed Identity of a Media Services account with the CLI](./includes/task-show-account-managed-identity-cli.md)]

### <a name="set-the-key-vault-policy"></a>Key Vault 정책 설정

두 번째 명령은 보안 주체 ID에 Key Vault에 대한 액세스 권한을 부여합니다. `object-id`를 이전 단계에서 얻은 `principalId`의 값으로 설정합니다.

[!INCLUDE [Set the Key Vault policy with the CLI](./includes/task-set-key-vault-policy-cli.md)]

### <a name="set-media-services-to-use-the-key-from-key-vault"></a>Key Vault의 키를 사용하도록 Media Services 설정

만든 키를 사용하도록 Media Services를 설정합니다. `key-identifier` 속성의 값은 키가 생성되었을 때 출력에서 제공됩니다. 이 명령은 액세스 제어 변경 내용을 전파하는 데 걸리는 시간 때문에 실패할 수 있습니다. 이 경우 몇 분 후에 다시 시도하세요.

[!INCLUDE [Set Media Services to use the key from Key Vault](./includes/task-set-encryption-cli.md)]

## <a name="validation"></a>유효성 검사

고객 관리형 키를 사용하여 계정이 암호화되었는지 확인하려면 계정 암호화 속성을 확인합니다.

[!INCLUDE [Set Media Services to use the key from Key Vault](./includes/task-show-account-encryption-cli.md)]

`type` 속성은 `CustomerKey`를 표시해야 하고, `currentKeyIdentifier`는 고객의 Key Vault에 있는 키의 경로로 설정해야 합니다.

## <a name="clean-up-resources"></a>리소스 정리

직접 만든 리소스를 사용할 계획이 없으면 리소스 그룹을 삭제합니다.

[!INCLUDE [Create a Media Services account with the CLI](./includes/clean-up-resources-cli.md)]
