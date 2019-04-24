---
title: C#을 사용하여 Linux에서 Azure Service Fabric 앱 처음 만들기 | Microsoft Docs
description: C# 및 .NET Core 2.0을 사용하여 Service Fabric 응용 프로그램을 만들고 배포하는 방법을 알아봅니다.
services: service-fabric
documentationcenter: csharp
author: mani-ramaswamy
manager: chackdan
editor: ''
ms.assetid: 5a96d21d-fa4a-4dc2-abe8-a830a3482fb1
ms.service: service-fabric
ms.devlang: csharp
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/11/2018
ms.author: subramar
ms.openlocfilehash: 7bb9b1c545d30f66a09817ba977f12666a9f41c9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60394322"
---
# <a name="create-your-first-azure-service-fabric-application"></a>첫 번째 Azure Service Fabric 애플리케이션 만들기
> [!div class="op_single_selector"]
> * [Java - Linux(미리 보기)](service-fabric-create-your-first-linux-application-with-java.md)
> * [C# - Linux(미리 보기)](service-fabric-create-your-first-linux-application-with-csharp.md)
>
>

Service Fabric은 .NET Core 및 Java 모두에서 Linux에 대한 서비스를 구축하는 SDK를 제공합니다. 이 자습서에서는 .NET Core 2.0에서 C#을 사용하여 Linux용 애플리케이션을 만들고 서비스를 구축하는 방법을 살펴보겠습니다.

## <a name="prerequisites"></a>필수 조건
시작하기 전에 [Linux 개발 환경을 설정](service-fabric-get-started-linux.md)하도록 합니다. Mac OS X을 사용하는 경우 [Vagrant를 사용하여 가상 머신에서 Linux one-box 환경을 설정](service-fabric-get-started-mac.md)할 수 있습니다.

또한 [Service Fabric CLI](service-fabric-cli.md)를 설치합니다.

### <a name="install-and-set-up-the-generators-for-c"></a>C#용 생성기 설치 및 설정
Service Fabric은 Yeoman 템플릿 생성기를 사용하여 터미널에서 Service Fabric 애플리케이션을 만들 수 있는 스캐폴딩 도구를 제공합니다. C#용 Service Fabric Yeoman 템플릿 생성기를 설정하려면 이러한 단계를 수행합니다.

1. 컴퓨터에서 Node.js 및 NPM 설치

   ```bash
   curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.0/install.sh | bash 
   nvm install node 
   ```
2. NPM의 컴퓨터에 [Yeoman](https://yeoman.io/) 템플릿 생성기 설치

   ```bash
   npm install -g yo
   ```
3. NPM에서 Service Fabric Yeoman C# 애플리케이션 생성기 설치

   ```bash
   npm install -g generator-azuresfcsharp
   ```

## <a name="create-the-application"></a>애플리케이션 만들기
Service Fabric 애플리케이션은 애플리케이션의 기능을 제공하는 특정 역할이 있는 하나 이상의 서비스를 포함할 수 있습니다. 마지막 단계에서 설치한 C#용 Service Fabric [Yeoman](https://yeoman.io/) 생성기를 통해 쉽게 첫 번째 서비스를 만들고 나중에 더 추가할 수 있습니다. 단일 서비스를 사용하여 애플리케이션을 만들기 위해 Yeoman을 사용해 보겠습니다.

1. 터미널에서 다음 명령을 입력하여 스캐폴딩 구축을 시작합니다. `yo azuresfcsharp`
2. 애플리케이션의 이름을 지정합니다.
3. 첫 번째 서비스의 형식을 선택하고 이름을 지정합니다. 이 자습서의 목적에 따라 Reliable Actor 서비스를 선택합니다.

   ![C#용 Service Fabric Yeoman 생성기][sf-yeoman]

> [!NOTE]
> 옵션에 대한 자세한 내용은 [서비스 패브릭 프로그래밍 모델 개요](service-fabric-choose-framework.md)를 참조하세요.
>
>

## <a name="build-the-application"></a>애플리케이션 빌드
Service Fabric Yeoman 템플릿은 빌드 스크립트를 포함하며 이것을 사용하여 터미널에서 앱을 빌드할 수 있습니다(애플리케이션 폴더로 이동한 후).

  ```sh
 cd myapp
 ./build.sh
  ```

## <a name="deploy-the-application"></a>애플리케이션 배포

애플리케이션이 빌드되면 로컬 클러스터에 배포할 수 있습니다.

1. 로컬 Service Fabric 클러스터에 연결합니다.

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```

2. 템플릿에 제공된 설치 스크립트를 실행하여 클러스터의 이미지 저장소에 애플리케이션 패키지를 복사하고 애플리케이션 유형을 등록하며 애플리케이션의 인스턴스를 만듭니다.

    ```bash
    ./install.sh
    ```

빌드된 애플리케이션을 배포하는 방법은 다른 Service Fabric 애플리케이션과 같습니다. 자세한 지침은 [Service Fabric CLI에서 Service Fabric 애플리케이션 관리](service-fabric-application-lifecycle-sfctl.md)에 대한 설명서를 참조하세요.

애플리케이션 패키지 내에 생성된 매니페스트에서 이러한 명령의 매개 변수를 찾을 수 있습니다.

애플리케이션이 배포되면 브라우저를 열고 [http://localhost:19080/Explorer](http://localhost:19080/Explorer)에 있는 [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)로 이동합니다. 그런 다음 **애플리케이션** 노드를 확장하면 애플리케이션 형식에 대한 항목 및 해당 형식의 첫 번째 인스턴스에 대한 다른 항목이 만들어집니다.

> [!IMPORTANT]
> 애플리케이션을 Azure의 보안 Linux 클러스터에 배포하려면 Service Fabric 런타임으로 애플리케이션의 유효성을 검사하는 인증서를 구성해야 합니다. 이렇게 하면 Reliable Services 서비스에서 기본 Service Fabric 런타임 API와 통신할 수 있습니다. 자세히 알아보려면 [Linux 클러스터에서 실행하도록 Reliable Services 앱 구성](./service-fabric-configure-certificates-linux.md#configure-a-reliable-services-app-to-run-on-linux-clusters)을 참조하세요.  
>

## <a name="start-the-test-client-and-perform-a-failover"></a>테스트 클라이언트 시작 및 장애 조치 수행
행위자 프로젝트 자체에서는 아무 것도 수행하지 않습니다. 메시지를 보낼 다른 서비스 또는 클라이언트가 필요합니다. 행위자 템플릿은 행위자 서비스와 상호 작용하는 데 사용할 수 있는 간단한 테스트 스크립트를 포함합니다.

1. 행위자 서비스의 출력을 확인하려면 조사식 유틸리티를 사용하여 스크립트를 실행합니다.

   MAC OS X의 경우 다음 추가 명령을 실행 하 여 컨테이너 내에서 동일한 위치로 myactorsvcTestClient 폴더를 복사 해야 합니다.
    
    ```bash
    docker cp  [first-four-digits-of-container-ID]:/home
    docker exec -it [first-four-digits-of-container-ID] /bin/bash
    cd /home
    ```
    
    ```bash
    cd myactorsvcTestClient
    watch -n 1 ./testclient.sh
    ```
2. Service Fabric Explorer에서 행위자 서비스에 대한 기본 복제본을 호스팅하는 노드를 찾습니다. 아래 스크린샷에 있는 노드 3입니다.

    ![Service Fabric Explorer에서 기본 복제본 찾기][sfx-primary]
3. 이전 단계에서 찾은 노드를 클릭한 다음 작업 메뉴에서 **비활성화(다시 시작)** 를 선택합니다. 이 작업은 다른 노드에서 실행 중인 보조 복제본에 장애 조치를 강제하는 로컬 클러스터에서 하나의 노드를 다시 시작합니다. 이 작업을 수행하면 테스트 클라이언트에서 출력에 주의하고 장애 조치에도 불구하고 카운터가 계속 증가하게 됩니다.

## <a name="adding-more-services-to-an-existing-application"></a>기존 애플리케이션에 더 많은 서비스 추가

`yo`을 사용하여 만든 애플리케이션에 다른 서비스를 추가하려면 다음 단계를 수행합니다.
1. 기존 애플리케이션의 루트로 디렉터리를 변경합니다.  예를 들어 `MyApplication`이 Yeoman에서 만든 애플리케이션인 경우 `cd ~/YeomanSamples/MyApplication`입니다.
2. `yo azuresfcsharp:AddService` 실행

## <a name="next-steps"></a>다음 단계

* [Service Fabric CLI를 사용하여 Service Fabric 클러스터와 상호 작용](service-fabric-cli.md)
* [Service Fabric 지원 옵션](service-fabric-support.md) 알아보기
* [Service Fabric CLI 시작](service-fabric-cli.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-create-your-first-linux-application-with-csharp/yeoman-csharp.png
[sfx-primary]: ./media/service-fabric-create-your-first-linux-application-with-csharp/sfx-primary.png
