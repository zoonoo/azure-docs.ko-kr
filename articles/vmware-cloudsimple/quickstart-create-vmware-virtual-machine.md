---
title: Azure VMware 솔루션 (AVS) 빠른 시작-Azure에서 VMware Vm 만들기
description: Azure VMware Solutions (AVS)를 사용 하 여 Azure Portal에서 VMware Vm을 만들고 구성 하는 방법을 알아봅니다.
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: dikamath
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 575d59d60ebfcfdbe4d234d608e8d988006773c2
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77019556"
---
# <a name="quickstart---create-vmware-vms-on-azure"></a>빠른 시작-Azure에서 VMware Vm 만들기

Azure Portal에서 가상 컴퓨터를 만들려면 사용자의 구독에 대해 AVS 관리자가 사용 하도록 설정한 가상 컴퓨터 템플릿을 사용 합니다. VM 템플릿은 VMware 인프라에 있습니다.

## <a name="avs-vm-creation-on-azure-requires-a-vm-template"></a>Azure에서 AVS VM을 만들려면 VM 템플릿이 필요 합니다.

VCenter UI에서 AVS 사설 클라우드에 가상 컴퓨터를 만듭니다. 템플릿을 만들려면 [vSphere 웹 클라이언트에서 템플릿에 가상 머신 복제](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE.html)의 지침을 따릅니다. VM 템플릿을 AVS 사설 클라우드 vCenter에 저장 합니다.

## <a name="create-a-virtual-machine-in-the-azure-portal"></a>Azure Portal에서 가상 머신 만들기

1. **모든 서비스**를 선택합니다.

2. **AVS Virtual Machines**를 검색 합니다.

3. **추가**를 클릭합니다.

    ![AVS 가상 컴퓨터 만들기](media/create-cloudsimple-virtual-machine.png)

4. 기본 정보를 입력 하 고 **다음: 크기**를 클릭 합니다.

    ![AVS 가상 머신 만들기-기본 사항](media/create-cloudsimple-virtual-machine-basic-info.png)

    | 필드 | Description |
    | ------------ | ------------- |
    | Subscription | AVS 사설 클라우드와 연결 된 Azure 구독입니다. |
    | 리소스 그룹 | VM이 할당 될 리소스 그룹입니다. 기존 그룹을 선택하거나 새 그룹을 만들 수 있습니다. |
    | 이름 | VM을 식별 하는 이름입니다.  |
    | 위치 | 이 VM이 호스트 되는 Azure 지역입니다.  |
    | AVS 사설 클라우드 | 가상 머신을 만들려는 AVS 사설 클라우드. |
    | Resource Pool | VM에 대 한 매핑된 리소스 풀입니다. 사용 가능한 리소스 풀에서 선택합니다. |
    | vSphere 템플릿 | VM의 vSphere 대 한 템플릿입니다.  |
    | 사용자 이름 | Windows 템플릿에만 사용할 VM 관리자의 사용자 이름입니다.|
    | 암호 |  VM 관리자의 암호입니다 (Windows 템플릿에만 해당). |
    | 암호 확인 | 암호를 확인합니다. |

5. VM의 코어 및 메모리 용량 수를 선택 하 고 **다음: 구성**을 클릭 합니다. 게스트 운영 체제에 전체 CPU 가상화를 노출 하려면 확인란을 선택 합니다. 하드웨어 가상화가 필요한 애플리케이션은 이진 변환 또는 반가상화 없이 가상 머신에서 실행할 수 있습니다. 자세한 내용은 <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html" target="_blank">VMware 하드웨어 지원 가상화 공개</a> VMware 문서를 참조하세요.

    ![AVS 가상 머신 만들기-크기](media/create-cloudsimple-virtual-machine-size.png)

6. 다음 표에 설명 된 대로 네트워크 인터페이스 및 디스크를 구성 하 고 **검토 + 만들기**를 클릭 합니다.

    ![AVS 가상 머신 만들기-구성](media/create-cloudsimple-virtual-machine-configurations.png)

    네트워크 인터페이스에 대해 **네트워크 인터페이스 추가** 를 클릭 하 고 다음 설정을 구성 합니다.

    | 제어 | Description |
    | ------------ | ------------- |
    | 이름 | 인터페이스를 식별하는 이름을 입력합니다.  |
    | 네트워크 | AVS 사설 클라우드 vSphere 구성 된 분산 포트 그룹 목록에서 선택 합니다. |
    | 어댑터 | VM에 대해 구성 된 사용 가능한 형식 목록에서 vSphere 어댑터를 선택 합니다. 자세한 내용은 VMware 기술 자료 문서 <a href="https://kb.vmware.com/s/article/1001805" target="_blank">가상 컴퓨터에 대 한 네트워크 어댑터 선택</a>을 참조 하십시오. |
    | 부팅 시 전원 켜기 | VM이 부팅되면 NIC 하드웨어를 사용하도록 설정할지 여부를 선택합니다. 기본값은 **사용**입니다. |

    디스크에 대해 **디스크 추가** 를 클릭 하 고 다음 설정을 구성 합니다.

    | 항목 | Description |
    | ------------ | ------------- |
    | 이름 | 디스크를 식별하는 이름을 입력합니다. |
    | 크기 | 사용 가능한 크기 중 하나를 선택합니다. |
    | SCSI 컨트롤러 | 디스크의 SCSI 컨트롤러를 선택합니다. |
    | Mode | 디스크가 스냅숏에 참여 하는 방식을 결정 합니다. 다음 옵션 중 하나를 선택합니다. <br> -독립적 영구: 디스크에 기록 된 모든 데이터는 영구적으로 기록 됩니다.<br> -독립 비영구: 디스크에 기록 된 변경 내용은 전원을 끄거나 가상 컴퓨터를 다시 설정 하면 삭제 됩니다. 독립적 비영구 모드를 사용하면 항상 동일한 상태에서 VM을 다시 시작할 수 있습니다. 자세한 내용은 <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html" target="_blank">VMware 설명서</a>를 참조하세요.

7. 유효성 검사가 완료 되 면 설정을 검토 하 고 **만들기**를 클릭 합니다. 변경을 수행 하려면 위쪽에 있는 탭을 클릭 하거나를 클릭 합니다.

    ![AVS 가상 머신 만들기-검토](media/create-cloudsimple-virtual-machine-review.png)

## <a name="next-steps"></a>다음 단계

* [AVS 가상 컴퓨터 목록 보기](azure-create-vm.md#view-list-of-avs-virtual-machines)
* [Azure에서 AVS 가상 머신 관리](azure-manage-vm.md)
