---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: aeca3550b5fc58694779e7e54ce6ca547ba30e17
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/26/2018
ms.locfileid: "50165921"
---
Azure 저장소의 모든 Blob은 컨테이너에 있어야 합니다. 컨테이너는 Blob 이름의 일부를 형성합니다. 예를 들어 `mycontainer` 은(는) 이러한 샘플 Blob URI에서 컨테이너의 이름입니다.

    https://storagesample.blob.core.windows.net/mycontainer/blob1.txt
    https://storagesample.blob.core.windows.net/mycontainer/photos/myphoto.jpg

컨테이너 이름은 유효한 DNS 이름이어야 하고 다음 명명 규칙을 따라야 합니다.

1. 컨테이너 이름은 문자 또는 숫자로 시작해야 하며 문자, 숫자 및 대시(-) 문자를 포함할 수 있습니다.
2. 컨테이너 이름에서 모든 대시(-) 문자는 문자 또는 숫자 바로 앞뒤에 와야 하며 연속 대시를 사용할 수 없습니다.
3. 컨테이너 이름의 모든 문자는 소문자여야 합니다.
4. 컨테이너 이름의 길이는 3자 이상, 63자 이하여야 합니다.

> [!IMPORTANT]
> 컨테이너의 이름은 항상 소문자여야 합니다. 컨테이너 이름에 대문자를 포함하거나 컨테이너 명명 규칙을 위반하는 경우에 400 오류(잘못된 요청) 메시지를 받을 수 있습니다. 
> 
> 

