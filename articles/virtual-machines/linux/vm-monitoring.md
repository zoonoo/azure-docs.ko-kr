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
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: 9b2fe579113d6ca6bfd27d82eb9d4657657d44ff
ms.contentlocale: ko-kr
ms.lasthandoff: 05/25/2017

---
# <a name="enable-or-disable-azure-vm-monitoring"></a>Azure VM 모니터링을 사용하거나 사용하지 않도록 설정

이 섹션에서는 Azure에서 실행 중인 가상 컴퓨터에서 모니터링을 사용하거나 사용하지 않도록 설정하는 방법을 설명합니다. 포털 또는 Mac, Linux 및 Windows(Azure CLI)용 Azure 명령줄 인터페이스를 사용하여 모니터링을 사용하거나 사용하지 않도록 설정할 수 있습니다.

> [!IMPORTANT]
> 이 문서에서는 사용되지 않는 Linux 진단 확장 2.3 버전에 대해 설명합니다. 버전 2.3은 2018년 6월 30일까지 지원됩니다.
>
> 대신 Linux 진단 확장 3.0 버전을 사용할 수 있습니다. 자세한 내용은 [문서](./diagnostic-extension.md)를 참조하세요.

## <a name="enable--disable-monitoring-through-the-azure-portal"></a>Azure Portal을 통해 모니터링 사용/사용 안 함

1분 동안 인스턴스에 대한 데이터를 제공하는 Azure VM의 모니터링을 사용하도록 설정할 수 있습니다. (저장소 변경 적용). 그러면 포털 그래프 또는 API를 통해 VM에 대한 자세한 진단 데이터를 사용할 수 있습니다. 기본적으로 Azure Portal은 제한된 메트릭 집합의 호스트 기반 모니터링을 사용하도록 설정할 수 있습니다. VM이 실행 중이거나 중지된 상태일 때 VM 내에서 메트릭 모니터링을 사용하도록 설정할 수 있습니다.

* [https://portal.azure.com](https://portal.azure.com)에서 Azure Portal을 엽니다.
* 왼쪽 탐색 모음에서 가상 컴퓨터를 클릭합니다.
* 가상 컴퓨터 목록에서 실행 중이거나 중지된 인스턴스를 선택합니다. "가상 컴퓨터" 블레이드가 열립니다.
* 모든 설정을 클릭합니다
* 진단을 클릭합니다.
* 상태를 켜짐 또는 꺼짐으로 변경합니다. 가상 컴퓨터에 대해 사용하도록 설정할 모니터링 세부 수준을 이 블레이드에서 선택할 수도 있습니다.

![Azure Portal을 통해 모니터링 사용/사용 안 함][1]

## <a name="enable--disable-monitoring-with-azure-cli"></a>Azure CLI를 사용하는 모니터링 사용/사용 안 함

Azure VM에 대한 모니터링을 사용하도록 설정하려면

* PrivateConfig.json과 같은 파일을 만듭니다.

```json
{
        "storageAccountName":"the storage account to receive data",
        "storageAccountKey":"the key of the account"
}
```

* Azure CLI를 통해 확장을 사용하도록 설정합니다.

```azurecli
azure vm extension set myvm LinuxDiagnostic Microsoft.OSTCExtensions 2.3 --private-config-path PrivateConfig.json
```

자세한 내용은 [Linux 진단 확장을 사용하여 Linux VM의 성능 및 진단 데이터 모니터링](classic/diagnostic-extension-v2.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)을 참조하세요.

<!--Image references-->
[1]: ./media/vm-monitoring/portal-enable-disable.png

