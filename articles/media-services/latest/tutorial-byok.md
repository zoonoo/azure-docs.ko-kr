---
title: 고객 관리형 키 또는 BYOK 사용
description: 이 자습서에서는 Azure Media Services 스토리지 계정을 통해 고객 관리형 키 또는 BYOK(Bring Your Own Key)를 사용합니다.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: tutorial
ms.date: 10/18/2020
ms.openlocfilehash: 010e5b8fc394448840f8ff8fd11e92ca2e1ec9d4
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92740474"
---
# <a name="tutorial-use-customer-managed-keys-or-byok-with-media-services"></a>자습서: Media Services에서 고객 관리형 키 또는 BYOK 사용

2020-05-01 API를 사용하면 시스템 관리 ID가 있는 Azure Media Services 계정에서 고객 관리형 RSA 키를 사용할 수 있습니다. 이 자습서에는 Azure 서비스에 REST 요청을 보내는 Postman 컬렉션 및 환경이 포함되어 있습니다. 사용되는 서비스는 다음과 같습니다.

- Postman에 대한 Azure AD(Azure Active Directory) 애플리케이션 등록
- Microsoft Graph API
- Azure Storage
- Azure Key Vault
- Azure Media Services

이 자습서에서 Postman을 사용하여 다음을 수행하는 방법에 대해 알아봅니다.

> [!div class="checklist"]
> - Azure 서비스에서 사용할 토큰 가져오기
> - 리소스 그룹 및 스토리지 계정 만들기
> - 시스템 관리 ID를 사용하여 Media Services 계정 만들기
> - 고객 관리형 RSA 키를 저장하기 위한 키 자격 증명 모음 만들기
> - 스토리지 계정에 RSA 키를 사용하도록 Media Services 계정 업데이트
> - Postman에서 변수 사용

