---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 04/20/2020
ms.author: cephalin
ms.openlocfilehash: c3fa57dd162fbbfbf0d46f73bffc78f279ef2968
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83649142"
---
* **사용자 수준 자격 증명** - 전체 Azure 계정에 대한 자격 증명 집합입니다. 모든 구독에서 Azure 계정에 액세스할 수 있는 권한이 있는 모든 앱의 App Service에 배포하는 데 사용할 수 있습니다. 포털 GUI(예: 앱 [리소스 페이지](../articles/azure-resource-manager/management/manage-resources-portal.md#manage-resources)의 **개요** 및 **속성**)에 표시되는 기본 집합입니다. RBAC(역할 기반 액세스 제어) 또는 공동 관리자 권한을 통해 사용자에게 앱 액세스 권한이 부여되면 해당 사용자는 액세스가 취소될 때까지 자신의 사용자 수준 자격 증명을 사용할 수 있습니다. 이러한 자격 증명을 다른 Azure 사용자와 공유하지 마세요.

* **앱 수준 자격 증명** - 각 앱마다 하나씩의 자격 증명 집합입니다. 해당 앱에만 배포하는 데 사용할 수 있습니다. 각 앱의 자격 증명은 앱 생성 시 자동으로 생성됩니다. 수동으로 구성할 수 없지만, 언제든지 다시 설정할 수 있습니다. RBAC를 통해 사용자에게 앱 수준 자격 증명에 대한 액세스 권한을 부여하려면 해당 사용자가 앱에서 기여자 이상이어야 합니다(웹 사이트 기여자 기본 제공 역할 포함). 읽기 권한자는 게시할 수 없으므로 자격 증명에 액세스할 수 없습니다.