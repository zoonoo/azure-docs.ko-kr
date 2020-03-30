---
title: Azure에 대한 관리되는 ID
description: 서비스 패브릭을 사용하여 Azure에 대해 관리되는 ID를 사용하는 방법에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 12/09/2019
ms.custom: sfrev
ms.openlocfilehash: 06ebcfdf3d6a3815908752153acb09437d745d15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76986753"
---
# <a name="using-managed-identities-for-azure-with-service-fabric-preview"></a>서비스 패브릭을 사용하여 Azure에 관리되는 ID 사용(미리 보기)

클라우드 응용 프로그램을 빌드할 때 일반적인 과제는 개발자 워크스테이션이나 소스 제어에 로컬로 저장하지 않고 다양한 서비스를 인증하기 위해 코드의 자격 증명을 안전하게 관리하는 방법입니다. *Azure에 대한 관리되는 ID는* Azure AD 내에서 자동으로 관리되는 ID를 제공하여 Azure Active Directory(Azure AD)의 모든 리소스에 대해 이 문제를 해결합니다. 코드에 저장된 자격 증명 없이 키 볼트를 포함하여 Azure AD 인증을 지원하는 모든 서비스에 대해 인증하기 위해 서비스의 ID를 사용할 수 있습니다.

*Azure 리소스에 대한 관리되는 ID는* Azure 구독에 대한 Azure AD를 사용하면 무료입니다. 추가 비용은 없습니다.

> [!NOTE]
> *Azure에 대한 관리ID는* MSI(관리 서비스 ID)로 알려진 서비스의 새 이름입니다.

## <a name="concepts"></a>개념

Azure에 대한 관리되는 ID는 다음과 같은 몇 가지 주요 개념을 기반으로 합니다.

