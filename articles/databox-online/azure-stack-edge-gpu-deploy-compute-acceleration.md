---
title: Kubernetes 배포에 대 한 Azure Stack Edge 장치에서 계산 가속 GPU 또는 VPU 사용 | Microsoft Docs
description: Azure Stack Edge Pro GPU에서 계산 가속 GPU 또는 VPU를 사용 하는 방법에 대해 설명 합니다. Kubernetes 배포에는 edge Pro R Azure Stack 또는 edge 미니 Ri를 Azure Stack 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 3eb648af60a7be62d08f6b172347778d2358643c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102440125"
---
# <a name="use-compute-acceleration-on-azure-stack-edge-pro-gpu-for-kubernetes-deployment"></a>Kubernetes 배포에 대 한 Azure Stack Edge Pro GPU에서 계산 가속 사용

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

이 문서에서는 Kubernetes 배포를 사용할 때 Azure Stack Edge 장치에서 계산 가속화를 사용 하는 방법을 설명 합니다. 이 문서는 Azure Stack Edge Pro GPU, Azure Stack Edge Pro R 및 Azure Stack Edge 미니 R 장치에 적용 됩니다.


## <a name="about-compute-acceleration"></a>계산 가속 정보 

CPU (중앙 처리 장치)는 컴퓨터에서 실행 되는 대부분의 프로세스에 대 한 기본 범용 계산입니다. 일반적으로 특정 컴퓨터 하드웨어를 CPU의 소프트웨어에서 실행 하는 것 보다 효율적으로 수행 하는 데 사용 됩니다. 예를 들어 GPU (그래픽 처리 장치)를 사용 하 여 픽셀 데이터의 처리 속도를 높일 수 있습니다.  

계산 가속화는 GPU (그래픽 처리 장치), VPU (FPGA) 또는 현장에서 프로그래밍 가능한 게이트 배열 ()이 하드웨어 가속에 사용 되는에 지 장치 Azure Stack에 특별히 사용 되는 용어입니다. Azure Stack Edge 장치에 배포 되는 대부분의 작업에는 중요 한 타이밍, 다중 카메라 스트림 및/또는 높은 프레임 속도가 포함 되며,이 중 일부는 특정 하드웨어 가속이 필요 합니다.

이 문서에서는 다음 장치에 대해 GPU 또는 VPU를 사용 하는 경우에만 계산 가속을 설명 합니다.

- **Azure Stack Edge PRO gpu** -이러한 장치는 1 또는 2 Nvidia T4 텐서 Core GPU를 사용할 수 있습니다. 자세한 내용은 [NVIDIA T4](https://www.nvidia.com/en-us/data-center/tesla-t4/)를 참조 하세요.
- **Azure Stack Edge Pro r** -이러한 장치에는 1 개의 Nvidia T4 텐서 Core GPU가 있습니다. 자세한 내용은 [NVIDIA T4](https://www.nvidia.com/en-us/data-center/tesla-t4/)를 참조 하세요.
- **Azure Stack Edge 미니 R** -이러한 장치에는 하나의 Intel Movidius 방대한 X VPU이 있습니다. 자세한 내용은 [Intel Movidius 방대한 X VPU](https://www.movidius.com/MyriadX)를 참조 하세요.


## <a name="use-gpu-for-kubernetes-deployment"></a>Kubernetes 배포에 GPU 사용

다음 예제는 `yaml` gpu를 사용 하는 Azure Stack Edge PRO GPU 또는 Azure Stack Edge Pro r 장치에 사용할 수 있습니다.

<!--In a production scenario, Pods are not used directly and these are wrapped around higher level constructs like Deployment, ReplicaSet which maintain the desired state in case of pod restarts, failures.-->

```yml
apiVersion: v1
kind: Pod
metadata:
  name: gpu-pod
spec:
  containers:
    - name: cuda-container
      image: nvidia/cuda:9.0-devel
      resources:
        limits:
          nvidia.com/gpu: 1 # requesting 1 GPU
    - name: digits-container
      image: nvidia/digits:6.0
      resources:
        limits:
          nvidia.com/gpu: 1 # requesting 1 GPU
```


## <a name="use-vpu-for-kubernetes-deployment"></a>Kubernetes 배포에 VPU 사용

다음 예제는 `yaml` VPU가 있는 Edge 미니 R 장치에 Azure Stack 사용할 수 있습니다.

```yml
apiVersion: batch/v1
kind: Job
metadata:
 name: intelvpu-demo-job
 labels:
   jobgroup: intelvpu-demo
spec:
 template:
   metadata:
     labels:
       jobgroup: intelvpu-demo
   spec:
     restartPolicy: Never
     containers:
       -
         name: intelvpu-demo-job-1
         image: ubuntu-demo-openvino:devel
         imagePullPolicy: IfNotPresent
         command: [ "/do_classification.sh" ]
         resources:
           limits:
             vpu.intel.com/hddl: 1
```


## <a name="next-steps"></a>다음 단계

[Kubectl를 사용 하 여 Azure Stack Edge PRO GPU 장치에서 PersistentVolume로 Kubernetes 상태 저장 응용 프로그램을 실행](azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes.md)하는 방법에 대해 알아봅니다.
