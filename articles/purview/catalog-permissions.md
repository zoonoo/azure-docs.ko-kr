---
title: 카탈로그 권한 (미리 보기)
description: 이 문서에서는 Azure 부서의 범위에서 RBAC (Role-Based Access Control)를 구성 하는 방법에 대 한 개요를 제공 합니다.
author: yarong
ms.author: yarong
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 10/20/2020
ms.openlocfilehash: b351be1e7212dc9923f701599dd951a73254afe0
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/20/2021
ms.locfileid: "98610372"
---
# <a name="role-based-access-control-in-azure-purviews-data-plane"></a>Azure 부서의 범위의 데이터 평면에서 역할 기반 액세스 제어

이 문서에서는 Azure 부서의 범위의 [데이터 평면](../azure-resource-manager/management/control-plane-and-data-plane.md#data-plane)에서 RBAC (Role-Based Access Control)를 구현 하는 방법을 설명 합니다.

> [!IMPORTANT]
> 부서의 범위 계정을 만든 사용자에 게는 그렇지 않을 수 있는 데이터 평면 역할에 관계 없이 모든 데이터 평면 권한이 자동으로 부여 됩니다. 다른 모든 사용자가 Azure 부서의 범위에서 모든 작업을 수행 하려면 미리 정의 된 데이터 평면 역할 중 하나 이상에 있어야 합니다.

## <a name="azure-purviews-pre-defined-data-plane-roles"></a>Azure 부서의 범위의 미리 정의 된 데이터 평면 역할

Azure 부서의 범위는 Azure 부서의 범위에서 어떤 항목에 액세스할 수 있는지를 제어 하는 데 사용할 수 있는 미리 정의 된 데이터 평면 역할 집합을 정의 합니다. 이러한 역할은 다음과 같습니다.

* **부서의 범위 데이터 판독기 역할** -부서의 범위 포털에 대 한 액세스 권한을 가지 며, 검색 바인딩을 제외 하 고 Azure 부서의 범위의 모든 콘텐츠를 읽을 수 있습니다.
* **부서의 범위 Data 큐레이터 Role** -부서의 범위 포털에 대 한 액세스 권한을 가지 며, 검색 바인딩을 제외 하 고 Azure 부서의 범위의 모든 콘텐츠를 읽을 수 있으며, 자산에 대 한 정보를 편집 하 고, 분류 정의 및 용어집 용어를 편집 하 고, 분류 및 용어집 용어를 자산에 적용할 수 있습니다.
* **부서의 범위 데이터 원본 관리자 역할** -부서의 범위 포털에 대 한 액세스 권한이 없으며 (사용자는 데이터 판독기 또는 데이터 큐레이터 역할에 있어야 함), 데이터를 azure 부서의 범위로 검색 하는 모든 측면을 관리할 수 있지만, azure 부서의 범위의 콘텐츠에 대 한 읽기 또는 쓰기 액세스 권한이 없는 azure에 대 한 읽기 또는 쓰기 권한이 없습니다.

## <a name="understanding-how-to-use-azure-purviews-data-plane-roles"></a>Azure 부서의 범위의 데이터 평면 역할을 사용 하는 방법 이해

Azure 부서의 범위 계정을 만들면 작성자가 부서의 범위 Data 큐레이터 및 부서의 범위 데이터 원본 관리자 역할 모두에 있는 것 처럼 처리 됩니다. 그러나 계정 작성자는 역할 저장소의 이러한 역할에 할당되지 않습니다. Azure Purview는 보안 주체가 계정의 작성자임을 인식하고 ID를 기반으로 이러한 기능을 확장합니다.

다른 모든 사용자는 이러한 역할 중 하나 이상에 배치된 경우에만 Azure Purview 계정을 사용할 수 있습니다. 즉, Azure 부서의 범위 계정이 생성 될 때 작성자가 계정에 액세스 하거나 이전에 정의 된 역할 중 하나 이상에 배치 될 때까지 해당 Api를 사용할 수 있습니다.

부서의 범위 데이터 원본 관리자 역할에는 지원 되는 두 가지 시나리오가 있습니다. 첫 번째 시나리오는 데이터 판독기를 이미 부서의 범위 하거나 검색을 만들 수 있는 데이터를 부서의 범위 하는 사용자를 위한 것입니다. 이러한 사용자는 하나 이상의 부서의 범위 데이터 판독기 또는 부서의 범위 Data 큐레이터 및 부서의 범위 데이터 원본 관리자 역할에 배치 해야 합니다.

부서의 범위 데이터 원본 관리자에 대 한 다른 시나리오는 검색을 설정 하 고 모니터링 해야 하지만 카탈로그의 데이터에 액세스할 수 있어야 하는 서비스 사용자와 같은 프로그래밍 프로세스를 위한 것입니다.

이 시나리오는 다른 두 역할에 배치 하지 않고 부서의 범위 데이터 원본 관리자 역할에 서비스 주체를 배치 하 여 구현할 수 있습니다. 보안 주체는 부서의 범위 포털에 대 한 액세스 권한이 없지만 o.k. 는 프로그래밍 방식 이므로 Api를 통해서만 통신 합니다.

## <a name="putting-users-into-roles"></a>사용자를 역할에 배치

따라서 Azure 부서의 범위 계정을 만든 후 비즈니스의 첫 번째 순서는 사용자를 이러한 역할에 할당 하는 것입니다.

역할 할당은 [Azure의 RBAC](../role-based-access-control/overview.md)를 통해 관리 됩니다.

Azure에서 두 개의 기본 제공 제어 평면 역할만 사용자 역할을 할당할 수 있습니다. 소유자 또는 사용자 액세스 관리자입니다. 따라서 사용자에 게 Azure 부서의 범위에 대 한 이러한 역할을 하려면 소유자 또는 사용자 액세스 관리자 인 사용자를 찾아야 하며 잊기 해야 합니다.

### <a name="an-example-of-assigning-someone-to-a-role"></a>사용자를 역할에 할당 하는 예

1. 로 이동 하 https://portal.azure.com 여 Azure 부서의 범위 계정으로 이동 합니다.
1. 왼쪽에서 "Access control (IAM)"을 클릭 합니다.
1. 그런 다음 [여기](../role-based-access-control/quickstart-assign-role-user-portal.md#create-a-resource-group) 에 제공 된 일반 지침을 따릅니다.

## <a name="role-definitions-and-actions"></a>역할 정의 및 작업

역할은 동작의 컬렉션으로 정의 됩니다. 역할을 정의 하는 방법에 대 한 자세한 내용은 [여기](../role-based-access-control/role-definitions.md) 를 참조 하세요. Azure 부서의 범위의 역할에 대 한 역할 정의는 [여기](../role-based-access-control/built-in-roles.md) 를 참조 하세요.

## <a name="getting-added-to-a-data-plane-role-in-an-azure-purview-account"></a>Azure 부서의 범위 계정의 데이터 평면 역할에 추가 하는 중

Azure 부서의 범위 계정에 대 한 액세스 권한을 부여 하 여 해당 스튜디오를 사용 하거나 Api를 호출할 수 있도록 하려면 Azure 부서의 범위 데이터 평면 역할에 추가 해야 합니다. 이 작업을 수행할 수 있는 유일한 사용자는 Azure 부서의 범위 계정에 대 한 소유자 또는 사용자 액세스 관리자입니다. 대부분의 사용자는 다음 단계를 통해 액세스 권한을 부여할 수 있는 적절 한 사용자를 찾을 수 있는 로컬 관리자를 찾을 수 있습니다.

회사의 [Azure Portal](https://portal.azure.com) 에 대 한 액세스 권한이 있는 사용자는 가입 하려는 특정 Azure 부서의 범위 계정을 조회할 수 있으며, 해당 "액세스 제어 (IAM)" 탭을 클릭 하 여 소유자 또는 Uaas (사용자 액세스 관리자)가 누구 인지 확인할 수 있습니다. 그러나 일부 Azure Active Directory 경우에는 그룹 또는 서비스 주체를 소유자 또는 UAAs로 사용할 수 있습니다 .이 경우에는 직접 연락 하지 못할 수 있습니다. 대신, 도움을 주는 관리자를 찾아야 합니다.

## <a name="who-should-be-assigned-to-what-role"></a>어떤 역할이 어떤 역할에 할당 되어야 하나요?

|사용자 시나리오|적절 한 역할|
|-------------|-----------------|
|자산을 찾을 수 있어야 합니다. 아무것도 편집 하지 않겠습니다.|부서의 범위 데이터 판독기 역할|
|자산에 대 한 정보를 편집 하 고, 분류를 입력 하 고, 용어를 용어집 항목과 연결 해야 합니다.|부서의 범위 Data 큐레이터 역할|
|용어집을 편집 하거나 새 분류 정의를 설정 해야 합니다.|부서의 범위 Data 큐레이터 역할|
|응용 프로그램의 서비스 주체는 Azure 부서의 범위에 데이터를 푸시 해야 합니다.|부서의 범위 Data 큐레이터 역할|
|부서의 범위 Studio를 통해 검색을 설정 해야 합니다.|부서의 범위 데이터 원본 관리자 역할 및 부서의 범위 데이터 판독기 역할 또는 부서의 범위 Data 큐레이터 Role 중 하나 이상|
|프로그래밍 id를 사용 하 여 카탈로그 정보에 액세스 하지 않고 Azure 부서의 범위에서 검색을 설정 하 고 모니터링 하려면 서비스 사용자 또는 다른 프로그래밍 id를 사용 하도록 설정 해야 합니다. |부서의 범위 데이터 원본 관리자 역할|
|Azure 부서의 범위에서 사용자를 역할에 추가 해야 합니다. | 소유자 또는 사용자 액세스 관리자 |

역할에 보안 주체를 추가 하는 방법에 대 한 자세한 내용은 빠른 시작 [: Azure 부서의 범위 계정 만들기](create-catalog-portal.md) 를 참조 하세요.

## <a name="next-steps"></a>다음 단계

* [데이터 인사이트](concept-insights.md)
