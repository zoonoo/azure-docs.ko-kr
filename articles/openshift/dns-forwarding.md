---
title: Azure Red Hat OpenShift 4에 대 한 DNS 전달 구성
description: Azure Red Hat OpenShift 4에 대 한 DNS 전달 구성
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: 6784c69c049945f3bd4977ecd647d57ec59bf9f1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "82232635"
---
# <a name="configure-dns-forwarding-on-an-azure-red-hat-openshift-4-cluster"></a>Azure Red Hat OpenShift 4 클러스터에서 DNS 전달 구성

Azure Red Hat OpenShift 클러스터에서 DNS 전달을 구성 하려면 DNS 운영자를 수정 해야 합니다. 이렇게 수정 하면 클러스터 내에서 실행 되는 응용 프로그램을 pod 하 여 클러스터 외부의 개인 DNS 서버에서 호스트 되는 이름을 확인할 수 있습니다. 이러한 단계는 OpenShift [4.3에](https://docs.openshift.com/container-platform/4.3/networking/dns-operator.html)대해 설명 되어 있습니다.

예를 들어 DNS 서버 192.168.100.10가 확인 하기 위해 *. example.com에 대 한 모든 DNS 요청을 전달 하려는 경우 다음을 실행 하 여 운영자 구성을 편집할 수 있습니다.
 
```bash
oc edit dns.operator/default
```
 
그러면 편집기가 시작 되 고를으로 바꿀 수 있습니다 `spec: {}` .
 
 ```yaml
spec:
  servers:
  - forwardPlugin:
      upstreams:
      - 192.168.100.10
    name: example-dns
    zones:
    - example.com
```

파일을 저장 하 고 편집기를 종료 합니다.

## <a name="next-steps"></a>다음 단계
OpenShift 4.3에 대 한 DNS 전달에 대 한 자세한 내용은 [여기](https://docs.openshift.com/container-platform/4.3/networking/dns-operator.html)를 참조 하세요.