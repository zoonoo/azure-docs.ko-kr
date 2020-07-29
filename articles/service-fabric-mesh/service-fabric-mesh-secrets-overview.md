---
title: Azure Service Fabric 메시 응용 프로그램 비밀 저장 및 사용
description: Service Fabric Mesh에서는 비밀을 Azure 리소스로 지원합니다. Service Fabric 메시 응용 프로그램을 사용 하 여 암호를 저장 하 고 관리 하는 방법은 다음과 같습니다.
author: erikadoyle
ms.author: edoyle
ms.date: 10/25/2018
ms.topic: conceptual
ms.openlocfilehash: da4eaa34840f14714616b85e24fd62cf65602b84
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "76277631"
---
# <a name="service-fabric-mesh-application-secrets"></a>Service Fabric Mesh 애플리케이션 비밀
Service Fabric Mesh에서는 비밀을 Azure 리소스로 지원합니다. Service Fabric Mesh 비밀은 스토리지 연결 문자열, 암호, 안전하게 저장하고 전송되어야 하는 기타 값 등 중요한 텍스트 정보일 수 있습니다.

![Mesh 비밀 개요][sf-mesh-secrets-overview]

## <a name="mesh-secrets-resources"></a>Mesh 비밀 리소스
Mesh 애플리케이션 비밀은 다음으로 이루어져 있습니다.
* **비밀** 리소스는 텍스트 비밀을 저장하는 컨테이너입니다. **비밀** 리소스 내에 포함된 비밀은 안전하게 저장되고 전송됩니다.
* 하나 이상의 **비밀/값** 리소스는 **비밀** 리소스 컨테이너에 저장됩니다. 각 **비밀/값** 리소스는 버전 번호로 구분됩니다.

## <a name="next-steps"></a>다음 단계 
Service Fabric Mesh 비밀에 대해 자세히 알아보려면 다음을 참고하세요.
- [Service Fabric Mesh 애플리케이션 비밀 관리](service-fabric-mesh-howto-manage-secrets.md)
- [Service Fabric 리소스 모델 소개](service-fabric-mesh-service-fabric-resources.md)

<!-- pics -->
[sf-mesh-secrets-overview]: ./media/service-fabric-mesh-secrets-overview/MeshAppSecretsOverview.png
