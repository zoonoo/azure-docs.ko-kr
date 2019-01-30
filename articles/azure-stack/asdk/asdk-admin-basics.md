---
title: Azure Stack 개발 키트 기본 사항 | Microsoft Docs
description: Azure Stack 개발 키트 (ASDK)에 대 한 기본 관리 작업을 수행 하는 방법을 설명 합니다.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: 79e031226f11d45121827795c34b0103235d8d79
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55248166"
---
# <a name="asdk-administration-basics"></a>ASDK 관리 기본 사항 
Azure Stack 개발 키트 (ASDK) 관리 접하는 경우 알아야 할 몇 가지 있습니다. 이 설명서는 평가 환경에 Azure Stack 운영자 역할의 개요를 제공 하 고 테스트 사용자를 확인 하는 방법을 신속 하 게 생산성 될 수 있습니다.

먼저 검토 해야 합니다 [Azure Stack 개발 키트 란?](asdk-what-is.md) 의 용도 ASDK 및 제한 사항을 이해 해야 하는 문서입니다. "샌드박스"로 개발 및 비-프로덕션 환경에서 앱을 테스트 하려면 Azure Stack을 평가할 수 있는 개발 키트를 사용 해야 있습니다. 

Azure와 같은 Azure Stack 빠르게 혁신이 하므로 새 빌드의 ASDK 정기적으로 출시할 예정입니다. 그러나 Azure Stack 통합 시스템 배포와 같이 ASDK 업그레이드할 수 없습니다. 따라서 최신 빌드를 이동 하려는 경우 해야 완전히 [재배포는 ASDK](asdk-redeploy.md)합니다. 업데이트 패키지를 적용할 수 없습니다. 이 프로세스 시간은 걸리지만 장점은 가능 하 게 하는 즉시 최신 기능을 사용해 보십시오 수 있습니다. 

## <a name="what-account-should-i-use"></a>사용할 계정은?
Azure Stack을 관리할 때 고려해 야 하는 몇 가지 계정 고려 사항이 있습니다. 배포에서 특히 Windows Server Active Directory Federation Services (AD FS)를 id 공급자로 Azure Active Directory (Azure AD) 대신 사용합니다. 다음 계정 고려 사항 ASDK 배포와 Azure Stack 통합 시스템에 적용 됩니다.

|계좌|Azure AD|AD FS|
|-----|-----|-----|
|로컬 관리자 (. \Administrator)|ASDK 호스트 관리자|ASDK 호스트 관리자|
|AzureStack\AzureStackAdmin|ASDK 호스트 관리자<br><br>Azure Stack 관리 포털에 로그인 할 수 있습니다.<br><br>보기 및 Service Fabric 링 관리에 대 한 액세스|ASDK 호스트 관리자<br><br>Azure Stack 관리 포털에 액세스할 수 없음<br><br>보기 및 Service Fabric 링 관리에 대 한 액세스<br><br>더 이상 기본 공급자 구독 (DPS)의 소유자|
|AzureStack\CloudAdmin|액세스 하 고 권한 있는 끝점 내에서 허용 된 명령을 실행할 수 있습니다.|액세스 하 고 권한 있는 끝점 내에서 허용 된 명령을 실행할 수 있습니다.<br><br>ASDK 호스트에 로그인 할 수 없습니다.<br><br>기본 공급자 구독 (DPS)의 소유자|
|Azure AD 전역 관리자|설치 중에 사용<br><br>기본 공급자 구독 (DPS)의 소유자|해당 없음|
|

## <a name="what-tools-do-i-use-to-manage"></a>관리 하려면 어떤 도구를 사용 합니까?
사용할 수는 [Azure Stack 관리자 포털](https://adminportal.local.azurestack.external) 또는 PowerShell을 Azure Stack을 관리 합니다. 기본 개념을 알아보고 하는 가장 쉬운 방법은 포털을 통해 됩니다. 설치 해야 하는 PowerShell을 사용 하려는 경우 [PowerShell for Azure Stack](asdk-post-deploy.md#install-azure-stack-powershell) 하 고 [GitHub에서 Azure Stack 도구를 다운로드](asdk-post-deploy.md#download-the-azure-stack-tools)합니다.

Azure Stack의 기본 배포, 관리 및 조직 메커니즘으로 Azure Resource Manager를 사용합니다. Azure Stack을 관리 하 여 사용자를 지원할 수 있도록 하려는 경우 Azure Resource Manager에 대 한 배워야 합니다. 읽어 자세히 알아볼 수 있습니다 합니다 [Getting Started with Azure Resource Manager 백서](https://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf)합니다.

## <a name="your-typical-responsibilities"></a>일반적인 사용자의 책임
사용자가 서비스를 사용 하려고 합니다. 자신의 관점에서 이러한 서비스를 사용할 수 있도록 주 역할을 맡고 합니다. 제공 서비스를 알아볼 수 있습니다 하 고 해당 하는 방법에 따라 사용 가능한 서비스는 ASDK 사용 [계획, 제품 및 할당량 만들기](asdk-offer-services.md)합니다. 가상 머신 이미지와 같은 marketplace에 항목을 추가 해야 합니다. 가장 쉬운 방법은 [마켓플레이스 항목 다운로드](asdk-marketplace-item.md) Azure Stack에는 Azure에서.

> [!NOTE]
> 에 계획, 제품 및 서비스를 테스트 하려는 경우 사용 해야 합니다 [사용자 포털](https://portal.local.azurestack.external); 합니다 [관리자 포털](https://adminportal.local.azurestack.external)합니다.

서비스를 제공 하는 것 외에도 모든 일반의 역할은 Azure Stack 운영자를 ASDK 실행 되도록 해야 합니다. 이러한 임무 다음 등이 포함 됩니다.
- Azure Active Directory (Azure AD) 또는 Active Directory Federation Services (AD FS) 배포에 대 한 사용자 계정을 추가 합니다.
- 역할 기반 액세스 제어 (RBAC) 역할 (관리자만 제한 되지 않습니다) 할당
- 인프라 상태 모니터링
- 네트워크 및 저장소 리소스 관리
- 실패 한 개발 키트 호스트 컴퓨터 하드웨어 교체 

## <a name="where-to-get-support"></a>지원을 받을 수 있는 위치
유일한 지원 옵션은 개발 키트에 대 한 지원 관련 질문에 [Microsoft Azure Stack 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)합니다. 관리자 포털의 오른쪽 위 모서리에 있는 도움말 및 지원 (물음표) 아이콘을 클릭 하 고 클릭 **새 지원 요청**, 직접 포럼 사이트 열립니다. 이러한 포럼을 정기적으로 모니터링 합니다. 

> [!IMPORTANT]
> ASDK 평가 환경 이므로 지원은 없습니다 공식를 통해 Microsoft 고객 지원 서비스 (CSS)를 제공 합니다.

## <a name="next-steps"></a>다음 단계
[ASDK 배포](asdk-install.md)

