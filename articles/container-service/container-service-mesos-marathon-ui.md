<properties
   pageTitle="웹 UI를 통해 ACS 컨테이너 관리"
   description="Marathon 웹 UI를 사용하여 컨테이너를 Azure 컨테이너 서비스 클러스터 서비스에 배포합니다."
   services="container-service"
   documentationCenter=""
   authors="neilpeterson"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker, 컨테이너, 마이크로 서비스, Mesos, Azure"/>
   
<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="nepeters"/>
   
# 웹 UI 통해 컨테이너 관리
   
Mesos는 기본 하드웨어를 추상화하는 동안 클러스터형 워크로드를 배포 및 확장하기 위한 환경을 제공 합니다. Mesos의 상단에서 프레임워크가 계산 워크로드의 예약 및 실행을 관리합니다. 프레임워크는 수많은 워크로드에 사용할 수 있지만 이 문서에서는 Marathon으로 컨테이너 배포를 만들고 확장하는 방법에 대해 자세히 설명합니다. 이러한 예제를 통해 작업하기 전에 ACS에 Mesos 클러스터가 구성되어야 하고 이 클러스터에 대한 원격 연결이 있어야 합니다. 이러한 항목에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Azure 컨테이너 서비스 클러스터 배포](./container-service-deployment.md) 
- [ACS 클러스터에 연결](./container-service-connect.md)

## Mesos UI 탐색

SSH 터널이 설정된 상태에서 http://localhost/Mesos로 이동합니다. 그러면 Mesos 웹 UI가 로드됩니다. 페이지에서 활성화된 에이전트, 태스크 상태 및 리소스 가용성과 같은 Mesos 클러스터에 대한 정보를 수집할 수 있습니다.

![배포 만들기](media/ui1.png)

## Marathon UI 탐색

Marathon UI를 보려면 http://localhost/Marathon으로 이동합니다. 이 화면에서 ACS Mesos의 새 컨테이너 또는 다른 응용 프로그램을 시작할 수 있으며 컨테이너 및 응용 프로그램 실행에 대한 정보를 볼 수 있습니다.

![배포 만들기](media/ui2.png)

## Docker 컨테이너 배포

Mesos 클러스터에서 새 컨테이너를 시작하는 데 Marathon을 사용하려면 `Create Application` 단추를 클릭합니다. 새 응용 프로그램 양식은 응용 프로그램 또는 컨테이너 매개 변수를 정의하는 데 사용됩니다. 이 예에서는 간단한 Nginx 컨테이너가 배포됩니다. 다음 정보를 입력합니다. 완료되면 '만들기'를 클릭합니다.
 
필드 | 값
----------------|-----------
ID | nginx
이미지 | nginx
네트워크 | Bridged
컨테이너 포트 | 80
호스트 포트 | 80
프로토콜 | TCP

![배포 만들기](media/ui3.png)

다시 Marathon 주 페이지에서 컨테이너에 대한 배포 상태를 볼 수 있습니다.

![배포 만들기](media/ui4.png)

Mesos 앱(http://localhost/Mesos)으로 다시 전환하면 태스크가 Mesos 클러스터에서 실행 중임이 표시됩니다(이 경우 Docker 컨테이너). 또한 태스크가 실행되는 클러스터 노드도 볼 수 있습니다.

![배포 만들기](media/ui5.png)

## Docker 컨테이너 확장

컨테이너의 인스턴스 수를 확장하는 데 Marathon 웹 UI도 사용할 수 있습니다. 이렇게 하려면 Marathon 페이지로 이동하고 확장할 컨테이너를 선택하고 `scale` 단추를 클릭합니다. 응용 프로그램 확장 창에서 원하는 컨테이너 인스턴스 수를 입력하고 `Scale Application`을 선택합니다.

![배포 만들기](media/ui6.png)

확장 작업이 완료되면 Mesos 에이전트 간에 분산되어 있는 동일한 태스크의 여러 인스턴스가 표시됩니다.

![배포 만들기](media/ui8.png)

<!---HONumber=AcomDC_0218_2016-->