---
title: Service Fabric 관리 Id 개요
description: 이 문서는 Azure Service Fabric에 대 한 관리 되는 Id 및 해당 응용 프로그램에 대 한 개요입니다.
ms.topic: conceptual
ms.date: 12/09/2019
ms.openlocfilehash: dc7dafa59596537456accde66e878c06f9e5ca23
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/28/2019
ms.locfileid: "75528150"
---
# <a name="managed-identity-for-service-fabric-application-preview"></a>Service Fabric 응용 프로그램에 대 한 관리 Id (미리 보기)

클라우드 애플리케이션을 빌드할 때 일반적으로 직면하는 난관 중 하나는 코드에서 클라우드 서비스에 인증하는 데 사용되는 자격 증명을 관리하는 방법입니다. 자격 증명을 안전 하 게 유지 하는 것은 개발자 워크스테이션에 표시 되지 않고 소스 제어로 체크 인하지 않기 때문에 중요 한 작업입니다. Azure AD (Azure Active Directory)에서 Azure 리소스에 대 한 관리 되는 Id 기능을 통해이 문제를 해결할 것입니다. 이 기능은 Azure AD에서 자동으로 관리되는 ID를 Azure 서비스에 제공합니다. 이 ID를 사용하면 Key Vault를 비롯하여 Azure AD 인증을 지원하는 모든 서비스에 인증할 수 있으므로 코드에 자격 증명이 필요 없습니다.

Azure 리소스에 대 한 관리 되는 Id 기능은 azure 구독에 azure AD에서 무료로 제공 됩니다. 추가 비용은 없습니다.

> [!NOTE]
> Azure 리소스에 대 한 관리 Id는 이전에 MSI (관리 서비스 ID)로 알려진 서비스의 새 이름입니다.

## <a name="terminology"></a>용어

Azure 리소스에 대 한 관리 되는 Id 설명서 집합 전체에서 사용 되는 용어는 다음과 같습니다.

