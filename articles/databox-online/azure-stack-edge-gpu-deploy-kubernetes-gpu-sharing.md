---
title: Azure Stack Edge Pro GPU 디바이스에서 GPU 공유를 사용하여 Kubernetes 워크로드 배포
description: Azure Stack Edge Pro GPU 디바이스에서 Kubernetes를 통해 GPU 공유 작업을 배포하는 방법을 알아봅니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/12/2021
ms.author: alkohli
ms.openlocfilehash: 04299ba4028de313f640074ca98c0b611f734981
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103565141"
---
# <a name="deploy-a-kubernetes-workload-using-gpu-sharing-on-your-azure-stack-edge-pro"></a>Azure Stack Edge Pro에서 GPU 공유를 사용하여 Kubernetes 워크로드 배포

이 문서에서는 컨테이너화된 워크로드가 Azure Stack Edge Pro GPU 디바이스에서 GPU를 공유하는 방법을 설명합니다. 이 문서에서는 두 가지 작업을 실행합니다. 하나는 GPU 컨텍스트 공유 없이, 다른 하나는 디바이스에서 MPS(Multi-Process Service)를 통해 컨텍스트 공유를 사용하는 작업입니다. 자세한 내용은 [Multi-Process Service](https://docs.nvidia.com/deploy/pdf/CUDA_Multi_Process_Service_Overview.pdf)를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

시작하기 전에 다음 사항을 확인합니다.

1. [활성화](azure-stack-edge-gpu-deploy-activate.md)되고 [컴퓨팅이 구성](azure-stack-edge-gpu-deploy-configure-compute.md)된 Azure Stack Edge Pro GPU 디바이스에 액세스했습니다. [Kubernetes API 엔드포인트](azure-stack-edge-gpu-deploy-configure-compute.md#get-kubernetes-endpoints)가 있으며 디바이스에 액세스하는 클라이언트의 `hosts` 파일에 해당 엔드포인트를 추가했습니다.

1. [지원 운영 체제](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device)를 사용하여 클라이언트 시스템에 액세스했습니다. Windows 클라이언트를 사용하는 경우 시스템은 PowerShell 5.0 이상을 실행하여 디바이스에 액세스해야 합니다.

1. 네임스페이스와 사용자를 만들었습니다. 또한 사용자에게 해당 네임스페이스에 대한 액세스 권한을 부여했습니다. 해당 네임스페이스의 kubeconfig 파일을 디바이스에 액세스하는 데 사용할 클라이언트 시스템에 설치했습니다. 자세한 지침은 [Azure Stack Edge PRO GPU 디바이스에서 kubectl을 통한 Kubernetes 클러스터 연결 및 관리](azure-stack-edge-gpu-create-kubernetes-cluster.md#configure-cluster-access-via-kubernetes-rbac)를 참조하세요. 

1. 로컬 시스템에 다음 배포 `yaml`을 저장합니다. 이 파일을 사용하여 Kubernetes 배포를 실행합니다. 이 배포는 Nvidia에서 공개적으로 사용할 수 있는 [Simple CUDA 컨테이너](https://docs.nvidia.com/cuda/wsl-user-guide/index.html#running-simple-containers)를 기반으로 합니다. 

    ```yml
    apiVersion: batch/v1
    kind: Job
    metadata:
      name: cuda-sample1
    spec:
      template:
        spec:
          hostPID: true
          hostIPC: true
          containers:
            - name: cuda-sample-container1
              image: nvidia/samples:nbody
              command: ["/tmp/nbody"]
              args: ["-benchmark", "-i=1000"]
              env:
              - name: NVIDIA_VISIBLE_DEVICES
                value: "0"
          restartPolicy: "Never"
      backoffLimit: 1
    ---
    
    apiVersion: batch/v1
    kind: Job
    metadata:
      name: cuda-sample2
    spec:
      template:
        metadata:
        spec:
          hostPID: true
          hostIPC: true
          containers:
            - name: cuda-sample-container2
              image: nvidia/samples:nbody
              command: ["/tmp/nbody"]
              args: ["-benchmark", "-i=1000"]
              env:
              - name: NVIDIA_VISIBLE_DEVICES
                value: "0"
          restartPolicy: "Never"
      backoffLimit: 1
    ```

## <a name="verify-gpu-driver-cuda-version"></a>GPU 드라이버, CUDA 버전 확인

첫 번째 단계는 필수 GPU 드라이버 및 CUDA 버전이 디바이스에서 실행되는지 확인하는 것입니다.

1. [디바이스의 PowerShell 인터페이스에 연결합니다](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface).

1. 다음 명령 실행:

    ```powershell
    Get-HcsGpuNvidiaSmi
    ```

1. Nvidia smi 출력에서 디바이스의 GPU 버전 및 CUDA 버전을 기록합니다. Azure Stack Edge 2102 소프트웨어를 실행하는 경우 이 버전은 다음의 드라이버 버전에 해당합니다.

    - GPU 드라이버 버전: 460.32.03
    - CUDA 버전: 11.2
    
    출력의 예제는 다음과 같습니다.

    ```powershell
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    K8S-1HXQG13CL-1HXQG13:
    
    Wed Mar  3 12:24:27 2021
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 460.32.03    Driver Version: 460.32.03    CUDA Version: 11.2     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            On   | 00002C74:00:00.0 Off |                    0 |
    | N/A   34C    P8     9W /  70W |      0MiB / 15109MiB |      0%      Default |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |  No running processes found                                                 |
    +-----------------------------------------------------------------------------+
    [10.100.10.10]: PS> 
    ```

1. 이 세션을 사용하여 문서 전체에서 Nvidia smi 출력을 볼 수 있도록 이 세션을 열어 두세요.



## <a name="job-without-context-sharing"></a>컨텍스트 공유가 없는 작업

첫 번째 작업을 실행하여 네임스페이스`mynamesp1`의 디바이스에 애플리케이션을 배포합니다. 또한 이 애플리케이션 배포에서는 GPU 컨텍스트 공유는 기본적으로 사용되지 않습니다. 

1. 네임스페이스에서 실행 중인 모든 Pod를 나열합니다. 다음 명령 실행: 

    ```powershell
    kubectl get pods -n <Name of the namespace>
    ```

    출력의 예제는 다음과 같습니다.

    ```powershell
    PS C:\WINDOWS\system32> kubectl get pods -n mynamesp1
    No resources found.
    ```
1. 이전에 제공된 deployment.yaml을 사용하여 디바이스에서 배포 작업을 시작합니다. 다음 명령 실행: 

    ```powershell
    kubectl apply -f <Path to the deployment .yaml> -n <Name of the namespace> 
    ```  

    이 작업은 두 개의 컨테이너를 만들고 두 컨테이너에서 n-body 시뮬레이션을 실행합니다. 시뮬레이션 반복 횟수는 `.yaml`에 지정됩니다. 자세한 내용은 [n-body 시뮬레이션](https://physics.princeton.edu//~fpretori/Nbody/intro.htm)을 참조하세요.
    
    출력의 예제는 다음과 같습니다.

    ```powershell
    PS C:\WINDOWS\system32> kubectl apply -f -n mynamesp1 C:\gpu-sharing\k8-gpusharing.yaml
    job.batch/cuda-sample1 created
    job.batch/cuda-sample2 created
    PS C:\WINDOWS\system32>
    ```

1. 배포에서 시작된 Pod를 나열하려면 다음 명령을 실행합니다.

    ```powershell
    kubectl get pods -n <Name of the namespace>
    ```   

    출력의 예제는 다음과 같습니다.

    ```powershell
    PS C:\WINDOWS\system32> kubectl get pods -n mynamesp1
    NAME                 READY   STATUS    RESTARTS   AGE
    cuda-sample1-27srm   1/1     Running   0          28s
    cuda-sample2-db9vx   1/1     Running   0          27s
    PS C:\WINDOWS\system32>
    ```

    디바이스에서 실행되는 두 개의 Pod에는 `cuda-sample1-cf979886d-xcwsq`와 `cuda-sample2-68b4899948-vcv68`이 있습니다.

1. Pod의 세부 정보를 가져옵니다. 다음 명령 실행:

    ```powershell
    kubectl -n <Name of the namespace> describe <Name of the job> 
    ```  

    출력의 예제는 다음과 같습니다.

    ```powershell
    PS C:\WINDOWS\system32> kubectl -n mynamesp1 describe job.batch/cuda-sample1;  kubectl -n mynamesp1 describe job.batch/cuda-sample2
    Name:           cuda-sample1
    Namespace:      mynamesp1
    Selector:       controller-uid=22783f76-6af1-490d-b6eb-67dd4cda0e1f
    Labels:         controller-uid=22783f76-6af1-490d-b6eb-67dd4cda0e1f
                    job-name=cuda-sample1
    Annotations:    kubectl.kubernetes.io/last-applied-configuration:
                      {"apiVersion":"batch/v1","kind":"Job","metadata":{"annotations":{},"name":"cuda-sample1","namespace":"mynamesp1"},"spec":{"backoffLimit":1...
    Parallelism:    1
    Completions:    1
    Start Time:     Wed, 03 Mar 2021 12:25:34 -0800
    Pods Statuses:  1 Running / 0 Succeeded / 0 Failed
    Pod Template:
      Labels:  controller-uid=22783f76-6af1-490d-b6eb-67dd4cda0e1f
               job-name=cuda-sample1
      Containers:
       cuda-sample-container1:
        Image:      nvidia/samples:nbody
        Port:       <none>
        Host Port:  <none>
        Command:
          /tmp/nbody
        Args:
          -benchmark
          -i=10000
        Environment:
          NVIDIA_VISIBLE_DEVICES:  0
        Mounts:                    <none>
      Volumes:                     <none>
    Events:
      Type    Reason            Age   From            Message
      ----    ------            ----  ----            -------
      Normal  SuccessfulCreate  60s   job-controller  Created pod: cuda-sample1-27srm
    Name:           cuda-sample2
    Namespace:      mynamesp1
    Selector:       controller-uid=e68c8d5a-718e-4880-b53f-26458dc24381
    Labels:         controller-uid=e68c8d5a-718e-4880-b53f-26458dc24381
                    job-name=cuda-sample2
    Annotations:    kubectl.kubernetes.io/last-applied-configuration:
                      {"apiVersion":"batch/v1","kind":"Job","metadata":{"annotations":{},"name":"cuda-sample2","namespace":"mynamesp1"},"spec":{"backoffLimit":1...
    Parallelism:    1
    Completions:    1
    Start Time:     Wed, 03 Mar 2021 12:25:35 -0800
    Pods Statuses:  1 Running / 0 Succeeded / 0 Failed
    Pod Template:
      Labels:  controller-uid=e68c8d5a-718e-4880-b53f-26458dc24381
               job-name=cuda-sample2
      Containers:
       cuda-sample-container2:
        Image:      nvidia/samples:nbody
        Port:       <none>
        Host Port:  <none>
        Command:
          /tmp/nbody
        Args:
          -benchmark
          -i=10000
        Environment:
          NVIDIA_VISIBLE_DEVICES:  0
        Mounts:                    <none>
      Volumes:                     <none>
    Events:
      Type    Reason            Age   From            Message
      ----    ------            ----  ----            -------
      Normal  SuccessfulCreate  60s   job-controller  Created pod: cuda-sample2-db9vx
    PS C:\WINDOWS\system32>
    ```
    출력은 두 Pod가 작업에 의해 성공적으로 만들었음을 나타냅니다. 

1. 두 개의 컨테이너가 n-body 시뮬레이션을 실행하는 동안 Nvidia smi 출력에서 GPU 사용률을 확인합니다. 디바이스의 PowerShell 인터페이스로 이동하고 `Get-HcsGpuNvidiaSmi`를 실행합니다.

    다음은 두 개의 컨테이너가 n-body 시뮬레이션을 실행하는 경우의 출력 예제입니다.

    ```powershell
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    K8S-1HXQG13CL-1HXQG13:
    
    Wed Mar  3 12:26:41 2021
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 460.32.03    Driver Version: 460.32.03    CUDA Version: 11.2     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            On   | 00002C74:00:00.0 Off |                    0 |
    | N/A   64C    P0    69W /  70W |    221MiB / 15109MiB |    100%      Default |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |    0   N/A  N/A    197976      C   /tmp/nbody                        109MiB |
    |    0   N/A  N/A    198051      C   /tmp/nbody                        109MiB |
    +-----------------------------------------------------------------------------+
    [10.100.10.10]: PS>    
    ```
    여기에서 볼 수 있듯이, GPU 0에서 n-body 시뮬레이션을 사용하여 실행되는 두 개의 컨테이너(Type = C)가 있습니다. 

1. n-body 시뮬레이션을 모니터링합니다. `get pod` 명령을 실행합니다. 시뮬레이션을 실행하는 경우의 출력 예제는 다음과 같습니다. 

    ```powershell
    PS C:\WINDOWS\system32> kubectl get pods -n mynamesp1
    NAME                 READY   STATUS    RESTARTS   AGE
    cuda-sample1-27srm   1/1     Running   0          70s
    cuda-sample2-db9vx   1/1     Running   0          69s
    PS C:\WINDOWS\system32>
    ```

    시뮬레이션이 완료되면 출력에 표시됩니다. 출력의 예제는 다음과 같습니다.

    ```powershell
    PS C:\WINDOWS\system32> kubectl get pods -n mynamesp1
    NAME                 READY   STATUS      RESTARTS   AGE
    cuda-sample1-27srm   0/1     Completed   0          2m54s
    cuda-sample2-db9vx   0/1     Completed   0          2m53s
    PS C:\WINDOWS\system32>
    ```
 
1. 시뮬레이션이 완료된 후 로그 및 시뮬레이션 완료에 소요된 총 시간을 볼 수 있습니다. 다음 명령 실행:

    ```powershell
    kubectl logs -n <Name of the namespace> <pod name>
    ``` 

    출력의 예제는 다음과 같습니다. 

    ```powershell
    PS C:\WINDOWS\system32> kubectl logs -n mynamesp1 cuda-sample1-27srm
    Run "nbody -benchmark [-numbodies=<numBodies>]" to measure performance.
    ===========// CUT //===================// CUT //=====================  
    > Windowed mode
    > Simulation data stored in video memory
    > Single precision floating point simulation
    > 1 Devices used for simulation
    GPU Device 0: "Turing" with compute capability 7.5
    
    > Compute 7.5 CUDA device: [Tesla T4]
    40960 bodies, total time for 10000 iterations: 170398.766 ms
    = 98.459 billion interactions per second
    = 1969.171 single-precision GFLOP/s at 20 flops per interaction
    PS C:\WINDOWS\system32>
    ```

    ```powershell
    PS C:\WINDOWS\system32> kubectl logs -n mynamesp1 cuda-sample2-db9vx
    Run "nbody -benchmark [-numbodies=<numBodies>]" to measure performance.
    ===========// CUT //===================// CUT //=====================
    > Windowed mode
    > Simulation data stored in video memory
    > Single precision floating point simulation
    > 1 Devices used for simulation
    GPU Device 0: "Turing" with compute capability 7.5
    
    > Compute 7.5 CUDA device: [Tesla T4]
    40960 bodies, total time for 10000 iterations: 170368.859 ms
    = 98.476 billion interactions per second
    = 1969.517 single-precision GFLOP/s at 20 flops per interaction
    PS C:\WINDOWS\system32>    
    ```
1. 현재 GPU에서 실행 중인 프로세스가 없어야 합니다. Nvidia smi 출력을 사용하여 GPU 사용률을 확인하여 이를 확인할 수 있습니다.

    ```powershell
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    K8S-1HXQG13CL-1HXQG13:
    
    Wed Mar  3 12:32:52 2021
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 460.32.03    Driver Version: 460.32.03    CUDA Version: 11.2     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            On   | 00002C74:00:00.0 Off |                    0 |
    | N/A   38C    P8     9W /  70W |      0MiB / 15109MiB |      0%      Default |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |  No running processes found                                                 |
    +-----------------------------------------------------------------------------+
    [10.100.10.10]: PS>
    ```

## <a name="job-with-context-sharing"></a>컨텍스트 공유를 사용하는 작업

MPS를 통해 GPU 컨텍스트 공유를 사용하는 경우 두 번째 작업을 실행하여 두 개의 CUDA 컨테이너에 n-body 시뮬레이션을 배포합니다. 먼저 디바이스에서 MPS를 사용하도록 설정합니다.

1. [디바이스의 PowerShell 인터페이스에 연결합니다](azure-stack-edge-gpu-connect-powershell-interface.md).

1. 디바이스에서 MPS를 사용하도록 설정하려면 `Start-HcsGpuMPS` 명령을 실행합니다.

    ```powershell
    [10.100.10.10]: PS>Start-HcsGpuMPS
    K8S-1HXQG13CL-1HXQG13:
    
    Set compute mode to EXCLUSIVE_PROCESS for GPU 00002C74:00:00.0.
    All done.
    Created nvidia-mps.service
    [10.100.10.10]: PS>    
    ```
1. 이전에 사용한 것과 동일한 배포`yaml`을 사용하여 작업을 실행합니다. 기존 배포를 삭제해야 할 수도 있습니다. [배포 삭제](#delete-deployment)를 참조하세요.

    출력의 예제는 다음과 같습니다.

    ```yml
    PS C:\WINDOWS\system32> kubectl -n mynamesp1 delete -f C:\gpu-sharing\k8-gpusharing.yaml
    job.batch "cuda-sample1" deleted
    job.batch "cuda-sample2" deleted
    PS C:\WINDOWS\system32> kubectl get pods -n mynamesp1
    No resources found.
    PS C:\WINDOWS\system32> kubectl -n mynamesp1 apply -f C:\gpu-sharing\k8-gpusharing.yaml
    job.batch/cuda-sample1 created
    job.batch/cuda-sample2 created
    PS C:\WINDOWS\system32> kubectl get pods -n mynamesp1
    NAME                 READY   STATUS    RESTARTS   AGE
    cuda-sample1-vcznt   1/1     Running   0          21s
    cuda-sample2-zkx4w   1/1     Running   0          21s
    PS C:\WINDOWS\system32> kubectl -n mynamesp1 describe job.batch/cuda-sample1;  kubectl -n mynamesp1 describe job.batch/cuda-sample2
    Name:           cuda-sample1
    Namespace:      mynamesp1
    Selector:       controller-uid=ed06bdf0-a282-4b35-a2a0-c0d36303a35e
    Labels:         controller-uid=ed06bdf0-a282-4b35-a2a0-c0d36303a35e
                    job-name=cuda-sample1
    Annotations:    kubectl.kubernetes.io/last-applied-configuration:
                      {"apiVersion":"batch/v1","kind":"Job","metadata":{"annotations":{},"name":"cuda-sample1","namespace":"mynamesp1"},"spec":{"backoffLimit":1...
    Parallelism:    1
    Completions:    1
    Start Time:     Wed, 03 Mar 2021 21:51:51 -0800
    Pods Statuses:  1 Running / 0 Succeeded / 0 Failed
    Pod Template:
      Labels:  controller-uid=ed06bdf0-a282-4b35-a2a0-c0d36303a35e
               job-name=cuda-sample1
      Containers:
       cuda-sample-container1:
        Image:      nvidia/samples:nbody
        Port:       <none>
        Host Port:  <none>
        Command:
          /tmp/nbody
        Args:
          -benchmark
          -i=10000
        Environment:
          NVIDIA_VISIBLE_DEVICES:  0
        Mounts:                    <none>
      Volumes:                     <none>
    Events:
      Type    Reason            Age   From            Message
      ----    ------            ----  ----            -------
      Normal  SuccessfulCreate  46s   job-controller  Created pod: cuda-sample1-vcznt
    Name:           cuda-sample2
    Namespace:      mynamesp1
    Selector:       controller-uid=6282b8fa-e76d-4f45-aa85-653ee0212b29
    Labels:         controller-uid=6282b8fa-e76d-4f45-aa85-653ee0212b29
                    job-name=cuda-sample2
    Annotations:    kubectl.kubernetes.io/last-applied-configuration:
                      {"apiVersion":"batch/v1","kind":"Job","metadata":{"annotations":{},"name":"cuda-sample2","namespace":"mynamesp1"},"spec":{"backoffLimit":1...
    Parallelism:    1
    Completions:    1
    Start Time:     Wed, 03 Mar 2021 21:51:51 -0800
    Pods Statuses:  1 Running / 0 Succeeded / 0 Failed
    Pod Template:
      Labels:  controller-uid=6282b8fa-e76d-4f45-aa85-653ee0212b29
               job-name=cuda-sample2
      Containers:
       cuda-sample-container2:
        Image:      nvidia/samples:nbody
        Port:       <none>
        Host Port:  <none>
        Command:
          /tmp/nbody
        Args:
          -benchmark
          -i=10000
        Environment:
          NVIDIA_VISIBLE_DEVICES:  0
        Mounts:                    <none>
      Volumes:                     <none>
    Events:
      Type    Reason            Age   From            Message
      ----    ------            ----  ----            -------
      Normal  SuccessfulCreate  47s   job-controller  Created pod: cuda-sample2-zkx4w
    PS C:\WINDOWS\system32>
    ```

1. 시뮬레이션을 실행하는 동안 Nvidia smi 출력을 볼 수 있습니다. 출력은 n-body 시뮬레이션과 MPS 서비스(C type)가 실행되는 CUDA 컨테이너(M + C type)에 해당하는 프로세스를 보여 줍니다. 모든 프로세스는 GPU 0을 공유합니다.

    ```powershell
    PS>Get-HcsGpuNvidiaSmi
    K8S-1HXQG13CL-1HXQG13:
    
    Mon Mar  3 21:54:50 2021
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 460.32.03    Driver Version: 460.32.03    CUDA Version: 11.2     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            On   | 0000E00B:00:00.0 Off |                    0 |
    | N/A   45C    P0    68W /  70W |    242MiB / 15109MiB |    100%   E. Process |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |    0   N/A  N/A    144377    M+C   /tmp/nbody                        107MiB |
    |    0   N/A  N/A    144379    M+C   /tmp/nbody                        107MiB |
    |    0   N/A  N/A    144443      C   nvidia-cuda-mps-server             25MiB |
    +-----------------------------------------------------------------------------+
    ```

1. 시뮬레이션이 완료된 후 로그 및 시뮬레이션 완료에 소요된 총 시간을 볼 수 있습니다. 다음 명령 실행:

    ```powershell
        PS C:\WINDOWS\system32> kubectl get pods -n mynamesp1
        NAME                 READY   STATUS      RESTARTS   AGE
        cuda-sample1-vcznt   0/1     Completed   0          5m44s
        cuda-sample2-zkx4w   0/1     Completed   0          5m44s
        PS C:\WINDOWS\system32> kubectl logs -n mynamesp1 cuda-sample1-vcznt
        Run "nbody -benchmark [-numbodies=<numBodies>]" to measure performance.
        ===========// CUT //===================// CUT //=====================    
        > Windowed mode
        > Simulation data stored in video memory
        > Single precision floating point simulation
        > 1 Devices used for simulation
        GPU Device 0: "Turing" with compute capability 7.5
        
        > Compute 7.5 CUDA device: [Tesla T4]
        40960 bodies, total time for 10000 iterations: 154979.453 ms
        = 108.254 billion interactions per second
        = 2165.089 single-precision GFLOP/s at 20 flops per interaction


        PS C:\WINDOWS\system32> kubectl logs -n mynamesp1 cuda-sample2-zkx4w
        Run "nbody -benchmark [-numbodies=<numBodies>]" to measure performance.
        ===========// CUT //===================// CUT //=====================
        > Windowed mode
        > Simulation data stored in video memory
        > Single precision floating point simulation
        > 1 Devices used for simulation
        GPU Device 0: "Turing" with compute capability 7.5
        
        > Compute 7.5 CUDA device: [Tesla T4]
        40960 bodies, total time for 10000 iterations: 154986.734 ms
        = 108.249 billion interactions per second
        = 2164.987 single-precision GFLOP/s at 20 flops per interaction
        PS C:\WINDOWS\system32>
    ```
1. 시뮬레이션이 완료된 후 Nvidia smi 출력을 다시 확인할 수 있습니다. MPS 서비스에 대한 nvidia-cuda-mps-server 프로세스만 실행 중으로 표시됩니다. 

    ```powershell
    PS>Get-HcsGpuNvidiaSmi
    K8S-1HXQG13CL-1HXQG13:
    
    Mon Mar  3 21:59:55 2021
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 460.32.03    Driver Version: 460.32.03    CUDA Version: 11.2     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            On   | 0000E00B:00:00.0 Off |                    0 |
    | N/A   37C    P8     9W /  70W |     28MiB / 15109MiB |      0%   E. Process |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |    0   N/A  N/A    144443      C   nvidia-cuda-mps-server             25MiB |
    +-----------------------------------------------------------------------------+
    ```

## <a name="delete-deployment"></a>배포 삭제

MPS를 사용하도록 설정한 상태에서 디바이스에서 MPS를 사용하지 않는다면 배포를 삭제해야 할 수 있습니다.

디바이스에서 배포를 삭제하려면 다음 명령을 실행합니다. 

```powershell
kubectl delete -f <Path to the deployment .yaml> -n <Name of the namespace> 
```

출력의 예제는 다음과 같습니다.

```powershell
PS C:\WINDOWS\system32> kubectl delete -f 'C:\gpu-sharing\k8-gpusharing.yaml' -n mynamesp1
deployment.apps "cuda-sample1" deleted
deployment.apps "cuda-sample2" deleted
PS C:\WINDOWS\system32>
```
    
## <a name="next-steps"></a>다음 단계

- [Azure Stack Edge Pro에서 GPU 공유를 사용하여 IoT Edge 워크로드를 배포](azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing.md)합니다.
