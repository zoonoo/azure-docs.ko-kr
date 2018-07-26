---
title: Azure Service Fabric 리소스 모델 소개 | Microsoft Docs
description: Service Fabric Mesh 응용 프로그램을 정의하는 간단한 방법인 Service Fabric 리소스 모델에 대해 알아봅니다.
services: service-fabric-mesh
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/12/2018
ms.author: vturecek
ms.custom: mvc, devcenter
ms.openlocfilehash: 6a96995a46cd2ad0a1afe4f4afe49d62409f2b68
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/17/2018
ms.locfileid: "39075553"
---
# <a name="introduction-to-service-fabric-resource-model"></a>Service Fabric 리소스 모델 소개

Service Fabric 리소스 모델에서는 Service Fabric Mesh 응용 프로그램을 구성하는 리소스를 정의하는 간단한 방법을 설명합니다. 다음 형식의 리소스가 현재 이 모델에서 지원됩니다.

- 응용 프로그램
- Services
- 볼륨
- 네트워크

각 리소스는 Mesh 응용 프로그램을 설명하는 간단한 YAML 또는 JSON 문서이며 Service Fabric 플랫폼에서 제공되는 리소스 파일에서 선언적으로 설명되어 있습니다.

## <a name="resource-overview"></a>리소스 개요

### <a name="applications-and-services"></a>응용 프로그램 및 서비스

응용 프로그램 리소스는 배포 단위, 버전 관리 및 Mesh 응용 프로그램의 수명입니다. 마이크로 서비스를 나타내는 하나 이상의 서비스 리소스로 구성됩니다. 따라서 각 서비스 리소스는 다음을 비롯한 코드 패키지와 관련된 컨테이너 이미지를 실행하는 데 필요한 모든 항목을 설명하는 하나 이상의 코드 패키지로 구성됩니다.

- 컨테이너 이름, 버전 및 레지스트리
- 각 컨테이너에 대한 필요한 CPU 및 메모리 리소스
- 네트워크 엔드포인트
- 별도 볼륨 리소스를 참조하는 컨테이너에 탑재할 볼륨

서비스 리소스의 일부로 정의된 모든 코드 패키지를 배포하고 그룹으로 함께 활성화합니다. 또한 서비스 리소스는 실행할 서비스의 인스턴스 개수를 설명하고 사용하는 기타 리소스(예: 네트워크 리소스)도 참조합니다.

Mesh 응용 프로그램이 둘 이상의 서비스로 구성되면 동일한 노드에서 함께 실행하도록 보장되지 않습니다. 또한 응용 프로그램을 업그레이드하는 동안 단일 서비스를 업그레이드하는 오류가 발생하면 모든 서비스가 이전 버전으로 롤백됩니다.



```yaml
    services:
      - name: helloWorldService
        properties:
          description: Hello world service.
          osType: linux
          codePackages:
            - name: helloworld
              image: myapp:1.0-alpine
              resources:
                requests:
                  cpu: 2
                  memoryInGB: 2
              endpoints:
                - name: helloWorldEndpoint
                  port: 8080
    replicaCount: 3
    networkRefs:
      - name: mynetwork
```

앞에서 언급한 대로 각 응용 프로그램 인스턴스의 수명 주기를 독립적으로 관리할 수 있습니다. 예를 들어 하나의 응용 프로그램 인스턴스는 다른 응용 프로그램 인스턴스와 독립적으로 업그레이드될 수 있습니다. 일반적으로 응용 프로그램에서 서비스 수를 적게 유지하고 응용 프로그램에 서비스가 많을수록 서비스를 독립적으로 관리하기 어렵게 됩니다.

### <a name="networks"></a>네트워크

네트워크 리소스는 개별적으로 배포 가능한 리소스이며 해당 종속성으로 참조할 수 있는 응용 프로그램 또는 서비스 리소스와 독립적입니다. 응용 프로그램에 대한 개인 네트워크를 만드는 데 사용됩니다. 다른 응용 프로그램의 여러 서비스는 동일한 네트워크의 일부일 수 있습니다.

> [!NOTE]
> 현재 미리 보기는 응용 프로그램과 네트워크 간의 일대일 매핑만을 지원합니다

### <a name="volumes"></a>볼륨

볼륨은 상태를 유지하는 데 사용할 수 있는 컨테이너 인스턴스 내에 탑재된 디렉터리입니다. 볼륨 리소스는 디렉터리를 탑재하는 방법 및 이에 대한 지원 저장소를 설명하는 선언적 방법입니다.

## <a name="programming-models"></a>프로그래밍 모델
서비스 리소스는 컨테이너 이미지를 실행해야 합니다. 이것은 리소스와 연결된 코드 패키지에서 참조됩니다. Service Fabric Mesh 특정 API를 이해하거나 사용하지 않고도 컨테이너 내에서 프레임워크를 사용하여 모든 언어로 작성된 코드를 실행할 수 있습니다. 

응용 프로그램 코드가 Service Fabric Mesh 외부에서도 이동 가능한 상태를 유지하고 응용 프로그램 배포가 서비스를 구현하는 데 사용되는 언어 또는 프레임워크에 관계 없이 일관성을 유지합니다. 응용 프로그램이 ASP.NET Core, Go 또는 프로세스 및 스크립트 집합인지에 관계 없이 Service Fabric Mesh 리소스 배포 모델은 동일하게 유지됩니다. 

## <a name="deployment"></a>배포

Service Fabric Mesh에 배포하는 경우 HTTP 또는 Azure CLI를 통해 Azure에 리소스를 Azure Resource Manager 템플릿으로 배포합니다. 


## <a name="next-steps"></a>다음 단계 
Service Fabric Mesh에 대한 자세한 내용은 다음 개요를 참조하세요.
- [Service Fabric Mesh 개요](service-fabric-mesh-overview.md)
