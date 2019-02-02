---
title: Azure Active Directory (Azure AD)를 사용 하 여 Azure Stack에 Kubernetes 배포 | Microsoft Docs
description: Kubernetes Azure Active Directory (Azure AD)를 사용 하 여 Azure Stack에 배포 하는 방법에 알아봅니다.
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
ms.date: 01/30/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 01/16/2019
ms.openlocfilehash: 8d2d8d0595099081498202a3103caa10df8cf67d
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/02/2019
ms.locfileid: "55657844"
---
# <a name="deploy-kubernetes-to-azure-stack-using-azure-active-directory"></a>Azure Active Directory를 사용 하 여 Azure Stack에 Kubernetes 배포

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

> [!Note]  
> Azure Stack에서 Kubernetes 미리 보기입니다. Azure Stack 연결이 끊긴된 시나리오는 미리 보기에서 현재 지원 되지 않습니다.

배포 하 고 단일에서 사용자 id 관리 서비스로 작업을 조정 하는 대로 Azure Active Directory (Azure AD)를 사용 하는 경우 kubernetes의 경우 리소스를 설정 하려면이 문서의 단계를 수행할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

시작 하려면 적절 한 권한이 있고 Azure Stack에 사용할 준비가 되어 있는지에 있는지를 확인 합니다.

