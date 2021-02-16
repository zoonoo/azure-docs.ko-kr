---
title: '빠른 시작: Azure Portal에서 Azure Purview 계정 만들기(미리 보기)'
description: 이 빠른 시작에서는 Azure Purview 계정을 만들고, 사용을 시작할 수 있는 권한을 구성하는 방법을 설명합니다.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: quickstart
ms.date: 10/23/2020
ms.openlocfilehash: 82b72567a0db4dc9dbff9128402d0ee9f40f3512
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/10/2021
ms.locfileid: "100094154"
---
# <a name="quickstart-create-an-azure-purview-account-in-the-azure-portal"></a>빠른 시작: Azure Portal에서 Azure Purview 계정 만들기

> [!IMPORTANT]
> Azure Purview는 현재 미리 보기에 있습니다. [Microsoft Azure 미리 보기에 대한 추가 사용 조건](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기에 있거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.

이 빠른 시작에서는 Azure Purview 계정을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

* 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* 사용자 고유의 [Azure Active Directory 테넌트](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

* 계정에는 구독에서 리소스를 만들 수 있는 권한이 있어야 합니다.

* 모든 애플리케이션에서 **Storage 계정** 및 **EventHub 네임스페이스** 를 만들지 못하도록 차단하는 **Azure Policy** 가 있는 경우 태그를 사용하여 정책 예외를 만들어야 합니다. 이 예외는 Purview 계정을 만드는 과정에서 입력할 수 있습니다. 가장 중요한 이유는 만든 각 Purview 계정에 대해 관리되는 리소스 그룹을 만들고 이 리소스 그룹 내에 Storage 계정 및 EventHub 네임스페이스를 만들어야 하기 때문입니다.

    > [!important]
    > Azure Policy가 없거나 기존 Azure Policy가 **스토리지 계정** 및 **EventHub 네임스페이스** 생성을 차단하지 않는 경우 이 단계를 수행하지 않아도 됩니다.

    1. Azure Portal로 이동하여 **정책** 을 검색합니다.
    1. [사용자 지정 정책 정의 만들기](../governance/policy/tutorials/create-custom-policy-definition.md)를 수행하거나, 기존 정책을 수정하여 `not` 연산자와 `resourceBypass` 태그를 사용하는 두 가지 예외를 추가합니다.

        ```json
        {
          "mode": "All",
          "policyRule": {
            "if": {
              "anyOf": [
              {
                "allOf": [
                {
                  "field": "type",
                  "equals": "Microsoft.Storage/storageAccounts"
                },
                {
                  "not": {
                    "field": "tags['<resourceBypass>']",
                    "exists": true
                  }
                }]
              },
              {
                "allOf": [
                {
                  "field": "type",
                  "equals": "Microsoft.EventHub/namespaces"
                },
                {
                  "not": {
                    "field": "tags['<resourceBypass>']",
                    "exists": true
                  }
                }]
              }]
            },
            "then": {
              "effect": "deny"
            }
          },
          "parameters": {}
        }
        ```
        
        > [!Note]
        > 태그는 `resourceBypass` 옆에 있을 수 있으며, 정책에서 태그를 검색할 수 있는 한 나중 단계에서 Purview를 만들 때 사용자가 값을 정의할 수 있습니다.

        :::image type="content" source="./media/create-catalog-portal/policy-definition.png" alt-text="정책 정의를 만드는 방법을 보여 주는 스크린샷":::

    1. 만든 사용자 지정 정책을 사용하여 [정책 할당을 만듭니다](../governance/policy/assign-policy-portal.md).

        [ ![정책 할당을 만드는 방법을 보여 주는 스크린샷](./media/create-catalog-portal/policy-assignment.png)](./media/create-catalog-portal/policy-assignment.png#lightbox)

## <a name="sign-in-to-azure"></a>Azure에 로그인

Azure 계정을 사용하여 [Azure Portal](https://portal.azure.com) 에 로그인합니다.

## <a name="configure-your-subscription"></a>구독 구성

필요한 경우 다음 단계에 따라 구독에서 Azure Purview가 실행될 수 있도록 구독을 구성합니다.

   1. Azure Portal에서 **구독** 을 검색하고 선택합니다.

   1. 구독 목록에서 사용하려는 구독을 선택합니다. 구독에 대한 관리자 액세스 권한이 필요합니다.

      :::image type="content" source="./media/create-catalog-portal/select-subscription.png" alt-text="Azure Portal에서 구독을 선택하는 방법을 보여 주는 스크린샷":::

   1. 구독의 경우 **리소스 공급자** 를 선택합니다. **리소스 공급자** 창에서 다음 세 가지 리소스 공급자를 모두 검색하고 등록합니다. 
       1. **Microsoft.Purview**
       1. **Microsoft.Storage**
       1. **Microsoft.EventHub** 
      
      등록되지 않은 경우 **등록** 을 선택하여 등록합니다.

      :::image type="content" source="./media/create-catalog-portal/register-purview-resource-provider.png" alt-text="Azure Portal에서 Microsoft.Azure Purview 리소스 공급자를 등록하는 방법을 보여 주는 스크린샷":::

## <a name="create-an-azure-purview-account-instance"></a>Azure Purview 계정 인스턴스 만들기

1. Azure Portal에서 **Purview 계정** 페이지로 이동한 다음, **추가** 를 선택하여 새 Azure Purview 계정을 만듭니다. 또는 **Purview 계정** 에 대한 마켓플레이스 검색으로 이동하여 **만들기** 를 선택할 수 있습니다. Azure Purview 계정은 한 번에 하나만 추가할 수 있습니다.

   :::image type="content" source="./media/create-catalog-portal/add-purview-instance.png" alt-text="Azure Portal에서 Azure Purview 계정 인스턴스를 만드는 방법을 보여 주는 스크린샷":::

    > [!Note] 
    > Azure Purview는 지역 간에 계정 이동을 지원하지 않습니다. 이에 대한 자세한 내용은 [Azure 지원 서비스 페이지](https://docs.microsoft.com/azure/azure-resource-manager/management/region-move-support)에서 확인할 수 있습니다.

1. **기본** 탭에서 다음을 수행합니다.
    1. **리소스 그룹** 을 선택합니다.
    1. 카탈로그에 대한 **Purview 계정 이름** 을 입력합니다. 공백과 기호는 허용되지 않습니다.
    1. **위치** 를 선택하고, **다음: 구성** 을 선택합니다.
1. **구성** 탭에서 원하는 **플랫폼 크기** 를 선택합니다. 허용되는 값은 4CU(용량 단위) 및 16CU입니다. 완료되면 **다음: 태그** 를 선택합니다.
1. **태그** 탭에서 필요에 따라 하나 이상의 태그를 추가할 수 있습니다. 이러한 태그는 Azure Purview가 아니라 Azure Portal에서만 사용할 수 있습니다. 

    > [!Note] 
    > **Azure Policy** 가 있고 **사전 요구 사항** 에서와 같이 예외를 추가해야 하는 경우 올바른 태그를 추가해야 합니다. 예를 들어 `resourceBypass` 태그를 추가할 수 있습니다. :::image type="content" source="./media/create-catalog-portal/add-purview-tag.png" alt-text="Purview 계정에 태그 추가":::

1. **검토 + 만들기** 를 선택한 다음, **만들기** 를 선택합니다. 만들기를 완료하는 데 몇 분 정도 걸립니다. 새로 만든 Azure Purview 계정 인스턴스가 **Purview 계정** 페이지의 목록에 표시됩니다.
1. 새 계정 프로비저닝이 완료되면 **리소스로 이동** 을 선택합니다.

    > [!Note]
    > 프로비저닝이 `Conflict` 상태로 인해 실패하면 Purview에서 **Storage 계정** 및 **EventHub 네임스페이스** 를 만들지 못하도록 차단하는 Azure 정책이 있음을 의미합니다. 예외를 추가하려면 **사전 요구 사항** 단계를 수행해야 합니다.
    > :::image type="content" source="./media/create-catalog-portal/purview-conflict-error.png" alt-text="Purview 충돌 오류 메시지":::

1. **Purview 계정 시작** 을 선택합니다.

   :::image type="content" source="./media/use-purview-studio/launch-from-portal.png" alt-text="Azure Purview 계정 카탈로그를 시작하기 위한 선택 항목의 스크린샷":::

## <a name="add-a-security-principal-to-a-data-plane-role"></a>데이터 평면 역할에 보안 주체 추가

사용자 또는 팀에서 Azure Purview를 사용하려면 먼저 하나 이상의 보안 주체를 미리 정의된 데이터 평면 역할 중 하나(**Purview 데이터 읽기 권한자**, **Purview 데이터 큐레이터** 또는 **Purview 데이터 원본 관리자**)에 추가해야 합니다. Azure Purview Data Catalog 권한에 대한 자세한 내용은 [카탈로그 권한](catalog-permissions.md)을 참조하세요.

보안 주체를 Azure Purview 계정의 **Purview 데이터 큐레이터** 데이터 평면 역할에 추가하려면 다음을 수행합니다.

1. Azure Portal에서 [**Purview 계정**](https://aka.ms/purviewportal) 페이지로 이동합니다.

1. 수정하려는 Azure Purview 계정을 선택합니다.

1. **Purview 계정** 페이지에서 **액세스 제어(IAM)** 탭을 선택합니다.

1. **+ 추가** 를 클릭합니다.

추가를 클릭할 때 두 가지 선택 항목이 모두 사용 안 함으로 표시되면 다른 사용자를 Azure Purview 계정의 데이터 평면 역할에 추가할 수 있는 적절한 권한이 없음을 의미합니다. Azure Purview 계정에서 소유자, 사용자 액세스 관리자 또는 역할 할당 기관과 관련된 다른 사용자를 찾아야 합니다. **역할 할당** 탭을 선택한 다음, 아래로 스크롤하여 소유자 또는 사용자 액세스 관리자를 찾고 해당 사용자에게 연락하여 적절한 사용자를 찾을 수 있습니다.

1. **역할 할당 추가** 를 선택합니다.

1. **Purview 데이터 큐레이터 역할** 또는 **Purview 데이터 원본 관리자 역할** 의 역할 유형은 서비스 주체의 용도에 따라 달라집니다(자세한 내용은 [카탈로그 권한](catalog-permissions.md) 및 [Azure Active Directory의 애플리케이션 및 서비스 주체 개체](../active-directory/develop/app-objects-and-service-principals.md) 참조).

1. **다음에 대한 액세스 할당** 에 대해 기본값(**사용자, 그룹 또는 서비스 주체**)을 그대로 둡니다.

1. **선택** 에 대해 할당하려는 사용자, Azure Active Directory 그룹 또는 서비스 주체의 이름을 입력한 다음, 결과 창에서 해당 이름을 클릭합니다.

1. **Save** 를 클릭합니다.

## <a name="clean-up-resources"></a>리소스 정리

이 Azure Purview 계정이 더 이상 필요하지 않은 경우 다음 단계를 사용하여 삭제합니다.

1. Azure Portal에서 **Purview 계정** 페이지로 이동합니다.

2. 이 빠른 시작의 시작 부분에서 만든 Azure Purview 계정을 선택합니다. **삭제** 를 선택하고, 계정 이름을 입력한 다음, **삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure Purview 계정을 만드는 방법을 알아보았습니다.

다음 문서로 계속 진행하여 사용자가 Azure Purview 계정에 액세스할 수 있도록 허용하는 방법을 알아보세요. 

> [!div class="nextstepaction"]
> [Azure Purview 계정에 사용자 추가](catalog-permissions.md)