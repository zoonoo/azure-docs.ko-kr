---
title: AKS에 마이크로 포커스 엔터프라이즈 서버 5.0 배포 Microsoft Docs
description: Azure Vm (가상 머신)에서 마이크로 포커스 개발 및 테스트 환경을 사용 하 여 IBM z/OS 메인프레임 워크 로드를 Rehost.
services: virtual-machines-linux
documentationcenter: ''
author: maggsl
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 06/29/2020
tags: ''
keywords: ''
ms.service: multiple
ms.openlocfilehash: 900e76fff3e5ccf88fa1e25ebea97f26e406a358
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85610373"
---
# <a name="deploy-micro-focus-enterprise-server-50-to-aks"></a>AKS에 마이크로 포커스 엔터프라이즈 서버 5.0 배포

다른 [문서](https://docs.microsoft.com/azure/virtual-machines/workloads/mainframe-rehosting/microfocus/run-enterprise-server-container)에서는 Docker 컨테이너에서 마이크로 포커스 엔터프라이즈 서버 5.0를 실행 하는 단계에 대해 간략하게 설명 했습니다. 그에 대 한 후속 작업으로,이를 한 단계 더 수행 하 고 Azure Kubernetes Service (AKS)에 만든 Docker 이미지를 배포 하는 방법을 보여 드리겠습니다.

Azure Kubernetes Service는 Kubernetes를 기반으로 하는 관리 되는 오케스트레이션 서비스입니다. 컨테이너 호스트의 클러스터에서 Docker 컨테이너 (및 기타 컨테이너 기반 응용 프로그램)를 배포, 확장 및 관리할 수 있습니다.

3 단계 프로세스입니다. 그러려면 다음 작업을 수행해야 합니다.

1.  Docker 이미지를 저장할 Azure Container Registry를 만듭니다.

2.  Docker 이미지를 실행 하는 Azure Kubernetes 클러스터를 만듭니다.

3.  애플리케이션을 실행합니다.

이렇게 하면 Azure에서 메인프레임 현대화 워크 로드를 확장 (및 규모 축소) 하 여 클라우드 플랫폼의 진정한 이점을 누릴 수 있습니다.

미리? 시작 하겠습니다.

## <a name="create-the-azure-container-registry"></a>Azure Container Registry 만들기

Azure Portal에서 왼쪽 위 모서리에 있는 **리소스 만들기** 를 선택 합니다. Marketplace 대시보드에서 **컨테이너를** 선택 하 고 **Container Registry**합니다. 그러면 **레지스트리 이름**, **Azure 구독**, **리소스 그룹**및 **위치**를 입력 해야 하는 **컨테이너 레지스트리 만들기** 창으로 이동 합니다. **레지스트리 이름을** 확인 해야 하므로 고유 해야 합니다. 이전 블로그 게시물 및 동일한 해당 **위치**에서 사용한 **리소스 그룹** 을 선택 합니다. **관리 사용자** 에 대해 **사용** 을 선택 하 고 **SKU**에 대해 **기본** 을 선택 합니다. 모든 항목을 채운 후 **만들기**를 선택 합니다.

![컨테이너 레지스트리 인터페이스 만들기](media/deploy-image-1.png)

레지스트리를 배포한 후 **리소스로 이동**을 선택 합니다. 그러면 Azure Container Registry에 대 한 기본 블레이드로 이동 합니다. 여기에서 유용한 기능은 **빠른 시작** 메뉴 옵션입니다. 이 항목을 선택 하면 레지스트리에서/로 이미지를 끌어오거나 가져오기 위해 수행 해야 하는 작업에 대 한 지침이 표시 됩니다. 다음과 같이 하겠습니다.

1.  **Docker 설치** – 이미 수행 되었기 때문에 걱정 하지 않아도 됩니다.

2.  **"Hello – 세계" 기본 이미지** 를 다시 실행 합니다. 반드시 필요한 것은 아니지만 자유롭게 사용해 볼 수 있습니다.

3.  **컨테이너 레지스트리에 로그인** – VM (가상 컴퓨터)에서이 작업을 수행 해야 합니다. 명령을 클립보드 또는 메모장에 복사 합니다.

    만든 레지스트리의 경우 명령은 **docker login acrmf50.azurecr.io** 입니다.

4.  **레지스트리에 푸시** – VM에 로그인 한 후에도 마이크로 포커스 이미지에 대해이 작업을 수행 해야 합니다.

5.  **레지스트리에서 끌어오기** –이 연습을 수행 하는 데는 관련이 없지만 다른 Docker 이미지를 실행 해야 하는지 여부를 파악 하는 것이 좋습니다.

포털을 나가기 전에 로그인 할 수 있도록 레지스트리에 대 한 자격 증명을 가져와야 합니다. **빠른 시작** 블레이드를 종료 하 고 레지스트리 메뉴에서 **액세스 키** 를 선택 합니다. **사용자 이름** 및 **암호** 중 하나 (두 개)를 클립보드 또는 메모장에 복사 합니다. 나중에 로그인 하는 데 필요 합니다.

이제 수행 해야 하는 작업을 알고 있으므로 VM에 로그인 합니다.

## <a name="rdp-to-the-virtual-machine-you-used-to-create-the-docker-image"></a>Docker 이미지를 만드는 데 사용한 가상 컴퓨터에 대 한 RDP

Windows 2016 서버에서 Docker 이미지를 이미 만들었기 때문에 해당 VM에 로그인 해야 합니다. 이 VM에서 방금 만든 Azure Container Registry에 이미지를 푸시할 수 있습니다. Azure Portal에서 VM으로 이동한 다음 **개요** 를 선택 하 고 **연결**을 선택 합니다. 그러면 RDP (원격 데스크톱 프로토콜)를 통해 VM에 연결 됩니다. VM을 만들 때의 자격 증명을 사용 해야 합니다.

## <a name="log-in-and-push-the-image-to-the-registry"></a>로그인 하 여 레지스트리에 이미지 푸시

로그인 한 후 명령 프롬프트를 열고 다음 Docker 명령을 시작 합니다.

-   **docker 이미지** – VM에 현재 설치 되어 있는 모든 이미지 목록을 표시 합니다. 이 작업을 수행 하 고 있는 것 이므로 **마이크로 포커스/es-acctdemo** 를 기록해 둡니다.

-   **docker login acrmf50.azurecr.io** – 올바른 형식은 *docker 로그인 \<registry name\> *입니다. 레지스트리를 만들 때 사용한 이름을 대체 합니다.

    -   Azure Portal에서 복사한 **사용자 이름** 및 **암호** 가 필요 합니다. 다음 이미지와 같은 정보가 표시됩니다.

    ![관리자 명령 프롬프트의 스크린샷](media/deploy-image-2.png)

-   **docker 태그 마이크로 포커스/es-acctdemo acrmf50.azurecr.io/es-acctdemo** – 적절 한 이미지를 리포지토리의 이름으로 태그를 합니다. **참고**: 이름이 \<microfocus/es-acctdemo\> 작동 하지 않는 경우 전체 이미지 ID를 사용해 보세요. 명령을 실행 한 후 **docker images – trunc**를 입력 합니다. 다음 이미지와 유사한 내용이 표시 되어야 합니다. 이미지에 태그가 올바르게 지정 되어 있는지 확인 합니다.

    ![관리자 명령 프롬프트 화면을 선택 합니다.](media/deploy-image-3.png)

-   **docker push acrmf50.azurecr.io/es-acctdemo** – 저장소에 대 한 실제 푸시를 시작 합니다. 크기는 15gb 이므로 실행 하는 데 몇 분 정도 걸립니다. 모든 항목을 오른쪽으로 이동 하면 다음 이미지와 유사한 내용이 표시 됩니다.

    ![관리자 명령 프롬프트 화면](media/deploy-image-4.png)

이제 **리포지토리로**Azure Portal로 돌아갑니다. **리포지토리의**메뉴에서 **리포지토리**를 선택 하면 **es-acctdemo** 가 표시 되어야 합니다. 이제 AKS 클러스터를 만듭니다.

## <a name="create-the-azure-kubernetes-aks-cluster"></a>Azure Kubernetes (AKS) 클러스터 만들기

Azure Portal에서 **리소스 만들기** 를 선택 하 고 **Marketplace** 메뉴에서 **컨테이너/Kubernetes 서비스** 를 선택 합니다. 그런 다음 **Create Kubernetes cluster** 블레이드를 입력 해야 합니다. 사용 중인 것과 동일한 지역 및 리소스 그룹에 클러스터를 유지 해야 합니다. **노드 수** 를 제외한 나머지 기본값을 그대로 사용할 수 있습니다. 단, 1만 필요 합니다. 완료되면 **검토 + 만들기**를 선택합니다.

![Kubernetes 클러스터 만들기 화면](media/deploy-image-5.png)

완료 되 면 배포에서 **Kubernetes 서비스** 아티팩트가 블레이드에서 선택한 **리소스 그룹** 에 배치 됩니다. 그러나 실제 클러스터에는 배포 중에 생성 되는 자체 리소스 그룹이 있습니다. 왼쪽의 메뉴에서 **리소스 그룹** 을 선택 하는 경우 명명 규칙에 따라 찾을 수 있습니다. 다음은 목록에 있는 마지막 이미지입니다.

![리소스 그룹의 스크린샷](media/deploy-image-6.png)

**이미지 실행**

이제 이미지를 가져와서 AKS에서 실행할 수 있습니다. Azure Portal에서이 작업을 수행 하는 가장 쉬운 방법은 Cloud Shell를 사용 하는 것입니다. Azure Portal의 오른쪽 위에 아이콘이 표시 됩니다. 이 연습에서는 Bash 셸을 사용 합니다.

![Cloud Shell 아이콘의 스크린샷](media/deploy-image-7.png)

셸이 로드 되 면 다음 명령을 입력 합니다.

**kubectl 실행 es-acctdemo--image acrmf50.azurecr.io/es-acctdemo--port = 9040**

그러면 **Acrmf50.azurecr.io** 리포지토리에서 이미지가 끌어오고 AKS에 로드 됩니다. 그런 다음 포트 9040이 열려 있는 이미지를 실행 합니다. Docker 이미지에 대해 열린 포트 였던 것을 기억할 수 있습니다. 엔터프라이즈 서버에 액세스 하려면 필요 합니다.

Kubernetes는 배포를 만든 메시지를 사용 하 여 응답 해야 합니다.

![배포 메시지 스크린샷](media/deploy-image-8.jpg)

컨테이너가 실제로 실행 중인지 확인 하려면 다음을 입력 합니다. **kubectl get pod**.

다음 이미지와 같이 es-acctdemo가 실행 pod로 표시 되어야 합니다.

![스크린샷 es-실행 pod로 acctdemo](media/deploy-image-9.png)

지금까지 이제 Azure Kubernetes Service에서 엔터프라이즈 서버를 실행 하 고 있습니다. 다음 문서에서는 엔터프라이즈 서버 관리 콘솔에 액세스 하는 방법 및 Kubernetes를 활용 하 여 배포를 확장 하는 방법을 보여 줍니다.
