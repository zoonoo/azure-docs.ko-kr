---
title: Kubernetes 대시보드를 통해 Azure Stack Edge Pro 장치 모니터링 | Microsoft Docs
description: Kubernetes 대시보드를 액세스 및 사용 하 여 Azure Stack Edge Pro 장치를 모니터링 하는 방법을 설명 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/29/2020
ms.author: alkohli
ms.openlocfilehash: 1a4f22e7ae3cc60d0a16b24a1f0e5f93d3a86d8c
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90899206"
---
# <a name="use-kubernetes-dashboard-to-monitor-your-azure-stack-edge-pro-gpu-device"></a>Kubernetes 대시보드를 사용 하 여 Azure Stack Edge Pro GPU 장치 모니터링

이 문서에서는 Kubernetes 대시보드를 액세스 및 사용 하 여 Azure Stack Edge Pro GPU 장치를 모니터링 하는 방법을 설명 합니다. 장치를 모니터링 하려면 Azure Portal에서 차트를 사용 하거나, Kubernetes 대시보드를 보거나, `kubectl` 장치의 PowerShell 인터페이스를 통해 명령을 실행할 수 있습니다. 

이 문서에서는 Kubernetes 대시보드에서 수행할 수 있는 모니터링 작업만 중점적으로 다룹니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
>
> * 장치에서 Kubernetes 대시보드 액세스
> * 장치에 배포 된 모듈 보기
> * 장치에 배포 된 응용 프로그램의 IP 주소를 가져옵니다.
> * 장치에 배포 된 모듈의 컨테이너 로그 보기


## <a name="about-kubernetes-dashboard"></a>Kubernetes 대시보드 정보

Kubernetes 대시보드는 컨테이너 화 된 응용 프로그램 문제를 해결 하는 데 사용할 수 있는 웹 기반 사용자 인터페이스입니다. Kubernetes 대시보드는 Kubernetes 명령줄에 대 한 UI 기반 대안입니다 `kubectl` . 자세한 내용은 [Kubernetes Dashboard](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/)를 참조 하세요. 

Azure Stack Edge Pro 장치에서 Kubernetes 대시보드를 *읽기 전용* 모드로 사용 하 여 Azure Stack Edge pro 장치에서 실행 되는 응용 프로그램의 개요를 가져오고, Kubernetes 클러스터 리소스의 상태를 보고, 장치에서 발생 한 모든 오류를 확인할 수 있습니다.

## <a name="access-dashboard"></a>액세스 대시보드

Kubernetes 대시보드는 *읽기 전용* 이며 포트 31000의 Kubernetes 마스터 노드에서 실행 됩니다. 대시보드에 액세스 하려면 다음 단계를 수행 합니다. 

1. 장치의 로컬 UI에서 **장치** 로 이동한 후 **장치 끝점**으로 이동 합니다. 
1. **구성 다운로드** 를 선택 하 여 `kubeconfig` 대시보드에 액세스할 수 있는를 다운로드 합니다. `config.json`로컬 시스템에 파일을 저장 합니다.
1. Kubernetes 대시보드 URL을 선택 하 여 브라우저에서 대시보드를 엽니다.

    ![로컬 UI의 장치 페이지에서 Kubernetes 대시보드 URL](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-dashboard-url-local-ui-1.png)

1. **Kubernetes 대시보드 로그인** 페이지에서 다음을 수행 합니다.
    
    1. **Kubeconfig**를 선택 합니다. 
        ![Kubeconfig 옵션 선택](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-dashboard-sign-in-1.png) 
    1. 줄임표 **...** 를 선택 합니다. `kubeconfig` 로컬 시스템에서 이전에 다운로드 한를 찾아서 가리킵니다. **로그인**을 선택합니다.
        ![Kubeconfig 파일로 이동 합니다.](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-dashboard-sign-in-2.png)    

6. 이제 읽기 전용 모드에서 Azure Stack Edge Pro 장치에 대 한 Kubernetes 대시보드를 볼 수 있습니다.

    ![Kubernetes 대시보드 기본 페이지](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-dashboard-main-page-1.png)

## <a name="view-module-status"></a>모듈 상태 보기

계산 모듈은 비즈니스 논리를 구현 하는 컨테이너입니다. 대시보드를 사용 하 여 Azure Stack Edge Pro 장치에서 계산 모듈이 성공적으로 배포 되었는지 확인할 수 있습니다.

모듈 상태를 보려면 대시보드에서 다음 단계를 수행 합니다.