Azure 구독이 아직 없는 경우 [평가판 계정](https://azure.microsoft.com/free/)을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

1. 적절한 권한으로 서비스 사용자를 등록합니다.
1. [Postman](https://www.postman.com)을 설치합니다.
1. [Azure 샘플: media-services-customer-managed-keys-byok](https://github.com/Azure-Samples/media-services-customer-managed-keys-byok)에서 이 자습서에 사용할 Postman 컬렉션을 다운로드합니다.

### <a name="register-a-service-principal-with-the-needed-permissions"></a>필요한 권한으로 서비스 주체를 등록합니다.

1. [서비스 주체 만들기](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).
1. [옵션 2: 새 애플리케이션 비밀 만들기](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#authentication-two-options)로 이동하여 서비스 주체 비밀을 가져옵니다.

   > [!IMPORTANT]
   >나중에 사용하기 위해 비밀을 복사하여 저장합니다. 포털의 비밀 페이지에서 나가면 비밀에 액세스할 수 없습니다.

1. 다음 스크린샷과 같이 권한을 서비스 주체에 할당합니다.

   :::image type="complex" source="./media/tutorial-byok/service-principal-permissions-1.png" alt-text="서비스 주체에 필요한 권한을 보여 주는 스크린샷.":::
   권한이 서비스, 권한 이름, 유형 및 설명별로 나열되어 있습니다. Azure Key Vault: Azure Key Vault에 대한 사용자 가장, 위임, 전체 액세스 Azure Service Management: 사용자 가장, 위임, 조직 사용자로 Azure Service Management 액세스 Azure Storage: 사용자 가장, 위임, Azure Storage 액세스 Media Services: 사용자 가장, 위임, Media Services 액세스 Microsoft Graph: user.read, 위임, 로그인 및 사용자 프로필 읽기
   :::image-end:::

### <a name="install-postman"></a>Postman을 설치합니다.

Azure에 사용할 Postman을 아직 설치하지 않은 경우 [postman.com](https://www.postman.com/)에서 받을 수 있습니다.

### <a name="download-and-import-the-collection"></a>컬렉션 다운로드 및 가져오기

[Azure 샘플: media-services-customer-managed-keys-byok](https://github.com/Azure-Samples/media-services-customer-managed-keys-byok)에서 이 자습서에 사용할 Postman 컬렉션을 다운로드합니다.

## <a name="install-the-postman-collection-and-environment"></a>Postman 컬렉션 및 환경 설치

1. Postman을 실행합니다.
1. **가져오기** 를 선택합니다.
1. **파일 업로드** 를 선택합니다.
1. 컬렉션 및 환경 파일을 저장한 위치로 이동합니다.
1. 컬렉션 및 환경 파일을 선택합니다.
1. **열기** 를 선택합니다. 파일을 API가 아니라 컬렉션으로 가져온다는 경고가 표시됩니다. 이 경고는 문제가 없습니다. 원하는 것입니다.

이제 컬렉션이 BYOK로 컬렉션에 표시됩니다. 또한 환경 변수가 환경에 표시됩니다.

### <a name="understand-the-rest-api-requests-in-the-collection"></a>컬렉션의 REST API 요청 이해

컬렉션은 다음과 같은 REST API 요청을 제공합니다.

> [!NOTE]
>
>- 요청은 제공된 시퀀스로 보내야 합니다.
>- 대부분의 요청에는 시퀀스의 다음 요청에 대한 전역 변수를 동적으로 만드는 테스트 스크립트가 있습니다.
>- 전역 변수를 수동으로 만들 필요는 없습니다.

Postman에서 이러한 변수는 대괄호 안에 포함됩니다. 예: `{{bearerToken}}`.

1. Azure AD 토큰 가져오기: 테스트에서 **bearerToken** 전역 변수를 설정합니다.
2. Graph 토큰 가져오기: 테스트에서 **graphToken** 전역 변수를 설정합니다.
3. 서비스 주체 세부 정보 가져오기: 테스트에서 **servicePrincipalObjectId** 전역 변수를 설정합니다.
4. 스토리지 계정 만들기: 테스트에서 **storageAccountId** 전역 변수를 설정합니다.
5. 시스템 관리 ID를 사용하여 Media Services 계정 만들기: 테스트에서 **principalId** 전역 변수를 설정합니다.
6. 서비스 주체에 대한 액세스 권한을 부여하는 키 자격 증명 모음 만들기: 테스트에서 **keyVaultId** 전역 변수를 설정합니다.
7. Key Vault 토큰 가져오기: 테스트에서 **keyVaultToken** 전역 변수를 설정합니다.
8. 키 자격 증명 모음에서 RSA 키 만들기: 테스트에서 **keyId** 전역 변수를 설정합니다.
9. 스토리지 계정에서 키를 사용하도록 Media Services 계정 업데이트: 이 요청에 대한 테스트 스크립트가 없습니다.

## <a name="define-environment-variables"></a>환경 변수 정의

1. 환경의 드롭다운 목록을 선택하여 다운로드한 환경으로 전환합니다.
1. Postman에서 환경 변수를 설정합니다. 이는 대괄호 안에 포함된 변수로도 사용됩니다. 예: `{{tenantId}}`.

    - **tenantId** : 테넌트 ID
    - **servicePrincipalId** : 포털 또는 CLI와 같이 즐겨찾는 방법을 사용하여 설정한 서비스 주체의 ID
    - **servicePrincipalSecret** : 서비스 주체에 대해 만든 비밀
    - **구독** : 구독 ID.
    - **storageName** : 스토리지에 지정하려는 이름
    - **accountName** : 사용하려는 Media Services 계정 이름
    - **keyVaultName** : 사용하려는 Key Vault 이름
    - **resourceLocation** : **CentralUs** 위치 또는 리소스를 배치하려는 위치. 이 컬렉션은 **CentralUs** 에서만 테스트되었습니다.
    - **resourceGroup** : 리소스 그룹 이름입니다.

    다음 변수는 Azure 리소스 작업에 대한 표준입니다. 따라서 변경할 필요가 없습니다.

    - **armResource** : `https://management.core.windows.net`
    - **graphResource** : `https://graph.windows.net/`
    - **keyVaultResource** : `https://vault.azure.net`
    - **armEndpoint** : `management.azure.com`
    - **graphEndpoint** : `graph.windows.net`
    - **aadEndpoint** : `login.microsoftonline.com`
    - **keyVaultDomainSuffix** : `vault.azure.net`

## <a name="send-the-requests"></a>요청 보내기

환경 변수가 정의되면 이전 시퀀스에서 요청을 한 번에 하나씩 실행할 수 있습니다. 또는 Postman의 실행기를 사용하여 컬렉션을 실행할 수 있습니다.

## <a name="change-the-key"></a>키 변경

키가 변경되면 Media Services에서 자동으로 감지합니다. 동일한 키에 대한 다른 키 버전을 만들어 이 프로세스를 테스트합니다. Media Services에서 이 키를 15분 이내에 검색합니다.

## <a name="clean-up-resources"></a>리소스 정리

여기서 만든 리소스가 더 이상 필요 없고 *리소스 요금이 더 이상 청구되지 않도록 하려면* 리소스를 삭제합니다.

## <a name="next-steps"></a>다음 단계

방법을 알아보려면 다음 문서로 이동하세요.
> [!div class="nextstepaction"]
> [URL에 따라 원격 파일을 인코딩하고 REST를 사용하여 비디오 스트리밍](stream-files-tutorial-with-rest.md)
