---
title: Azure IoT Edge for Linux on Windows의 중첩된 가상화 | Microsoft Docs
description: Azure IoT Edge for Linux on Windows의 중첩된 가상화를 탐색하는 방법에 대해 알아봅니다.
author: fcabrera
manager: kgremban
ms.author: fcabrera
ms.date: 2/24/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 0e0021ec3564ca079f9ab02fe5ed3f0cfa5a1560
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104609658"
---
# <a name="nested-virtualization-for-azure-iot-edge-for-linux-on-windows"></a>Azure IoT Edge for Linux on Windows의 중첩된 가상화
Azure IoT Edge for Linux on Windows와 호환되는 중첩된 가상화에는 두 가지 형식이 있습니다. 사용자는 로컬 VM이나 Azure VM을 통한 배포를 선택할 수 있습니다. 이 문서에서는 사용자에게 시나리오에 가장 적합한 옵션을 명확하게 설명하고 구성 요구 사항에 대한 인사이트를 제공합니다.

> [!NOTE]
>
> 중첩된 가상화에 대해 하나의 [네트워킹 옵션](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization#networking-options)을 사용하도록 설정해야 합니다. 그러지 못하면 EFLOW 설치 오류가 발생합니다. 

## <a name="deployment-on-local-vm"></a>로컬 VM에 배포
이는 Azure IoT Edge for Linux on Windows를 호스팅하는 모든 Windows VM에 대한 기본 방법입니다. 이 경우 배포를 시작하기 전에 중첩된 가상화를 사용하도록 설정해야 합니다. 이 시나리오를 구성하는 방법에 대한 자세한 내용은 [중첩된 가상화를 사용하는 가상 머신에서 Hyper-V 실행](https://docs.microsoft.com/virtualization/hyper-v-on-windows/user-guide/nested-virtualization)을 참조하세요.

Windows Server를 사용하는 경우 [Hyper-V 역할을 설치](https://docs.microsoft.com/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server)해야 합니다.

## <a name="deployment-on-azure-vms"></a>Azure VM에 배포
Azure VM에 배포하도록 선택하는 경우에는 설계상 외부 스위치가 없습니다. Azure IoT Edge for Linux on Windows는 기본 스위치를 표시하는 특정 스크립트가 실행되지 않는 한 서버 SKU를 실행하는 Azure VM 에서도 호환되지 않습니다. 기본 스위치를 가져오는 방법에 대한 자세한 내용은 아래의 [Windows Server 섹션](#windows-server)을 참조하세요. 

> [!NOTE]
>
> EFLOW를 호스트해야 하는 모든 Azure VM은 중첩된 가상화를 [지원하는](../virtual-machines/acu.md) VM이어야 합니다.


## <a name="limited-use-of-external-switch"></a>외부 스위치 사용 제한
VM이 외부 스위치를 통해 IP 주소를 가져올 수 없는 경우 배포 기능에서 자동으로 배포에 대한 내부 기본 스위치를 사용합니다. 즉, Linux VM에 대한 Azure IoT Edge 관리는 대상 디바이스 자체에서만 실행할 수 있습니다. 즉, WAC PowerShell SSH 확장을 통한 Azure IoT Edge Linux VM 연결은 localhost 에서만 가능합니다.

## <a name="windows-server"></a>Windows Server
Windows Server 사용자의 경우 Azure IoT Edge for Linux on Windows는 기본 스위치를 자동으로 지원하지 않음에 유의하십시오.

* 로컬 VM에 대한 결과: EFLOW VM이 외부 스위치를 통해 IP 주소를 가져올 수 있는지 확인합니다.

* Azure VM에 대한 결과: Azure VM에 외부 스위치가 없기 때문에 서버에서 내부 스위치를 설정하기 전에 EFLOW를 배포할 수 없습니다.

서버 SKU가 Azure VM에서 실행되는지 여부와 관계 없이 기본적으로 서버 SKU에는 기본 스위치가 없습니다. 외부 스위치를 사용할 수 없는 Azure VM에 배포하는 경우 Azure IoT Edge for Linux on Windows를 배포하기 전에 기본 스위치를 수동으로 설정하고 구성해야 합니다. 이러한 배포 기능은 내부 스위치, NAT 구성 및 DHCP 서버 설치 및 구성에 대한 IP 구성이 필요하다는 사실로 다룹니다. 공개 미리 보기의 배포 기능을 사용하는 경우 이러한 설정과 관련하여 생산적인 배포에 대한 네트워크 구성을 저하하지 않습니다.

* 기본 스위치를 수동으로 설정하는 방법에 대한 관련 정보는 [Azure Virtual Machines에서 중첩된 가상화를 사용하는 방법](https://docs.microsoft.com/azure/virtual-machines/windows/nested-virtualization)에서 찾을 수 있습니다.
* 이 시나리오에 대한 DHCP 서버를 설정하는 방법에 대한 설명서는 [Windows PowerShell을 사용하여 DHCP 배포](https://docs.microsoft.com/windows-server/networking/technologies/dhcp/dhcp-deploy-wps)에서 찾을 수 있습니다.
