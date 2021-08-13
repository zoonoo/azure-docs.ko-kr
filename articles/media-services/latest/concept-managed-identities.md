---
title: 관리 ID
description: Media Services는 Azure 관리 ID와 함께 사용할 수 있습니다.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.date: 05/17/2021
ms.author: inhenkel
ms.openlocfilehash: 02f832c7fc25003950d6a112d951c07d1332e08a
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110454371"
---
# <a name="managed-identities"></a>관리 ID

개발자는 일반적으로 서로 다른 서비스 간의 통신을 보호하기 위해 비밀과 자격 증명을 관리하는 데 어려움을 겪고 있습니다. Azure에서 관리 ID를 사용하면 개발자가 Azure AD에서 Azure 리소스에 대한 ID를 제공하고 이를 사용하여 Azure AD(Azure Active Directory) 토큰을 가져오는 방식으로 자격 증명을 관리할 필요가 없습니다.

## <a name="media-services-managed-identity-scenarios"></a>Media Services 관리 ID 시나리오

관리 ID를 Media Services와 함께 사용할 수 있는 세 가지 시나리오가 있습니다.

- [고객 관리형 키를 사용하도록 Key Vault에 대한 Media Services 계정 액세스 권한 부여](security-encrypt-data-managed-identity-cli-tutorial.md)
- [Media Services가 Azure Storage 네트워크 ACL을 바이패스할 수 있도록 스토리지 계정에 대한 Media Services 계정 액세스 권한 부여](security-access-storage-managed-identity-cli-tutorial.md)
- 다른 서비스(예: VM 또는 [Azure Functions](security-function-app-managed-identity-cli-tutorial.md))가 Media Services에 액세스하도록 허용

처음 두 시나리오에서 관리 ID는 다른 서비스에 대한 *Media Services 계정* 액세스 권한을 부여하는 데 사용됩니다.  세 번째 시나리오에서 *서비스* 에는 Media Services에 액세스하는 데 사용되는 관리 ID가 있습니다.

:::image type="content" source="media/diagrams/managed-identities-scenario-comparison.svg" alt-text="관리 ID 시나리오 비교":::

> [!NOTE]
> 이러한 시나리오를 결합할 수 있습니다. Media Services 계정(예: 고객 관리형 키에 액세스) 및 Media Services 계정에 액세스할 Azure Functions 리소스 둘 다에 대한 관리 ID를 만들 수 있습니다.

## <a name="tutorials-and-how-tos"></a>자습서 및 방법

Media Services에서 관리 ID를 사용하는 실습 경험을 얻으려면 이 자습서를 사용해 보세요.

- [CLI: Key Vault의 키를 사용하여 데이터를 Media Services 계정으로 암호화](security-encrypt-data-managed-identity-cli-tutorial.md)
- [CLI: Media Services가 알 수 없는 IP 주소의 요청을 차단하도록 구성된 스토리지 계정에 액세스하도록 허용](security-access-storage-managed-identity-cli-tutorial.md)
- [CLI: Media Services 계정에 함수 앱 액세스 권한 부여](security-function-app-managed-identity-cli-tutorial.md)
- [포털: Azure Portal을 사용하여 Media Services에서 고객 관리형 키 또는 BYOK 사용](security-customer-managed-keys-portal-tutorial.md)
- [POSTMAN/REST: Media Services REST API에서 고객 관리형 키 또는 BYOK를 사용](security-customer-managed-keys-rest-postman-tutorial.md)

## <a name="further-reading"></a>추가 정보

관리 ID가 사용자 및 Azure 애플리케이션에 대해 수행할 수 있는 작업에 대한 자세한 내용은 [Azure AD 관리 ID](../../active-directory/managed-identities-azure-resources/overview.md)를 참조하세요.

Azure Functions에 대해 자세히 알아보려면 [Azure 함수 정보](../../azure-functions/functions-overview.md)를 참조하세요.
