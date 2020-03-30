---
title: Azure 사이트 복구에서 VMware vCenter 서버 관리
description: 이 문서에서는 Azure 사이트 복구를 사용하여 VMware VM을 Azure에 재해 복구하기 위해 VMware vCenter를 추가하고 관리하는 방법을 설명합니다.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/24/2019
ms.author: ramamill
ms.openlocfilehash: 01aef3aca4f6967b1681bff9598c7dd7a24739cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257265"
---
# <a name="manage-vmware-vcenter-server"></a>VMware vCenter 서버 관리

이 문서에서는 [Azure 사이트 복구의](site-recovery-overview.md)VMware vCenter 서버에서 관리 작업을 요약합니다.

## <a name="verify-prerequisites-for-vcenter-server"></a>vCenter 서버에 대한 필수 구성 프로그램 확인

VMware VM을 Azure로 복구하는 동안 vCenter 서버 및 VM의 필수 구성 조건은 [지원 매트릭스에](vmware-physical-azure-support-matrix.md#replicated-machines)나열됩니다.

## <a name="set-up-an-account-for-automatic-discovery"></a>자동 검색용 계정 설정

온-프레미스 VMware VM에 대한 재해 복구를 설정할 때 사이트 복구는 vCenter 서버/vSphere 호스트에 액세스해야 합니다. 그런 다음 사이트 복구 프로세스 서버는 VM을 자동으로 검색하고 필요에 따라 장애 조치할 수 있습니다. 기본적으로 프로세스 서버는 사이트 복구 구성 서버에서 실행됩니다. 구성 서버에 대한 계정을 추가하여 다음과 같이 vCenter Server/vSphere 호스트에 연결합니다.

1. 구성 서버에 로그인합니다.
1. 데스크톱 바로 가기를 사용하여 구성 서버_도구(cspsconfigtool.exe)를_엽니다.
1. 계정 **관리** 탭에서 **계정 추가**를 클릭합니다.

   ![add-account](./media/vmware-azure-manage-vcenter/addaccount.png)

1. 계정 세부 정보를 입력하고 **확인**을 클릭하여 계정을 추가합니다. 계정에는 계정 사용 권한 테이블에 요약된 권한이 있어야 합니다.

   > [!NOTE]
   > 계정 정보를 사이트 복구와 동기화하는 데 약 15분이 걸립니다.

### <a name="account-permissions"></a>계정 권한

|**작업** | **계정** | **권한을** | **세부 정보**|
|--- | --- | --- | ---|
|**VM 검색/마이그레이션(장애 조치 없이)** | 최소한 읽기 전용 사용자 계정입니다. | 데이터 센터 개체 –> 자식 개체에 전파, role=Read 전용 | 사용자는 데이터 센터 수준에서 할당되며 데이터 센터의 모든 개체에 대한 액세스 권한이 있습니다.<br/><br/> 액세스를 제한하려는 경우 **자식에 전파** 개체를 사용하여 **액세스 권한 없음** 역할을 자식 개체(vSphere 호스트, 데이터 저장소, 가상 머신 및 네트워크)에 할당합니다.|
|**복제/장애 조치** | 최소한 읽기 전용 사용자 계정입니다. | 데이터 센터 개체 –> 자식 개체에 전파, role=Read 전용 | 사용자는 데이터 센터 수준에서 할당되며 데이터 센터의 모든 개체에 대한 액세스 권한이 있습니다.<br/><br/> 액세스를 제한하려면 자식 개체(vSphere 호스트, 데이터 스토어, 가상 시스템 및 **네트워크)에 자식 개체에 대한 전파를** 사용하여 **액세스 없음** 역할을 할당합니다.<br/><br/> 전체 복제, 장애 조치, 장애 복구가 아닌 마이그레이션에 유용합니다.|
|**복제/장애 조치(failover)/장애 복구(failback)** | 필요한 권한이 있는 역할(AzureSiteRecoveryRole)을 만든 다음 VMware 사용자 또는 그룹에 역할을 할당하는 것이 좋습니다. | 데이터 센터 개체 –> 자식 개체에 전파, role=AzureSiteRecoveryRole<br/><br/> 데이터 저장소 -&gt; 공간 할당, 데이터 저장소 찾아보기, 낮은 수준 파일 작업, 파일 제거, 가상 머신 파일 업데이트<br/><br/> 네트워크 -> 네트워크 할당<br/><br/> 리소스 -> 리소스 풀에 VM 할당, 전원이 꺼진 VM 마이그레이션, 전원이 켜진 VM 마이그레이션<br/><br/> 태스크 -> 만들기 태스크, 업데이트 태스크<br/><br/> 가상 머신 -&gt; 구성<br/><br/> 가상 머신 -&gt; 상호 작용 -&gt; 질문 응답, 디바이스 연결, CD 미디어 구성, 플로피 미디어 구성, 전원 끄기, 전원 켜기, VMware 도구 설치<br/><br/> 가상 머신 -&gt; 인벤토리 -&gt; 만들기, 등록, 등록 취소<br/><br/> 가상 머신 -&gt; 프로비전 -&gt; 가상 머신 다운로드 허용, 가상 머신 파일 업로드 허용<br/><br/> 가상 머신 -&gt; 스냅샷 -&gt; 스냅샷 제거 | 사용자는 데이터 센터 수준에서 할당되며 데이터 센터의 모든 개체에 대한 액세스 권한이 있습니다.<br/><br/> 액세스를 제한하려는 경우 **자식에 전파** 개체를 사용하여 **액세스 권한 없음** 역할을 자식 개체(vSphere 호스트, 데이터 저장소, 가상 머신 및 네트워크)에 할당합니다.|

## <a name="add-vmware-server-to-the-vault"></a>자격 증명 모음에 VMware 서버 추가

온-프레미스 VMware VM에 대 한 재해 복구를 설정 하는 경우 다음과 같이 VM을 검색 하는 vCenter 서버/vSphere 호스트를 사이트 복구 자격 증명 모음에 추가 합니다.

1. 볼트 > **사이트 복구 인프라** > **구성 Severs에서**구성 서버를 엽니다.
1. **세부 정보** 페이지에서 **vCenter**를 클릭합니다.
1. **vCenter 추가에서**vSphere 호스트 또는 vCenter 서버에 대해 친숙한 이름을 지정합니다.
1. 서버의 IP 주소 또는 FQDN을 지정합니다.
1. VMware 서버가 다른 포트에서 요청을 수신하도록 구성되지 않은 경우 포트를 443으로 그대로 둡니다.
1. VMware vCenter 또는 vSphere ESXi 서버에 연결하는 데 사용되는 계정을 선택합니다. 그런 다음 **확인**을 클릭합니다.

## <a name="modify-credentials"></a>자격 증명 수정

필요한 경우 다음과 같이 vCenter Server/vSphere 호스트에 연결하는 데 사용되는 자격 증명을 수정할 수 있습니다.

1. 구성 서버에 로그인합니다.
1. 데스크톱 바로 가기를 사용하여 구성 서버_도구(cspsconfigtool.exe)를_엽니다.
1. **계정 관리** 탭에서 **계정 추가**를 클릭합니다.

   ![add-account](./media/vmware-azure-manage-vcenter/addaccount.png)

1. 새 계정 세부 정보를 제공하고 **확인을**클릭합니다. 계정에는 계정 사용 권한 테이블에 나열된 권한이 [필요합니다.](#account-permissions)
1. 사이트 복구 **인프라** > **구성 서버를**> 볼트에서 구성 서버를 엽니다.
1. **자세한 내용을**보려면 서버 **새로 고침을**클릭합니다.
1. 서버 새로 고침 작업이 완료되면 vCenter 서버를 선택합니다.
1. **요약에서** **vCenter 서버/vSphere 호스트 계정에서**새로 추가된 계정을 선택하고 **저장을**클릭합니다.

   ![modify-account](./media/vmware-azure-manage-vcenter/modify-vcente-creds.png)

## <a name="delete-a-vcenter-server"></a>vCenter 서버 삭제

1. 사이트 복구 **인프라** > **구성 서버를**> 볼트에서 구성 서버를 엽니다.
1. **세부 정보** 페이지에서 vCenter 서버를 선택합니다.
1. **삭제** 단추를 클릭합니다.

   ![delete-account](./media/vmware-azure-manage-vcenter/delete-vcenter.png)

## <a name="modify-the-ip-address-and-port"></a>IP 주소 및 포트 수정

vCenter 서버의 IP 주소 또는 서버와 사이트 복구 간의 통신에 사용되는 포트를 수정할 수 있습니다. 기본적으로 사이트 복구는 포트 443을 통해 vCenter 서버/vSphere 호스트 정보에 액세스합니다.

1. 사이트 복구 **인프라** > **구성 서버를**> 볼트에서 vCenter 서버가 추가되는 구성 서버를 클릭합니다.
1. **vCenter 서버에서**수정하려는 vCenter 서버를 클릭합니다.
1. **요약에서**IP 주소와 포트를 업데이트하고 변경 내용을 저장합니다.

   ![add_ip_new_vcenter](media/vmware-azure-manage-vcenter/add-ip.png)

1. 변경 사항이 적용되려면 15분 동안 기다리거나 [구성 서버를 새로 고칩니다.](vmware-azure-manage-configuration-server.md#refresh-configuration-server)

## <a name="migrate-all-vms-to-a-new-server"></a>모든 VM을 새 서버로 마이그레이션

새 vCenter 서버를 사용 하 여 모든 VM을 마이그레이션 하려는 경우 vCenter 서버에 할당 된 IP 주소를 업데이트 해야 합니다. 중복 항목으로 이어질 수 있기 때문에 다른 VMware 계정을 추가하지 마십시오. 주소를 다음과 같이 업데이트합니다.

1. 사이트 복구 **인프라** > **구성 서버를**> 볼트에서 vCenter 서버가 추가되는 구성 서버를 클릭합니다.
1. **vCenter 서버** 섹션에서 마이그레이션할 vCenter 서버를 클릭합니다.
1. **요약에서**새 vCenter 서버의 IP 주소를 업데이트하고 변경 내용을 저장합니다.
1. IP 주소가 업데이트되는 즉시 사이트 복구는 새 vCenter 서버에서 VM 검색 정보를 받기 시작합니다. 이는 진행 중인 복제 활동에 영향을 미치지 않습니다.

## <a name="migrate-a-few-vms-to-a-new-server"></a>몇 개의 VM을 새 서버로 마이그레이션

복제하는 VM 중 일부만 새 vCenter 서버로 마이그레이션하려면 다음을 수행합니다.

1. 구성 서버에 새 vCenter 서버를 [추가합니다.](#add-vmware-server-to-the-vault)
1. 새 서버로 이동하는 VM에 대한 [복제를 사용하지 않도록 설정합니다.](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure)
1. VMware에서 VM을 새 vCenter 서버로 마이그레이션합니다.
1. 새 vCenter 서버를 선택하여 마이그레이션된 VM에 대한 [복제를](vmware-azure-tutorial.md#enable-replication) 다시 활성화합니다.

## <a name="migrate-most-vms-to-a-new-server"></a>대부분의 VM을 새 서버로 마이그레이션

새 vCenter 서버로 마이그레이션할 VM 수가 원래 vCenter 서버에 남아 있는 VM 수보다 많으면 다음을 수행합니다.

1. 구성 서버 [설정에서](#modify-the-ip-address-and-port) vCenter Server에 할당된 IP 주소를 새 vCenter 서버의 주소로 업데이트합니다.
1. 이전 서버에 남아 있는 몇 개의 VM에 대한 [복제를 사용하지 않도록 설정합니다.](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure)
1. [이전 vCenter 서버와](#add-vmware-server-to-the-vault) 해당 IP 주소를 구성 서버에 추가합니다.
1. 이전 서버에 남아 있는 VM에 대한 [복제를 다시 활성화합니다.](vmware-azure-tutorial.md#enable-replication)

## <a name="next-steps"></a>다음 단계

문제가 있는 경우 [vCenter 서버 검색 실패 를 참조하세요.](vmware-azure-troubleshoot-vcenter-discovery-failures.md)
