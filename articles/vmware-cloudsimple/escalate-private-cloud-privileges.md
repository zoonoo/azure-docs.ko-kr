---
title: 프라이빗 클라우드 권한 에스컬레이션
titleSuffix: Azure VMware Solution by CloudSimple
description: vCenter에서 관리 기능에 대한 프라이빗 클라우드의 권한을 에스컬레이션하는 방법에 대해 설명합니다.
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/05/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 63dc18c522a1e2e3b03bdf806945e0be67774b18
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81870465"
---
# <a name="escalate-private-cloud-vcenter-privileges-from-the-cloudsimple-portal"></a>클라우드 단순 포털에서 프라이빗 클라우드 vCenter 권한 에스컬레이션

프라이빗 클라우드 vCenter에 대한 관리 액세스를 위해 CloudSimple 권한을 일시적으로 에스컬레이션할 수 있습니다.  높은 권한을 사용하여 VMware 솔루션을 설치하고 ID 원본을 추가하며 사용자를 관리할 수 있습니다.

vCenter SSO 도메인에서 새 사용자를 만들고 vCenter에 액세스할 수 있습니다.  새 사용자를 만들 때 vCenter에 액세스하기 위해 CloudSimple 기본 제공 그룹에 사용자를 추가합니다.  자세한 내용은 [VMware vCenter의 CloudSimple 프라이빗 클라우드 사용 권한 모델을](https://docs.microsoft.com/azure/vmware-cloudsimple/learn-private-cloud-permissions/)참조하십시오.

> [!CAUTION]
> 관리 구성 요소에 대한 구성을 변경하지 마십시오. 에스컬레이션된 권한 있는 상태 중에 수행된 작업은 시스템에 부정적인 영향을 미치거나 시스템을 사용할 수 없게 될 수 있습니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

[https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.

## <a name="escalate-privileges"></a>권한 높이기

1. [CloudSimple 포털에 액세스합니다.](access-cloudsimple-portal.md)

2. **리소스** 페이지를 열고 권한을 에스컬레이션할 비공개 클라우드를 선택합니다.

3. **vSphere 권한 변경**아래의 요약 페이지 하단 근처에서 **에스컬레이션을 클릭합니다.**

    ![vSphere 권한 변경](media/escalate-private-cloud-privilege.png)

4. vSphere 사용자 유형을 선택합니다.  로컬 `CloudOwner@cloudsimple.local` 사용자만 에스컬레이션할 수 있습니다.

5. 드롭다운에서 에스컬레이션 시간 간격을 선택합니다. 작업을 완료할 수 있는 가장 짧은 기간을 선택합니다.

6. 위험을 이해하려면 확인란을 선택합니다.

    ![권한 에스컬레이션 대화 상자](media/escalate-private-cloud-privilege-dialog.png)

7. **확인**을 클릭합니다.

8. 에스컬레이션 프로세스는 몇 분 정도 걸릴 수 있습니다. 완료한 경우 **확인**을 클릭합니다.

권한 에스컬레이션이 시작되고 선택한 간격이 끝날 때까지 지속됩니다.  사설 클라우드 vCenter에 로그인하여 관리 작업을 수행할 수 있습니다.

> [!IMPORTANT]
> 한 명의 사용자만 에스컬레이션된 권한을 가질 수 있습니다.  다른 사용자의 권한을 에스컬레이션하려면 먼저 사용자의 권한을 에스컬레이션해제해야 합니다.

> [!CAUTION]
> 새 사용자는 *클라우드-소유자 그룹,* *클라우드-글로벌-클러스터-관리자-그룹,* *클라우드-글로벌-스토리지-관리자-그룹,* *클라우드-글로벌-네트워크-관리자 그룹* 또는, *클라우드-글로벌-VM-관리자 그룹.*  *관리자* 그룹에 추가된 사용자는 자동으로 제거됩니다.  서비스 계정만 *Administrators* 그룹에 추가해야 하며 서비스 계정은 vSphere 웹 UI에 로그인하는 데 사용할 수 없습니다.

## <a name="extend-privilege-escalation"></a>권한 에스컬레이션 확장

작업을 완료하는 데 추가 시간이 필요한 경우 권한 에스컬레이션 기간을 연장할 수 있습니다.  관리 작업을 완료할 수 있는 추가 에스컬레이션 시간 간격을 선택합니다.

1. CloudSimple 포털의 **리소스** > **프라이빗 클라우드에서** 권한 에스컬레이션을 확장할 프라이빗 클라우드를 선택합니다.

2. 요약 탭 하단에서 **권한 에스컬레이션 확장을**클릭합니다.

    ![권한 에스컬레이션 확장](media/de-escalate-private-cloud-privilege.png)

3. 드롭다운에서 에스컬레이션 시간 간격을 선택합니다. 새 에스컬레이션 종료 시간을 검토합니다.

4. **저장을** 클릭하여 간격을 연장합니다.

## <a name="de-escalate-privileges"></a>에스컬레이션 해제 권한

관리 작업이 완료되면 권한을 해제해야 합니다.  

1. CloudSimple 포털의 **리소스** > **프라이빗 클라우드에서** 권한 에스컬레이션을 해제할 프라이빗 클라우드를 선택합니다.

2. **에스컬레이션 해제를 클릭합니다.**

3. **확인**을 클릭합니다.

> [!IMPORTANT]
> 오류를 방지하려면 vCenter에서 로그아웃하고 권한 해제 후 다시 로그인합니다.

## <a name="next-steps"></a>다음 단계

* [활성 디렉터리를 사용하도록 vCenter ID 원본 설정](https://docs.microsoft.com/azure/vmware-cloudsimple/set-vcenter-identity/)
* 백업 워크로드 [가상 머신에 백업](https://docs.microsoft.com/azure/vmware-cloudsimple/backup-workloads-veeam/) 솔루션 설치