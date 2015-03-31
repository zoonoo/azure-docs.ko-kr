<properties
   pageTitle="Azure x-plat-cli를 사용하여 여러 VM 배포 만들기 | Azure"
   description="Azure x-plat-cli를 사용하여 여러 VM 배포를 만드는 방법에 대해 알아봅니다."
   services="virtual-machines"
   documentationCenter="nodejs"
   authors="AlanSt"
   manager="timlt"
   editor=""/>

   <tags
   ms.service="virtual-machines"
   ms.devlang="nodejs"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="02/20/2015"
   ms.author="alanst;kasing"/>

# Azure x-plat-cli를 사용하여 여러 VM 배포 만들기

다음 스크립트는 Azure 플랫폼 간 명령줄 인터페이스(xplat-cli)를 사용하여 VNET에서 여러 VM/다중 클라우드 서비스 배포를 구성하는 방법을 보여 줍니다.

아래 이미지에서는 스크립트 완료 후 표시되는 배포를 보여 줍니다.

![](./media/virtual-machines-create-multi-vm-deployment-xplat-cli/multi-vm-xplat-cli.png)

이 스크립트는 클라우드 서비스 **servercs**에 데이터 디스크 2개가 연결된 VM 1개(**servervm**)를 만들고 클라우드 서비스 **workercs**에 VM 2개(**clientvm1, clientvm2**)를 만듭니다. 두 클라우드 서비스는 모두 VNET **samplevnet**에 배치됩니다. **servercs** 클라우드 서비스에는 외부 연결용 끝점도 구성되어 있습니다.

## 작업 수행용 CLI 스크립트
위에서 설명한 배포를 설정하는 코드는 비교적 단순합니다.

>[AZURE.NOTE] 클라우드 서비스 이름인 servercs 및 workercs는 고유한 클라우드 서비스 이름으로 변경해야 합니다.

    azure network vnet create samplevnet -l "West US"
    azure vm create -l "West US" -w samplevnet -e 10000 -z Small -n servervm servercs b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_10-amd64-server-20150202-ko-kr-30GB azureuser Password@1
    azure vm create -l "West US" -w samplevnet -e 10001 -z Small -n clientvm1 clientcs b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_10-amd64-server-20150202-ko-kr-30GB azureuser Password@1
    azure vm create -l "West US" -w samplevnet -e 10002 -c -z Small -n clientvm2 clientcs b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_10-amd64-server-20150202-ko-kr-30GB azureuser Password@1
    azure vm disk attach-new servervm 100
    azure vm disk attach-new servervm 500
    azure vm endpoint create servervm 443 443 -n https -o tcp

해당 배포를 삭제하는 코드도 마찬가지로 단순합니다.

    azure vm delete -b -q servervm
    azure vm delete -b -q clientvm1
    azure vm delete -b -q clientvm2
    azure network vnet delete -q samplevnet

*-q 옵션을 사용하는 경우 개체 삭제를 위한 대화형 확인을 표시하지 않습니다. -b를 사용하면 VM과 연결된 디스크/Blob을 정리합니다.*

## 사용되는 명령의 제네릭 형식

모든 Azure CLI 명령에서 -help 옵션을 사용하면 추가 정보를 확인할 수 있습니다. 위에서 사용한 것과 같은 각 명령의 제네릭 형식은 다음과 같습니다.

    azure network vnet create -l <Region> <VNet_name>
    azure network vnet delete -q <VNet_name>

    azure vm create -l <Region> -w <Vnet_name> -e <SSH_port> -z <VM_size> -n <VM_name> <Cloud_service_name> <VM_image> <Username> <Password>
    azure vm delete -b -q <VM_name>
    azure vm disk attach-new <VM_name>
    azure vm endpoint create <VM_name> <External_port> <Internal_port> -n <Endpoint_name> -o <TCP/UDP>

## 다음 단계

 
* [Azure의 오픈 소스 컴퓨팅 및 Linux](../virtual-machines-linux-opensource/)
* [Linux를 실행하는 가상 컴퓨터에 로그온하는 방법](../virtual-machines-linux-how-to-log-on/)

<!--HONumber=47-->
