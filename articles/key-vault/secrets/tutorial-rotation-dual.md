---
title: 두 개의 자격 증명 세트를 사용하는 리소스에 대한 순환 자습서
description: 이 자습서를 사용하여 두 개의 인증 자격 증명 세트를 사용하는 리소스의 비밀을 자동으로 순환하는 방법을 알아봅니다.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: rotation
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 06/22/2020
ms.author: jalichwa
ms.openlocfilehash: b9478d3b171189decb4e2cca7fc93ba2fa75e32e
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2020
ms.locfileid: "89482785"
---
# <a name="automate-the-rotation-of-a-secret-for-resources-with-two-sets-of-authentication-credentials"></a>두 개의 인증 자격 증명 세트를 사용하는 리소스의 비밀 순환 자동화

Azure 서비스를 인증하는 가장 좋은 방법은 [관리 ID](../general/authentication.md)를 사용하는 것이지만, 이 방법을 사용할 수 없는 시나리오도 있습니다. 이러한 경우 액세스 키 또는 암호가 사용됩니다. 액세스 키와 암호는 자주 순환해야 합니다.

이 자습서에서는 두 개의 인증 자격 증명 세트를 사용하는 데이터베이스 및 서비스의 비밀을 정기적으로 자동 순환하는 방법을 보여줍니다. 특히 이 자습서에서는 Azure Event Grid 알림에 의해 트리거되는 함수를 사용하여 Azure Key Vault에 비밀로 저장된 Azure Storage 계정 키를 순환합니다. :

> [!NOTE]
> 스토리지 계정 키는 스토리지 계정에 대한 위임된 액세스가 가능하도록 공유 액세스 서명 토큰을 제공하여 Key Vault에서 자동으로 관리할 수 있습니다. 액세스 키를 사용하는 스토리지 계정 연결 문자열이 필요한 서비스가 있으며, 이러한 시나리오에는 이 솔루션을 사용하는 것이 좋습니다.

![순환 솔루션 다이어그램](../media/secrets/rotation-dual/rotation-diagram.png)

위의 솔루션에서 Azure Key Vault는 스토리지 계정 개별 액세스 키를 후속 버전에서 기본 키와 보조 키 사이에서 교대로 반복되는 동일한 비밀 버전으로 저장합니다. 한 액세스 키가 최신 버전의 비밀에 저장되면 대체 키가 다시 생성되어 Key Vault에 최신 버전의 비밀로 추가됩니다. 이 솔루션은 다시 생성된 최신 키로 새로 고치는 애플리케이션 전체 순환 주기를 제공합니다. 

1. 비밀 만료 30일 전에 Key Vault는 "만료 임박" 이벤트를 Event Grid에 게시합니다.
1. Event Grid는 이벤트 구독을 확인하고 HTTP POST를 사용하여 이벤트를 구독하는 함수 앱 엔드포인트를 호출합니다.
1. 함수 앱은 대체 키(최신이 아닌)를 식별하고 스토리지 계정을 호출하여 대체 키를 다시 생성합니다.
1. 함수 앱은 다시 생성된 새 키를 비밀의 새 버전으로 Azure Key Vault에 추가합니다.

## <a name="prerequisites"></a>필수 구성 요소
* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Azure Key Vault
* Azure Storage 계정 2개

기존 키 자격 증명 모음 및 스토리지 계정이 없는 경우 아래의 배포 링크를 사용할 수 있습니다.

