---
title: Azure Stack Edge Pro GPU 디바이스에서 Kubernetes 클러스터 만들기 및 관리 | Microsoft Docs
description: Windows PowerShell 인터페이스를 통해 Azure Stack Edge Pro GPU 디바이스에서 Kubernetes 클러스터를 만들고 관리하는 방법을 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/08/2021
ms.author: alkohli
ms.openlocfilehash: ddbf3c99845f4c38f511260f4dec274903a9d586
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102631962"
---
# <a name="connect-to-and-manage-a-kubernetes-cluster-via-kubectl-on-your-azure-stack-edge-pro-gpu-device"></a>Azure Stack Edge Pro GPU 디바이스에서 kubectl을 통해 Kubernetes 클러스터에 연결하고 관리합니다.

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Azure Stack Edge Pro 디바이스에서 컴퓨팅 역할을 구성할 때 Kubernetes 클러스터가 만들어집니다. Kubernetes 클러스터가 생성되면 *kubectl* 과 같은 네이티브 도구를 통해 클라이언트 머신에서 로컬로 클러스터에 연결하고 관리할 수 ​​있습니다.

이 문서에서는 Azure Stack Edge Pro 디바이스에서 Kubernetes 클러스터에 연결하고 *kubectl* 을 사용하여 관리하는 방법을 설명합니다. 


## <a name="prerequisites"></a>필수 구성 요소

시작하기 전에 다음 사항을 확인합니다.

1. Azure Stack Edge Pro 디바이스에 액세스할 수 있습니다.

2. [Azure Stack Edge Pro 활성화](azure-stack-edge-gpu-deploy-activate.md)에 설명된 대로 Azure Stack Edge Pro 디바이스를 활성화했습니다.

3. 디바이스에서 컴퓨팅 역할을 사용하도록 설정했습니다. [Azure Stack Edge Pro 디바이스에서 컴퓨팅 구성](azure-stack-edge-gpu-deploy-configure-compute.md)의 지침에 따라 디바이스에서 컴퓨팅을 구성하는 경우에도 디바이스에 Kubernetes 클러스터가 생성됐습니다.

4. 디바이스 액세스용으로 PowerShell 5.0 이상을 실행하는 Windows 클라이언트 시스템에 액세스할 수 있습니다. [지원되는 운영 체제](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device)를 사용하는 다른 클라이언트도 있을 수 있습니다. 

