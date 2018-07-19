---
title: Azure에서 거버넌스 | Microsoft Docs
description: 응용 프로그램 또는 엔터프라이즈의 요구를 충족시키기 위해 강화 및 축소할 수 있는 클라우드 기반 컴퓨팅 서비스에 대해 알아봅니다.
services: security
documentationcenter: na
author: UnifyCloud
manager: mbaldwin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/01/2017
ms.author: TomSh
ms.openlocfilehash: 579e900ee6616af8fd197e501364acd8e18d3e37
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38970526"
---
# <a name="governance-in-azure"></a>Azure에서 거버넌스

Azure는 많은 보안 옵션과 이를 제어하는 기능을 제공하므로 조직의 고유한 배포 요구 사항을 충족할 수 있습니다.

Azure 클라우드 거버넌스는 클라우드 컴퓨팅의 계획, 아키텍처, 취득, 배포, 작업 및 관리에 포함된 정책, 기준 및 의사 결정 프로세스를 참조합니다. Azure 클라우드 거버넌스는 Azure Platform 사용량에 대해 검토하여 조직에 알릴 수 있는 통합된 감사 및 컨설팅 접근 방식을 제공합니다. 

Azure 클라우드 거버넌스에 대한 계획을 만들려면 현재 상태의 사람, 프로세스 및 기술을 자세히 확인해야 합니다. 그러면, IT가 비즈니스 요구 사항을 일관성 있게 지원하면서 사용자에게 Azure의 기능을 사용하는 유연성을 제공하는 프레임워크를 빌드할 수 있습니다.

## <a name="implementation-of-policies-processes-and-standards"></a>정책, 프로세스 및 표준의 구현 

Azure에서 정보 보안 정책 및 작업 연속성의 구현을 감독하도록 관리에서 역할과 책임을 설정했습니다. Azure 관리는 각 팀(타사 포함) 내에 보안 및 연속성 사례를 감독합니다. 또한 보안 정책, 프로세스 및 표준 규정 준수를 용이하게 합니다.

### <a name="account-provisioning"></a>계정 프로비전

계정 계층 정의는 회사 내에서 Azure 서비스를 구성하고 사용하기 위한 주요 단계입니다. 핵심 거버넌스 구조입니다. EA(기업계약)을 보유한 고객은 환경을 부서, 계정, 구독으로 더욱 세분화할 수 있습니다.

![계정 프로비전](./media/governance-in-azure/security-governance-in-azure-fig1.png)

