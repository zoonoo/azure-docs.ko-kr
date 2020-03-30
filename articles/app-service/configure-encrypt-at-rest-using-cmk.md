---
title: 미사용 응용 프로그램 소스 암호화
description: Azure Storage에서 응용 프로그램 데이터를 암호화하고 패키지 파일로 배포합니다.
ms.topic: article
ms.date: 03/06/2020
ms.openlocfilehash: 7e5e809fe8b670ae6ec5bfd15e54f9a8019e76d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79408746"
---
# <a name="encryption-at-rest-using-customer-managed-keys"></a>고객 관리 키를 사용하여 미사용 암호화

사용 중이면 웹 앱의 응용 프로그램 데이터를 암호화하려면 Azure 저장소 계정과 Azure 키 자격 증명 모음이 필요합니다. 이러한 서비스는 배포 패키지에서 앱을 실행할 때 사용됩니다.

  - [Azure 저장소는 미사용 암호화를 제공합니다.](../storage/common/storage-service-encryption.md) 시스템에서 제공한 키 또는 고객 관리 키를 직접 사용할 수 있습니다. Azure의 웹 앱에서 실행되지 않을 때 응용 프로그램 데이터가 저장되는 위치입니다.
  - [배포 패키지에서 실행하는](deploy-run-package.md) 것은 App Service의 배포 기능입니다. SAS(공유 액세스 서명) URL을 사용하여 Azure 저장소 계정에서 사이트 콘텐츠를 배포할 수 있습니다.
  - [키 볼트 참조는](app-service-key-vault-references.md) 앱 서비스의 보안 기능입니다. 런타임에 응용 프로그램 설정으로 비밀을 가져올 수 있습니다. Azure 저장소 계정의 SAS URL을 암호화하는 데 사용합니다.

## <a name="set-up-encryption-at-rest"></a>미사용 암호화 설정

### <a name="create-an-azure-storage-account"></a>Azure Storage 계정 만들기

먼저 [Azure Storage 계정을 만들고](../storage/common/storage-account-create.md) [고객 관리 키로 암호화합니다.](../storage/common/encryption-customer-managed-keys.md) 저장소 계정이 만들어지면 Azure [저장소 탐색기를](../vs-azure-tools-storage-manage-with-storage-explorer.md) 사용하여 패키지 파일을 업로드합니다.

