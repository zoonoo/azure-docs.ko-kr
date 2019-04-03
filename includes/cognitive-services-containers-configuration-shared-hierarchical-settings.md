---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/02/2019
ms.openlocfilehash: 03ec8740a4cf36bf3d09dade8a24b155c09d1299
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2019
ms.locfileid: "58887263"
---
컨테이너에 대한 설정은 계층 구조이며 호스트 컴퓨터의 모든 컨테이너는 공유 계층 구조를 사용합니다.

다음 중 하나를 사용하여 설정을 지정할 수 있습니다.

* [환경 변수](#environment-variable-settings)
* [명령줄 인수](#command-line-argument-settings)

환경 변수 값은 명령줄 인수를 재정의한 다음, 컨테이너 이미지에 대한 기본값을 재정의합니다. 동일한 구성 설정에 대한 환경 변수와 명령줄 인수에 다른 값을 지정한 경우, 환경 변수의 값은 인스턴스화된 컨테이너에서 사용됩니다.

|우선 순위|설정 위치|
|--|--|
|1|환경 변수| 
|2|명령줄|
|3|컨테이너 이미지 기본값|

### <a name="environment-variable-settings"></a>환경 변수 설정

환경 변수를 사용하는 이점은 다음과 같습니다.

* 다중 설정을 구성할 수 있습니다.
* 다중 컨테이너를 동일하게 설정할 수 있습니다.

### <a name="command-line-argument-settings"></a>명령줄 인수 설정

명령줄 인수를 사용하는 이점은 각 컨테이너를 다르게 설정할 수 있습니다.
