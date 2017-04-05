---
title: "Azure VM 모니터링 사용 또는 사용 안 함"
description: "Azure VM 모니터링을 사용하거나 사용하지 않도록 설정하는 방법을 설명합니다."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: kmouss
manager: timlt
editor: 
ms.assetid: 6ce366d2-bd4c-4fef-a8f5-a3ae2374abcc
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/08/2016
ms.author: kmouss
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 1f15a77304ccf4fdef995d216817363fbf3f6e73
ms.lasthandoff: 03/27/2017


---
# <a name="enable-or-disable-azure-vm-monitoring"></a>Azure VM 모니터링을 사용하거나 사용하지 않도록 설정
이 섹션에서는 Azure에서 실행 중인 가상 컴퓨터에서 모니터링을 사용하거나 사용하지 않도록 설정하는 방법을 설명합니다. 기본적으로 [Azure 포털](https://portal.azure.com) 에서 배포되면 Azure 가상 컴퓨터에서 모니터링이 사용되도록 설정되고, 기본적으로 1분 동안의 모니터링 그래프가 제공됩니다. 포털 또는 Mac, Linux 및 Windows(Azure CLI)용 Azure 명령줄 인터페이스를 사용하여 모니터링을 사용하거나 사용하지 않도록 설정할 수 있습니다. 

## <a name="enable--disable-monitoring-through-the-azure-portal"></a>Azure 포털을 통해 모니터링 사용/사용 안 함
1분 동안 인스턴스에 대한 데이터를 제공하는 Azure VM의 모니터링을 사용하도록 설정할 수 있습니다. (저장소 변경 적용). 그러면 포털 그래프 또는 API를 통해 VM에 대한 자세한 진단 데이터를 사용할 수 있습니다. 기본적으로 Azure 포털에서 모니터링이 사용되도록 설정되어 있지만 아래 설명에 따라 해제할 수 있습니다. VM이 실행 중이거나 중지된 상태일 때 모니터링을 사용하도록 설정할 수 있습니다.

* **[https://portal.azure.com](https://portal.azure.com)**에서 Azure Portal을 엽니다.
* 왼쪽 탐색 모음에서 가상 컴퓨터를 클릭합니다.
* 가상 컴퓨터 목록에서 실행 중이거나 중지된 인스턴스를 선택합니다. 가상 컴퓨터 블레이드가 열립니다.
* “모든 설정”을 클릭합니다
* “진단”을 클릭합니다.
* 상태를 켜짐 또는 꺼짐으로 변경합니다. 가상 컴퓨터에 대해 사용하도록 설정할 모니터링 세부 수준을 이 블레이드에서 선택할 수도 있습니다.

[Azure.Note] 진단 스위치는 새 가상 컴퓨터를 만들 때 기본적으로 켜짐 상태입니다.

![Azure 포털을 통해 모니터링 사용/사용 안 함][1]

## <a name="enable--disable-monitoring-with-azure-cli"></a>Azure CLI를 사용하는 모니터링 사용/사용 안 함
Azure VM에 대한 모니터링을 사용하도록 설정하려면

* 다음과 같은 내용으로 PrivateConfig.json과 같은 파일을 만듭니다.
        { "storageAccountName":"데이터를 수신할 저장소 계정", "storageAccountKey":"계정의 키" }
* 다음 Azure CLI 명령을 실행합니다.
  
        azure vm extension set myvm LinuxDiagnostic Microsoft.OSTCExtensions 2.0 --private-config-path PrivateConfig.json

[Azure.Note] 사용 가능한 경우 버전 2.0에서 이후 버전으로 변경할 수 있습니다. 

모니터링 메트릭 구성 및 샘플에 대한 자세한 내용은 **[Linux 진단 확장을 사용하여 Linux VM의 성능 및 진단 데이터 모니터링](linux/classic/diagnostic-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json) 문서를 참조하세요.

<!--Image references-->
[1]: ./media/virtual-machines-linux-vm-monitoring/portal-enable-disable.png



