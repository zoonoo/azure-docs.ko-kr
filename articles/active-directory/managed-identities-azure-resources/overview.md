---
title: Azure 리소스에 대한 관리 ID
description: Azure 리소스에 대한 관리 ID 개요
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 0232041d-b8f5-4bd2-8d11-27999ad69370
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: overview
ms.custom: mvc
ms.date: 10/23/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: d70dfceb0101c4f6dbd76f3c6b34d85e5255aa72
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "60290762"
---
# <a name="what-is-managed-identities-for-azure-resources"></a>Azure 리소스에 대한 관리 ID란?

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

클라우드 애플리케이션을 빌드할 때 일반적으로 직면하는 난관 중 하나는 코드에서 클라우드 서비스에 인증하는 데 사용되는 자격 증명을 관리하는 방법입니다. 자격 증명을 안전하게 보호하는 것이 중요합니다. 자격 증명이 개발자 워크스테이션에 표시되지 않고 소스 제거에 체크 인되지 않는 것이 가장 좋습니다. Azure Key Vault를 사용하면 자격 증명, 비밀 및 기타 키를 안전하게 저장할 수 있지만, 자격 증명/키/비밀을 검색하려면 코드가 Key Vault에 인증해야 합니다. 

Azure AD(Azure Active Directory)의 Azure 리소스에 대한 관리 ID 기능은 이 문제를 해결합니다. 이 기능은 Azure AD에서 자동으로 관리되는 ID를 Azure 서비스에 제공합니다. 이 ID를 사용하면 Key Vault를 비롯하여 Azure AD 인증을 지원하는 모든 서비스에 인증할 수 있으므로 코드에 자격 증명이 필요 없습니다.

Azure 리소스에 대한 관리 ID 기능은 Azure 구독용 Azure AD에 무료로 제공됩니다. 추가 비용은 없습니다.

> [!NOTE]
> Azure 리소스에 대한 관리 ID는 이전에 MSI(관리 서비스 ID)로 알려진 서비스에 대한 새 이름입니다.

## <a name="terminology"></a>용어

다음 용어는 Azure 리소스 문서 세트의 관리 ID 전체에서 사용됩니다.

- **클라이언트 ID** - 초기 프로비저닝 중에 애플리케이션과 서비스 주체에 연결된, Azure AD에서 생성된 고유 식별자입니다.
- **보안 주체 ID** - Azure 리소스에 대한 역할 기반 액세스 권한을 부여하는 데 사용되는 관리 ID에 대한 서비스 주체 개체의 개체 ID입니다.
- **IMDS(Azure Instance Metadata Service)** - Azure Resource Manager를 통해 생성된 모든 IaaS VM에서 액세스할 수 있는 REST 엔드포인트입니다. 이 엔드포인트는 VM 내에서만 액세스할 수 있는, 라우팅이 불가능한 잘 알려진 IP 주소(169.254.169.254)에서 사용할 수 있습니다.

## Azure 리소스에 대한 관리 ID는 어떻게 작동하나요?<a name="how-does-it-work"></a>

두 가지 종류의 관리 ID가 있습니다.

- **시스템 할당 관리 ID**는 Azure 서비스 인스턴스에서 직접 사용하도록 설정됩니다. ID를 사용하도록 설정하면 Azure는 인스턴스의 구독에서 신뢰하는 Azure AD 테넌트에 인스턴스의 ID를 만듭니다. ID가 만들어지면 자격 증명이 인스턴스에 프로비전됩니다. 시스템 할당 ID의 수명 주기는 사용하도록 설정된 Azure 서비스 인스턴스와 직접적으로 연관됩니다. 인스턴스가 삭제되면 Azure는 Azure AD에서 자격 증명과 ID를 자동으로 정리합니다.
- **사용자 할당 관리 ID**는 독립 실행형 Azure 리소스로 생성됩니다. 만들기 프로세스를 통해 Azure는 사용 중인 구독에서 신뢰하는 Azure AD 테넌트에 ID를 만듭니다. 생성된 ID는 하나 이상의 Azure 서비스 인스턴스에 할당할 수 있습니다. 사용자 할당 ID의 수명 주기는 할당된 Azure 서비스 인스턴스의 수명 주기와 별도로 관리됩니다.

