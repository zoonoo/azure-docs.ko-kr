---
title: Azure 포털을 통해 리소스 배포
description: Azure 포털 및 Azure 리소스 관리를 사용하여 구독의 리소스 그룹에 리소스를 배포합니다.
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: ea2faf810b3a5d1b8fa46575201022a501cc7d58
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80153440"
---
# <a name="deploy-resources-with-arm-templates-and-azure-portal"></a>ARM 템플릿 및 Azure 포털을 통해 리소스 배포

[AZURE 리소스 관리자(ARM) 템플릿을](overview.md) 사용하여 [Azure](https://portal.azure.com) 리소스를 배포하는 방법에 대해 알아봅니다. 리소스 관리에 대해 자세히 알아보려면 [Azure 포털을 사용하여 Azure 리소스 관리를](../management/manage-resources-portal.md)참조하세요.

Azure 포털을 사용하여 Azure 리소스를 배포하려면 일반적으로 다음 두 단계가 포함됩니다.

- 리소스 그룹을 만듭니다.
- 리소스 그룹에 리소스를 배포합니다.

또한 ARM 템플릿을 배포하여 Azure 리소스를 만들 수도 있습니다.

이 문서에서는 두 가지 방법을 모두 보여줍니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

1. 새 리소스 그룹을 만들려면 [Azure 포털에서](https://portal.azure.com) **리소스 그룹을** 선택합니다.

   ![리소스 그룹 선택](./media/deploy-portal/select-resource-groups.png)

1. 리소스 그룹에서 **추가를**선택합니다.

   ![리소스 그룹 추가](./media/deploy-portal/add-resource-group.png)

1. 다음 속성 값을 선택하거나 입력합니다.

    - **구독**: Azure 구독을 선택합니다.
    - **리소스 그룹**: 리소스 그룹에 이름을 지정합니다.
    - **지역**: Azure 위치를 지정합니다. 리소스 그룹이 리소스에 대한 메타데이터를 저장하는 위치입니다. 규정 준수 때문에 메타데이터를 저장할 위치를 지정하려고 합니다. 일반적으로 대부분의 리소스가 상주할 위치를 지정하는 것이 좋습니다. 동일한 위치를 사용하여 템플릿을 간소화할 수 있습니다.

   ![그룹값 설정](./media/deploy-portal/set-group-properties.png)

1. **검토 + 만들기를**선택합니다.
1. 값을 검토한 다음 **에서 만들기를**선택합니다.
1. 목록에서 새 리소스 그룹을 보려면 **새로 고침을** 선택합니다.

## <a name="deploy-resources-to-a-resource-group"></a>리소스 그룹에 리소스 배포

리소스 그룹을 만든 후 마켓플레이스에서 그룹에 리소스를 배포할 수 있습니다. Marketplace는 일반적인 시나리오에 사전 정의된 솔루션을 제공합니다.

1. 배포를 시작하려면 [Azure 포털에서](https://portal.azure.com) **리소스 만들기를** 선택합니다.

   ![새 리소스](./media/deploy-portal/new-resources.png)

1. 배포할 리소스의 유형을 찾습니다. 리소스는 범주별로 구성됩니다. 배포하려는 특정 솔루션이 보이지 않는다면 Marketplace에서 이를 검색할 수 있습니다. 다음 스크린샷은 우분투 서버가 선택되어 있음을 보여줍니다.

   ![리소스 종류 선택](./media/deploy-portal/select-resource-type.png)

1. 선택한 리소스 유형에 따라 배포하기 전에 관련 속성의 컬렉션을 설정합니다. 모든 유형에 대해 대상 리소스 그룹을 선택해야 합니다. 다음 이미지는 Linux 가상 컴퓨터를 만들고 만든 리소스 그룹에 배포하는 방법을 보여 주며, 이를 보여 주시면 됩니다.

   ![리소스 그룹 만들기](./media/deploy-portal/select-existing-group.png)

   또는 리소스를 배포할 때 리소스 그룹을 만들도록 결정할 수 있습니다. **새로 만들기** 를 선택하고 리소스 그룹에 이름을 지정합니다.

1. 배포가 시작됩니다. 배포에는 몇 분 정도 걸릴 수 있습니다. 일부 리소스는 다른 리소스보다 시간이 오래 걸릴 수 있습니다. 배포가 완료되면 알림이 표시됩니다. 열려면 **리소스로 이동을** 선택합니다.

   ![알림 보기](./media/deploy-portal/view-notification.png)

1. 리소스를 배포한 후에 **추가**를 선택하여 더 많은 리소스를 리소스 그룹에 추가할 수 있습니다.

   ![리소스 추가](./media/deploy-portal/add-resource.png)

## <a name="deploy-resources-from-custom-template"></a>사용자 지정 템플릿에서 배포

배포를 실행하려고 하지만 Marketplace에서 템플릿 중 하나를 사용하지 않으려는 경우 솔루션에 대한 인프라를 정의하는 사용자 지정된 템플릿을 만들 수 있습니다. 템플릿 만들기에 대해 자세히 알아보려면 [ARM 템플릿의 구조 및 구문 이해를](template-syntax.md)참조하십시오.

> [!NOTE]
> 포털 인터페이스는 [Key Vault에서 비밀](key-vault-parameter.md) 참조를 지원하지 않습니다. 대신 [PowerShell](deploy-powershell.md) 또는 [Azure CLI](deploy-cli.md)를 사용하여 외부 URI에서 또는 로컬로 템플릿을 배포합니다.

1. 포털을 통해 사용자 지정된 템플릿을 배포하려면 **리소스 만들기,** **템플릿**검색 을 선택합니다. 그런 다음 **템플릿 배포를**선택합니다.

   ![템플릿 배포 검색](./media/deploy-portal/search-template.png)

1. **만들기**를 선택합니다.
1. 템플릿을 만들기 위한 몇 가지 옵션이 있습니다.

    - **편집기에서 사용자 고유의 템플릿 을 빌드**: 포털 템플릿 편집기에서 템플릿을 만듭니다.  편집기는 리소스 템플릿 스키마를 추가할 수 있습니다.
    - **일반적인 템플릿**: Linux 가상 컴퓨터, Windows 가상 컴퓨터, 웹 응용 프로그램 및 Azure SQL 데이터베이스를 만들기 위한 네 가지 일반적인 템플릿이 있습니다.
    - **GitHub 빠른 시작 템플릿로드**: 기존 [빠른 시작 템플릿을](https://azure.microsoft.com/resources/templates/)사용합니다.

   ![옵션 보기](./media/deploy-portal/see-options.png)

    이 자습서에서는 빠른 시작 템플릿을 로드하는 방법에 대한 지침을 제공합니다.

1. **GitHub 빠른 시작 템플릿로드에서** **101 저장소 계정 만들기를**입력하거나 선택합니다.

    다음 두 가지 옵션을 사용할 수 있습니다.

    - **템플릿 선택**: 템플릿을 배포합니다.
    - **템플릿 편집**: 템플릿을 배포하기 전에 빠른 시작 템플릿을 편집합니다.

1. **템플릿 편집을** 선택하여 포털 템플릿 편집기를 탐색합니다. 템플릿이 편집기에서 로드됩니다. 두 가지 매개 변수가 있습니다: **storageAccountType** 및 **위치**.

   ![템플릿 만들기](./media/deploy-portal/show-json.png)

1. 템플릿을 약간 변경합니다. 예를 들어 **저장소계정** 이름 변수를 다음으로 업데이트합니다.

    ```json
    "storageAccountName": "[concat('azstore', uniquestring(resourceGroup().id))]"
    ```

1. **저장**을 선택합니다. 이제 포털 템플릿 배포 인터페이스가 표시됩니다. 템플릿에서 정의한 두 매개변수를 확인합니다.
1. 속성 값을 입력하거나 선택합니다.

    - **구독**: Azure 구독을 선택합니다.
    - 리소스 그룹 : **새 만들기를** 선택하고 이름을 **지정합니다.**
    - **위치**: Azure 위치를 선택합니다.
    - **저장소 계정 유형**: 기본값을 사용합니다.
    - **위치**: 기본값을 사용합니다.
    - **위에 명시된 사용 약관에 동의함**: (선택)

1. **구매**를 선택합니다.

## <a name="next-steps"></a>다음 단계

- 감사 로그를 보려면 [Resource Manager로 작업 감사](../management/view-activity-logs.md)를 참조하세요.
- 배포 오류를 해결하려면 [배포 작업 보기](deployment-history.md)를 참조하세요.
- 배포 또는 리소스 그룹에서 템플릿을 내보내려면 [ARM 내보내기 템플릿을](export-template-portal.md)참조하십시오.
- 여러 지역에서 서비스를 안전하게 롤아웃하려면 [Azure 배포 관리자](deployment-manager-overview.md)를 참조하십시오.
