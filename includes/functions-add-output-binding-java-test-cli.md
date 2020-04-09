---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 6a359cdd44cc0c0cfbd93bd23b69a67a641c7fbb
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673254"
---
## <a name="update-the-tests"></a>테스트 업데이트

또한 원형은 테스트 세트를 만들기 때문에 `run` 메서드 시그니처에서 새 `msg` 매개 변수를 처리하도록 이러한 테스트를 업데이트해야 합니다.  

_src/test/java_에서 테스트 코드의 위치로 이동하여 *Function.java* 프로젝트 파일을 열고, `//Invoke` 아래의 코드 줄을 다음 코드로 바꿉니다.

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/test/java/com/function/FunctionTest.java" range="48-50":::