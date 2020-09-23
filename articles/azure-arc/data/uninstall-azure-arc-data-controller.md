---
title: Azure Arc 데이터 컨트롤러 삭제
description: Azure Arc 데이터 컨트롤러 삭제
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 2bae661218989d49b74ed8ca3f694ccb912ef912
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90939113"
---
# <a name="delete-azure-arc-data-controller"></a>Azure Arc 데이터 컨트롤러 삭제

다음 문서에서는 Azure Arc 데이터 컨트롤러를 삭제 하는 방법을 설명 합니다.

계속 하기 전에 데이터 컨트롤러에 생성 된 모든 데이터 서비스가 다음과 같이 제거 되었는지 확인 합니다.

## <a name="log-in-to-the-data-controller"></a>데이터 컨트롤러에 로그인 합니다.

삭제할 데이터 컨트롤러에 로그인 합니다.

```
azdata login
```

## <a name="list--delete-existing-data-services"></a>기존 데이터 서비스 나열 & 삭제

다음 명령을 실행 하 여 생성 된 SQL 관리 인스턴스가 있는지 확인 합니다.

```
azdata arc sql mi list
```

위의 목록에서 각 SQL 관리 되는 인스턴스에 대해 다음과 같이 delete 명령을 실행 합니다.

```
azdata arc sql mi delete -n <name>
# for example: azdata arc sql mi delete -n sqlinstance1
```

마찬가지로 PostgreSQL Hyperscale 인스턴스를 확인 하려면 다음을 실행 합니다.

```
azdata arc postgres server list
```

그리고 각 PostgreSQL Hyperscale 인스턴스에 대해 다음과 같이 delete 명령을 실행 합니다.
```
azdata arc postgres server delete -n <name>
# for example: azdata arc postgres server delete -n pg1
```

## <a name="delete-controller"></a>컨트롤러 삭제

모든 SQL 관리 되는 인스턴스 및 PostgreSQL Hyperscale 인스턴스를 제거한 후 다음과 같이 데이터 컨트롤러를 삭제할 수 있습니다.

```
azdata arc dc delete -n <name> -ns <namespace>
# for example: azdata arc dc delete -ns arc -n arcdc
```

### <a name="remove-sccs-red-hat-openshift-only"></a>SCCs 제거 (Red Hat OpenShift 전용)

```console
oc adm policy remove-scc-from-user privileged -z default -n arc
oc adm policy remove-scc-from-user anyuid     -z default -n arc
```

### <a name="optionally-delete-the-azure-arc-data-controller-namespace"></a>필요에 따라 Azure Arc 데이터 컨트롤러 네임 스페이스를 삭제 합니다.


```console
kubectl delete ns <nameSpecifiedDuringCreation>
# for example kubectl delete ns arc
```

## <a name="next-steps"></a>다음 단계

[Azure Arc에서 사용할 수 있는 데이터 서비스는 무엇 인가요?](overview.md)