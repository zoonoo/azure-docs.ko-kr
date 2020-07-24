---
title: CloudSimple의 azure VMware 솔루션-Azure에서 사설 클라우드 Vm 관리
description: 디스크 추가, VM 용량 변경, 네트워크 인터페이스 추가 등 Azure Portal에서 CloudSimple 사설 클라우드 Vm을 관리 하는 방법을 설명 합니다.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 82aa1ba6ec20e8242568a2725899ddc515a4dc0f
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87082072"
---
# <a name="manage-your-cloudsimple-private-cloud-virtual-machines-in-azure"></a>Azure에서 CloudSimple 사설 클라우드 가상 머신 관리

[CloudSimple 사설 클라우드에 대해 만든](azure-create-vm.md)가상 컴퓨터를 관리 하려면 [Azure Portal](https://portal.azure.com)에 로그인 합니다. 가상을 검색 하 고 선택 합니다 ( **모든 서비스** 에서 검색 또는 측면 메뉴의 **Virtual Machines** ).

## <a name="control-virtual-machine-operation"></a>가상 컴퓨터 작업 제어

선택한 가상 컴퓨터에 대 한 **개요** 페이지에서 다음 컨트롤을 사용할 수 있습니다.

| 제어 | Description |
| ------------ | ------------- |
| 연결 | 지정 된 VM에 연결 합니다.  |
| 시작 | 지정 된 VM을 시작 합니다.  |
| 다시 시작 | 지정 된 VM을 종료 하 고 전원을 켭니다.  |
| 중지 | 특정 VM을 종료 합니다.  |
| 캡처 | 다른 Vm을 만드는 이미지로 사용할 수 있도록 지정 된 VM의 이미지를 캡처합니다. [Azure에서 일반화 된 VM의 관리 이미지 만들기](../virtual-machines/windows/capture-image-resource.md)를 참조 하세요.   |
| 이동 | 지정 된 VM으로 이동 합니다.  |
| 삭제 | 지정 된 VM을 제거 합니다.  |
| 새로 고침 | 표시 되는 데이터를 새로 고칩니다.  |

### <a name="view-performance-information"></a>성능 정보 보기

**개요** 페이지의 아래쪽 영역에 있는 차트는 선택한 간격에 대 한 성능 데이터를 표시 합니다 (지난 1 시간에서 지난 30 일 사이). 기본값은 지난 1 시간입니다. 차트에서 커서를 앞뒤로 이동 하 여 간격 내에 모든 시간에 대 한 숫자 값을 표시할 수 있습니다.

다음 차트가 표시 됩니다.

| 항목 | 설명 |
| ------------ | ------------- |
| CPU (평균) | 선택한 간격 동안의 평균 CPU 사용률 (%)입니다.   |
| 네트워크 | 선택한 간격 동안 네트워크 들어오고 나가는 트래픽 (MB)  |
| 디스크 바이트 | 선택한 간격 동안 디스크에서 읽고 디스크에 쓴 총 데이터 (MB)입니다.  |
| 디스크 작업 | 선택한 간격 동안의 평균 디스크 작업 (작업/초)입니다. |

## <a name="manage-vm-disks"></a>VM 디스크 관리

VM 디스크를 추가 하려면 선택한 VM에 대 한 **디스크** 페이지를 엽니다. 디스크를 추가 하려면 **디스크 추가**를 클릭 합니다. 인라인 옵션을 입력 하거나 선택 하 여 다음 설정을 각각 구성 합니다. **저장**을 클릭합니다.

   | 항목 | 설명 |
   | ------------ | ------------- |
   | Name | 디스크를 식별하는 이름을 입력합니다.  |
   | Size | 사용 가능한 크기 중 하나를 선택합니다.  |
   | SCSI 컨트롤러 | SCSI 컨트롤러를 선택 하십시오. 사용 가능한 컨트롤러는 지원 되는 다른 운영 체제에 따라 달라 집니다.  |
   | Mode | 디스크가 스냅숏에 참여 하는 방식을 결정 합니다. 다음 옵션 중 하나를 선택합니다. <br> -독립적 영구: 디스크에 기록 된 모든 데이터는 영구적으로 기록 됩니다.<br> -독립적이 지 않으며, 가상 컴퓨터의 전원을 끄거나 다시 설정 하면 디스크에 기록 된 변경 내용이 삭제 됩니다.  이 모드를 사용 하면 항상 동일한 상태에서 VM을 다시 시작할 수 있습니다. 자세한 내용은 [VMware 설명서](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html)를 참조하세요. |

디스크를 삭제 하려면 디스크를 선택 하 고 **삭제**를 클릭 합니다.

## <a name="change-the-capacity-of-the-vm"></a>VM의 용량 변경

VM의 용량을 변경 하려면 선택한 VM에 대 한 **크기** 페이지를 엽니다. 다음 중 하나를 지정 하 고 **저장**을 클릭 합니다.

| 항목 | 설명 |
| ------------ | ------------- |
| 코어 수 | VM에 할당 된 코어 수입니다.  |
| 하드웨어 가상화 | 게스트 OS에 하드웨어 가상화를 노출 하는 확인란을 선택 합니다. Vmware 문서 [Vmware 하드웨어 지원 가상화 노출](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html)을 참조 하세요. |
| 메모리 크기 | VM에 할당할 메모리의 양을 선택 합니다.  

## <a name="manage-network-interfaces"></a>네트워크 인터페이스 관리(영문)

인터페이스를 추가 하려면 **네트워크 인터페이스 추가**를 클릭 합니다. 인라인 옵션을 입력 하거나 선택 하 여 다음 설정을 각각 구성 합니다. **저장**을 클릭합니다.

   | 제어 | Description |
   | ------------ | ------------- |
   | Name | 인터페이스를 식별하는 이름을 입력합니다.  |
   | 네트워크 | 사설 클라우드 vSphere 구성 된 네트워크 목록에서 선택 합니다.  |
   | 어댑터 | VM에 대해 구성 된 사용 가능한 형식 목록에서 vSphere 어댑터를 선택 합니다. 자세한 내용은 VMware 기술 자료 문서 [가상 컴퓨터에 대 한 네트워크 어댑터 선택](https://kb.vmware.com/s/article/1001805)을 참조 하십시오. |
   | 부팅 시 전원 켜기 | VM이 부팅되면 NIC 하드웨어를 사용하도록 설정할지 여부를 선택합니다. 기본값은 **사용**입니다. |

네트워크 인터페이스를 삭제 하려면 해당 인터페이스를 선택 하 고 **삭제**를 클릭 합니다.
