---
title: 응용 프로그램 게이트웨이를 사용하여 쿠키 기반 선호도 활성화
description: 이 문서에서는 응용 프로그램 게이트웨이와 쿠키 기반 선호도를 활성화하는 방법에 대한 정보를 제공합니다.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: adacd805e736524fd7956c4bbc0ad402980b4cd0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73795987"
---
# <a name="enable-cookie-based-affinity-with-an-application-gateway"></a>응용 프로그램 게이트웨이로 쿠키 기반 선호도 활성화
Azure 응용 [프로그램 게이트웨이 설명서에](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#http-settings)설명된 대로 응용 프로그램 게이트웨이는 쿠키 기반 선호도를 지원하므로 사용자 세션에서 처리를 위해 동일한 서버로 후속 트래픽을 직접 처리할 수 있습니다.

## <a name="example"></a>예제
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: guestbook
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/cookie-based-affinity: "true"
spec:
  rules:
  - http:
      paths:
      - backend:
          serviceName: frontend
          servicePort: 80
```
