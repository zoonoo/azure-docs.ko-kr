---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/02/2020
ms.author: trbye
ms.openlocfilehash: fdcb5bb57afe145b57cb1223242d4d5625a2dfb7
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400717"
---
음성 구성 개체를 만들려면 음성 리소스 구독 키와 지역이 필요합니다. 음성 인식기 개체를 인스턴스화하려면 구성 개체가 필요합니다.

인식기 인스턴스는 음성을 인식하는 여러 가지 방법을 제공합니다. 이 예제에서 음성은 한 번 인식됩니다. 이 기능을 사용하면 Speech Service에서 사용자가 인식을 위해 단일 구를 보내고, 구가 식별되고 나면 음성 인식이 중지됨을 알 수 있습니다. 결과가 생성되면 코드에서 인식 이유를 콘솔에 기록합니다.

> [!TIP]
> Speech SDK는 기본적으로 언어에 `en-us`를 사용하여 인식합니다. 원본 언어 선택에 대한 자세한 내용은 [음성 텍스트 변환에 대한 원본 언어 지정](../../../how-to-specify-source-language.md)을 참조하세요.