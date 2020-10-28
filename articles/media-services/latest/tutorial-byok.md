---
title: Media Services에서 고객 관리형 키 또는 BYOK(Bring Your Own Key) 사용
description: Media Services 스토리지 계정에서 고객 관리형 키를 사용하는 방법에 대한 자습서입니다.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: tutorial
ms.date: 10/18/2020
ms.openlocfilehash: c26da9d888bbcdf72c052fa4bd7fcdf443a2d8f7
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92325891"
---
# <a name="tutorial-use-customer-managed-keys-or-bring-your-own-key-byok-with-media-services"></a>자습서: Media Services에서 고객 관리형 키 또는 BYOK(Bring Your Own Key) 사용

2020-05-01 API를 사용하면 시스템 관리 ID가 있는 Media Services 계정에서 고객 관리형 RSA 키를 사용할 수 있습니다.  이 자습서에는 Azure 서비스에 REST 요청을 보내는 Postman 컬렉션 및 환경이 포함되어 있습니다.  사용되는 서비스:

- Postman에 대한 Azure Active Directory 애플리케이션 등록
- Microsoft Graph API
- Azure Storage
- Azure Key Vault
- Media Services

이 자습서에서 Postman을 사용하여 다음을 수행하는 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Azure 서비스에서 사용할 토큰 가져오기
> * 리소스 그룹 및 스토리지 계정 만들기
> * 시스템 관리 ID를 사용하여 Media Services 계정 만들기
> * 스토리지 계정에 사용할 RSA(고객 관리형) 키를 저장하는 Key Vault 만들기
> * 스토리지 계정에 RSA 키를 사용하도록 Media Services 계정 업데이트
> * Postman에서 변수 사용