내부적으로 관리 ID는 Azure 리소스에만 사용할 수 있도록 잠긴 특별한 유형의 서비스 주체입니다. 관리 ID가 삭제되면 해당하는 서비스 주체가 자동으로 제거됩니다. 

코드가 관리 ID를 사용하여 Azure AD 인증을 지원하는 서비스용 액세스 토큰을 요청할 수 있습니다. 서비스 인스턴스가 사용하는 자격 증명 롤링은 Azure에서 처리합니다. 

다음 다이어그램은 관리 서비스 ID가 Azure VM(가상 머신)에서 작동하는 방식을 보여줍니다.

![관리 서비스 ID 및 Azure VM](media/overview/msi-vm-vmextension-imds-example.png)

|  자산    | 시스템 할당 관리 ID | 사용자 할당 관리 ID |
|------|----------------------------------|--------------------------------|
| 만들기 |  Azure 리소스의 일부로 생성됨(예: Azure 가상 머신 또는 Azure App Service) | 독립 실행형 Azure 리소스로 생성됨 |
| 수명 주기 | 관리 ID를 만드는 데 사용된 Azure 리소스와 공유되는 수명 주기입니다. <br/> 부모 리소스를 삭제하면 관리 ID도 삭제됩니다. | 독립적인 수명 주기입니다. <br/> 명시적으로 삭제되어야 합니다. |
| Azure 리소스 전체에서 공유 | 공유할 수 없습니다. <br/> 단일 Azure 리소스하고만 연결할 수 있습니다. | 공유할 수 있습니다. <br/> 동일한 사용자 할당 관리 ID를 둘 이상의 Azure 리소스와 연결할 수 있습니다. |
| 일반 사용 예 | 단일 Azure 리소스에 포함된 워크로드 <br/> 독립적인 ID가 필요한 워크로드 <br/> 예를 들어 단일 가상 머신에서 실행되는 애플리케이션 | 여러 리소스에서 실행되며 단일 ID를 공유할 수 있는 워크로드입니다. <br/> 프로비전 흐름에 보안 리소스 사전 승인이 필요한 워크로드입니다. <br/> 리소스가 자주 재활용되지만 권한이 알관적으로 유지되어야 하는 워크로드입니다. <br/> 예를 들어 여러 가상 머신이 동일한 리소스에 액세스해야 하는 워크로드가 있습니다. | 

### <a name="how-a-system-assigned-managed-identity-works-with-an-azure-vm"></a>시스템 할당 관리 ID가 Azure VM에서 작동하는 방식

1. Azure Resource Manager가 VM에서 시스템 할당 관리 ID를 사용하도록 설정하라는 메시지를 받습니다.
2. Azure Resource Manager가 Azure AD에 VM ID에 대한 서비스 주체를 만듭니다. 서비스 주체는 구독이 신뢰하는 Azure AD 테넌트에 생성됩니다.
3. Azure Resource Manager가 VM에서 ID를 구성합니다.
    1. Azure Instance Metadata Service ID 엔드포인트를 서비스 주체 클라이언트 ID 및 인증서로 업데이트합니다.
    1. VM 확장(2019년 1월에 사용 중단될 예정)을 프로비전하고 서비스 주체 클라이언트 ID 및 인증서를 추가합니다. (이 단계는 사용 중단될 예정입니다.)
