---
title: Azure Event Hubs에 대한 액세스 권한 부여
description: 이 문서에서는 Azure Event Hubs 리소스에 대한 액세스 권한을 부여하기 위한 다양한 옵션에 대한 정보를 제공합니다.
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: spelluru
ms.openlocfilehash: f44be4e1d3d1186f0122bd4669ae800ab42e31d6
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521302"
---
# <a name="authorize-access-to-azure-event-hubs"></a>Azure Event Hubs에 대한 액세스 권한 부여
이벤트 허브에서 이벤트/데이터를 게시하거나 사용할 때마다 클라이언트가 Event Hubs 리소스에 액세스하려고 합니다. 서비스가 클라이언트가 데이터를 게시/사용하는 데 필요한 권한이 있는지 확인할 수 있도록 보안 리소스에 대한 모든 요청이 승인되어야 합니다. 

Azure Event Hubs는 보안 리소스에 대한 액세스 권한을 부여하기 위한 다음 옵션을 제공합니다.

- Azure Active Directory
- 공유 액세스 서명

> [!NOTE]
> 이 문서는 이벤트 허브와 [아파치 카프카](event-hubs-for-kafka-ecosystem-overview.md) 시나리오 모두에 적용됩니다. 

## <a name="azure-active-directory"></a>Azure Active Directory
이벤트 허브 리소스에 대한 Azure Active Directory(Azure AD) 통합은 클라이언트의 리소스 에 대한 액세스에 대한 세분화된 제어를 위한 역할 기반 액세스 제어(RBAC)를 제공합니다. RBAC(역할 기반 액세스 제어)를 사용하여 사용자, 그룹 또는 응용 프로그램 서비스 주체일 수 있는 보안 주체에 권한을 부여할 수 있습니다. 보안 주체는 Azure AD에서 인증되어 OAuth 2.0 토큰을 반환합니다. 토큰을 사용하여 Event Hubs 리소스에 액세스하는 요청을 승인할 수 있습니다.

Azure AD인증에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Azure Active 디렉터리를 사용하여 Azure 이벤트 허브에 대한 요청을 인증합니다.](authenticate-application.md)
- [Azure Active Directory를 사용하여 이벤트 허브 리소스에 대한 액세스 권한을 부여합니다.](authorize-access-azure-active-directory.md)

## <a name="shared-access-signatures"></a>공유 액세스 서명 
이벤트 허브 리소스에 대한 공유 액세스 서명(SAS)은 이벤트 허브 리소스에 대한 제한된 위임된 액세스를 제공합니다. 서명이 유효한 시간 간격또는 서명이 부여하는 사용 권한에 제약 조건을 추가하면 리소스를 유연하고 관리할 수 있습니다. 자세한 내용은 [SAS(공유 액세스 서명)를 사용하여 인증을](authenticate-shared-access-signature.md)참조하십시오. 

Azure AD에서 반환하는 OAuth 2.0 토큰을 사용하여 사용자 또는 응용 프로그램에 권한을 부여하면 SAS(공유 액세스 서명)를 통해 뛰어난 보안과 사용 편의성을 제공합니다. Azure AD를 사용하면 코드와 함께 액세스 토큰을 저장하고 잠재적인 보안 취약점을 위험에 빠뜨릴 필요가 없습니다. SAS(공유 액세스 시그니처)를 계속 사용하여 이벤트 허브 리소스에 대한 세분화된 액세스 권한을 부여할 수 있지만 Azure AD는 SAS 토큰을 관리하거나 손상된 SAS를 취소할 필요 없이 유사한 기능을 제공합니다. 

기본적으로 모든 이벤트 허브 리소스는 보호되며 계정 소유자만 사용할 수 있습니다. 위에 설명된 권한 부여 전략을 사용하여 클라이언트에게 이벤트 허브 리소스에 대한 액세스 권한을 부여할 수 있지만. 가능한 한 보안 및 사용 편의성을 극대화하기 위해 Azure AD를 사용하는 것이 좋습니다.

SAS를 사용한 권한 부여에 대한 자세한 내용은 [공유 액세스 서명을 사용하여 이벤트 허브 리소스에 대한 액세스 권한 부여를](authorize-access-shared-access-signature.md)참조하십시오.

## <a name="next-steps"></a>다음 단계
- GitHub 리포지토리에 게시된 [RBAC 샘플을](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac) 검토합니다. 
- 다음 문서를 참조하세요.
    - [Azure Active Directory를 사용하여 응용 프로그램에서 Azure 이벤트 허브에 대한 요청을 인증합니다.](authenticate-application.md)
    - [Azure Active Directory를 사용하여 관리되는 ID를 인증하여 이벤트 허브 리소스에 액세스](authenticate-managed-identity.md)
    - [공유 액세스 서명을 사용하여 Azure 이벤트 허브에 대한 요청을 인증합니다.](authenticate-shared-access-signature.md)
    - [Azure Active Directory를 사용하여 이벤트 허브 리소스에 대한 액세스 권한 부여](authorize-access-azure-active-directory.md)
    - [공유 액세스 서명을 사용하여 이벤트 허브 리소스에 대한 액세스 권한 부여](authorize-access-shared-access-signature.md)

