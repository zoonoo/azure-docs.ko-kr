---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/12/2019
ms.author: pafarley
ms.openlocfilehash: a37087c90b6c9b3629402c7a8c2fa5861e46ae9a
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67592520"
---
Form Recognizer를 사용할 수 있는 액세스 권한이 부여되면 몇 가지 링크와 리소스가 포함된 환영 이메일을 받을 수 있습니다. 해당 메시지의 “Azure Portal” 링크를 사용하여 Azure Portal을 열고 Form Recognizer 리소스를 만듭니다. **만들기** 창에서 다음 정보를 제공합니다.

|    |    |
|--|--|
| **Name** | 리소스에 대한 설명이 포함된 이름입니다. 설명이 포함된 이름(예: *MyNameFormRecognizer*)을 사용하는 것이 좋습니다. |
| **구독** | 액세스 권한이 부여된 Azure 구독을 선택합니다. |
| **위치**: | Cognitive Service 인스턴스의 위치입니다. 다른 위치를 사용하면 대기 시간이 발생할 수 있지만 리소스의 런타임 가용성에는 영향을 주지 않습니다. |
| **가격 책정 계층** | 리소스 비용은 선택한 가격 책정 계층과 사용량에 따라 달라집니다. 자세한 내용은 API [가격 책정 세부 정보](https://azure.microsoft.com/pricing/details/cognitive-services/)를 참조하세요.
| **리소스 그룹** | 리소스가 포함될 [Azure 리소스 그룹](https://docs.microsoft.com/azure/architecture/cloud-adoption/governance/resource-consistency/azure-resource-access#what-is-an-azure-resource-group)입니다. 새 그룹을 만들거나 기존 그룹에 추가할 수 있습니다. |

> [!IMPORTANT]
> 일반적으로 Azure Portal에서 Cognitive Service 리소스를 만들 때는 다중 서비스 구독 키(여러 Cognitive Service에서 사용) 또는 단일 서비스 구독 키(특정 Cognitive Service에서만 사용)를 만들 수 있는 옵션이 제공됩니다. 그러나 Form Recognizer는 미리 보기 릴리스이므로 다중 서비스 구독에 포함되지 않으며 환영 이메일에 제공된 링크를 사용하지 않으면 단일 서비스 구독을 만들 수 없습니다.

Form Recognizer 리소스의 배포가 완료되면 포털의 **모든 리소스** 목록에서 해당 리소스를 찾아 선택합니다. 그런 다음, **키** 탭을 선택하여 구독 키를 봅니다. 두 키 중 하나에서 리소스에 대한 앱 액세스 권한을 부여합니다. **키 1**의 값을 복사합니다. 다음 섹션에서 사용하게 됩니다.