다음으로 저장소 탐색기를 사용하여 [SAS 를 생성합니다.](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#generate-a-sas-in-storage-explorer) 

> [!NOTE]
> 이 SAS URL을 저장하면 런타임에 배포 패키지에 대한 보안 액세스를 활성화하는 데 나중에 사용됩니다.

### <a name="configure-running-from-a-package-from-your-storage-account"></a>저장소 계정에서 패키지실행 구성
  
파일을 Blob 저장소에 업로드하고 파일에 대한 SAS URL이 `WEBSITE_RUN_FROM_PACKAGE` 있으면 응용 프로그램 설정을 SAS URL로 설정합니다. 다음 예제는 Azure CLI를 사용하여 수행합니다.

```
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_RUN_FROM_PACKAGE="<your-SAS-URL>"
```

이 응용 프로그램 설정을 추가하면 웹 앱이 다시 시작됩니다. 앱을 다시 시작한 후 앱을 찾아보고 배포 패키지를 사용하여 앱이 올바르게 시작되었는지 확인합니다. 응용 프로그램이 올바르게 시작되지 않은 경우 [패키지 문제 해결 가이드에서 실행을](deploy-run-package.md#troubleshooting)참조하십시오.

### <a name="encrypt-the-application-setting-using-key-vault-references"></a>키 볼트 참조를 사용하여 응용 프로그램 설정 암호화

이제 `WEBSITE_RUN_FROM_PACKAGE` 응용 프로그램 설정의 값을 SAS 인코딩 된 URL에 대한 Key Vault 참조로 바꿀 수 있습니다. 이렇게 하면 키 볼트에서 SAS URL이 암호화되어 추가 보안 계층이 제공됩니다.

1. 다음 [`az keyvault create`](/cli/azure/keyvault#az-keyvault-create) 명령을 사용하여 키 볼트 인스턴스를 만듭니다.       

    ```azurecli    
    az keyvault create --name "Contoso-Vault" --resource-group <group-name> --location eastus    
    ```    

1. 다음 지침을 따라 키 자격 증명 [모음에 대한 앱 액세스 권한을 부여합니다.](app-service-key-vault-references.md#granting-your-app-access-to-key-vault)

1. 다음 [`az keyvault secret set`](/cli/azure/keyvault/secret#az-keyvault-secret-set) 명령을 사용하여 외부 URL을 키 자격 증명 모음의 비밀로 추가합니다.   

    ```azurecli    
    az keyvault secret set --vault-name "Contoso-Vault" --name "external-url" --value "<SAS-URL>"    
    ```    

1.  다음 [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) 명령을 사용하여 외부 `WEBSITE_RUN_FROM_PACKAGE` URL에 대한 Key Vault 참조값으로 응용 프로그램 설정을 만듭니다.

    ```azurecli    
    az webapp config appsettings set --settings WEBSITE_RUN_FROM_PACKAGE="@Microsoft.KeyVault(SecretUri=https://Contoso-Vault.vault.azure.net/secrets/external-url/<secret-version>"    
    ```

    는 `<secret-version>` 이전 `az keyvault secret set` 명령의 출력에 있을 것입니다.

이 응용 프로그램 설정을 업데이트하면 웹 앱이 다시 시작됩니다. 앱을 다시 시작한 후 키 볼트 참조를 사용하여 앱이 올바르게 시작되었는지 찾아봅니까?

## <a name="how-to-rotate-the-access-token"></a>액세스 토큰을 회전하는 방법

저장소 계정의 SAS 키를 주기적으로 회전하는 것이 좋습니다. 웹 앱이 실수로 액세스가 느슨하지 않도록 하려면 키 자격 증명 모음의 SAS URL도 업데이트해야 합니다.

1. Azure 포털의 저장소 계정으로 이동하여 SAS 키를 회전합니다. **설정** > **액세스 키에서**아이콘을 클릭하여 SAS 키를 회전시려면

1. 새 SAS URL을 복사하고 다음 명령을 사용하여 키 자격 증명 모음에서 업데이트된 SAS URL을 설정합니다.

    ```azurecli    
    az keyvault secret set --vault-name "Contoso-Vault" --name "external-url" --value "<SAS-URL>"    
    ``` 

1. 응용 프로그램 설정에서 키 자격 증명 모음 참조를 새 비밀 버전으로 업데이트합니다.

    ```azurecli    
    az webapp config appsettings set --settings WEBSITE_RUN_FROM_PACKAGE="@Microsoft.KeyVault(SecretUri=https://Contoso-Vault.vault.azure.net/secrets/external-url/<secret-version>"    
    ```

    는 `<secret-version>` 이전 `az keyvault secret set` 명령의 출력에 있을 것입니다.

## <a name="how-to-revoke-the-web-apps-data-access"></a>웹 앱의 데이터 액세스를 취소하는 방법

저장소 계정에 대한 웹 앱의 액세스를 취소하는 방법에는 두 가지가 있습니다. 

### <a name="rotate-the-sas-key-for-the-azure-storage-account"></a>Azure 저장소 계정에 대한 SAS 키 회전

저장소 계정의 SAS 키가 회전되면 웹 앱은 더 이상 저장소 계정에 액세스할 수 없지만 마지막으로 다운로드한 패키지 파일 버전에서 계속 실행됩니다. 웹 앱을 다시 시작하여 마지막으로 다운로드한 버전을 지웁히 지웁습니다.

### <a name="remove-the-web-apps-access-to-key-vault"></a>키 볼트에 대한 웹 앱의 액세스 권한 제거

Key Vault에 대한 웹 앱의 액세스를 비활성화하여 웹 앱의 사이트 데이터에 대한 액세스를 취소할 수 있습니다. 이렇게 하려면 웹 앱의 ID에 대한 액세스 정책을 제거합니다. 키 자격 증명 모음 참조를 구성하는 동안 이전에 만든 ID와 동일합니다.

## <a name="summary"></a>요약

이제 응용 프로그램 파일이 저장소 계정에서 미사용 암호화됩니다. 웹 앱이 시작되면 키 자격 증명 모음에서 SAS URL을 검색합니다. 마지막으로 웹 앱은 저장소 계정에서 응용 프로그램 파일을 로드합니다. 

웹 앱의 저장소 계정에 대한 액세스를 취소해야 하는 경우 키 자격 증명 모음에 대한 액세스를 취소하거나 저장소 계정 키를 회전하여 SAS URL을 무효화할 수 있습니다.

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="is-there-any-additional-charge-for-running-my-web-app-from-the-deployment-package"></a>배포 패키지에서 웹 앱을 실행하는 데 추가 요금이 부과되지 는 않습니다.

Azure 저장소 계정과 연결된 비용 및 적용 가능한 송신 요금만 청구됩니다.

### <a name="how-does-running-from-the-deployment-package-affect-my-web-app"></a>배포 패키지에서 실행해도 웹 앱에 어떤 영향을 미칩니까?

- 배포 패키지에서 앱을 실행하면 읽기 전용이 됩니다. `wwwroot/` 앱이 이 디렉터리에 쓰려고 할 때 오류가 발생합니다.
- TAR 및 GZIP 형식은 지원되지 않습니다.
- 이 기능은 로컬 캐시와 호환되지 않습니다.

## <a name="next-steps"></a>다음 단계

- [앱 서비스에 대한 키 볼트 참조](app-service-key-vault-references.md)
- [미사용 데이터에 대한 Azure 저장소 암호화](../storage/common/storage-service-encryption.md)
