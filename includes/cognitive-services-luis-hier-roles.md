---
title: 포함 파일
description: 포함 파일
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: include
ms.custom: include file
ms.date: 08/02/2018
ms.author: diberry
ms.openlocfilehash: dd99218765c873120c4117a3ec1712fe0a605e66
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44164590"
---
**질문**: 역할에서 단순 엔터티와 함께 계층 구조 엔터티 또는 패턴을 사용해야 하나요? 

**응답**: 경우에 따라 다릅니다. 패턴 및 발화 예제는 사용자의 발화를 나타낸다는 점에서 서로 비교할 수 있고 의도에 따라 다릅니다.  

발화에 명백한 패턴이 없는 경우 계층 구조 엔터티를 사용합니다. 

|계층 구조 엔터티|역할에서 단순 엔터티|
|--|--|
|의도에서 레이블이 지정된 자식 엔터티를 사용하는 예제 발화가 포함되어야 합니다.|예제 발화가 포함되어야 합니다. **역할은 의도에서 레이블이 지정될 수 없습니다.**|
|패턴에서 사용할 수 있습니다.|패턴에서 사용**해야** 합니다.|
|의도에서 **추가** 예제 발화가 필요할 수 있습니다.|의도에서 **적은 예제 발화가 필요할 수 있습니다.|