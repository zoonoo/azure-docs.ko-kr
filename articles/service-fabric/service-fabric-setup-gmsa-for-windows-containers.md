---
title: Azure Service Fabric container service에 대 한 gMSA 설정
description: 이제 Azure Service Fabric에서 실행 되는 컨테이너에 대 한 그룹 관리 서비스 계정 (gMSA)을 설정 하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 03/20/2019
ms.openlocfilehash: d34b4c6e11628b6a4843f8a9077ebf69c9e023fe
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2020
ms.locfileid: "86260883"
---
# <a name="set-up-gmsa-for-windows-containers-running-on-service-fabric"></a>Service Fabric에서 실행 중인 Windows 컨테이너에 대한 gMSA 설정

gMSA(그룹 관리 서비스 계정)를 설정하려면 자격 증명 사양 파일(`credspec`)을 클러스터의 모든 노드에 배치합니다. VM 확장을 사용하여 모든 노드에서 파일을 복사할 수 있습니다.  `credspec` 파일은 gMSA 계정 정보를 포함해야 합니다. 파일에 대 한 자세한 내용은 `credspec` [자격 증명 사양 만들기](/virtualization/windowscontainers/manage-containers/manage-serviceaccounts#create-a-credential-spec)를 참조 하세요. `Hostname`응용 프로그램 매니페스트에서 자격 증명 사양과 태그가 지정 됩니다. `Hostname` 태그는 컨테이너를 실행하는 gMSA 계정 이름과 일치해야 합니다.  `Hostname` 태그를 사용하면 컨테이너를 Kerberos 인증을 사용하여 도메인에 있는 다른 서비스에 인증할 수 있습니다.  애플리케이션 매니페스트에서 `Hostname` 및 `credspec`를 지정하는 샘플은 다음 코드 조각에 표시됩니다.

```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" Hostname="gMSAAccountName">
    <SecurityOption Value="credentialspec=file://WebApplication1.json"/>
  </ContainerHostPolicies>
</Policies>
```
다음 단계는 아래 문서를 참조하세요.

* [Windows Server 2016에서 Windows 컨테이너를 Service Fabric에 배포](service-fabric-get-started-containers.md)
* [Linux에서 Docker 컨테이너를 Service Fabric에 배포](service-fabric-get-started-containers-linux.md)
