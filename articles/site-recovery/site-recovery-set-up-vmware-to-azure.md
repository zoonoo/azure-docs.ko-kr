---
title: "원본 환경 설정(Azure-VMware) | Microsoft Docs"
description: "이 문서에서는 온-프레미스 환경을 설정하여 VMware 가상 컴퓨터를 Azure에 복제하기 시작하는 방법을 설명합니다."
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
ms.date: 1/10/2017
ms.author: anoopkv
translationtype: Human Translation
ms.sourcegitcommit: 9dfcdeb4dc3e84089eb8031272b870f87d90b689
ms.openlocfilehash: 2256ba395e8d5f7a1ce7d4d78168a9cc51d4f074

---

# <a name="set-up-the-source-environment-vmware-to-azure"></a>원본 환경 설정(Azure-VMware)
> [!div class="op_single_selector"]
> * [VMware Virtual Machines](./site-recovery-set-up-vmware-to-azure.md)
> * [물리적 서버](./site-recovery-set-up-physical-to-azure.md)

이 문서에서는 온-프레미스 환경을 설정하여 VMware에서 실행 중인 가상 컴퓨터를 Azure에 복제하기 시작하는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 조건

이 문서에서는 사용자가 다음 항목을 이미 만들었다고 가정합니다.
1. Recovery Services 자격 증명 모음 [Azure Portal](http://portal.azure.com "Azure portal")
2. [자동 검색](./site-recovery-vmware-to-azure.md#vmware-account-permissions)에 사용할 수 있는 VMware vCenter의 전용 계정
3. 구성 서버를 설치하는 가상 컴퓨터

### <a name="configuration-server-minimum-requirements"></a>구성 서버 최소 요구 사항
**항상 사용 가능한** VMware 가상 컴퓨터에 구성 서버 소프트웨어를 배포해야 합니다. 다음 테이블에서는 구성 서버에 대한 최소 하드웨어, 소프트웨어 및 네트워크 요구 사항을 나열합니다.
[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-server-requirements.md)]

> [!NOTE]
> HTTPS 기반 프록시 서버가 구성 서버에서 지원되지 않습니다.

## <a name="choose-your-protection-goals"></a>보호 목표 선택

1. Azure Portal에서 **Recovery Services** 자격 증명 모음 블레이드를 탐색하고 사용자 자격 증명 모음을 선택합니다.
2. 자격 증명 모음의 리소스 메뉴에서 **시작** > **Site Recovery** > **1단계: 인프라 준비** > **보호 목표**를 클릭합니다.

    ![목표 선택](./media/site-recovery-set-up-vmware-to-azure/choose-goals.png)
3. **보호 목표**에서 **Azure에**를 선택하고 **예, VMware vSphere 하이퍼바이저 사용**을 선택합니다. 그런 후 **OK**를 클릭합니다.

    ![목표 선택](./media/site-recovery-set-up-vmware-to-azure/choose-goals2.png)

## <a name="set-up-the-source-environment"></a>원본 환경 설정
두 가지 주요 작업을 포함한 원본 환경 설정

1. Site Recovery 서비스를 사용하여 구성 서버 설치 및 등록
2. Azure Site Recovery를 온-프레미스 VMware vCenter 또는 vSphere EXSi 호스트에 연결하여 온-프레미스 가상 컴퓨터 검색

### <a name="step-1-install--register-a-configuration-server"></a>1단계: 구성 서버 설치 및 등록

1. **1단계: 인프라 준비** > **원본**을 클릭합니다. **원본 준비**에서 구성 서버가 없는 경우 **+구성 서버**를 클릭하여 하나를 추가합니다.

    ![원본 설정](./media/site-recovery-set-up-vmware-to-azure/set-source1.png)
2. **서버 추가** 블레이드에서 **구성 서버**가 **서버 형식**에 표시되는지 확인합니다.
4. Site Recovery 통합 설치 프로그램 설치 파일을 다운로드합니다.
5. 자격 증명 모음 등록 키를 다운로드합니다. 통합 설치 프로그램을 실행하는 경우 등록 키가 필요합니다. 이 키는 생성된 날로부터 **5**일간 유효합니다.

    ![원본 설정](./media/site-recovery-set-up-vmware-to-azure/set-source2.png)
6. 구성 서버로 사용 중인 컴퓨터에서 **Azure Site Recovery 통합 설치 프로그램**을 실행하여 구성 서버, 프로세스 서버 및 마스터 대상 서버를 설치합니다.

#### <a name="running-the-azure-site-recovery-unified-setup"></a>Azure Site Recovery 통합 설치 프로그램 실행

> [!TIP]
> 사용자 컴퓨터 시스템 클록의 시간이 현지 시간보다&5;분 이상 빠르거나 느린 경우 구성 서버 등록에 실패합니다. 설치를 시작하기 전에 시스템 클록을 [시간 서버](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service)와 동기화합니다.

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> 명령줄을 통해 구성 서버를 설치할 수 있습니다. 자세한 내용은 [명령줄 도구를 사용하여 구성 서버 설치](http://aka.ms/installconfigsrv)를 참조하세요.

#### <a name="add-the-vmware-account-for-automatic-discovery"></a>자동 검색에 VMware 계정 추가

[!INCLUDE [site-recovery-add-vcenter-account](../../includes/site-recovery-add-vcenter-account.md)]

### <a name="step-2-add-a-vcenter"></a>2단계: vCenter 추가
Azure Site Recovery가 온-프레미스 환경에서 실행 중인 가상 컴퓨터를 검색할 수 있게 하려면 Site Recovery와 VMware vCenter 서버 또는 vSphere ESXi 호스트를 연결해야 합니다.

+vCenter 단추를 클릭하여 VMware vCenter 서버 또는 VMware vSphere ESXi 호스트를 연결하기 시작합니다.

[!INCLUDE [site-recovery-add-vcenter](../../includes/site-recovery-add-vcenter.md)]


## <a name="common-issues"></a>일반적인 문제
[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]


## <a name="next-steps"></a>다음 단계
다음 단계는 Azure에서 [대상 환경 설정](./site-recovery-vmware-to-azure.md#step-3-set-up-the-target-environment)을 포함합니다.



<!--HONumber=Jan17_HO2-->


