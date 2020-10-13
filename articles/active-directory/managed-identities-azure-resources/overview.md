---
title: Azure 리소스에 대한 관리 ID
description: Azure 리소스에 대한 관리 ID 개요
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.assetid: 0232041d-b8f5-4bd2-8d11-27999ad69370
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: overview
ms.custom: mvc
ms.date: 10/06/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 728ca38cc3ef3bf989a75d757c69f7ca1993d82d
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2020
ms.locfileid: "91803122"
---
# <a name="what-are-managed-identities-for-azure-resources"></a>Azure 리소스용 관리 ID란?

개발자는 일반적으로 서로 다른 서비스 간의 통신을 보호하기 위해 비밀과 자격 증명을 관리하는 데 어려움을 겪고 있습니다. Azure에서 관리 ID를 사용하면 개발자가 Azure AD에서 Azure 리소스에 대한 ID를 제공하고 이를 사용하여 Azure AD(Azure Active Directory) 토큰을 가져오는 방식으로 자격 증명을 관리할 필요가 없습니다. 또한 이는 개발자가 자격 증명을 안전한 방식으로 저장할 수 있는 [Azure Key Vault](../../key-vault/general/overview.md)에 액세스하는 데 도움이 됩니다. Azure 리소스에 대한 관리 ID는 Azure AD에서 자동으로 관리되는 ID를 Azure 서비스에 제공하여 이 문제를 해결합니다.

관리 ID를 사용할 수 있는 대상은 무엇일까요?

   > [!VIDEO https://www.youtube.com/embed/5lqayO_oeEo]

관리 ID를 사용하는 경우 얻을 수 있는 몇 가지 혜택은 다음과 같습니다.

- 자격 증명을 관리할 필요가 없으며, 자격 증명에 액세스할 수도 없습니다.
- 관리 ID를 사용하여 Azure Key Vault를 포함한 Azure AD 인증을 지원하는 모든 Azure 서비스에 인증할 수 있습니다.
- 관리 ID는 추가 비용 없이 사용할 수 있습니다.

> [!NOTE]
> Azure 리소스에 대한 관리 ID는 이전에 MSI(관리 서비스 ID)로 알려진 서비스에 대한 새 이름입니다.

## <a name="managed-identity-types"></a>관리 ID 유형

두 가지 종류의 관리 ID가 있습니다.

- **시스템 할당** 일부 Azure 서비스를 사용하면 서비스 인스턴스에서 직접 관리 ID를 사용하도록 설정할 수 있습니다. 시스템이 할당한 관리 ID를 사용하도록 설정하면 해당 서비스 인스턴스의 수명 주기와 연결된 ID가 Azure AD에 만들어집니다. 따라서 리소스가 삭제되면 Azure에서 자동으로 ID를 삭제합니다. 의도적으로 해당 Azure 리소스만 이 ID를 사용하여 Azure AD에서 토큰을 요청할 수 있습니다.
- **사용자 할당** 관리 ID를 독립 실행형 Azure 리소스로 만들 수도 있습니다. [사용자가 할당한 관리 ID를 만들고](how-to-manage-ua-identity-portal.md), 이를 하나 이상의 Azure 서비스 인스턴스에 할당할 수 있습니다. 사용자가 할당한 관리 ID는 이를 사용하는 리소스와 별도로 관리됩니다. </br></br>

  > [!VIDEO https://www.youtube.com/embed/OzqpxeD3fG0]

아래 표에서는 두 가지 유형의 관리 ID 간의 차이점을 보여 줍니다.

|  속성    | 시스템 할당 관리 ID | 사용자 할당 관리 ID |
|------|----------------------------------|--------------------------------|
| 만들기 |  Azure 리소스의 일부로 생성됨(예: Azure 가상 머신 또는 Azure App Service) | 독립 실행형 Azure 리소스로 생성됨 |
| 수명 주기 | 관리 ID를 만드는 데 사용된 Azure 리소스와 공유되는 수명 주기입니다. <br/> 부모 리소스를 삭제하면 관리 ID도 삭제됩니다. | 독립적인 수명 주기. <br/> 명시적으로 삭제되어야 합니다. |
| Azure 리소스 전체에서 공유 | 공유할 수 없습니다. <br/> 단일 Azure 리소스하고만 연결할 수 있습니다. | 공유할 수 있습니다. <br/> 동일한 사용자 할당 관리 ID를 둘 이상의 Azure 리소스와 연결할 수 있습니다. |
| 일반 사용 예 | 단일 Azure 리소스에 포함된 워크로드 <br/> 독립적인 ID가 필요한 워크로드 <br/> 예를 들어 단일 가상 머신에서 실행되는 애플리케이션 | 여러 리소스에서 실행되며 단일 ID를 공유할 수 있는 워크로드입니다. <br/> 프로비전 흐름에 보안 리소스 사전 승인이 필요한 워크로드입니다. <br/> 리소스가 자주 재활용되지만 권한이 알관적으로 유지되어야 하는 워크로드입니다. <br/> 예를 들어 여러 가상 머신이 동일한 리소스에 액세스해야 하는 워크로드가 있습니다. |

>[!IMPORTANT]
>선택한 ID 유형에 관계없이 관리 ID는 Azure 리소스에만 사용할 수 있는 특수 유형의 서비스 주체입니다. 관리 ID가 삭제되면 해당하는 서비스 주체가 자동으로 제거됩니다.

## <a name="how-can-i-use-managed-identities-for-azure-resources"></a>Azure 리소스에 대한 관리 ID를 사용하는 방법

![개발자가 인증 정보를 관리하지 않고 코드에서 리소스에 액세스하기 위해 관리 ID를 사용할 수 있는 방법의 몇 가지 예](media/overview/azure-managed-identities-examples.png)

## <a name="what-azure-services-support-the-feature"></a>Azure 서비스에서 어떤 기능을 지원하나요?<a name="which-azure-services-support-managed-identity"></a>

Azure 리소스에 대한 관리 ID는 Azure AD 인증을 지원하는 서비스를 인증하는 데 사용할 수 있습니다. Azure 리소스에 대한 관리 ID 기능을 지원하는 Azure 서비스 목록은 [Azure 리소스에 대한 관리 ID를 지원하는 서비스](./services-support-managed-identities.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Windows VM 시스템 할당 관리 ID를 사용하여 Resource Manager에 액세스](tutorial-windows-vm-access-arm.md)
* [Linux VM 시스템 할당 관리 ID를 사용하여 Resource Manager에 액세스](tutorial-linux-vm-access-arm.md)
* [App Service 및 Azure Functions에 대한 관리 ID를 사용하는 방법](../../app-service/overview-managed-identity.md)
* [Azure Container Instances에서 관리 ID를 사용하는 방법](../../container-instances/container-instances-managed-identity.md)
* [Microsoft Azure 리소스에 대한 관리 ID 구현](https://www.pluralsight.com/courses/microsoft-azure-resources-managed-identities-implementing)