1. Azure Active Directory (Azure AD) 테 넌 트에서 응용 프로그램을 만들 수 있는지 확인 합니다. Kubernetes 배포에 대 한 이러한 권한이 필요 합니다.

    에 대 한 지침은 권한을 확인 하십시오 [Azure Active Directory 확인 권한을](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#check-azure-active-directory-permissions)합니다.

1. SSH 공용 및 개인 키 쌍을 Azure Stack에서 Linux VM에 로그인을 생성 합니다. 클러스터를 만들 때 공개 키를 해야 합니다.

    키 생성에 대 한 지침을 참조 하세요 [SSH 키 생성](https://github.com/msazurestackworkloads/acs-engine/blob/master/docs/ssh.md#ssh-key-generation)합니다.

1. Azure Stack 테 넌 트 포털에서 유효한 구독이 있는 새 응용 프로그램을 추가 하려면 사용할 수 있는 주소가 충분 한 공용 IP 있는지 확인 합니다.

    Azure Stack에 클러스터를 배포할 수 없습니다 **관리자** 구독 합니다. 사용 해야 합니다는 **사용자** 구독 합니다. 

1. Marketplace에 Kubernetes 클러스터 없는 경우 Azure Stack 관리자에 게 문의 합니다.

## <a name="create-a-service-principal"></a>서비스 주체 만들기

Azure에서 서비스 주체를 설정 합니다. 서비스 주체는 Azure Stack 리소스에 응용 프로그램 액세스를 제공합니다.

1. 전역에 로그인 [Azure portal](http://portal.azure.com)합니다.

1. Azure Stack 인스턴스와 연결 된 Azure AD 테 넌 트를 사용 하 여 서명 되었는지 확인 합니다. Azure 도구 모음에서 필터 아이콘을 클릭 하 여 로그인을 전환할 수 있습니다.

    ![AD 테 넌 트 선택](media/azure-stack-solution-template-kubernetes-deploy/tenantselector.png)

1. Azure AD 애플리케이션을 만듭니다.

    a. 선택 **Azure Active Directory** > **+ 앱 등록** > **새 응용 프로그램 등록**합니다.

    b. 입력을 **이름** 응용 프로그램입니다.

    다. 선택 **웹 앱 / API**합니다.

    d. 입력 `http://localhost` 에 대 한 합니다 **로그온 URL**합니다.

    다. **만들기**를 클릭합니다.

1. **응용 프로그램 ID**를 적어 둡니다. 클러스터를 만들 때 ID가 필요 합니다. ID로 참조 되 **서비스 주체 클라이언트 ID**합니다.

1. 선택 **설정을** > **키**합니다.

    a. 입력 된 **설명을**합니다.

    b. 선택 **무기한** 에 대 한 **Expires**합니다.

    다. **저장**을 선택합니다. 키 문자열을 기록 하십시오. 클러스터를 만들 때 키 문자열을 해야 합니다. 키로 참조 되는 **서비스 주체 클라이언트 비밀**합니다.

## <a name="give-the-service-principal-access"></a>서비스 주체 액세스를 제공 합니다.

보안 주체가 리소스를 만들 수 있도록 구독에 대 한 서비스 주체 액세스를 제공 합니다.

1.  에 로그인 합니다 [Azure Stack 포털](https://portal.local.azurestack.external/)합니다.

1. 선택 **모든 서비스** > **구독**합니다.

1. Kubernetes 클러스터를 사용 하 여 연산자에 의해 만들어진 구독을 선택 합니다.

1. 선택 **액세스 제어 (IAM)** > 선택 **역할 할당 추가**합니다.

1. 선택 된 **참가자** 역할입니다.

1. 주 서비스에 대해 만든 응용 프로그램 이름을 선택 합니다. 검색 상자에 이름을 입력 해야 합니다.

1. **저장**을 클릭합니다.

## <a name="deploy-kubernetes"></a>Kubernetes 배포

1. 엽니다는 [Azure Stack 포털](https://portal.local.azurestack.external)합니다.

1. 선택 **+ 리소스 만들기** > **계산** > **Kubernetes 클러스터**합니다. **만들기**를 클릭합니다.

    ![솔루션 템플릿 배포](media/azure-stack-solution-template-kubernetes-deploy/01_kub_market_item.png)

### <a name="1-basics"></a>1. 기본 사항

1. 선택 **기본 사항** Kubernetes 클러스터를 만듭니다.

    ![솔루션 템플릿 배포](media/azure-stack-solution-template-kubernetes-deploy/02_kub_config_basic.png)

1. 선택 하면 **구독** id입니다.

1. 새 리소스 그룹의 이름을 입력 하거나 기존 리소스 그룹을 선택 합니다. 리소스 이름은 영숫자 및 소문자 있어야 합니다.

1. 선택 합니다 **위치** 리소스 그룹입니다. Azure Stack 설치에 대해 선택 하는 지역입니다.

### <a name="2-kubernetes-cluster-settings"></a>2. Kubernetes 클러스터 설정

1. 선택 **Kubernetes 클러스터 설정을** Kubernetes 클러스터를 만듭니다.

    ![솔루션 템플릿 배포](media/azure-stack-solution-template-kubernetes-deploy/03_kub_config_settings-aad.png)

1. 입력 된 **Linux VM Admin Username**합니다. Kubernetes 클러스터의 일부인 Linux Virtual Machines 및 dvm이 대 한 사용자 이름입니다.

1. 입력 된 **SSH 공개 키** dvm이 고 Kubernetes 클러스터의 일부로 생성 하는 모든 Linux 컴퓨터에 대 한 인증에 사용 합니다.

1. 입력 된 **마스터 프로필 DNS 접두사** 지역에 고유한 합니다. 같은 지역 고유 이름 이어야 합니다 `k8s-12345`합니다. 하려고 선택 리소스 그룹과 동일한 이름을 모범 사례로 좋습니다.

    > [!Note]  
    > 각 클러스터에 대 한 마스터 프로필 새롭고 고유한 DNS 접두사를 사용 합니다.

1. 선택 된 **Kubernetes 마스터 풀 프로필 수**입니다. 마스터 풀의 노드 수를 포함 하는 수 있습니다. 있을 수 있습니다 1에서 7로. 이 값은 홀수 여야 합니다.

1. 선택 **Kubernetes 마스터 vm은 VMSize**합니다.

1. 선택 된 **Kubernetes 노드 풀 프로필 수**입니다. 수는 클러스터의 에이전트 수를 포함합니다. 

1. 선택 된 **저장소 프로필**합니다. 선택할 수 있습니다 **Blob 디스크** 하거나 **Managed Disk**합니다. 이 VM 크기의 Kubernetes 노드 Vm을 지정합니다. 

1. 선택 **Azure AD** 에 대 한 합니다 **Azure Stack id 시스템** Azure Stack 설치 합니다. 

1. 입력 된 **서비스 주체 ClientId** Kubernetes Azure 클라우드 공급자가 사용 됩니다. 서비스 주체를 만들 때 응용 프로그램 ID로 식별 된 클라이언트 ID입니다.

1. 입력 된 **서비스 주체 클라이언트 비밀** 서비스 주체를 만들 때 만든 합니다.

1. 입력 된 **Kubernetes Azure Cloud Provider 버전이**합니다. Kubernetes Azure 공급자에 대 한 버전입니다. Azure Stack에는 각 Azure Stack 버전에 대 한 사용자 지정 Kubernetes 빌드를 해제합니다.

### <a name="3-summary"></a>3. 요약

1. 요약을 선택 합니다. 블레이드는 Kubernetes 클러스터 구성 설정에 대해 유효성 검사 메시지를 표시합니다.

    ![솔루션 템플릿 배포](media/azure-stack-solution-template-kubernetes-deploy/04_preview.png)

2. 설정을 검토 합니다.

3. 선택 **확인** 클러스터를 배포 합니다.

> [!TIP]  
>  배포에 대 한 질문이 있으면 질문을 게시 하거나 경우 사용자에 대 한 답변이 이미 질문에 참조를 [Azure Stack 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)합니다.


## <a name="next-steps"></a>다음 단계

[클러스터에 연결](azure-stack-solution-template-kubernetes-deploy.md#connect-to-your-cluster)