기업계약이 없는 경우 구독 수준에서 [Azure 태그](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)를 사용하여 계층을 정의합니다. Azure 구독은 모든 리소스를 포함하는 기본 단위입니다. 코어 수, 리소스 등 Azure 내에서 여러 가지 한도도 정의합니다. 구독에는 [리소스 그룹](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)이 포함될 수 있고 리소스 그룹에는 리소스가 포함할 수 있습니다. [RBAC(역할 기반 액세스 제어)](https://docs.microsoft.com/azure/role-based-access-control/overview) 원칙이 이러한 세 가지 수준에 적용됩니다.

모든 기업은 서로 다릅니다. 엔터프라이즈 외 기업의 경우 Azure 태그를 사용하는 계층 구조를 통해 Azure가 구성되는 방식에 유연성을 허용할 수 있습니다. Azure에서 리소스를 배포하기 전에 계층을 모델링하고 청구, 리소스 액세스 및 복잡성에 대한 영향을 이해해야 합니다.

### <a name="subscription-controls"></a>구독 제어

구독은 리소스 사용량의 보고 및 청구 방식을 결정합니다. 별도의 청구 및 지불을 위해 구독을 설정할 수 있습니다. 하나의 Azure 계정에 여러 구독이 포함될 수 있습니다. 구독은 회사에서 여러 부서의 Azure 리소스 사용량을 결정하는 데 사용할 수 있습니다.

예를 들어 회사에 IT, HR 및 마케팅 부서가 있으며 이러한 부서는 실행 중인 서로 다른 프로젝트입니다. 회사는 가상 머신처럼 Azure 리소스의 부서별 사용량에 대한 청구를 기반으로 할 수 있습니다. 그런 다음, 회사는 각 부서의 재정을 제어할 수 있습니다.

Azure 구독은 세 개의 매개 변수를 설정합니다.

- 고유한 구독자 ID

- 청구 위치

- 사용 가능한 리소스 집합

개별 사용자의 경우 해당 매개 변수는 하나의 Microsoft 계정 ID, 신용 카드 번호 및 전체 Azure 서비스 모음을 포함합니다. Microsoft에서 구독 유형에 따라 사용을 제한할 수 있습니다.

Azure 등록 계층은 기업계약 내에 서비스 구성 방법을 정의합니다. 기업계약 포털을 통해 고객은 조직의 요구 사항에 맞게 사용자 지정할 수 있는 유연한 계층 구조를 기반으로 기업계약과 연결된 Azure 리소스에 대한 액세스를 나눌 수 있습니다. 연결된 청구 및 리소스 액세스를 계산할 수 있도록 계층 패턴이 조직의 관리 및 지리적 구조와 일치해야 합니다.

세 가지 개략적인 패턴은 기능, 비즈니스 단위 및 지리적 패턴입니다. 부서는 계정 그룹화에 대한 관리 구문입니다. 각 부서 내에서 계정은 할당된 구독일 수 있으며 Azure에서 몇 가지 주요 제한(예: VM 수, 저장소 계정) 및 청구에 대한 사일로를 만듭니다.

![구독 제어](./media/governance-in-azure/security-governance-in-azure-fig2.png)


기업계약이 있는 조직의 경우 Azure 구독은 4-수준 계층 구조를 수행합니다.

1. 기업 등록계약 관리자

2. 부서 관리자

3. 계정 소유자

4. 서비스 관리자

이 계층 구조는 다음을 제어합니다.

- 청구 관계

- 계정 관리

- RBAC를 통해 리소스 액세스

- 경계:

  - 사용량 및 청구(제품 번호 기준 요금 카드)

  - 제한

  - 가상 네트워크

- Azure AD(Azure Active Directory)에 연결. 하나의 Azure AD 인스턴스를 여러 구독에 연결할 수 있습니다.

- 기업 등록계약 계정과 연결

### <a name="role-based-access-control"></a>역할 기반 액세스 제어

[RBAC](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)를 사용하면 Azure의 리소스에 대한 세부적인 액세스 관리가 가능합니다. RBAC를 사용하여 사용자가 해당 작업을 수행하는 데 필요한 액세스 만큼 권한을 부여할 수 있습니다. 회사는 직원에게 정확히 필요한 권한을 제공하는 데 중점을 두어야 합니다. 권한이 너무 많으면 공격자에게 계정이 노출되고, 권한이 너무 적으면 직원이 업무를 효율적으로 수행할 수 없습니다. 

Azure 구독 또는 리소스에서 모든 사람에게 무제한 권한을 제공하는 대신 특정 작업만 허용할 수 있습니다. 예를 들어 RBAC를 사용하여 한 명의 직원은 구독의 가상 머신을 관리하도록 하고, 다른 직원은 동일한 구독 내에서 SQL 데이터베이스를 관리하도록 할 수 있습니다.

액세스 권한을 부여하기 위해 특정 범위에서 사용, 그룹 또는 응용 프로그램에 역할을 할당합니다. 역할 할당의 범위는 구독, 리소스 그룹 또는 단일 리소스일 수 있습니다. 부모 범위에서 할당된 역할은 역할 내에 포함된 하위 항목에 대한 액세스를 부여합니다. 예를 들어 리소스 그룹에 액세스할 수 있는 사용자는 웹 사이트, 가상 머신 및 서브넷을 포함하여 그 안에 포함된 모든 리소스를 관리할 수 있습니다. 각 구독 내에서 최대 2,000개의 역할 할당을 만들 수 있습니다.

역할은 권한의 모음이며 RBAC에는 몇 가지 기본 제공 역할이 있습니다. 다음 기본 제공 역할이 모든 리소스 종류에 적용됩니다.

- **소유자** - 액세스 권한을 다른 사용자에게 위임할 수 있는 권한을 포함하여 모든 리소스에 대한 전체 액세스 권한을 보유합니다.

- **참가자**는 모든 유형의 Azure 리소스를 만들고 관리할 수 있지만 다른 사용자에게 액세스 권한을 부여할 수 없습니다.

- **읽기 권한자**는 모든 Azure 리소스를 볼 수 있습니다.

Azure에서 나머지 기본 제공 역할은 특정 Azure 리소스의 관리를 허용합니다. 예를 들어 Virtual Machine 참여자 역할을 사용하면 사용자가 가상 머신을 만들고 관리할 수 있습니다. 모든 기본 제공 역할 및 해당 작업의 목록은 [RBAC 기본 제공 역할](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)을 참조하세요.

RBAC는 Azure Portal 및 Azure Resource Manager API에서 Azure 리소스의 관리 작업을 지원합니다. 대부분의 경우, RBAC는 Azure 리소스의 데이터 수준 작업에 대한 권한을 부여할 수 없습니다. RBAC가 데이터 작업으로 확장되는 방법에 대한 자세한 내용은 [역할 정의 이해](https://docs.microsoft.com/azure/role-based-access-control/role-definitions)를 참조하세요.

기본 제공 역할이 특정 액세스 요구를 충족하지 못하는 경우 [사용자 지정 역할을 만들](https://docs.microsoft.com/azure/role-based-access-control/custom-roles) 수 있습니다. 기본 제공 역할과 마찬가지로 사용자 지정 역할을 사용자, 그룹 및 응용 프로그램에 구독, 리소스 그룹 및 리소스 범위에서 할당할 수 있습니다. [Azure PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell), [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) 및 [REST API](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest)를 사용하여 사용자 지정 역할을 만들 수 있습니다.

### <a name="resource-management"></a>리소스 관리

Azure는 현재 [클래식](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model) 및 Azure Resource Manger라는 두 개의 배포 모델을 제공합니다.

클래식 모델에서는 각 리소스가 독립적으로 존재합니다. 관련된 리소스를 함께 그룹화할 방법이 없습니다. 어떤 리소스로 솔루션 또는 응용 프로그램이 구성되었는지를 수동으로 추적하고 통합된 방식으로 이를 관리해야 했습니다. 기본 관리 단위는 구독입니다. 구독 내에서는 리소스를 분류하기가 어려우며 따라서 많은 수의 구독이 생성됩니다.

Resource Manager 배포 모델에는 [리소스 그룹](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) 개념이 포함됩니다. 리소스 그룹은 공통 수명 주기를 공유하는 리소스의 컨테이너입니다. 솔루션에 대한 모든 리소스 또는 그룹으로 관리하려는 해당 리소스만 포함될 수 있습니다. 사용자의 조직에 가장 적합한 내용에 따라 리소스 그룹에 리소스를 어떻게 할당할지 결정합니다.

리소스 관리자 배포 모델에는 여러 이점을 제공합니다.

- 솔루션에 대한 모든 서비스를 개별적으로 처리하는 것이 아니라 이러한 서비스를 그룹으로 배포, 관리 및 모니터링할 수 있습니다.

- 수명 주기 내내 솔루션을 반복적으로 배포하며 안심하고 일관된 상태로 리소스를 배포할 수 있습니다.

- 리소스 그룹에 있는 모든 리소스에 액세스 제어를 적용할 수 있습니다. 새 리소스가 리소스 그룹에 추가될 때에 해당 정책이 자동으로 적용됩니다.

- 리소스에 태그를 적용하여 구독에서 모든 리소스를 논리적으로 구성할 수 있습니다.

- JSON(JavaScript Object Notation)을 사용하여 솔루션에 대한 인프라를 정의할 수 있습니다. JSON 파일을 Resource Manager 템플릿이라고 합니다.

- 올바른 순서로 배포되므로 리소스 간의 종속성을 정의할 수 있습니다.

![리소스 관리자](./media/governance-in-azure/security-governance-in-azure-fig4.png)

템플릿에 대한 권장 사항은 [Azure Resource Manager 템플릿 생성 모범 사례](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices)를 참조하세요.

리소스가 올바른 순서로 생성되도록 Azure Resource Manager가 종속성을 분석합니다. 한 리소스가 다른 리소스(예: 디스크에 대한 저장소 계정을 필요로 하는 가상 머신)의 값에 의존하는 경우 템플릿에서 [종속성](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-define-dependencies)을 설정합니다.

또한 인프라의 업데이트에 대한 템플릿을 사용할 수 있습니다. 예를 들어 솔루션에 리소스를 추가할 수 있으며 이미 배포된 리소스에 대한 구성 규칙을 추가할 수 있습니다. 템플릿에서 리소스 만들기를 지정하지만 해당 리소스가 이미 존재하는 경우 Resource Manager는 새 자산을 만드는 대신 업데이트를 수행합니다. Resource Manager는 새 것과 동일한 상태로 기존 자산을 업데이트합니다.

Resource Manager는 설치에 포함되지 않은 소프트웨어를 설치하는 등의 추가 작업이 필요할 때 시나리오에 대한 확장을 제공합니다.

### <a name="resource-tracking"></a>리소스 추적

조직의 사용자가 구독에 리소스를 추가함에 따라 리소스를 적절한 부서, 고객 및 환경과 연결하는 것이 더욱 중요해지고 있습니다. [태그](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)를 통해 메타데이터를 리소스에 연결할 수 있습니다. 태그를 사용하여 리소스 또는 소유자에 대한 정보를 제공합니다. 태그를 사용하면 리소스를 여러 방식으로 집계 및 그룹화할 수 있을 뿐만 아니라 차지백 용도로도 데이터를 사용할 수 있습니다.

리소스 그룹 및 리소스의 복잡한 컬렉션이 있고 이러한 자산에 가장 적합한 방식으로 시각화할 필요가 있을 때 태그를 사용합니다. 예를 들어 조직에서 비슷한 역할을 제공하거나 동일한 부서에 속한 리소스를 태그로 지정할 수 있습니다.

조직의 사용자는 태그 없이 나중에 식별하고 관리하기 어려울 수 있는 여러 리소스를 만들 수 있습니다. 예를 들어 프로젝트에 대한 모든 리소스를 삭제하려고 할 수 있습니다. 해당 리소스가 프로젝트에 대해 태그가 지정되지 않은 경우 수동으로 찾아야 합니다. 태그를 지정하는 작업은 구독에서 불필요한 비용을 줄일 수 있는 좋은 방법입니다.

리소스는 태그를 공유하는 동일한 리소스 그룹에 있을 필요가 없습니다. 조직의 모든 사용자가 실수로 약간 다른 태그(예: “department” 대신 “dept”)를 적용하지 않고 일반 태그를 사용하는지 확인하려면 사용자 고유의 태그 분류를 만들 수 있습니다.

리소스 정책을 통해 조직의 표준 규칙을 만들 수 있습니다. 적절한 값으로 리소스에 태그가 지정되도록 정책을 만들 수 있습니다.

Azure Portal을 통해 태그가 지정된 리소스를 볼 수도 있습니다. 구독에 대한 [사용 현황 보고서](https://docs.microsoft.com/azure/billing/billing-understand-your-bill)는 태그 이름 및 값을 포함하며 이를 통해 태그별 비용을 알아낼 수 있습니다.

태그에 대한 자세한 내용은 [청구 태그 정책 이니셔티브](../azure-policy/scripts/billing-tags-policy-init.md)를 참조하세요.

다음 제한 사항이 태그에 적용됩니다.

- 각 리소스 또는 리소스 그룹에는 최대 15개의 태그 키/값 쌍이 포함될 수 있습니다. 이 제한은 리소스 그룹 또는 리소스에 직접 적용되는 태그에만 적용됩니다. 리소스 그룹은 각각 15개의 태그 키/값 쌍이 있는 여러 리소스를 포함할 수 있습니다.

- 태그 이름의 최대 길이는 512자입니다.

- 태그 값의 최대 길이는 256자입니다.

- 태그는 해당 리소스 그룹의 리소스에 의해 상속되지 않은 리소스 그룹에 적용됩니다.

리소스와 연결해야 하는 값이 15보다 많은 경우 태그 값에 JSON 문자열을 사용합니다. JSON 문자열은 단일 태그 키에 적용되는 여러 값을 포함할 수 있습니다.

#### <a name="tags-for-billing"></a>청구에 대한 태그

태그를 사용하여 청구 데이터를 그룹화할 수 있습니다. 예를 들어 서로 다른 조직에 여러 VM을 실행하는 경우 태그를 사용하여 비용 센터별로 사용량을 그룹화합니다. 또한 프로덕션 환경에서 실행 중인 VM에 대한 청구 사용량과 같이 런타임 환경별로 비용을 분류하는 데 태그를 사용할 수도 있습니다.

[Azure 리소스 사용 및 RateCard API](https://docs.microsoft.com/azure/billing/billing-usage-rate-card-overview) 또는 사용 CSV(쉼표로 구분된 값) 파일을 통해 태그에 대한 정보를 검색할 수 있습니다. [Azure 계정 포털](https://account.windowsazure.com/) 또는 [EA 포털](https://ea.azure.com/)에서 사용 현황 파일을 다운로드할 수 있습니다.

대금 청구 정보에 프로그래밍 방식으로 액세스하는 방법은 [Microsoft Azure 리소스 소비에 대한 통찰력 얻기](https://docs.microsoft.com/azure/billing/billing-usage-rate-card-overview)를 참조하세요. REST API 작업에 대한 내용은 [Azure 청구 REST API 참조](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c)를 참조하세요.

대금 청구에 태그를 지원하는 서비스용 사용 CSV를 다운로드하면 태그 열에 태그가 나타납니다.

### <a name="critical-resource-controls"></a>중요한 리소스 제어

조직에서 구독에 코어 서비스를 추가함에 따라 해당 서비스를 업무 중단을 방지하는 데 사용할 수 있도록 하는 것이 더욱 중요해집니다. [리소스 잠금](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources)을 통해 리소스를 삭제할 경우 응용 프로그램 또는 클라우드 인프라에 상당한 영향을 미치는 높은 가치의 리소스에서 작업을 제한할 수 있습니다. 잠금은 구독, 리소스 그룹 또는 리소스에 적용할 수 있습니다. 일반적으로 가상 네트워크, 게이트웨이 및 저장소 계정과 같은 기본 리소스에 잠금을 적용합니다.

리소스 잠금은 현재 두 가지 값(**CanNotDelete** 및 **ReadOnly**)을 지원합니다. **CanNotDelete**는 적절한 권한이 있는 사용자가 계속해서 리소스를 읽거나 수정할 수 있지만 삭제할 수 없음을 의미합니다. **ReadOnly**는 권한이 있는 사용자가 리소스를 삭제 또는 수정할 수 없음을 의미합니다.

리소스 잠금은 <https://management.azure.com>에 전송된 작업으로 구성되는 관리 평면에서 발생된 작업에만 적용됩니다. 잠금은 리소스 자체 기능을 수행하는 방법을 제한하지 않습니다. 리소스 변경은 제한되지만 리소스 작업은 제한되지 않습니다. 예를 들어 SQL Database에 대한 **ReadOnly** 잠금을 사용하면 데이터베이스를 삭제하거나 수정하지 못하지만, 데이터베이스에서 데이터를 만들기, 업데이트 또는 삭제하지 못합니다.

**ReadOnly**를 적용하면 읽기 작업처럼 보이는 일부 작업에 추가 작업이 필요하기 때문에 예기치 않은 결과가 발생할 수 있습니다. 예를 들어 저장소 계정에 **ReadOnly** 잠금을 설정하면 모든 사용자가 키를 나열하지 않도록 방지합니다. 반환되는 키를 쓰기 작업에 사용할 수 있기 때문에 키 나열 작업은은 POST 요청을 통해 처리됩니다.

![중요한 리소스 제어](./media/governance-in-azure/security-governance-in-azure-fig5.png)

또 다른 예로 Azure App Service 리소스에 **ReadOnly** 잠금을 설정하면 해당 상호 작용이 쓰기 액세스를 필요로 하기 때문에 Visual Studio 서버 탐색기가 리소스에 대한 파일을 표시하지 않도록 방지합니다.

역할 기반 액세스 제어와 달리 관리 잠금을 사용하여 모든 사용자와 역할에 걸쳐 제한을 적용합니다. 사용 권한 설정에 대한 자세한 내용은 [RBAC 및 Azure Portal을 사용하여 액세스 관리](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)를 참조하세요.

부모 범위에서 잠금을 적용하면 해당 범위 내 모든 리소스가 동일한 잠금을 상속합니다. 나중에 추가하는 리소스도 부모의 잠금을 상속합니다. 상속의 가장 제한적인 잠금이 우선 적용됩니다.

관리 잠금을 만들거나 삭제하려면 Microsoft.Authorization/ 또는 Microsoft.Authorization/locks/ 작업에 대한 액세스 권한이 있어야 합니다. 기본 제공 역할의 경우 소유자 및 사용자 액세스 관리자에게만 이러한 작업의 권한이 부여됩니다.

### <a name="api-access-to-billing-information"></a>청구 정보에 대한 API 액세스

Azure Billing API를 사용하여 사용량 및 리소스 데이터를 기본 설정된 데이터 분석 도구로 끌어옵니다. Azure 리소스 사용량 및 RateCard API를 통해 비용을 정확하게 예측하고 관리할 수 있습니다. API는 Azure Resource Manager가 노출한 API의 제품군의 일부로서 리소스 공급자로 구현됩니다.

#### <a name="resource-usage-api-preview"></a>리소스 사용량 API(미리 보기)

Azure [리소스 사용량 API](https://msdn.microsoft.com/library/azure/mt219003)를 사용하여 예상된 Azure 사용량 데이터를 가져올 수 있습니다. API에는 다음이 포함됩니다.

- **RBAC**: [Azure Portal](https://portal.azure.com/) 또는 [Azure PowerShell cmdlet](https://docs.microsoft.com/powershell/azure/overview)을 통해 액세스 정책을 구성하여 사용자 또는 응용 프로그램이 구독의 사용 현황 데이터에 액세스를 얻도록 지정할 수 있습니다. 호출자가 인증에 대한 표준 Azure Active Directory 토큰을 사용해야 합니다. 호출자가 특정 Azure 구독에 대한 사용 데이터에 액세스하려면 호출자를 청구서 읽기 권한자, 읽기 권한자, 소유자 또는 참가자 역할에 추가해야 합니다.

- **매시간 또는 매일 집계**: 호출자는 Azure 사용 데이터를 매시간 또는 매일 증분으로 할지 지정할 수 있습니다. 기본값은 매일입니다.

- **인스턴스 메타데이터(리소스 태그 포함)**: 정규화된 리소스 URI(/subscriptions/{subscription-id} /..)와 같은 인스턴스 수준 정보, 리소스 그룹 정보 및 리소스 태그를 가져옵니다. 이 메타데이터를 통해 청구 구간 같은 사용 사례에 대한 태그로 사용을 프로그래밍 방식으로 명확하게 할당할 수 있습니다.

- **리소스 메타데이터**: 측정기 이름, 측정기 범주, 측정기 하위 범주, 단위 및 하위 지역과 같은 리소스 세부 정보를 통해 호출자는 사용된 것을 더 잘 이해할 수 있습니다. Azure Portal, Azure 사용 CSV, EA 청구 CSV 및 다른 공용 환경을 통해 리소스 메타데이터 용어를 정렬하기 위해 노력하여 환경을 통해 데이터를 서로 연결할 수 있도록 합니다.

- **모든 사용 유형에 대한 사용** – 종량제, MSDN, 약정 금액, 금액 크레딧 및 EA 등을 비롯한 모든 사용 유형에 대한 사용 데이터가 제공됩니다.

#### <a name="resource-ratecard-api"></a>리소스 RateCard API

Azure 리소스 RateCard API를 사용하여 각 예상 가격 정보 및 사용 가능한 Azure 리소스의 목록을 가져옵니다. API에는 다음이 포함됩니다.

- **RBAC**: Azure Portal 또는 Azure PowerShell cmdlet을 통해 액세스 정책을 구성하여 사용자 또는 응용 프로그램이 RateCard 데이터에 액세스를 얻도록 지정할 수 있습니다. 호출자가 인증에 대한 표준 Azure Active Directory 토큰을 사용해야 합니다. 호출자가 특정 Azure 구독에 대한 사용 데이터에 액세스하려면 호출자를 판독기, 소유자 또는 기여자 역할에 추가합니다.

- **종량제, MSDN, 금액 약정 및 금액 크레딧 제안(EA은 제외)**: 이 API는 Azure 제안 수준 환율 정보를 제공합니다. API의 호출자는 제안 정보를 전달하여 리소스 세부 정보 및 속도를 가져와야 합니다. 현재 EA는 등록당 사용자 지정된 속도를 제공하기 때문에 지원되지 않습니다. 

#### <a name="scenarios"></a>시나리오

사용량 및 RateCard API를 함께 사용하면 다음 시나리오가 가능합니다.

- **해당월 Azure 소비 파악**: 사용 및 RateCard API 조합을 사용하여 해당 월 동안 클라우드 지출에 대한 높은 통찰력을 얻을 수 있습니다. 매 시간 및 매일 사용 및 요금 예측량을 분석할 수 있습니다.

- **경고 설정**: 사용 및 RateCard API를 사용하여 예상되는 클라우드 사용량 및 요금을 가져오고 리소스 기반 또는 통화 기반 경로를 설정합니다.

- **예측 청구**: 예상된 소비량 및 클라우드 지출을 얻을 수 있으며 청구 주기가 끝날 때 청구를 예측할 기계 학습 알고리즘을 적용합니다.

- **사전 소비 비용 분석 수행**: RateCard API를 사용하여 워크로드를 Azure에 이동하면 예상 사용량에 대해 얼마가 청구되는지 예측할 수 있습니다. 다른 클라우드 또는 사설 클라우드에 기존 워크로드를 가진 경우 Azure 속도로 사용을 매핑하여 Azure 소비를 더 잘 예측할 수 있습니다. 이러한 예측을 통해 제품을 피벗하고 약정 금액 및 금액 크레딧과 같은 종량제뿐만 아니라 다른 제품 유형 간에 비교하는 기능을 제공할 수 있습니다.

- **가상 분석 수행**: 워크로드를 Azure 리소스의 다른 하위 지역에서 실행하는 것이 비용 효율적인지 또는 다른 구성에서 실행하는 것이 비용 효율적인지 여부를 확인할 수 있습니다. Azure 리소스 비용은 사용 중인 Azure 지역에 따라 달라질 수 있습니다. 다른 Azure 제품 유형에서 Azure 리소스에 더 나은 속도를 제공하는지 결정할 수도 있습니다.

### <a name="networking-controls"></a>네트워킹 제어

리소스에 대한 액세스는 내부(기업 네트워크 내부) 또는 외부(인터넷을 통함)일 수 있습니다. 조직의 사용자가 실수로 리소스를 잘못된 위치에 배치하여 잠재적으로 악의적인 액세스에 노출되기 쉽습니다. 온-프레미스 장치에서 기업은 적절한 제어를 추가하여 Azure 사용자가 합리적인 의사 결정을 내릴 수 있도록 해야 합니다.

![네트워킹 제어](./media/governance-in-azure/security-governance-in-azure-fig6.png)

구독 관리를 위해 다음 핵심 리소스는 기본적인 액세스 제어를 제공합니다.

#### <a name="network-connectivity"></a>네트워크 연결

[가상 네트워크](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)는 서브넷에 대한 컨테이너 개체입니다. 반드시 필요한 것은 아니지만 가상 네트워크는 응용 프로그램을 내부 기업 리소스에 연결할 때 주로 사용됩니다. Azure Virtual Network 서비스를 사용하면 Azure 리소스와 가상 네트워크를 서로 안전하게 연결할 수 있습니다.

가상 네트워크는 클라우드의 사용자 네트워크를 나타내는 표현입니다. 가상 네트워크는 구독 전용 Azure 클라우드를 논리적으로 격리한 것입니다. 가상 네트워크를 온-프레미스 네트워크에 연결할 수도 있습니다.

다음은 Azure Virtual Network의 기능입니다.

- **격리**: 가상 네트워크는 서로 격리되어 있습니다. 동일한 CIDR 주소 블록을 사용하는 개별 가상 네트워크를 만들어 개발, 테스트, 프로덕션 용도에 이용할 수 있습니다. 반대로, 여러 CIDR 주소 블록을 사용하는 가상 네트워크를 만들어 네트워크를 서로 연결할 수도 있습니다. 가상 네트워크를 여러 서브넷으로 분할할 수 있습니다. Azure는 가상 네트워크에 연결된 VM 및 Azure Cloud Services 역할 인스턴스의 내부 이름 확인을 제공합니다. 필요에 따라 Azure 내부 이름 확인을 사용하는 대신, 고유한 DNS 서버를 사용하도록 가상 네트워크를 구성할 수 있습니다.

- **인터넷 연결**: 가상 네트워크에 연결되는 모든 Azure Virtual Machines 및 Cloud Services 역할 인스턴스는 기본적으로 인터넷에 액세스할 수 있습니다. 또한 필요에 따라 특정 리소스에 대한 인바운드 액세스를 사용하도록 설정할 수 있습니다.

- **Azure 리소스 연결**: Cloud Services 및 VM과 같은 Azure 리소스를 동일한 가상 네트워크에 연결할 수 있습니다. 리소스는 서로 다른 서브넷에 있더라도 사설 IP 주소를 통해 서로 연결할 수 있습니다. Azure는 서브넷, 가상 네트워크 및 온-프레미스 네트워크 간에 기본 라우팅을 제공하므로 수동으로 경로를 구성하고 관리할 필요가 없습니다.

- **가상 네트워크 연결**: 가상 네트워크를 서로 연결할 수 있습니다. 가상 네트워크에 연결된 리소스는 다른 가상 네트워크의 모든 리소스와 통신할 수 있습니다.

- **온-프레미스 연결**: 가상 네트워크를 네트워크와 Azure 간의 개인 네트워크 연결이나, 인터넷을 통한 사이트 간 VPN(가상 사설망) 연결을 통해 온-프레미스 네트워크에 연결할 수 있습니다.

- **트래픽 필터링**: 가상 머신 및 Cloud Services에 대한 네트워크 트래픽(인바운드 및 아웃바운드)을 원본 IP 주소 및 포트, 대상 IP 주소 및 포트, 프로토콜별로 필터링할 수 있습니다.

- **라우팅**: 필요에 따라 자체 경로를 구성하거나 네트워크 게이트웨이를 통한 BGP 경로를 사용하여 기본 Azure 라우팅을 재정의할 수 있습니다.

#### <a name="network-access-controls"></a>네트워크 액세스 컨트롤

[NSG(네트워크 보안 그룹)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)는 방화벽과 유사하며 리소스가 네트워크를 통해 "통신"하는 방식에 대한 규칙을 제공합니다. 서브넷(또는 가상 머신)이 인터넷 또는 동일한 가상 네트워크의 다른 서브넷에 연결하는 방식에 대한 세분화된 제어를 제공합니다.

네트워크 보안 그룹에는 Azure Virtual Network에 연결된 리소스에 대한 네트워크 트래픽을 허용하거나 거부하는 보안 규칙 목록이 포함되어 있습니다. NSG는 서브넷, 개별 VM(클래식) 또는 VM(Resource Manager)에 연결된 개별 NIC(네트워크 인터페이스)와 연결될 수 있습니다.

NSG를 서브넷에 연결하면 규칙이 서브넷에 연결된 모든 리소스에 적용됩니다. 또한 NSG를 VM 또는 NIC에 연결하여 트래픽을 더욱 제한할 수 있습니다.

## <a name="security-and-compliance-with-organizational-standards"></a>보안 및 조직 표준 준수

비즈니스마다 요구 사항이 다르며 클라우드 솔루션에서 차별화된 이점을 누립니다. 모든 유형의 고객은 여전히 클라우드로 이동하는 것에 대해 기본적으로 같은 우려를 합니다. 고객이 클라우드 공급자에게 바라는 점은 다음과 같습니다.

- **데이터 보호**: IT 리더는 클라우드가 향상된 데이터 보안 및 관리 제어를 제공할 수 있다는 것을 알고 있습니다. 하지만 여전히 클라우드로 마이그레이션하는 것이 현재 내부 솔루션보다 해커에 더 취약한 것을 우려합니다.

- **데이터 보관**: 클라우드 서비스는 고유한 개인정보보호 문제를 발생시킵니다. 회사에서는 클라우드가 인프라 비용을 절약하고 유연성을 향상시킬 수 있다고 보지만 또한 데이터가 어디에 저장되고 누가 액세스하고 어떻게 사용하는지에 대해 제어하지 못하게 되는 것을 염려합니다.

- **제어 제공**: 더 많은 혁신적인 솔루션을 배포하기 위해 클라우드를 활용하면서도 회사는 해당 데이터를 제어하지 못하게 되는 것을 염려합니다. 법적 수단 및 그 외의 수단을 통해 고객 데이터에 액세스하는 정부 기관의 최근 공개로 인해 일부 CIO는 클라우드에 데이터를 저장하는 것에 대해 매우 조심스럽습니다.

- **투명성 강화**: 비즈니스 의사 결정자는 보안, 개인정보보호 및 제어의 중요성을 알고 있습니다. 하지만 데이터가 어떻게 저장되고 액세스되고 보호되는지 독립적으로 확인하는 기능 또한 원합니다.

- **규정 준수 유지 관리**: 회사의 클라우드 기술 사용이 확대되면서 표준 및 규정의 범위와 복잡성도 계속해서 진화하고 있습니다. 회사는 규정 준수 표준을 충족하는지 알아야 합니다.

## <a name="security-configuration-for-monitoring-logging-and-auditing"></a>모니터링, 로깅 및 감사에 대한 보안 구성

Azure 구독자는 여러 장치에서 자신의 클라우드 환경을 관리할 수 있습니다. 이러한 장치에는 관리 워크스테이션, 개발자 PC, 심지어 작업별 사용 권한을 가진 최종 사용자 장치 등이 포함될 수 있습니다. 

경우에 따라, 관리 기능은 Azure Portal과 같은 웹 기반 콘솔을 통해 수행됩니다. 다른 경우, VPN, 터미널 서비스, 클라이언트 응용 프로그램 프로토콜 또는 (프로그래밍 방식의) Azure Service Management API(SMAPI)를 통해 온-프레미스 시스템에서 Azure에 직접 연결할 수 있습니다. 또한 클라이언트 엔드포인트는 태블릿이나 스마트폰 같이 조인 또는 격리되고 관리되지 않는 도메인이 될 수 있습니다.

이 가변성은 클라우드 배포에 상당한 위험을 야기할 수 있습니다. 관리 작업을 관리, 추적 및 감사하기 어려울 수 있습니다. 이 가변성은 또한 클라우드 서비스 관리에 사용되는 클라이언트 엔드포인트에 대한 액세스를 규제하지 않음으로써 보안 위협을 들여올 수 있습니다. 인프라를 개발 및 관리하기 위한 일반 또는 개인 워크스테이션을 사용 하면 웹 검색(예: 워터링 홀 공격) 또는 전자 메일(예: 소셜 엔지니어링 및 피싱 공격)와 같이 예측할 수 없는 위협 벡터를 열게 됩니다.

모니터링, 로깅 및 감사는 관리 작업을 추적 및 파악하기 위한 기초를 제공합니다. 생성된 데이터의 양으로 인해 항상 모든 작업을 자세히 감사하지는 않습니다. 그러나 관리 정책의 효율성을 감사하는 것이 최선입니다.

Azure AD DS(Active Directory Domain Services) GPO에서 Azure 보안 거버넌스를 사용하여 파일 공유와 같이 모든 관리자의 Windows 인터페이스를 제어할 수 있습니다. 모니터링, 감사 및 감사 프로세스에 관리 워크스테이션을 포함합니다. 모든 관리자 및 개발자 액세스 및 사용을 추적합니다.

### <a name="azure-security-center"></a>Azure Security Center

[Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)를 통해 구독에서 리소스 보안 상태를 중앙에서 볼 수 있습니다. 여기에서 리소스 손상을 방지하기 위한 권장 사항이 제공됩니다. 보다 세부적인 정책이 가능합니다. 예를 들어 기업이 직면한 위험에 따라 대비할 수 있도록 특정 리소스 그룹에 정책을 적용할 수 있습니다.

![Azure Security Center](./media/governance-in-azure/security-governance-in-azure-fig7.png)

Security Center는 Azure 구독을 통해 통합된 보안 모니터링 및 정책 관리를 제공하고, 달리 발견되지 않을 수도 있는 위협을 검색하는 데 도움이 되며, 보안 솔루션의 광범위한 에코시스템에서 작동합니다. 구독의 리소스에 대해 [보안 정책](https://docs.microsoft.com/azure/security-center/security-center-policies)을 사용하도록 설정하면 Security Center에서 리소스의 보안을 분석하여 잠재적 취약성을 식별합니다. 네트워크 구성 정보는 즉시 이용할 수 있지만,

Azure Security Center는 Azure 구독 내에 모든 리소스에 대한 모범 사례 분석 및 보안 정책 관리의 조합을 나타냅니다. 이를 통해 Azure 리소스, 네트워크, 맬웨어 방지 프로그램 및 방화벽과 같은 파트너 솔루션의 보안 데이터를 자동으로 수집하고 분석하므로 보안 팀과 위험 관리자가 보안 위협을 방지 및 감지하고 대응할 수 있습니다.

또한 Azure Security Center는 기계 학습 및 행동 분석을 비롯한 고급 분석을 적용합니다. Microsoft 제품과 서비스, Microsoft DCU(Digital Crimes Unit), MSRC(Microsoft 보안 대응 센터) 및 외부 피드에서 글로벌 위협 인텔리전스를 사용합니다. 구독 수준에서 광범위하게 [보안 거버넌스](https://www.credera.com/blog/credera-site/azure-governance-part-4-other-tools-in-the-toolbox/)를 적용할 수 있습니다. 또는 정책 정의를 통해 특정 요구 사항으로 범위를 좁히고 개별 리소스에 적용할 수 있습니다.

마지막으로 Azure Security Center는 해당 정책을 기반으로 리소스 보안 상태를 분석하고 이 정보를 사용하여 통찰력 있는 대시보드를 제공하고 맬웨어 검색 또는 악성 IP 연결 시도 등의 이벤트에 대한 경고를 제공합니다. 권장 사항을 적용하는 방법에 대한 자세한 내용은 [Azure Security Center에서 보안 권장 사항 구현](https://docs.microsoft.com/azure/security-center/security-center-recommendations)을 참조하세요.

Azure Security Center에서는 다음과 같은 Azure 리소스를 모니터링합니다.

- VM(가상 머신)(클라우드 서비스 포함)

- 가상 네트워크

- SQL 데이터베이스

- VM 및 [App Service Environment](https://docs.microsoft.com/azure/app-service/app-service-app-service-environments-readme)에서 웹 응용 프로그램 방화벽 같이 Azure 구독과 통합된 파트너 솔루션

보안 센터에 처음 액세스할 경우 구독의 모든 가상 머신에서 데이터 수집을 활성화합니다. 데이터 컬렉션을 사용하도록 유지하는 것이 좋지만 Security Center 정책에서 [사용하지 않도록 설정](https://docs.microsoft.com/azure/security-center/security-center-faq)할 수 있습니다.

### <a name="log-analytics"></a>Log Analytics

Azure Log Analytics 소프트웨어 개발 및 서비스 팀의 정보 보안 및 [거버넌스 프로그램](https://github.com/Microsoft/azure-docs/blob/master/articles/log-analytics/log-analytics-security.md)은 비즈니스 요구 사항을 지원합니다. [Microsoft Azure 보안 센터](https://azure.microsoft.com/support/trust-center/) 및 [Microsoft 보안 센터 규정 준수](https://www.microsoft.com/TrustCenter/Compliance/default.aspx)에 설명된 법률 및 규정을 준수합니다. 여기에는 Log Analytics가 보안 요구 사항을 정하고 보안 컨트롤을 식별하며 위험을 관리 및 모니터링하는 방식도 설명되어 있습니다. 팀은 정책, 표준, 절차, 지침을 매년 검토합니다.

각 Log Analytics 개발 팀 멤버는 공식적인 응용 프로그램 보안 교육을 이수합니다. 버전 제어 시스템을 통해 개발 중인 각 소프트웨어 프로젝트를 보호할 수 있습니다.

Microsoft에는 Microsoft의 모든 서비스를 감독 및 평가하는 보안 및 규정 준수 팀이 있습니다. 이 팀은 정보 보안 책임자로 구성되어 있으며 Log Analytics를 개발하는 엔지니어링 부서와 관련이 없습니다. 보안 책임자는 자체적인 관리 체인을 보유합니다. 제품과 서비스에 대한 독립적 평가를 실시하여 보안과 규정 준수를 보장합니다.

Log Analytics의 핵심 기능은 Azure에서 실행되는 서비스 집합을 통해 제공됩니다. 각 서비스는 고유의 관리 기능을 제공하며, 사용자는 서비스를 결합하여 다양한 관리 시나리오를 해결할 수 있습니다.

![관리에 대한 Azure 서비스](./media/governance-in-azure/security-governance-in-azure-fig9.JPG)

이러한 관리 서비스는 클라우드에서 설계되었습니다. 이러한 서비스는 Azure에서 전적으로 호스트되므로 온-프레미스 리소스를 배포 및 관리하지 않아도 됩니다. 구성 작업이 최소화되어 몇 분 안에 실행할 수 있습니다.

데이터 센터의 Windows 또는 Linux 컴퓨터에 에이전트를 배포하면 에이전트가 Log Analytics로 데이터를 보냅니다. 전송된 데이터는 클라우드 또는 온-프레미스 서비스에서 수집된 다른 모든 데이터와 함께 분석할 수 있습니다. 온-프레미스 리소스의 백업 및 고가용성을 위해 Azure Backup 및 Azure Site Recovery를 통해 클라우드를 활용할 수 있습니다.

![Azure 대시보드에서 관리 서비스](./media/governance-in-azure/security-governance-in-azure-fig8.png)

클라우드의 Runbook은 일반적으로 온-프레미스 리소스에 액세스할 수 없지만, 하나 이상의 컴퓨터에 에이전트를 설치하여 데이터 센터에서 Runbook을 호스팅할 수도 있습니다. Runbook을 시작할 때 클라우드에서 실행할 것인지 아니면 로컬 작업자에서 실행할 것인지 지정합니다.

Azure 구독에 추가할 수 있는 Microsoft와 파트너의 다양한 솔루션을 사용할 수 있기 때문에 Log Analytics에 대한 투자 가치를 높일 수 있습니다. 예를 들어 Azure는 하나 이상의 관리 서비스를 활용하여 관리 시나리오를 구현하는 미리 패키지된 논리 집합인 [관리 솔루션](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-solutions)을 제공합니다.

![Azure의 관리 솔루션 갤러리](./media/governance-in-azure/security-governance-in-azure-fig10.png)

파트너는 본인의 응용 프로그램 및 서비스를 지원하는 자체 솔루션을 만들어서 Azure Marketplace 또는 빠른 시작 템플릿을 통해 사용자에게 제공할 수 있습니다.

## <a name="performance-alerting-and-monitoring"></a>성능 경고 및 모니터링

### <a name="alerting"></a>경고

경고는 Azure 리소스 메트릭, 이벤트 또는 로그를 모니터링하는 방법입니다. 사용자가 지정한 조건에 부합하면 이를 알려 줍니다.

경고는 다음과 같은 서비스에서 사용할 수 있습니다.

- **Azure Application Insights**: 웹 테스트 및 메트릭 경고가 가능합니다. 자세한 내용은 [Application Insights에서 경고 설정](https://docs.microsoft.com/azure/application-insights/app-insights-alerts) 및 [모든 웹 사이트의 가용성 및 응답성 모니터링](https://docs.microsoft.com/azure/application-insights/app-insights-monitor-web-app-availability)을 참조하세요.

- **Log Analytics**: Log Analytics에 대한 작업 및 진단 로그를 라우팅할 수 있습니다. 메트릭, 로그 및 기타 경고 유형을 허용합니다. 자세한 내용은 [Log Analytics의 경고](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts)를 참조하세요.

- **Azure Monitor**: Azure Monitor에서는 메트릭 값과 활동 로그 이벤트 모두를 기반으로 한 경고가 가능합니다. [Azure Monitor REST API](https://msdn.microsoft.com/library/dn931943.aspx)를 사용하여 경고를 관리할 수 있습니다. 자세한 내용은 [Azure 포털, PowerShell 또는 명령줄 인터페이스를 사용하여 경고 만들기](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-alerts-portal)를 참조하세요.

### <a name="monitoring"></a>모니터링

클라우드 앱의 성능 문제는 비즈니스에 영향을 미칠 수 있습니다. 상호 연결된 여러 구성 요소와 자주 제공되는 릴리스를 사용하면 언제든지 성능 저하가 발생할 수 있습니다. 앱을 개발하는 경우 사용자는 일반적으로 테스트에서 찾지 못한 문제를 발견합니다. 이러한 문제에 대해 즉시 알고 있어야 하며 문제를 진단하고 수정하는 도구가 있어야 합니다.

Azure 응용 프로그램과 서비스를 모니터링하기 위한 다양한 도구가 있습니다. 일부 기능은 겹칩니다. 이는 부분적으로 응용 프로그램의 개발과 운영 사이의 모호함 때문입니다.

다음은 주요 도구입니다.

- **Azure Monitor**는 Azure에서 실행되는 서비스를 모니터링하는 기본 도구입니다. 또한 서비스의 처리량과 주변 환경에 대한 인프라 수준 데이터를 제공합니다. Azure에서 모든 앱을 관리하고 리소스를 강화 또는 축소할지 여부를 결정하려면 Azure Monitor를 통해 시작할 수 있습니다.

- **Application Insights** - 개발을 위해 사용하거나 프로덕션 모니터링 솔루션으로 사용할 수 있습니다. 앱에 패키지를 설치하여 작동하므로 진행 상황에 대한 자세한 내부 보기를 제공합니다. 이 데이터에는 종속성, 예외 추적, 디버깅 스냅숏, 실행 프로필의 응답 시간이 포함됩니다. 이 원격 분석 데이터를 모두 분석하여 앱을 디버그하고 사용자가 무엇을 하고 있는지 이해할 수 있도록 도움을 주는 도구를 제공합니다. 응답 시간의 급증이 어떤 앱 요소 또는 어떤 외부 리소스 문제로 인한 것인지 여부를 알 수 있습니다. Visual Studio를 사용하고 앱에 문제가 있는 경우 문제를 해결할 수 있도록 문제의 코드 줄로 바로 이동합니다.

- **Log Analytics** - 성능을 튜닝하고 프로덕션 환경에서 실행되는 응용 프로그램의 유지 관리를 계획해야 하는 사용자를 위한 것입니다. 10-15분 간의 지연으로 많은 소스의 데이터를 수집하고 집계합니다. Azure, 온-프레미스 및 타사 클라우드 기반 인프라(예: Amazon Web Services)에 대한 전체적인 IT 관리 솔루션을 제공합니다. 소스에서 데이터를 분석할 수 있는 도구를 제공하고, 모든 로그에서 복잡한 쿼리를 허용하며, 지정된 조건에 대해 사전에 경고할 수 있습니다. 사용자 지정 데이터를 해당되는 중앙의 리포지토리에 수집하여 데이터를 쿼리하고 시각화할 수도 있습니다.

- **System Center Operations Manager**는 대규모 클라우드 설치를 관리하고 모니터링하기 위한 것입니다. 온-프레미스 Windows Sever 및 Hyper-V 기반 클라우드를 위한 관리 도구로 이미 친숙해 있을 수 있지만, Azure 앱과 통합하여 이러한 앱을 관리할 수도 있습니다. 무엇보다도 기존의 라이브 앱에 Application Insights를 설치할 수 있습니다. 앱 작동이 중단되면 Operations Manager가 몇 초 안에 알려줍니다.


## <a name="next-steps"></a>다음 단계

- [Azure Resource Manager 템플릿 생성 모범 사례](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices)

- [Azure 구독 거버넌스 구현 예제](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-subscription-examples)

- [Microsoft Azure Government](https://docs.microsoft.com/azure/azure-government/)
