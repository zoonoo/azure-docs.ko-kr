---
title: " Azure Site Recovery에서 VMware vCenter 서버 관리 | Microsoft Docs"
description: "이 문서에서는 Azure Site Recovery에서 VMware vCenter를 추가 및 관리하는 방법을 설명합니다."
services: site-recovery
documentationcenter: 
author: AnoopVasudavan
manager: gauravd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: backup-recovery
ms.date: 12/04/2017
ms.author: anoopkv
ms.openlocfilehash: 2dcca54c9e1e9330bf7c2a37dc08f67817b6af47
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2017
---
# <a name="manage-vmware-vcenter-server-in-azure-site-recovery"></a>Azure Site Recovery에서 VMware vCenter 서버 관리
이 문서는 VMware vCenter에서 수행될 수 있는 다양한 Site Recovery 작업을 설명합니다.

## <a name="prerequisites"></a>필수 조건

**VMware vCenter 및 VMware vSphere ESX 호스트 지원** | **세부 정보** |
|--- | --- |
|**온-프레미스 VMware 서버** | 최신 업데이트가 포함된 6.5, 6.0, 5.5를 실행하는 하나 이상의 VMware vSphere 서버. 서버는 구성 서버(또는 별도의 프로세스 서버)와 동일한 네트워크에 있어야 합니다.<br/><br/> 호스트를 관리하는 vCenter 서버를 두는 것이 좋습니다(최신 업데이트가 포함된 6.5, 6.0 또는 5.5 실행). 버전 6.0을 배포하는 경우 5.5에서 사용할 수 있는 기능만 지원됩니다.|

## <a name="prepare-an-account-for-automatic-discovery"></a>자동 검색용 계정 준비
Site Recovery는 가상 컴퓨터 자동 검색 및 가상 컴퓨터의 장애 조치와 장애 복구를 위해 프로세스 서버의 VMware에 대한 액세스 권한이 필요합니다.

* **마이그레이션**: 장애 복구를 수행하지 않고 가상 컴퓨터를 Azure에 마이그레이션하려는 경우 읽기 전용 역할이 있는 VMware 계정을 사용할 수 있습니다. 이러한 역할은 장애 조치(failover)를 실행할 수 있지만 보호된 원본 컴퓨터를 종료할 수는 없습니다. 마이그레이션에서는 필요하지 않습니다.
* **복제/복구**: 전체 복제(복제, 장애 조치, 장애 복구)를 배포하려는 경우 해당 계정은 디스크 작성 및 제거, 가상 컴퓨터 전원 켜기 등의 작업을 실행할 수 있어야 합니다.
* **자동 검색**: 최소한 읽기 전용 계정이 필요합니다.


|**작업** | **필요한 계정/역할** | **권한** | **세부 정보**|
|--- | --- | --- | ---|
|**프로세스 서버가 VMware 가상 컴퓨터를 자동으로 검색** | 최소한 읽기 전용 사용자 필요 | 데이터 센터 개체 –> 자식 개체에 전파, role=Read 전용 | 사용자는 데이터 센터 수준에서 할당되며 데이터 센터의 모든 개체에 대한 액세스 권한이 있습니다.<br/><br/> 액세스를 제한하려는 경우 **자식에 전파** 개체를 사용하여 **액세스 권한 없음** 역할을 자식 개체(vSphere 호스트, 데이터 저장소, 가상 컴퓨터 및 네트워크)에 할당합니다.|
|**장애 조치(Failover)** | 최소한 읽기 전용 사용자 필요 | 데이터 센터 개체 –> 자식 개체에 전파, role=Read 전용 | 사용자는 데이터 센터 수준에서 할당되며 데이터 센터의 모든 개체에 대한 액세스 권한이 있습니다.<br/><br/> 액세스를 제한하려는 경우 **자식에 전파** 개체를 사용하여 **액세스 권한 없음** 역할을 자식 개체(vSphere 호스트, 데이터 저장소, 가상 컴퓨터 및 네트워크)에 할당합니다.<br/><br/> 전체 복제, 장애 조치, 장애 복구가 아닌 마이그레이션에 유용합니다.|
|**장애 조치 및 장애 복구** | 필요한 권한과 함께 역할(AzureSiteRecoveryRole)을 만든 후 역할을 VMware 사용자 또는 그룹으로 할당하는 것이 좋습니다. | 데이터 센터 개체 –> 자식 개체에 전파, role=AzureSiteRecoveryRole<br/><br/> 데이터 저장소 -> 공간 할당, 데이터 저장소 찾아보기, 낮은 수준 파일 작업, 파일 제거, 가상 컴퓨터 파일 업데이트<br/><br/> 네트워크 -> 네트워크 할당<br/><br/> 리소스 -> 리소스 풀에 VM 할당, 전원이 꺼진 VM 마이그레이션, 전원이 켜진 VM 마이그레이션<br/><br/> 태스크 -> 만들기 태스크, 업데이트 태스크<br/><br/> 가상 컴퓨터 -> 구성<br/><br/> 가상 컴퓨터 -> 상호 작용 -> 질문 응답, 장치 연결, CD 미디어 구성, 플로피 미디어 구성, 전원 끄기, 전원 켜기, VMware 도구 설치<br/><br/> 가상 컴퓨터 -> 인벤토리 -> 만들기, 등록, 등록 취소<br/><br/> 가상 컴퓨터 -> 프로비전 -> 가상 컴퓨터 다운로드 허용, 가상 컴퓨터 파일 업로드 허용<br/><br/> 가상 컴퓨터 -> 스냅숏 -> 스냅숏 제거 | 사용자는 데이터 센터 수준에서 할당되며 데이터 센터의 모든 개체에 대한 액세스 권한이 있습니다.<br/><br/> 액세스를 제한하려는 경우 **자식에 전파** 개체를 사용하여 **액세스 권한 없음** 역할을 자식 개체(vSphere 호스트, 데이터 저장소, 가상 컴퓨터 및 네트워크)에 할당합니다.|

