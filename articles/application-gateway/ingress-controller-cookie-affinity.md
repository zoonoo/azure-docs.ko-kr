---
title: Application Gateway로 쿠키 기반 선호도 사용
description: 이 문서에서는 Application Gateway로 쿠키 기반 선호도를 사용 하도록 설정 하는 방법에 대 한 정보를 제공 합니다.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: a7806cf9518090539ba540a9a522af1aae2691f0
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93397419"
---
# <a name="enable-cookie-based-affinity-with-an-application-gateway"></a>Application Gateway를 사용 하 여 쿠키 기반 선호도 사용
[Azure 애플리케이션 Gateway 설명서](./application-gateway-components.md#http-settings)에 설명 된 대로 Application Gateway는 쿠키 기반 선호도를 지원 합니다. 즉, 사용자 세션에서 후속 트래픽을 처리 하기 위해 동일한 서버로 보낼 수 있습니다.

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