---
title: Windows에서 Linux 용 Azure IoT Edge에 대 한 중첩 된 가상화 | Microsoft Docs
description: Windows에서 Linux 용 Azure IoT Edge의 중첩 된 가상화를 탐색 하는 방법에 대해 알아봅니다.
author: fcabrera
manager: kgremban
ms.author: fcabrera
ms.date: 2/24/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 0e0021ec3564ca079f9ab02fe5ed3f0cfa5a1560
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104609658"
---
# <a name="nested-virtualization-for-azure-iot-edge-for-linux-on-windows"></a>Windows에서 Linux 용 Azure IoT Edge에 대 한 중첩 된 가상화
Windows에서 Linux 용 Azure IoT Edge와 호환 되는 두 가지 형식의 중첩 된 가상화가 있습니다. 사용자는 로컬 VM 또는 Azure VM을 통해 배포 하도록 선택할 수 있습니다. 이 문서에서는 사용자가 시나리오에 가장 적합 한 옵션을 명확 하 게 설명 하 고 구성 요구 사항에 대 한 통찰력을 제공 합니다.

> [!NOTE]
>
> 중첩 된 가상화에 대해 하나의 [netowrking 옵션](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization#networking-options) 을 사용 하도록 설정 해야 합니다. 이 작업을 수행 하지 못하면 EFLOW 설치 오류가 발생 합니다. 

## <a name="deployment-on-local-vm"></a>로컬 VM에 배포
이는 Windows에서 Linux 용 Azure IoT Edge를 호스트 하는 모든 Windows VM에 대 한 기본 방법입니다. 이 경우 배포를 시작 하기 전에 중첩 된 가상화를 사용 하도록 설정 해야 합니다. 이 시나리오를 구성 하는 방법에 대 한 자세한 내용은 [중첩 된 가상화를 사용 하는 가상 머신에서 Hyper-v 실행](https://docs.microsoft.com/virtualization/hyper-v-on-windows/user-guide/nested-virtualization) 을 참조 하세요.

Windows Server를 사용 하는 경우 [hyper-v 역할을 설치](https://docs.microsoft.com/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server)해야 합니다.

## <a name="deployment-on-azure-vms"></a>Azure Vm에 배포
Azure Vm에 배포 하도록 선택 하는 경우에는 외부 스위치가 설계 되지 않습니다. Windows의 Linux에 대 한 Azure IoT Edge는 기본 스위치를 표시 하는 특정 스크립트가 실행 되지 않는 한 서버 SKU를 실행 하는 Azure VM 에서도 호환 되지 않습니다. 기본 스위치를 가져오는 방법에 대 한 자세한 내용은 아래의 [Windows Server 섹션](#windows-server) 을 참조 하세요. 

> [!NOTE]
>
> EFLOW를 호스트 해야 하는 모든 Azure Vm은 중첩 된 가상화를 [지 원하는](../virtual-machines/acu.md) VM 이어야 합니다.


## <a name="limited-use-of-external-switch"></a>외부 스위치 사용 제한
VM이 외부 스위치를 통해 IP 주소를 가져올 수 없는 경우 배포 기능에서 자동으로 배포에 대 한 내부 기본 스위치를 사용 합니다. 즉, Linux VM에 대 한 Azure IoT Edge 관리는 대상 장치 자체 에서만 실행할 수 있습니다. 즉, WAC PowerShell SSH 확장을 통해 Linux VM에 대 한 Azure IoT Edge에 연결 하는 것은 localhost 에서만 가능 합니다.

## <a name="windows-server"></a>Windows Server
Windows Server 사용자의 경우 Windows에서 Linux에 대 한 Azure IoT Edge 기본 스위치를 자동으로 지원 하지 않습니다.

* 로컬 VM에 대 한 결과: EFLOW VM이 외부 스위치를 통해 IP 주소를 가져올 수 있는지 확인 합니다.

* Azure VM에 대 한 결과: Azure vm에 외부 스위치가 없기 때문에 서버에서 내부 스위치를 설정 하기 전에 EFLOW를 배포할 수 없습니다.

서버 sku가 Azure VM에서 실행 되는지 여부에 관계 없이 기본적으로 서버 Sku에는 기본 스위치가 없습니다. 외부 스위치를 사용할 수 없는 Azure VM에서 배포 하는 경우 Windows 배포에서 Linux에 대 한 Azure IoT Edge 하기 전에 기본 스위치를 수동으로 설정 하 고 구성 해야 합니다. 이러한 배포 기능은 내부 스위치, NAT 구성 및 DHCP 서버 설치 및 구성에 대 한 IP 구성이 필요 하다는 것을 다룹니다. 공개 미리 보기의 배포 기능을 사용 하는 경우 이러한 설정과 관련 하 여 생산적인 배포에 대 한 네트워크 구성을 조작 하지 않습니다.

* 기본 스위치를 수동으로 설정 하는 방법에 대 한 관련 정보는 [Azure에서 중첩 된 가상화를 사용 하도록 설정 하는 방법](https://docs.microsoft.com/azure/virtual-machines/windows/nested-virtualization) 에서 찾을 수 있습니다 Virtual Machines
* 이 시나리오에 대 한 DHCP 서버를 설정 하는 방법에 대 한 설명서는 [Windows PowerShell을 사용 하 여 Dhcp 배포](https://docs.microsoft.com/windows-server/networking/technologies/dhcp/dhcp-deploy-wps) 에서 찾을 수 있습니다.
