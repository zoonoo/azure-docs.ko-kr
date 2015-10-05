<properties
   pageTitle="Azure CLI를 사용하여 Azure VM을 만드는 방법 | Microsoft Azure"
   description="이 항목에서는 모든 플랫폼에서 Azure CLI를 설치하고, Azure CLI를 사용하여 Azure 계정에 연결하고, Azure CLI에서 VM을 만드는 방법을 설명합니다."
   services="virtual-machines"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor="tysonn"
   tags="azure-service-management"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="command-line-interface"
   ms.workload="infrastructure-services"
   ms.date="06/09/2015"
   ms.author="danlep"/>

# Azure CLI(Azure 명령줄 인터페이스)를 사용하여 VM 만들기

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]이 문서에서는 클래식 배포 모델을 사용하여 리소스를 만드는 방법을 설명합니다. [리소스 관리자 배포 모델](virtual-machines-deploy-rmtemplates-azure-cli.md)을 사용하여 리소스를 만들 수도 있습니다.

Azure CLI를 사용하면 어떤 플랫폼에서나 Azure 인프라를 효율적으로 관리할 수 있습니다.

그러나 Azure CLI를 설치하고 Azure 구독만 생성한다고 해서 VM을 즉시 만들 수는 없습니다. 따라서 이 문서에서는 VM을 만드는 단계를 설명합니다. Azure 계정이 없으면 [무료 계정](http://azure.microsoft.com/pricing/free-trial/)을 만드세요.

## Azure CLI 설치

[Azure CLI 설치](../xplat-cli.md#install) 지침을 따르세요.

## Azure CLI를 사용하여 Azure에 연결

Azure CLI 설치를 개인 Azure 계정이나 회사 또는 학교 Azure 계정에 연결할 수 있습니다. 각 계정 사용 시의 차이점과 선택 가능한 항목을 확인하려면 [Azure 구독에 연결하는 방법](../xplat-cli.md#configure)을 참조하세요.

## Azure에서 VM 작성 및 연결

VM을 만들 때는 먼저 이미지를 선택하거나 업로드하고 `azure vm create` 명령을 실행합니다.

1. 명령줄에서 이미지를 선택하려는 경우 `azure vm image list` 명령을 사용하여 사용 가능한 VM 이미지를 나열할 수 있습니다. 이미지가 매우 많으면 `more`를 사용하여 결과 페이지를 이동하거나 `grep`(Linux) 또는 `findstr`(Windows)을 사용하여 결과를 필터링할 수 있습니다. 예를 들어 Linux의 Ubuntu 이미지를 찾는 경우 다음과 같은 명령을 사용합니다.

        azure vm image list | grep Ubuntu

    그러나 이렇게 하더라도 긴 이미지 목록이 생성되기 때문에 버전을 기준으로 결과의 범위를 더 좁힐 수 있습니다.

        azure vm image list | grep Ubuntu-14_10

    여기서 이미지를 선택한 다음 `show` 명령을 사용하여 해당 속성을 보다 자세하게 확인할 수 있습니다.

        azure vm image show b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_10-amd64-server-20150202-ko-KR-30GB

2. VM 이미지를 선택한 다음 `vm create` 명령을 사용하여 이미지를 만듭니다. 이 명령에는 많은 옵션이 있으며, `help` 명령을 사용하면 목록을 표시할 수 있습니다.

        vm create --help

    1단계의 이미지와 함께 VM을 만드는 데 필요한 주요 인수는 위치, DNS 이름, 사용자 이름입니다.

    인증에 대해서는 명령줄에서 또는 대화형으로 암호를 지정할 수도 있고 인증서를 사용하여 인증할 수도 있습니다. 암호를 사용하도록 선택하는 경우 해당 암호는 8자 이상이어야 하고 대문자, 소문자, 특수 문자(예: !@#$%^&+= 중 하나)를 모두 포함해야 합니다. 암호를 명령줄에서 전달하는 경우에는 따옴표로 묶고 특수 문자를 이스케이프 처리하는 것이 좋습니다.

    위치를 선택하려면 `vm location list` 명령을 사용하여 인근 지역을 선택할 수 있습니다.

  선택한 DNS 이름은 고유해야 하며(이 이름은 `dnsname.cloudapp.net`에 매핑됨), 명령줄에서 컴퓨터 이름을 별도로 지정하지 않는 경우에는 컴퓨터 이름과 같아야 합니다.

    The Linux example below creates a VM in West US, opens the default SSH port 22 (the -e argument), and creates a user called `myadminuser`:

        azure vm create -e -l "West US"  my-new-cli-vm b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_10-amd64-server-20150202-ko-KR-30GB "myadminuser" "myAdm1n@passwd"

## 다음 단계

VM에서 작업을 수행해 봅니다.

위의 예제에서는 기본 SSH 포트를 열었으므로 VM이 작동 및 실행되고 나면 쉽게 VM에 연결할 수 있습니다. Linux 명령줄에서 다음 명령을 실행합니다.

    ssh myadminuser@my-new-cli-vm.cloudapp.net

[Azure CLI 명령 참조 페이지](../virtual-machines-command-line-tools.md)에서 Azure CLI를 사용하여 Azure 인프라를 관리하는 방법에 대한 더 많은 예제를 확인할 수 있습니다.

<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png

<!---HONumber=Sept15_HO4-->