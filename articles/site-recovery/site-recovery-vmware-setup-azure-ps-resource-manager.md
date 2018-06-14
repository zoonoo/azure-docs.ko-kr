---
title: " Azure(Resource Manager)에서 실행되는 프로세스 서버 관리 | Microsoft Docs"
description: 이 문서에서는 Azure에서 장애 복구 프로세스 서버(Resource Manager)를 설정하는 방법을 설명합니다.
services: site-recovery
documentationcenter: ''
author: AnoopVasudavan
manager: gauravd
editor: ''
ms.assetid: ''
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 12/19/2017
ms.author: anoopkv
ms.openlocfilehash: 1b75acb13ac4c8990f99f7454a6de5483f6ca2f1
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/07/2018
ms.locfileid: "29767518"
---
# <a name="manage-a-process-server-running-in-azure-resource-manager"></a>Azure에서 실행되는 프로세스 서버 관리(Resource Manager)
> [!div class="op_single_selector"]
> * [리소스 관리자](./site-recovery-vmware-setup-azure-ps-resource-manager.md)
> * [클래식](./site-recovery-vmware-setup-azure-ps-classic.md)

장애 복구 동안 Azure Virtual Network와 온-프레미스 네트워크 간에 대기 시간이 긴 경우 Azure에서 프로세스 서버를 배포하는 것이 좋습니다. 이 문서에서는 Azure에서 실행 중인 프로세스 서버를 설정, 구성 및 관리하는 방법을 설명합니다.

> [!NOTE]
> 이 문서는 장애 조치 중에 가상 머신에 대한 배포 모델로 **Resource Manager**를 사용한 경우에 사용할 수 있습니다. 배포 모델로 **클래식**을 사용한 경우 [장애 복구 프로세스 서버를 설정 및 구성하는 방법(클래식)](./site-recovery-vmware-setup-azure-ps-classic.md)의 단계를 따르세요.

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [site-recovery-vmware-process-server-prerequ](../../includes/site-recovery-vmware-azure-process-server-prereq.md)]

## <a name="deploy-a-process-server-on-azure"></a>Azure에 프로세스 서버 배포
1. 자격 증명 모음 > **Site Recovery 인프라**("관리" 헤드에 있음) > **구성 서버**("VMware 및 실제 컴퓨터" 헤드에 있음)에서 구성 서버를 선택합니다.
2. 열리는 구성 서버 세부 정보 페이지에서 "+프로세스 서버" 클릭

  ![프로세스 서버 갤러리 추가](./media/site-recovery-vmware-setup-azure-ps-arm/add-ps.png)

3.  **프로세스 서버 추가** 페이지에서 다음 값 선택

  ![프로세스 서버 갤러리 항목 추가](./media/site-recovery-vmware-setup-azure-ps-arm/add-ps-page-1.png)
|**필드 이름**|**값**|
|-|-|
|프로세스 서버를 배포할 위치 선택|**Azure에 장애 복구 프로세스 서버 배포** 값 선택 |
|구독|가상 머신을 장애 조치한 Azure 구독 선택|
|리소스 그룹|리소스 그룹을 만들어 이 프로세스 서버를 배포하거나 기존 리소스 그룹에 프로세스 서버를 배포하도록 선택|
|위치|가상 머신이 장애 조치된 Azure 데이터 센터 선택|
|Azure 네트워크|가상 머신이 장애 조치된 Azure VNet(Virtual Network) 선택. 가상 머신을 여러 Azure Vnet으로 장애 조치한 경우 VNet마다 배포된 프로세스 서버가 있어야 합니다.|

4. 프로세스 서버에 대한 나머지 속성 채우기

  ![프로세스 서버 요약 추가](./media/site-recovery-vmware-setup-azure-ps-arm/add-ps-page-2.png)
|**필드 이름**|**값**|
|-|-|
|서버 이름|프로세스 서버 가상 컴퓨터에 대한 표시 이름/호스트 이름|
| 사용자 이름|해당 가상 컴퓨터에서 관리자가 되는 사용자 이름|
|Storage 계정|가상 머신의 가상 디스크가 배치되는 Storage 계정의 이름|
|서브넷|가상 머신이 연결되는 Azure VNet의 서브넷|
| IP 주소|프로세스 서버가 부팅될 경우 간주되는 IP 주소|
5. 확인 단추를 클릭하여 프로세스 서버 가상 머신 배포를 시작합니다.

> [!NOTE]
> 장애 복구를 위해 이 프로세스 서버를 사용하려면 온-프레미스 구성 서버에 등록해야 합니다.

## <a name="registering-the-process-server-running-in-azure-to-a-configuration-server-running-on-premises"></a>프로세스 서버(Azure에서 실행)를 구성 서버(온-프레미스에서 실행)에 등록

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]

## <a name="upgrading-the-process-server-to-latest-version"></a>프로세스 서버를 최신 버전으로 업그레이드

[!INCLUDE [site-recovery-vmware-upgrade-process-server](../../includes/site-recovery-vmware-upgrade-process-server.md)]

## <a name="unregistering-the-process-server-running-in-azure-from-a-configuration-server-running-on-premises"></a>프로세스 서버(Azure에서 실행)를 구성 서버(온-프레미스에서 실행)에 등록 취소

[!INCLUDE [site-recovery-vmware-unregister-process-server](../../includes/site-recovery-vmware-unregister-process-server.md)]
