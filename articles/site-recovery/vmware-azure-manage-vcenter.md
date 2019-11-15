---
title: Azure Site Recovery에서 VMware vCenter 서버 관리
description: 이 문서에서는 Azure Site Recovery를 사용하여 Azure로 VMware VM의 재해 복구를 위해 VMware vCenter를 추가 및 관리하는 방법을 설명합니다.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: ramamill
ms.openlocfilehash: 8f339103f67f37d10999ef43fa57a6eb27b60f37
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083971"
---
# <a name="manage-vmware-vcenter-server"></a>VMware vCenter 서버 관리

이 문서는 VMware vCenter에서 수행될 수 있는 다양한 Site Recovery 작업을 설명합니다. 시작하기 전에 [필수 구성 요소](vmware-physical-azure-support-matrix.md#replicated-machines)를 확인합니다.


## <a name="set-up-an-account-for-automatic-discovery"></a>자동 검색용 계정 설정

Site Recovery는 가상 머신 자동 검색 및 가상 머신의 장애 조치와 장애 복구를 위해 프로세스 서버의 VMware에 대한 액세스 권한이 필요합니다. 다음과 같이 액세스용 계정을 만듭니다.

1. 구성 서버 컴퓨터에 로그온합니다.
2. 바탕 화면 바로 가기를 사용하여 cspsconfigtool.exe를 시작합니다.
3. **계정 관리** 탭에서 **계정 추가**를 클릭합니다.

   ![add-account](./media/vmware-azure-manage-vcenter/addaccount.png)
1. 계정 세부 정보를 입력하고 **확인**을 클릭하여 계정을 추가합니다.  계정에는 다음 표에 요약된 권한이 있어야 합니다. 

계정 정보가 Site Recovery 서비스와 동기화되는 데 약 15분 정도가 걸립니다.

### <a name="account-permissions"></a>계정 권한

|**작업** | **계정** | **Permissions** | **세부 정보**|
|--- | --- | --- | ---|
|**자동 검색/마이그레이션(장애 복구(failback) 없이)** | 최소한 읽기 전용 사용자 필요 | 데이터 센터 개체 –> 자식 개체에 전파, role=Read 전용 | 사용자는 데이터 센터 수준에서 할당되며 데이터 센터의 모든 개체에 대한 액세스 권한이 있습니다.<br/><br/> 액세스를 제한하려는 경우 **자식에 전파** 개체를 사용하여 **액세스 권한 없음** 역할을 자식 개체(vSphere 호스트, 데이터 저장소, 가상 머신 및 네트워크)에 할당합니다.|
|**복제/장애 조치(failover)** | 최소한 읽기 전용 사용자 필요| 데이터 센터 개체 –> 자식 개체에 전파, role=Read 전용 | 사용자는 데이터 센터 수준에서 할당되며 데이터 센터의 모든 개체에 대한 액세스 권한이 있습니다.<br/><br/> 액세스를 제한하려는 경우 **자식에 전파** 개체를 사용하여 **액세스 권한 없음** 역할을 자식 개체(vSphere 호스트, 데이터 저장소, 가상 머신 및 네트워크)에 할당합니다.<br/><br/> 전체 복제, 장애 조치, 장애 복구가 아닌 마이그레이션에 유용합니다.|
|**복제/장애 조치(failover)/장애 복구(failback)** | 필요한 권한과 함께 역할(AzureSiteRecoveryRole)을 만든 후 역할을 VMware 사용자 또는 그룹으로 할당하는 것이 좋습니다. | 데이터 센터 개체 –> 자식 개체에 전파, role=AzureSiteRecoveryRole<br/><br/> 데이터 저장소 -&gt; 공간 할당, 데이터 저장소 찾아보기, 낮은 수준 파일 작업, 파일 제거, 가상 머신 파일 업데이트<br/><br/> 네트워크 -> 네트워크 할당<br/><br/> 리소스 -> 리소스 풀에 VM 할당, 전원이 꺼진 VM 마이그레이션, 전원이 켜진 VM 마이그레이션<br/><br/> 태스크 -> 만들기 태스크, 업데이트 태스크<br/><br/> 가상 머신 -&gt; 구성<br/><br/> 가상 머신 -&gt; 상호 작용 -&gt; 질문 응답, 디바이스 연결, CD 미디어 구성, 플로피 미디어 구성, 전원 끄기, 전원 켜기, VMware 도구 설치<br/><br/> 가상 머신 -&gt; 인벤토리 -&gt; 만들기, 등록, 등록 취소<br/><br/> 가상 컴퓨터 -> 프로비전 -> 가상 컴퓨터 다운로드 허용, 가상 컴퓨터 파일 업로드 허용<br/><br/> 가상 머신 -&gt; 스냅샷 -&gt; 스냅샷 제거 | 사용자는 데이터 센터 수준에서 할당되며 데이터 센터의 모든 개체에 대한 액세스 권한이 있습니다.<br/><br/> 액세스를 제한하려는 경우 **자식에 전파** 개체를 사용하여 **액세스 권한 없음** 역할을 자식 개체(vSphere 호스트, 데이터 저장소, 가상 머신 및 네트워크)에 할당합니다.|


## <a name="add-vmware-server-to-the-vault"></a>자격 증명 모음에 VMware 서버 추가

1. Azure Portal에서 자격 증명 모음 > **Site Recovery 인프라** > **구성 서버**를 열고 구성 서버를 엽니다.
2. **세부 정보** 페이지에서 **+vCenter**를 클릭합니다.

[!INCLUDE [site-recovery-add-vcenter](../../includes/site-recovery-add-vcenter.md)]

## <a name="modify-credentials"></a>자격 증명 수정

다음과 같이 vCenter 서버 또는 ESXi 호스트에 연결하는 데 사용되는 자격 증명을 수정합니다.

1. 구성 서버에 로그인하고, 바탕 화면에서 cspsconfigtool.exe를 시작합니다.
2. **계정 관리** 탭에서 **계정 추가**를 클릭합니다.

   ![add-account](./media/vmware-azure-manage-vcenter/addaccount.png)
3. 새 계정의 세부 정보를 입력하고 **확인**을 클릭하여 계정을 추가합니다. 계정에는 [위](#account-permissions)에 나열된 권한이 있어야 합니다.
4. Azure Portal에서 자격 증명 모음 > **Site Recovery 인프라** > **구성 서버**를 열고 구성 서버를 엽니다.
5. **세부 정보** 페이지에서 **서버 새로 고침**을 클릭합니다.
6. 서버 새로 고침 작업이 완료되면 vCenter Server를 선택하여 vCenter **요약** 페이지를 엽니다.
7. **vCenter 서버/vSphere 호스트 계정** 필드에서 새로 추가된 계정을 선택하고 **저장**을 클릭합니다.

   ![modify-account](./media/vmware-azure-manage-vcenter/modify-vcente-creds.png)

## <a name="delete-a-vcenter-server"></a>vCenter 서버 삭제

1. Azure Portal에서 자격 증명 모음 > **Site Recovery 인프라** > **구성 서버**를 열고 구성 서버를 엽니다.
2. **세부 정보** 페이지에서 vCenter 서버를 선택합니다.
3. **삭제** 단추를 클릭합니다.

   ![delete-account](./media/vmware-azure-manage-vcenter/delete-vcenter.png)

## <a name="modify-the-vcenter-ip-address-and-port"></a>VCenter IP 주소 및 포트 수정

1. Azure 포털에 로그인합니다.
2. **Recovery Services 자격 증명 모음** > **Site Recovery 인프라** > **구성 서버**로 이동 합니다.
3. VCenter가 할당 된 구성 서버를 클릭 합니다.
4. **Vcenter servers** 섹션에서 수정 하려는 vcenter를 클릭 합니다.
5. VCenter 요약 페이지에서 각 필드에 있는 vCenter의 IP 주소와 포트를 업데이트 한 후 변경 내용을 저장 합니다.

   ![add_ip_new_vcenter](media/vmware-azure-manage-vcenter/add-ip.png)

6. 변경 내용이 적용 되려면 15 분 동안 기다리거나 [구성 서버를 새로 고 치세요](vmware-azure-manage-configuration-server.md#refresh-configuration-server).

## <a name="migrate-all-protected-virtual-machines-to-a-new-vcenter"></a>모든 보호 된 가상 컴퓨터를 새 vCenter로 마이그레이션

모든 가상 컴퓨터를 새 vCenter로 마이그레이션하려면 다른 vCenter 계정을 추가 하지 마십시오. 이로 인해 중복 된 항목이 발생할 수 있습니다. 새 vCenter의 IP 주소를 업데이트 하면 됩니다.

1. Azure 포털에 로그인합니다.
2. **Recovery Services 자격 증명 모음** > **Site Recovery 인프라** > **구성 서버**로 이동 합니다.
3. 이전 vCenter가 할당 된 구성 서버를 클릭 합니다.
4. **Vcenter servers** 섹션에서 마이그레이션할 계획인 vcenter를 클릭 합니다.
5. VCenter 요약 페이지에서 **vcenter server/vSphere 호스트 이름 또는 ip 주소**필드에서 새 VCENTER의 IP 주소를 업데이트 합니다. 변경 내용을 저장합니다.

IP 주소가 업데이트 되는 즉시 Site Recovery 구성 요소는 새 vCenter에서 가상 컴퓨터의 검색 정보를 수신 하기 시작 합니다. 이는 진행 중인 복제 작업에 영향을 주지 않습니다.

## <a name="migrate-few-protected-virtual-machines-to-a-new-vcenter"></a>보호 된 가상 컴퓨터를 새 vCenter로 마이그레이션

> [!NOTE]
> 이 섹션은 보호 된 가상 머신 중 일부를 새 vCenter로 마이그레이션하는 경우에만 적용할 수 있습니다. 새 vCenter에서 새 가상 컴퓨터 집합을 보호 하려면 새 [vcenter 세부 정보를 구성 서버에 추가](#add-vmware-server-to-the-vault) 하 고 **[보호 사용](vmware-azure-tutorial.md#enable-replication)** 으로 시작 합니다.

몇 개의 가상 컴퓨터를 새 vCenter로 이동 하려면 다음을 수행 합니다.

1. [새 vCenter 세부 정보를 구성 서버에 추가](#add-vmware-server-to-the-vault)합니다.
2. 마이그레이션하려는 [가상 컴퓨터의 복제를 사용 하지 않도록 설정](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) 합니다.
3. 새 vCenter로 선택한 가상 컴퓨터의 마이그레이션을 완료 합니다.
4. 이제 [보호를 사용 하도록 설정할 때 새 vCenter를 선택](vmware-azure-tutorial.md#enable-replication)하 여 마이그레이션된 가상 컴퓨터를 보호 합니다.

> [!TIP]
> 마이그레이션하는 가상 컴퓨터 수가 이전 vCenter에 보관 된 가상 컴퓨터의 수를 **더 많이** 사용 하는 경우 여기에 제공 된 지침을 사용 하 여 새 VCENTER의 IP 주소를 업데이트 합니다. 이전 vCenter에 유지 되는 일부 가상 컴퓨터의 경우 복제를 [사용 하지 않도록 설정](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure)합니다. [새 vCenter 세부 정보를 구성 서버에 추가](#add-vmware-server-to-the-vault)하 고 **[보호 사용](vmware-azure-tutorial.md#enable-replication)** 을 시작 합니다.

## <a name="frequently-asked-questions"></a>질문과 대답

1. 보호 된 가상 컴퓨터가 한 ESXi 호스트에서 다른 호스트로 이동 되 면 복제에 영향을 미칩니까?

    아니요, 진행 중인 복제에는 영향을 주지 않습니다. 그러나 [충분 한 권한이 있는 마스터 대상 서버를 배포 해야 합니다](vmware-azure-reprotect.md#deploy-a-separate-master-target-server) .

2. VCenter와 기타 Site Recovery 구성 요소 간 통신에 사용 되는 포트 번호는 무엇입니까?

    기본 포트는 443입니다. 구성 서버는이 포트를 통해 vCenter/vSphere 호스트 정보에 액세스 합니다. 이 정보를 업데이트 하려면 [여기](#modify-the-vcenter-ip-address-and-port)를 클릭 하세요.
