---
title: Azure에 대한 관리 ID
description: Service Fabric에서 Azure에 대한 관리 ID 사용에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 12/09/2019
ms.openlocfilehash: 71fa9ef4da8081a167b68553e2e3eac8477e8aee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98881723"
---
# <a name="using-managed-identities-for-azure-with-service-fabric"></a>Service Fabric에서 Azure에 대한 관리 ID 사용

클라우드 애플리케이션을 빌드할 때 일반적인 문제는 원본 제어에 또는 개발자 워크스테이션에 로컬로 저장하지 않고 다양한 서비스에 대한 인증을 위해 코드에서 자격 증명을 안전하게 관리하는 방법입니다. *Azure에 대한 관리 ID* 는 Azure AD 내에서 자동으로 관리 ID를 제공하여 Azure AD(Azure Active Directory)의 모든 리소스에 대해 이 문제를 해결합니다. 서비스의 ID를 사용하면 Key Vault를 비롯하여 Azure AD 인증을 지원하는 모든 서비스에 인증할 수 있으므로 코드에 자격 증명을 저장하지 않아도 됩니다.

*Azure 리소스에 대한 관리 ID* 는 Azure 구독용 Azure AD에 무료로 제공됩니다. 추가 비용은 없습니다.

> [!NOTE]
> *Azure에 대한 관리 ID* 는 이전 MSI(관리되는 서비스 ID) 서비스의 새 이름입니다.

## <a name="concepts"></a>개념

Azure에 대한 관리 ID는 다음과 같은 몇 가지 주요 개념을 기반으로 합니다.

- **클라이언트 ID** - 초기 프로비저닝 중에 애플리케이션과 서비스 주체에 연결된, Azure AD에서 생성된 고유 식별자입니다([애플리케이션 ID](../active-directory/develop/developer-glossary.md#application-id-client-id)도 참조).

- **보안 주체 ID** - Azure 리소스에 대한 역할 기반 액세스 권한을 부여하는 데 사용되는 관리 ID에 대한 서비스 주체 개체의 개체 ID입니다.

- **서비스 주체** - 지정된 테넌트에서 AAD 애플리케이션의 투영을 나타내는 Azure Active Directory 개체입니다 ([서비스 주체](../active-directory/develop/developer-glossary.md#service-principal-object)도 참조).

두 가지 종류의 관리 ID가 있습니다.

- **시스템 할당 관리 ID** 는 Azure 서비스 인스턴스에서 직접 사용하도록 설정됩니다.  시스템 할당 ID의 수명 주기는 사용하도록 설정된 Azure 서비스 인스턴스에 대해 고유합니다.
- **사용자 할당 관리 ID** 는 독립 실행형 Azure 리소스로 생성됩니다. ID는 하나 이상의 Azure 서비스 인스턴스에 할당될 수 있으며 해당 인스턴스의 수명 주기와 별도로 관리됩니다.

관리 ID 형식 간의 차이점을 자세히 이해하려면 [Azure 리소스에 대한 관리 ID는 어떻게 작동하나요?](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types)를 참조하세요.

## <a name="supported-scenarios-for-service-fabric-applications"></a>Service Fabric 애플리케이션에 지원되는 시나리오

Service Fabric에 대한 관리 ID는 Azure에서 배포한 Service Fabric 클러스터에서만 지원되며 Azure 리소스로 배포된 애플리케이션에 대해서만 지원됩니다. Azure 리소스로 배포되지 않은 애플리케이션에는 ID를 할당할 수 없습니다. 개념적으로 말하면 Azure Service Fabric 클러스터에서 관리 ID에 대한 지원은 다음 두 단계로 구성됩니다.

1. 하나 이상의 관리 ID를 애플리케이션 리소스에 할당합니다. 애플리케이션에는 단일 시스템 할당 ID 및/또는 최대 32개의 사용자 할당 ID가 각각 할당될 수 있습니다.

2. 애플리케이션의 정의 내에서 애플리케이션에 할당된 ID 중 하나를 애플리케이션을 구성하는 개별 서비스에 매핑합니다.

애플리케이션의 시스템 할당 ID는 해당 애플리케이션에 대해 고유합니다. 사용자 할당 ID는 여러 애플리케이션에 할당될 수 있는 독립 실행형 리소스입니다. 애플리케이션 내에서 단일 ID (시스템 할당 또는 사용자 할당 여부)는 애플리케이션의 여러 서비스에 할당될 수 있지만 각 개별 서비스에는 하나의 ID만 할당할 수 있습니다. 마지막으로 이 기능에 액세스하려면 서비스에 명시적으로 ID를 할당해야 합니다. 실제로 애플리케이션 ID를 해당 구성 서비스에 매핑하면 애플리케이션 내 격리를 사용할 수 있습니다. 즉, 서비스는 매핑된 ID만 사용할 수 있습니다.  

현재 이 기능에 대해 다음 시나리오가 지원됩니다.

- 하나 이상의 서비스와 하나 이상의 할당된 ID로 새 애플리케이션 배포

- Azure 리소스에 액세스하기 위해 하나 이상의 관리 ID를 기존(Azure 배포) 애플리케이션에 할당

다음 시나리오는 지원되지 않거나 권장되지 않습니다. 이러한 작업은 차단되지 않을 수 있지만 애플리케이션에서 중단이 발생할 수 있습니다.

- 애플리케이션에 할당된 ID를 제거하거나 변경합니다. 변경해야 하는 경우 별도의 배포를 제출하여 먼저 새 ID 할당을 추가하고 이전에 할당된 ID를 제거합니다. 기존 애플리케이션에서 ID를 제거하면 애플리케이션을 업그레이드할 수 없는 상태로 두는 등 바람직하지 않은 영향을 미칠 수 있습니다. ID를 제거해야 하는 경우 애플리케이션을 완전히 삭제하는 것이 안전합니다. 이는 애플리케이션과 연결된 시스템 할당 ID(정의된 경우)를 삭제하고 애플리케이션에 할당된 사용자 할당 ID를 사용하여 모든 연결을 제거합니다.

- 관리 ID에 대한 Service Fabric 지원은 현재 [AzureServiceTokenProvider](/dotnet/api/overview/azure/service-to-service-authentication)에 통합되지 않습니다.

## <a name="next-steps"></a>다음 단계

- [관리 ID 지원을 사용하여 새 Azure Service Fabric 클러스터 배포](./configure-new-azure-service-fabric-enable-managed-identity.md)
- [기존 Azure Service Fabric 클러스터에서 관리 ID 지원 사용](./configure-existing-cluster-enable-managed-identity-token-service.md)
- [시스템 할당 관리 ID를 사용하여 Azure Service Fabric 애플리케이션 배포](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
- [사용자가 할당한 관리 ID를 사용하여 Azure Service Fabric 애플리케이션 배포](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
- [서비스 코드에서 Service Fabric 애플리케이션의 관리 ID 활용](./how-to-managed-identity-service-fabric-app-code.md)
- [Azure Service Fabric 애플리케이션에 다른 Azure 리소스에 대한 액세스 권한 부여](./how-to-grant-access-other-resources.md)
- [애플리케이션 비밀을 KeyVaultReferences로 선언 및 사용](./service-fabric-keyvault-references.md)