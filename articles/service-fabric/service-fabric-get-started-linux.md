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
ms.date: 8/23/2017
ms.author: subramar
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: 4f51030446d2d2a5a11018b1fce7d7e9193f3dfc
ms.contentlocale: ko-kr
ms.lasthandoff: 08/24/2017

---
# <a name="prepare-your-development-environment-on-linux"></a>Linux에서 개발 환경 준비
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md)
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
>
>  

Linux 개발 컴퓨터에서 [Azure Service Fabric 응용 프로그램](service-fabric-application-model.md)을 배포하고 실행하려면 런타임 및 일반적인 SDK를 설치해야 합니다. 또한 Java 및 .NET Core용 선택적 SDK를 설치할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

개발을 위해 다음 운영 체제 버전이 지원됩니다.

* Ubuntu 16.04(`Xenial Xerus`)

## <a name="update-your-apt-sources"></a>APT 원본 업데이트
apt-get 명령줄 도구를 통해 SDK 및 관련 런타임 패키지를 설치하려면 먼저 APT(Advanced Packaging Tool) 원본을 업데이트해야 합니다.

1. 터미널을 엽니다.
2. Service Fabric 리포지토리를 원본 목록에 추가합니다.

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] http://apt-mo.trafficmanager.net/repos/servicefabric/ xenial main" > /etc/apt/sources.list.d/servicefabric.list'
    ```

3. 원본 목록에 `dotnet` 리포지토리를 추가합니다.

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/dotnet-release/ xenial main" > /etc/apt/sources.list.d/dotnetdev.list'
    ```

4. 새로운 Gnu Privacy Guard(GnuPG 또는 GPG) 키를 APT 인증 키(keyring)에 추가합니다.

    ```bash
    sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
    sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 417A0893
    ```

5. APT 인증 키에 공식 Docker GPG 키를 추가합니다.

    ```bash
    sudo apt-get install curl
    sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    ```

6. Docker 리포지토리를 설정합니다.

    ```bash
    sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
    ```

7. 새로 추가된 리포지토리에 따라 패키지 목록을 새로 고칩니다.

    ```bash
    sudo apt-get update
    ```

## <a name="install-and-set-up-the-sdk-for-local-cluster-setup"></a>로컬 클러스터 설정에 SDK 설치 및 설정

원본을 업데이트하면 SDK를 설치할 수 있습니다. Service Fabric SDK 패키지를 설치하고, 설치를 확인한 다음, 사용권 계약에 동의합니다.

```bash
sudo apt-get install servicefabricsdkcommon
```

>   [!TIP]
>   다음 명령은 Service Fabric 패키지의 라이센스 수락을 자동화합니다.
>   ```bash
>   echo "servicefabric servicefabric/accepted-eula-v1 select true" | sudo debconf-set-selections
>   echo "servicefabricsdkcommon servicefabricsdkcommon/accepted-eula-v1 select true" | sudo debconf-set-selections
>   ```

