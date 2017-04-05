---
title: "3노드 Deis 클러스터 배포 | Microsoft Docs"
description: "이 문서에서는 Azure 리소스 관리자 템플릿을 사용하여 Azure에서 3노드 Deis 클러스터를 만드는 방법을 설명합니다."
services: virtual-machines-linux
documentationcenter: 
author: HaishiBai
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 5eb67eb7-95d4-461d-8eac-44925224ba5f
ms.service: virtual-machines-linux
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/24/2015
ms.author: hbai
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: c0fc55d24506f669a74d67a803304ebf0b53bf21
ms.lasthandoff: 03/29/2017


---
# <a name="deploy-and-configure-a-3-node-deis-cluster-in-azure"></a>Azure에서 3노드 Deis 클러스터 배포 및 구성
이 문서는 Azure에서 [Deis](http://deis.io/) 클러스터를 프로비전하는 과정을 단계별로 안내합니다. 새로 프로비전된 클러스터에서 배포 및 샘플 **이동** 응용 프로그램 확장에 필요한 인증서를 만드는 모든 단계를 다룹니다.

다음 다이어그램에서는 배포된 시스템의 아키텍처를 보여줍니다. 시스템 관리자는 **deis** 및 **deisctl**과 같은 Deis 도구를 사용하여 클러스터를 관리합니다. 연결은 클러스터의 멤버 노드 중 하나에 연결을 전달하는 Azure 부하 분산 장치를 통해 설정됩니다. 클라이언트도 부하 분산 장치를 통해 배포된 응용 프로그램을 액세스합니다. 이 경우 부하 분산 장치는 트래픽을 Deis 라우터 메시에 전달합니다. 이렇게 하면 트래픽을 클러스터에서 호스트되는 해당 Docker 컨테이너에 추가로 라우트합니다.

  ![배포된 Desis 클러스터의 아키텍처 다이어그램](media/virtual-machines-linux-deis-cluster/architecture-overview.png)

다음 단계를 실행하려면 다음이 필요 합니다.

* 활성 Azure 구독. 없는 경우 [azure.com](https://azure.microsoft.com/)에서 평가판을 얻을 수 있습니다.
* Azure 리소스 그룹을 사용할 회사 또는 학교 ID. 개인 계정이 있고 Microsoft ID로 로그인하는 경우 [개인 계정에서 회사 ID를 만들어야](virtual-machines-windows-create-aad-work-id.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)합니다.
* 클라이언트 운영 체제에 따라 [Azure PowerShell](/powershell/azureps-cmdlets-docs) 또는 [Mac, Linux 및 Windows용 Azure CLI](../cli-install-nodejs.md)
* [OpenSSL](https://www.openssl.org/). OpenSSL은 필요한 인증서를 생성하는 데 사용됩니다.
* [Git Bash](https://git-scm.com/)와 같은 Git 클라이언트
* 샘플 응용 프로그램을 테스트하려면 DNS 서버도 필요합니다. 모든 DNS 서버 또는 와일드 카드 A 레코드를 지원하는 서비스를 사용할 수 있습니다.
* Deis 클라이언트 도구를 실행할 컴퓨터. 로컬 컴퓨터 또는 가상 컴퓨터 중 하나를 사용할 수 있습니다. 거의 모든 Linux 배포판에서 이러한 도구를 실행할 수는 있지만 다음 지침은 Ubuntu를 사용합니다.

## <a name="provision-the-cluster"></a>클러스터 프로비전
이 섹션에서는 오픈 소스 리포지토리 [azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates)의 [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) 템플릿을 사용합니다. 우선 템플릿을 복사합니다. 그런 다음 인증에 대해 새 SSH 키 쌍을 만듭니다. 그런 다음 클러스터에 대해 새 식별자를 구성합니다. 마지막으로 클러스터를 프로비전하는 셸 스크립트 또는 PowerShell 스크립트를 사용합니다.

1. [https://github.com/Azure/azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates)리포지토리를 복제합니다.
   
        git clone https://github.com/Azure/azure-quickstart-templates
2. 템플릿 폴더로 이동:
   
        cd azure-quickstart-templates\deis-cluster-coreos
3. ssh-keygen를 사용하여 새 SSH 키 쌍 만들기:
   
        ssh-keygen -t rsa -b 4096 -c "[your_email@domain.com]"
4. 위의 개인 키를 사용하는 인증서 생성:
   
        openssl req -x509 -days 365 -new -key [your private key file] -out [cert file to be generated]
5. [https://discovery.etcd.io/new](https://discovery.etcd.io/new) 로 이동하여 다음과 같이 표시되는 새 클러스터 토큰을 생성합니다.
   
        https://discovery.etcd.io/6a28e078895c5ec737174db2419bb2f3
   <br />
   각 CoreOS 클러스터에 이 무료 서비스의 고유한 토큰이 있어야 합니다. 자세한 내용은 [CoreOS 설명서](https://coreos.com/docs/cluster-management/setup/cluster-discovery/) 를 참조하세요.
6. **cloud-config.yaml** 파일을 수정하여 기존 **검색** 토큰을 새 토큰으로 바꿉니다.
   
        #cloud-config
        ---
        coreos:
          etcd:
            # generate a new token for each unique cluster from https://discovery.etcd.io/new
            # uncomment the following line and replace it with your discovery URL
            discovery: https://discovery.etcd.io/3973057f670770a7628f917d58c2208a
        ...
7. **azuredeploy-parameters.json** 파일을 수정합니다. 4단계에서 만든 인증서를 텍스트 편집기에서 엽니다. `----BEGIN CERTIFICATE-----` 및 `-----END CERTIFICATE-----` 사이의 모든 텍스트를 **sshKeyData** 매개 변수로 복사합니다(모든 줄 바꿈 문자를 제거해야 함).
8. **newStorageAccountName** 매개 변수를 수정합니다. VM OS 디스크의 저장소 계정입니다. 이 계정 이름은 전역적으로 고유해야 합니다.
9. **publicDomainName** 매개 변수를 수정합니다. 이는 부하 분산 장치 공용 IP와 연결된 DNS 이름의 일부가 됩니다. 최종 FQDN은 *[이 매개 변수의 값]*.*[지역]*.cloudapp.azure.com 형식입니다. 예를 들어, 이름을 deishbai32로 지정하고 리소스 그룹이 미국 서부 지역인 경우 부하 분산 장치에 대해 최종 FQDN은 deishbai32.westus.cloudapp.azure.com이 됩니다.
10. 매개 변수 파일을 저장합니다. 그런 다음 Azure PowerShell을 사용하여 클러스터를 프로비전할 수 있습니다.
    
        .\deploy-deis.ps1 -ResourceGroupName [resource group name] -ResourceGroupLocation "West US" -TemplateFile
        .\azuredeploy.json -ParametersFile .\azuredeploy-parameters.json -CloudInitFile .\cloud-config.yaml
    
    또는 Azure CLI:
    
        ./deploy-deis.sh -n "[resource group name]" -l "West US" -f ./azuredeploy.json -e ./azuredeploy-parameters.json
        -c ./cloud-config.yaml  
11. 리소스 그룹이 프로비전되면 Azure 클래식 포털의 그룹에서 모든 리소스를 볼 수 있습니다. 다음 스크린샷에 표시된 것과 같이 리소스 그룹은 동일한 가용성 집합에 조인된 3개의 VM이 있는 가상 네트워크를 포함합니다. 또한 그룹은 관련 공용 IP가 있는 부하 분산 장치를 포함합니다.
    
    ![Azure 클래식 포털에서 프로비전된 리소스 그룹](media/virtual-machines-linux-deis-cluster/resource-group.png)

## <a name="install-the-client"></a>클라이언트 설치
Deis 클러스터를 제어하려면 **deisctl** 이 필요합니다. deisctl은 모든 클러스터 노드에서 자동으로 설치되지만 별도의 관리 컴퓨터에서 deisctl를 사용하는 것이 좋습니다. 또한 모든 노드는 개인 IP 주소만으로 구성되기 때문에, 공용 IP가 있는 부하 분산 장치를 통해 SSH 터널을 사용해야 노드 컴퓨터에 연결할 수 있습니다. Ubuntu 물리적 또는 가상 컴퓨터에서 deisctl을 설정하는 단계는 다음과 같습니다.

1. deisctl 설치:mkdir deis
   
        cd deis
        curl -sSL http://deis.io/deisctl/install.sh | sh -s 1.6.1
        sudo ln -fs $PWD/deisctl /usr/local/bin/deisctl
2. ssh 에이전트에 개인 키 추가:
   
        eval `ssh-agent -s`
        ssh-add [path to the private key file, see step 1 in the previous section]
3. Deisct 구성:
   
        export DEISCTL_TUNNEL=[public ip of the load balancer]:2223

템플릿은 2223을 인스턴스 1로, 2224를 인스턴스 2로, 2225를 인스턴스 3으로 매핑하는 인바운드 NAT 규칙을 정의합니다. 이는 deisctl 도구를 사용하는 것에 대한 중복성을 제공합니다. Azure 클래식 포털에서 이러한 규칙을 검사할 수 있습니다.

![부하 분산 장치에서 NAT 규칙](media/virtual-machines-linux-deis-cluster/nat-rules.png)

> [!NOTE]
> 현재 템플릿은 3 개 노드 클러스터만 지원합니다. 이것은 루프 구문을 지원하지 않는 Azure 리소스 관리자 템플릿 NAT 규칙 정의의 제한 사항 때문입니다.
> 
> 

## <a name="install-and-start-the-deis-platform"></a>Deis 플랫폼 설치 및 시작
이제 deisctl을 설치하고 Deis 플랫폼을 시작할 수 있습니다.

    deisctl config platform set domain=[some domain]
    deisctl config platform set sshPrivateKey=[path to the private key file]
    deisctl install platform
    deisctl start platform

> [!NOTE]
> 플랫폼을 시작하는 시간이 오래 걸립니다(10 분 정도). 특히, 작성기 서비스를 시작 하면 시간이 오래 걸릴 수 있습니다. 성공하기 위해 몇 번의 시도를 할 수도 있습니다. 작업이 중지하는 것처럼 보이는 경우 `ctrl+c`을 입력하여 명령의 실행을 중단하고 다시 시도해보십시오.
> 
> 

`deisctl list`를 사용하여 모든 서비스가 실행되고 있는지 확인할 수 있습니다.

    deisctl list
    UNIT                            MACHINE                 LOAD    ACTIVE          SUB
    deis-builder.service            ebe3005e.../10.0.0.6    loaded  active          running
    deis-controller.service         ebe3005e.../10.0.0.6    loaded  active          running
    deis-database.service           9c79bbdd.../10.0.0.5    loaded  active          running
    deis-logger.service             9c79bbdd.../10.0.0.5    loaded  active          running
    deis-logspout.service           8d658d5a.../10.0.0.4    loaded  active          running
    deis-logspout.service           9c79bbdd.../10.0.0.5    loaded  active          running
    deis-logspout.service           ebe3005e.../10.0.0.6    loaded  active          running
    deis-publisher.service          8d658d5a.../10.0.0.4    loaded  active          running
    deis-publisher.service          9c79bbdd.../10.0.0.5    loaded  active          running
    deis-publisher.service          ebe3005e.../10.0.0.6    loaded  active          running
    deis-registry@1.service         ebe3005e.../10.0.0.6    loaded  active          running
    deis-router@1.service           ebe3005e.../10.0.0.6    loaded  active          running
    deis-router@2.service           8d658d5a.../10.0.0.4    loaded  active          running
    deis-router@3.service           9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-daemon.service       8d658d5a.../10.0.0.4    loaded  active          running
    deis-store-daemon.service       9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-daemon.service       ebe3005e.../10.0.0.6    loaded  active          running
    deis-store-gateway@1.service    9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-metadata.service     8d658d5a.../10.0.0.4    loaded  active          running
    deis-store-metadata.service     9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-metadata.service     ebe3005e.../10.0.0.6    loaded  active          running
    deis-store-monitor.service      8d658d5a.../10.0.0.4    loaded  active          running
    deis-store-monitor.service      9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-monitor.service      ebe3005e.../10.0.0.6    loaded  active          running
    deis-store-volume.service       8d658d5a.../10.0.0.4    loaded  active          running
    deis-store-volume.service       9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-volume.service       ebe3005e.../10.0.0.6    loaded  active          running

축하합니다. 이제 Azure에서 Deis 클러스터를 실행할 수 있습니다! 다음, 작업에서 클러스터를 보려면 샘플 이동 응용 프로그램을 배포합니다.

## <a name="deploy-and-scale-a-hello-world-application"></a>Hello World 응용 프로그램 배포 및 확장
다음 단계는 "Hello World" 이동 응용 프로그램을 클러스터에 배포하는 방법을 보여줍니다. 단계는 [Deis 설명서](http://docs.deis.io/en/latest/using_deis/using-dockerfiles/#using-dockerfiles)를 기반으로 합니다.

1. 라우팅 메시를 제대로 작동하려면 부하 분산 장치의 공용 IP를 가리키는 도메인에 대한 와일드카드 A 레코드를 해야 합니다. 다음 스크린샷은 GoDaddy에서 샘플 도메인 등록에 대한 A 레코드를 보여줍니다.
   
    ![Godaddy A 레코드](media/virtual-machines-linux-deis-cluster/go-daddy.png)
   
   <p />
2. deis 설치:
   
        mkdir deis
        cd deis
        curl -sSL http://deis.io/deis-cli/install.sh | sh
        ln -fs $PWD/deis /usr/local/bin/deis
3. 새 SSH 키를 만든 다음 GitHub로 공용 키를 추가합니다(물론, 기존 키를 재사용할 수도 있습니다). 새 SSH 키 쌍을 만들려면 다음을 사용합니다.
   
        cd ~/.ssh
        ssh-keygen (press [Enter]s to use default file names and empty passcode)
4. GitHub로 id_rsa.pub 또는 사용자가 선택한 공용 키를 추가합니다. SSH 키 구성 화면에서 SSH 키 추가 단추를 사용하여 수행할 수 있습니다.
   
   ![GitHub 키](media/virtual-machines-linux-deis-cluster/github-key.png)
   
   <p />
5. 새 사용자 등록:
   
        deis register http://deis.[your domain]
   <p />
6. SSH 키 추가:
   
        deis keys:add [path to your SSH public key]
   <p />      
7. 응용 프로그램을 만듭니다.
   
        git clone https://github.com/deis/helloworld.git
        cd helloworld
        deis create
        git push deis master
   <p />
8. git push가 빌드 및 배포되도록 Docker 이미지를 트리거합니다. 이 작업은 시간이 몇 분 걸립니다. 경험으로는 보통 10단계(개인 저장소에 이미지 푸시하기)가 중단될 수 있습니다. 이런 경우가 발생하면 프로세스를 중지하고, `deis apps:destroy –a <application name>` to remove the application and try again. You can use `deis apps:list`를 사용하여 응용 프로그램을 제거하고 응용 프로그램의 이름을 찾아냅니다. 모두 작동하는 경우 명령 출력의 끝에 다음과 같이 나타납니다.
   
        -----> Launching...
               done, lambda-underdog:v2 deployed to Deis
               http://lambda-underdog.artitrack.com
               To learn more, use `deis help` or visit http://deis.io
        To ssh://git@deis.artitrack.com:2222/lambda-underdog.git
         * [new branch]      master -> master
   <p />
9. 응용 프로그램이 작동하고 있는지 확인합니다.
   
        curl -S http://[your application name].[your domain]
   다음과 같은 결과가 표시됩니다.
   
        Welcome to Deis!
        See the documentation at http://docs.deis.io/ for more information.
        (you can use geis apps:list to get the name of your application).
   <p />
10. 3개의 인스턴스로 응용 프로그램의 크기를 조정합니다.
    
        deis scale cmd=3
    <p />
11. 선택적으로 deis 정보를 사용하여 응용 프로그램의 세부 정보를 검사할 수 있습니다. 다음 출력은 응용 프로그램 배포에서 나옵니다.
    
        deis info
        === lambda-underdog Application
        {
          "updated": "2015-05-22T06:14:10UTC",
          "uuid": "10c74ee7-b7ff-4786-967a-7e65af7eabc3",
          "created": "2015-05-22T06:07:55UTC",
          "url": "lambda-underdog.artitrack.com",
          "owner": "haishi",
          "id": "lambda-underdog",
          "structure": {
            "cmd": 3
          }
        }
    
        === lambda-underdog Processes
        --- cmd:
        cmd.1 up (v2)
        cmd.2 up (v2)
        cmd.3 up (v2)
    
        === lambda-underdog Domains
        No domains

## <a name="next-steps"></a>다음 단계
이 문서는 Azure 리소스 관리자 템플릿을 사용하여 Azure에서 새 Deis 클러스터를 프로비전하는 모든 과정을 단계별로 안내합니다. 템플릿은 배포된 응용 프로그램에 대해 부하 분산 및 연결 도구에 중복성을 지원합니다. 템플릿은 또한 멤버 노드에서 공용 IP를 사용하는 것을 방지합니다. 이렇게 하면 중요한 공용 IP 리소스를 저장하고 호스트 응용 프로그램에 보다 안전한 환경을 제공합니다. 자세한 내용은 다음 문서를 참조하세요.

[Azure Resource Manager 개요][resource-group-overview]  
[Azure CLI를 사용하는 방법][azure-command-line-tools]  
[Azure Resource Manager로 Azure PowerShell 사용][powershell-azure-resource-manager]  

[azure-command-line-tools]: ../cli-install-nodejs.md
[resource-group-overview]: ../azure-resource-manager/resource-group-overview.md
[powershell-azure-resource-manager]: ../powershell-azure-resource-manager.md