5. 로컬 웹 UI의 **디바이스** 페이지에 Kubernetes API 엔드포인트가 있습니다. 자세한 내용은 [Kubernetes API 엔드포인트 가져오기](azure-stack-edge-gpu-deploy-configure-compute.md#get-kubernetes-endpoints)의 지침을 참조하세요.


## <a name="connect-to-powershell-interface"></a>PowerShell 인터페이스에 연결

Kubernetes 클러스터가 생성된 후 이 클러스터에 액세스하여 네임스페이스 및 사용자를 만들고 네임스페이스에 사용자를 할당할 수 있습니다. 이를 위해 디바이스의 PowerShell 인터페이스에 연결해야 합니다. PowerShell을 실행하는 Windows 클라이언트에서 다음 단계를 수행합니다.

[!INCLUDE [Connect to admin runspace](../../includes/azure-stack-edge-gateway-connect-minishell.md)]


## <a name="configure-cluster-access-via-kubernetes-rbac"></a>Kubernetes RBAC를 통해 클러스터 액세스 구성

Kubernetes 클러스터가 생성된 후 명령줄을 통해 *kubectl* 을 사용하여 클러스터에 액세스할 수 있습니다. 

이를 통해 네임스페이스와 사용자를 생성합니다. 그런 다음, 사용자를 네임스페이스와 연결합니다. 또한 Azure Stack Edge Pro 디바이스의 PowerShell 인터페이스에 연결하지 않고도 Kubernetes 클라이언트를 사용하여 Kubernetes 클러스터와 직접 통신할 수 있도록 *구성* 파일을 가져와야 합니다.

1. 네임스페이스 만들기 유형:

    `New-HcsKubernetesNamespace -Namespace <string>` 

    > [!NOTE]
    > 네임스페이스와 사용자 이름에는 [DNS 하위 도메인 명명 규칙](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/#dns-subdomain-names)이 적용됩니다.

    샘플 출력은 다음과 같습니다.

    `[10.100.10.10]: PS> New-HcsKubernetesNamespace -Namespace "myasetest1"`

2. 사용자를 만들고 구성 파일을 가져옵니다. 유형:

    `New-HcsKubernetesUser -UserName <string>`

    > [!NOTE]
    > *aseuser* 는 Azure Stack Edge Pro 용 IoT 네임스페이스와 연결된 기본 사용자 용으로 사용되기 때문에 사용자 이름으로 사용할 수 없습니다.

    구성 파일의 샘플 출력은 다음과 같습니다.
   
    ```powershell
    [10.100.10.10]: PS> New-HcsKubernetesUser -UserName "aseuser1"
        apiVersion: v1
        clusters:
        - cluster:
            certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUN5RENDQWJDZ0F3SUJBZ0lCQURBTkJna3Foa2lHOXcwQkFRc0ZBREFWTVJNd0VRWURWUVFERXdwcmRXSmwKY201bGRHVnpNQjRYRFRJd01ERXlPVEUyTlRFeE4xb1hEVE13TURFeU5qRTJOVEV4TjFvd0ZURVRNQkVHQTFVRQpBeE1LYTNWaVpYSnVaWFJsY3pDQ0FTSXdEUVlKS29aSWh2Y05BUUVCQlFBRGdnRVBBRENDQVFvQ2dnRUJBTXNpCkdZUHB0U1VWcDhKZEdVcHE1MVBURWhsZm8wWkk3YXFBdUlrOHZWdUFCZHJQK1FBSzFxcEN1di93NjIwbUtpZ0QKak1aT3Q4QkREREppWHF6UDZRZm5Oc0U2VXBHMnh0YnYrcTZHV2R5K0t6WkxMbXlwWGY3VjlzZEJnejVKVDNvYQpIdzFja2NTUklHSlV3UWxTbklNaHJUS3JUNDZFUUp3d282TmlNUzZMZDZieVk3WkUrTGg3OS9aNEhLanhTRmhMClc5ZG8veThZR3FXUDZmZTFmMmVmSkhUeGtwR05HZE1UVjNuOFlCZ0pSRzdrNjh0N2MrZ1NhbUlVWVJpTUNSNFAKYlFxcFpscWYvV2REZEJHOFh6aDJ0M1l4SkVIMm00T0Z1cSsvUitMYm95aHdKbmNMdVJ5OEpNZWlwTEQ3UlN0QwpZTDNNR0EzN2JieTRyYm4zVzg4Q0F3RUFBYU1qTUNFd0RnWURWUjBQQVFIL0JBUURBZ0trTUE4R0ExVWRFd0VCCi93UUZNQU1CQWY4d0RRWUpLb1pJaHZjTkFRRUxCUUFEZ2dFQkFNbzFwWlBtQzV1cmRPZUJhSWQ4eEQzRkxCMG8KTlErbXBXMWpDd0ZtY3h6dUtlWmRsNXc2N0tuS2JTcDR0TXo1cXg3bUtSc0UxcnBoWkh2VHlKUXg1ZFk2ZE1Kdgp5d2FQZjBpT05TNlU2cC9INE12U1dJaEtJZ1FuTnE1dDh4TjJCNnZpQW41RmZoRkx6WEQrUlZGSm42cnovWkZnCmV6MHpxTkNKYmcvelFucFROcmQ2cnFFRHpoSVFZOVdYVWQycFh3ZXRqUXJpMkpZamh4NmtEcTVoRkZTM0FLUnIKOWlQTVQxaWNkR1NUMFVvM1hIZ1k2ck45WGp3MHFrY2I0Sy83UlVVWlRvS3dKamROR3lNTnpad000L2puR0p5SwpQTE9ycU5Ddlkvb0lkVEM5eVZVY3VRbXVlR0VqT20xUnN1RDFHYVE0RTZwakppVWJpMVdrajJ1bFhOWT0KLS0tLS1FTkQgQ0VSVElGSUNBVEUtLS0tLQo=
            server: https://10.128.47.90:6443
            name: kubernetes
        contexts:
        - context:
            cluster: kubernetes
            user: aseuser1
            name: aseuser1@kubernetes
        current-context: aseuser1@kubernetes
        kind: Config
        preferences: {}
        users:
        - name: aseuser1
            user:
            client-certificate-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUMwVENDQWJtZ0F3SUJBZ0lJWlFXcjY2cGFWSm93RFFZSktvWklodmNOQVFFTEJRQXdGVEVUTUJFR0ExVUUKQXhNS2EzVmlaWEp1WlhSbGN6QWVGdzB5TURBeE1qa3hOalV4TVRkYUZ3MHlNVEF4TWpneU1qVTJNVGRhTUJNeApFVEFQQmdOVkJBTVRDR0Z6WlhWelpYSXhNSUlCSWpBTkJna3Foa2lHOXcwQkFRRUZBQU9DQVE4QU1JSUJDZ0tDCkFRRUF4R3RDaXJ2cVhGYm5wVmtaYlVPRWQ3cXg2UzNVZ092MlhHRHNKM2VYWXN0bUxQVjMrQnlBcWwyN211L1AKaWdmaWt6MG9QSW1iYmtvcVJkamlYckxFZnk0N3dHcEhzdUhUOHNLY2tHTnJleFE2bXhaZ29xaU1nL2FuMUpMdwpiOFEvVnlQaWdVdUt6eVBseEhUZmlmSVM5MzR1VnZVZUc0dzlMRjAyZ2s2Nitpc0ZtanhsVmhseWRMNlc2UmZTCjl0OGpNMEFkdEpJL0xNbE13RHJJRVdFKzM4WDVNelJhQkJYNnlzNDFWSkZxekcwdW14dHdxN2pGOXp1UTE4ekIKalRZaDl3OWVKcDJwS2Fvak5tNE9SSDh4SzVSaUhocjJ2anFJWXkxRDd2WDh0b0U1K05HNmxHZjh5L1NvQnNRbQpmOG9vL1k3SEZmQXVGdlN6WUc1RUlQTFM4UUlEQVFBQm95Y3dKVEFPQmdOVkhROEJBZjhFQkFNQ0JhQXdFd1lEClZSMGxCQXd3Q2dZSUt3WUJCUVVIQXdJd0RRWUpLb1pJaHZjTkFRRUxCUUFEZ2dFQkFNR1BxY0YzS1BCbHZ0K24KN1NOcGE3anhWYkhZVGxyNTgwVWxzek93WEwwVnVPUUlCYmN2djIzZk9HNkhDZlQ0bWxBU0JRWVNZcmpLMjJTTwpTWld4cjNQUDlhVzNHajkxc0ttSnc1ZUF1WFhQbUJpK1RWQzBvY0ZLaEQvZ0o1aC93YnBaVndpVjVyRWE5Kzc2CnhNcFAzRld6dG5tT1hPaEl6UFNlR3B4YWpwQXd3ZXd4QU0yb0xGRFZFcy9XTFFMODJZM3NFcE93NVNaSVJJNXMKUHhMUTVnV1ZPM2x2SXcwZ3IrdkJlanZSOUZKaWVuTWFRdGdjSVgyRmpDaDBRMHVYRkdsTVNXWEljbjRLRTR0TApQSFFMalRSVUwyVnRXcW1YZ1RBM3RzN01DcGNRTFdPZFJUYkpSejZCbkc1aXVwcDdOSlFvYW9YcWpNVk5DVDZCCllYMEd0Skk9Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K
            client-key-data: LS0tLS1CRUdJTiBSU0EgUFJJVkFURSBLRVktLS0tLQpNSUlFb2dJQkFBS0NBUUVBeEd0Q2lydnFYRmJucFZrWmJVT0VkN3F4NlMzVWdPdjJYR0RzSjNlWFlzdG1MUFYzCitCeUFxbDI3bXUvUGlnZmlrejBvUEltYmJrb3FSZGppWHJMRWZ5NDd3R3BIc3VIVDhzS2NrR05yZXhRNm14WmcKb3FpTWcvYW4xSkx3YjhRL1Z5UGlnVXVLenlQbHhIVGZpZklTOTM0dVZ2VWVHNHc5TEYwMmdrNjYraXNGbWp4bApWaGx5ZEw2VzZSZlM5dDhqTTBBZHRKSS9MTWxNd0RySUVXRSszOFg1TXpSYUJCWDZ5czQxVkpGcXpHMHVteHR3CnE3akY5enVRMTh6QmpUWWg5dzllSnAycEthb2pObTRPUkg4eEs1UmlIaHIydmpxSVl5MUQ3dlg4dG9FNStORzYKbEdmOHkvU29Cc1FtZjhvby9ZN0hGZkF1RnZTellHNUVJUExTOFFJREFRQUJBb0lCQUVVSUVXM2kxMTQycU5raQo5RjNEWWZZV1pscTJZYjRoc0FjTmhWSGxwUTN5d0dsQ3FEUktDQ3BZSVF3MkJqSFR6WnpEM0xWU0E0K0NmMUxuCkE4QVdnaHJVcStsWE1QVzhpcG9DTGJaTlNzUUord0x3bld2dFl0MHFQaGZtd0p2M1UrK1RUQkwyOHNVVUw3ZVkKLzh0aWlhbno3ZU5mNklIMENyZmgxcnQ3WWhsemtRd1hBVHNScVJja0dMaTgrdGN5WnVzdGFhbENUSzBGRTdCaQpBUGE5a2w1SG56eCs4TTcvNWladHkwTUIxYWpWMnlGblBkUmlKSFVCb1AxVVV0QUthYjVZU0RvNllkZ2pIUTRHCjNWN1l1YWZobnVFMXA0VVIvUkloVVdjRlVVaTFBOFpZMFdnd1BDTmhnMWpQZU5vb2Y1UHpRbEY1OTRBREVwUUYKOFR2bG92RUNnWUVBOWZZbUxyY0tlQ0JiMTFoQVhoTi91Z1RTbU5xNnpFL1pPSWl6M0xwckdjRDhvWDdCVW9GcgplelkxbktSS2tkczE5OERnVjlQZUhuNzllQTRoMjM5RkIwNFFhMUJBdUVMRzRsdHJ3VlNxaFBENUR6YkcrSEhSCnJtYThVMEpUSmVVS0tJVjRUUGxlTzFtK2tjbkRJVXY1ckpwZDVXU3RvcUhXdk9RZkEvRUF0VlVDZ1lFQXpHOTcKTitCZVVvbFNiREttVUNGdTdPZGhYSXJYR3RnSEorZ2JOMDlnSHRURG5PY0IxZ1NzNkpZa1FPQU9qbWFxK05lRAp5SUF1NytheWlFRmpyT2tzTGhkSTREUXNkWFZveFFGVko1V1JwWlk3UTVRaFZpYUR2enR4NDlzSDlKSkplM2U0Cnl3NWdpNGkxKy90MnY2eWRKcWdNQ0xxOHlEdFRrcE9PSitkbkp5MENnWUJwZ3lpcURaZU9KTU9CUTdpSkl2QSsKQ21lVmJ1K0hTaEd6TU9HSHBPamc2V3IybEh1Mk94S3lqblM5TjdWTmtLNDhGQitwVFpnUm1RUi9CZ0Q4T2tLUQplYXFOZnFYazViQ1AxZ3dKcVpwazRVTFdoZmNoQ1NLY0lESlZ2VFFTSTRrU0RQK29kYWs0Nkt6WnVhWGRtTXdJCmdVZ2FhZkFhdmpaeVhhSDRmT0NDNlFLQmdHVXJCaDh3dVh5KzJEc1RGWnF4OE9McjNoS2Q0clUyRXRSODJIc1cKbk1xbEgraVZxU0x3VFdFTWJBUnUzTVU3cVlCYnBxdWlRNWdVNG1UcmR4Z3FpK0tEUTEwd2RJL3IrbDBEdTlCTApCRGlkajlaeGg4M0tZWWhSTXBzLzJULys1TDVsRU4zcnozczl2RkZtcisxS3pycENqeklDdDBtZmtrd0hHV0pGCjhaWkJBb0dBVXB3aUIrcWlHbkpxU1FtZHNSZFVabGFBaTRpbGhaa01RYTRHem95ZFQ3OTVHTm44ZThBRjd3WHMKTGpyYjdEV1FwakdCMnZpUlkySUZBVmIyKzZsdDlwOVJRMTZnSmxpNU5ZRXVvQWRoWXBsVWdBZGFHWHNGNHdabwo3SHFHTHBGdmUxVU5Gb0dQdkxpWUNrUFVYdGduQ3dNb0R2SEpKNzVYMXl6ckh6cmxUS1k9Ci0tLS0tRU5EIFJTQSBQUklWQVRFIEtFWS0tLS0tCg==  
        
    [10.100.10.10]: PS>
    ```
    

3. 구성 파일은 일반 텍스트로 표시됩니다. 이 파일을 복사하여 *구성* 파일로 저장합니다. 

    > [!IMPORTANT]
    > 구성 파일을 *.txt* 파일로 저장하지 말고 파일 확장명 없이 저장합니다.

4. 구성 파일은 로컬 컴퓨터에 있는 사용자 프로필의 `.kube` 폴더에 있어야 합니다. 사용자 프로필의 해당 폴더에 파일을 복사합니다.

    ![클라이언트에 있는 구성 파일의 위치](media/azure-stack-edge-gpu-create-kubernetes-cluster/location-config-file.png)

5. 생성한 사용자를 네임스페이스와 연결합니다. 유형:

    `Grant-HcsKubernetesNamespaceAccess -Namespace <string> -UserName <string>`

    샘플 출력은 다음과 같습니다.

    `[10.100.10.10]: PS>Grant-HcsKubernetesNamespaceAccess -Namespace "myasetest1" -UserName "aseuser1"`

    구성 파일을 만든 후에는 클러스터에 대한 물리적 액세스 권한이 필요하지 않습니다. 클라이언트에서 Azure Stack Edge Pro 디바이스 IP를 ping 할 수 있는 경우 *kubectl* 명령을 사용하여 클러스터를 보낼 수 있습니다.

6. 클라이언트에서 새 PowerShell 세션을 시작합니다. 디바이스 인터페이스에 연결하지 않아도 됩니다. 이제 다음 명령을 사용하여 클라이언트에 `kubectl`을 설치할 수 있습니다.

    ```powershell
    PS C:\windows\system32> curl https://storage.googleapis.com/kubernetes-release/release/v1.15.2/bin/windows/amd64/kubectl.exe -O kubectl.exe
    
    PS C:\windows\system32>
    ```
    예를 들어 Kubernetes 마스터 노드가 v1.15.2를 실행하는 경우 클라이언트에 v1.15.2를 설치합니다.

    > [!IMPORTANT]
    > 마스터에서 부 버전이 두 개 이상 왜곡된 클라이언트를 다운로드합니다. 그러나 클라이언트 버전은 최대 하나의 부 버전으로 마스터를 이끌 수 있습니다. 예를 들어 v1.3 마스터는 v1.1, v1.2 및 v1.3 노드와 함께 사용해야 하며, v1.2, v1.3 및 v1.4 클라이언트에서 작동해야 합니다. Kubernetes client 버전에 대한 자세한 내용은 [Kubernetes 버전 및 버전 차이 지원 정책](https://kubernetes.io/docs/setup/release/version-skew-policy/#supported-version-skew)을 참조하세요. Azure Stack Edge Pro의 Kubernetes 서버 버전에 대한 자세한 내용은 Kubernetes 서버 버전 가져오기를 참조하세요.<!-- insert link-->
    > Windows용 Docker 또는 다른 도구를 실행 중인 경우 시스템에 `kubectl`가 미리 설치 되어 있는 경우가 있습니다. 이 kubernetes 클러스터를 사용하려면 이 섹션에서 설명한 대로 `kubectl`의 특정 버전을 다운로드해야 합니다. 

    설치는 몇 분 정도 걸립니다.

7. 설치한 버전이 다운로드한 버전인지 확인합니다. 시스템에 `kubectl.exe`가 설치된 위치에 대한 절대 경로를 지정해야 합니다.
    
    ```powershell
    PS C:\Users\myuser> C:\windows\system32\kubectl.exe version
    Client Version: version.Info{Major:"1", Minor:"15", GitVersion:"v1.15.2", GitCommit:"f6278300bebbb750328ac16ee6dd3aa7d3549568", GitTreeState:"clean", BuildDate:"2019-08-05T09:23:26Z", GoVersion:"go1.12.5", Compiler:"gc", Platform:"windows/amd64"}
    Server Version: version.Info{Major:"1", Minor:"15", GitVersion:"v1.15.1", GitCommit:"4485c6f18cee9a5d3c3b4e523bd27972b1b53892", GitTreeState:"clean", BuildDate:"2019-07-18T09:09:21Z", GoVersion:"go1.12.5", Compiler:"gc", Platform:"linux/amd64"}
    PS C:\Users\myuser>
    ``` 
    
    Kubernetes 클러스터를 관리하는 데 사용되는 `kubectl` 명령에 대한 자세한 내용은 [kubectl 개요](https://kubernetes.io/docs/reference/kubectl/overview/)를 참조하세요.

8. 시스템의 호스트 파일에 DNS 항목을 추가합니다. 

    1. 관리자 권한으로 메모장을 실행하고 `hosts` 파일을 `C:\windows\system32\drivers\etc\hosts`에서 엽니다. 
    2. 로컬 UI의 **디바이스** 페이지에서 저장한 정보를 사용하여 호스트 파일에 항목을 만듭니다. 

        예를 들어 이 엔드포인트 `https://compute.asedevice.microsoftdatabox.com/[10.100.10.10]`을 복사하여 디바이스 IP 주소와 DNS 도메인으로 다음 항목을 만듭니다. 

        `10.100.10.10 compute.asedevice.microsoftdatabox.com`

9. Kubernetes pod에 연결할 수 있는지 확인하려면 다음을 입력합니다.
    
    ```powershell
    PS C:\Users\myuser> kubectl get pods -n "myasetest1"
    No resources found.
    PS C:\Users\myuser>
    ```
이제 네임스페이스에 애플리케이션을 배포한 다음, 해당 애플리케이션 및 로그를 볼 수 있습니다.

> [!IMPORTANT]   
> 실행할 수 없는 명령이 많이 있습니다(예: 관리자 액세스 권한이 필요한 명령). 네임스페이스에서 허용되는 작업만 수행할 수 있습니다.


## <a name="remove-kubernetes-cluster"></a>Kubernetes 클러스터 제거

Kubernetes 클러스터를 제거하려면 IoT Edge 구성을 제거해야 합니다.

자세한 지침은 [IoT Edge 구성 관리](azure-stack-edge-gpu-manage-compute.md#manage-iot-edge-configuration)를 참조하세요.
   

## <a name="next-steps"></a>다음 단계

- [Azure Stack Edge Pro에 상태 비저장 애플리케이션을 배포합니다.](azure-stack-edge-gpu-deploy-stateless-application-kubernetes.md)