Azure 구독이 아직 없는 경우 [평가판 계정](https://azure.microsoft.com/free/)을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

- 적절한 권한으로 서비스 사용자를 등록합니다.
- [Postman](https://www.postman.com)을 설치합니다.
- [Azure 샘플: media-services-customer-managed-keys-byok](https://github.com/Azure-Samples/media-services-customer-managed-keys-byok)에서 이 자습서에 사용할 Postman 컬렉션을 다운로드합니다.

### <a name="register-a-service-principal-with-the-needed-permissions"></a>필요한 권한으로 서비스 주체를 등록합니다.

[서비스 주체 만들기](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).  서비스 주체 비밀을 얻는 방법은 [옵션 2](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#authentication-two-options)를 참조하세요.  포털에서 비밀 페이지를 벗어나면 액세스할 수 없게 되므로 어딘가에 비밀을 적어 두세요.

이 자습서의 작업을 수행하려면 서비스 주체에게 다음 권한이 필요합니다.

![서비스 주체에게 필요한 권한](./media/tutorial-byok/service-principal-permissions-1.png)

### <a name="install-postman"></a>Postman을 설치합니다.

Azure에 사용할 Postman을 아직 설치하지 않은 경우 [postman.com](https://www.postman.com/)에서 받을 수 있습니다.

### <a name="download-and-import-the-collection"></a>컬렉션 다운로드 및 가져오기

[Azure 샘플: media-services-customer-managed-keys-byok](https://github.com/Azure-Samples/media-services-customer-managed-keys-byok)에서 이 자습서에 사용할 Postman 컬렉션을 다운로드합니다.

## <a name="installation-of-collection-and-environment"></a>컬렉션 및 환경 설치

1. Postman을 실행합니다.
1. **가져오기** 를 선택합니다.
1. **파일 업로드** 를 선택합니다.
1. 컬렉션 및 환경 파일을 저장한 위치로 이동합니다.
1. 컬렉션 및 환경 파일을 선택합니다.
1. **열기** 를 선택합니다.  (파일을 API가 아닌 컬렉션으로 가져온다는 경고가 표시됩니다.  상관 없습니다.  우리가 원하는 바입니다.)
1. 이제 이 컬렉션은 [컬렉션]에 BYOK로 표시됩니다.
1. 환경 변수가 [환경]에 표시됩니다.

### <a name="understand-the-rest-api-requests-in-the-collection"></a>컬렉션의 REST API 요청 이해

컬렉션은 다음과 같은 REST API 요청을 제공합니다. 대부분의 요청은 시퀀스의 다음(또는 후속) 요청에 대한 글로벌 변수를 동적으로 만드는 테스트 스크립트를 포함하고 있으므로 제공된 순서대로 전송해야 합니다. 글로벌 변수를 수동으로 만들 필요는 없습니다.

Postman에서는 이러한 변수가 대괄호 `{{ }}` 안에 포함되어 있습니다.  예: `{{bearerToken}}`.

1. AAD 토큰 가져오기 - 이 테스트는 글로벌 변수 *bearerToken* 을 설정합니다.
2. Graph 토큰 가져오기 - 이 테스트는 글로벌 변수 *graphToken* 을 설정합니다.
3. 서비스 주체 세부 정보 가져오기 - 이 테스트는 글로벌 변수 *servicePrincipalObjectId* 를 설정합니다.
4. 스토리지 계정 만들기 - 이 테스트는 글로벌 변수 *storageAccountId* 를 설정합니다.
5. 시스템 관리 ID를 사용하여 Media Services 계정 만들기 - 이 테스트는 글로벌 변수 *principalId* 를 설정합니다.
6. Key Vault 만들기, 서비스 주체에 대한 액세스 권한 부여 - 이 테스트는 글로벌 변수 *keyVaultId* 를 설정합니다.
7. Key Vault 토큰 가져오기 - 이 테스트는 글로벌 변수 *keyVaultToken* 을 설정합니다.
8. 키 자격 증명 모음에 RSA 키 만들기 - 이 테스트는 글로벌 변수 *keyId* 를 설정합니다.
9. 스토리지 계정에서 키를 사용하도록 Media Services 계정 업데이트 - 이 요청에 대한 테스트 스크립트는 없습니다.

## <a name="define-environment-variables"></a>환경 변수 정의

1. 환경 드롭다운 목록을 선택하여 다운로드한 환경으로 전환합니다.
1. Postman에서 환경 변수를 설정합니다. 이 환경 변수는 대괄호 `{{ }}` 안에 포함되는 변수로도 사용됩니다.  예: `{{tenantId}}`.

    * *tenantId* = 테넌트 ID
    * *servicePrincipalId* = 자주 사용하는 방법(포털, CLI 등)으로 설정하는 서비스 주체의 ID
    * *servicePrincipalSecret* = 서비스 주체에 대해 만든 비밀
    * *subscription* = 구독 ID
    * *storageName* = 스토리지에 지정할 이름
    * *accountName* = 사용하려는 미디어 서비스 계정 이름
    * *keyVaultName* = 사용하려는 Key Vault 이름
    * *resourceLocation* = centralus(또는 리소스를 배치하려는 위치.  이 컬렉션은 centralus에서만 테스트되었습니다.)
    * *resourceGroup* = 리소스 그룹 이름

    다음 변수는 Azure 리소스 작업의 표준 변수이므로 변경할 필요가 없습니다.

    * *armResource* = `https://management.core.windows.net`
    * *graphResource* = `https://graph.windows.net/`
    * *keyVaultResource* = `https://vault.azure.net`
    * *armEndpoint* = `management.azure.com`
    * *graphEndpoint* = `graph.windows.net`
    * *aadEndpoint* = `login.microsoftonline.com`
    * *keyVaultDomainSuffix* = `vault.azure.net`

## <a name="send-the-requests"></a>요청 보내기

환경 변수를 정의한 후에는 위의 시퀀스에서 한 번에 하나씩 요청을 실행하거나, Postman의 실행기를 사용하여 컬렉션을 실행할 수 있습니다.

## <a name="change-the-key"></a>키 변경

키가 변경되면 Media Services에서 자동으로 탐지합니다.  이 동작을 테스트하려면 동일한 키에 대한 또 다른 키 버전을 만듭니다. Media Services가 15분 이내에 이 키를 탐지합니다.

## <a name="clean-up-resources"></a>리소스 정리

여기서 만든 리소스가 더 이상 필요 없고 **리소스 요금이 더 이상 청구되지 않도록 하려면** 리소스를 삭제합니다.

## <a name="next-steps"></a>다음 단계

아래 내용에 대한 다음 문서를 진행하세요.
> [!div class="nextstepaction"]
> [URL에 따라 원격 파일을 인코딩하고 REST를 사용하여 비디오 스트리밍](stream-files-tutorial-with-rest.md)
