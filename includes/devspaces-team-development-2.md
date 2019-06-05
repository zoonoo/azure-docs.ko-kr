---
title: 포함 파일
description: 포함 파일
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.subservice: azds-kubernetes
author: DrEsteban
ms.author: stevenry
ms.date: 12/17/2018
ms.topic: include
manager: yuvalm
ms.openlocfilehash: e0f768b876b49ec006ce98decf121d73d334b6d8
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66157862"
---
### <a name="run-the-service"></a>서비스 실행

서비스를 실행하려면, F5 키를 눌러서(또는 터미널 창에서 `azds up` 입력) 서비스를 실행합니다. 새로 선택한 공간인 _dev/scott_에서 서비스가 자동으로 실행됩니다. `azds list-up`를 다시 실행하여 서비스가 자체 공간에서 실행되는지 확인합니다.

```cmd
$ azds list-up

Name                      DevSpace  Type     Updated  Status
mywebapi                  scott     Service  3m ago   Running
webfrontend               dev       Service  26m ago  Running
```

*mywebapi* 인스턴스가 이제 _dev/scott_ 공간에서 실행되는 것을 확인할 수 있습니다. _dev_에서 실행되는 버전은 계속 실행되지만 나열되지 않습니다.

`azds list-uris`를 실행하여 현재 공간에 대한 URL을 나열합니다.

```cmd
$ azds list-uris

Uri                                                                        Status
-------------------------------------------------------------------------  ---------
http://localhost:53831 => mywebapi.scott:80                                Tunneled
http://scott.s.dev.webfrontend.6364744826e042319629.ce.azds.io/  Available
```

*webfrontend*에 대한 공용 액세스 지점 URL에 *scott.s*가 접두사로 추가된 것을 확인할 수 있습니다. 이 URL은 _dev/scott_ 공간에 대해 고유합니다. 이 URL 접두사는 요청을 _dev/scott_ 버전의 서비스로 라우팅하도록 수신 컨트롤러에 지시합니다. 이 URL이 포함된 요청이 Dev Spaces에서 처리될 때, 수신 컨트롤러는 먼저 _dev/scott_ 공간의 *webfrontend* 서비스로 요청을 라우팅하려고 시도합니다. 이것이 실패하면 요청은 _dev_ 공간의 *webfrontend* 서비스로 대체 라우팅됩니다. Kubernetes *port-forward* 기능을 사용하여 localhost를 통해 서비스에 액세스하는 localhost URL도 있습니다. Azure Dev Spaces의 URL 및 라우팅에 대한 자세한 내용은 [Azure Dev Spaces 작동 및 구성 방법](../articles/dev-spaces/how-dev-spaces-works.md)을 참조하세요.



![공간 라우팅](../articles/dev-spaces/media/common/Space-Routing.png)

기본 제공되는 이 Azure Dev Spaces 기능을 사용하면 각 개발자가 자신의 공간에서 서비스의 전체 스택을 다시 만들 필요 없이 공유 공간에서 코드를 테스트할 수 있습니다. 이 라우팅을 사용하려면 이 가이드의 이전 단계에서 설명한 대로 앱 코드에서 전파 헤더를 전달해야 합니다.

### <a name="test-code-in-a-space"></a>공간에서 코드 테스트
*webfrontend*와 함께 새 버전의 *mywebapi*를 테스트하려면 브라우저를 *webfrontend*에 대한 공용 액세스 지점 URL로 브라우저를 열고 정보 페이지로 이동합니다. 새 메시지가 표시됩니다.

이제 URL의 "scott.s." 부분을 제거하고 브라우저를 새로 고칩니다. 이전 동작(_dev_에서 실행되는 *mywebapi* 버전으로)이 표시됩니다.

항상 최신 변경 사항을 포함하는 _dev_ 공간을 확보하고 난 후, 이 자습서 섹션에서 설명한 대로 DevSpace의 공간 기반 라우팅을 이용하도록 애플리케이션이 설계되었다고 가정하면 Dev Spaces가 더 큰 애플리케이션의 컨텍스트 내에서 새로운 기능을 테스트하는 데 얼마나 유용한지를 쉽게 확인할 수 있을 것입니다. 프라이빗 공간에 _모든_ 서비스를 배포하는 대신, _dev_에서 파생되는 프라이빗 공간을 만들고 실제 작업하는 서비스만 "작동"시킬 수 있습니다. Dev Spaces 라우팅 인프라는 _dev_ 공간에서 실행 중인 최신 버전으로 기본값을 다시 설정하는 동안 프라이빗 공간 외에 찾을 수 있는 한 많은 서비스를 활용하여 나머지 부분을 처리합니다. 그리고 더 좋은 것은 _여러_ 개발자들이 서로를 방해하지 않으면서 자신의 공간에서 동시에 다양한 서비스를 적극적으로 개발할 수 있다는 점입니다.

### <a name="well-done"></a>모두 완료되었습니다!
시작 가이드를 완료했습니다! 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * Azure에서 관리되는 Kubernetes 클러스터를 사용하여 Azure Dev Spaces를 설정합니다.
> * 컨테이너에서 반복적으로 코드를 개발합니다.
> * 독립적으로 별도의 두 서비스를 개발하고, Kubernetes의 DNS 서비스 검색을 사용하여 다른 서비스를 호출합니다.
> * 팀 환경에서 코드를 생산적으로 개발하고 테스트합니다.
> * Dev Spaces를 통해 기능의 기준을 설정하여 더 큰 마이크로 서비스 애플리케이션의 컨텍스트 내에서 격리된 변경 내용을 손쉽게 테스트합니다.

지금까지 Azure Dev Spaces에 대해 알아보았으므로 [팀 멤버와 개발 공간을 공유](../articles/dev-spaces/how-to/share-dev-spaces.md)하고 함께 공동 작업을 수행할 수 있습니다.

## <a name="clean-up"></a>정리
모든 개발 환경 및 그 안에서 실행되는 서비스를 포함하여 클러스터에서 Azure Dev Spaces 인스턴스를 완전히 삭제하려면 `az aks remove-dev-spaces` 명령을 사용합니다. 이 작업은 되돌릴 수 없습니다. 클러스터에서 Azure Dev Spaces에 대한 지원을 추가할 수 있지만 마치 다시 시작하는 것 같을 것입니다. 이전 서비스와 공간을 복원할 수 없습니다.

다음 예제에서는 활성 구독에 Azure Dev Spaces 컨트롤러를 나열한 다음, 리소스 그룹 'myaks-rg'에서 AKS 클러스터 'myaks'와 연결되는 Azure Dev Spaces 컨트롤러를 삭제합니다.

```cmd
    azds controller list
    az aks remove-dev-spaces --name myaks --resource-group myaks-rg
```