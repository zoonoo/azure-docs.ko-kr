---
title: "C#을 사용하여 Linux에서 첫 번째 Azure 마이크로 서비스 만들기 | Microsoft Docs"
description: "C#을 사용하여 Service Fabric 응용 프로그램 만들기 및 배포"
services: service-fabric
documentationcenter: csharp
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: 5a96d21d-fa4a-4dc2-abe8-a830a3482fb1
ms.service: service-fabric
ms.devlang: csharp
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 6/28/2017
ms.author: subramar
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: 4baf144cc28eeff0ab8f8b60e837f8a2bad903af
ms.contentlocale: ko-kr
ms.lasthandoff: 07/01/2017


---
# 첫 번째 Azure Service Fabric 응용 프로그램 만들기
<a id="create-your-first-azure-service-fabric-application" class="xliff"></a>
> [!div class="op_single_selector"]
> * [C# - Windows](service-fabric-create-your-first-application-in-visual-studio.md)
> * [Java - Linux](service-fabric-create-your-first-linux-application-with-java.md)
> * [C# - Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
>
>

Service Fabric은 .NET Core 및 Java 모두에서 Linux에 대한 서비스를 구축하는 SDK를 제공합니다. 이 자습서에서는 C#(.NET Core)을 사용하여 Linux용 응용 프로그램을 만들고 서비스를 구축하는 방법을 살펴보겠습니다.

## 필수 조건
<a id="prerequisites" class="xliff"></a>
시작하기 전에 [Linux 개발 환경을 설정](service-fabric-get-started-linux.md)하도록 합니다. Mac OS X을 사용하는 경우 [Vagrant를 사용하여 가상 컴퓨터에서 Linux one-box 환경을 설정](service-fabric-get-started-mac.md)할 수 있습니다.

응용 프로그램을 배포하기 위해 [Azure CLI 2.0](service-fabric-azure-cli-2-0.md)(권장) 또는 [XPlat CLI](service-fabric-azure-cli.md)를 구성할 수도 있습니다.

## 응용 프로그램 만들기
<a id="create-the-application" class="xliff"></a>
서비스 패브릭 응용 프로그램은 응용 프로그램의 기능을 제공하는 특정 역할이 있는 하나 이상의 서비스를 포함할 수 있습니다. Linux용 Service Fabric SDK는 쉽게 첫 번째 서비스를 만들고 나중에 더 추가할 수 있는 [Yeoman](http://yeoman.io/) 생성기를 포함합니다. 단일 서비스를 사용하여 응용 프로그램을 만들기 위해 Yeoman을 사용해 보겠습니다.

1. 터미널에서 다음 명령을 입력하여 스캐폴딩 구축을 시작합니다. `yo azuresfcsharp`
2. 응용 프로그램의 이름을 지정합니다.
3. 첫 번째 서비스의 형식을 선택하고 이름을 지정합니다. 이 자습서의 목적에 따라 Reliable Actor 서비스를 선택합니다.

   ![C#용 Service Fabric Yeoman 생성기][sf-yeoman]

> [!NOTE]
> 옵션에 대한 자세한 내용은 [서비스 패브릭 프로그래밍 모델 개요](service-fabric-choose-framework.md)를 참조하세요.
>
>

## 응용 프로그램 빌드
<a id="build-the-application" class="xliff"></a>
Service Fabric Yeoman 템플릿은 빌드 스크립트를 포함하며 이것을 사용하여 터미널에서 앱을 빌드할 수 있습니다(응용 프로그램 폴더로 이동한 후).

  ```sh
 cd myapp
 ./build.sh
  ```

## 응용 프로그램 배포
<a id="deploy-the-application" class="xliff"></a>

응용 프로그램이 빌드되면 로컬 클러스터에 배포할 수 있습니다.

### 플랫폼 간 CLI 사용
<a id="using-xplat-cli" class="xliff"></a>

1. 로컬 Service Fabric 클러스터에 연결합니다.

    ```bash
    azure servicefabric cluster connect
    ```

2. 템플릿에 제공된 설치 스크립트를 실행하여 클러스터의 이미지 저장소에 응용 프로그램 패키지를 복사하고 응용 프로그램 유형을 등록하며 응용 프로그램의 인스턴스를 만듭니다.

    ```bash
    ./install.sh
    ```

### Azure CLI 2.0 사용
<a id="using-azure-cli-20" class="xliff"></a>

빌드된 응용 프로그램을 배포하는 방법은 다른 Service Fabric 응용 프로그램과 같습니다. 자세한 지침은 [Azure CLI로 Service Fabric 응용 프로그램 관리](service-fabric-application-lifecycle-azure-cli-2-0.md)에 대한 설명서를 참조하세요.

응용 프로그램 패키지 내에 생성된 매니페스트에서 이러한 명령의 매개 변수를 찾을 수 있습니다.

응용 프로그램이 배포되면 브라우저를 열고 [http://localhost:19080/Explorer](http://localhost:19080/Explorer)에 있는 [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)로 이동합니다.
그런 다음 **응용 프로그램** 노드를 확장하면 응용 프로그램 형식에 대한 항목 및 해당 형식의 첫 번째 인스턴스에 대한 다른 항목이 만들어집니다.

## 테스트 클라이언트 시작 및 장애 조치 수행
<a id="start-the-test-client-and-perform-a-failover" class="xliff"></a>
행위자 프로젝트 자체에서는 아무 것도 수행하지 않습니다. 메시지를 보낼 다른 서비스 또는 클라이언트가 필요합니다. 행위자 템플릿은 행위자 서비스와 상호 작용하는 데 사용할 수 있는 간단한 테스트 스크립트를 포함합니다.

1. 행위자 서비스의 출력을 확인하려면 조사식 유틸리티를 사용하여 스크립트를 실행합니다.

    ```bash
    cd myactorsvcTestClient
    watch -n 1 ./testclient.sh
    ```
2. Service Fabric Explorer에서 행위자 서비스에 대한 기본 복제본을 호스팅하는 노드를 찾습니다. 아래 스크린샷에 있는 노드 3입니다.

    ![Service Fabric Explorer에서 기본 복제본 찾기][sfx-primary]
3. 이전 단계에서 찾은 노드를 클릭한 다음 작업 메뉴에서 **비활성화(다시 시작)** 를 선택합니다. 이 작업은 다른 노드에서 실행 중인 보조 복제본에 장애 조치를 강제하는 로컬 클러스터에서 하나의 노드를 다시 시작합니다. 이 작업을 수행하면 테스트 클라이언트에서 출력에 주의하고 장애 조치에도 불구하고 카운터가 계속 증가하게 됩니다.

## 기존 응용 프로그램에 더 많은 서비스 추가
<a id="adding-more-services-to-an-existing-application" class="xliff"></a>

`yo`을 사용하여 만든 응용 프로그램에 다른 서비스를 추가하려면 다음 단계를 수행합니다. 
1. 기존 응용 프로그램의 루트로 디렉터리를 변경합니다.  예를 들어 `MyApplication`이 Yeoman에서 만든 응용 프로그램인 경우 `cd ~/YeomanSamples/MyApplication`입니다.
2. `yo azuresfcsharp:AddService`을 실행합니다.

## project.json에서 .csproj로 마이그레이션
<a id="migrating-from-projectjson-to-csproj" class="xliff"></a>
1. 프로젝트 루트 디렉터리에서 'dotnet migrate'를 실행하면 모든 project.json을 csproj 형식으로 마이그레이션합니다.
2. 프로젝트 파일에서 csproj 파일에 따라 프로젝트 참조를 업데이트합니다.
3. 프로젝트 파일 이름을 build.sh의 csproj 파일로 업데이트합니다.

## 다음 단계
<a id="next-steps" class="xliff"></a>
* [Reliable Actors에 대해 자세히 알아보기](service-fabric-reliable-actors-introduction.md)
* [Azure CLI를 사용하여 Service Fabric 클러스터와 상호 작용](service-fabric-azure-cli.md)
* [Service Fabric 지원 옵션](service-fabric-support.md) 알아보기

## 관련된 문서
<a id="related-articles" class="xliff"></a>

* [Service Fabric 및 Azure CLI 2.0 시작](service-fabric-azure-cli-2-0.md)
* [Service Fabric 및 XPlat CLI 시작](service-fabric-azure-cli.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-create-your-first-linux-application-with-csharp/yeoman-csharp.png
[sfx-primary]: ./media/service-fabric-create-your-first-linux-application-with-csharp/sfx-primary.png

