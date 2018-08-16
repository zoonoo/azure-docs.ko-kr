---
title: 포함 파일
description: 포함 파일
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: include
manager: douge
ms.openlocfilehash: 8d0de9b027cff375526ef570e88540acfed3ef1c
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/15/2018
ms.locfileid: "40129110"
---
### <a name="run-the-service"></a>서비스 실행

1. F5 키를 눌러(또는 터미널 창에서 `azds up` 입력) 서비스를 실행합니다. 새로 선택한 공간인 `default/scott`에서 서비스가 자동으로 실행됩니다. 
1. `azds list-up`를 다시 실행하여 서비스가 자체 공간에서 실행되는지 확인할 수 있습니다. 첫째, `mywebapi` 인스턴스가 `default/scott` 공간에서 실행되고 있는지 확인합니다(`default`에서 실행되는 버전은 여전히 실행되지만 나열되지 않음). 둘째, `webfrontend`의 액세스 지점 URL에 텍스트 "scott.s."가 접두사로 있는지 확인합니다. 이 URL은 `default/scott` 공간에서 고유합니다. 이 특수 URL은 "scott URL"로 전송된 요청이 일단 `default/scott` 공간의 서비스로 라우팅을 시도하고, 시도가 실패하면 `default` 공간의 서비스로 대체됨을 나타냅니다.

```
Name         Space          Chart              Ports   Updated     Access Points
-----------  --------       -----------------  ------  ----------  -------------
mywebapi     default/scott  mywebapi-0.1.0     80/TCP  15s ago     http://localhost:61466
webfrontend  default        webfrontend-0.1.0  80/TCP  5h ago      http://scott.s.webfrontend-contosodev.1234abcdef.eastus.aksapp.io
```

![](../media/common/space-routing.png)

기본 제공되는 이 Azure Dev Spaces 기능을 사용하면 각 개발자가 자신의 공간에서 서비스의 전체 스택을 다시 만들 필요 없이 공유 공간에서 코드를 테스트할 수 있습니다. 이 라우팅을 사용하려면 이 가이드의 이전 단계에서 설명한 대로 앱 코드에서 전파 헤더를 전달해야 합니다.

### <a name="test-code-in-a-space"></a>공간에서 코드 테스트
`webfrontend`와 함께 새 버전의 `mywebapi`를 테스트하려면 브라우저를 `webfrontend`의 공용 액세스 지점 URL로 열고 [정보] 페이지로 이동합니다. 새 메시지가 표시됩니다.

이제 URL의 "scott.s." 부분을 제거하고 브라우저를 새로 고칩니다. 이전 동작(`default`에서 실행되는 `mywebapi` 버전으로)이 표시됩니다.
