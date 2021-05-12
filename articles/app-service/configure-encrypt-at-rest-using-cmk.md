---
title: 미사용 애플리케이션 원본의 암호화
description: Azure Storage에서 애플리케이션 데이터를 암호화하고, 이를 패키지 파일로 배포하는 방법에 대해 알아봅니다.
ms.topic: article
ms.date: 03/06/2020
ms.openlocfilehash: a26660723fbe96a9b765401af1f0c9cfc80dbc3f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779437"
---
# <a name="encryption-at-rest-using-customer-managed-keys"></a>고객 관리형 키를 사용하는 미사용 데이터의 암호화

웹앱의 미사용 애플리케이션 데이터를 암호화하려면 Azure Storage 계정과 Azure Key Vault가 필요합니다. 이러한 서비스는 배포 패키지에서 앱을 실행할 때 사용됩니다.

  - [Azure Storage는 미사용 암호화를 제공합니다](../storage/common/storage-service-encryption.md). 시스템 제공 키 또는 자신의 고객관리 키를 사용할 수 있습니다. Azure의 웹앱에서 실행되고 있지 않을 때에는 애플리케이션 데이터가 여기에 저장됩니다.
  - [배포 패키지에서의 실행](deploy-run-package.md)은 App Service의 배포 기능입니다. 이 기능을 통해 SAS(공유 액세스 서명) URL을 사용하여 Azure Storage 계정에서 사이트 콘텐츠를 배포할 수 있습니다.
  - [Key Vault 참조](app-service-key-vault-references.md)는 App Service의 보안 기능입니다. 이 기능을 통해 애플리케이션 설정으로 런타임에 비밀을 가져올 수 있습니다. 이러한 방법으로 Azure Storage 계정의 SAS URL을 암호화합니다.

## <a name="set-up-encryption-at-rest"></a>미사용 암호화 설정

### <a name="create-an-azure-storage-account"></a>Azure Storage 계정 만들기

먼저 [Azure Storage 계정을 만들고,](../storage/common/storage-account-create.md) [이를 고객관리 키로 암호화합니다](../storage/common/customer-managed-keys-overview.md). 스토리지 계정이 생성되면 [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md)를 사용하여 패키지 파일을 업로드할 수 있습니다.

