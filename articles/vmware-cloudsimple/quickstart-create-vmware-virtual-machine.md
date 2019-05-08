---
title: Azure VMware 솔루션 CloudSimple 빠른 시작-Azure에서 VMware Vm 사용
description: 구성 및 Azure CloudSimple VMware 솔루션을 사용 하 여 Azure portal에서 VMware Vm을 사용 하는 방법을 알아봅니다
author: sharaths-cs
ms.author: dikamath
ms.date: 04/11/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3940adfaa42de8ac9c3f32a9eadc8f6d643ce3ce
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65149512"
---
# <a name="quickstart---consume-vmware-vms-on-azure"></a>빠른 시작-Azure에서 VMware Vm 사용

Azure portal에서 가상 컴퓨터를 만들려면 구독에 대 한 CloudSimple 관리자가 설정 하는 가상 머신 템플릿을 사용 합니다. 이러한 VM 템플릿은 VMware 인프라에 있습니다.

## <a name="cloudsimple-vm-creation-on-azure-requires-a-vm-template"></a>Azure에서 CloudSimple VM 생성 하려면 VM 템플릿이 필요

VCenter UI에서에서 사설 클라우드에서 가상 컴퓨터를 만듭니다. 템플릿을 만들려면의 지침을 따릅니다 [vSphere Web Client에에서는 템플릿에서 가상 머신을 복제](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE.html)합니다. 사설 클라우드 vCenter에서 VM 템플릿을 저장 합니다.

## <a name="create-a-virtual-machine-in-the-azure-portal"></a>Azure portal에서 가상 머신 만들기

1. **모든 서비스**를 선택합니다.

2. 검색할 **CloudSimple Virtual Machines**합니다.

3. **추가**를 클릭합니다.

    ![CloudSimple 가상 머신 만들기](media/create-cloudsimple-virtual-machine.png)

4. 기본 정보 클릭 입력 **다음: 크기**합니다.

    ![기본 사항-CloudSimple 가상 머신 만들기](media/create-cloudsimple-virtual-machine-basic-info.png)

    | 필드 | 설명 |
    | ------------ | ------------- |
    | 구독 | 사설 클라우드를 사용 하 여 연결 된 azure 구독입니다.  |
    | 리소스 그룹 | 리소스 그룹을 VM에 할당 됩니다. 기존 그룹을 선택 하거나 새를 만들 수 있습니다. |
    | 이름 | VM을 식별 하는 이름입니다.  |
    | Location | 이 VM 호스트 되는 azure 지역입니다.  |
    | 사설 클라우드 | 가상 머신을 만들려는 CloudSimple 사설 클라우드입니다. |
    | 리소스 풀 | VM에 대 한 매핑된 리소스 풀입니다. 사용 가능한 리소스 풀에서 선택 합니다. |
    | vSphere 템플릿 | VM에 대 한 vSphere 템플릿입니다.  |
    | 사용자 이름 | VM 관리자 사용자 이름 (예: Windows 템플릿)|
    | 암호 |  (예: Windows 템플릿) VM 관리자 암호입니다. |
    | 암호 확인 | 암호 확인 |

5. 코어 및 메모리 용량 VM 및 클릭 수를 선택 **다음: 구성**합니다. 전체 CPU 가상화 게스트 운영 체제에 노출 하려는 경우 확인란을 선택 합니다. 하드웨어 가상화를 필요로 하는 응용 프로그램 이진 변환 또는 준이 없는 virtual machines에서 실행할 수 있습니다. 자세한 내용은 VMware 문서를 참조 <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html" target="_blank">VMware 하드웨어 기반 가상화 노출</a>합니다.

    ![크기-CloudSimple 가상 머신 만들기](media/create-cloudsimple-virtual-machine-size.png)

6. 다음 표에 설명 된 대로 네트워크 인터페이스와 디스크를 구성 하 고 클릭 **검토 + 만들기**합니다.

    ![구성-CloudSimple 가상 머신 만들기](media/create-cloudsimple-virtual-machine-configurations.png)

    네트워크 인터페이스, 클릭 **네트워크 인터페이스 추가** 하 고 다음 설정을 구성 합니다.
    
    | 제어 | 설명 |
    | ------------ | ------------- |
    | 이름 | 인터페이스를 식별 하는 이름을 입력 합니다.  |
    | 네트워크 | 에 사설 클라우드 vsphere 분산된 구성 된 포트 그룹의 목록에서 선택 합니다.  |
    | 어댑터 | VSphere 어댑터를 VM에 대해 구성 된 사용 가능한 형식 목록에서 선택 합니다. 자세한 내용은 VMware 기술 자료 문서를 참조 하세요 <a href="https://kb.vmware.com/s/article/1001805" target="_blank">가상 머신에 대 한 네트워크 어댑터를 선택</a>합니다. |
    | 부팅 시 전원 켜기 | VM이 부팅 될 때 NIC 하드웨어를 사용할지를 선택 합니다. 기본값은 **사용**합니다. |

    디스크에 대 한 클릭 **디스크 추가** 하 고 다음 설정을 구성 합니다.

    | 항목 | Description | 
    | ------------ | ------------- | 
    | 이름 | 디스크를 식별 하는 이름을 입력 합니다.  | 
    | 크기 | 사용 가능한 크기 중 하나를 선택 합니다.  | 
    | SCSI 컨트롤러 | 디스크의 SCSI 컨트롤러를 선택 합니다.  |
    | Mode | 디스크 스냅숏을 참여 하는 방식을 결정 합니다. 이러한 옵션 중 하나를 선택 합니다. <br> 에 독립적인 영구: 디스크에 쓴 모든 데이터는 영구적으로 기록 됩니다.<br> 에 관계 없이 비 영구적인: 디스크에 기록 된 변경 전원을 또는 가상 컴퓨터를 다시 설정 될 때 삭제 됩니다.  비영구적 독립 모드를 사용 하면 항상 같은 주에 VM을 다시 시작 수 있습니다. 자세한 내용은 참조는 <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html" target="_blank">VMware 설명서</a>합니다.

7. 유효성 검사에는 다음이 완료 되 면 설정을 검토 하 고 클릭 **만들기**합니다. 설정을 변경 하려면 맨 위에 있는 탭을 클릭 하거나 클릭 합니다.

    ![검토-CloudSimple 가상 머신 만들기](media/create-cloudsimple-virtual-machine-review.png)

## <a name="next-steps"></a>다음 단계

* [CloudSimple 가상 머신의 목록 보기](https://docs.azure.cloudsimple.com/azure-manage-vm/)
* [Azure에서 CloudSimple 가상 머신을 관리합니다](https://docs.azure.cloudsimple.com/azure-create-vm/#view-list-of-cloudsimple-virtual-machines)