---
title: "Service Fabric 및 Linux에서 컨테이너 배포 | Microsoft Docs"
description: "Service Fabric 및 마이크로 서비스 응용 프로그램 배포를 위한 Docker 컨테이너 사용. 이 문서는 Service Fabric이 컨테이너에 대해 제공하는 기능과 클러스터에 Docker 컨테이너 이미지를 배포하는 방법을 설명합니다."
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: 
ms.assetid: 4ba99103-6064-429d-ba17-82861b6ddb11
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/24/2016
ms.author: msfussell
translationtype: Human Translation
ms.sourcegitcommit: 57aec98a681e1cb5d75f910427975c6c3a1728c3
ms.openlocfilehash: 9c45513f7d2987a83026adab39257d298292b7a5


---
# <a name="deploy-a-docker-container-to-service-fabric"></a>Docker 컨테이너를 Service Fabric에 배포
> [!div class="op_single_selector"]
> * [Windows 컨테이너 배포](service-fabric-deploy-container.md)
> * [Docker 컨테이너 배포](service-fabric-deploy-container-linux.md)
>
>

이 문서에서는 Linux의 Docker 컨테이너에서 컨테이너화된 서비스를 빌드하는 과정을 안내합니다.

Service Fabric에는 컨테이너화된 마이크로 서비스로 구성된 응용 프로그램을 빌드하는 데 도움을 주는 몇 가지 컨테이너 기능이 있습니다. 이것을 컨테이너화된 서비스라고 합니다.

기능은 다음과 같습니다.

* 컨테이너 이미지 배포 및 활성화
* 리소스 관리
* 리포지토리 인증
* 포트 매핑을 호스트하는 컨테이너 포트
* 컨테이너 간 검색 및 통신
* 환경 변수를 구성하고 설정할 수 있는 기능

## <a name="packaging-a-docker-container-with-yeoman"></a>yeoman과 함께 docker 컨테이너 패키징
Linux에서 컨테이너를 패키징할 경우 yeoman 템플릿을 사용하거나 [응용 프로그램 패키지를 수동으로 만들도록](service-fabric-deploy-container.md#manually) 선택할 수 있습니다.

Service Fabric 응용 프로그램은 응용 프로그램의 기능을 제공하는 특정 역할이 있는 하나 이상의 컨테이너를 포함할 수 있습니다. Linux용 Service Fabric SDK는 쉽게 응용 프로그램을 만들고 컨테이너 이미지를 추가할 수 있는 [Yeoman](http://yeoman.io/) 생성기를 포함합니다. Yeoman을 사용하여 *SimpleContainerApp*이라는 단일 Docker 컨테이너가 있는 새 응용 프로그램을 만들어 보겠습니다. 일반화된 매니페스트 파일을 편집하여 나중에 더 많은 서비스를 추가할 수 있습니다.

## <a name="create-the-application"></a>응용 프로그램 만들기
1. 터미널에서 **yo azuresfguest**를 입력합니다.
2. 프레임워크에 대해 **컨테이너**를 선택합니다.
3. 응용 프로그램 이름을 지정합니다(예: SimpleContainerApp).
4. DockerHub 리포지토리에서 컨테이너 이미지에 대한 URL을 제공합니다. [리포지토리]/[이미지 이름] 양식을 사용합니다.

![컨테이너용 Service Fabric Yeoman 생성기][sf-yeoman]

## <a name="deploy-the-application"></a>응용 프로그램 배포
응용 프로그램이 빌드되면 Azure CLI를 사용하여 로컬 클러스터에 배포할 수 있습니다.

1. 로컬 Service Fabric 클러스터에 연결합니다.

    ```bash
    azure servicefabric cluster connect
    ```
2. 템플릿에 제공된 설치 스크립트를 사용하여 클러스터의 이미지 저장소에 응용 프로그램 패키지를 복사하고 응용 프로그램 유형을 등록하며 응용 프로그램의 인스턴스를 만듭니다.

    ```bash
    ./install.sh
    ```
3. 브라우저를 열고 http://localhost:19080/Explorer에서 Service Fabric Explorer로 이동합니다(Mac OS X에서 Vagrant를 사용하는 경우 localhost를 VM의 개인 IP로 바꿉니다).
4. 응용 프로그램 노드를 확장하면 응용 프로그램 유형에 대한 항목 및 해당 유형의 첫 번째 인스턴스에 대한 다른 항목이 만들어집니다.
5. 템플릿에 제공된 제거 스크립트를 사용하여 응용 프로그램 인스턴스를 삭제하고 응용 프로그램 유형을 등록 해제합니다.

    ```bash
    ./uninstall.sh
    ```

## <a name="next-steps"></a>다음 단계
* [Service Fabric 및 컨테이너 개요](service-fabric-containers-overview.md)
* [Azure CLI를 사용하여 Service Fabric 클러스터와 상호 작용](service-fabric-azure-cli.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-deploy-container-linux/sf-container-yeoman.png



<!--HONumber=Nov16_HO3-->


