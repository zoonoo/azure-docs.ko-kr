---
title: Windows에서 Azure Service Fabric Linux 클러스터 설정
description: 이 문서에서는 Windows 개발 컴퓨터에서 실행되는 Service Fabric Linux 클러스터를 설정하는 방법을 알아봅니다. 이 방법은 플랫폼 간 개발에 유용합니다.
ms.topic: conceptual
ms.date: 10/16/2020
ms.openlocfilehash: 7b25a84e76773baea9f17430df1b7ba13aa661aa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93087080"
---
# <a name="set-up-a-linux-service-fabric-cluster-on-your-windows-developer-machine"></a>Windows 개발자 컴퓨터에서 Linux Service Fabric 클러스터 설정

이 문서에서는 Windows 개발 머신에서 로컬 Linux Service Fabric 클러스터를 설정하는 방법을 설명합니다. 로컬 Linux 클러스터를 설정하는 경우 Linux 클러스터를 대상으로 하지만 Windows 컴퓨터에서 개발되는 애플리케이션을 빠르게 테스트하는 데 유용합니다.

## <a name="prerequisites"></a>필수 구성 요소
Linux 기반 Service Fabric 클러스터는 Windows에서 실행되지 않지만 플랫폼 간 프로토타입이 가능하도록 Windows용 Docker를 통해 배포할 수 있는 Linux Service Fabric Onebox 클러스터 Docker 컨테이너를 제공했습니다.

시작하기 전에 다음 항목이 필요합니다.

