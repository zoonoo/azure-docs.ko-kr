---
title: Azure Service Fabric Mesh 애플리케이션 비밀 저장 및 사용
description: Service Fabric Mesh에서는 비밀을 Azure 리소스로 지원합니다. Service Fabric Mesh 애플리케이션을 사용하여 비밀을 저장하고 관리하는 방법은 다음과 같습니다.
author: erikadoyle
ms.author: edoyle
ms.date: 10/25/2018
ms.topic: conceptual
ms.openlocfilehash: 85b4bb6ee4d76a7115c9ebdd1466049afe6683c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99625535"
---
# <a name="service-fabric-mesh-application-secrets"></a>Service Fabric Mesh 애플리케이션 비밀

> [!IMPORTANT]
> Azure Service Fabric Mesh의 미리 보기가 사용 중지되었습니다. 새 배포는 더이상 Service Fabric Mesh API를 통해 허용되지 않습니다. 기존 배포에 대한 지원은 2021년 4월 28일까지 계속됩니다.
> 
> 자세한 내용은 [Azure Service Fabric Mesh 미리 보기 사용 중지](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)를 참조하세요.

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
