---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 05/12/2021
ms.author: cephalin
ms.openlocfilehash: 43eda9f0f5cdb108c2949205d72433c9d7176571
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110371981"
---
## <a name="create-an-app-service-kubernetes-environment"></a>App Service Kubernetes 환경 만들기

시작하기 전에 Azure Arc 지원 Kubernetes 클러스터에 대해 [App Service Kubernetes 환경을 생성](../articles/app-service/manage-create-arc-environment.md)해야 합니다. 

> [!NOTE] 
> 환경을 만들 때는 사용자 지정 위치 이름과 사용자 지정 위치가 포함된 리소스 그룹의 이름을 모두 기록해 둡니다. 이를 사용하면 환경에서 함수 앱을 만들 때 필요한 사용자 지정 위치 ID를 찾을 수 있습니다. 
>
> 환경을 만들지 않은 경우 클러스터 관리자에게 문의하세요.