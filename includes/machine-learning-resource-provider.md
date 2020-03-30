---
author: blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 01/09/2020
ms.author: larryfr
ms.openlocfilehash: 8fd774f8a3a73ceaffa7902b35e1b1dff12ef5af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "75893979"
---
Azure 기계 학습 작업 영역 또는 작업 영역에서 사용 하는 리소스를 만들 때 다음 메시지와 비슷한 오류가 나타날 수 있습니다.

* `No registered resource provider found for location {location}`
* `The subscription is not registered to use namespace {resource-provider-namespace}`

대부분의 리소스 공급자는 자동으로 등록되지만 전부는 아닙니다. 이 메시지가 수신되면 언급된 공급자를 등록해야 합니다.

리소스 공급자 등록에 대한 자세한 내용은 [리소스 공급자 등록에 대한 오류 해결을](../articles/azure-resource-manager/templates/error-register-resource-provider.md)참조하십시오.