- **클라이언트 ID** -초기 프로 비전 중에 응용 프로그램 및 서비스 주체에 연결 된 Azure AD에서 생성 되는 고유 식별자 ( [응용 프로그램 ID](/azure/active-directory/develop/developer-glossary#application-id-client-id)도 참조)

- **보안 주체 id** -Azure 리소스에 대 한 역할 기반 액세스 권한을 부여 하는 데 사용 되는 관리 id의 서비스 주체 개체에 대 한 개체 id입니다.

- **서비스 사용자** -지정 된 테 넌 트에서 AAD 응용 프로그램의 프로젝션을 나타내는 Azure Active Directory 개체입니다 ( [서비스 주체](../active-directory/develop/developer-glossary.md#service-principal-object)도 참조).


## <a name="about-managed-identities-in-azure"></a>Azure의 관리 되는 Id 정보

- [Azure의 MI (관리 Id) 유형](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview#how-does-the-managed-identities-for-azure-resources-work)

- [Azure에서 시스템 할당 관리 Id가 작동 하는 방식](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview#how-a-system-assigned-managed-identity-works-with-an-azure-vm)

- [Azure에서 사용자 정의 관리 Id (MI)는 어떻게 작동 하나요?](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview#how-a-user-assigned-managed-identity-works-with-an-azure-vm)


## <a name="supported-scenarios-for-service-fabric-applications"></a>Service Fabric 응용 프로그램에 대해 지원 되는 시나리오

Service Fabric에 대 한 관리 id는 azure 리소스로 배포 된 응용 프로그램에 대해서만 Azure에서 배포 된 Service Fabric 클러스터에서 지원 됩니다. Azure 리소스로 배포 되지 않은 응용 프로그램에는 id를 할당할 수 없습니다. 개념적으로 말하면 Azure Service Fabric 클러스터에서 관리 되는 id에 대 한 지원은 다음 두 단계로 구성 됩니다.

1. 응용 프로그램 리소스에 관리 되는 id를 하나 이상 할당 합니다. 응용 프로그램에는 시스템에 할당 된 단일 id 및/또는 최대 32 사용자 할당 id가 각각 할당 될 수 있습니다.

2. 응용 프로그램의 정의 내에서 응용 프로그램에 할당 된 id 중 하나를 응용 프로그램을 구성 하는 개별 서비스에 매핑합니다.

응용 프로그램의 시스템 할당 id는 해당 응용 프로그램에 대해 고유 합니다. 사용자 할당 id는 여러 응용 프로그램에 할당 될 수 있는 독립 실행형 리소스입니다. 응용 프로그램 내에서 단일 id (시스템 할당 또는 사용자 할당 여부)는 응용 프로그램의 여러 서비스에 할당 될 수 있지만 각 개별 서비스에는 하나의 id만 할당할 수 있습니다. 마지막으로, 서비스에이 기능에 대 한 액세스 권한이 있는 id를 명시적으로 할당 해야 합니다. 실제로 응용 프로그램의 id를 해당 구성 서비스에 매핑하면 응용 프로그램 격리를 사용할 수 있습니다. 서비스는 매핑된 id만 사용할 수 있으며, 명시적으로 할당 되지 않은 경우에는 아무 것도 사용할 수 없습니다.  

미리 보기 릴리스에 대해 지원 되는 시나리오 목록은 다음과 같습니다.

   - 하나 이상의 서비스 및 하나 이상의 할당 된 id를 사용 하 여 새 응용 프로그램 배포

   - Azure 리소스에 액세스 하기 위해 기존 응용 프로그램에 관리 되는 id를 하나 이상 할당 합니다. 응용 프로그램은 Azure 리소스 자체로 배포 되어야 합니다.


다음 시나리오는 지원 되지 않거나 권장 되지 않습니다. 이러한 작업은 차단 되지 않을 수 있지만 응용 프로그램에서 중단이 발생할 수 있습니다.

   - 응용 프로그램에 할당 된 id를 제거 하거나 변경 합니다. 변경 해야 하는 경우 별도의 배포를 제출 하 여 먼저 새 id 할당을 추가 하 고 이전에 할당 된 id를 제거 합니다. 기존 응용 프로그램에서 id를 제거 하면 응용 프로그램을 업그레이드할 수 없는 상태로 유지 하는 것을 포함 하 여 원치 않는 결과가 발생할 수 있습니다. Id를 제거 해야 하는 경우 응용 프로그램을 완전히 삭제 하는 것이 안전 합니다. 이는 응용 프로그램과 연결 된 시스템 할당 id (정의 된 경우)를 삭제 하 고 응용 프로그램에 할당 된 사용자 할당 id를 사용 하 여 모든 연결을 제거 합니다.

   - 관리 id에 대 한 SF 지원은 현재 [AzureServiceTokenProvider](../key-vault/service-to-service-authentication.md)에 통합 되지 않습니다. 관리 되는 id 기능에 대 한 미리 보기 기간이 끝나면 통합이 이루어집니다.

>
> [!NOTE]
>
> 이 기능은 미리 보기 상태입니다. 따라서 자주 변경 될 수 있으며 프로덕션 배포에 적합 하지 않을 수 있습니다.

## <a name="next-steps"></a>다음 단계
* [관리 되는 id를 지 원하는 새 Azure Service Fabric 클러스터 배포](./configure-new-azure-service-fabric-enable-managed-identity.md) 
* [기존 Azure Service Fabric 클러스터에서 관리 되는 id 지원 사용](./configure-existing-cluster-enable-managed-identity-token-service.md)
* [시스템 할당 관리 id를 사용 하 여 Azure Service Fabric 응용 프로그램 배포](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [사용자 할당 관리 id를 사용 하 여 Azure Service Fabric 응용 프로그램 배포](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [서비스 코드에서 Service Fabric 응용 프로그램의 관리 되는 id 활용](./how-to-managed-identity-service-fabric-app-code.md)
* [Azure Service Fabric 응용 프로그램에 다른 Azure 리소스에 대 한 액세스 권한 부여](./how-to-grant-access-other-resources.md)
* [KeyVaultReferences로 응용 프로그램 비밀 선언 및 사용](./service-fabric-keyvault-references.md) 
