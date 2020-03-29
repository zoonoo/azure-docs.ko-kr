---
title: 앱을 다른 지역으로 이동
description: 한 리전에서 다른 리전으로 앱 서비스 리소스를 이동하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 02/27/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 7e68f12ce062831ad361c88345188aca61922c4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77925710"
---
# <a name="move-an-app-service-app-to-another-region"></a>앱 서비스 앱을 다른 지역으로 이동

이 문서에서는 앱 서비스 리소스를 다른 Azure 리전으로 이동하는 방법에 대해 설명합니다. 여러 가지 이유로 리소스를 다른 지역으로 이동할 수 있습니다. 예를 들어 새 Azure 리전을 활용하고, 특정 지역에서만 사용할 수 있는 기능 또는 서비스를 배포하거나, 내부 정책 및 거버넌스 요구 사항을 충족하거나, 용량 계획 요구 사항에 응답합니다.

앱 서비스 리소스는 지역별로 다르며 리전 간에 이동할 수 없습니다. 대상 지역에서 기존 앱 서비스 리소스의 복사본을 만들고 콘텐츠를 새 앱으로 이동해야 합니다. 소스 앱에서 사용자 지정 도메인을 사용하는 경우 완료되면 [대상 지역의 새 앱으로 마이그레이션할](manage-custom-dns-migrate-domain.md) 수 있습니다.

앱을 더 쉽게 복사할 수 있도록 개별 앱 서비스 앱을 다른 지역의 앱 서비스 계획에 [limitations](app-service-web-app-cloning.md#current-restrictions) [복제할](app-service-web-app-cloning.md) 수 있지만 특히 Linux 앱을 지원하지 않는 제한사항이 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

- 앱 서비스 앱이 이동하려는 Azure 지역에 있는지 확인합니다.
- 대상 리전에서 이동하려는 리소스를 지원하는 앱 서비스 및 관련 서비스를 지원하는지 확인합니다.
<!-- - Domain bindings, certificates, and managed identities can't replicated using the **Export template** method. You must create them manually. -->

## <a name="prepare"></a>준비

현재 사용 중인 모든 앱 서비스 리소스를 식별합니다. 예를 들어:

- App Service 앱
- [앱 서비스 계획](overview-hosting-plans.md)
- [배포 슬롯](deploy-staging-slots.md)
- [Azure에서 구입한 사용자 지정 도메인](manage-custom-dns-buy-domain.md)
- [SSL 인증서](configure-ssl-certificate.md)
- [Azure 가상 네트워크 통합](web-sites-integrate-with-vnet.md)
- [하이브리드 연결](app-service-hybrid-connections.md).
- [관리되는 ID](overview-managed-identity.md)
- [백업 설정](manage-backup.md)

가져온 인증서 또는 하이브리드 연결과 같은 특정 리소스에는 다른 Azure 서비스와의 통합이 포함됩니다. 지역 간에 이러한 리소스를 이동하는 방법에 대한 자세한 내용은 해당 서비스에 대한 설명서를 참조하십시오.

## <a name="move"></a>이동

1. [소스 앱의 백업을 만듭니다.](manage-backup.md)
1. [대상 지역에서 새 앱 서비스 계획에서 앱을 만듭니다.](app-service-plan-manage.md#create-an-app-service-plan)
2. [대상 앱에서 백업 복원](web-sites-restore.md)
2. 사용자 지정 도메인을 사용하는 경우 대상 앱에 미리 `awverify.` [바인딩하고 대상 앱에서](manage-custom-dns-migrate-domain.md#bind-the-domain-name-preemptively) [도메인을 사용하도록 설정합니다.](manage-custom-dns-migrate-domain.md#enable-the-domain-for-your-app)
3. 대상 앱의 다른 모든 것을 원본 앱과 동일하게 구성하고 구성을 확인합니다.
4. 사용자 지정 도메인이 대상 앱을 가리킬 준비가 되면 [도메인 이름을 다시 매핑합니다.](manage-custom-dns-migrate-domain.md#remap-the-active-dns-name)

<!-- 1. Login to the [Azure portal](https://portal.azure.com) > **Resource Groups**.
2. Locate the Resource Group that contains the source App Service resources and click on it.
3. Select > **Settings** > **Export template**.
4. Choose **Deploy** in the **Export template** blade.
5. Click **TEMPLATE** > **Edit template** to open the template in the online editor.
6. Click inside the online editor and type Ctrl+F (or ⌘+F on a Mac) and type `"identity": {` to find any managed identity definition. The following is an example if you have a user-assigned managed identity.
    ```json
    "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
            "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/<group-name>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<identity-name>": {
                "principalId": "00000000-0000-0000-0000-000000000000",
                "clientId": "00000000-0000-0000-0000-000000000000"
            }
        }
    },
    ```
6. Click inside the online editor and type Ctrl+F (or ⌘+F on a Mac) and type `"Microsoft.Web/sites/hostNameBindings` to find all hostname bindings. The following is an example if you have a user-assigned managed identity.
    ```json
    {
        "type": "Microsoft.Web/sites/hostNameBindings",
        "apiVersion": "2018-11-01",
        "name": "[concat(parameters('sites_webapp_name'), '/', parameters('sites_webapp_name'), '.azurewebsites.net')]",
        "location": "West Europe",
        "dependsOn": [
            "[resourceId('Microsoft.Web/sites', parameters('sites_webapp_name'))]"
        ],
        "properties": {
            "siteName": "<app-name>",
            "hostNameType": "Verified"
        }
    },
    ```
6. Click inside the online editor and type Ctrl+F (or ⌘+F on a Mac) and type `"Microsoft.Web/certificates` to find all hostname bindings. The following is an example if you have a user-assigned managed identity.
    ```json
    {
        "type": "Microsoft.Web/certificates",
        "apiVersion": "2018-11-01",
        "name": "[parameters('certificates_test2_cephaslin_com_name')]",
        "location": "West Europe",
        "properties": {
            "hostNames": [
                "[parameters('certificates_test2_cephaslin_com_name')]"
            ],
            "password": "[parameters('certificates_test2_cephaslin_com_password')]"
        }
    },
    ```
7. Delete the entire JSON block. Click **Save** in the online editor.
8. Click **BASICS** > **Create new** to create a new resource group. Type the group name and click **OK**.
9. In **BASICS** > **Location**, select the region you want.   -->

## <a name="clean-up-source-resources"></a>소스 리소스 정리

소스 앱 및 앱 서비스 계획을 삭제합니다. [무료 계층이 아닌 앱 서비스 요금제에는 앱이 실행되지 않더라도 요금이 부과됩니다.](app-service-plan-manage.md#delete-an-app-service-plan)

## <a name="next-steps"></a>다음 단계

[PowerShell을 사용하여 Azure App Service 앱 복제](app-service-web-app-cloning.md)