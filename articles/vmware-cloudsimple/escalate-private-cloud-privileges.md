---
title: 사설 클라우드 권한 에스컬레이션-CloudSimple 별 Azure VMware 솔루션
description: VCenter에서 관리 기능에 대 한 사설 클라우드의 권한을 에스컬레이션 하는 방법에 대해 설명 합니다.
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/05/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6e53d0d4f8f19a3cf81f1adb6c62b2797a50d146
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812218"
---
# <a name="escalate-private-cloud-vcenter-privileges-from-the-cloudsimple-portal"></a>CloudSimple 포털에서 사설 클라우드 vCenter 권한 에스컬레이션 

사설 클라우드 vCenter에 대 한 관리 액세스를 위해 CloudSimple 권한을 일시적으로 에스컬레이션 할 수 있습니다.  상승 된 권한을 사용 하 여 VMware 솔루션을 설치 하 고, id 원본을 추가 하 고, 사용자를 관리할 수 있습니다.

VCenter SSO 도메인에 새 사용자를 만들고 vCenter에 대 한 액세스 권한을 부여할 수 있습니다.  새 사용자를 만들 때는 vCenter 액세스를 위해 CloudSimple builtin 그룹에 추가 합니다.  자세한 내용은 [VMware vCenter의 Cloudsimple 사설 클라우드 권한 모델](https://docs.azure.cloudsimple.com/learn-private-cloud-permissions/)을 참조 하세요.

> [!CAUTION]
> 관리 구성 요소에 대 한 구성을 변경 하지 마세요. 에스컬레이션 된 권한 있는 상태에서 수행 되는 작업은 시스템에 부정적인 영향을 주거나 시스템을 사용할 수 없게 될 수 있습니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

[https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.

## <a name="escalate-privileges"></a>권한 높이기

1. [Cloudsimple 포털](access-cloudsimple-portal.md)에 액세스 합니다.

2. **리소스** 페이지를 열고 권한을 에스컬레이션 하려는 사설 클라우드를 선택 합니다.

3. 요약 페이지의 아래쪽에 있는 **vSphere 대 한 권한 변경**에서 **에스컬레이션**을 클릭 합니다.

    ![VSphere 대 한 권한 변경](media/escalate-private-cloud-privilege.png)

4. VSphere 사용자 유형을 선택 합니다.  로컬 **CloudOwner@cloudsimple.local** 사용자만 에스컬레이션 가능 합니다.

5. 드롭다운에서 에스컬레이션 시간 간격을 선택 합니다. 작업을 완료 하는 데 사용할 가장 짧은 기간을 선택 합니다.

6. 위험을 이해 하 고 있는지 확인 하려면이 확인란을 선택 합니다.

    ![권한 상승 대화 상자](media/escalate-private-cloud-privilege-dialog.png)

7. **확인**을 클릭합니다.

8. 에스컬레이션 프로세스는 몇 분 정도 걸릴 수 있습니다. 완료되면 **확인**을 클릭합니다.

권한 에스컬레이션이 시작 되 고 선택 된 간격이 끝날 때까지 지속 됩니다.  사설 클라우드 vCenter에 로그인 하 여 관리 작업을 수행할 수 있습니다.

> [!IMPORTANT]
> 사용자 한 명에 게 에스컬레이션 된 권한이 있을 수 있습니다.  사용자의 권한을 에스컬레이션 해야 다른 사용자의 권한을 에스컬레이션 할 수 있습니다.

## <a name="extend-privilege-escalation"></a>권한 상승 확장

작업을 완료 하기 위해 추가 시간이 필요한 경우 권한 에스컬레이션 기간을 확장할 수 있습니다.  관리 작업을 완료 하는 데 사용할 수 있는 추가 에스컬레이션 시간 간격을 선택 합니다.

1. Cloudsimple 포털의 **리소스** > **사설 클라우드에서** 권한 에스컬레이션을 확장 하려는 사설 클라우드를 선택 합니다.

2. 요약 탭의 아래쪽에 있는 **권한 에스컬레이션 확장**을 클릭 합니다.

    ![권한 상승 확장](media/de-escalate-private-cloud-privilege.png)

3. 드롭다운에서 에스컬레이션 시간 간격을 선택 합니다. 새 에스컬레이션 종료 시간을 검토 합니다.

4. **저장** 을 클릭 하 여 간격을 확장 합니다.

## <a name="de-escalate-privileges"></a>권한 상승

관리 작업이 완료 되 면 권한을 에스컬레이션 해야 합니다.  

1. Cloudsimple 포털의 **리소스** > **사설 클라우드에서** 권한을 에스컬레이션 하려는 사설 클라우드를 선택 합니다.

2. **에스컬레이션**을 클릭 합니다.

3. **확인**을 클릭합니다.

> [!IMPORTANT]
> 오류를 방지 하려면 vCenter에서 로그 아웃 한 후 다시 에스컬레이션 된 권한으로 다시 로그인 합니다.

## <a name="next-steps"></a>다음 단계

* [Active Directory 사용할 vCenter id 원본 설정](https://docs.azure.cloudsimple.com/set-vcenter-identity/)
* 백업 [워크 로드 가상 컴퓨터](https://docs.azure.cloudsimple.com/backup-workloads-veeam/) 에 백업 솔루션 설치