그런 다음 Storage Explorer를 사용하여 [SAS를 생성](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#generate-a-sas-in-storage-explorer)합니다. 

> [!NOTE]
> 이 SAS URL을 저장합니다. 이는 나중에 런타임에 배포 패키지에 대한 보안 액세스를 사용하도록 설정하는 데 사용됩니다.

### <a name="configure-running-from-a-package-from-your-storage-account"></a>스토리지 계정의 패키지에서 실행 구성하기
  
Blob Storage에 파일을 업로드하고 파일에 대한 SAS URL이 있는 경우, `WEBSITE_RUN_FROM_PACKAGE` 애플리케이션 설정을 SAS URL로 설정합니다. 다음 예시에서는 Azure CLI를 사용하여 해당 작업을 수행합니다.

```
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_RUN_FROM_PACKAGE="<your-SAS-URL>"
```

이 애플리케이션 설정을 추가하면 웹앱이 다시 시작됩니다. 앱이 다시 시작되면 해당 앱으로 이동해서 배포 패키지를 사용하여 앱이 제대로 시작되었는지 확인합니다. 애플리케이션이 제대로 시작되지 않으면 [패키지에서 실행 문제 해결 가이드](deploy-run-package.md#troubleshooting)를 참조하십시오.

### <a name="encrypt-the-application-setting-using-key-vault-references"></a>Key Vault 참조를 사용하여 애플리케이션 설정 암호화하기

이제 `WEBSITE_RUN_FROM_PACKAGE` 애플리케이션 설정 값을 SAS로 인코딩된 URL에 대한 Key Vault 참조로 바꿀 수 있습니다. 그러면 Key Vault에 암호화된 SAS URL이 유지되어, 추가 보안 계층을 제공합니다.

1. 다음 [`az keyvault create`](/cli/azure/keyvault#az_keyvault_create) 명령을 사용하여 Key Vault 인스턴스를 만듭니다.       

    ```azurecli    
    az keyvault create --name "Contoso-Vault" --resource-group <group-name> --location eastus    
    ```    

1. [다음 지침에 따라 키 자격 증명 모음에 대한 앱 액세스 권한을 부여](app-service-key-vault-references.md#granting-your-app-access-to-key-vault)하십시오.

1. 다음 [`az keyvault secret set`](/cli/azure/keyvault/secret#az_keyvault_secret_set) 명령을 사용하여 외부 URL을 키 자격 증명 모음에 암호로 추가합니다.   

    ```azurecli    
    az keyvault secret set --vault-name "Contoso-Vault" --name "external-url" --value "<SAS-URL>"    
    ```    

1.  다음 [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) 명령을 사용하여 외부 URL에 대한 Key Vault 참조로서의 값을 가지도록 `WEBSITE_RUN_FROM_PACKAGE` 애플리케이션 설정을 만듭니다.

    ```azurecli    
    az webapp config appsettings set --settings WEBSITE_RUN_FROM_PACKAGE="@Microsoft.KeyVault(SecretUri=https://Contoso-Vault.vault.azure.net/secrets/external-url/<secret-version>"    
    ```

    `<secret-version>`은 이전 `az keyvault secret set` 명령의 출력에 있습니다.

이 애플리케이션 설정을 업데이트하면 웹앱이 다시 시작됩니다. 앱이 다시 시작되면 해당 앱으로 이동해서 Key Vault 참조를 사용하여 앱이 제대로 시작되었는지 확인합니다.

## <a name="how-to-rotate-the-access-token"></a>액세스 토큰을 회전하는 방법

스토리지 계정의 SAS 키를 정기적으로 회전하는 것이 좋습니다. 웹앱이 의도치 않게 액세스 권한을 잃어버리지 않도록 하려면 Key Vault에서 SAS URL도 업데이트해야 합니다.

1. 이렇게 하려면 Azure Portal에서 스토리지 계정으로 이동하여 SAS 키를 회전하십시오. **설정** > **액세스 키** 에서 아이콘을 클릭하여 SAS 키를 회전합니다.

1. 새로운 SAS URL을 복사하고, 다음 명령을 사용하여 주요 자격 증명 모음에 업데이트된 SAS URL을 설정합니다.

    ```azurecli    
    az keyvault secret set --vault-name "Contoso-Vault" --name "external-url" --value "<SAS-URL>"    
    ``` 

1. 애플리케이션 설정의 키 자격 증명 모음 참조를 새로운 비밀 버전으로 업데이트합니다.

    ```azurecli    
    az webapp config appsettings set --settings WEBSITE_RUN_FROM_PACKAGE="@Microsoft.KeyVault(SecretUri=https://Contoso-Vault.vault.azure.net/secrets/external-url/<secret-version>"    
    ```

    `<secret-version>`은 이전 `az keyvault secret set` 명령의 출력에 있습니다.

## <a name="how-to-revoke-the-web-apps-data-access"></a>웹앱의 데이터 액세스를 취소하는 방법

스토리지 계정에 대한 웹앱의 액세스를 취소하는 방법에는 두 가지가 있습니다. 

### <a name="rotate-the-sas-key-for-the-azure-storage-account"></a>Azure Storage 계정의 SAS 키를 회전합니다.

스토리지 계정의 SAS 키를 회전하는 경우, 웹앱은 더 이상 스토리지 계정에 액세스할 수 없게 되지만, 마지막으로 다운로드한 버전의 패키지 파일을 사용하여 계속 실행됩니다. 마지막으로 다운로드한 버전을 지우려면 웹앱을 다시 시작하십시오.

### <a name="remove-the-web-apps-access-to-key-vault"></a>Key Vault에 대한 웹앱의 액세스 권한 제거하기

Key Vault에 대한 웹앱의 액세스를 사용하지 않도록 설정하여 사이트 데이터에 대한 웹앱의 액세스를 취소할 수 있습니다. 이렇게 하려면 웹앱 ID에 대한 액세스 정책을 제거합니다. 이는 이전에 키 자격 증명 모음 참조를 구성하면서 만든 것과 동일한 ID입니다.

## <a name="summary"></a>요약

이제 애플리케이션 파일이 스토리지 계정에서 미사용으로 암호화됩니다. 웹앱이 시작되면 키 자격 증명 모음에서 SAS URL을 검색합니다. 마지막으로 웹앱은 스토리지 계정에서 애플리케이션 파일을 로드합니다. 

스토리지 계정에 대한 웹앱의 액세스 권한을 취소해야 하는 경우, 키 자격 증명 모음에 대한 액세스를 취소하거나 스토리지 계정 키를 회전할 수 있습니다. 그러면 SAS URL이 무효화됩니다.

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="is-there-any-additional-charge-for-running-my-web-app-from-the-deployment-package"></a>배포 패키지에서 웹앱을 실행하는 데 추가 요금이 부과되나요?

Azure Storage 계정과 관련된 비용 및 모든 해당 송신 요금만이 적용됩니다.

### <a name="how-does-running-from-the-deployment-package-affect-my-web-app"></a>배포 패키지에서의 실행이 나의 웹앱에 어떤 영향을 주나요?

- 배포 패키지에서 앱을 실행하면 `wwwroot/`을 읽기 전용으로 만듭니다. 앱에서 이 디렉터리에 쓰려고 할 때 오류가 수신됩니다.
- TAR 및 GZIP 형식은 지원되지 않습니다.
- 이 기능은 로컬 캐시와 호환되지 않습니다.

## <a name="next-steps"></a>다음 단계

- [App Service의 Key Vault 참조](app-service-key-vault-references.md)
- [미사용 데이터에 대한 Azure Storage 암호화](../storage/common/storage-service-encryption.md)