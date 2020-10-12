---
title: 미사용 응용 프로그램 원본 암호화
description: Azure Storage에서 응용 프로그램 데이터를 암호화 하 고 패키지 파일로 배포 합니다.
ms.topic: article
ms.date: 03/06/2020
ms.openlocfilehash: 1dd0d11baa16a325a22a501d40e22e5bad6adb21
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87282333"
---
# <a name="encrypt-your-application-data-at-rest-using-customer-managed-keys"></a>고객 관리 키를 사용 하 여 미사용 응용 프로그램 데이터 암호화

휴지 상태의 함수 앱의 응용 프로그램 데이터를 암호화 하려면 Azure Storage 계정과 Azure Key Vault 필요 합니다. 이러한 서비스는 배포 패키지에서 앱을 실행할 때 사용 됩니다.

  - [Azure Storage는 미사용 암호화를 제공](../storage/common/storage-service-encryption.md)합니다. 시스템 제공 키 또는 고객이 관리 하는 고유한 키를 사용할 수 있습니다. Azure의 함수 앱에서 실행 되 고 있지 않을 때 응용 프로그램 데이터가 저장 되는 위치입니다.
  - [배포 패키지에서 실행](run-functions-from-deployment-package.md) 은 App Service의 배포 기능입니다. SAS (공유 액세스 서명) URL을 사용 하 여 Azure Storage 계정에서 사이트 콘텐츠를 배포할 수 있습니다.
  - [Key Vault 참조](../app-service/app-service-key-vault-references.md) 는 App Service의 보안 기능입니다. 이를 통해 런타임에 응용 프로그램 설정으로 암호를 가져올 수 있습니다. 이를 사용 하 여 Azure Storage 계정의 SAS URL을 암호화 합니다.

## <a name="set-up-encryption-at-rest"></a>미사용 암호화 설정

### <a name="create-an-azure-storage-account"></a>Azure Storage 계정 만들기

먼저 [Azure Storage 계정을 만들고](../storage/common/storage-account-create.md) [고객 관리 키를 사용 하 여 암호화](../storage/common/encryption-customer-managed-keys.md)합니다. 저장소 계정을 만든 후 [Azure Storage 탐색기](../vs-azure-tools-storage-manage-with-storage-explorer.md) 를 사용 하 여 패키지 파일을 업로드 합니다.

