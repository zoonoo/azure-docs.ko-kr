---
title: 배포 자격 증명 구성
description: Azure App Service에 있는 배포 자격 증명의 유형과 이를 구성하고 사용하는 방법에 대해 알아봅니다.
ms.topic: article
ms.date: 08/14/2019
ms.reviewer: byvinyal
ms.custom: seodec18
ms.openlocfilehash: c6f7c2422e043da6df498fe81da938576687b916
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83649137"
---
# <a name="configure-deployment-credentials-for-azure-app-service"></a>Azure App Service의 배포 자격 증명 구성
[Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714)는 [로컬 Git 배포](deploy-local-git.md) 및 [FTP/S 배포](deploy-ftp.md)를 위해 두 가지 유형의 자격 증명을 지원합니다. 이러한 자격 증명은 Azure 구독 자격 증명과 동일하지 않습니다.

[!INCLUDE [app-service-deploy-credentials](../../includes/app-service-deploy-credentials.md)]

## <a name="configure-user-level-credentials"></a><a name="userscope"></a>사용자 수준 자격 증명 구성

모든 앱의 [리소스 페이지](../azure-resource-manager/management/manage-resources-portal.md#manage-resources)에서 사용자 수준 자격 증명을 구성할 수 있습니다. 이러한 자격 증명을 구성하는 앱에 관계 없이 Azure 계정의 모든 앱과 모든 구독에 적용됩니다. 

### <a name="in-the-cloud-shell"></a>Cloud Shell에서

[Cloud Shell](https://shell.azure.com)에서 배포 사용자를 구성하려면 [az webapp deployment user set](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set) 명령을 실행합니다. \<username> 및 \<password>를 배포 사용자 이름 및 암호로 바꿉니다. 

- 사용자 이름은 Azure 내에서 고유해야 하고, 로컬 Git 푸시의경우 ' @' 기호를 포함하면 안 됩니다. 
- 암호는 글자, 숫자, 기호의 세 가지 요소 중 두 가지를 사용하고 8자 이상이어야 합니다. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

JSON 출력에는 암호가 `null`로 나옵니다. `'Conflict'. Details: 409` 오류가 발생하면 사용자 이름을 변경합니다. `'Bad Request'. Details: 400` 오류가 발생하면 더 강력한 암호를 사용합니다. 

### <a name="in-the-portal"></a>포털에서

Azure Portal에서 배포 자격 증명 페이지에 액세스하려면 앱이 하나 이상 있어야 합니다. 사용자 수준 자격 증명을 구성하려면

1. [Azure Portal](https://portal.azure.com)의 왼쪽 메뉴에서 **App Services** >  **\<any_app >**  > **배포 센터** > **FTP** > **대시보드**를 선택합니다.

    ![](./media/app-service-deployment-credentials/access-no-git.png)

    또는 Git 배포를 이미 구성한 경우 **App Services** >  **&lt;any_app>**  > **배포 센터** > **FTP/자격 증명**을 선택합니다.

    ![](./media/app-service-deployment-credentials/access-with-git.png)

2. **사용자 자격 증명**을 선택하여 사용자 이름 및 암호를 구성한 다음, **자격 증명 저장**을 선택합니다.

배포 자격 증명을 설정하면 앱의 **개요** 페이지에서 *Git* 배포 사용자 이름을 찾을 수 있습니다.

![](./media/app-service-deployment-credentials/deployment_credentials_overview.png)

Git 배포가 구성된 경우 페이지에 **Git/배포 사용자 이름**이 표시됩니다. 그렇지 않으면 **FTP/배포 사용자 이름**이 표시됩니다.

> [!NOTE]
> Azure에서는 사용자 수준 배포 암호가 표시되지 않습니다. 암호를 잊은 경우 이 섹션의 단계에 따라 자격 증명을 다시 설정할 수 있습니다.
>
> 

## <a name="use-user-level-credentials-with-ftpftps"></a>FTP/FTPS와 함께 사용자 수준 자격 증명 사용

사용자 수준 자격 증명을 사용하여 FTP/FTPS 엔드포인트를 인증하려면 `<app-name>\<user-name>` 형식의 사용자 이름이 필요합니다.

사용자 수준 자격 증명은 특정 리소스가 아니라 사용자에게 연결되므로 로그인 작업을 올바른 앱 엔드포인트로 보내려면 사용자 이름이 이 형식이어야 합니다.

## <a name="get-and-reset-app-level-credentials"></a><a name="appscope"></a>앱 수준 자격 증명 정보 가져오기 및 다시 설정
앱 수준 자격 증명을 가져오려면

1. [Azure Portal](https://portal.azure.com)의 왼쪽 메뉴에서 **App Services** >  **&lt;any_app>**  > **배포 센터** > **FTP/자격증명**을 선택합니다.

2. **앱 자격 증명**을 선택하고 **복사** 링크를 선택하여 사용자 이름 또는 암호를 복사합니다.

앱 수준 자격 증명을 다시 설정하려면 동일한 대화 상자에서 **자격 증명 다시 설정**을 선택합니다.

## <a name="next-steps"></a>다음 단계

이러한 자격 증명을 사용하여 [로컬 Git](deploy-local-git.md)에서 또는 [FTP/S](deploy-ftp.md)를 통해 앱을 배포하는 방법을 알아봅니다.
