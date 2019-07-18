---
title: Azure VMware Solution by CloudSimple 빠른 시작 - Azure에서 VMware VM 사용
description: 이 빠른 시작에서는 Azure VMware Solution by CloudSimple을 사용하여 Azure Portal에서 VMware VM을 구성하고 사용하는 방법을 알아봅니다.
author: sharaths-cs
ms.author: dikamath
ms.date: 04/11/2019
ms.topic: quickstart
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: b430efbc931d72de4b095a7eac4c1e7ca496b1b9
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393506"
---
# <a name="quickstart-consume-vmware-vms-on-azure"></a>빠른 시작: Azure에서 VMware VM 사용

Azure Portal에서 가상 머신을 만들려면 vCenter 프라이빗 클라우드에서 사용할 수 있는 가상 머신 템플릿을 사용할 수 있습니다. vCenter 관리자는 추가 템플릿을 프라이빗 클라우드에 만들 수 있습니다.

## <a name="create-a-vm-template"></a>VM 템플릿 만들기

먼저 vCenter UI를 사용하여 가상 머신을 프라이빗 클라우드에 만듭니다. 템플릿을 만들려면 [Virtual Machine을 vSphere Web Client의 템플릿에 복제](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE.html) VMware 문서의 지침을 따릅니다. VM 템플릿은 vCenter 프라이빗 클라우드에 저장합니다.

## <a name="create-a-virtual-machine-in-the-azure-portal"></a>Azure Portal에서 가상 머신 만들기

1. **모든 서비스**를 선택합니다.

2. **CloudSimple 가상 머신**을 검색합니다.

3. **추가**를 선택합니다.

    ![추가 선택](media/create-cloudsimple-virtual-machine.png)

4. 가상 머신에 대한 다음 정보를 입력하고, **다음: 크기**를 선택합니다.

    ![CloudSimple 가상 머신 만들기 - 기본 사항](media/create-cloudsimple-virtual-machine-basic-info.png)

    | 필드 | 설명 |
    | ------------ | ------------- |
    | **구독** | 프라이빗 클라우드와 연결된 Azure 구독입니다.  |
    | **리소스 그룹** | VM이 할당될 리소스 그룹입니다. 기존 그룹을 선택하거나 새 그룹을 만들 수 있습니다. |
    | **Name** | VM을 식별하는 이름입니다.  |
    | **위치**: | VM이 호스팅되는 Azure 지역입니다.  |
    | **프라이빗 클라우드** | VM을 만들려는 CloudSimple 프라이빗 클라우드입니다. |
    | **ResourcePool** | VM에 매핑된 리소스 풀입니다. 사용 가능한 리소스 풀에서 선택합니다. |
    | **vSphere 템플릿** | VM용 vSphere 템플릿입니다.  |
    | **사용자 이름** | VM 관리자의 사용자 이름입니다(Windows 템플릿의 경우).|
    | **암호** |  VM 관리자의 암호입니다(Windows 템플릿의 경우). |
    | **암호 확인** | 이전 필드에서 제공한 암호입니다. |

5. VM의 코어 수와 메모리 용량을 선택합니다. 전체 CPU 가상화를 게스트 운영 체제에 공개하려면 **게스트 OS에 공개**를 선택합니다. 하드웨어 가상화가 필요한 애플리케이션은 이진 변환 또는 반가상화 없이 가상 머신에서 실행할 수 있습니다. 자세한 내용은 <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html" target="_blank">VMware 하드웨어 지원 가상화 공개</a> VMware 문서를 참조하세요. 완료되면 **다음: 구성**을 선택합니다.

    ![CloudSimple 가상 머신 만들기 - 크기](media/create-cloudsimple-virtual-machine-size.png)

6. 다음 표에서 설명한 대로 네트워크 인터페이스와 디스크를 구성한 다음, **검토 + 만들기**를 선택합니다.

    ![CloudSimple 가상 머신 만들기 - 구성](media/create-cloudsimple-virtual-machine-configurations.png)

    네트워크 인터페이스에 대해 **네트워크 인터페이스 추가**를 선택하고, 다음 설정을 구성합니다.
    
    | 설정 | 설명 |
    | ------------ | ------------- |
    | **Name** | 인터페이스를 식별하는 이름을 입력합니다.  |
    | **네트워크** | vSphere 프라이빗 클라우드에 구성된 분산 포트 그룹 목록에서 선택합니다.  |
    | **어댑터** | VM에 대해 구성된 사용 가능한 종류 목록에서 vSphere 어댑터를 선택합니다. 자세한 내용은 <a href="https://kb.vmware.com/s/article/1001805" target="_blank">가상 머신에 대한 네트워크 어댑터 선택</a> VMware 문서를 참조하세요. |
    | **부팅 시 전원 켜기** | VM이 부팅되면 NIC 하드웨어를 사용하도록 설정할지 여부를 선택합니다. 기본값은 **사용**입니다. |

    디스크에 대해 **디스크 추가**를 선택하고, 다음 설정을 구성합니다.

    | 설정 | 설명 |
    | ------------ | ------------- |
    | **Name** | 디스크를 식별하는 이름을 입력합니다.  |
    | **크기** | 사용 가능한 크기 중 하나를 선택합니다.  |
    | **SCSI 컨트롤러** | 디스크의 SCSI 컨트롤러를 선택합니다.  |
    | **모드** | 모드는 디스크에서 스냅샷에 참여하는 방법을 지정합니다. 다음 옵션 중 하나를 선택합니다. <br> **독립적 영구**: 디스크에 기록된 모든 변경 내용이 영구적으로 기록됩니다.<br> **독립적 비영구**: 전원을 끄거나 가상 머신을 다시 설정하면 디스크에 기록된 변경 내용이 삭제됩니다. 독립적 비영구 모드를 사용하면 항상 동일한 상태에서 VM을 다시 시작할 수 있습니다. 자세한 내용은 <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html" target="_blank">VMware 설명서</a>를 참조하세요.

7. 유효성 검사가 완료되면 설정을 검토하고 **만들기**를 선택합니다. 변경하려면 위쪽의 탭을 선택하거나 **이전: 구성**을 선택합니다.

    ![CloudSimple 가상 머신 만들기 - 검토 + 만들기](media/create-cloudsimple-virtual-machine-review.png)

## <a name="next-steps"></a>다음 단계

* [CloudSimple 가상 머신의 목록 보기](https://docs.azure.cloudsimple.com/azure-create-vm/#view-list-of-cloudsimple-virtual-machines)
* [Azure에서 CloudSimple 가상 머신 관리](https://docs.azure.cloudsimple.com/azure-manage-vm/)