4. VM에 ID가 생긴 후에는 서비스 주체 정보를 사용하여 Azure 리소스에 대한 VM 액세스 권한을 부여합니다. Azure Resource Manager를 호출하려면 Azure AD에서 RBAC(역할 기반 액세스 제어)를 사용하여 VM 서비스 주체에 적절한 역할을 할당합니다. Key Vault를 호출하려면 Key Vault의 특정 비밀 또는 키에 대한 액세스 권한을 코드에 부여합니다.
5. VM에서 실행되는 코드는 Azure Instance Metadata Service 엔드포인트에서 토큰(`http://169.254.169.254/metadata/identity/oauth2/token`)을 요청할 수 있으며, VM 내에서만 액세스할 수 있습니다.
    - 리소스 매개 변수가 토큰을 보낼 대상 서비스를 지정합니다. Azure Resource Manager에 인증하려면 `resource=https://management.azure.com/`을 사용합니다.
    - API 버전 매개 변수는 IMDS 버전을 지정하고, api-version=2018-02-01 이상을 사용합니다.

> [!NOTE]
> 코드는 VM 확장 엔드포인트에서 토큰을 요청할 수도 있지만 이 확장은 곧 사용 중단될 예정입니다. VM 확장에 대한 자세한 내용은 [인증을 위해 VM 확장에서 Azure IMDS로 마이그레이션](howto-migrate-vm-extension.md)을 참조하세요.

6. 3단계에서 구성한 클라이언트 ID 및 인증서를 사용하여 5단계에서 지정한 대로 액세스 토큰을 요청하는 Azure AD에 대한 호출이 생성됩니다. Azure AD가 JWT(JSON Web Token) 액세스 토큰을 반환합니다.
7. 코드가 Azure AD 인증을 지원하는 서비스에 대한 호출에서 액세스 토큰을 전송합니다.

### <a name="how-a-user-assigned-managed-identity-works-with-an-azure-vm"></a>사용자 할당 관리 ID가 Azure VM에서 작동하는 방식

1. Azure Resource Manager가 사용자 할당 관리 ID를 만들라는 요청을 받습니다.
2. Azure Resource Manager가 Azure AD에 사용자 할당 관리 ID에 대한 서비스 주체를 만듭니다. 서비스 주체는 구독이 신뢰하는 Azure AD 테넌트에 생성됩니다.
3. Azure Resource Manager가 VM에서 사용자 할당 관리 ID를 구성하라는 요청을 받습니다.
    1. Azure Instance Metadata Service ID 엔드포인트를 사용자 할당 관리 ID 서비스 주체 클라이언트 ID 및 인증서로 업데이트합니다.
    1. VM 확장을 프로비전하고, 사용자 할당 관리 ID 서비스 주체 클라이언트 ID 및 인증서를 추가합니다. (이 단계는 사용 중단될 예정입니다.)
4. 사용자 할당 관리 ID가 생성된 후에는 서비스 주체 정보를 사용하여 Azure 리소스에 대한 ID 액세스 권한을 부여합니다. Azure Resource Manager를 호출하려면 Azure AD에서 RBAC를 사용하여 사용자 할당 ID의 서비스 주체에 적절한 역할을 할당합니다. Key Vault를 호출하려면 Key Vault의 특정 비밀 또는 키에 대한 액세스 권한을 코드에 부여합니다.

   > [!Note]
   > 이 단계를 3단계 전에 수행할 수도 있습니다.

5. VM에서 실행되는 코드는 Azure Instance Metadata Service ID 엔드포인트에서 토큰(`http://169.254.169.254/metadata/identity/oauth2/token`)을 요청할 수 있으며, VM 내에서만 액세스할 수 있습니다.
    - 리소스 매개 변수가 토큰을 보낼 대상 서비스를 지정합니다. Azure Resource Manager에 인증하려면 `resource=https://management.azure.com/`을 사용합니다.
    - 클라이언트 ID 매개 변수는 토큰이 요청되는 ID를 지정합니다. 이 값은 단일 VM에 사용자 할당 ID가 두 개 이상 있을 때 분명히 하기 위해 필요합니다.
    - API 버전 매개 변수는 Azure Instance Metadata Service 버전을 지정합니다. `api-version=2018-02-01` 이상을 사용하세요.

> [!NOTE]
> 코드는 VM 확장 엔드포인트에서 토큰을 요청할 수도 있지만 이 확장은 곧 사용 중단될 예정입니다. VM 확장에 대한 자세한 내용은 [인증을 위해 VM 확장에서 Azure IMDS로 마이그레이션](howto-migrate-vm-extension.md)을 참조하세요.

