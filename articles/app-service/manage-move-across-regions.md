---
title: 다른 지역으로 앱 이동
description: 한 지역에서 다른 지역으로 App Service 리소스를 이동 하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 02/27/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: c19c18e8d9980b75acd9790dba712fbb6b2a4b1d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84945480"
---
# <a name="move-an-app-service-app-to-another-region"></a>App Service 앱을 다른 지역으로 이동

이 문서에서는 App Service 리소스를 다른 Azure 지역으로 이동 하는 방법을 설명 합니다. 여러 가지 이유로 리소스를 다른 지역으로 이동할 수 있습니다. 예를 들어, 새 Azure 지역을 활용 하 여 특정 지역 에서만 사용할 수 있는 기능 또는 서비스를 배포 하거나, 내부 정책 및 거 버 넌 스 요구 사항을 충족 하거나, 용량 계획 요구 사항에 대 한 응답으로 배포할 수 있습니다.

App Service 리소스는 지역에 따라 달라 지 며 지역 간에 이동할 수 없습니다. 대상 지역에 기존 App Service 리소스의 복사본을 만든 다음 콘텐츠를 새 앱으로 이동 해야 합니다. 원본 앱에서 사용자 지정 도메인을 사용 하는 경우 완료 되 면 [대상 지역의 새 앱으로 마이그레이션할](manage-custom-dns-migrate-domain.md) 수 있습니다.

앱을 더 쉽게 복사 하기 위해 [개별 App Service 앱](app-service-web-app-cloning.md) 을 다른 지역의 App Service 계획에 복제할 수 있지만 특히 Linux 앱을 지원 하지 않는 [제한](app-service-web-app-cloning.md#current-restrictions)사항이 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

- App Service 앱이 이동 하려는 Azure 지역에 있는지 확인 합니다.
- 대상 지역이 리소스를 이동 하려는 App Service 및 관련 서비스를 지원 하는지 확인 합니다.
<!-- - Domain bindings, certificates, and managed identities can't replicated using the **Export template** method. You must create them manually. -->

## <a name="prepare"></a>준비

현재 사용 중인 모든 App Service 리소스를 식별 합니다. 예를 들어:

- App Service 앱
- [App Service 계획](overview-hosting-plans.md)
- [배포 슬롯](deploy-staging-slots.md)
- [Azure에서 구매한 사용자 지정 도메인](manage-custom-dns-buy-domain.md)
- [SSL 인증서](configure-ssl-certificate.md)
- [Azure Virtual Network 통합](web-sites-integrate-with-vnet.md)
- [하이브리드 연결](app-service-hybrid-connections.md).
- [관리 ID](overview-managed-identity.md)
- [백업 설정](manage-backup.md)

가져온 인증서 또는 하이브리드 연결과 같은 특정 리소스에는 다른 Azure 서비스와의 통합이 포함 되어 있습니다. 이러한 리소스를 지역 간에 이동 하는 방법에 대 한 자세한 내용은 각 서비스에 대 한 설명서를 참조 하세요.

## <a name="move"></a>이동

1. [원본 앱의 백업을 만듭니다](manage-backup.md).
1. [새 App Service 계획의 대상 지역에 앱을 만듭니다](app-service-plan-manage.md#create-an-app-service-plan).
2. [대상 앱에서 백업 복원](web-sites-restore.md)
2. 사용자 지정 도메인을 사용 하는 경우 미리을 사용 하 여 [대상 앱에 바인딩하고](manage-custom-dns-migrate-domain.md#bind-the-domain-name-preemptively) `awverify.` [대상 앱에서 도메인을 사용 하도록 설정](manage-custom-dns-migrate-domain.md#enable-the-domain-for-your-app)합니다.
3. 대상 앱의 다른 모든 항목을 원본 앱과 동일 하 게 구성 하 고 구성을 확인 합니다.
4. 사용자 지정 도메인이 대상 앱을 가리키도록 준비 되 면 [도메인 이름을 다시 매핑합니다](manage-custom-dns-migrate-domain.md#remap-the-active-dns-name).

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

## <a name="clean-up-source-resources"></a>원본 리소스 정리

원본 앱 및 App Service 계획을 삭제 합니다. [무료 계층의 App Service 계획에는 앱이 실행 되 고 있지 않은 경우에도 요금이 부과 됩니다.](app-service-plan-manage.md#delete-an-app-service-plan)

## <a name="next-steps"></a>다음 단계

[PowerShell을 사용하여 Azure App Service 앱 복제](app-service-web-app-cloning.md)