1. 대시보드의 왼쪽 창에서 **네임 스페이스**로 이동 합니다. IoT Edge 모듈이 표시 되는 네임 스페이스 (이 경우에는 **iotedge**)를 기준으로 필터링 합니다.
1. 왼쪽 창에서 **워크 로드 > 배포**로 이동 합니다.
1. 오른쪽 창에 장치에 배포 된 모든 모듈이 표시 됩니다. 이 경우 GettingStartedWithGPU 모듈은 Azure Stack Edge Pro에 배포 되었습니다. 모듈이 배포 된 것을 볼 수 있습니다.

    ![모듈 배포 보기](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-view-module-deployment-1.png)

 
## <a name="get-ip-address-for-services-or-modules"></a>서비스 또는 모듈에 대 한 IP 주소 가져오기

대시보드를 사용 하 여 Kubernetes 클러스터 외부에 노출 하려는 서비스 또는 모듈의 IP 주소를 가져올 수 있습니다. 

**Compute 네트워크 설정** 페이지에서 장치의 로컬 웹 UI를 통해 이러한 외부 서비스에 대 한 IP 범위를 할당 합니다. IoT Edge 모듈을 배포한 후에는 특정 모듈 또는 서비스에 할당 된 IP 주소를 가져올 수 있습니다. 

IP 주소를 가져오려면 대시보드에서 다음 단계를 수행 합니다.

1. 대시보드의 왼쪽 창에서 **네임 스페이스**로 이동 합니다. 외부 서비스가 배포 되는 네임 스페이스 (이 경우에는 **iotedge**)를 기준으로 필터링 합니다.
1. 왼쪽 창에서 **검색 및 부하 분산 > 서비스로**이동 합니다.
1. 오른쪽 창에 `iotedge` Azure Stack Edge Pro 장치의 네임 스페이스에서 실행 중인 모든 서비스가 표시 됩니다.

    ![외부 서비스에 대 한 IP 가져오기](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-get-ip-external-service-1.png)

## <a name="view-container-logs"></a>컨테이너 로그 보기

컨테이너 로그를 확인 해야 하는 인스턴스가 있습니다. 대시보드를 사용 하 여 Kubernetes 클러스터에 배포한 특정 컨테이너의 로그를 가져올 수 있습니다.

컨테이너 로그를 보려면 대시보드에서 다음 단계를 수행 합니다.

1. 대시보드의 왼쪽 창에서 **네임 스페이스**로 이동 합니다. IoT Edge 모듈이 배포 되는 네임 스페이스 (이 경우에는 **iotedge**)를 기준으로 필터링 합니다.
1. 왼쪽 창에서 **워크 로드 > pod**로 이동 합니다.
1. 오른쪽 창에는 장치에서 실행 되는 모든 pod가 표시 됩니다. 로그를 보려는 모듈을 실행 하 고 있는 pod를 확인 합니다. 식별 한 pod에 대해 세로 줄임표를 선택 하 고 상황에 맞는 메뉴에서 **로그**를 선택 합니다.

    ![컨테이너 로그 보기 1](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-view-container-logs-1.png)

1. 로그는 대시보드에 기본 제공 되는 로그 뷰어에 표시 됩니다. 로그를 다운로드할 수도 있습니다.

    ![컨테이너 로그 보기 2](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-view-container-logs-1.png)
    

## <a name="view-cpu-memory-usage"></a>CPU, 메모리 사용량 보기

Azure Stack Edge Pro 장치에 대 한 Kubernetes 대시보드의 Kubernetes 리소스에서 CPU 및 메모리 사용량을 집계 하는 [메트릭 서버 추가](https://kubernetes.io/docs/tasks/debug-application-cluster/resource-metrics-pipeline/) 기능도 있습니다.
 
예를 들어 모든 네임 스페이스의 배포에서 사용 되는 CPU 및 메모리를 볼 수 있습니다. 

![모든 배포에서 CPU 및 메모리 사용량 보기](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/view-cpu-memory-all-1.png)

특정 네임 스페이스를 기준으로 필터링 할 수도 있습니다. 다음 예제에서는 Azure Arc 배포에 대 한 CPU 및 메모리 소비량을 볼 수 있습니다.  

![Azure Arc 배포의 CPU 및 메모리 사용량 보기](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/view-cpu-memory-azure-arc-1.png)

Kubernetes 메트릭 서버는 [수평 Pod Autoscaler](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/)와 유사한 자동 크기 조정 파이프라인을 제공 합니다.


## <a name="next-steps"></a>다음 단계

Kubernetes 문제를 해결 하는 방법 알아보기 <!--insert link-->.
