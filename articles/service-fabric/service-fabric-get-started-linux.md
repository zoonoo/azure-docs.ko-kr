---
title: "Linux에서 개발 환경 설정 | Microsoft Docs"
description: "Linux에서 런타임 및 SDK를 설치하고 로컬 개발 클러스터를 만듭니다. 이 설정을 마치면 응용 프로그램을 빌드할 수 있습니다."
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: d552c8cd-67d1-45e8-91dc-871853f44fc6
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/23/2017
ms.author: subramar
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 516b8e517a16dd0d87e02189260166696225fbab
ms.lasthandoff: 03/29/2017


---
# <a name="prepare-your-development-environment-on-linux"></a>Linux에서 개발 환경 준비
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md)
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
>
>  

 Linux 개발 컴퓨터에서 [Azure Service Fabric 응용 프로그램](service-fabric-application-model.md) 을 배포하고 실행하려면 런타임 및 일반적인 SDK를 설치해야 합니다. 또한 Java 및 .NET Core용 선택적 SDK를 설치할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

### <a name="supported-operating-system-versions"></a>지원되는 운영 체제 버전
개발을 위해 다음 운영 체제 버전이 지원됩니다.

* Ubuntu 16.04(i**"Xenial Xerus"**)

## <a name="update-your-apt-sources"></a>APT 원본 업데이트
apt-get을 통해 SDK 및 관련된 런타임 패키지를 설치하려면 먼저 APT 원본을 업데이트해야 합니다.

1. 터미널을 엽니다.
2. Service Fabric 리포지토리를 원본 목록에 추가합니다.

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] http://apt-mo.trafficmanager.net/repos/servicefabric/ trusty main" > /etc/apt/sources.list.d/servicefabric.list'
    ```
3. **dotnet** 리포지토리를 원본 목록에 추가합니다.

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/dotnet-release/ xenial main" > /etc/apt/sources.list.d/dotnetdev.list'
    ```
4. APT 키링에 새 GPG 키를 추가합니다.

    ```bash
    sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
    ```
    ```bash
    sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 417A0893
    ```

5. 새로 추가된 리포지토리에 따라 패키지 목록을 새로 고칩니다.

    ```bash
    sudo apt-get update
    ```
## <a name="install-and-set-up-the-sdk-for-containers-and-guest-executables"></a>컨테이너 및 게스트 실행 파일에 SDK 설치 및 설정
원본을 업데이트하면 SDK를 설치할 수 있습니다.

1. Service Fabric SDK 패키지를 설치합니다. 설치를 확인하고 라이선스 규약에 동의하라는 메시지가 표시됩니다.

    ```bash
    sudo apt-get install servicefabricsdkcommon
    ```
