---
title: Azure Cosmos DB의 역할 기반 액세스 제어
description: Azure Cosmos DB가 RBAC(활성 디렉터리 통합)를 통해 데이터베이스 보호를 제공하는 방법을 알아봅니다.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: mjbrown
ms.openlocfilehash: 0c7332a42751b35b6ad8ec3f88afb7bc78cc85e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75445094"
---
# <a name="role-based-access-control-in-azure-cosmos-db"></a>Azure Cosmos DB의 역할 기반 액세스 제어

Azure Cosmos DB는 Azure Cosmos DB의 일반적인 관리 시나리오에 대해 기본 제공 역할 기반 액세스 제어(RBAC)를 제공합니다. Azure Active Directory에 프로필이 있는 개인은 이러한 RBAC 역할을 사용자, 그룹, 서비스 주체 또는 관리되는 ID에 할당하여 Azure Cosmos DB 리소스의 리소스 및 작업에 대한 액세스 권한을 부여하거나 거부할 수 있습니다. 역할 할당은 Azure Cosmos 계정, 데이터베이스, 컨테이너 및 제공(처리량)에 대한 액세스를 포함하는 제어 평면 액세스로만 범위가 지정됩니다.

## <a name="built-in-roles"></a>기본 제공 역할

다음은 Azure Cosmos DB에서 지원하는 기본 제공 역할입니다.

|**기본 제공 역할**  |**설명**  |
|---------|---------|
|[DocumentDB 계정 기여자](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)|Azure Cosmos DB 계정을 관리할 수 있습니다.|
|[코스모스 DB 계정 리더](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)|Azure Cosmos DB 계정 데이터를 읽을 수 있음.|
|[코스모스 백업 연산자](../role-based-access-control/built-in-roles.md#cosmosbackupoperator)|Azure Cosmos 데이터베이스 또는 컨테이너에 대한 복원 요청을 제출할 수 있습니다.|
|[코스모스 DB 오퍼레이터](../role-based-access-control/built-in-roles.md#cosmos-db-operator)|Azure Cosmos 계정, 데이터베이스 및 컨테이너를 프로비전할 수 있지만 데이터에 액세스하는 데 필요한 키에 액세스할 수는 없습니다.|

> [!IMPORTANT]
> Azure Cosmos DB의 RBAC 지원은 제어 평면 작업에만 적용됩니다. 데이터 평면 작업은 마스터 키 또는 리소스 토큰을 사용하여 보호됩니다. 자세한 내용은 [Azure Cosmos DB의 데이터에 대한 보안 액세스를](secure-access-to-data.md) 참조하십시오.

## <a name="identity-and-access-management-iam"></a>ID 및 액세스 관리(IAM)

Azure 포털의 **IAM(액세스 제어)** 창은 Azure Cosmos 리소스에서 역할 기반 액세스 제어를 구성하는 데 사용됩니다. 역할은 Active Directory의 사용자, 그룹, 서비스 주체 및 관리되는 ID에 적용됩니다. 개인 및 그룹에 기본 제공 역할 또는 사용자 지정 역할을 사용할 수 있습니다. 다음 스크린샷은 Azure 포털에서 IAM(액세스 제어)을 사용하여 RBAC(활성 디렉터리 통합)를 보여 주며 다음과 같은 화면입니다.

![Azure Portal에서 액세스 제어(IAM) - 데이터베이스 보안 설명](./media/role-based-access-control/database-security-identity-access-management-rbac.png)

## <a name="custom-roles"></a>사용자 지정 역할

기본 제공 역할 외에도 사용자는 Azure에서 [사용자 지정 역할을](../role-based-access-control/custom-roles.md) 만들고 Active Directory 테넌트 내의 모든 구독에서 서비스 주체에 이러한 역할을 적용할 수도 있습니다. 사용자 지정 역할은 사용자에게 사용자 지정 리소스 공급자 작업 집합을 사용하여 RBAC 역할 정의를 만드는 방법을 제공합니다. Azure Cosmos DB에 대한 사용자 지정 역할을 빌드하는 데 사용할 수 있는 작업을 알아보려면 [Azure Cosmos DB 리소스 공급자 작업](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)

## <a name="preventing-changes-from-cosmos-sdk"></a>코스모스 SDK의 변경 방지

Cosmos 리소스 공급자는 계정 키(예: Cosmos SDK를 통해 연결하는 응용 프로그램)를 통해 연결하는 모든 클라이언트의 Cosmos 계정, 데이터베이스, 컨테이너 및 처리량을 포함한 리소스변경을 방지하기 위해 잠글 수 있습니다. 설정하면 적절한 RBAC 역할 및 자격 증명을 가진 사용자로부터 리소스를 변경해야 합니다. 이 기능은 Cosmos 리소스 공급자의 속성 값으로 `disableKeyBasedMetadataWriteAccess` 설정됩니다. 이 속성 설정이 있는 Azure 리소스 관리자 템플릿의 예는 다음과 같습니다.

```json
{
    {
      "type": "Microsoft.DocumentDB/databaseAccounts",
      "name": "[variables('accountName')]",
      "apiVersion": "2019-08-01",
      "location": "[parameters('location')]",
      "kind": "GlobalDocumentDB",
      "properties": {
        "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
        "locations": "[variables('locations')]",
        "databaseAccountOfferType": "Standard",
        "disableKeyBasedMetadataWriteAccess": true
        }
    }
}
```

## <a name="next-steps"></a>다음 단계

- [Azure 리소스에 대한 역할 기반 액세스 제어(RBAC)란 무엇입니까?](../role-based-access-control/overview.md)
- [Azure 리소스에 대한 사용자 지정 역할](../role-based-access-control/custom-roles.md)
- [Azure 코스모스 DB 리소스 공급자 작업](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)
