---
author: blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 01/09/2020
ms.author: larryfr
ms.openlocfilehash: 8fd774f8a3a73ceaffa7902b35e1b1dff12ef5af
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "75893979"
---
작업 영역 또는 작업 영역에서 사용 하는 리소스를 Azure Machine Learning 만들 때 다음과 같은 오류 메시지가 표시 될 수 있습니다.

* `No registered resource provider found for location {location}`
* `The subscription is not registered to use namespace {resource-provider-namespace}`

대부분의 리소스 공급자는 자동으로 등록 되지만 모두는 그렇지 않습니다. 이 메시지가 표시 되 면 언급 된 공급자를 등록 해야 합니다.

리소스 공급자를 등록 하는 방법에 대 한 자세한 내용은 [리소스 공급자 등록에 대 한 오류 해결](../articles/azure-resource-manager/templates/error-register-resource-provider.md)을 참조 하세요.