2. SDK 설치 스크립트를 실행합니다.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/sdkcommonsetup.sh
    ```

일반적인 SDK 패키지를 설치하는 단계를 실행하면 `yo azuresfguest`을 실행하여 게스트 실행 파일 또는 컨테이너 서비스를 사용하여 앱을 만들 수 있습니다. 노드 모듈이 있는 위치에 **$NODE_PATH** 환경 변수를 설정해야 합니다. 

    ```bash
    export NODE_PATH=$NODE_PATH:$HOME/.node/lib/node_modules 
    ```

환경을 루트로 사용하는 경우 다음 명령을 사용하여 변수를 설정해야 합니다.

    ```bash
    export NODE_PATH=$NODE_PATH:/root/.node/lib/node_modules 
    ```

> [!TIP]
> 로그인할 때마다 환경 변수를 설정하지 않아도 되도록 ~/.bashrc 파일에 이러한 명령을 추가하려고 합니다.
>

## <a name="set-up-the-azure-cross-platform-cli"></a>Azure 플랫폼 간 CLI 설정
[Azure 플랫폼 간 CLI][azure-xplat-cli-github]는 클러스터 및 응용 프로그램을 비롯하여 Service Fabric 엔터티와 상호 작용하기 위한 명령을 포함합니다. 다음 지침을 계속 진행하기 전에 Node.js에 기반하여 [노드를 설치했는지 확인][install-node]합니다.

1. 개발 컴퓨터에 GitHub 리포지토리를 복제합니다.

    ```bash
    git clone https://github.com/Azure/azure-xplat-cli.git
    ```
2. NPM(Node Package Manager)을 사용하여 복제된 리포지토리로 전환하고 CLI의 종속성을 설치합니다.

    ```bash
    cd azure-xplat-cli
    npm install
    ```
3. 복제된 bin/azure 폴더에서 /usr/bin/azure 폴더에 symlink를 만들어서 경로에 추가하고 모든 디렉터리에서 사용할 수 있도록 합니다.

    ```bash
    sudo ln -s $(pwd)/bin/azure /usr/bin/azure
    ```
4. 마지막으로 자동 완성 Service Fabric 명령을 사용하도록 설정합니다.

    ```bash
    azure --completion >> ~/azure.completion.sh
    echo 'source ~/azure.completion.sh' >> ~/.bash_profile
    source ~/azure.completion.sh
    ```

> [!NOTE]
> Service Fabric 명령을 Azure CLI 2.0에서 사용할 수 없습니다.


## <a name="set-up-a-local-cluster"></a>로컬 클러스터를 설정합니다.
모든 항목을 성공적으로 설치하면 로컬 클러스터를 시작할 수 있습니다.

1. 클러스터 설치 스크립트를 실행합니다.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```
2. 웹 브라우저를 열고 http://localhost:19080/Explorer로 이동합니다. 클러스터를 시작하는 경우 Service Fabric Explorer 대시보드가 표시됩니다.

    ![Linux의 Service Fabric Explorer][sfx-linux]

이 시점에서 게스트 컨테이너 또는 게스트 실행 파일에 따라 미리 빌드된 Service Fabric 응용 프로그램 패키지 또는 새 응용 프로그램 패키지를 배포할 수 있습니다. Java 또는 .NET Core SDK를 사용하여 새 서비스를 빌드하려면 후속 선택에서 제공되는 옵션 설정 단계를 수행합니다.


> [!NOTE]
> 독립 실행형 클러스터는 Linux에서 지원되지 않습니다 미리 보기에서는 one box 및 Azure Linux 다중 컴퓨터 클러스터만 지원됩니다.
>

## <a name="install-the-java-sdk-optional-if-you-wish-to-use-the-java-programming-models"></a>Java SDK 설치(선택 사항, Java 프로그래밍 모델을 사용하려는 경우)
Java SDK에서는 Java를 사용하여 Service Fabric 서비스를 빌드하는 데 필요한 라이브러리 및 템플릿을 제공합니다.

1. Java SDK 패키지를 설치합니다.

    ```bash
    sudo apt-get install servicefabricsdkjava
    ```