* RAM 4GB 이상
* [Windows용 Docker](https://store.docker.com/editions/community/docker-ce-desktop-windows) 최신 버전
* Docker는 Linux 컨테이너 모드로 실행되어야 합니다.

>[!TIP]
> Windows 머신에 Docker를 설치하려면 [Docker 설명서](https://store.docker.com/editions/community/docker-ce-desktop-windows/plans/docker-ce-desktop-windows-tier?tab=instructions)의 단계를 수행합니다. 설치 후 [설치를 확인합니다](https://docs.docker.com/docker-for-windows/#check-versions-of-docker-engine-compose-and-machine).
>

## <a name="create-a-local-container-and-setup-service-fabric"></a>로컬 컨테이너 만들기 및 Service Fabric 설치
로컬 Docker 컨테이너를 설정하고 컨테이너에서 Service Fabric 클러스터를 실행하려면 다음 단계를 실행합니다.


1. 다음을 사용하여 호스트에서 Docker 디먼 구성을 업데이트하고 Docker 디먼을 다시 시작합니다. 

    ```json
    {
      "ipv6": true,
      "fixed-cidr-v6": "2001:db8:1::/64"
    }
    ```
    권장 업데이트 방법은 다음과 같습니다. 

    * Docker 아이콘 > 설정 > Docker 엔진으로 이동
    * 위에 나열된 새 필드 추가
    * 적용 및 다시 시작 - Docker 디먼을 다시 시작하여 변경 내용 적용

2. PowerShell을 통해 클러스터를 시작합니다.<br/>
    <b>Ubuntu 18.04 LTS:</b>
    ```powershell
    docker run --name sftestcluster -d -v /var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mcr.microsoft.com/service-fabric/onebox:u18
    ```

    <b>Ubuntu 16.04 LTS:</b>
    ```powershell
    docker run --name sftestcluster -d -v /var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mcr.microsoft.com/service-fabric/onebox:u16
    ```

    >[!TIP]
    > 기본적으로 이렇게 하면 최신 버전의 Service Fabric으로 이미지를 가져옵니다. 특정 수정 버전은 Docker Hub의 [Service Fabric Onebox](https://hub.docker.com/_/microsoft-service-fabric-onebox) 페이지를 참조하세요.



3. 선택 사항: 확장된 Service Fabric 이미지를 빌드합니다.

    새 디렉터리에서 사용자 지정 이미지를 빌드하기 위한 `Dockerfile`이라는 파일을 만듭니다.

    >[!NOTE]
    >Dockerfile로 위 이미지를 조정하여 컨테이너에 추가 프로그램 또는 종속성을 추가할 수 있습니다.
    >예를 들어 `RUN apt-get install nodejs -y`를 추가하면 게스트 실행 파일인 `nodejs` 애플리케이션에 대한 지원이 허용됩니다.
    ```Dockerfile
    FROM mcr.microsoft.com/service-fabric/onebox:u18
    RUN apt-get install nodejs -y
    EXPOSE 19080 19000 80 443
    WORKDIR /home/ClusterDeployer
    CMD ["./ClusterDeployer.sh"]
    ```
    
    >[!TIP]
    > 기본적으로 이렇게 하면 최신 버전의 Service Fabric으로 이미지를 가져옵니다. 특정 수정 버전은 [Docker 허브](https://hub.docker.com/r/microsoft/service-fabric-onebox/) 페이지를 참조하세요.

    `Dockerfile`에서 재사용 가능한 이미지를 빌드하려면 터미널을 열고 `Dockerfile`이 보관된 디렉터리로 `cd`한 후 다음을 실행합니다.

    ```powershell 
    docker build -t mysfcluster .
    ```
    
    >[!NOTE]
    >이 작업에 다소 시간이 걸릴 수 있지만 한 번만 수행하면 됩니다.

    이제 필요할 때마다 다음을 실행하여 신속하게 Service Fabric의 로컬 복사를 시작할 수 있습니다.

    ```powershell 
    docker run --name sftestcluster -d -v /var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mysfcluster
    ```

    >[!TIP]
    >읽기 쉬운 방식으로 처리할 수 있도록 컨테이너 인스턴스의 이름을 제공합니다. 
    >
    >애플리케이션을 특정 포트에서 수신 대기하는 경우 추가 `-p` 태그를 사용하여 포트를 지정해야 합니다. 예를 들어 애플리케이션이 포트 8080에서 수신 대기하는 경우 다음 `-p` 태그를 추가합니다.
    >
    >`docker run -itd -p 19000:19000 -p 19080:19080 -p 8080:8080 --name sfonebox mcr.microsoft.com/service-fabric/onebox:u18`
    >


4. 잠시 후 클러스터가 시작되면, 다음 명령을 사용하여 로그를 보거나 대시보드로 이동하여 클러스터 상태(`http://localhost:19080`)를 볼 수 있습니다.

    ```powershell 
    docker logs sftestcluster
    ```

5. 4단계에서 확인한 대로 클러스터가 배포된 후에는 Windows 머신에서 ``http://localhost:19080``으로 이동하여 Service Fabric Explorer 대시보드를 찾을 수 있습니다. 이때 Windows 개발자 머신의 도구를 사용하여 이 클러스터에 연결하고 Linux Service Fabric 클러스터용 애플리케이션을 배포할 수 있습니다. 

    > [!NOTE]
    > Eclipse 플러그 인은 현재 Windows에서 지원되지 않습니다. 

6. 작업을 모두 마쳤으면 다음 명령을 사용하여 컨테이너를 중지하고 정리합니다.

    ```powershell 
    docker rm -f sftestcluster
    ```

### <a name="known-limitations"></a>알려진 제한 사항 
 
 다음은 Mac용 컨테이너에서 실행하는 로컬 클러스터의 알려진 제한 사항입니다. 
 
 * DNS 서비스는 실행되지 않으며 현재 컨테이너 내에서 지원되지 않습니다. [문제 #132](https://github.com/Microsoft/service-fabric/issues/132)
 * 컨테이너 기반 앱을 실행하려면 Linux 호스트에서 SF를 실행해야 합니다. 중첩된 컨테이너 앱은 현재 지원되지 않습니다.

## <a name="next-steps"></a>다음 단계
* [Yeoman을 사용하여 Linux에서 첫 번째 Service Fabric Java 애플리케이션 만들기 및 배포](service-fabric-create-your-first-linux-application-with-java.md)
* [Eclipse](./service-fabric-get-started-eclipse.md) 시작
* 다른 [Java 샘플](https://github.com/Azure-Samples/service-fabric-java-getting-started) 확인
* [Service Fabric 지원 옵션](service-fabric-support.md) 알아보기


<!-- Image references -->

[publishdialog]: ./media/service-fabric-manage-multiple-environment-app-configuration/publish-dialog-choose-app-config.png
[app-parameters-solution-explorer]:./media/service-fabric-manage-multiple-environment-app-configuration/app-parameters-in-solution-explorer.png