## <a name="create-an-account-to-connect-to-vmware-vcenter-server-vmware-vsphere-exsi-host"></a>VMware vCenter 서버/ VMware vSphere EXSi 호스트에 연결할 계정 만들기
1. 바탕 화면에 있는 바로 가기를 사용하여 구성 서버에 로그인하고 cspsconfigtool.exe를 시작합니다.
2. **계정 관리** 탭에서 **계정 추가**를 클릭합니다.

  ![add-account](./media/site-recovery-vmware-to-azure-manage-vcenter/addaccount.png)
3. 계정 세부 정보를 제공하고 확인을 클릭하여 계정을 추가합니다. 계정은 [자동 검색용 계정 준비](#prepare-an-account-for-automatic-discovery) 섹션에 나열된 권한이 있어야 합니다.

  >[!NOTE]
  계정 정보가 Site Recovery 서비스와 동기화되는 데 약 15분 정도가 걸립니다.


## <a name="associate-a-vmware-vcenter-vmware-vsphere-esx-host-add-vcenter"></a>VMware vCenter/VMware vSphere ESX 호스트 연결(vCenter 추가)
* Azure Portal에서 *YourRecoveryServicesVault* > **Site Recovery 인프라** > **구성 서버** > *ConfigurationServer* 찾기
* 구성 서버의 세부 정보 페이지에서 +vCenter 단추를 클릭합니다.

[!INCLUDE [site-recovery-add-vcenter](../../includes/site-recovery-add-vcenter.md)]

## <a name="modify-credentials-used-to-connect-to-the-vcenter-server-vsphere-esxi-host"></a>vCenter server/vSphere ESXi 호스트에 연결하는 데 사용되는 자격 증명 수정

1. 구성 서버에 로그인하고 cspsconfigtool.exe 시작
2. **계정 관리** 탭에서 **계정 추가**를 클릭합니다.

  ![add-account](./media/site-recovery-vmware-to-azure-manage-vcenter/addaccount.png)
3. 새 계정 세부 정보를 제공하고 확인을 클릭하여 계정을 추가합니다. 계정은 [자동 검색용 계정 준비](#prepare-an-account-for-automatic-discovery) 섹션에 나열된 권한이 있어야 합니다.
4. Azure Portal에서 *YourRecoveryServicesVault* > **Site Recovery 인프라** > **구성 서버** > *ConfigurationServer* 찾기
5. 구성 서버의 세부 정보 페이지에서 **서버 새로 고침** 단추를 클릭합니다.
6. 새로 고침 서버 작업이 완료되면 vCenter 서버를 선택하여 vCenter 요약 페이지를 엽니다.
7. **vCenter 서버/vSphere 호스트 계정** 필드에서 새로 추가된 계정을 선택하고 **저장** 단추를 클릭합니다.

  ![modify-account](./media/site-recovery-vmware-to-azure-manage-vcenter/modify-vcente-creds.png)

## <a name="delete-a-vcenter-in-azure-site-recovery"></a>Azure Site Recovery에서 vCenter 삭제
1. Azure Portal에서 *YourRecoveryServicesVault* > **Site Recovery 인프라** > **구성 서버** > *ConfigurationServer* 찾기
2. 구성 서버의 세부 정보 페이지에서 vCenter 서버를 선택하여 vCenter 요약 페이지를 엽니다.
3. **삭제** 단추를 클릭하여 vCenter를 삭제합니다.

  ![delete-account](./media/site-recovery-vmware-to-azure-manage-vcenter/delete-vcenter.png)

> [!NOTE]
vCenter의 IP 주소/FQDN, Port 포트 세부 정보를 수정해야 하는 경우 vCenter 서버를 삭제하고 다시 추가해야 합니다.
