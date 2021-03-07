---
title: Azure Stack Edge Pro GPU 장치에서 Edge 컨테이너 레지스트리를 사용 하도록 설정
description: Azure Stack Edge Pro GPU 장치에서 로컬에 지 컨테이너 레지스트리를 사용 하도록 설정 하는 방법을 설명 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: f19625f1d558071ccb29487efe56146756c7692c
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102437539"
---
# <a name="enable-edge-container-registry-on-your-azure-stack-edge-pro-gpu-device"></a>Azure Stack Edge Pro GPU 장치에서 Edge 컨테이너 레지스트리를 사용 하도록 설정

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

이 문서에서는 Edge 컨테이너 레지스트리를 사용 하도록 설정 하 고 Azure Stack Edge Pro 장치의 Kubernetes 클러스터 내에서 사용 하는 방법을 설명 합니다. 이 문서에 사용 된 예제에서는 원본 레지스트리에서 이미지를 푸시하는 방법 (이 경우 Microsoft Container registry)을 Edge 컨테이너 레지스트리 인 Azure Stack Edge 장치의 레지스트리에 푸시하는 방법에 대해 자세히 설명 합니다.

### <a name="about-edge-container-registry"></a>Edge 컨테이너 레지스트리 정보

컨테이너 화 된 계산 응용 프로그램은 컨테이너 이미지에서 실행 되며 이러한 이미지는 레지스트리에 저장 됩니다. 레지스트리는 Docker 허브, 사설 또는 클라우드 공급자 관리 (예: Azure Container Registry)와 같이 공용이 될 수 있습니다. 자세한 내용은 [레지스트리, 리포지토리 및 이미지 정보](../container-registry/container-registry-concepts.md)를 참조 하세요.

Edge 컨테이너 레지스트리는 Azure Stack Edge Pro 장치에서에 지에 대 한 리포지토리를 제공 합니다. 이 레지스트리를 사용 하 여 개인 컨테이너 이미지를 저장 하 고 관리할 수 있습니다.

다중 노드 환경에서는 컨테이너 이미지를 다운로드 하 여 Edge 컨테이너 레지스트리에 한 번 푸시할 수 있습니다. 모든에 지 응용 프로그램은 후속 배포에 Edge 컨테이너 레지스트리를 사용할 수 있습니다.


## <a name="prerequisites"></a>필수 구성 요소

시작하기 전에 다음 사항을 확인합니다.

1. Azure Stack Edge Pro 장치에 액세스할 수 있습니다.

1. [Azure Stack Edge Pro 활성화](azure-stack-edge-gpu-deploy-activate.md)에 설명된 대로 Azure Stack Edge Pro 디바이스를 활성화했습니다.

1. 장치에서 계산 역할을 사용 하도록 설정 했습니다. [Azure Stack Edge Pro 장치에서 계산 구성](azure-stack-edge-gpu-deploy-configure-compute.md)의 지침에 따라 장치에서 compute를 구성 하는 경우에도 장치에 Kubernetes 클러스터가 만들어집니다.

