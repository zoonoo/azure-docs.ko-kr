---
title: "Azure Service Fabric에서 작업하도록 Mac OS X에서 개발 환경 설정 | Microsoft Docs"
description: "런타임, SDK 및 도구를 설치하고 로컬 개발 클러스터를 만듭니다. 이 설정을 완료하면 Mac OS X에서 응용 프로그램을 빌드할 수 있습니다."
services: service-fabric
documentationcenter: java
author: sayantancs
manager: timlt
editor: 
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: java
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/21/2017
ms.author: saysa
ms.translationtype: HT
ms.sourcegitcommit: cf381b43b174a104e5709ff7ce27d248a0dfdbea
ms.openlocfilehash: 8b4fc0ab9034263418cac42ced203035e0a8fcad
ms.contentlocale: ko-kr
ms.lasthandoff: 08/23/2017

---
# <a name="set-up-your-development-environment-on-mac-os-x"></a>Mac OS X에서 개발 환경 설정
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md)
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
>
>  

Mac OS X를 사용하여 Service Fabric 응용 프로그램을 Linux 클러스터에서 실행하도록 빌드할 수 있습니다. 이 문서에서는 개발을 위해 Mac을 설정하는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 조건
Service Fabric은 OS X에서 고유하게 실행되지 않습니다. 로컬 Service Fabric 클러스터를 실행하기 위해 Vagrant 및 VirtualBox를 사용하여 미리 구성된 Ubuntu 가상 컴퓨터를 제공합니다. 시작하기 전에 다음 항목이 필요합니다.

