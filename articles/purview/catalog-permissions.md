---
title: 카탈로그 권한 (미리 보기)
description: 이 문서에서는 Azure Purview에서 역할 기반 액세스 제어(RBAC)를 구성하는 방법에 대한 개요를 제공합니다.
author: yarong
ms.author: yarong
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 10/20/2020
ms.openlocfilehash: b351be1e7212dc9923f701599dd951a73254afe0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98610372"
---
# <a name="role-based-access-control-in-azure-purviews-data-plane"></a>Azure Purview의 데이터 평면에서 역할 기반 액세스 제어

이 문서에서는 Azure Purview의 [데이터 평면](../azure-resource-manager/management/control-plane-and-data-plane.md#data-plane)에서 역할 기반 액세스 제어(RBAC)를 구현하는 방법을 설명합니다.

> [!IMPORTANT]
> Purview 계정을 만든 사용자에게는 그렇거나 혹은 그렇지 않을 수 있는 데이터 평면 역할에 관계없이 모든 데이터 평면 권한이 자동으로 부여됩니다. 다른 모든 사용자가 Azure Purview에서 모든 작업을 수행하려면 미리 정의된 데이터 평면 역할 중 하나 이상에 있어야 합니다.

## <a name="azure-purviews-pre-defined-data-plane-roles"></a>Azure Purview의 미리 정의된 데이터 평면 역할

Azure Purview는 Azure Purview에서 액세스를 제어하는 데 사용할 수 있는 미리 정의된 데이터 평면 역할 집합을 제공합니다. 이러한 역할은 다음과 같습니다.

* **Purview 데이터 판독기 역할** - Purview 포털에 대한 액세스 권한을 가지며, 검사 바인딩을 제외하고 Azure Purview의 모든 콘텐츠를 읽을 수 있습니다.
* **Purview 데이터 큐레이터 역할** - Purview 포털에 액세스할 수 있으며 검사 바인딩을 제외한 Azure Purview의 모든 콘텐츠를 읽을 수 있고, 자산에 대한 정보를 편집할 수 있으며, 분류 정의 및 용어를 편집할 수 있고, 자산에 분류 및 용어를 적용할 수 있습니다.
* **Purview 데이터 원본 관리자 역할** - Purview Portal에 대한 액세스 권한이 없으며(사용자는 데이터 판독기 또는 데이터 큐레이터 역할에 있어야 함), Azure Purview에 대한 데이터 검사의 모든 측면을 관리할 수 있지만, 검사에 관련된 것 외에 Azure Purview의 콘텐츠에 대한 읽기 또는 쓰기 액세스 권한은 없습니다.

## <a name="understanding-how-to-use-azure-purviews-data-plane-roles"></a>Azure Purview의 데이터 평면 역할을 사용하는 방법 이해

Azure Purview 계정이 만들어지면, 작성자는 Azure Purview 데이터 큐레이터 및 데이터 원본 관리자 역할 모두에 있는 것처럼 간주됩니다. 그러나 계정 작성자는 역할 저장소의 이러한 역할에 할당되지 않습니다. Azure Purview는 보안 주체가 계정의 작성자임을 인식하고 ID를 기반으로 이러한 기능을 확장합니다.

다른 모든 사용자는 이러한 역할 중 하나 이상에 배치된 경우에만 Azure Purview 계정을 사용할 수 있습니다. 즉, Azure Purview 계정을 만들 때, 하나 이상의 미리 정의된 역할에 배치될 때까지 작성자만 계정에 액세스하거나 해당 API를 사용할 수 있습니다.

Purview 데이터 원본 관리자 역할에는 지원되는 두 가지 시나리오가 있습니다. 첫 번째 시나리오는 이미 Purview 데이터 판독기 또는 Purview 데이터 큐레이터인 사용자를 위한 것으로, 검사를 만들 수도 있어야 합니다. 이러한 사용자는 최소한 Purview 데이터 읽기 권한자 또는 Purview 데이터 큐레이터 중 하나인 두 역할에 속해야 하며, Purview 데이터 원본 관리자 역할에도 배치되어야 합니다.

Purview 데이터 원본 관리자의 다른 시나리오는 검색을 설정하고 모니터링할 수 있어야 하지만 카탈로그의 데이터에 액세스할 수 없어야 하는 프로그래매틱 프로세스(예: 서비스 주체)에 대한 것입니다.

이 시나리오는 다른 두 역할에 배치하지 않고 Purview 데이터 원본 관리자 역할에 서비스 주체를 배치하여 구현할 수 있습니다. 보안 주체는 Purview 포털에 액세스할 수 없지만 괜찮습니다. 프로그래밍 방식 보안 주체이며 API를 통해서만 통신하기 때문입니다.

## <a name="putting-users-into-roles"></a>역할에 사용자 배치

따라서 Azure Purview 계정을 만든 후 첫 번째 비즈니스 순서는 이러한 역할에 사람을 할당하는 것입니다.

역할 할당은 [Azure의 RBAC](../role-based-access-control/overview.md)를 통해 관리됩니다.

Azure에서 두 개의 기본 제공 컨트롤 플레인 역할만 사용자 역할을 할당할 수 있고, 이러한 역할은 소유자 또는 사용자 액세스 관리자입니다. 따라서 Azure Purview에 대한 이러한 역할에 사용자를 배치하려면 소유자 또는 사용자 액세스 관리자인 사람을 찾거나 스스로가 되어야 합니다.

### <a name="an-example-of-assigning-someone-to-a-role"></a>사용자를 역할에 할당하는 예

1. https://portal.azure.com 로 이동하여 Azure Purview 계정으로 이동합니다.
1. 왼쪽에서 "액세스 제어 (IAM)"을 클릭합니다.
1. 그런 다음 [여기](../role-based-access-control/quickstart-assign-role-user-portal.md#create-a-resource-group) 에 제공된 일반적인 지침을 따릅니다.

## <a name="role-definitions-and-actions"></a>역할 정의 및 작업

역할은 동작의 컬렉션으로 정의됩니다. 역할을 정의하는 방법에 대한 자세한 내용은 [여기](../role-based-access-control/role-definitions.md)를 참조하세요. Azure Purview 역할에 대한 역할 정의는 [여기](../role-based-access-control/built-in-roles.md)를 참조하세요.

## <a name="getting-added-to-a-data-plane-role-in-an-azure-purview-account"></a>Azure Purview 계정의 데이터 평면 역할에 추가

Azure Purview 계정에 대한 액세스 권한을 부여하여 해당 스튜디오를 사용하거나 API를 호출할 수 있도록 하려면 Azure Purview 데이터 평면 역할에 추가되어야 합니다. 이 작업을 수행할 수 있는 유일한 사용자는 Azure Purview 계정에 대한 소유자 또는 사용자 액세스 관리자입니다. 대부분의 사용자는 다음 단계를 통해 액세스 권한을 부여할 수 있는 적절한 사용자를 찾을 수 있는 로컬 관리자를 찾을 수 있습니다.

회사의 [Azure Portal](https://portal.azure.com)에 대한 액세스 권한이 있는 사용자는 가입하려는 특정 Azure Purview 계정을 조회할 수 있으며, 해당 "액세스 제어 (IAM)" 탭을 클릭하여 소유자 또는 사용자 액세스 관리자(UAAs)가 누구인지 확인할 수 있습니다. 그러나 일부 경우에는 Azure Active Directory 그룹 또는 서비스 주체를 소유자 또는 UAAs로 사용할 수 있으며, 이 경우에는 직접 연락하지 못할 수 있습니다. 대신, 도움을 주는 관리자를 찾아야 합니다.

## <a name="who-should-be-assigned-to-what-role"></a>누가 어떤 역할에 할당되어야 하나요?

|사용자 시나리오|적절한 역할|
|-------------|-----------------|
|그저 자산을 찾을 수 있어야 하고, 아무것도 편집하지 않겠습니다.|Purview 데이터 읽기 권한자 역할|
|자산에 대한 정보를 편집하고, 분류를 입력하고, 용어를 용어집 항목과 연결 등을 해야 합니다.|Purview 데이터 큐레이터 역할|
|용어집을 편집하거나 새 분류 정의를 설정해야 합니다.|Purview 데이터 큐레이터 역할|
|애플리케이션의 서비스 주체는 Azure Purview에 데이터를 푸시해야 합니다.|Purview 데이터 큐레이터 역할|
|Purview Studio를 통해 검사를 설정해야 합니다.|Purview 데이터 원본 관리자 역할에 Purview 데이터 읽기 권한자 역할 또는 Purview 데이터 관리자 역할 중 하나 이상 추가|
|프로그래매틱 ID가 카탈로그의 정보에 액세스하도록 허용하지 않고 Azure Purview에서 검사를 설정하고 모니터링하려면 서비스 주체 또는 기타 프로그래매틱 ID를 사용하도록 설정해야 합니다. |Purview 데이터 원본 관리자 역할|
|Azure Purview에서 사용자를 역할에 배치해야 합니다. | 소유자 또는 사용자 액세스 관리자 |

역할에 보안 주체를 추가하는 방법에 대한 자세한 내용은 [빠른 시작: Azure Purview 계정 만들기](create-catalog-portal.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [데이터 인사이트](concept-insights.md)
