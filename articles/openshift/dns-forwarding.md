---
title: Azure Red Hat OpenShift 4에 대한 DNS 전달 구성
description: Azure Red Hat OpenShift 4에 대한 DNS 전달 구성
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: 68cc5dbee5b96472d11bdfb3a8bf35d118638dcd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100633872"
---
# <a name="configure-dns-forwarding-on-an-azure-red-hat-openshift-4-cluster"></a>Azure Red Hat OpenShift 4 클러스터에 대한 DNS 전달 구성

Azure Red Hat OpenShift 클러스터에서 DNS 전달을 구성하려면 DNS 운영자를 수정해야 합니다. 이렇게 수정하면 클러스터 내에서 실행되는 애플리케이션 Pod가 클러스터 외부의 프라이빗 DNS 서버에서 호스트되는 이름을 확인할 수 있습니다. 이러한 단계가 OpenShift 4.6을 대상으로 [여기](https://docs.openshift.com/container-platform/4.6/networking/dns-operator.html)에 설명되어 있습니다.

예를 들어 DNS 서버 192.168.100.10이 확인하도록 *.example.com에 대한 모든 DNS 요청을 전달하려는 경우 다음을 실행하여 운영자 구성을 편집할 수 있습니다.
 
```bash
oc edit dns.operator/default
```
 
그러면 편집기가 시작되고 `spec: {}`을 다음으로 바꿀 수 있습니다.
 
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

파일을 저장하고 편집기를 종료합니다.

## <a name="next-steps"></a>다음 단계
OpenShift 4.6에서의 DNS 전달에 대한 자세한 내용은 [여기](https://docs.openshift.com/container-platform/4.6/networking/dns-operator.html)를 참조하세요.