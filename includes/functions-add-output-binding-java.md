---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/18/2020
ms.author: glenga
ms.openlocfilehash: c27f3fea6d2dd8b891220ba06b375b33e6cd950b
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2020
ms.locfileid: "80673464"
---
Java 프로젝트에서 바인딩은 함수 메서드의 바인딩 주석으로 정의됩니다. 그런 다음, *function.json* 파일은 이러한 주석을 기반으로 자동으로 생성됩니다.

_src/main/java_에서 함수 코드의 위치로 이동하여 *Function.java* 프로젝트 파일을 열고, 다음 매개 변수를 `run` 메서드 정의에 추가합니다.

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="20-21":::

`msg` 매개 변수는 [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding) 형식이며, 함수가 완료될 때 출력 바인딩에 메시지로 작성되는 문자열의 컬렉션을 나타냅니다. 이 경우 출력은 `outqueue`라는 스토리지 큐입니다. 스토리지 계정에 대한 연결 문자열은 `connection` 메서드로 설정됩니다. 연결 문자열 자체 대신 스토리지 계정 연결 문자열을 포함하는 애플리케이션 설정을 전달합니다.

`run` 메서드 정의는 이제 다음 예제와 같이 표시됩니다.  

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="16-22":::