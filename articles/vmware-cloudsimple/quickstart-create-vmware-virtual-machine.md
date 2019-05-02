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
ms.openlocfilehash: 58edadb553730b646f23f4981d6cbf1bdbfe76d5
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/26/2019
ms.locfileid: "64577722"
---
# <a name="quickstart---consume-vmware-vms-on-azure"></a>빠른 시작-Azure에서 VMware Vm 사용

Azure portal에서 가상 컴퓨터를 만들려면 구독에 대 한 CloudSimple 관리자가 설정 하는 가상 머신 템플릿을 사용 합니다. 이러한 VM 템플릿은 VMware 인프라에 있습니다.

## <a name="cloudsimple-vm-creation-on-azure-requires-a-vm-template"></a>Azure에서 CloudSimple VM 생성 하려면 VM 템플릿이 필요

VCenter UI에서에서 사설 클라우드에서 가상 컴퓨터를 만듭니다. 템플릿을 만들려면의 지침을 따릅니다 [vSphere Web Client에에서는 템플릿에서 가상 머신을 복제](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE.html)합니다. 사설 클라우드 vCenter에서 VM 템플릿을 저장 합니다.

## <a name="create-a-virtual-machine-in-the-azure-portal"></a>Azure portal에서 가상 머신 만들기

1. 왼쪽된 메뉴에서 클릭 **+** 하거나 **리소스를 만드는**합니다.

2. 왼쪽된 메뉴에서 클릭 **계산**를 클릭 하 고 **CloudSimple 가상 머신**합니다.

3. 클릭 **Confirm** 새 VM을 만들 것인지 확인 합니다.

4. 다음 표에 설명 된 대로 기본 구성을 설정 하 고 클릭 **다음: 크기**합니다.

    | 필드 | 설명 |
    | ------------ | ------------- |
    | 구독 | 사설 클라우드 배포와 연결 된 azure 구독입니다.  |
    | 리소스 그룹 | 배포 그룹을 VM에 할당 됩니다. 기존 그룹을 선택 하거나 새를 만들 수 있습니다. |
    | 이름 | VM을 식별 하는 이름입니다.  |
    | Location | 이 VM 호스트 되는 azure 지역입니다.  |
    | 리소스 풀 | VM에 대 한 물리적 리소스입니다. 사용 가능한 리소스 풀에서 선택 합니다. |
    | vSphere 템플릿 | Vm 템플릿의 운영 체제 유형입니다.  |
    | 사용자 이름 | VM 관리자의 사용자 이름입니다. |
    | 암호를 확인 하는 암호 | VM 관리자 암호입니다.  |

5. 코어 및 메모리 용량을 VM의 수를 선택 합니다.

6. (선택 사항) 전체 CPU 가상화 게스트 운영 체제에 노출 하려는 경우 선택 합니다 **게스트 OS에 노출** 확인란을 선택 합니다.
이 선택을 통해 이진 변환 또는 준이 없는 virtual machines에서 실행 하도록 하드웨어 가상화를 필요로 하는 응용 프로그램. 자세한 내용은 VMware 문서를 참조 [VMware 하드웨어 기반 가상화 노출](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html)합니다.

7. **다음: 구성**합니다.

8. 네트워크 인터페이스 및 다음 표에 설명 된 대로 디스크를 구성 합니다.

    네트워크 인터페이스, 클릭 **네트워크 인터페이스 추가** 하 고 다음 설정을 구성 합니다.

    | 제어 | 설명 |
    | ------------ | ------------- |
    | 이름 | 인터페이스를 식별 하는 이름을 입력 합니다.  |
    | 네트워크 | 사설 클라우드 vsphere에 구성 된 네트워크 목록에서 선택 합니다.  |
    | 어댑터 | VSphere 어댑터를 VM에 대해 구성 된 사용 가능한 형식 목록에서 선택 합니다. 자세한 내용은 VMware 기술 자료 문서를 참조 하세요 [가상 머신에 대 한 네트워크 어댑터를 선택](https://kb.vmware.com/s/article/1001805)합니다. |
    | 부팅 시 전원 켜기 | VM이 부팅 될 때 NIC 하드웨어를 사용할지를 선택 합니다. 기본값은 **사용**합니다. |

    디스크에 대 한 클릭 **디스크 추가** 하 고 다음 설정을 구성 합니다.

    | 항목 | Description |
    | ------------ | ------------- |
    | 이름 | 디스크를 식별 하는 이름을 입력 합니다.  |
    | 크기 | 사용 가능한 크기 중 하나를 선택 합니다.  |
    | SCSI 컨트롤러 | SCSI 컨트롤러를 선택 합니다. 지원 되는 다른 운영 체제에 대 한 사용 가능한 컨트롤러 달라 집니다.  |
    | Mode | 디스크 스냅숏을 참여 하는 방식을 결정 합니다. 이러한 옵션 중 하나를 선택 합니다. <br> 에 독립적인 영구: 디스크에 쓴 모든 데이터는 영구적으로 기록 됩니다.<br> 에 관계 없이 비 영구적인: 디스크에 기록 된 변경 전원을 또는 가상 컴퓨터를 다시 설정 될 때 삭제 됩니다.  비영구적 독립 모드를 사용 하면 항상 같은 주에 VM을 다시 시작 수 있습니다. 자세한 내용은 참조는 [VMware 설명서.](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html)

9. 설정을 검토 합니다. 설정을 변경 하려면 맨 위에 있는 탭을 클릭 합니다.

10. 클릭 **만들기** VM을 만들고 설정을 저장 합니다.

## <a name="next-steps"></a>다음 단계

* [CloudSimple 가상 머신의 목록 보기](https://docs.azure.cloudsimple.com/azurelistvms/)
* [Azure에서 CloudSimple 가상 머신을 관리합니다](https://docs.azure.cloudsimple.com/azureoverviewpage/)