---
title: " Azure에서 실행되는 프로세스 서버 관리(클래식) | Microsoft Docs"
description: "이 문서에서는 Azure에서 장애 복구 프로세스 서버(클래식)를 설정하는 방법을 설명합니다."
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
ms.workload: storage-backup-recovery
ms.date: 06/29/2017
ms.author: anoopkv
ms.openlocfilehash: 479bbd207bcf715138c340f9e4d2634120bab85c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="manage-a-process-server-running-in-azure-classic"></a>Azure에서 실행되는 프로세스 서버 관리(클래식)
> [!div class="op_single_selector"]
> * [Azure 클래식](./site-recovery-vmware-setup-azure-ps-classic.md)
> * [리소스 관리자](./site-recovery-vmware-setup-azure-ps-resource-manager.md)

장애 복구 동안 Azure Virtual Network와 온-프레미스 네트워크 간에 대기 시간이 긴 경우 Azure에서 프로세스 서버를 배포하는 것이 좋습니다. 이 문서에서는 Azure에서 실행 중인 프로세스 서버를 설정, 구성 및 관리하는 방법을 설명합니다.

> [!NOTE]
> 이 문서는 장애 조치 중에 가상 컴퓨터에 대한 배포 모델로 클래식을 사용한 경우에 사용할 수 있습니다. 배포 모델로 Resource Manager를 사용한 경우 [장애 복구 프로세스 서버를 설정 및 구성하는 방법(Resource Manager)](./site-recovery-vmware-setup-azure-ps-resource-manager.md)의 단계를 따르세요.

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [site-recovery-vmware-process-server-prereq](../../includes/site-recovery-vmware-azure-process-server-prereq.md)]

## <a name="deploy-a-process-server-on-azure"></a>Azure에 프로세스 서버 배포

1. Azure Marketplace에서 **Microsoft Azure Site Recovery 프로세스 서버 V2**를 사용하여 가상 컴퓨터를 만듭니다. </br>
    ![Marketplace_image_1](./media/site-recovery-vmware-setup-azure-ps-classic/marketplace-ps-image.png)
2. 배포 모델로 **클래식**을 선택 했는지 확인합니다. </br>
  ![Marketplace_image_2](./media/site-recovery-vmware-setup-azure-ps-classic/marketplace-ps-image-classic.png)
3. 가상 컴퓨터 만들기 마법사 > 기본 설정에서 가상 컴퓨터를 장애 조치한 구독 및 위치를 선택합니다.</br>
  ![create_image_1](./media/site-recovery-vmware-setup-azure-ps-classic/azureps-classic-basic-info.png)
4. 가상 컴퓨터가 장애 조치된 가상 컴퓨터가 연결된 Azure Virtual Network에 연결되어 있는지 확인합니다.</br>
  ![create_image_2](./media/site-recovery-vmware-setup-azure-ps-classic/azureps-classic-settings.png)
5. 프로세스 서버 가상 컴퓨터가 프로비전되면 로그인한 후 구성 서버에 등록해야 합니다.

> [!NOTE]
> 장애 복구를 위해 이 프로세스 서버를 사용하려면 온-프레미스 구성 서버에 등록해야 합니다.

## <a name="registering-the-process-server-running-in-azure-to-a-configuration-server-running-on-premises"></a>프로세스 서버(Azure에서 실행)를 구성 서버(온-프레미스에서 실행)에 등록

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]

## <a name="upgrading-the-process-server-to-latest-version"></a>프로세스 서버를 최신 버전으로 업그레이드

[!INCLUDE [site-recovery-vmware-upgrade-process-server](../../includes/site-recovery-vmware-upgrade-process-server.md)]

## <a name="unregistering-the-process-server-running-in-azure-from-a-configuration-server-running-on-premises"></a>프로세스 서버(Azure에서 실행)를 구성 서버(온-프레미스에서 실행)에 등록 취소

[!INCLUDE [site-recovery-vmware-upgrade-process-server](../../includes/site-recovery-vmware-unregister-process-server.md)]
