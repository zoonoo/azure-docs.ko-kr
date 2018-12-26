---
title: Azure Service Fabric Mesh의 상태 저장소 옵션 | Microsoft Docs
description: Azure Service Fabric Mesh에서 실행되는 Service Fabric Mesh 응용 프로그램에 상태를 안정적으로 저장하는 방법을 알아봅니다.
services: service-fabric-mesh
keywords: ''
author: rwike77
ms.author: ryanwi
ms.date: 07/12/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: 6aa268cf56bfb8be9c27a9e0d9e5c9f4464b0c9d
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/17/2018
ms.locfileid: "39075940"
---
# <a name="state-management-with-service-fabric"></a>Service Fabric을 사용한 상태 관리
Service Fabric은 상태 저장소에 대한 다양한 옵션을 지원합니다. 상태 관리 패턴 및 Service Fabric의 개념적인 개요는 [Service Fabric 개념: 상태](/azure/service-fabric/service-fabric-concepts-state)를 참조하세요. 이러한 모든 동일한 개념은 Service Fabric Mesh 내부 또는 외부에서 서비스를 실행할지 여부에 관계없이 적용됩니다. 

## <a name="state-storage-options-in-azure-service-fabric-mesh"></a>Azure Service Fabric Mesh의 상태 저장소 옵션
Service Fabric Mesh를 사용하면 새 응용 프로그램을 쉽게 배포하고 Azure에 호스트되는 기존 데이터 저장소에 연결할 수 있습니다. 원격 데이터베이스를 사용하는 것 외에도 서비스에 로컬 또는 원격 저장소가 필요한지 여부에 따라 다양한 데이터 저장 옵션이 있습니다. 

* 로컬로 저장된 복제 데이터
  * 신뢰할 수 있는 컬렉션(미리 보기에서 사용할 수 없음)
    * 서비스에서 사용할 큐 및 키-값 쌍 같은 데이터 구조를 구현하는 라이브러리
    * 이는 데이터를 조작하는 가장 쉽고 빠른 방법이며, Service Fabric Mesh의 지능형 라우팅과 결합하여 간편한 파티션 라우팅을 제공합니다.
  * Service Fabric 볼륨 드라이버(미리 보기에서 사용할 수 없음)
    * 로컬 볼륨을 컨테이너에 탑재하는 docker 볼륨 드라이버
    * 이는 파일 저장소를 지원하는 API를 통해 로컬로 데이터를 저장할 수 있는 최상의 유연성을 제공합니다.

* 원격 저장소
  * Azure Files 볼륨 드라이버
    * Azure Files 공유를 컨테이너에 탑재하는 docker 볼륨 드라이버
    * 성능이 떨어지지만, 파일 저장소를 지원하는 API를 통해 저렴하고, 완전히 유연하고, 안정적인 데이터 옵션을 제공합니다.
    * [방법 가이드: Azure Files 볼륨으로 앱 배포](service-fabric-mesh-howto-deploy-app-azurefiles-volume.md)
    
## <a name="next-steps"></a>다음 단계

응용 프로그램 모델에 대한 자세한 내용은 [Service Fabric 리소스](service-fabric-mesh-service-fabric-resources.md)를 참조하세요.
