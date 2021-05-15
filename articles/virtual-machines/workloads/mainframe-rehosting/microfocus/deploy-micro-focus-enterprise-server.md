---
title: AKS에 마이크로 포커스 Enterprise 서버 5.0 배포 | Microsoft Docs
description: Azure 가상 머신(VM)에서 마이크로 포커스 개발 및 테스트 환경을 사용하여 IBM z/OS 메인프레임 워크로드를 다시 호스트.
services: virtual-machines
documentationcenter: ''
author: maggsl
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 06/29/2020
tags: ''
keywords: ''
ms.service: virtual-machines
ms.subservice: mainframe-rehosting
ms.openlocfilehash: 58ba530e434a9dc141ba8cb98120f6325eb06f7e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104950476"
---
# <a name="deploy-micro-focus-enterprise-server-50-to-aks"></a>AKS에 마이크로 포커스 Enterprise 서버 5.0 배포

다른 [문서](./run-enterprise-server-container.md)에서는 Docker 컨테이너에서 마이크로 포커스 Enterprise 서버 5.0를 실행하는 단계에 대해 간략하게 설명했습니다. 그에 대한 후속 작업으로,이를 한 단계 더 수행하고 Azure Kubernetes Service (AKS)에 만든 Docker 이미지를 배포하는 방법을 보여드리겠습니다.

Azure Kubernetes Service는 Kubernetes에서 관리 오케스트레이션 서비스를 기반으로 합니다. 컨테이너 호스트의 클러스터에서 Docker 컨테이너 (및 기타 컨테이너 기반 애플리케이션)를 배포, 확장 및 관리할 수 있습니다.

이는 세 단계로 이루어집니다. 그러려면 다음 작업을 수행해야 합니다.

1.  Azure Container Registry를 만들어 Docker 이미지를 저장합니다.

2.  Docker 이미지를 실행하는 Azure Kubernetes 클러스터를 만듭니다.

3.  애플리케이션을 실행합니다.

이렇게 하면 Azure에서 메인프레임 현대화 워크로드를 규모 확장 (및 축소)하여 클라우드 플랫폼의 진정한 이점을 누릴 수 있습니다.

준비되었나요? 이제 시작하겠습니다.

## <a name="create-the-azure-container-registry"></a>Azure Container Registry를 만듭니다.

Azure Portal에서 왼쪽 위 모서리에서 **리소스 만들기** 를 선택합니다. Marketplace 대시보드에서 **컨테이너**, 그러고 **Container Registry** 를 선택합니다. 그러면 **레지스트리 이름**, **Azure 구독**, **리소스 그룹** 과 **위치** 를 입력해야 하는 **컨테이너 레지스트리 만들기** 창으로 이동합니다. **레지스트리 이름** 을 확인해야 하므로, 이는 고유한 것이여야 합니다. 이전 블로그 게시물 및 동일한 해당 **위치** 에서 사용한 **리소스 그룹** 을 선택합니다. **관리 사용자** 에 대해 **사용** 을 선택하고 **SKU** 에 대해 **기본** 을 선택합니다. 일단 모든 항목을 채운 후 **만들기** 를 선택합니다.

![컨테이너 레지스트리 인터페이스 만들기](media/deploy-image-1.png)

레지스트리를 배포한 후 **리소스로 이동** 을 선택합니다. 그러면 Azure Container Registry에 대한 기본 블레이드로 이동합니다. 여기에서 유용한 기능은 **빠른 시작** 메뉴 옵션입니다. 이 항목을 선택하면 레지스트리에서/로 이미지를 끌어오거나 가져오기 위해 수행해야 하는 작업에 대한 명령이 표시됩니다. 다음과 같이 하겠습니다.

1.  **Docker 설치** – 이미 수행되었기 때문에 걱정하지 않아도 됩니다.

2.  **"Hello – 세계" 기본 이미지 실행** - 다시 실행하고, 반드시 필요한 것은 아니지만 자유롭게 사용해 볼 수 있습니다.

3.  **컨테이너 레지스트리에 로그인** – 가상 컴퓨터(VM )에서 이 작업을 수행해야 합니다. 명령을 클립보드 또는 메모장에 복사합니다.

    만든 레지스트리의 경우 명령은 **docker login acrmf50.azurecr.io** 입니다.

4.  **레지스트리에 푸시** – 일단 VM에 로그인한 후에도 마이크로 포커스 이미지에 대해 이 작업을 수행해야 합니다.

5.  **레지스트리에서 끌어오기** –이 연습을 수행하는 데는 관련이 없지만 다른 Docker 이미지를 실행해야 하는지 여부를 파악하는 것이 좋습니다.

포털을 나가기 전에 로그인할 수 있도록 레지스트리에 대한 자격 증명을 가져와야 합니다. **빠른 시작** 블레이드를 종료하고 레지스트리 메뉴에서 **액세스 키** 를 선택합니다. **사용자 이름** 및 **암호** 중 하나 (두 개 있습니다)를 클립보드 또는 메모장에 복사합니다. 로그인을 위해 나중에 필요합니다.

이제 수행해야 하는 작업을 알고 있으므로 VM에 로그인합니다.

## <a name="rdp-to-the-virtual-machine-you-used-to-create-the-docker-image"></a>Docker 이미지를 만드는 데 사용한 가상 컴퓨터에 대한 RDP

