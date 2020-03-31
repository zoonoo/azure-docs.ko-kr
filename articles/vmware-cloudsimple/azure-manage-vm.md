---
title: 클라우드간이별 Azure VMware 솔루션 - Azure에서 프라이빗 클라우드 VM 관리
description: 디스크 추가, VM 용량 변경 및 네트워크 인터페이스 추가를 포함하여 Azure 포털에서 CloudSimple 프라이빗 클라우드 VM을 관리하는 방법에 대해 설명합니다.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 44a0b8fe56477620c0ac47d5c5de8830dac46214
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77015000"
---
# <a name="manage-your-cloudsimple-private-cloud-virtual-machines-in-azure"></a>Azure에서 CloudSimple 프라이빗 클라우드 가상 머신 관리

[CloudSimple 프라이빗 클라우드용으로 만든](azure-create-vm.md)가상 컴퓨터를 관리하려면 [Azure 포털에](https://portal.azure.com)서명합니다. 검색 및 가상 (사이드 메뉴의 **모든 서비스** 또는 **가상 머신에서** 검색)을 선택합니다.

## <a name="control-virtual-machine-operation"></a>가상 머신 작동 제어

선택한 가상 컴퓨터에 대한 **개요** 페이지에서 다음 컨트롤을 사용할 수 있습니다.

| 제어 | 설명 |
| ------------ | ------------- |
| 연결 | 지정된 VM에 연결합니다.  |
| 시작 | 지정된 VM을 시작합니다.  |
| 다시 시작 | 종료한 다음 지정된 VM의 전원을 켜는 것입니다.  |
| 중지 | 특정 VM을 종료합니다.  |
| 캡처 | 지정된 VM의 이미지를 캡처하여 다른 VM을 만드는 이미지로 사용할 수 있습니다. [Azure에서 일반화된 VM의 관리되는 이미지 만들기를](../virtual-machines/windows/classic/capture-image.md)참조하십시오.   |
| 이동 | 지정된 VM으로 이동합니다.  |
| DELETE | 지정된 VM을 제거합니다.  |
| 새로 고침 | 디스플레이에서 데이터를 새로 고칩니다.  |

### <a name="view-performance-information"></a>성능 정보 보기

**개요** 페이지의 아래쪽 영역의 차트는 선택한 간격에 대한 성능 데이터를 표시합니다(지난 1시간에서 마지막 30일까지; 기본값은 마지막 시간임). 각 차트 내에서 차트 위로 커서를 앞뒤로 이동하여 간격 내에서 언제든지 숫자 값을 표시할 수 있습니다.

다음 차트가 표시됩니다.

| 항목 | 설명 |
| ------------ | ------------- |
| CPU(평균) | 선택한 간격에 대한 평균 CPU 사용률백분율입니다.   |
| 네트워크 | 선택한 간격을 초과하여 MB(네트워크 내외) 트래픽입니다.  |
| 디스크 바이트 | 선택한 간격동안 디스크에서 읽고 디스크(MB)에 기록된 총 데이터입니다.  |
| 디스크 작업 | 선택한 간격동안 디스크 작업의 평균 속도(작업/초)입니다. |

## <a name="manage-vm-disks"></a>VM 디스크 관리

VM 디스크를 추가하려면 선택한 VM에 대한 **디스크** 페이지를 엽니다. 디스크를 추가하려면 **디스크 추가**를 클릭합니다. 인라인 옵션을 입력하거나 선택하여 다음 각 설정을 구성합니다. **저장**을 클릭합니다.

   | 항목 | 설명 |
   | ------------ | ------------- |
   | 이름 | 디스크를 식별하는 이름을 입력합니다.  |
   | 크기 | 사용 가능한 크기 중 하나를 선택합니다.  |
   | SCSI 컨트롤러 | SCSI 컨트롤러를 선택합니다. 사용 가능한 컨트롤러는 지원되는 다른 운영 체제에 따라 다릅니다.  |
   | Mode | 디스크가 스냅숏에 참여하는 방법을 결정합니다. 다음 옵션 중 하나를 선택합니다. <br> - 독립적 인 지속 : 디스크에 기록 된 모든 데이터는 영구적으로 기록됩니다.<br> - 독립적이고 지속적이지 않은 : 가상 머신의 전원을 끄거나 재설정하면 디스크에 기록된 변경 내용이 삭제됩니다.  이 모드를 사용하면 항상 동일한 상태에서 VM을 다시 시작할 수 있습니다. 자세한 내용은 [VMware 설명서](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html)를 참조하세요. |

디스크를 삭제하려면 디스크를 선택하고 **삭제를**클릭합니다.

## <a name="change-the-capacity-of-the-vm"></a>VM의 용량 변경

VM의 용량을 변경하려면 선택한 VM의 **크기** 페이지를 엽니다. 다음 중 임의의 지정을 클릭하고 **저장을**클릭합니다.

| 항목 | 설명 |
| ------------ | ------------- |
| 코어 수 | VM에 할당된 코어 수입니다.  |
| 하드웨어 가상화 | 게스트 OS에 하드웨어 가상화를 노출하려면 확인란을 선택합니다. VMware 문서 [노출 VMware 하드웨어 지원 가상화](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html)를 참조하십시오. |
| 메모리 크기 | VM에 할당할 메모리 양을 선택합니다.  

## <a name="manage-network-interfaces"></a>네트워크 인터페이스 관리(영문)

인터페이스를 추가하려면 **네트워크 인터페이스 추가를**클릭합니다. 인라인 옵션을 입력하거나 선택하여 다음 각 설정을 구성합니다. **저장**을 클릭합니다.

   | 제어 | 설명 |
   | ------------ | ------------- |
   | 이름 | 인터페이스를 식별하는 이름을 입력합니다.  |
   | 네트워크 | 프라이빗 클라우드 vSphere에서 구성된 네트워크 목록에서 선택합니다.  |
   | 어댑터 | VM에 대해 구성된 사용 가능한 유형 목록에서 vSphere 어댑터를 선택합니다. 자세한 내용은 가상 컴퓨터에 대한 네트워크 [어댑터 선택](https://kb.vmware.com/s/article/1001805)VMware 기술 자료 문서를 참조하십시오. |
   | 부팅 시 전원 켜기 | VM이 부팅되면 NIC 하드웨어를 사용하도록 설정할지 여부를 선택합니다. 기본값은 **사용**입니다. |

네트워크 인터페이스를 삭제하려면 네트워크 인터페이스를 선택하고 **삭제를**클릭합니다.
