<properties
   pageTitle="Linux VM의 크기 조정 방법 | Microsoft Azure"
   description="VM 크기를 변경하여 Linux 가상 컴퓨터의 규모를 확장하거나 축소하는 방법"
   services="virtual-machines-linux"
   documentationCenter="na"
   authors="mikewasson"
   manager="timlt"
   editor=""
   tags=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/16/2016"
   ms.author="mikewasson"/>


# Linux VM의 크기 조정 방법

## 개요 

VM(가상 컴퓨터)을 프로비전한 후 [VM 크기][vm-sizes]를 변경하여 VM의 크기를 확장 또는 축소할 수 있습니다. 경우에 따라 먼저 VM의 할당을 취소해야 합니다. 이는 VM을 호스트하는 하드웨어 클러스터에서 새 크기를 사용할 수 없는 경우에 발생할 수 있습니다.

이 문서에서는 [Azure CLI][azure-cli]를 사용하여 Linux VM의 크기를 조정하는 방법을 보여 줍니다.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] 클래식 배포 모델.


## Linux VM 크기 조정 

VM의 크기를 조정하려면 다음 단계를 수행합니다.

1. 다음 CLI 명령을 실행합니다. 이 명령은 VM이 호스트되는 하드웨어 클러스터에서 사용할 수 있는 VM 크기를 나열합니다.

    ```
    azure vm sizes -g <resource-group> --vm-name <vm-name>
    ```

2. 원하는 크기가 목록에 나열된 경우 다음 명령을 실행하여 VM 크기를 조정합니다.

    ```
    azure vm set -g <resource-group> --vm-size <new-vm-size> -n <vm-name>  
        --enable-boot-diagnostics --boot-diagnostics-storage-uri
        https://<storage-account-name>.blob.core.windows.net/ 
    ```

    이 과정에서 VM이 다시 시작됩니다. 다시 시작한 후 기존의 OS 및 데이터 디스크는 다시 매핑됩니다. 임시 디스크에 있는 모든 내용이 손실됩니다.

    `--enable-boot-diagnostics` 옵션을 사용하면 [진단 부팅][boot-diagnostics]으로 시작과 관련된 모든 오류를 기록할 수 있습니다.

3. 그러지 않고 원하는 크기가 나열되지 않은 경우에는 다음 명령을 실행하여 VM의 할당을 취소하고 크기를 조정한 다음 VM을 다시 시작합니다.

    ```
    azure vm deallocate -g <resource-group> <vm-name>
    azure vm set -g <resource-group> --vm-size <new-vm-size> -n <vm-name>  
        --enable-boot-diagnostics --boot-diagnostics-storage-uri
        https://<storage-account-name>.blob.core.windows.net/ 
    azure vm start -g <resource-group> <vm-name>
    ```

   > [AZURE.WARNING] VM의 할당이 취소되면 VM에 할당된 모든 동적 IP 주소도 해제됩니다. OS 및 데이터 디스크는 영향을 받지 않습니다.
   
## 다음 단계

확장성을 높이기 위해서는 여러 VM 인스턴스를 실행하고 규모를 확장합니다. 자세한 내용은 [가상 컴퓨터 확장 집합에서 Linux 컴퓨터 자동 확장][scale-set]을 참조하세요.

<!-- links -->
   
[azure-cli]: ../xplat-cli-install.md
[boot-diagnostics]: https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/
[scale-set]: ../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md
[vm-sizes]: virtual-machines-linux-sizes.md

<!---HONumber=AcomDC_0824_2016-->