1. 로컬 웹 UI의 **장치** 페이지에서 Kubernetes API 끝점이 있습니다. 자세한 내용은 [KUBERNETES API 끝점 가져오기](azure-stack-edge-gpu-deploy-configure-compute.md#get-kubernetes-endpoints)의 지침을 참조 하세요.

1. [지원 되는 운영 체제](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device)를 사용 하 여 클라이언트 시스템에 액세스할 수 있습니다. Windows 클라이언트를 사용 하는 경우 시스템은 PowerShell 5.0 이상을 실행 하 여 장치에 액세스 해야 합니다.

    1. 사용자 고유의 컨테이너 이미지를 끌어오거나 푸시 하려면 시스템에 Docker 클라이언트가 설치 되어 있는지 확인 합니다. Windows 클라이언트를 사용 하는 경우 [windows에 Docker Desktop을 설치](https://docs.docker.com/docker-for-windows/install/)합니다.  


## <a name="enable-container-registry-as-add-on"></a>컨테이너 레지스트리를 추가 기능으로 사용

첫 번째 단계는 Edge 컨테이너 레지스트리를 추가 기능으로 사용 하도록 설정 하는 것입니다.

1. [장치의 PowerShell 인터페이스에 연결](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface)합니다. 

1. 컨테이너 레지스트리를 추가 기능으로 사용 하도록 설정 하려면 다음을 입력 합니다. 

    `Set-HcsKubernetesContainerRegistry`
    
    이 작업을 완료하는 데 몇 분 정도 걸릴 수 있습니다.

    이 명령의 샘플 출력은 다음과 같습니다.  
            
    ```powershell
    [10.128.44.40]: PS>Set-HcsKubernetesContainerRegistry
    Operation completed successfully. Use Get-HcsKubernetesContainerRegistryInfo for credentials    
    ```
            
1. 컨테이너 레지스트리 세부 정보를 가져오려면 다음을 입력 합니다.

    `Get-HcsKubernetesContainerRegistryInfo`

    다음은이 명령에서 제외 되는 샘플입니다.  
    
    ```powershell
    [10.128.44.40]: PS> Get-HcsKubernetesContainerRegistryInfo
                
    Endpoint                                   IPAddress    Username     Password
    --------                                   ---------    --------     --------
    ecr.dbe-hw6h1t2.microsoftdatabox.com:31001 10.128.44.41 ase-ecr-user i3eTsU4zGYyIgxV
    ``` 

1. 의 출력에서 사용자 이름 및 암호를 기록해 둡니다 `Get-HcsKubernetesContainerRegistryInfo` . 이러한 자격 증명은 이미지를 푸시하는 동안에 지 컨테이너 레지스트리에 로그인 하는 데 사용 됩니다.         


## <a name="manage-container-registry-images"></a>컨테이너 레지스트리 이미지 관리

Azure Stack Edge 장치 외부에서 컨테이너 레지스트리에 액세스할 수 있습니다. 레지스트리에서 이미지를 끌어오거나 끌어올 수도 있습니다.

Edge 컨테이너 레지스트리에 액세스 하려면 다음 단계를 수행 합니다.

1. Edge 컨테이너 레지스트리에 대 한 끝점 세부 정보를 가져옵니다.
    1. 장치의 로컬 UI에서 **장치** 로 이동 합니다.
    1. **Edge 컨테이너 레지스트리 끝점** 을 찾습니다.
        ![장치 페이지의 Edge 컨테이너 레지스트리 끝점](media/azure-stack-edge-gpu-edge-container-registry/get-edge-container-registry-endpoint-1.png) 
    1. 이 끝점을 복사 하 고 클라이언트의 파일에 해당 하는 DNS 항목을 만들어 `C:\Windows\System32\Drivers\etc\hosts` Edge 컨테이너 레지스트리 끝점에 연결 합니다. 

        <IP address of the Kubernetes main node>    <Edge container registry endpoint> 
        
        ![Edge 컨테이너 레지스트리 끝점에 대 한 DNS 항목 추가](media/azure-stack-edge-gpu-edge-container-registry/add-domain-name-service-entry-hosts-1.png)    

1. 로컬 UI에서 Edge 컨테이너 레지스트리 인증서를 다운로드 합니다. 
    1. 장치의 로컬 UI에서 **인증서** 로 이동 합니다.
    1. **Edge 컨테이너 레지스트리 인증서** 에 대 한 항목을 찾습니다. 이 항목의 오른쪽에서 **다운로드** 를 선택 하 여 장치에 액세스 하는 데 사용할 클라이언트 시스템에 Edge 컨테이너 레지스트리 인증서를 다운로드 합니다. 

        ![Edge 컨테이너 레지스트리 끝점 인증서 다운로드](media/azure-stack-edge-gpu-edge-container-registry/download-edge-container-registry-endpoint-certificate-1.png)  

1. 클라이언트에 다운로드 한 인증서를 설치 합니다. Windows 클라이언트를 사용 하는 경우 다음 단계를 수행 합니다. 
    1. 인증서를 선택 하 고 **인증서 가져오기 마법사** 에서 **로컬 컴퓨터로** 위치 저장을 선택 합니다. 

        ![인증서 설치 1](media/azure-stack-edge-gpu-edge-container-registry/install-certificate-1.png) 
    
    1. 로컬 컴퓨터의 신뢰할 수 있는 루트 저장소에 인증서를 설치 합니다. 

        ![인증서 2 설치](media/azure-stack-edge-gpu-edge-container-registry/install-certificate-2.png) 

1. 인증서가 설치 된 후 시스템에서 Docker 클라이언트를 다시 시작 합니다.

1. Edge 컨테이너 레지스트리에 로그인 합니다. 유형:

    `docker login <Edge container registry endpoint> -u <username> -p <password>`

    **장치** 페이지에서에 지 컨테이너 레지스트리 엔드포인트를 제공 하 고 출력에서 가져온 사용자 이름 및 암호를 제공 `Get-HcsKubernetesContainerRegistryInfo` 합니다. 

1. Docker push 또는 pull 명령을 사용 하 여 컨테이너 레지스트리에서 컨테이너 이미지를 끌어오거나 풀 합니다.
 
    1. Microsoft Container Registry 이미지에서 이미지를 끌어옵니다. 유형:
        
        `docker pull <Full path to the container image in the Microsoft Container Registry>`
       
    1. 레지스트리의 정규화 된 경로를 사용 하 여 끌어온 이미지의 별칭을 만듭니다.

        `docker tag <Path to the image in the Microsoft container registry> <Path to the image in the Edge container registry/Image name with tag>`

    1. 레지스트리에 이미지를 밀어넣습니다.
    
        `docker push <Path to the image in the Edge container registry/Image name with tag>`

    1. 레지스트리에 푸시된 이미지를 실행 합니다.
    
        `docker run -it --rm -p 8080:80 <Path to the image in the Edge container registry/Image name with tag>`

        다음은 pull 및 push 명령의 샘플 출력입니다.

        ```powershell
        PS C:\WINDOWS\system32> docker login ecr.dbe-hw6h1t2.microsoftdatabox.com:31001 -u ase-ecr-user -p 3bbo2sOtDe8FouD
        WARNING! Using --password via the CLI is insecure. Use --password-stdin.
        Login Succeeded
        PS C:\WINDOWS\system32> docker pull mcr.microsoft.com/oss/nginx/nginx:1.17.5-alpine
        1.17.5-alpine: Pulling from oss/nginx/nginx
        Digest: sha256:5466bbc0a989bd1cd283c0ba86d9c2fc133491ccfaea63160089f47b32ae973b
        Status: Image is up to date for mcr.microsoft.com/oss/nginx/nginx:1.17.5-alpine
        mcr.microsoft.com/oss/nginx/nginx:1.17.5-alpine
        PS C:\WINDOWS\system32> docker tag mcr.microsoft.com/oss/nginx/nginx:1.17.5-alpine ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/nginx:2.0
        PS C:\WINDOWS\system32> docker push ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/nginx:2.0
        The push refers to repository [ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/nginx]
        bba7d2385bc1: Pushed
        77cae8ab23bf: Pushed
        2.0: digest: sha256:b4c0378c841cd76f0b75bc63454bfc6fe194a5220d4eab0d75963bccdbc327ff size: 739
        PS C:\WINDOWS\system32> docker run -it --rm -p 8080:80 ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/nginx:2.0
        2020/11/10 00:00:49 [error] 6#6: *1 open() "/usr/share/nginx/html/favicon.ico" failed (2: No such file or directory), client: 172.17.0.1, server: localhost, request: "GET /favicon.ico HTTP/1.1", host: "localhost:8080", referrer: "http://localhost:8080/"
        172.17.0.1 - - [10/Nov/2020:00:00:49 +0000] "GET /favicon.ico HTTP/1.1" 404 555 "http://localhost:8080/" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/86.0.4240.183 Safari/537.36" "-"
        ^C
        PS C:\WINDOWS\system32>    
        ```
    
1. `http://localhost:8080`으로 이동하여 실행 중인 컨테이너를 봅니다. 이 경우 nginx 웹 서버를 실행 하는 것을 볼 수 있습니다.

    ![실행 중인 컨테이너 보기](media/azure-stack-edge-gpu-edge-container-registry/view-running-container-1.png)

    컨테이너를 중지하고 제거하려면 `Control+C`를 누릅니다.

 

## <a name="use-edge-container-registry-images-via-kubernetes-pods"></a>Kubernetes pod를 통해 Edge 컨테이너 레지스트리 이미지 사용

이제 Kubernetes pod 내에서 Edge 컨테이너 레지스트리에 푸시된 이미지를 배포할 수 있습니다.

1. 이미지를 배포 하려면 *kubectl* 를 통해 클러스터 액세스를 구성 해야 합니다. 네임 스페이스를 만들고 사용자에 게 네임 스페이스에 대 한 액세스 권한을 부여 하 고 *구성* 파일을 가져옵니다. Kubernetes pod에 연결할 수 있는지 확인 합니다. 
    
    [Azure Stack Edge PRO GPU 장치에서 kubectl를 통해 Kubernetes 클러스터에 연결 및 관리](azure-stack-edge-gpu-create-kubernetes-cluster.md)의 모든 단계를 수행 합니다. 

    사용자가 Kubernetes 클러스터에 액세스할 수 있는 장치에서 네임 스페이스에 대 한 샘플 출력은 다음과 같습니다.

    ```powershell
    [10.128.44.40]: PS>New-HcsKubernetesNamespace -Namespace myecr
    [10.128.44.40]: PS>New-HcsKubernetesUser -UserName ecruser
    apiVersion: v1
    clusters:
    - cluster:
        certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUN5RENDQWJDZ0F3SUJBZ0lCQURBTkJna3Foa2lHOXcwQkFRc0ZBREFWTVJNd0VRWURWUVFERXdwcmRXSmwKY201bGRHVnpNQjRYRFRJd01URXdOVEF6TkRJek1Gb1hEVE13TVRFd016QXpOREl6TUZvd0ZURVRNQkVnNjOVRLWndCQ042cm1XQms2eXFwcXI1MUx6bApTaXMyTy91UEJ2YXNSSUUzdzgrbmEwdG1aTERZZ2F6MkQwMm42Q29mUmtyUTR2d1lLTnR1MlpzR3pUdz0KLS0tLS1FTkQgQ0VSVElGSUNBVEUtLS0tLQo=
        server: https://compute.dbe-hw6h1t2.microsoftdatabox.com:6443
        name: kubernetes
        ===================CUT=========================================CUT==============
        client-certificate-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUMwRENDQWJpZ0F3SUJBZ0lJYmVWRGJSTzZ3ell3RFFZSktvWklodmNOQVFFTEJRQXdGVEVUTUJFR0ExVUUKQXhNS2EzVmlaWEp1WlhSbGN6QWVGdzB5TURFeE1EVXdNelF5TXpCYUZ3MHlNVEV4TURreU16UTRNal
        ===================CUT=========================================CUT==============
        DMVUvN3lFOG5UU3k3b2VPWitUeHdzCjF1UDByMjhDZ1lCdHdRY0ZpcFh1blN5ak16dTNIYjhveFI2V3VWWmZldFFKNElKWEFXOStVWGhKTFhyQ2x4bUcKWHRtbCt4UU5UTzFjQVNKRVZWVDd6Tjg2ay9kSU43S3JIVkdUdUxlUDd4eGVjV2VRcWJrZEVScUsxN0liTXpiVApmbnNxc0dobEdmLzdmM21kTGtyOENrcWs5TU5aM3MvUVIwRlFCdk94ZVpuUlpTeDVGbUR5S1E9PQotLS0tLUVORCBSU0EgUFJJVkFURSBLRVktLS0tLQo=

    [10.128.44.40]: PS>Grant-HcsKubernetesNamespaceAccess -Namespace myecr -UserName ecruser
    [10.128.44.40]: PS>kubectl get pods -n "myecr"
    No resources found.
    PS C:\WINDOWS\system32>
    ```  

2. 이미지 끌어오기 비밀이 장치의 모든 Kubernetes 네임 스페이스에 이미 설정 되어 있습니다. 명령을 사용 하 여 암호를 가져올 수 있습니다 `get secrets` . 샘플 출력은 다음과 같습니다.

    ```powershell
    PS C:\WINDOWS\system32> .\kubectl.exe get secrets -n myecr
    NAME                  TYPE                                  DATA   AGE
    ase-ecr-credentials   kubernetes.io/dockerconfigjson        1      99m
    default-token-c7kww   kubernetes.io/service-account-token   3      107m
    sec-smbcredentials    microsoft.com/smb                     2      99m
    PS C:\WINDOWS\system32>   
    ```    

3. Kubectl를 사용 하 여 pod를 네임 스페이스에 배포 합니다. 다음을 사용 `yaml` 합니다. 

    이미지를 `<image-name>` 컨테이너 레지스트리에 푸시된 이미지로 바꿉니다. 이름으로 imagePullSecrets을 사용 하 여 네임 스페이스의 비밀을 참조 `ase-ecr-credentials` 합니다.
    
    ```yml
    apiVersion: v1
    kind: Pod
    metadata:
      name: nginx
    spec:
      containers:
      - name: nginx
        image: ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/nginx:2.0
        imagePullPolicy: Always
      imagePullSecrets:
      - name: ase-ecr-credentials
    ```

4. Apply 명령을 사용 하 여 만든 네임 스페이스에 배포를 적용 합니다. 컨테이너가 실행 중인지 확인 합니다. 샘플 출력은 다음과 같습니다.
   
    ```yml
    PS C:\Windows\System32> .\kubectl.exe apply -f .\deployment.yml -n myecr
    pod/nginx configured
    PS C:\Windows\System32> .\kubectl.exe get pods -n myecr
    NAME    READY   STATUS    RESTARTS   AGE
    nginx   1/1     Running   0          27m
    PS C:\Windows\System32>
    ```

## <a name="delete-container-registry-images"></a>컨테이너 레지스트리 이미지 삭제

Edge Container Registry 저장소는 장치에서 사용 가능한 저장소에 의해 제한 되는 Azure Stack Edge Pro 장치 내의 로컬 공유에서 호스팅됩니다. Docker HTTP v2 API (를 사용 하 여 컨테이너 레지스트리에서 사용 하지 않는 docker 이미지를 삭제 하는 것은 사용자의 책임입니다 https://docs.docker.com/registry/spec/api/) .  

컨테이너 이미지를 하나 이상 제거 하려면 다음 단계를 수행 합니다.

1. 이미지 이름을 삭제 하려는 이미지로 설정 합니다.

    ```powershell
    PS C:\WINDOWS\system32> $imageName="nginx"    
    ```

1. 컨테이너 레지스트리의 사용자 이름 및 암호를 PS 자격 증명으로 설정 합니다.

    ```powershell
    PS C:\WINDOWS\system32> $username="ase-ecr-user"
    PS C:\WINDOWS\system32> $password="3bbo2sOtDe8FouD"
    PS C:\WINDOWS\system32> $securePassword = ConvertTo-SecureString $password -AsPlainText -Force
    PS C:\WINDOWS\system32> $credential = New-Object System.Management.Automation.PSCredential ($username, $securePassword)    
    ```

1. 이미지와 연결 된 태그 나열

    ```powershell
    PS C:\WINDOWS\system32> $tags = Invoke-RestMethod -Credential $credential -Uri "https://ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/v2/nginx/tags/list" | Select-Object -ExpandProperty tags
    PS C:\WINDOWS\system32> $tags
    2.0
    PS C:\WINDOWS\system32> $tags = Invoke-RestMethod -Credential $credential -Uri "https://ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/v2/$imageName/tags/list" | Select-Object -ExpandProperty tags
    PS C:\WINDOWS\system32> $tags
    2.0
    PS C:\WINDOWS\system32>    
    ```
  
1. 삭제할 태그와 연결 된 다이제스트를 나열 합니다. 이는 위의 명령 출력에서 $tags를 사용 합니다. 태그가 여러 개인 경우 하나를 선택 하 고 다음 명령에서를 사용 합니다.

    ```powershell
    PS C:\WINDOWS\system32> $response = Invoke-WebRequest -Method Head -Credential $credential -Uri "https://ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/v2/$imageName/manifests/$tags" -Headers @{ 'Accept' = 'application/vnd.docker.distribution.manifest.v2+json' }
    PS C:\WINDOWS\system32> $digest = $response.Headers['Docker-Content-Digest']
    PS C:\WINDOWS\system32> $digest
    sha256:b4c0378c841cd76f0b75bc63454bfc6fe194a5220d4eab0d75963bccdbc327ff
    PS C:\WINDOWS\system32>    
    ```
1. Image: tag의 다이제스트를 사용 하 여 이미지를 삭제 합니다.

    ```powershell
    PS C:\WINDOWS\system32> Invoke-WebRequest -Method Delete -Credential $credential -Uri "https://ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/v2/$imageName/manifests/$digest" | Select-Object -ExpandProperty StatusDescription    
    ```

사용 하지 않는 이미지를 삭제 한 후에는 참조 되지 않은 이미지와 연결 된 공간을 매일 실행 하는 프로세스에서 자동으로 회수 합니다. 

## <a name="next-steps"></a>다음 단계

- [Azure Stack Edge Pro에 상태 비저장 응용 프로그램을 배포](azure-stack-edge-j-series-deploy-stateless-application-kubernetes.md)합니다.
