---
title: "Azure Site Recovery를 사용하여 Azure에 복제할 온-프레미스 VMware 리소스 준비 | Microsoft Docs"
description: "VMware VM에서 실행되는 워크로드를 Azure Storage에 복제하는 데 필요한 단계를 요약합니다."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 6aba0e89-ad7c-467e-9db2-cfb3bfe4c7d6
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.openlocfilehash: 3e1c589030210c2eae1ad9c02811775d9d6365d4
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2017
---
# <a name="step-6-prepare-on-premises-vmware-replication-to-azure"></a>6단계: Azure에 온-프레미스 VMware 복제 준비

이 문서의 지침을 사용하여 Azure Site Recovery와 상호작용하도록 온-프레미스 VMware 서버를 준비하고 모바일 서비스를 설치하기 위해 VMWare VM을 준비합니다. Azure에 복제하려는 모든 온-프레미스 VM에 모바일 서비스 에이전트를 설치해야 합니다.

## <a name="prepare-for-automatic-discovery"></a>자동 검색 준비

Site Recovery는 vSphere ESXi 호스트에서 실행 중인 VM을 자동으로 검색합니다(vCenter 서버 포함 또는 제외). 자동 검색의 경우 Site Recovery는 호스트 및 서버에 액세스할 계정이 필요합니다.

1. 전용 계정을 사용하려면 역할을 만듭니다(vCenter 수준에서, 아래 테이블에 설명한 사용 권한 사용). 이름을 **Azure_Site_Recovery**와 같이 지정합니다.
2. 그 다음, vSphere 호스트/vCenter 서버에 사용자를 만들고 이 사용자에게 역할을 할당합니다. Site Recovery를 배포하는 동안 이 사용자 계정을 지정합니다.


### <a name="vmware-account-permissions"></a>VMware 계정 권한

Site Recovery는 VM 자동 검색 및 VM의 장애 조치와 장애 복구를 위해 프로세스 서버의 VMware에 대한 액세스 권한이 필요합니다.

- **마이그레이션**: 장애 복구를 수행하지 않고 VM을 Azure에 마이그레이션하려는 경우 읽기 전용 역할이 있는 VMware 계정을 사용할 수 있습니다. 이러한 역할은 장애 조치(failover)를 실행할 수 있지만 보호된 원본 컴퓨터를 종료할 수는 없습니다. 마이그레이션에서는 필요하지 않습니다.
- **복제/복구**: 전체 복제(복제, 장애 조치, 장애 복구)를 배포하려는 경우 해당 계정은 디스크 작성 및 제거, VM 전원 켜기 등의 작업을 실행할 수 있어야 합니다.
- **자동 검색**: 최소한 읽기 전용 계정이 필요합니다.


**Task** | **필요한 계정/역할** | **권한** | **세부 정보**
--- | --- | --- | ---
**프로세스 서버가 VMware VM을 자동으로 검색** | 최소한 읽기 전용 사용자 필요 | 데이터 센터 개체 –> 자식 개체에 전파, role=Read 전용 | 사용자는 데이터 센터 수준에서 할당되며 데이터 센터의 모든 개체에 대한 액세스 권한이 있습니다.<br/><br/> 액세스를 제한하려는 경우 **자식에 전파** 개체를 사용하여 **액세스 권한 없음** 역할을 자식 개체(vSphere 호스트, 데이터 저장소, VM 및 네트워크)에 할당합니다.
**장애 조치(Failover)** | 최소한 읽기 전용 사용자 필요 | 데이터 센터 개체 –> 자식 개체에 전파, role=Read 전용 | 사용자는 데이터 센터 수준에서 할당되며 데이터 센터의 모든 개체에 대한 액세스 권한이 있습니다.<br/><br/> 액세스를 제한하려는 경우 **자식에 전파** 개체를 사용하여 **액세스 권한 없음** 역할을 자식 개체(vSphere 호스트, 데이터 저장소, VM 및 네트워크)에 할당합니다.<br/><br/> 전체 복제, 장애 조치, 장애 복구가 아닌 마이그레이션에 유용합니다.
**장애 조치 및 장애 복구** | 필요한 권한과 함께 역할(Azure_Site_Recovery)을 만든 후 역할을 VMware 사용자 또는 그룹으로 할당하는 것이 좋습니다. | 데이터 센터 개체 –> 자식 개체에 전파, role=Azure_Site_Recovery<br/><br/> 데이터 저장소 -> 공간 할당, 데이터 저장소 찾아보기, 낮은 수준 파일 작업, 파일 제거, 가상 컴퓨터 파일 업데이트<br/><br/> 네트워크 -> 네트워크 할당<br/><br/> 리소스 -> 리소스 풀에 VM 할당, 전원이 꺼진 VM 마이그레이션, 전원이 켜진 VM 마이그레이션<br/><br/> 태스크 -> 만들기 태스크, 업데이트 태스크<br/><br/> 가상 컴퓨터 -> 구성<br/><br/> 가상 컴퓨터 -> 상호 작용 -> 질문 응답, 장치 연결, CD 미디어 구성, 플로피 미디어 구성, 전원 끄기, 전원 켜기, VMware 도구 설치<br/><br/> 가상 컴퓨터 -> 인벤토리 -> 만들기, 등록, 등록 취소<br/><br/> 가상 컴퓨터 -> 프로비전 -> 가상 컴퓨터 다운로드 허용, 가상 컴퓨터 파일 업로드 허용<br/><br/> 가상 컴퓨터 -> 스냅숏 -> 스냅숏 제거 | 사용자는 데이터 센터 수준에서 할당되며 데이터 센터의 모든 개체에 대한 액세스 권한이 있습니다.<br/><br/> 액세스를 제한하려는 경우 **자식에 전파** 개체를 사용하여 **액세스 권한 없음** 역할을 자식 개체(vSphere 호스트, 데이터 저장소, VM 및 네트워크)에 할당합니다.


## <a name="prepare-for-push-installation-of-the-mobility-service"></a>모바일 서비스의 푸시 설치 준비

복제하려는 모든 VM에 모바일 서비스가 설치되어야 합니다. 수동 설치, Site Recovery 프로세스 서버에서 푸시 설치 및 System Center Configuration Manager와 같은 메서드를 사용한 설치를 비롯하여 서비스를 설치하는 여러 가지 방법이 있습니다.

푸시 설치를 사용하려는 경우 Site Recovery가 VM에 액세스하는 데 사용할 수 있는 계정을 준비해야 합니다.

- 도메인 또는 로컬 계정을 사용할 수 있습니다.
- Windows에서는 도메인 계정을 사용하지 않는 경우 로컬 컴퓨터에서 원격 사용자 액세스 제어를 사용하지 않도록 설정해야 합니다. 그러려면 레지스터의 **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**에서 값이 1인 **LocalAccountTokenFilterPolicy** DWORD 항목을 추가합니다.
- CLI에서 Windows의 레지스트리 항목을 추가하려면 다음을 입력합니다. ``REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1.``
- Linux에서 계정은 원본 Linux 서버의 루트여야 합니다.



## <a name="next-steps"></a>다음 단계

[7단계: 자격 증명 모음 만들기](vmware-walkthrough-create-vault.md)로 이동합니다.