[!["Azure에 배포"라는 레이블이 지정된 단추를 보여주는 이미지](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2Farm-templates%2FInitial-Setup%2Fazuredeploy.json)

1. **리소스 그룹**에서 **새로 만들기**를 선택합니다. 그룹 이름을 **akvrotation**으로 지정하고 **확인**을 클릭합니다.
1. **검토+만들기**를 선택합니다.
1. **만들기**

    ![리소스 그룹 만들기](../media/secrets/rotation-dual/dual-rotation-1.png)

이제 키 자격 증명 모음과 스토리지 계정 2개가 있습니다. Azure CLI에서 다음 명령을 실행하여 이 설정을 확인할 수 있습니다.

```azurecli
az resource list -o table -g akvrotation
```

결과는 다음 출력과 비슷합니다.

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
akvrotation-kv         akvrotation      eastus      Microsoft.KeyVault/vaults
akvrotationstorage     akvrotation      eastus      Microsoft.Storage/storageAccounts
akvrotationstorage2    akvrotation      eastus      Microsoft.Storage/storageAccounts
```

## <a name="create-and-deploy-storage-account-key-rotation-function"></a>스토리지 계정 키 순환 함수 만들기 및 배포

다음으로, 시스템 관리 ID와 기타 필수 구성 요소를 사용하여 함수 앱을 만들고, 스토리지 계정 키 순환 함수를 배포합니다.

함수 앱 순환 함수에는 다음과 같은 구성 요소 및 구성이 필요합니다.
- Azure App Service 계획
- 함수 앱 트리거 관리에 필요한 스토리지 계정
- Key Vault의 비밀에 액세스하는 액세스 정책
- 스토리지 계정 키 운영자 서비스 역할을 함수 앱에 할당하여 스토리지 계정 액세스 키에 액세스
- 이벤트 트리거와 http 트리거를 사용하는 스토리지 계정 키 순환 함수(주문형 순환)
- **SecretNearExpiry** 이벤트에 대한 EventGrid 이벤트 구독

1. Azure 템플릿 배포 링크를 선택합니다. 

   [!["Azure에 배포"라는 레이블이 지정된 단추를 보여주는 이미지](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2Farm-templates%2FInitial-Setup%2Fazuredeploy.json)

1. **리소스 그룹** 목록에서 **akvrotation**을 선택합니다.
1. 순환할 액세스 키가 포함된 스토리지 계정 이름을 **스토리지 계정 이름**에 입력합니다.
1. **Key Vault 이름**에 키 자격 증명 모음 이름을 입력합니다.
1. **함수 앱 이름**에 함수 앱 이름을 입력합니다.
1. **비밀 이름**에 액세스 키를 저장할 비밀 이름을 입력합니다.
1. **리포지토리 Url**에 함수 코드 GitHub 위치( **https://github.com/jlichwa/KeyVault-Rotation-StorageAccountKey-PowerShell.git** )를 입력합니다.
1. **검토+만들기**를 선택합니다.
1. **만들기**

   ![첫 번째 스토리지 계정 검토 및 만들기](../media/secrets/rotation-dual/dual-rotation-2.png)

위의 단계를 마치면 스토리지 계정, 서버 팜, 함수 앱, 애플리케이션 인사이트가 생깁니다. 배포가 완료되면 아래 화면이 표시됩니다. ![배포 완료](../media/secrets/rotation-dual/dual-rotation-3.png)
> [!NOTE]
> 오류가 발생하는 경우 **재배포**를 클릭하여 나머지 구성 요소를 배포할 수 있습니다.


배포 템플릿 및 순환 함수 코드는 [GitHub](https://github.com/jlichwa/KeyVault-Rotation-StorageAccountKey-PowerShell)에서 찾을 수 있습니다.

## <a name="add-storage-account-access-key-to-key-vault"></a>Key Vault에 스토리지 계정 액세스 키 추가

먼저 사용자에게 *비밀 관리* 권한을 부여하도록 액세스 정책을 설정합니다.

```azurecli
az keyvault set-policy --upn <email-address-of-user> --name akvrotation-kv --secret-permissions set delete get list
```

이제 스토리지 계정 액세스 키를 값으로 사용하여 새 비밀을 만들 수 있습니다. 스토리지 계정 리소스 ID, 비밀 유효 기간, 비밀에 추가할 키 ID도 필요하므로, 순환 함수가 스토리지 계정에서 키를 다시 생성할 수 있습니다.

스토리지 계정 리소스 ID를 검색합니다. 값은 `id` 속성에서 찾을 수 있습니다.
```azurecli
az storage account show -n akvrotationstorage
```

키 값을 검색하는 스토리지 계정 액세스 키를 나열합니다.

```azurecli
az storage account keys list -n akvrotationstorage 
```

**key1Value** 및 **storageAccountResourceId**에 대해 검색된 값을 채웁니다.

```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddThh:mm:ssZ")
az keyvault secret set --name storageKey --vault-name akvrotation-kv --value <key1Value> --tags "CredentialId=key1" "ProviderAddress=<storageAccountResourceId>" "ValidityPeriodDays=60" --expires $tomorrowDate
```

만료 날짜가 짧은 비밀을 만들면 `SecretNearExpiry` 이벤트가 몇 분 내에 게시되고, 이어서 비밀을 순환하는 함수가 트리거됩니다.

스토리지 계정 키와 Key Vault 비밀을 검색하고 비교하여 액세스 키가 다시 생성되었는지 확인할 수 있습니다.

아래 명령을 사용하여 비밀 정보를 표시할 수 있습니다.
```azurecli
az keyvault secret show --vault-name akvrotation-kv --name storageKey
```
`CredentialId`는 대체 `keyName`로 업데이트되고 `value`는 ![첫 번째 스토리지계정에 대한 az keyvault secret show의 출력](../media/secrets/rotation-dual/dual-rotation-4.png)을 다시 생성됩니다.

값의 유효성을 검사하기 위한 액세스 키를 검색합니다.
```azurecli
az storage account keys list -n akvrotationstorage 
```
![첫 번째 스토리지 계정에 대한 az 스토리지 계정 키 목록 출력](../media/secrets/rotation-dual/dual-rotation-5.png)

## <a name="add-additional-storage-accounts-for-rotation"></a>순환할 추가 스토리지 계정 추가

동일한 함수 앱을 재사용하여 여러 스토리지 계정을 순환할 수 있습니다. 

순환할 스토리지 계정 키를 기존 함수에 추가하려면 다음이 필요합니다.
- 스토리지 계정 키 운영자 서비스 역할을 함수 앱에 할당하여 스토리지 계정 액세스 키에 액세스
- **SecretNearExpiry** 이벤트에 대한 EventGrid 이벤트 구독

1. Azure 템플릿 배포 링크를 선택합니다. 

   [!["Azure에 배포"라는 레이블이 지정된 단추를 보여주는 이미지](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2Farm-templates%2FAdd-Event-Subscription%2Fazuredeploy.json)

1. **리소스 그룹** 목록에서 **akvrotation**을 선택합니다.
1. 순환할 액세스 키가 포함된 스토리지 계정 이름을 **스토리지 계정 이름**에 입력합니다.
1. **Key Vault 이름**에 키 자격 증명 모음 이름을 입력합니다.
1. **함수 앱 이름**에 함수 앱 이름을 입력합니다.
1. **비밀 이름**에 액세스 키를 저장할 비밀 이름을 입력합니다.
1. **검토+만들기**를 선택합니다.
1. **만들기**

   ![두 번째 스토리지 계정 검토 및 만들기](../media/secrets/rotation-dual/dual-rotation-7.png)

### <a name="add-another-storage-account-access-key-to-key-vault"></a>Key Vault에 또 다른 스토리지 계정 액세스 키 추가

스토리지 계정 리소스 ID를 검색합니다. 값은 `id` 속성에서 찾을 수 있습니다.
```azurecli
az storage account show -n akvrotationstorage2
```

key2 값을 검색하는 스토리지 계정 액세스 키를 나열합니다.

```azurecli
az storage account keys list -n akvrotationstorage2 
```

**key2Value** 및 **storageAccountResourceId**에 대해 검색된 값을 채웁니다.

```azurecli
tomorrowDate=`date -d tomorrow -Iseconds -u | awk -F'+' '{print $1"Z"}'`
az keyvault secret set --name storageKey2 --vault-name akvrotation-kv --value <key2Value> --tags "CredentialId=key2" "ProviderAddress=<storageAccountResourceId>" "ValidityPeriodDays=60" --expires $tomorrowDate
```

아래 명령을 사용하여 비밀 정보를 표시합니다.
```azurecli
az keyvault secret show --vault-name akvrotation-kv --name storageKey2
```
`CredentialId`는 대체 `keyName`로 업데이트되고 `value`는 ![두 번째 스토리지계정에 대한 az keyvault secret show의 출력](../media/secrets/rotation-dual/dual-rotation-8.png)을 다시 생성됩니다.

값의 유효성을 검사하기 위한 액세스 키를 검색합니다.
```azurecli
az storage account keys list -n akvrotationstorage 
```
![두 번째 스토리지 계정에 대한 az 스토리지 계정 키 목록 출력](../media/secrets/rotation-dual/dual-rotation-9.png)

## <a name="available-key-vault-dual-credential-rotation-functions"></a>사용 가능한 Key Vault 이중 자격 증명 순환 함수

- [Storage 계정](https://github.com/jlichwa/KeyVault-Rotation-StorageAccountKey-PowerShell)
- [Redis Cache](https://github.com/jlichwa/KeyVault-Rotation-RedisCacheKey-PowerShell)

## <a name="learn-more"></a>자세한 정보
- 개요: [Azure Event Grid를 사용하여 Key Vault 모니터링(미리 보기)](../general/event-grid-overview.md)
- 방법: [Azure Portal에서 첫 번째 함수 만들기](../../azure-functions/functions-create-first-azure-function.md)
- 방법: [키 자격 증명 모음 비밀 변경 시 이메일 받기](../general/event-grid-logicapps.md)
- [Azure Key Vault에 대한 Azure Event Grid 이벤트 스키마(미리 보기)](../../event-grid/event-schema-key-vault.md)
