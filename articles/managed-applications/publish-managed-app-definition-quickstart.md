---
title: Azure 관리되는 애플리케이션 만들기 | Microsoft Docs
description: 조직의 구성원을 위한 Azure 관리형 애플리케이션을 만드는 방법이 나와 있습니다.
services: managed-applications
author: tfitzmac
ms.service: managed-applications
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.date: 10/04/2018
ms.author: tomfitz
ms.openlocfilehash: 1f80d7e63d994f0e3eb3733b99afaa1b056f4686
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48804915"
---
# <a name="publish-an-azure-managed-application-definition"></a>관리되는 애플리케이션 정의 게시

이 빠른 시작에서는 관리되는 애플리케이션의 작업에 대해 소개합니다. 조직의 사용자에 대한 내부 카탈로그에 관리되는 애플리케이션 정의를 추가합니다. 간략히 소개하기 위해 이미 관리되는 애플리케이션용 파일을 빌드했습니다. 이러한 파일은 GitHub를 통해 사용할 수 있습니다. [서비스 카탈로그 응용 프로그램 만들기](publish-service-catalog-app.md) 자습서에서 이러한 파일을 빌드하는 방법을 알아볼 수 있습니다.

작업이 완료되면 관리되는 애플리케이션 정의가 있는 **appDefinitionGroup**이라는 리소스 그룹이 있습니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group-for-definition"></a>정의에 대한 리소스 그룹 만들기

관리되는 애플리케이션 정의는 리소스 그룹에 있습니다. 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컬렉션입니다.

리소스 그룹을 만들려면 다음 명령을 사용합니다.

```azurecli-interactive
az group create --name appDefinitionGroup --location westcentralus
```

## <a name="create-the-managed-application-definition"></a>관리형 애플리케이션 정의 만들기

관리되는 애플리케이션을 정의하는 경우 소비자에 대한 리소스를 관리하는 사용자, 그룹 또는 애플리케이션을 선택합니다. 이 ID에는 할당된 역할에 따라 관리되는 리소스 그룹에 대한 권한이 있습니다. 일반적으로 Azure Active Directory 그룹을 만들어 리소스를 관리합니다. 그러나 이 문서에서는 자신의 ID를 사용합니다.

ID의 개체 ID를 가져오려면 다음 명령에서 UPN(사용자 계정 이름)을 입력합니다.

```azurecli-interactive
userid=$(az ad user show --upn-or-object-id example@contoso.org --query objectId --output tsv)
```

다음으로 사용자에게 액세스 권한을 부여하려는 RBAC 기본 제공 역할에 대한 역할 정의 ID가 필요합니다. 다음 명령은 소유자 역할에 대한 역할 정의 ID를 가져오는 방법을 보여 줍니다.

```azurecli-interactive
roleid=$(az role definition list --name Owner --query [].name --output tsv)
```

이제 관리형 애플리케이션 정의 리소스를 만듭니다. 관리되는 애플리케이션에는 하나의 스토리지 계정만 포함됩니다.

```azurecli-interactive
az managedapp definition create \
  --name "ManagedStorage" \
  --location "westcentralus" \
  --resource-group appDefinitionGroup \
  --lock-level ReadOnly \
  --display-name "Managed Storage Account" \
  --description "Managed Azure Storage Account" \
  --authorizations "$userid:$roleid" \
  --package-file-uri "https://raw.githubusercontent.com/Azure/azure-managedapp-samples/master/samples/201-managed-storage-account/managedstorage.zip"
```

명령이 완료되면 리소스 그룹에 관리되는 애플리케이션 정의가 있습니다. 

앞의 예제에서 사용된 몇 가지 매개 변수는 다음과 같습니다.

* **resource-group**: 관리되는 응용 프로그램 정의를 만든 리소스 그룹의 이름입니다.
* **lock-level**: 관리되는 리소스 그룹에 배치하는 잠금 유형입니다. 고객이 이 리소스 그룹에서 바람직하지 않은 작업을 수행할 수 없게 합니다. 현재 지원되는 유일한 잠금 수준은 ReadOnly입니다. ReadOnly를 지정하는 경우 고객은 관리되는 리소스 그룹에 있는 리소스만 읽을 수 있습니다. 관리되는 리소스 그룹에 대한 액세스 권한이 부여된 게시자 ID는 잠금에서 제외됩니다.
* **authorizations**: 관리되는 리소스 그룹에 권한을 부여하는 데 사용되는 보안 주체 ID 및 역할 정의 ID를 설명합니다. `<principalId>:<roleDefinitionId>` 형식으로 지정됩니다. 둘 이상의 값이 필요한 경우 `<principalId1>:<roleDefinitionId1> <principalId2>:<roleDefinitionId2>` 형식으로 지정합니다. 값은 공백으로 구분됩니다.
* **package-file-uri**: 필요한 파일이 포함되어 있는 .zip 패키지의 위치입니다. 패키지에는 **mainTemplate.json** 및 **createUiDefinition.json** 파일이 있어야 합니다. **mainTemplate.json**은 관리되는 응용 프로그램의 일부로 만들어지는 Azure 리소스를 정의합니다. 템플릿은 일반 Resource Manager 템플릿과 차이가 없습니다. **createUiDefinition.json**은 포털을 통해 관리되는 응용 프로그램을 만드는 사용자를 위한 사용자 인터페이스를 생성합니다.

## <a name="next-steps"></a>다음 단계

관리되는 애플리케이션 정의를 게시했습니다. 이제 해당 정의의 인스턴스를 배포하는 방법을 알아보세요.

> [!div class="nextstepaction"]
> [빠른 시작: 서비스 카탈로그 앱 배포](deploy-service-catalog-quickstart.md)