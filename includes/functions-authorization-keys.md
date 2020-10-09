---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/06/2020
ms.author: glenga
ms.openlocfilehash: 617608f382d3331e59ae92c9eb272347c736b768
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2020
ms.locfileid: "91828983"
---
함수에서는 키를 사용해 개발 중에 HTTP 함수 엔드포인트 액세스를 더 어렵게 만들 수 있습니다. HTTP 트리거 함수의 HTTP 액세스 수준이 `anonymous`로 설정되지 않는 경우 요청에는 API 액세스 키가 포함되어야 합니다. 

키가 기본 보안 메커니즘을 제공 하는 반면, 프로덕션 환경에서 HTTP 끝점을 보호 하기 위한 추가 옵션을 고려해 야 할 수 있습니다. 예를 들어 공용 앱에 공유 암호를 배포 하는 것은 일반적이 지 않습니다. Public 클라이언트에서 함수를 호출 하는 경우 다른 보안 메커니즘을 구현 하는 것을 고려해 볼 수 있습니다. 자세히 알아보려면 [프로덕션 환경에서 HTTP 엔드포인트 보호](../articles/azure-functions/functions-bindings-http-webhook-trigger.md#secure-an-http-endpoint-in-production)를 참조하세요.

함수 키 값을 갱신 하는 경우 함수를 호출 하는 모든 클라이언트에 업데이트 된 키 값을 수동으로 다시 배포 해야 합니다.  

#### <a name="authorization-scopes-function-level"></a>권한 부여 범위(함수 수준)

함수 수준 키에는 두 가지 액세스 범위가 있습니다.

* **함수**: 정의된 특정 함수에만 적용됩니다. API 키로 사용되면 이 키를 통해 해당 함수에만 액세스할 수 있습니다.

* **호스트**: 호스트 범위가 있는 키를 사용하여 함수 앱 내의 모든 함수에 액세스할 수 있습니다. API 키로 사용되면 이 키를 통해 함수 앱 내의 모든 함수에 액세스할 수 있습니다. 

각 키의 이름은 참조될 수 있도록 지정되며 함수 및 호스트 수준에서는 "default"라는 기본 키가 있습니다. function 키는 호스트 키보다 우선합니다. 두 키가 동일한 이름으로 정의되면 항상 함수 키가 사용됩니다.

#### <a name="master-key-admin-level"></a>마스터 키(관리자 수준) 

각 함수 앱에는 `_master`라는 관리자 수준 호스트 키도 있습니다. 마스터 키는 앱의 모든 함수에 대한 호스트 수준 액세스를 제공할 뿐만 아니라 런타임 REST API에 대한 관리 액세스도 제공합니다. 이 키를 취소할 수 없습니다. 액세스 수준을 `admin`으로 설정하면 요청에서 마스터 키를 사용해야 하며, 다른 키를 사용하면 액세스가 실패합니다.

> [!CAUTION]  
> 함수 앱에서는 마스터 키를 통해 높은 권한이 부여되므로, 이 키를 제3자와 공유하거나 네이티브 클라이언트 애플리케이션에 배포해서는 안 됩니다. 따라서 관리자 액세스 수준을 선택하는 경우 주의해야 합니다.