Windows 2016 서버에서 Docker 이미지를 이미 만들었으므로 해당 VM에 로그인해야 합니다. 이 VM에서 방금 만든 Azure Container Registry에 이미지를 푸시할 수 있습니다. Azure Portal에서 VM으로 이동한 다음 **개요** 를 선택하고 **연결** 을 선택합니다. 그러면 원격 데스크톱 프로토콜(RDP)을 통해 VM에 연결됩니다. VM을 만들 때의 자격 증명을 사용해야 합니다.

## <a name="log-in-and-push-the-image-to-the-registry"></a>로그인하고 이미지를 레지스트리로 푸시합니다.

일단 로그인한 후, 명령 프롬프트를 열고 다음 Docker 명령을 시작합니다.

-   **docker 이미지** – VM에 현재 설치되어 있는 모든 이미지 목록을 표시합니다. 이 작업을 하게 될 것이므로 **microfocus/es-acctdemo** 를 기록해둡니다.

-   **docker login acrmf50.azurecr.io** – 여기에 올바른 형식은 *docker 로그인 \<registry name\>* 입니다. 레지스트리를 만들 때 사용한 이름을 대체합니다.

    -   Azure Portal에서 복사한 **사용자 이름** 및 **암호** 가 필요합니다. 다음 이미지와 같은 정보가 표시됩니다.

    ![관리자 명령 프롬프트의 스크린샷](media/deploy-image-2.png)

-   **docker 태그 마이크로 포커스/es-acctdemo acrmf50.azurecr.io/es-acctdemo** – 적절한 이미지를 리포지토리의 이름으로 태그를 합니다. **참고**: 이름 \<microfocus/es-acctdemo\>이 작동하지 않는 경우 전체 이미지 ID를 사용해보세요. 명령을 실행한 후 **docker images – trunc** 를 입력합니다. 다음 이미지와 유사한 출력이 표시됩니다. 이미지에 태그가 올바르게 지정되어 있는지 확인합니다.

    ![관리자 명령 프롬프트 화면을 선택합니다.](media/deploy-image-3.png)

-   **docker push acrmf50.azurecr.io/es-acctdemo** – 리포지토리로 실제 푸시 작업을 시작합니다. 크기는 15GB이므로 실행하는 데 몇 분 정도 걸립니다. 모든 항목을 오른쪽으로 이동하면 다음 이미지와 유사한 내용이 표시됩니다.

    ![관리자: 명령 프롬프트 화면](media/deploy-image-4.png)

이제 Azure Portal로, 특히 **리포지토리** 로 돌아갑니다. **리포지토리** 의 메뉴에서 **리포지토리** 를 선택하면 **es-acctdemo** 가 표시되어야 합니다. 이제 AKS 클러스터를 만듭니다.

## <a name="create-the-azure-kubernetes-aks-cluster"></a>Azure Kubernetes (AKS) 클러스터 만들기

Azure Portal에서 **리소스 만들기** 를 선택하고 **Marketplace** 메뉴에서 **컨테이너 / Kubernetes 서비스** 를 선택합니다. 그런 다음 **Create Kubernetes cluster** 블레이드를 입력해야 합니다. 사용 중인 것과 동일한 지역 및 리소스 그룹에 클러스터를 유지해야 합니다. **노드 수** 를 제외한 나머지 기본값을 그대로 사용할 수 있으며, 오직 1만 필요합니다. 완료되면 **검토 + 만들기** 를 선택합니다.

![Kubernetes 클러스터 화면 만들기](media/deploy-image-5.png)

완료되면 배포에서 **Kubernetes 서비스** 아티팩트가 블레이드에서 선택한 **리소스 그룹** 에 배치됩니다. 그러나 실제 클러스터에는 배포 중에 생성되는 자체 리소스 그룹이 있습니다. 왼쪽의 메뉴에서 **리소스 그룹** 을 선택하는 경우 명명 규칙에 따라 찾을 수 있습니다. 다음은 목록에 있는 마지막 이미지입니다.

![리소스 그룹의 스크린샷](media/deploy-image-6.png)

**이미지 실행**

이제 이미지를 가져와서 AKS에서 실행할 수 있습니다. Azure Portal에서이 작업을 수행하는 가장 쉬운 방법은 Cloud Shell를 사용하는 것입니다. Azure Portal의 오른쪽 위에 아이콘이 표시됩니다. 참고로 이 연습에서는 Bash 셸을 사용합니다.

![Cloud Shell 아이콘의 스크린샷](media/deploy-image-7.png)

일단 셸이 로드되면 다음 명령을 입력합니다.

**kubectl이 es-acctdemo를 실행합니다--image acrmf50.azurecr.io/es-acctdemo --port=9040**

그러면 **Acrmf50.azurecr.io** 리포지토리에서 이미지를 끌어오고 AKS에 로드합니다. 그런 다음 포트 9040을 열어두고 이미지를 실행합니다. Docker 이미지에 대해 열린 포트였던 것을 기억할 수 있습니다. Enterprise 서버에 액세스 하려면 필요합니다.

Kubernetes는 배포를 만든 메시지를 사용하여 응답해야 합니다.

![배포 메세지의 스크린샷](media/deploy-image-8.jpg)

컨테이너가 실제로 실행 중인지 확인하려면 다음을 입력합니다: **kubectl get pods**.

다음 이미지와 같이 es-acctdemo가 실행 pod로 표시되어야 합니다.

![실행 pod인 es-acctdemo 스크린샷](media/deploy-image-9.png)

축하합니다! 이제 Azure Kubernetes Service에서 Enterprise 서버를 실행하고 있습니다. 다음 문서에서는 Enterprise 서버 관리 콘솔에 액세스하는 방법 및 Kubernetes를 활용하여 배포를 확장하는 방법을 보여줄 것입니다.