그런 다음 Storage 탐색기를 사용 하 여 [SAS를 생성](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#generate-a-sas-in-storage-explorer)합니다. 

> [!NOTE]
> 이 SAS URL을 저장 합니다 .이는 나중에 런타임에 배포 패키지에 대 한 보안 액세스를 사용 하도록 설정 하는 데 사용 됩니다.

### <a name="configure-running-from-a-package-from-your-storage-account"></a>저장소 계정의 패키지에서 실행 구성
  
Blob 저장소에 파일을 업로드 하 고 파일에 대 한 SAS URL이 있는 경우 `WEBSITE_RUN_FROM_PACKAGE` 응용 프로그램 설정을 SAS url로 설정 합니다. 다음 예에서는 Azure CLI를 사용 하 여이 작업을 수행 합니다.

```
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_RUN_FROM_PACKAGE="<your-SAS-URL>"
```

이 응용 프로그램 설정을 추가 하면 함수 앱이 다시 시작 됩니다. 앱이 다시 시작 되 면 해당 앱으로 이동 하 고 배포 패키지를 사용 하 여 앱이 제대로 시작 되었는지 확인 합니다. 응용 프로그램이 제대로 시작 되지 않으면 [패키지에서 실행 문제 해결 가이드](run-functions-from-deployment-package.md#troubleshooting)를 참조 하세요.

### <a name="encrypt-the-application-setting-using-key-vault-references"></a>Key Vault 참조를 사용 하 여 응용 프로그램 설정 암호화

이제 `WEBSITE_RUN_FROM_PACKAGE` 응용 프로그램 설정 값을 SAS 인코딩된 URL에 대 한 Key Vault 참조로 바꿀 수 있습니다. 그러면 추가 보안 계층을 제공 하는 Key Vault 암호화 된 SAS URL이 유지 됩니다.

1. 다음 명령을 사용 [`az keyvault create`](/cli/azure/keyvault#az-keyvault-create) 하 여 Key Vault 인스턴스를 만듭니다.       

    ```azurecli    
    az keyvault create --name "Contoso-Vault" --resource-group <group-name> --location eastus    
    ```    

1. 앱에 키 자격 증명 모음에 대 한 [액세스 권한을 부여 하려면 다음 지침을](../app-service/app-service-key-vault-references.md#granting-your-app-access-to-key-vault) 따르세요.

1. 다음 명령을 사용 [`az keyvault secret set`](/cli/azure/keyvault/secret#az-keyvault-secret-set) 하 여 외부 URL을 키 자격 증명 모음에 암호로 추가 합니다.   

    ```azurecli    
    az keyvault secret set --vault-name "Contoso-Vault" --name "external-url" --value "<SAS-URL>"    
    ```    

1.  다음 명령을 사용 [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) 하 여 값이 `WEBSITE_RUN_FROM_PACKAGE` 외부 URL에 대 한 Key Vault 참조로 응용 프로그램 설정을 만듭니다.

    ```azurecli    
    az webapp config appsettings set --settings WEBSITE_RUN_FROM_PACKAGE="@Microsoft.KeyVault(SecretUri=https://Contoso-Vault.vault.azure.net/secrets/external-url/<secret-version>"    
    ```

    는 `<secret-version>` 이전 명령의 출력에 `az keyvault secret set` 있습니다.

이 응용 프로그램 설정을 업데이트 하면 함수 앱이 다시 시작 됩니다. 앱이 다시 시작 되 면 Key Vault 참조를 사용 하 여 제대로 시작 되었는지 확인 합니다.

## <a name="how-to-rotate-the-access-token"></a>액세스 토큰을 회전 하는 방법

저장소 계정의 SAS 키를 정기적으로 회전 하는 것이 좋습니다. 함수 앱이 실수로 액세스를 완화 하지 않도록 하려면 Key Vault의 SAS URL도 업데이트 해야 합니다.

1. Azure Portal에서 저장소 계정으로 이동 하 여 SAS 키를 회전 합니다. **설정**  >  **액세스 키**에서 아이콘을 클릭 하 여 SAS 키를 회전 합니다.

1. 새 SAS URL을 복사 하 고 다음 명령을 사용 하 여 주요 자격 증명 모음에 업데이트 된 SAS URL을 설정 합니다.

    ```azurecli    
    az keyvault secret set --vault-name "Contoso-Vault" --name "external-url" --value "<SAS-URL>"    
    ``` 

1. 응용 프로그램 설정의 key vault 참조를 새 비밀 버전으로 업데이트 합니다.

    ```azurecli    
    az webapp config appsettings set --settings WEBSITE_RUN_FROM_PACKAGE="@Microsoft.KeyVault(SecretUri=https://Contoso-Vault.vault.azure.net/secrets/external-url/<secret-version>"    
    ```

    는 `<secret-version>` 이전 명령의 출력에 `az keyvault secret set` 있습니다.

## <a name="how-to-revoke-the-function-apps-data-access"></a>함수 앱의 데이터 액세스를 취소 하는 방법

저장소 계정에 대 한 함수 앱의 액세스를 취소 하는 방법에는 두 가지가 있습니다. 

### <a name="rotate-the-sas-key-for-the-azure-storage-account"></a>Azure Storage 계정의 SAS 키를 회전 합니다.

저장소 계정에 대 한 SAS 키를 회전 하는 경우 함수 앱은 더 이상 저장소 계정에 액세스할 수 없지만 마지막으로 다운로드 한 버전의 패키지 파일을 사용 하 여 계속 실행 됩니다. 마지막으로 다운로드 한 버전을 지우려면 함수 앱을 다시 시작 합니다.

### <a name="remove-the-function-apps-access-to-key-vault"></a>Key Vault에 대 한 함수 앱의 액세스 권한 제거

Key Vault에 대 한 함수 앱의 액세스를 사용 하지 않도록 설정 하 여 함수 앱의 사이트 데이터에 대 한 액세스를 취소할 수 있습니다. 이렇게 하려면 함수 앱 id에 대 한 액세스 정책을 제거 합니다. 키 자격 증명 모음 참조를 구성 하는 동안 이전에 만든 것과 동일한 id입니다.

## <a name="summary"></a>요약

이제 응용 프로그램 파일이 저장소 계정에서 미사용으로 암호화 됩니다. 함수 앱이 시작 되 면 키 자격 증명 모음에서 SAS URL을 검색 합니다. 마지막으로 함수 앱은 저장소 계정에서 응용 프로그램 파일을 로드 합니다. 

저장소 계정에 대 한 함수 앱의 액세스 권한을 취소 해야 하는 경우 키 자격 증명 모음에 대 한 액세스를 취소 하거나 저장소 계정 키를 회전할 수 있습니다. 그러면 SAS URL이 무효화 됩니다.

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="is-there-any-additional-charge-for-running-my-function-app-from-the-deployment-package"></a>배포 패키지에서 함수 앱을 실행 하는 데 추가 요금이 부과 되나요?

Azure Storage 계정과 관련 된 비용 및 모든 해당 송신 요금이 적용 됩니다.

### <a name="how-does-running-from-the-deployment-package-affect-my-function-app"></a>배포 패키지에서 실행 되는가 함수 앱에 어떤 영향을 미칩니까?

- 배포 패키지에서 앱을 실행 하면 `wwwroot/` 읽기 전용으로 설정 됩니다. 앱에서이 디렉터리에 쓰려고 할 때 오류가 수신 됩니다.
- TAR 및 GZIP 형식은 지원 되지 않습니다.
- 이 기능은 로컬 캐시와 호환 되지 않습니다.

## <a name="next-steps"></a>다음 단계

- [App Service에 대 한 참조 Key Vault](../app-service/app-service-key-vault-references.md)
- [미사용 데이터에 대한 Azure Storage 암호화](../storage/common/storage-service-encryption.md)
