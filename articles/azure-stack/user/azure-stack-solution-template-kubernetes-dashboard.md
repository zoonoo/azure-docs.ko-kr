---
title: Azure Stack에서 Kubernetes 대시보드 액세스 | Microsoft Docs
description: Azure Stack에서 Kubernetes 대시보드에 액세스 하는 방법 알아보기
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 02/27/2019
ms.openlocfilehash: 4e9df0d413b964b4a14cf9ca48db8b7956b441f9
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58482592"
---
# <a name="access-the-kubernetes-dashboard-in-azure-stack"></a>Azure Stack의 Kubernetes 대시보드에 액세스 

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트* 
> [!Note]   
> Azure Stack에서 Kubernetes 미리 보기입니다. Azure Stack 연결이 끊긴된 시나리오는 미리 보기에서 현재 지원 되지 않습니다. 

Kubernetes는 기본 관리 작업에 사용할 수 있는 웹 대시보드를 포함 합니다. 이 대시보드를 사용하면 애플리케이션의 기본 상태와 메트릭을 보고 서비스를 작성 및 배포하며 기존 애플리케이션을 편집할 수 있습니다. 이 문서에서는 Azure Stack에서 Kubernetes 대시보드를 설정 하는 방법을 보여 줍니다.

## <a name="prerequisites-for-kubernetes-dashboard"></a>Kubernetes 대시보드에 대 한 필수 구성 요소

* Azure Stack Kubernetes 클러스터

    Azure Stack에 Kubernetes 클러스터를 배포 해야 합니다. 자세한 내용은 [Kubernetes 배포](azure-stack-solution-template-kubernetes-deploy.md)합니다.

* SSH 클라이언트

    보안에 SSH 클라이언트가 필요 합니다. 클러스터의 마스터 노드를 연결 합니다. Windows를 사용 하는 경우 사용할 수 있습니다 [Putty](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-connect-vm)합니다. Kubernetes 클러스터를 배포할 때 사용 되는 개인 키가 필요 합니다.

