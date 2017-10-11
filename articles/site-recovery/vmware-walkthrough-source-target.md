---
title: "Azure Site Recovery를 사용하여 Azure에 VMware 복제를 위한 소스 및 대상 설정 | Microsoft Docs"
description: "Azure Site Recovery를 사용하여 Azure Storage에 VMware VM의 복제를 위한 원본 및 대상 설정을 설정하는 단계를 요약"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: d99e422e-daf7-4fa8-af3c-af2340340136
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.openlocfilehash: 94b629a62c3a54eee69ee397b2f27e3f20b753d5
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2017
---
# <a name="step-8-set-up-the-source-and-target-for-vmware-replication-to-azure"></a>8단계: Azure에 VMware 복제를 위한 원본 및 대상

이 문서에서는 Azure Portal에서 [Azure Site Recovery](site-recovery-overview.md) 서비스를 사용하여 Azure에 온-프레미스 VMware 가상 컴퓨터를 복제할 때 소스 및 대상 설정을 구성하는 방법을 설명합니다.

이 문서의 하단 또는 [Azure Recovery Services 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)에서 의견이나 질문을 게시합니다.


## <a name="set-up-the-source-environment"></a>원본 환경 설정

구성 서버를 설정하고 자격 증명 모음에 등록한 후 VM을 검색합니다.

1. **Site Recovery** > **1단계: 인프라 준비** > **원본**을 클릭합니다.
2. 구성 서버가 없는 경우 **+구성 서버**를 클릭합니다.
3. **서버 추가**에서 **구성 서버**가 **서버 형식**에 표시되는지 확인합니다.
4. Site Recovery 통합 설치 프로그램 설치 파일을 다운로드합니다.
5. 자격 증명 모음 등록 키를 다운로드합니다. 통합 설치를 실행할 때 이 키가 필요합니다. 이 키는 생성된 날로부터 5일간 유효합니다.

   ![원본 설정](./media/vmware-walkthrough-source-target/set-source2.png)


## <a name="register-the-configuration-server-in-the-vault"></a>자격 증명 모음에 구성 서버 등록

다음을 수행하여 시작하기 전에 통합 설치 프로그램을 실행하여 구성 서버, 프로세스 서버 및 마스터 대상 서버를 설치합니다.
    - 간단한 동영상 개요 보기

        > [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video1-Source-Infrastructure-Setup/player]

    - 구성 서버 VM에서 시스템 시계가 [시간 서버](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service)와 동기화되었는지 확인합니다. 서로 일치해야 합니다. 15분 빠르거나 늦은 경우 설치가 실패할 수 있습니다.
    - 구성 서버 VM에서 로컬 관리자로 설치 프로그램을 실행합니다.
    - TLS 1.0이 VM에서 활성화되어 있는지 확인합니다.


[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> [명령줄](http://aka.ms/installconfigsrv)을 통해 구성 서버를 설치할 수도 있습니다.



## <a name="connect-to-vmware-servers"></a>VMware 서버에 연결

Azure Site Recovery가 온-프레미스 환경에서 실행 중인 가상 컴퓨터를 검색할 수 있게 하려면 Site Recovery와 VMware vCenter 서버 또는 vSphere ESXi 호스트를 연결해야 합니다. 시작하기 전에 다음을 기억하세요.

- 서버에 관리자 권한이 없는 계정으로 Site Recovery에 vCenter 서버 또는 vSphere 호스트를 추가하는 경우 계정에서
    - 데이터 센터, 데이터 저장소, 폴더, 호스트, 네트워크, 리소스, 가상 컴퓨터, vSphere 분산 스위치 등의 권한을 사용할 수 있도록 설정해야 합니다.
    - vCenter 서버에는 Storage 보기 권한이 필요합니다.
- Site Recovery에 VMware 서버를 추가하는 경우 포털에 나타나려면 15분 이상 걸릴 수 있습니다.

### <a name="add-the-account-for-automatic-discovery"></a>자동 검색에 사용할 계정 추가

[!INCLUDE [site-recovery-add-vcenter-account](../../includes/site-recovery-add-vcenter-account.md)]

### <a name="set-up-a-connection"></a>연결 설정

다음과 같이 서버에 연결합니다.

1. **+vCenter**를 선택하여 VMware vCenter 서버 또는 VMware vSphere ESXi 호스트를 연결하기 시작합니다.
2. **vCenter 추가**에서 vSphere 호스트 또는 vCenter Server에 대한 식별 이름을 지정하고 서버의 IP 주소 또는 FQDN을 지정합니다.
3. VMware 서버가 다른 포트에서 요청을 수신하도록 구성되지 않은 경우 포트를 443으로 그대로 둡니다. VMware vCenter 또는 vSphere ESXi 서버에 연결할 계정을 선택합니다. **확인**을 클릭합니다.
4. Site Recovery는 지정한 설정을 사용하여 VMware 서버에 연결하고 VM을 검색합니다.

> [!NOTE]
> vCenter 또는 호스트 서버에 대해 관리자 권한이 없는 계정으로 서버 또는 호스트를 추가하는 경우 계정에 데이터 센터, 데이터 저장소, 폴더, 호스트, 네트워크, 리소스, 가상 컴퓨터 및 vSphere 분산 스위치에 대한 권한이 설정되어 있는지 확인합니다. 또한 VMware vCenter 서버에 저장소 보기 권한을 사용하도록 설정되어야 합니다.


## <a name="set-up-the-target-environment"></a>대상 환경 설정

대상 환경을 설정하기 전에 Azure Storage 계정 및 가상 네트워크가 설정되어 있는지 확인합니다.

1. **인프라 준비** > **대상**을 클릭하고 사용하려는 Azure 구독을 선택합니다.
2. 대상 배포 모델이 리소스 관리자 기반인지, 클래식인지 지정합니다.
3. Site Recovery가 호환되는 Azure 저장소 계정 및 네트워크가 하나 이상 있는지 확인합니다.

   ![대상](./media/vmware-walkthrough-source-target/gs-target.png)
4. 저장소 계정 또는 네트워크를 만들지 않았으면 **+저장소 계정** 또는 **+네트워크**를 클릭하여 리소스 관리자 계정이나 인라인 네트워크를 만듭니다.

## <a name="next-steps"></a>다음 단계

[9단계: 복제 정책 설정](vmware-walkthrough-replication.md)으로 이동합니다.
