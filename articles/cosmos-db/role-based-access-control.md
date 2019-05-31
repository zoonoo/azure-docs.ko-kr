---
title: Azure Active Directory 통합을 사용 하 여 Azure Cosmos DB의 역할 기반 액세스 제어
description: Azure Cosmos DB에서 Active directory 통합 (RBAC)를 사용 하 여 데이터베이스 보호를 제공 하는 방법에 대해 알아봅니다.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: mjbrown
ms.openlocfilehash: 971d2ec96906a3309963495dd1af5d293a71f265
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66243509"
---
# <a name="role-based-access-control-in-azure-cosmos-db"></a>Azure Cosmos DB의 역할 기반 액세스 제어

Azure Cosmos DB는 Azure Cosmos DB의 일반적인 관리 시나리오에 대 한 기본 제공 역할 기반 액세스 제어 (RBAC)를 제공합니다. 프로필을 Azure Active Directory에서 권한이 있는 사용자는 개별 사용자, 그룹, 서비스 주체에 RBAC 역할을 할당할 수 또는 id를 부여 하거나 거부 리소스 및 Azure Cosmos DB 리소스에 대 한 작업에 대 한 액세스를 관리 합니다. 제어 평면 액세스를 제공 (처리량) 및 Azure Cosmos 계정, 데이터베이스, 컨테이너에 대 한 액세스를 포함 하는 역할 할당 범위가 지정 됩니다.

## <a name="built-in-roles"></a>기본 제공 역할

다음은 Azure Cosmos DB에서 지 원하는 기본 제공 역할입니다.

|**기본 제공 역할**  |**설명**  |
|---------|---------|
|[DocumentDB 계정 기여자](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)   | Azure Cosmos DB 계정을 관리할 수 있습니다.  |
|[Cosmos DB 계정 독자](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)  | Azure Cosmos DB 계정 데이터를 읽을 수 있음.        |
|[Cosmos 백업 운영자](../role-based-access-control/built-in-roles.md#cosmosbackupoperator)     |  Azure Cosmos 데이터베이스 또는 컨테이너에 대 한 복원 요청을 제출할 수 있습니다.       |
|[Cosmos DB 연산자](../role-based-access-control/built-in-roles.md#cosmos-db-operator)  | Azure Cosmos 계정, 데이터베이스 및 컨테이너에 프로 비전 할 수 있지만 데이터에 액세스 하는 데 필요한 키를 액세스할 수 없습니다.         |

> [!IMPORTANT]
> Azure Cosmos DB의 RBAC 지원이 제어 평면 작업에만 적용 됩니다. 데이터 평면 작업 마스터 키 또는 리소스 토큰을 사용 하 여 보호 됩니다. 자세한 내용은를 참조 하세요. [Azure Cosmos DB에서 데이터에 대 한 액세스를 보호 합니다.](secure-access-to-data.md)

## <a name="identity-and-access-management-iam"></a>ID 및 액세스 관리(Identity and Access Managmenet:  IAM)

합니다 **액세스 제어 (IAM)** 창 Azure portal에서 Azure Cosmos 리소스에 역할 기반 access control을 구성 하려면 사용 됩니다. 역할은 사용자, 그룹, 서비스 주체 및 Active Directory의 관리 되는 id에 적용 됩니다. 개인 및 그룹에 대 한 기본 제공 역할 또는 사용자 지정 역할을 사용할 수 있습니다. 다음 스크린샷은 Azure portal에서 액세스 제어 (IAM)를 사용 하 여 Active Directory 통합 (RBAC):

![Azure Portal에서 액세스 제어(IAM) - 데이터베이스 보안 설명](./media/role-based-access-control/database-security-identity-access-management-rbac.png)

## <a name="custom-roles"></a>사용자 지정 역할

기본 제공 역할을 하는 것 외에도 사용자를 만들 수도 [사용자 지정 역할](../role-based-access-control/custom-roles.md) Azure에서 Active Directory 테 넌 트 내에서 모든 구독에서 서비스 주체에 이러한 역할을 적용 합니다. 사용자 지정 역할 공급자 작업 리소스의 사용자 지정 집합을 사용 하 여 RBAC 역할 정의 만드는 방법을 사용자를 제공 합니다. Azure Cosmos DB 참조에 대 한 사용자 지정 역할을 구축 하기 위한 사용할 수 있는 작업에 알아보려면 [Azure Cosmos DB 리소스 공급자 작업](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)

## <a name="next-steps"></a>다음 단계

- [Azure 리소스에 대 한 역할 기반 액세스 제어 (RBAC) 란](../role-based-access-control/overview.md)
- [Azure 리소스에 대한 사용자 지정 역할](../role-based-access-control/custom-roles.md)
- [Azure Cosmos DB 리소스 공급자 작업](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)
