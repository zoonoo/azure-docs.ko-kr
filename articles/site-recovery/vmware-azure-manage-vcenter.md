---
title: Azure Site Recovery에서 VMware vCenter 서버 관리
description: 이 문서에서는 Azure Site Recovery를 사용 하 여 Azure에 VMware Vm의 재해 복구를 위해 VMware vCenter를 추가 하 고 관리 하는 방법을 설명 합니다.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/24/2019
ms.author: ramamill
ms.openlocfilehash: 01aef3aca4f6967b1681bff9598c7dd7a24739cd
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79257265"
---
# <a name="manage-vmware-vcenter-server"></a>VMware vCenter Server 관리

이 문서에서는 [Azure Site Recovery](site-recovery-overview.md)의 VMware vCenter Server에 대 한 관리 작업을 요약 합니다.

## <a name="verify-prerequisites-for-vcenter-server"></a>vCenter Server에 대 한 필수 구성 요소 확인

VMware Vm을 Azure로 재해 복구 하는 동안 vCenter 서버 및 Vm에 대 한 필수 구성 요소가 [지원 매트릭스](vmware-physical-azure-support-matrix.md#replicated-machines)에 나열 되어 있습니다.

## <a name="set-up-an-account-for-automatic-discovery"></a>자동 검색용 계정 설정

온-프레미스 VMware Vm에 대 한 재해 복구를 설정 하는 경우 Site Recovery vCenter Server/vSphere 호스트에 액세스할 수 있어야 합니다. 그러면 Site Recovery 프로세스 서버는 자동으로 Vm을 검색 하 고 필요에 따라 장애 조치 (failover)를 수행할 수 있습니다. 기본적으로 프로세스 서버는 Site Recovery 구성 서버에서 실행 됩니다. 구성 서버가 다음과 같이 vCenter Server/vSphere 호스트에 연결할 계정을 추가 합니다.

1. 구성 서버에 로그인합니다.
1. 바탕 화면 바로 가기를 사용 하 여 구성 서버 도구 (_cspsconfigtool .exe_)를 엽니다.
1. **계정 관리** 탭에서 **계정 추가**를 클릭 합니다.

   ![add-account](./media/vmware-azure-manage-vcenter/addaccount.png)

1. 계정 세부 정보를 입력하고 **확인**을 클릭하여 계정을 추가합니다. 계정에는 계정 권한 테이블에 요약 된 권한이 있어야 합니다.

   > [!NOTE]
   > Site Recovery와 계정 정보를 동기화 하는 데 약 15 분이 걸립니다.

### <a name="account-permissions"></a>계정 권한

|**Task** | **계정** | **사용 권한** | **세부 정보**|
|--- | --- | --- | ---|
|**VM 검색/마이그레이션 (장애 복구 (failback) 제외)** | 최소한 읽기 전용 사용자 계정 | 데이터 센터 개체 –> 자식 개체에 전파, role=Read 전용 | 사용자는 데이터 센터 수준에서 할당되며 데이터 센터의 모든 개체에 대한 액세스 권한이 있습니다.<br/><br/> 액세스를 제한하려는 경우 **자식에 전파** 개체를 사용하여 **액세스 권한 없음** 역할을 자식 개체(vSphere 호스트, 데이터 저장소, 가상 머신 및 네트워크)에 할당합니다.|
|**복제/장애 조치** | 최소한 읽기 전용 사용자 계정 | 데이터 센터 개체 –> 자식 개체에 전파, role=Read 전용 | 사용자는 데이터 센터 수준에서 할당되며 데이터 센터의 모든 개체에 대한 액세스 권한이 있습니다.<br/><br/> 액세스를 제한하려는 경우 **자식에 전파** 개체를 사용하여 **액세스 권한 없음** 역할을 자식 개체(vSphere 호스트, 데이터 저장소, 가상 머신 및 네트워크)에 할당합니다.<br/><br/> 전체 복제, 장애 조치, 장애 복구가 아닌 마이그레이션에 유용합니다.|
|**복제/장애 조치(failover)/장애 복구(failback)** | 필요한 권한으로 역할 (AzureSiteRecoveryRole)을 만든 다음 VMware 사용자 또는 그룹에 역할을 할당 하는 것이 좋습니다. | 데이터 센터 개체 –> 자식 개체에 전파, role=AzureSiteRecoveryRole<br/><br/> 데이터 저장소 -&gt; 공간 할당, 데이터 저장소 찾아보기, 낮은 수준 파일 작업, 파일 제거, 가상 머신 파일 업데이트<br/><br/> 네트워크 -> 네트워크 할당<br/><br/> 리소스 -> 리소스 풀에 VM 할당, 전원이 꺼진 VM 마이그레이션, 전원이 켜진 VM 마이그레이션<br/><br/> 태스크 -> 만들기 태스크, 업데이트 태스크<br/><br/> 가상 머신 -&gt; 구성<br/><br/> 가상 머신 -&gt; 상호 작용 -&gt; 질문 응답, 디바이스 연결, CD 미디어 구성, 플로피 미디어 구성, 전원 끄기, 전원 켜기, VMware 도구 설치<br/><br/> 가상 머신 -&gt; 인벤토리 -&gt; 만들기, 등록, 등록 취소<br/><br/> 가상 머신 -&gt; 프로비전 -&gt; 가상 머신 다운로드 허용, 가상 머신 파일 업로드 허용<br/><br/> 가상 머신 -&gt; 스냅샷 -&gt; 스냅샷 제거 | 사용자는 데이터 센터 수준에서 할당되며 데이터 센터의 모든 개체에 대한 액세스 권한이 있습니다.<br/><br/> 액세스를 제한하려는 경우 **자식에 전파** 개체를 사용하여 **액세스 권한 없음** 역할을 자식 개체(vSphere 호스트, 데이터 저장소, 가상 머신 및 네트워크)에 할당합니다.|

## <a name="add-vmware-server-to-the-vault"></a>자격 증명 모음에 VMware 서버 추가

온-프레미스 VMware Vm에 대 한 재해 복구를 설정 하는 경우 다음과 같이 Site Recovery 자격 증명 모음에 Vm을 검색 하는 vCenter Server/vSphere 호스트를 추가 합니다.

1. 자격 증명 모음 > **Site Recovery 인프라** > **구성**서버에서 구성 서버를 엽니다.
1. **세부 정보** 페이지에서 **vCenter**를 클릭 합니다.
1. **Vcenter 추가**에서 vsphere 호스트 또는 vCenter server의 이름을 지정 합니다.
1. 서버의 IP 주소 또는 FQDN을 지정 합니다.
1. VMware 서버가 다른 포트에서 요청을 수신하도록 구성되지 않은 경우 포트를 443으로 그대로 둡니다.
1. VMware vCenter 또는 vSphere ESXi 서버에 연결 하는 데 사용할 계정을 선택 합니다. 그런 후 **OK**를 클릭합니다.

## <a name="modify-credentials"></a>자격 증명 수정

필요한 경우 다음과 같이 vCenter Server/vSphere 호스트에 연결 하는 데 사용 되는 자격 증명을 수정할 수 있습니다.

1. 구성 서버에 로그인합니다.
1. 바탕 화면 바로 가기를 사용 하 여 구성 서버 도구 (_cspsconfigtool .exe_)를 엽니다.
1. **계정 관리** 탭에서 **계정 추가**를 클릭합니다.

   ![add-account](./media/vmware-azure-manage-vcenter/addaccount.png)

1. 새 계정 정보를 입력 하 고 **확인**을 클릭 합니다. 계정에는 [계정 권한](#account-permissions) 테이블에 나열 된 사용 권한이 필요 합니다.
1. 자격 증명 모음 > **Site Recovery 인프라** > **구성**서버에서 구성 서버를 엽니다.
1. **자세히**에서 **서버 새로 고침**을 클릭 합니다.
1. 서버 새로 고침 작업이 완료 되 면 vCenter Server를 선택 합니다.
1. **요약**에서 **VCenter Server/vsphere 호스트 계정**에 새로 추가 된 계정을 선택 하 고 **저장**을 클릭 합니다.

   ![modify-account](./media/vmware-azure-manage-vcenter/modify-vcente-creds.png)

## <a name="delete-a-vcenter-server"></a>vCenter Server 삭제

1. 자격 증명 모음 > **Site Recovery 인프라** > **구성**서버에서 구성 서버를 엽니다.
1. **세부 정보** 페이지에서 vCenter 서버를 선택합니다.
1. **삭제** 단추를 클릭합니다.

   ![delete-account](./media/vmware-azure-manage-vcenter/delete-vcenter.png)

## <a name="modify-the-ip-address-and-port"></a>IP 주소 및 포트 수정

VCenter Server의 IP 주소 또는 서버와 Site Recovery 간에 통신 하는 데 사용 되는 포트를 수정할 수 있습니다. 기본적으로 Site Recovery는 포트 443을 통해 vCenter Server/vSphere 호스트 정보에 액세스 합니다.

1. 자격 증명 모음 > **Site Recovery 인프라** > **구성 서버**에서 vCenter Server 추가 된 구성 서버를 클릭 합니다.
1. **VCenter 서버**에서 수정 하려는 vCenter Server를 클릭 합니다.
1. **요약**에서 IP 주소와 포트를 업데이트 하 고 변경 내용을 저장 합니다.

   ![add_ip_new_vcenter](media/vmware-azure-manage-vcenter/add-ip.png)

1. 변경 내용이 적용 되려면 15 분 동안 기다리거나 [구성 서버를 새로 고 치세요](vmware-azure-manage-configuration-server.md#refresh-configuration-server).

## <a name="migrate-all-vms-to-a-new-server"></a>모든 Vm을 새 서버로 마이그레이션

새 vCenter Server을 사용 하도록 모든 Vm을 마이그레이션하려면 vCenter Server 할당 된 IP 주소만 업데이트 해야 합니다. 중복 된 항목이 생길 수 있으므로 다른 VMware 계정을 추가 하지 마세요. 다음과 같이 주소를 업데이트 합니다.

1. 자격 증명 모음 > **Site Recovery 인프라** > **구성 서버**에서 vCenter Server 추가 된 구성 서버를 클릭 합니다.
1. **VCenter servers** 섹션에서 마이그레이션할 vCenter Server를 클릭 합니다.
1. **요약**에서 새 vCenter Server IP 주소를 업데이트 하 고 변경 내용을 저장 합니다.
1. IP 주소가 업데이트 되는 즉시 Site Recovery 새 vCenter Server에서 VM 검색 정보를 수신 하기 시작 합니다. 진행 중인 복제 작업에는 영향을 주지 않습니다.

## <a name="migrate-a-few-vms-to-a-new-server"></a>몇 개의 Vm을 새 서버로 마이그레이션

복제 하는 Vm 중 일부를 새 vCenter Server 마이그레이션하려면 다음을 수행 합니다.

1. 구성 서버에 새 vCenter Server를 [추가](#add-vmware-server-to-the-vault) 합니다.
1. 새 서버로 이동 하는 Vm에 대해 [복제를 사용 하지 않도록 설정](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) 합니다.
1. VMware에서 Vm을 새 vCenter Server 마이그레이션합니다.
1. 새 vCenter Server을 선택 하 여 마이그레이션된 Vm에 대 한 복제를 다시 [사용 하도록 설정](vmware-azure-tutorial.md#enable-replication) 합니다.

## <a name="migrate-most-vms-to-a-new-server"></a>새 서버로 대부분의 Vm 마이그레이션

새 vCenter Server로 마이그레이션하려는 Vm의 수가 원래 vCenter Server에 남아 있는 Vm의 수보다 높은 경우 다음을 수행 합니다.

1. 구성 서버 설정의 vCenter Server에 할당 된 [IP 주소](#modify-the-ip-address-and-port) 를 새 vCenter Server의 주소로 업데이트 합니다.
1. 이전 서버에 남아 있는 몇 개의 Vm에 대해 [복제를 사용 하지 않도록 설정](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) 합니다.
1. [이전 vCenter Server](#add-vmware-server-to-the-vault) 와 해당 IP 주소를 구성 서버에 추가 합니다.
1. 이전 서버에 남아 있는 Vm에 대 한 [복제를 다시 사용 하도록 설정](vmware-azure-tutorial.md#enable-replication) 합니다.

## <a name="next-steps"></a>다음 단계

문제가 있는 경우 [vCenter Server 검색 오류 문제 해결](vmware-azure-troubleshoot-vcenter-discovery-failures.md)을 참조 하세요.