6. 3단계에서 구성한 클라이언트 ID 및 인증서를 사용하여 5단계에서 지정한 대로 액세스 토큰을 요청하는 Azure AD에 대한 호출이 생성됩니다. Azure AD가 JWT(JSON Web Token) 액세스 토큰을 반환합니다.
7. 코드가 Azure AD 인증을 지원하는 서비스에 대한 호출에서 액세스 토큰을 전송합니다.

## <a name="how-can-i-use-managed-identities-for-azure-resources"></a>Azure 리소스에 대한 관리 ID를 사용하는 방법

관리 ID를 사용하여 Azure 리소스에 액세스하는 방법을 알아보려면 다음 자습서를 진행하세요.

> [!NOTE]
> 몇 가지 지원되는 시나리오의 자세한 비디오 연습을 비롯한 관리 ID에 대한 자세한 내용은 [Microsoft Azure 리소스에 대한 관리 ID 구현](https://www.pluralsight.com/courses/microsoft-azure-resources-managed-identities-implementing) 과정을 확인하세요.

Windows VM에서 관리 ID를 사용하는 방법:

* [Azure Data Lake Store에 액세스](tutorial-windows-vm-access-datalake.md)
* [Azure Resource Manager 액세스](tutorial-windows-vm-access-arm.md)
* [Azure SQL에 액세스](tutorial-windows-vm-access-sql.md)
* [액세스 키를 사용하여 Azure Storage에 액세스](tutorial-windows-vm-access-storage.md)
* [공유 액세스 서명을 사용하여 Azure Storage에 액세스](tutorial-windows-vm-access-storage-sas.md)
* [Azure Key Vault를 사용하여 비 Azure AD 리소스에 액세스](tutorial-windows-vm-access-nonaad.md)

Linux VM에서 관리 ID를 사용하는 방법:

* [Azure Data Lake Store에 액세스](tutorial-linux-vm-access-datalake.md)
* [Azure Resource Manager 액세스](tutorial-linux-vm-access-arm.md)
* [액세스 키를 사용하여 Azure Storage에 액세스](tutorial-linux-vm-access-storage.md)
* [공유 액세스 서명을 사용하여 Azure Storage에 액세스](tutorial-linux-vm-access-storage-sas.md)
* [Azure Key Vault를 사용하여 비 Azure AD 리소스에 액세스](tutorial-linux-vm-access-nonaad.md)

다른 Azure 서비스에서 관리 ID를 사용하는 방법:

* [Azure App Service](/azure/app-service/overview-managed-identity)
* [Azure Functions](/azure/app-service/overview-managed-identity)
* [Azure Logic Apps](/azure/logic-apps/create-managed-service-identity)
* [Azure Service Bus](../../service-bus-messaging/service-bus-managed-service-identity.md)
* [Azure Event Hubs](../../event-hubs/event-hubs-managed-service-identity.md)
* [Azure API Management](../../api-management/api-management-howto-use-managed-service-identity.md)
* [Azure Container Instances](../../container-instances/container-instances-managed-identity.md)

## Azure 서비스에서 어떤 기능을 지원하나요?<a name="which-azure-services-support-managed-identity"></a>

Azure 리소스에 대한 관리 ID는 Azure AD 인증을 지원하는 서비스를 인증하는 데 사용할 수 있습니다. Azure 리소스에 대한 관리 ID 기능을 지원하는 Azure 서비스 목록은 [Azure 리소스에 대한 관리 ID를 지원하는 서비스](services-support-msi.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

다음 빠른 시작으로 Azure 리소스에 대한 관리 ID 기능을 시작하세요.

* [Windows VM 시스템 할당 관리 ID를 사용하여 Resource Manager에 액세스](tutorial-windows-vm-access-arm.md)
* [Linux VM 시스템 할당 관리 ID를 사용하여 Resource Manager에 액세스](tutorial-linux-vm-access-arm.md)