* [Vagrant(v1.8.4 이상)](http://www.vagrantup.com/downloads.html)
* [VirtualBox](http://www.virtualbox.org/wiki/Downloads)

>[!NOTE]
> Vagrant 및 VirtualBox에서 상호 간에 지원되는 버전을 사용해야 합니다. 지원되지 않는 VirtualBox 버전에서는 Vagrant가 비정상적으로 동작할 수 있습니다.
>

## <a name="create-the-local-vm"></a>로컬 VM 만들기
5개 노드의 Service Fabric 클러스터를 포함하는 로컬 VM을 만들려면 다음 단계를 수행합니다.

1. `Vagrantfile` 리포지토리를 복제합니다.

    ```bash
    git clone https://github.com/azure/service-fabric-linux-vagrant-onebox.git
    ```
    이 단계에서는 VM을 다운로드할 위치와 함께 VM 구성을 포함하는 파일 `Vagrantfile`을 가져옵니다.

2. 리포지토리의 로컬 클론으로 이동

    ```bash
    cd service-fabric-linux-vagrant-onebox
    ```
3. (선택 사항)기본 VM 설정 수정

    기본적으로 로컬 VM은 다음과 같이 구성됩니다.

   * 3GB의 메모리 할당
   * Mac 호스트에서 트래픽을 통과시키는 데 IP 192.168.50.50에서 구성된 개인 호스트 네트워크 사용

     이러한 설정 중 하나를 변경하거나 `Vagrantfile`의 VM에 다른 구성을 추가할 수 있습니다. 구성 옵션의 전체 목록은 [Vagrant 설명서](http://www.vagrantup.com/docs)를 참조하세요.
4. VM 만들기

    ```bash
    vagrant up
    ```

   이 단계에서는 미리 구성된 VM 이미지를 다운로드하고 로컬로 부팅한 다음 로컬 Service Fabric 클러스터를 설정합니다. 배포를 완료하려면 몇 분 정도가 걸립니다. 설치가 성공적으로 완료되면 출력에 클러스터가 시작되었음을 나타내는 메시지가 표시됩니다.

    ![다음 VM 프로비전을 시작하는 클러스터 설치][cluster-setup-script]

    >[!TIP]
    > VM을 다운로드하는 데 오랜 시간이 소요되면 wget 또는 curl을 사용하거나 `Vagrantfile` 파일에서 **config.vm.box_url**에 지정된 링크에 이동하여 브라우저를 통해 다운로드할 수 있습니다. 로컬에서 다운로드한 후에 `Vagrantfile` 이미지가 다운로드한 로컬 경로를 가리키도록 편집합니다. 예를 들어 /home/users/test/azureservicefabric.tp8.box에 이미지를 다운로드하는 경우 해당 경로에 **config.vm.box_url**을 설정합니다.
    >

5. http://192.168.50.50:19080/Explorer에서 Service Fabric Explorer로 이동하여 클러스터가 올바르게 설정되었는지 테스트합니다(기본 개인 네트워크 IP를 유지한다고 가정함).

    ![호스트 Mac에서 본 Service Fabric Explorer][sfx-mac]


## <a name="create-application-on-mac-using-yeoman"></a>Yeoman을 사용하여 Mac에서 응용 프로그램 만들기
Service Fabric은 Yeoman 템플릿 생성기를 사용하여 터미널에서 Service Fabric 응용 프로그램을 만들 수 있는 스캐폴딩 도구를 제공합니다. 컴퓨터에서 Service Fabric Yeoman 템플릿 생성기가 작동하는지 확인하려면 다음 단계를 따르세요.

1. Mac에 Node.js 및 NPM이 설치되어 있어야 합니다. 그렇지 않으면 다음을 사용하여 Homebrew를 사용하는 Node.js 및 NPM을 설치할 수 있습니다. Mac에 설치된 Node.js 및 NPM의 버전을 확인하려면 ``-v`` 옵션을 사용할 수 있습니다.

  ```bash
  brew install node
  node -v
  npm -v
  ```
2. NPM의 컴퓨터에 [Yeoman](http://yeoman.io/) 템플릿 생성기 설치

  ```bash
  npm install -g yo
  ```
3. 사용하려는 Yeoman 생성기를 설치하고 [설명서](service-fabric-get-started-linux.md)를 시작하는 단계를 따릅니다. Yeoman을 사용하여 Service Fabric 응용 프로그램을 만들려면 다음 단계에 따릅니다.

  ```bash
  npm install -g generator-azuresfjava       # for Service Fabric Java Applications
  npm install -g generator-azuresfguest      # for Service Fabric Guest executables
  npm install -g generator-azuresfcontainer  # for Service Fabric Container Applications
  ```
4. Mac에서 Service Fabric Java 응용 프로그램을 빌드하려면 컴퓨터에 JDK 1.8 및 Gradle을 설치해야 합니다.


## <a name="install-the-service-fabric-plugin-for-eclipse-neon"></a>Eclipse Neon용 Service Fabric 플러그 인 설치

Service Fabric은 Java 서비스를 만들고 빌드하고 배포하는 프로세스를 간소화할 수 있는 **Java IDE용 Eclipse Neon**에 대한 플러그 인을 제공합니다. Service Fabric Eclipse 플러그 인 설치 또는 업데이트에 대한 일반적인 [설명서](service-fabric-get-started-eclipse.md#install-or-update-the-service-fabric-plug-in-in-eclipse-neon)에 나와 있는 설치 단계를 따르면 됩니다.

>[!TIP]
> 생성된 응용 프로그램 ``Local.json``의 ``Vagrantfile``에서 설명한 것처럼 기본적으로 기본 IP를 지원합니다. 해당 IP를 변경하고 다른 IP로 Vagrant를 배포하는 경우에 응용 프로그램의 ``Local.json``에서 해당 IP를 업데이트하세요.

## <a name="next-steps"></a>다음 단계
<!-- Links -->
* [Yeoman을 사용하여 Linux에서 첫 번째 Service Fabric Java 응용 프로그램 만들기 및 배포](service-fabric-create-your-first-linux-application-with-java.md)
* [Eclipse용 Service Fabric 플러그 인을 사용하여 Linux에서 첫 번째 Service Fabric Java 응용 프로그램 만들기 및 배포](service-fabric-get-started-eclipse.md)
* [Azure Portal에서 Service Fabric 클러스터 만들기](service-fabric-cluster-creation-via-portal.md)
* [Azure 리소스 관리자를 사용하여 Service Fabric 클러스터 만들기](service-fabric-cluster-creation-via-arm.md)
* [Service Fabric 응용 프로그램 모델 이해](service-fabric-application-model.md)

<!-- Images -->
[cluster-setup-script]: ./media/service-fabric-get-started-mac/cluster-setup-mac.png
[sfx-mac]: ./media/service-fabric-get-started-mac/sfx-mac.png
[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-mac/sf-eclipse-plugin-install.png
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

