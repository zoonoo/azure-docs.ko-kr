---
title: 포함 파일
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 06/07/2021
ms.author: tamram
ms.openlocfilehash: 7f2b15a57b42f87e749060f510dafff732a1d9e0
ms.sourcegitcommit: f9e368733d7fca2877d9013ae73a8a63911cb88f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111904514"
---
Azure RBAC 역할을 보안 주체에 할당하기 전에 보안 주체에게 부여해야 하는 액세스 범위를 결정합니다. 모범 사례에 따르면 항상 가능한 가장 좁은 범위만 부여하는 것이 가장 좋습니다. 더 광범위한 범위에서 정의된 Azure RBAC 역할은 그 아래에 있는 리소스에 상속됩니다.

다음 목록에서는 Azure 큐 리소스에 대한 액세스 범위를 가장 좁은 범위에서 시작하여 지정할 수 있는 수준을 설명합니다.

- **개별 큐.** 이 범위의 역할 할당은 큐의 메시지와 큐 속성 및 메타데이터에 적용됩니다.
- **스토리지 계정.** 이 범위의 역할 할당은 모든 큐와 해당 메시지에 적용됩니다.
- **리소스 그룹.** 이 범위의 역할 할당은 리소스 그룹의 모든 스토리지 계정에 있는 모든 큐에 적용됩니다.
- **구독.** 이 범위의 역할 할당은 구독의 모든 리소스 그룹에 있는 모든 스토리지 계정의 모든 큐에 적용됩니다.
- **관리 그룹.** 이 범위의 역할 할당은 관리 그룹의 모든 구독에 있는 모든 리소스 그룹의 모든 스토리지 계정에 있는 모든 큐에 적용됩니다.

Azure RBAC 역할 할당 범위에 대한 자세한 내용은 [Azure RBAC 범위 이해](../articles/role-based-access-control/scope-overview.md)를 참조하세요.
