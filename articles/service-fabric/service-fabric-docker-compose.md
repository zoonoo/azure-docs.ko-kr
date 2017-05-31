---
title: "Azure Service Fabric Docker Compose 미리 보기 | Microsoft Docs"
description: "Azure Service Fabric은 Docker Compose 형식을 수락하여 Service Fabric을 통해 기존 컨테이너를 보다 쉽게 조정할 수 있도록 합니다. 이 지원은 현재 미리 보기로 제공되고 있습니다."
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/01/2017
ms.author: subramar
ms.translationtype: Human Translation
ms.sourcegitcommit: fc4172b27b93a49c613eb915252895e845b96892
ms.openlocfilehash: 800f5bacd5197f64968fb1c169ef58330ee75e0d
ms.contentlocale: ko-kr
ms.lasthandoff: 05/12/2017

---


# <a name="compose-application-support-in-service-fabric-preview"></a>Service Fabric의 Compose 응용 프로그램 지원(미리 보기)

Docker는 다중 컨테이너 응용 프로그램을 정의하기 위해 [docker-compose.yml](https://docs.docker.com/compose) 파일을 사용합니다. Docker에 익숙한 고객이 Service Fabric에서 기존 컨테이너 응용 프로그램을 쉽게 조정하도록 하기 위해 플랫폼에 기본적으로 Docker Compose에 대한 미리 보기 지원을 포함했습니다. Service Fabric은 `docker-compose.yml` 파일의 버전 3(+)을 수락할 수 있습니다. 이 지원은 미리 보기로 제공되므로 Compose 지시문의 하위 집합만 지원됩니다. 예를 들어 응용 프로그램 업그레이드는 지원되지 않습니다. 그러나 응용 프로그램을 업그레이드하는 대신 항상 제거한 후 배포할 수 있습니다.  

이 미리 보기를 사용하려면 포털을 통해 미리 보기 SDK(버전 255.255.x.x)를 설치해야 합니다. 

> [!NOTE]
> 이 기능은 미리 보기로 제공되며 지원되지 않습니다.

## <a name="using-a-docker-composeyml-file-with-service-fabric-preview"></a>Service Fabric에서 docker-compose.yml 파일 사용(미리 보기)

PS에서 다음 명령을 실행하여 docker-compose.yml 파일에서 Service Fabric 응용 프로그램을 만듭니다.

```powershell
New-ServiceFabricComposeApplication -ApplicationName fabric:/TestContainerApp -Compose docker-compose.yml [-RepositoryUserName <>] [-RepositoryPassword <>] [-PasswordEnctypted]
```

RepositoryUserName 및 RepoistoryPassword는 컨테이너 레지스트리 사용자 이름 및 암호를 나타냅니다.

Azure CLI 2.0을 사용하는 경우 다음 명령을 실행합니다.

```bash
az sf compose create --application-id fabric:/TestContainerApp --file docker-compose.yml [ [ --repo-user --repo-pass --encrypted ] | [ --repo-user ] ] [ --timeout ]
```
이러한 명령은 Service Fabric Explorer를 통해 모니터링할 수 있는 Service Fabric 응용 프로그램(이전 예제의 `fabric:/TestContainerApp`)을 만듭니다. 지정된 `ApplicationName`은 PS, Azure CLI 2.0 또는 SFX를 통해 상태 쿼리에 사용됩니다.

PS를 통해 응용 프로그램을 삭제하려면 다음 명령을 사용합니다.

```powershell
Remove-ServiceFabricComposeApplication  -ApplicationName fabric:/TestContainerApp
```

Azure CLI 2.0을 통해 응용 프로그램을 삭제하려면 다음 명령을 사용합니다.

```bash
az sf compose remove  --application-id TestContainerApp [ --timeout ]
```

Compose 응용 프로그램의 상태를 가져오려면 PS에서 다음 명령을 사용합니다.

```powershell
Get-ServiceFabricComposeApplicationStatus -ApplicationName fabric:/TestContainerApp -GetAllPages
```

Azure CLI 2.0의 경우 다음 명령을 사용합니다.

```bash
az sf compose status --application-id TestContainerApp [ --timeout ]
```



## <a name="supported-compose-directives"></a>지원되는 Compose 지시문

Compose V3 형식의 구성 옵션 하위 집합은 이 미리 보기에서 지원됩니다. 다음과 같은 기본 형식이 지원됩니다.

* Services->Deploy->replicas    
* Services->Deploy->Placement->Constraints    
* Services->Deploy->Resources->Limits 
*         -cpu-shares    
*         -memory
*         -memory-swap
* Services->Commands    
* Services->Environment
* Services->Ports    
* Services->image
* Services->Isolation(Windows만 해당)
* Services->logging->driver    
* Services->logging->driver->options
* Volume & Deploy->Volume    

클러스터는 [Service Fabric 리소스 관리](service-fabric-resource-governance.md)에 설명된 대로 리소스 제한을 적용하도록 설정되어야 합니다.
다른 모든 docker compose 지시문은 이 미리 보기에서 지원되지 않습니다. 

## <a name="servicednsname-computation"></a>ServiceDnsName 계산

compose 파일에 지정된 서비스 이름이 정규화된 도메인 이름(즉, 마침표 ‘.’ 포함)인 경우 Service Fabric에서 등록된 DNS 이름은 마침표를 포함하는 `<ServiceName>`입니다. 정규화된 도메인 이름이 아니면 ApplicationName의 각 경로 세그먼트는 첫 번재째 경로가 최상위 도메인 레이블에 해당하는 서비스 DNS 이름의 도메인 레이블이 됩니다. 따라서 응용 프로그램 이름을 `fabric:/SampleApp/MyComposeApp`으로 지정하면 `<ServiceName>.MyComposeApp.SampleApp`은 등록된 DNS 이름이 됩니다.

## <a name="differences-between-compose-instance-definition-and-service-fabric-application-model-type-definition"></a>Compose(인스턴스 정의) 및 Service Fabric 응용 프로그램 모델(형식 정의) 간 차이

docker-compose.yml 파일은 해당 속성 및 구성(예: 환경 변수 및 포트)을 포함하는 컨테이너의 배포 가능 집합을 설명합니다. 배치 제약 조건, 리소스 제한, DNS 이름과 같은 배포 매개 변수는 docker-compose.yml 파일에도 지정됩니다.

[Service Fabric 응용 프로그램 모델](service-fabric-application-model.md)은 서비스 형식 및 응용 프로그램 형식을 사용합니다. 따라서 동일한 형식의 여러 응용 프로그램 인스턴스(예: 고객당 하나의 응용 프로그램 인스턴스)를 유지할 수 있습니다. 이 형식 기반 모델은 동일한 응용 프로그램 유형의 여러 버전을 런타임에 등록하도록 지원합니다. 예를 들어 고객 A에는 1.0 형식의 AppTypeA로 인스턴스화된 응용 프로그램이 있고, 고객 B에는 동일한 형식 및 버전으로 인스턴스화된 다른 응용 프로그램을 유지할 수 있습니다. 응용 프로그램 형식은 응용 프로그램 매니페스트에 정의되고, 응용 프로그램 이름 및 배포 매개 변수는 응용 프로그램 생성 시에 지정됩니다.

이 모델을 사용하면 유연하게 작업할 수 있지만 매니페스트 파일에서 형식이 암시되는 좀 더 간단한 인스턴스 기반 모델을 지원하려고 합니다. 이 모델에서 각 응용 프로그램은 자체의 독립적인 매니페스트를 갖게 됩니다. 인스턴스 기반 배포 형식인 docker-compose.yml에 대한 지원을 추가하여 이러한 방식을 미리 검토하고 있습니다.


## <a name="next-steps"></a>다음 단계

* [Service Fabric 응용 프로그램 모델](service-fabric-application-model.md)에 대해 자세히 알아보기