* FTP (PSCP)

    Azure Stack 관리 컴퓨터를 마스터 노드에서 인증서를 전송 하는 SSH 및 SSH 파일 전송 프로토콜을 지 원하는 FTP 클라이언트를 할 수도 있습니다. 사용할 수 있습니다 [FileZilla](https://filezilla-project.org/download.php?type=client)합니다. Kubernetes 클러스터를 배포할 때 사용 되는 개인 키가 필요 합니다.

## <a name="overview-of-steps-to-enable-dashboard"></a>대시보드를 사용 하도록 설정 하는 단계 개요

1.  클러스터의 마스터 노드를 통해 Kubernetes 인증서를 내보냅니다. 
2.  관리 컴퓨터를를 Azure Stack 인증서를 가져옵니다.
2.  Kubernetes 웹 대시보드를 엽니다. 

## <a name="export-certificate-from-the-master"></a>마스터에서 인증서 내보내기 

클러스터의 마스터 노드에서 대시보드에 대 한 URL를 검색할 수 있습니다.

1. Azure Stack 대시보드에서 클러스터 마스터에 대 한 사용자 이름을 확인 하 고 공용 IP 주소를 가져옵니다. 이 정보를 얻으려면:

    - 에 로그인 하 여 [Azure Stack 포털](https://portal.local.azurestack.external/)
    - 선택 **모든 서비스** > **리소스를 모두**입니다. 클러스터 리소스 그룹에 마스터를 찾습니다. 마스터 이름은 `k8s-master-<sequence-of-numbers>`합니다. 

2. 포털의 마스터 노드를 엽니다. 복사 합니다 **공용 IP** 주소입니다. 클릭 **Connect** 사용자 이름을 가져오려고 합니다 **VM 로컬 계정을 사용 하 여 로그인** 상자입니다. 이 클러스터를 만들 때 설정한 동일한 사용자 이름입니다. 연결 블레이드에서 나열 된 개인 IP 주소 대신 공용 IP 주소를 사용 합니다.

3.  마스터에 연결할 SSH 클라이언트를 엽니다. Windows에서 작업할 경우 사용할 수 있습니다 [Putty](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-connect-vm) 연결을 만듭니다. 공용 IP 주소를 사용 하 여 마스터 노드의 사용자 이름 및 클러스터를 만들 때 사용 되는 개인 키를 추가 합니다.

4.  터미널에 연결 하는 경우 입력 `kubectl` 하려면 Kubernetes 명령줄 클라이언트를 엽니다.

5. 다음 명령 실행:

    ```Bash   
    kubectl cluster-info 
    ``` 
    대시보드에 대 한 URL을 찾습니다. 예: `https://k8-1258.local.cloudapp.azurestack.external/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy`

6.  자체 서명 된 인증서를 추출 및 PFX 형식으로 변환 합니다. 다음 명령 실행:

    ```Bash  
    sudo su 
    openssl pkcs12 -export -out /etc/kubernetes/certs/client.pfx -inkey /etc/kubernetes/certs/client.key  -in /etc/kubernetes/certs/client.crt -certfile /etc/kubernetes/certs/ca.crt 
    ```

7.  암호의 목록을 가져오려면 합니다 **kube 시스템** 네임 스페이스입니다. 다음 명령 실행:

    ```Bash  
    kubectl -n kube-system get secrets
    ```

    Kubernetes를 기록해-대시보드-토큰-\<XXXXX > 값입니다. 

8.  토큰을 가져와 저장 합니다. 업데이트 된 `kubernetes-dashboard-token-<####>` 이전 단계에서 비밀 값을 사용 하 여 합니다.

    ```Bash  
    kubectl -n kube-system describe secret kubernetes-dashboard-token-<####>| awk '$1=="token:"{print $2}' 
    ```

## <a name="import-the-certificate"></a>인증서 가져오기

1. Filezilla를 열고 마스터 노드에 연결 합니다. 해야 합니다.

    - 마스터 노드 공용 IP
    - 사용자 이름
    - 개인 암호
    - 사용 하 여 **SFTP-SSH 파일 전송 프로토콜**

2. 복사본 `/etc/kubernetes/certs/client.pfx` 고 `/etc/kubernetes/certs/ca.crt` Azure Stack 관리 컴퓨터에 있습니다.

3. 파일 위치를 적어 둡니다. 위치를 사용 하 여 스크립트를 업데이트 하 고 PowerShell 프롬프트를 사용 하 여 엽니다. 업데이트 된 스크립트를 실행 합니다.  

    ```powershell   
    Import-Certificate -Filepath "ca.crt" -CertStoreLocation cert:\LocalMachine\Root 
    $pfxpwd = Get-Credential -UserName 'Enter password below' -Message 'Enter password below' 
    Import-PfxCertificate -Filepath "client.pfx" -CertStoreLocation cert:\CurrentUser\My -Password $pfxpwd.Password 
    ``` 

## <a name="open-the-kubernetes-dashboard"></a>Kubernetes 대시보드 열기 

1. 웹 브라우저에서 팝업 차단을 사용 하지 않도록 설정 합니다.

2. URL로 브라우저가 명령을 실행할 때 표시 되는 지점 `kubectl cluster-info`합니다. 예: https:\//azurestackdomainnamefork8sdashboard/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard: / 프록시 
3. 클라이언트 인증서를 선택 합니다.
4. 토큰을 입력 합니다. 
5. 마스터 노드에서 bash 명령줄에 다시 연결 하 고 권한을 부여 `kubernetes-dashboard`합니다. 다음 명령 실행:

    ```Bash  
    kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard 
    ``` 

    스크립트는 제공 `kubernetes-dashboard` 클라우드 관리자 권한이 있습니다. 자세한 내용은 [클러스터에 대 한 RBAC 지원](https://docs.microsoft.com/azure/aks/kubernetes-dashboard)합니다.

대시보드를 사용할 수 있습니다. Kubernetes 대시보드에 대 한 자세한 내용은 참조 하세요. [Kubernetes 웹 UI 대시보드](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/) 

![Azure Stack Kubernetes 대시보드](media/azure-stack-solution-template-kubernetes-dashboard/azure-stack-kub-dashboard.png)

## <a name="next-steps"></a>다음 단계 

[Azure Stack에 Kubernetes 배포](azure-stack-solution-template-kubernetes-deploy.md)  

[(Azure Stack 운영자)에 대 한 Marketplace에 Kubernetes 클러스터를 추가](../azure-stack-solution-template-kubernetes-cluster-add.md)  

[Azure의 Kubernetes](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)  
