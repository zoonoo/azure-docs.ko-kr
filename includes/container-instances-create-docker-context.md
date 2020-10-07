---
title: 포함 파일
description: 포함 파일
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: include
ms.date: 08/13/2020
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 3221c4f3e196cf1573bd7c0424fa3b4530c0b2ca
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2020
ms.locfileid: "90708267"
---
## <a name="create-azure-context"></a>Azure 컨텍스트 만들기

Docker 명령을 사용하여 Azure Container Instances에서 컨테이너를 실행하려면 먼저 Azure에 로그인합니다.

```bash
docker login azure
```

메시지가 표시되면 Azure 자격 증명을 입력하거나 선택합니다.


`docker context create aci`를 실행하여 ACI 컨텍스트를 만듭니다. 이 컨텍스트는 사용자가 컨테이너 인스턴스를 만들고 관리할 수 있도록 Docker를 Azure 구독 및 리소스 그룹과 연결합니다. 예를 들어, *myacicontext*라는 컨텍스트를 만듭니다.

```
docker context create aci myacicontext
```

메시지가 표시되면 Azure 구독 ID를 선택하고 기존 리소스 그룹을 선택하거나 **새 리소스 그룹을 만듭니다**. 새 리소스 그룹을 선택하는 경우 시스템에서 생성된 이름으로 만들어집니다. 모든 Azure 리소스와 마찬가지로 Azure 컨테이너 인스턴스는 리소스 그룹에 배포해야 합니다. 리소스 그룹을 사용하면 관련 Azure 리소스를 구성하고 관리할 수 있습니다.


`docker context ls`을(를) 실행하여 Docker 컨텍스트에 ACI 컨텍스트를 추가했는지 확인합니다.

```
docker context ls
```