---
title: Azure Stack Marketplace Kubernetes 추가할 | Microsoft Docs
description: Kubernetes Azure Stack Marketplace에 추가 하는 방법에 알아봅니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.openlocfilehash: e89575323b87ba28ef4f062da098fea4f0e27035
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/14/2019
ms.locfileid: "54264057"
---
# <a name="add-kubernetes-to-the-azure-stack-marketplace"></a>Kubernetes Azure Stack Marketplace에 추가

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

> [!note]  
> Azure Stack에서 Kubernetes 미리 보기입니다.

사용자에 게 Marketplace 항목으로 Kubernetes를 제공할 수 있습니다. 사용자가 단일의 조정 된 작업에서 Kubernetes를 배포할 수 있습니다.

다음 문서를 배포 하 고 독립 실행형 Kubernetes 클러스터에 대 한 리소스를 프로 비전 하는 Azure Resource Manager 템플릿을 사용 하 여 확인 합니다. Kubernetes 클러스터 마켓플레이스 항목 0.3.0 Azure Stack 버전 1808 필요합니다. 시작 하기 전에 Azure Stack 및 Azure 전역 테 넌 트 설정을 확인 합니다. Azure Stack에 대 한 필수 정보를 수집 합니다. Azure Stack Marketplace 및 테 넌 트에 필요한 리소스를 추가 합니다. 클러스터는 Ubuntu server, 사용자 지정 스크립트 및 Kubernetes 항목을 marketplace에 따라 달라 집니다.

## <a name="create-a-plan-an-offer-and-a-subscription"></a>계획, 제공 및 구독 만들기

계획, 제공 및 Kubernetes 마켓플레이스 항목에 대 한 구독을 만듭니다. 또한 기존 계획을 사용 하 고 제공할 수 있습니다.

1. 에 로그인 하 여 [관리 포털.](https://adminportal.local.azurestack.external)

1. 기본 계획으로 계획을 만듭니다. 자세한 내용은 [Azure Stack에서 계획을 만들어](azure-stack-create-plan.md)합니다.

1. 제품을 만듭니다. 자세한 내용은 [Azure Stack에서 제품 만들기](azure-stack-create-offer.md)합니다.

1. 선택 **제공**를 만든 제품을 찾습니다.

1. 선택 **개요** 제품 블레이드에서 합니다.

1. 선택 **상태 변경**합니다. **공용**을 선택합니다.

1. 선택 **+ 리소스 만들기** > **제공 하 고 계획** > **구독** 새 구독을 만듭니다.

    a. 입력 한 **표시 이름**합니다.

    b. 입력 한 **사용자**합니다. 테 넌 트와 연결 된 Azure AD 계정을 사용 합니다.

    다. **공급자 설명**

    d. 설정 된 **디렉터리 테 넌 트** Azure Stack에 대 한 Azure AD 테 넌 트입니다. 

    e. 선택 **제공**합니다. 사용자가 만든 제품의 이름을 선택 합니다. 구독 id 기록

## <a name="add-an-ubuntu-server-image"></a>Ubuntu server 이미지를 추가 합니다.

Marketplace에는 다음 Ubuntu Server 이미지를 추가 합니다.

1. 에 로그인 합니다 [관리 포털](https://adminportal.local.azurestack.external)합니다.

1. 선택 **모든 서비스**를 선택한 다음는 **관리** 범주를 선택한 **Marketplace 관리**합니다.

1. 선택 **+ Azure에서 추가**합니다.

1. `UbuntuServer` 을 입력합니다.

1. 최신 버전의 서버를 선택 합니다. 정식 버전을 확인 하 고 최신 버전이 있는지 확인 합니다.
    - **게시자**: Canonical
    - **제품**: UbuntuServer
    - **버전**: 16.04.201806120 (또는 이상)
    - **SKU**: 16.04-LTS

1. 선택 **다운로드 합니다.**

## <a name="add-a-custom-script-for-linux"></a>Linux 용 사용자 지정 스크립트를 추가 합니다.

Marketplace에서 Kubernetes를 추가 합니다.

1. 엽니다는 [관리 포털](https://adminportal.local.azurestack.external)합니다.

1. 선택 **모든 서비스** 한 다음는 **관리** 범주를 선택한 **Marketplace 관리**합니다.

1. 선택 **+ Azure에서 추가**합니다.

1. `Custom Script for Linux` 을 입력합니다.

1. 다음 프로필을 사용 하 여 스크립트를 선택 합니다.
    - **제품**: Linux 2.0에 대 한 사용자 지정 스크립트
    - **버전**: 2.0.6 (이상)
    - **게시자**: Microsoft Corp

    > [!Note]  
    > Linux 용 사용자 지정 스크립트의 버전이 둘 이상 나타날 수 있습니다. 항목의 최신 버전을 추가 해야 합니다.

1. 선택 **다운로드 합니다.**


## <a name="add-kubernetes-to-the-marketplace"></a>Kubernetes는 marketplace에 추가

1. 엽니다는 [관리 포털](https://adminportal.local.azurestack.external)합니다.

1. 선택 **모든 서비스** 한 다음는 **관리** 범주를 선택한 **Marketplace 관리**합니다.

1. 선택 **+ Azure에서 추가**합니다.

1. `Kubernetes` 을 입력합니다.

1. `Kubernetes Cluster`를 선택합니다.

1. 선택 **다운로드 합니다.**

    > [!note]  
    > Marketplace에서 표시할 marketplace 항목에 대 일 분이 걸릴 수 있습니다.

    ![kubernetes](user/media/azure-stack-solution-template-kubernetes-deploy/marketplaceitem.png)

## <a name="update-or-remove-the-kubernetes"></a>업데이트 또는 Kubernetes를 제거 합니다. 

Kubernetes 항목을 업데이트할 때 Marketplace에 있는 항목을 제거 해야 합니다. 그런 다음 Kubernetes marketplace에 추가 하려면이 문서의 지침을 따라 수 있습니다.

Kubernetes 항목 제거 하려면:

1. Operator 자격으로 PowerShell 사용 하 여 Azure Stack에 연결 합니다. 명령 참조 [연산자로 PowerShell 사용 하 여 Azure Stack에 연결](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-configure-admin)합니다.

2. 갤러리에서 현재 Kubernetes 클러스터 항목을 찾습니다.

    ```PowerShell  
    Get-AzsGalleryItem | Select Name
    ```
    
3. 현재 항목의 이름 같은 note `Microsoft.AzureStackKubernetesCluster.0.3.0`

4. 항목을 제거 하려면 다음 PowerShell cmdlet을 사용 합니다.

    ```PowerShell  
    $Itemname="Microsoft.AzureStackKubernetesCluster.0.3.0"

    Remove-AzsGalleryItem -Name $Itemname
    ```

## <a name="next-steps"></a>다음 단계

[Azure Stack에 Kubernetes 배포](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-template-kubernetes-deploy)

[Azure Stack에서 서비스 제공 개요](azure-stack-offer-services-overview.md)
