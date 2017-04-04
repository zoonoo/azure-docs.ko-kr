---
title: "Mac OS X에서 개발 환경 설정 | Microsoft Docs"
description: "런타임, SDK 및 도구를 설치하고 로컬 개발 클러스터를 만듭니다. 이 설정을 완료하면 Mac OS X에서 응용 프로그램을 빌드할 수 있습니다."
services: service-fabric
documentationcenter: java
author: saysa
manager: raunakp
editor: 
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: java
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/27/2016
ms.author: saysa
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: e5d14eb0a656d67030f4c0d3d510aec0e9cafae7
ms.lasthandoff: 03/29/2017


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

     이러한 설정 중 하나를 변경하거나 `Vagrantfile`의 VM에 다른 구성을 추가할 수 있습니다. 구성 옵션의 전체 목록은 [Vagrant 설명서](http://www.vagrantup.com/docs) 를 참조하세요.
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

## <a name="install-the-service-fabric-plugin-for-eclipse-neon"></a>Eclipse Neon용 Service Fabric 플러그 인 설치

Service Fabric은 Java 서비스를 만들고 빌드하고 배포하는 프로세스를 간소화할 수 있는 **Java IDE용 Eclipse Neon**에 대한 플러그 인을 제공합니다. Service Fabric Eclipse 플러그 인 설치 또는 업데이트에 대한 일반적인 [설명서](service-fabric-get-started-eclipse.md#install-or-update-the-service-fabric-plug-in-in-eclipse-neon)에 나와 있는 설치 단계를 따르면 됩니다.

## <a name="using-service-fabric-eclipse-plugin-on-mac"></a>Mac에서 Service Fabric Eclipse 플러그 인 사용

[Service Fabric Eclipse 플러그 인 설명서](service-fabric-get-started-eclipse.md)에 나와 있는 단계를 완료했는지 확인합니다. Mac 호스트에서 vagrant-guest 컨테이너를 사용하여 Service Fabric Java 응용 프로그램을 만들고 빌드하며 배포하는 단계는 다음과 같은 사항을 제외하고 대부분 일반적인 설명서와 같습니다.

* Service Fabric Java 응용 프로그램을 성공적으로 빌드하는 데 Service Fabric 라이브러리가 필요하므로 Eclipse 프로젝트를 공유 경로에 생성해야 합니다. 기본적으로 ``Vagrantfile``이 존재하는 호스트에서 경로의 내용은 게스트에서 ``/vagrant`` 경로와 공유됩니다.
* ``~/home/john/allprojects/`` 경로에 ``Vagrantfile``이 있는 경우 ``~/home/john/allprojects/MyActor`` 위치에 ``MyActor``라는 Service Fabric 프로젝트를 만들어야 하며 Eclipse 작업 영역에 대한 경로는 ``~/home/john/allprojects``입니다.

## <a name="next-steps"></a>다음 단계
<!-- Links -->
* [Yeoman을 사용하여 Linux에서 첫 번째 Service Fabric Java 응용 프로그램 만들기 및 배포](service-fabric-create-your-first-linux-application-with-java.md)
* [Eclipse용 Service Fabric 플러그 인을 사용하여 Linux에서 첫 번째 Service Fabric Java 응용 프로그램 만들기 및 배포](service-fabric-get-started-eclipse.md)
* [Azure Portal에서 Service Fabric 클러스터 만들기](service-fabric-cluster-creation-via-portal.md)
* [Azure Resource Manager를 사용하여 Service Fabric 클러스터 만들기](service-fabric-cluster-creation-via-arm.md)
* [Service Fabric 응용 프로그램 모델 이해](service-fabric-application-model.md)

<!-- Images -->
[cluster-setup-script]: ./media/service-fabric-get-started-mac/cluster-setup-mac.png
[sfx-mac]: ./media/service-fabric-get-started-mac/sfx-mac.png
[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-mac/sf-eclipse-plugin-install.png
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

