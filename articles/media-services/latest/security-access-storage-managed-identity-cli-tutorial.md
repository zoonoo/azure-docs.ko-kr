---
title: Media Services 관리 ID로 스토리지에 액세스
description: 스토리지 계정이 알 수 없는 IP 주소의 요청을 차단하도록 구성된 경우 스토리지 계정에 액세스하려면 Media Services 계정에 스토리지 계정에 대한 액세스 권한이 부여되어야 합니다. 아래 단계에 따라 Media Services 계정에 대한 관리 ID를 만들고 Media Services CLI를 사용하여 이 ID에 스토리지에 대한 액세스 권한을 부여합니다.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: tutorial
ms.date: 05/17/2021
ms.author: inhenkel
ms.openlocfilehash: 77eb737a48213701e424bbc8b1a49ead0c95e88c
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110464299"
---
# <a name="tutorial-access-storage-with-a-media-services-managed-identity"></a>자습서: Media Services 관리 ID로 스토리지에 액세스

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

스토리지 계정이 알 수 없는 IP 주소의 요청을 차단하도록 구성된 경우 스토리지 계정에 액세스하려면 Media Services 계정에 스토리지 계정에 대한 액세스 권한이 부여되어야 합니다. 아래 단계에 따라 Media Services 계정에 대한 관리 ID를 만들고 Media Services CLI를 사용하여 이 ID에 스토리지에 대한 액세스 권한을 부여합니다.

:::image type="content" source="media/diagrams/managed-identities-scenario-storage-permissions-media-services-account.svg" alt-text="Media Services 계정은 관리 ID를 사용하여 스토리지에 액세스합니다.":::

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
> 명령에서 --mi 플래그를 사용하는 것을 기억해야 합니다.  그렇지 않으면 이후 단계를 위한 `principalId`를 찾을 수 없습니다.

[!INCLUDE [Create a Media Services account with the CLI](./includes/task-create-media-services-account-managed-identity-cli.md)]

## <a name="grant-the-media-services-managed-identity-access-to-the-storage-account"></a>스토리지 계정에 Media Services 관리 ID 액세스 권한 부여

스토리지 계정에 Media Services 관리 ID 액세스 권한을 부여합니다. 세 가지 명령이 있습니다.

### <a name="get-show-the-managed-identity-of-the-media-services-account"></a>Media Services 계정의 관리 ID 가져오기(표시)

아래의 첫 번째 명령은 Media Services 계정의 관리 ID 즉, 명령에서 반환된 JSON에 나열된 `principalId`를 보여줍니다.

[!INCLUDE [Show the Managed Identity of a Media Services account with the CLI](./includes/task-show-account-managed-identity-cli.md)]

### <a name="create-the-storage-blob-contributor-role-assignment"></a>Storage Blob 기여자 역할 할당 만들기

[!INCLUDE [Create the Storage Blob Contributor role assignment](./includes/task-create-storage-blob-contributor-role-cli.md)]

### <a name="create-the-reader-role-assignment"></a>reader 역할 할당 만들기

[!INCLUDE [Create the Reader role assignment](./includes/task-create-reader-role-cli.md)]

## <a name="use-the-managed-identity-to-access-the-storage-account"></a>관리 ID를 사용하여 스토리지 계정에 액세스

[!INCLUDE [Use the Managed Identity to access the Storage account](./includes/task-set-storage-managed-identity-cli.md)]

## <a name="validation"></a>유효성 검사

고객 관리형 키를 사용하여 계정이 암호화되었는지 확인하려면 계정 암호화 속성을 확인합니다.

[!INCLUDE [Set Media Services to use the key from Key Vault](./includes/task-show-account-managed-identity-cli.md)]

`storageAuthentication` 속성에 "ManagedIdentity"가 표시되어야 합니다.

추가 유효성 검사를 위해 Azure Storage 로그를 확인하여 각 요청에 사용되는 인증 방법을 확인할 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

직접 만든 리소스를 사용할 계획이 없으면 리소스 그룹을 삭제합니다.

[!INCLUDE [Create a Media Services account with the CLI](./includes/clean-up-resources-cli.md)]
