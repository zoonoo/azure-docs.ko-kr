---
title: Azure Portal을 사용하여 Azure 리소스 배포 | Microsoft Docs
description: Azure 포털과 Azure 리소스 관리를 사용하여 리소스를 배포합니다.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: tomfitz
ms.openlocfilehash: a171d9b4f054c942eebb08e7e11dd1164545f408
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67460463"
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-portal"></a>리소스 관리자 템플릿과 Azure 포털로 리소스 배포

사용 방법 알아보기를 [Azure portal](https://portal.azure.com) 사용 하 여 [Azure Resource Manager](resource-group-overview.md) Azure 리소스를 배포 합니다. 리소스를 관리 하는 방법에 대 한 자세한 참조 [Azure portal을 사용 하 여 Azure 관리 리소스](manage-resources-portal.md)합니다.

일반적으로 Azure portal을 사용 하 여 Azure 리소스를 배포할 두 단계로 이루어집니다.

- 리소스 그룹을 만듭니다.
- 리소스 그룹에 리소스를 배포 합니다.

또한 Azure 리소스를 만드는 Azure Resource Manager 템플릿을 배포할 수 있습니다.

이 문서에서는 두 가지 방법을 모두 보여줍니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

1. 새 리소스 그룹을 만들려면 **리소스 그룹** 에서 합니다 [Azure portal](https://portal.azure.com)합니다.

   ![리소스 그룹 선택](./media/resource-group-template-deploy-portal/select-resource-groups.png)

1. 리소스 그룹에서 **추가**를 선택합니다.

   ![리소스 그룹 추가](./media/resource-group-template-deploy-portal/add-resource-group.png)

1. 선택 하거나 다음 속성 값을 입력 합니다.

    - **구독**: Azure 구독을 선택합니다.
    - **리소스 그룹**: 리소스 그룹 이름을 지정 합니다.
    - **지역**: Azure 위치를 지정 합니다. 리소스 그룹에서 리소스에 대 한 메타 데이터를 저장 하는 위치입니다. 규정 준수 때문에 메타데이터를 저장할 위치를 지정하려고 합니다. 일반적으로 대부분의 리소스가 상주할 위치를 지정하는 것이 좋습니다. 동일한 위치를 사용하여 템플릿을 간소화할 수 있습니다.

   ![그룹값 설정](./media/resource-group-template-deploy-portal/set-group-properties.png)

1. **검토 + 만들기**를 선택합니다.
1. 값을 검토 하 고 선택한 **만들기**합니다.
1. 선택 **새로 고침** 목록에서 새 리소스 그룹을 볼 수 있습니다.

## <a name="deploy-resources-to-a-resource-group"></a>리소스 그룹에 리소스 배포

리소스 그룹을 만든 후 Marketplace에서 리소스 그룹에 배포할 수 있습니다. Marketplace는 일반적인 시나리오에 사전 정의된 솔루션을 제공합니다.

1. 배포를 시작 하려면 **리소스 만들기** 에서 합니다 [Azure portal](https://portal.azure.com)합니다.

   ![새 리소스](./media/resource-group-template-deploy-portal/new-resources.png)

1. 배포할 리소스의 유형을 찾습니다. 리소스를 범주로 구성 됩니다. 배포하려는 특정 솔루션이 보이지 않는다면 Marketplace에서 이를 검색할 수 있습니다. 다음 스크린샷에서 Ubuntu 서버가 선택 되어 있는지 보여 줍니다.

   ![리소스 종류 선택](./media/resource-group-template-deploy-portal/select-resource-type.png)

1. 선택한 리소스 유형에 따라 배포하기 전에 관련 속성의 컬렉션을 설정합니다. 모든 유형에 대해 대상 리소스 그룹을 선택해야 합니다. 다음 이미지는 Linux 가상 머신을 만들고 만든 리소스 그룹에 배포 하는 방법을 보여 줍니다.

   ![리소스 그룹 만들기](./media/resource-group-template-deploy-portal/select-existing-group.png)

   또는 리소스를 배포할 때 리소스 그룹을 만들도록 결정할 수 있습니다. **새로 만들기** 를 선택하고 리소스 그룹에 이름을 지정합니다.

1. 배포가 시작됩니다. 배포에는 몇 분 정도 걸릴 수 있습니다. 일부 리소스에는 다른 리소스 보다 더 걸릴 합니다. 배포가 완료되면 알림이 표시됩니다. 선택 **리소스로 이동** 를 열려면

   ![알림 보기](./media/resource-group-template-deploy-portal/view-notification.png)

1. 리소스를 배포한 후에 **추가**를 선택하여 더 많은 리소스를 리소스 그룹에 추가할 수 있습니다.

   ![리소스 추가](./media/resource-group-template-deploy-portal/add-resource.png)

## <a name="deploy-resources-from-custom-template"></a>사용자 지정 템플릿에서 배포

배포를 실행하려고 하지만 Marketplace에서 템플릿 중 하나를 사용하지 않으려는 경우 솔루션에 대한 인프라를 정의하는 사용자 지정된 템플릿을 만들 수 있습니다. 템플릿을 만드는 방법에 대해 알아보려면 [Azure Resource Manager 템플릿의 구조 및 구문 이해](resource-group-authoring-templates.md)를 참조하세요.

> [!NOTE]
> 포털 인터페이스는 [Key Vault에서 비밀](resource-manager-keyvault-parameter.md) 참조를 지원하지 않습니다. 대신 [PowerShell](resource-group-template-deploy.md) 또는 [Azure CLI](resource-group-template-deploy-cli.md)를 사용하여 외부 URI에서 또는 로컬로 템플릿을 배포합니다.

1. 포털을 통해 사용자 지정된 템플릿을 배포 하려면를 선택 **리소스 만들기**, 검색할 **템플릿**합니다. 선택한 후 **템플릿 배포**합니다.

   ![템플릿 배포 검색](./media/resource-group-template-deploy-portal/search-template.png)

1. **만들기**를 선택합니다.
1. 템플릿을 만들기 위한 몇 가지 옵션이 표시 됩니다.

    - **편집기에서 사용자 고유의 템플릿을 빌드합니다**: 편집기 포털 템플릿을 사용 하 여 템플릿을 만듭니다.  편집기는 리소스 템플릿 스키마를 추가할 수 있습니다.
    - **일반적인 템플릿**: Linux 가상 머신, Windows 가상 머신, 웹 응용 프로그램 및 Azure SQL database를 만들기 위한 일반적인 templatess 네 가지가 있습니다.
    - **GitHub 빠른 시작 템플릿 로드**: 기존 사용할 [빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/)합니다.

   ![옵션 보기](./media/resource-group-template-deploy-portal/see-options.png)

    이 자습서에서는 빠른 시작 템플릿 로드 하기 위한 지침을 제공 합니다.

1. 아래 **GitHub 빠른 시작 템플릿 로드**입력 하거나 선택 **101-저장소-계정 만들기**합니다.

    다음 두 가지 옵션을 사용할 수 있습니다.

    - **템플릿 선택**: 템플릿을 배포 합니다.
    - **템플릿 편집**: 배포 하기 전에 빠른 시작 템플릿을 편집 합니다.

1. 선택 **템플릿 편집** 포털 템플릿의 편집기를 소개 합니다. 템플릿을은 편집기에 로드 됩니다. 두 가지 매개 변수를 확인할 수 있습니다: **storageAccountType** 하 고 **위치**합니다.

   ![템플릿 만들기](./media/resource-group-template-deploy-portal/show-json.png)

1. 서식 파일에 사소한 변경만 수행 합니다. 예를 들어 업데이트는 **storageAccountName** 변수:

    ```json
    "storageAccountName": "[concat('azstore', uniquestring(resourceGroup().id))]"
    ```

1. **저장**을 선택합니다. 이제 포털 템플릿 배포 인터페이스를 표시 합니다. 템플릿에 정의 된 두 매개 변수를 확인 합니다.
1. 입력 하거나 속성 값을 선택 합니다.

    - **구독**: Azure 구독을 선택합니다.
    - **리소스 그룹**: 선택 **새로 만들기** 이름을 지정 합니다.
    - **Location**: Azure 위치를 선택합니다.
    - **저장소 계정 유형**: 기본값을 사용합니다.
    - **Location**: 기본값을 사용합니다.
    - **위에 명시된 사용 약관에 동의함**: (선택)

1. **구매**를 선택합니다.

## <a name="next-steps"></a>다음 단계

- 감사 로그를 보려면 [Resource Manager로 작업 감사](./resource-group-audit.md)를 참조하세요.
- 배포 오류를 해결하려면 [배포 작업 보기](./resource-manager-deployment-operations.md)를 참조하세요.
- 배포 또는 리소스 그룹에서 템플릿을 내보내려면 참조 [Azure Resource Manager 템플릿 내보내기](./manage-resource-groups-portal.md#export-resource-groups-to-templates)합니다.
- 안전 하 게 여러 지역에서 서비스에 롤아웃, 참조 [Azure Deployment Manager](./deployment-manager-overview.md)합니다.
