---
title: "Azure Site Recovery를 사용하여 Azure에 물리적 서버 복제를 위한 원본 및 대상 설정 | Microsoft Docs"
description: "Azure Site Recovery 서비스를 사용하여 Azure Storage에 물리적 서버의 복제를 위한 원본 및 대상 설정을 수행하는 단계를 요약합니다."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 2e3d03bc-4e53-4590-8a01-f702d3cc9500
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.translationtype: Human Translation
ms.sourcegitcommit: 138f04f8e9f0a9a4f71e43e73593b03386e7e5a9
ms.openlocfilehash: e89bbf5a2c1d71852e49da43d3106a05ebfc28a8
ms.contentlocale: ko-kr
ms.lasthandoff: 06/29/2017


---
# <a name="step-7-set-up-the-source-and-target-for-physical-server-replication-to-azure"></a>7단계: Azure에 물리적 서버 복제를 위한 원본 및 대상 설정

이 문서에서는 Azure Portal에서 [Azure Site Recovery](site-recovery-overview.md) 서비스를 사용하여 Azure에 온-프레미스 물리적 서버를 복제할 때 원본 및 대상 설정을 구성하는 방법을 설명합니다.

이 문서의 하단 또는 [Azure Recovery Services 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)에서 의견이나 질문을 게시합니다.


## <a name="set-up-the-source-environment"></a>원본 환경 설정

구성 서버를 설정하고 자격 증명 모음에 등록한 후 컴퓨터를 검색합니다.

1. **Site Recovery** > **1단계: 인프라 준비** > **원본**을 클릭합니다.
2. 구성 서버가 없는 경우 **+구성 서버**를 클릭합니다.
3. **서버 추가**에서 **구성 서버**가 **서버 형식**에 표시되는지 확인합니다.
4. Site Recovery 통합 설치 프로그램 설치 파일을 다운로드합니다.
5. 자격 증명 모음 등록 키를 다운로드합니다. 통합 설치를 실행할 때 이 키가 필요합니다. 이 키는 생성된 날로부터 5일간 유효합니다.

   ![원본 설정](./media/vmware-walkthrough-source-target/set-source2.png)


## <a name="register-the-configuration-server-in-the-vault"></a>자격 증명 모음에 구성 서버 등록

다음을 수행하여 시작하기 전에 통합 설치 프로그램을 실행하여 구성 서버, 프로세스 서버 및 마스터 대상 서버를 설치합니다. 이 비디오에서는 VMware VM을 Azure로 복제하기 위한 구성 요소를 설정하는 과정을 설명합니다. 그러나 물리적 서버를 Azure로 복제하는 과정에도 동일한 프로세스가 적용됩니다.

- 구성 서버 VM에서 시스템 시계가 [시간 서버](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service)와 동기화되었는지 확인합니다. 서로 일치해야 합니다. 15분 빠르거나 늦은 경우 설치가 실패할 수 있습니다.
- 구성 서버 컴퓨터에서 로컬 관리자로 설치 프로그램을 실행합니다.
- TLS 1.0이 VM에서 활성화되어 있는지 확인합니다.


[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> [명령줄](http://aka.ms/installconfigsrv)을 통해 구성 서버를 설치할 수도 있습니다.




## <a name="set-up-the-target-environment"></a>대상 환경 설정

대상 환경을 설정하기 전에 Azure Storage 계정 및 가상 네트워크가 설정되어 있는지 확인합니다.

1. **인프라 준비** > **대상**을 클릭하고 사용하려는 Azure 구독을 선택합니다.
2. 대상 배포 모델이 리소스 관리자 기반인지, 클래식인지 지정합니다.
3. Site Recovery가 호환되는 Azure 저장소 계정 및 네트워크가 하나 이상 있는지 확인합니다.

   ![대상](./media/physical-walkthrough-source-target/gs-target.png)

4. 저장소 계정 또는 네트워크를 만들지 않았으면 **+저장소 계정** 또는 **+네트워크**를 클릭하여 리소스 관리자 계정이나 인라인 네트워크를 만듭니다.

## <a name="next-steps"></a>다음 단계

[8단계: 복제 정책 설정](physical-walkthrough-replication.md)으로 이동합니다.