## <a name="set-up-a-local-cluster"></a>로컬 클러스터를 설정합니다.
  성공적으로 설치되었으면 로컬 클러스터를 시작할 수 있습니다.

  1. 클러스터 설치 스크립트를 실행합니다.

      ```bash
      sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
      ```

  2. 웹 브라우저를 열고 [Service Fabric Explorer](http://localhost:19080/Explorer)로 이동합니다. 클러스터를 시작하는 경우 Service Fabric Explorer 대시보드가 표시됩니다.

      ![Linux의 Service Fabric Explorer][sfx-linux]

  이 시점에서 게스트 컨테이너 또는 게스트 실행 파일에 따라 미리 빌드된 Service Fabric 응용 프로그램 패키지 또는 새 응용 프로그램 패키지를 배포할 수 있습니다. Java 또는 .NET Core SDK를 사용하여 새 서비스를 빌드하려면 다음 섹션에서 제공되는 선택적 설정 단계를 수행합니다.


  > [!NOTE]
  > 독립 실행형 클러스터는 Linux에서 지원되지 않습니다. 미리 보기는 단일 상자 및 Azure Linux 다중 컴퓨터 클러스터만 지원합니다.
  >

## <a name="set-up-the-service-fabric-cli"></a>Service Fabric CLI 설정

[Service Fabric CLI](service-fabric-cli.md)는 클러스터 및 응용 프로그램을 비롯하여 Service Fabric 엔터티와 상호 작용하기 위한 명령을 포함합니다. 해당 항목은 Python에 기반하므로 다음 명령을 사용하여 계속 진행하기 전에 Python 및 PIP를 설치해야 합니다.

```bash
pip install sfctl
```

## <a name="install-and-set-up-the-generators-for-containers-and-guest-executables"></a>컨테이너 및 게스트 실행 파일에 생성기 설치 및 설정
Service Fabric은 Yeoman 템플릿 생성기를 사용하여 터미널에서 Service Fabric 응용 프로그램을 만들 수 있는 스캐폴딩 도구를 제공합니다. 컴퓨터에서 Service Fabric Yeoman 템플릿 생성기가 작동하는지 확인하려면 다음 단계를 따르세요.

1. 컴퓨터에서 Node.js 및 NPM 설치

  ```bash
  sudo apt-get install npm
  sudo apt install nodejs-legacy
  ```
2. NPM의 컴퓨터에 [Yeoman](http://yeoman.io/) 템플릿 생성기 설치

  ```bash
  sudo npm install -g yo
  ```
3. Service Fabric Yeo 컨테이너 생성기 및 NPM의 게스트 실행 파일 생성기 설치

  ```bash
  sudo npm install -g generator-azuresfcontainer  # for Service Fabric container application
  sudo npm install -g generator-azuresfguest      # for Service Fabric guest executable application
  ```

위의 생성기를 설치한 후에는 각각 `yo azuresfguest` 또는 `yo azuresfcontainer`를 실행하여 게스트 실행 파일 또는 컨테이너 서비스로 앱을 만들 수 있어야 합니다.

## <a name="install-the-necessary-java-artifacts-optional-if-you-want-to-use-the-java-programming-models"></a>필요한 Java 아티팩트 설치(선택 사항, Java 프로그래밍 모델을 사용하려는 경우)

Java를 사용하여 Service Fabric 서비스를 빌드하려면 빌드 작업을 실행하는 데 사용되는 Gradle과 함께 JDK 1.8을 설치했는지 확인합니다. 다음 코드 조각은 Gradle과 함께 Open JDK 1.8을 설치합니다. Service Fabric Java 라이브러리는 Maven에서 끌어옵니다.

  ```bash
  sudo apt-get install openjdk-8-jdk-headless
  sudo apt-get install gradle
  ```

## <a name="install-the-eclipse-neon-plug-in-optional"></a>Eclipse Neon 플러그 인 설치(선택 사항)

**Java 개발자용 Eclipse IDE** 내에 Service Fabric용 Eclipse 플러그 인을 설치할 수 있습니다. Eclipse를 사용하여 Service Fabric Java 응용 프로그램 외에도 Service Fabric 게스트 실행 파일 응용 프로그램 및 컨테이너 응용 프로그램을 만들 수 있습니다.

1. Eclipse에서 최신 Eclipse Neon 및 최신 Buildship 버전(1.0.17 이상)이 설치되어 있는지 확인합니다. **도움말** > **설치 세부 정보**를 차례로 선택하여 설치된 구성 요소의 버전을 확인할 수 있습니다. [Eclipse Buildship: Gradle용 Eclipse 플러그 인(영문)][buildship-update]의 지침을 사용하여 Buildship을 업데이트할 수 있습니다.

2. Service Fabric 플러그 인을 설치하려면 **도움말** > **새 소프트웨어 설치**를 차례로 선택합니다.

3. **Work with**(사용할 플러그 인) 상자에서 **http://dl.microsoft.com/eclipse**를 입력합니다.

4. **추가**를 클릭합니다.

    ![사용 가능한 소프트웨어 페이지][sf-eclipse-plugin]

5. **ServiceFabric** 플러그 인을 선택하고 **다음**을 클릭합니다.

6. 설치 단계를 완료한 다음 최종 사용자 사용권 계약에 동의합니다.

Service Fabric Eclipse 플러그 인이 이미 설치되어 있으면 최신 버전인지 확인합니다. **도움말** > **설치 세부 정보**를 선택한 다음 설치된 플러그 인 목록에서 Service Fabric을 검색하여 확인할 수 있습니다. 최신 버전을 사용할 수 있는 경우 **업데이트**를 선택합니다.

자세한 내용은 [Eclipse Java 응용 프로그램 배포를 위한 Azure Service Fabric 플러그 인](service-fabric-get-started-eclipse.md)을 참조하세요.


## <a name="install-the-net-core-sdk-optional-if-you-want-to-use-the-net-core-programming-models"></a>.NET Core SDK 설치(선택 사항, .NET Core 프로그래밍 모델을 사용하려는 경우)
.NET Core SDK는 .NET Core를 사용하여 Service Fabric 서비스를 빌드하는 데 필요한 라이브러리와 템플릿을 제공합니다. 다음을 실행하여 .NET Core SDK 패키지를 설치합니다.

   ```bash
   sudo apt-get install servicefabricsdkcsharp
   ```

## <a name="update-the-sdk-and-runtime"></a>SDK 및 런타임 업데이트

최신 버전의 SDK 및 런타임으로 업데이트하려면 다음 명령을 실행하십시오(원하지 않는 SDK는 선택 취소하십시오).

```bash
sudo apt-get update
sudo apt-get install servicefabric servicefabricsdkcommon servicefabricsdkcsharp
```
Maven에서 Java SDK 이진 파일을 업데이트하려면 최신 버전을 가리키도록 ``build.gradle`` 파일에서 해당 이진 파일의 버전 정보를 업데이트해야 합니다. 버전을 업데이트해야 하는 위치를 알려면 [여기](https://github.com/Azure-Samples/service-fabric-java-getting-started)에서 서비스 패브릭 시작 샘플에 있는 ``build.gradle`` 파일을 참조하면 됩니다.

> [!NOTE]
> 패키지를 업데이트하면 로컬 개발 클러스터의 실행이 중지될 수 있습니다. 이 페이지의 지침에 따라 업그레이드한 후에 로컬 클러스터를 다시 시작합니다.

## <a name="next-steps"></a>다음 단계

* [Yeoman을 사용하여 Linux에서 첫 번째 Service Fabric Java 응용 프로그램 만들기 및 배포](service-fabric-create-your-first-linux-application-with-java.md)
* [Eclipse용 Service Fabric 플러그 인을 사용하여 Linux에서 첫 번째 Service Fabric Java 응용 프로그램 만들기 및 배포](service-fabric-get-started-eclipse.md)
* [Linux에서 첫 번째 CSharp 응용 프로그램 만들기](service-fabric-create-your-first-linux-application-with-csharp.md)
* [OSX에서 개발 환경 준비](service-fabric-get-started-mac.md)
* [Service Fabric CLI를 사용하여 응용 프로그램 관리](service-fabric-application-lifecycle-sfctl.md)
* [Service Fabric Windows/Linux 간 차이점](service-fabric-linux-windows-differences.md)
* [Service Fabric CLI 시작](service-fabric-cli.md)

<!-- Links -->

[azure-xplat-cli-github]: https://github.com/Azure/azure-xplat-cli
[install-node]: https://nodejs.org/en/download/package-manager/#installing-node-js-via-package-manager
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

<!--Images -->

[sf-eclipse-plugin]: ./media/service-fabric-get-started-linux/service-fabric-eclipse-plugin.png
[sfx-linux]: ./media/service-fabric-get-started-linux/sfx-linux.png

