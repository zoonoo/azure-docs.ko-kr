---
title: 클라우드에 의한 Azure VMware 솔루션 간단함 - VM 템플릿을 사용하여 Azure에서 가상 컴퓨터 만들기
description: CloudSimple 프라이빗 클라우드용 VMware 인프라의 VM 템플릿을 사용하여 Azure에서 가상 컴퓨터를 만드는 방법에 대해 설명합니다.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ee3029de9826aee17dc76d0e69f08b3c1068423b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244694"
---
# <a name="create-a-virtual-machine-in-azure-using-vm-templates-on-the-vmware-infrastructure"></a>VMware 인프라에서 VM 템플릿을 사용하여 Azure에서 가상 컴퓨터 만들기

CloudSimple 관리자가 구독에 대해 사용하도록 설정한 VM웨어 인프라의 VM 템플릿을 사용하여 Azure 포털에서 가상 컴퓨터를 만들 수 있습니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

[Azure 포털에](https://portal.azure.com)로그인합니다.

## <a name="create-cloudsimple-virtual-machine"></a>클라우드 간단한 가상 컴퓨터 만들기

1. **모든 서비스를**선택합니다.

2. **CloudSimple 가상 머신**을 검색합니다.

3. **추가**를 클릭합니다.

    ![클라우드 간단한 가상 컴퓨터 만들기](media/create-cloudsimple-virtual-machine.png)

4. 기본 정보를 입력하려면 **다음 을 클릭합니다.**

    > [!NOTE]
    > Azure에서 CloudSimple 가상 컴퓨터를 만들려면 VM 템플릿이 필요합니다.  이 VM 템플릿은 프라이빗 클라우드 vCenter에 있어야 합니다.  원하는 운영 체제 및 구성으로 vCenter UI에서 프라이빗 클라우드에 가상 머신을 만듭니다.  가상 컴퓨터 복제의 지침을 [vSphere 웹 클라이언트의 템플릿에](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE_copy.html)사용하여 템플릿을 만듭니다.

    ![클라우드 간단한 가상 머신 만들기 - 기본 사항](media/create-cloudsimple-virtual-machine-basic-info.png)

    | 필드 | 설명 |
    | ------------ | ------------- |
    | Subscription | 프라이빗 클라우드와 연결된 Azure 구독입니다.  |
    | 리소스 그룹 | VM이 할당될 리소스 그룹입니다. 기존 그룹을 선택하거나 새 그룹을 만들 수 있습니다. |
    | 이름 | VM을 식별하는 이름입니다.  |
    | 위치 | 이 VM이 호스팅되는 Azure 지역입니다.  |
    | 프라이빗 클라우드 | 가상 머신을 만들려는 CloudSimple 프라이빗 클라우드. |
    | Resource Pool | VM에 대한 매핑된 리소스 풀입니다. 사용 가능한 리소스 풀에서 선택합니다. |
    | vSphere 템플릿 | vM에 대한 스피어 템플릿입니다.  |
    | 사용자 이름 | VM 관리자의 사용자 이름(Windows 템플릿용)|
    | 암호 <br>암호 확인 | VM 관리자의 암호(Windows 템플릿용)입니다.  |

5. VM의 코어 수와 메모리 용량을 선택하고 **다음:구성을 클릭합니다.** 하드웨어 가상화가 필요한 응용 프로그램이 이진 변환 이나 가상화 없이 가상 시스템에서 실행할 수 있도록 게스트 운영 체제에 전체 CPU 가상화를 노출 하려는 경우 확인란을 선택 합니다. 자세한 내용은 [VMware 하드웨어 지원 가상화 공개](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html) VMware 문서를 참조하세요.

    ![클라우드 간단한 가상 머신 만들기 - 크기](media/create-cloudsimple-virtual-machine-size.png)

6. 다음 표에 설명된 대로 네트워크 인터페이스와 디스크를 구성하고 **검토 + 만들기를**클릭합니다.

    ![CloudSimple 가상 컴퓨터 만들기 - 구성](media/create-cloudsimple-virtual-machine-configurations.png)

    네트워크 인터페이스의 경우 **네트워크 인터페이스 추가를** 클릭하고 다음 설정을 구성합니다.

    | 제어 | 설명 |
    | ------------ | ------------- |
    | 이름 | 인터페이스를 식별하는 이름을 입력합니다.  |
    | 네트워크 | 프라이빗 클라우드 vSphere에서 구성된 분산 포트 그룹 목록에서 선택합니다.  |
    | 어댑터 | VM에 대해 구성된 사용 가능한 유형 목록에서 vSphere 어댑터를 선택합니다. 자세한 내용은 가상 컴퓨터에 대한 네트워크 [어댑터 선택](https://kb.vmware.com/s/article/1001805)VMware 기술 자료 문서를 참조하십시오. |
    | 부팅 시 전원 켜기 | VM이 부팅되면 NIC 하드웨어를 사용하도록 설정할지 여부를 선택합니다. 기본값은 **사용**입니다. |

    디스크의 경우 **디스크 추가를** 클릭하고 다음 설정을 구성합니다.

    | 항목 | 설명 |
    | ------------ | ------------- |
    | 이름 | 디스크를 식별하는 이름을 입력합니다.  |
    | 크기 | 사용 가능한 크기 중 하나를 선택합니다.  |
    | SCSI 컨트롤러 | 디스크의 SCSI 컨트롤러를 선택합니다.  |
    | Mode | 디스크가 스냅숏에 참여하는 방법을 결정합니다. 다음 옵션 중 하나를 선택합니다. <br> - 독립적 인 지속 : 디스크에 기록 된 모든 데이터는 영구적으로 기록됩니다.<br> - 독립적 인 비 지속 : 당신이 전원을 끄거나 가상 머신을 재설정 할 때 디스크에 기록 된 변경 사항은 삭제됩니다.  독립적 비영구 모드를 사용하면 항상 동일한 상태에서 VM을 다시 시작할 수 있습니다. 자세한 내용은 [VMware 설명서](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html)를 참조하세요.

7. 유효성 검사가 완료되면 설정을 검토하고 **만들기를**클릭합니다. 변경하려면 상단의 탭을 클릭하거나 클릭합니다.

    ![CloudSimple 가상 컴퓨터 만들기 - 검토](media/create-cloudsimple-virtual-machine-review.png)

## <a name="view-list-of-cloudsimple-virtual-machines"></a>CloudSimple 가상 머신의 목록 보기

1. **모든 서비스를**선택합니다.

2. **CloudSimple 가상 머신**을 검색합니다.

3. 프라이빗 클라우드가 생성된 것을 선택합니다.

    ![클라우드 단순 가상 컴퓨터 목록](media/list-cloudsimple-virtual-machines.png)

CloudSimple 가상 컴퓨터 목록에는 Azure 포털에서 만든 가상 시스템이 포함되어 있습니다.  매핑된 vCenter 리소스 풀의 프라이빗 클라우드 vCenter에서 만든 가상 시스템이 목록에 표시됩니다.  