2. SDK 설치 스크립트를 실행합니다.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/java/sdkjavasetup.sh
    ```
## <a name="install-the-eclipse-neon-plugin-optional"></a>Eclipse Neon 플러그 인 설치(선택 사항)

**Java 개발자를 위한 Eclipse IDE** 내에서 Service Fabric용 Eclipse 플러그 인을 설치할 수 있습니다. Eclipse를 사용하여 Service Fabric Java 응용 프로그램 외에도 Service Fabric 게스트 실행 파일 응용 프로그램 및 컨테이너 응용 프로그램을 만들 수 있습니다.

> [!NOTE]
> Java SDK를 설치하는 작업은 게스트 실행 파일 및 컨테이너 응용 프로그램을 만들고 배포하는 경우에도 Eclipse 플러그 인을 사용하는 필수 구성 요소입니다.
>

1. Eclipse에서, 최신 Eclipse **Neon** 및 Buildship 버전(1.0.17 이상)을 설치하도록 합니다. **도움말 > 설치 세부 정보**를 선택하여 설치된 구성 요소의 버전을 확인할 수 있습니다. [여기][buildship-update]에 있는 지침을 사용하여 Buildship을 업데이트할 수 있습니다.
2. Service Fabric 플러그 인을 설치하려면 **도움말 > 새 소프트웨어 설치...**를 선택합니다.
3. "사용" 텍스트 상자에 http://dl.windowsazure.com/eclipse/servicefabric을 입력합니다.
4. 추가를 클릭합니다.
    ![Eclipse 플러그 인][sf-eclipse-plugin]
5. Service Fabric 플러그 인을 선택하고 [다음]을 클릭합니다.
6. 설치를 계속하고 최종 사용자 라이선스 규약에 동의합니다.

Service Fabric Eclipse 플러그 인이 이미 설치된 경우 최신 버전인지 확인합니다. 설치된 플러그 인 목록에서 ``Help => Installation Details``을 선택하고 Service Fabric을 검색하여 확인할 수 있습니다. 최신 버전을 사용할 수 있는 경우 업데이트를 선택합니다. 

자세한 내용은 [Eclipse를 사용하여 Service Fabric 시작](service-fabric-get-started-eclipse.md)을 참조하세요.


## <a name="install-the-net-core-sdk-optional-if-you-wish-to-use-the-net-core-programming-models"></a>.NET Core SDK 설치(선택 사항, .NET Core 프로그래밍 모델을 사용하려는 경우)
.NET Core SDK에서는 플랫폼 간 .NET Core를 사용하여 Service Fabric 서비스를 빌드하는 데 필요한 라이브러리 및 템플릿을 제공합니다.

1. .NET Core SDK 패키지를 설치합니다.

   ```bash
   sudo apt-get install servicefabricsdkcsharp
   ```

2. SDK 설치 스크립트를 실행합니다.

   ```bash
   sudo /opt/microsoft/sdk/servicefabric/csharp/sdkcsharpsetup.sh
   ```

## <a name="updating-the-sdk-and-runtime"></a>SDK 및 런타임 업데이트

최신 버전 SDK 및 런타임으로 업데이트하려면 다음 단계를 실행합니다(업데이트하거나 설치하려는 목록에서 SDK 제거).

   ```bash
   sudo apt-get update
   sudo apt-get install servicefabric servicefabricsdkcommon servicefabricsdkcsharp servicefabricsdkjava
   ```

CLI를 업데이트하는 경우 CLI를 복제한 디렉터리로 이동하고 업데이트할 `git pull`을 실행합니다.  업데이트를 위한 추가 단계가 필요한 경우 릴리스 정보는 해당 단계를 지정합니다. 


## <a name="next-steps"></a>다음 단계
* [Yeoman을 사용하여 Linux에서 첫 번째 Service Fabric Java 응용 프로그램 만들기 및 배포](service-fabric-create-your-first-linux-application-with-java.md)
* [Eclipse용 Service Fabric 플러그 인을 사용하여 Linux에서 첫 번째 Service Fabric Java 응용 프로그램 만들기 및 배포](service-fabric-get-started-eclipse.md)
* [Linux에서 첫 번째 CSharp 응용 프로그램 만들기](service-fabric-create-your-first-linux-application-with-csharp.md)
* [OSX에서 개발 환경 준비](service-fabric-get-started-mac.md)
* [Azure CLI를 사용하여 Service Fabric 응용 프로그램 관리](service-fabric-azure-cli.md)
* [Service Fabric Windows/Linux 간 차이점](service-fabric-linux-windows-differences.md)

<!-- Links -->

[azure-xplat-cli-github]: https://github.com/Azure/azure-xplat-cli
[install-node]: https://nodejs.org/en/download/package-manager/#installing-node-js-via-package-manager
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

<!--Images -->

[sf-eclipse-plugin]: ./media/service-fabric-get-started-linux/service-fabric-eclipse-plugin.png
[sfx-linux]: ./media/service-fabric-get-started-linux/sfx-linux.png