- **클라이언트 ID** - 초기 프로비저닝 중에 응용 프로그램 및 서비스 주체에 연결된 Azure AD에서 생성된 고유 식별자입니다(응용 [프로그램 ID](/azure/active-directory/develop/developer-glossary#application-id-client-id)참조).

- **보안 주체 ID** - Azure 리소스에 대한 역할 기반 액세스를 부여하는 데 사용되는 관리 ID에 대한 서비스 주체 개체의 개체 ID입니다.

- **서비스 주체** - 지정된 테넌트에서 AAD 응용 프로그램의 투영을 나타내는 Azure Active Directory [개체(서비스 주체](../active-directory/develop/developer-glossary.md#service-principal-object)참조).

두 가지 종류의 관리 ID가 있습니다.

- Azure 서비스 인스턴스에서 **시스템 할당된 관리 되는 ID를** 직접 사용할 수 있습니다.  시스템 할당된 ID의 수명 주기는 사용하도록 설정된 Azure 서비스 인스턴스에 고유합니다.
- **사용자 할당 관리 ID**는 독립 실행형 Azure 리소스로 생성됩니다. ID는 하나 이상의 Azure 서비스 인스턴스에 할당할 수 있으며 해당 인스턴스의 수명 주기와 별도로 관리됩니다.

관리되는 ID 형식간의 차이점을 자세히 이해하려면 [Azure 리소스에 대한 관리ID가 어떻게 작동합니까?](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work)

## <a name="supported-scenarios-for-service-fabric-applications"></a>서비스 패브릭 응용 프로그램에 대한 지원되는 시나리오

서비스 패브릭에 대한 관리되는 ID는 Azure 배포 서비스 패브릭 클러스터에서만 지원되며 Azure 리소스로 배포된 응용 프로그램에만 지원됩니다. Azure 리소스로 배포되지 않은 응용 프로그램은 ID를 할당할 수 없습니다. 개념적으로 말하자면 Azure Service Fabric 클러스터에서 관리되는 ID에 대한 지원은 다음 두 단계로 구성됩니다.

1. 응용 프로그램 리소스에 하나 이상의 관리되는 ID를 할당합니다. 응용 프로그램에는 단일 시스템 할당 ID 및/또는 최대 32개의 사용자 할당 ID가 각각 할당될 수 있습니다.

2. 응용 프로그램의 정의 내에서 응용 프로그램에 할당된 ID 중 하나를 응용 프로그램을 포함하는 개별 서비스에 매핑합니다.

응용 프로그램의 시스템 할당 ID는 해당 응용 프로그램에 고유합니다. 사용자 할당된 ID는 여러 응용 프로그램에 할당될 수 있는 독립 실행형 리소스입니다. 응용 프로그램 내에서 단일 ID(시스템 할당 또는 사용자 할당 여부)를 응용 프로그램의 여러 서비스에 할당할 수 있지만 각 개별 서비스에는 하나의 ID만 할당할 수 있습니다. 마지막으로 이 기능에 액세스하려면 서비스에 ID를 명시적으로 할당해야 합니다. 실제로 응용 프로그램의 ID를 구성 서비스에 매핑하면 응용 프로그램 내에서 격리할 수 있습니다.  

현재 이 미리 보기 기능에는 다음 시나리오가 지원됩니다.

- 하나 이상의 서비스와 하나 이상의 할당된 ID를 사용 하 여 새 응용 프로그램 배포

- Azure 리소스에 액세스하기 위해 하나 이상의 관리되는 ID를 기존(Azure 배포) 응용 프로그램에 할당합니다.

다음 시나리오는 지원되지 않거나 권장되지 않습니다. 이러한 작업은 차단되지 않을 수 있지만 응용 프로그램에서 가동 중단이 발생할 수 있습니다.

- 응용 프로그램에 할당된 ID를 제거하거나 변경합니다. 변경해야 하는 경우 별도의 배포를 제출하여 먼저 새 ID 할당을 추가한 다음 이전에 할당된 배포를 제거합니다. 기존 응용 프로그램에서 ID를 제거하면 응용 프로그램을 업그레이드할 수 없는 상태로 두는 등 바람직하지 않은 영향을 미칠 수 있습니다. ID를 제거해야 하는 경우 응용 프로그램을 완전히 삭제해도 안전합니다. 이렇게 하면 응용 프로그램과 연결된 시스템 할당 ID(정의된 경우)가 삭제되고 응용 프로그램에 할당된 사용자 할당 ID와의 연결이 제거됩니다.

- 관리되는 ID에 대한 서비스 패브릭 지원은 현재 [AzureServiceTokenProvider에](../key-vault/service-to-service-authentication.md)통합되지 않습니다. 통합은 관리되는 ID 기능에 대한 미리 보기 기간이 끝날 때까지 달성됩니다.

>
> [!NOTE]
>
> 이 기능은 미리 보기 상태입니다. 자주 변경될 수 있으며 프로덕션 배포에 적합하지 않을 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [관리되는 ID 지원을 통해 새 Azure 서비스 패브릭 클러스터 배포](./configure-new-azure-service-fabric-enable-managed-identity.md)
- [기존 Azure 서비스 패브릭 클러스터에서 관리되는 ID 지원 활성화](./configure-existing-cluster-enable-managed-identity-token-service.md)
- [시스템 할당된 관리되는 ID를 사용 하 고 Azure 서비스 패브릭 응용 프로그램 배포](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
- [사용자 할당된 관리되는 ID를 사용 하 고 Azure 서비스 패브릭 응용 프로그램 배포](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
- [서비스 코드에서 서비스 패브릭 응용 프로그램의 관리되는 ID 활용](./how-to-managed-identity-service-fabric-app-code.md)
- [Azure 서비스 패브릭 응용 프로그램 액세스 권한을 다른 Azure 리소스에 부여합니다.](./how-to-grant-access-other-resources.md)
- [응용 프로그램 비밀을 KeyVault 참조로 선언하고 사용](./service-fabric-keyvault-references.md)
