---
title: Azure 및 Azure Stack 응용 프로그램을 사용 하 여 하이브리드 클라우드 id 구성 | Microsoft Docs
description: Azure 및 Azure Stack 응용 프로그램을 사용 하 여 하이브리드 클라우드 id를 구성 하는 방법에 알아봅니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/14/2019
ms.author: mabrigg
ms.reviewer: anajod
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: d36b94ac9f130649e880e6bff1fa3a8b223828b4
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55244411"
---
# <a name="tutorial-configure-hybrid-cloud-identity-for-azure-and-azure-stack-applications"></a>자습서: Azure 및 Azure Stack 응용 프로그램에 대 한 하이브리드 클라우드 id를 구성 합니다.

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

Azure 및 Azure Stack 응용 프로그램에 대 한 하이브리드 클라우드 id를 구성 하는 방법에 알아봅니다.

전역 Azure 및 Azure Stack에서 응용 프로그램에 대 한 액세스 권한을 부여 하는 것에 대 한 두 가지 옵션이 있습니다.

 * Azure Stack에 인터넷에 대 한 연속 연결이 있는 경우에 Azure Active Directory (Azure AD)를 사용할 수 있습니다.
 * Azure Stack의 인터넷에서 연결 Azure Directory Federated Services (AD FS)를 사용할 수 있습니다.

서비스 주체를 사용 하 여 배포 또는 Azure Stack에서 Azure Resource Manager를 사용 하 여 구성을 목적으로 Azure Stack 응용 프로그램에 대 한 액세스 권한을 부여 합니다.

이 자습서에서는 샘플 환경을 빌드합니다.

> [!div class="checklist"]
> - 전역 Azure 및 Azure Stack에서 하이브리드 id 설정
> - Azure Stack API에 액세스 토큰을 검색 합니다.

이 자습서의 단계에 대 한 Azure Stack 연산자 권한이 있어야 합니다.

> [!Tip]  
> ![하이브리드 pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack은 Azure의 확장입니다. Azure Stack은 유연성 및 혁신을 온-프레미스 환경에 컴퓨팅 및 하이브리드 앱을 어디서 나 빌드 및 배포 할 수 있는 유일한 하이브리드 클라우드를 사용 하도록 설정 하는 클라우드를 제공 합니다.  
> 
> 백서 [하이브리드 응용 프로그램에 대 한 디자인 고려 사항](https://aka.ms/hybrid-cloud-applications-pillars) (배치, 확장성, 가용성, 복원 력, 관리 효율성 및 보안) 소프트웨어 품질 핵심 요소를 디자인, 배포 및 하이브리드 운영 검토 응용 프로그램입니다. 디자인 고려 사항을 프로덕션 환경에서 문제를 최소화 되는 하이브리드 응용 프로그램 디자인을 최적화 하는 데 도움이 됩니다.


## <a name="create-a-service-principal-for-azure-ad-in-the-portal"></a>포털에서 Azure AD에 대 한 서비스 주체 만들기

Azure AD id 저장소로 사용 하 여 Azure Stack을 배포한 경우 Azure에 대해 수행 하는 것 처럼 서비스 주체를 만들 수 있습니다. 합니다 [서비스 주체를 만들](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-create-service-principals#create-service-principal-for-azure-ad) 문서에서는 포털을 통해 단계를 수행 하는 방법을 보여 줍니다. 있는지 확인 합니다 [필요한 Azure AD 권한](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions) 시작 하기 전에 합니다.

## <a name="create-a-service-principal-for-ad-fs-using-powershell"></a>PowerShell을 사용 하 여 AD FS에 대 한 서비스 주체 만들기

AD FS 사용 하 여 Azure Stack을 배포한 경우에 서비스 주체 만들기, 액세스에 대 한 역할 할당 및 해당 id를 사용 하 여 PowerShell에서 로그인 하려면 PowerShell을 사용할 수 있습니다. [AD FS에 대 한 서비스 주체 만들기](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-create-service-principals#create-service-principal-for-ad-fs) PowerShell을 사용 하는 데 필요한 단계를 수행 하는 방법을 보여 줍니다.

## <a name="using-the-azure-stack-api"></a>Azure Stack API를 사용 하 여

합니다 [Azure Stack API](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-rest-api-use) 자습서에서는 Azure Stack API에 액세스 토큰을 검색 하는 과정을 단계별로 설명 합니다.

## <a name="connect-to-azure-stack-using-powershell"></a>Powershell을 사용 하 여 Azure Stack에 연결

빠른 시작 [Azure Stack에서 PowerShell을 사용 하 여 즉시 가동 및 실행 하려면](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-configure-quickstart) Azure PowerShell을 설치 하 고 Azure Stack 설치에 연결 하는 데 필요한 단계를 안내 합니다.

### <a name="prerequisites"></a>필수 조건

설치 된 Azure Stack 구독에 액세스할 수 있습니다를 사용 하 여 Azure Active Directory에 연결. 설치 된 Azure Stack에 없는 경우 설정 하려면 다음이 지침을 사용할 수 있습니다는 [Azure Stack 개발 키트](https://docs.microsoft.com/azure/azure-stack/asdk/asdk-deploy)합니다.

#### <a name="connect-to-azure-stack-using-code"></a>코드를 사용 하 여 Azure Stack에 연결

코드를 사용 하 여 Azure Stack에 연결 하려면 Azure Resource Manager 끝점 API를 사용 하 여 Azure Stack 설치를 위한 인증 및 graph 끝점 가져오고 다음 REST 요청을 사용 하 여 인증 합니다. 샘플 클라이언트 응용 프로그램을 찾을 수 있습니다 [GitHub](https://github.com/shriramnat/HybridARMApplication)합니다.

>[!Note]
>선택한 언어에 대 한 Azure SDK에서 Azure API 프로필을 지 원하는 경우가 아니면 SDK는 Azure Stack과 함께 작동 하지 않습니다. Azure API 프로필에 대 한 자세한 내용은 참조는 [API 버전 프로필 관리](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-version-profiles) 문서.

## <a name="next-steps"></a>다음 단계

 - Azure Stack에서 identity가 처리 하는 방법에 대 한 자세한 내용은 참조 하세요 [Azure Stack에 대 한 Id 아키텍처](https://docs.microsoft.com/azure/azure-stack/azure-stack-identity-architecture)합니다.
 - Azure 클라우드 패턴에 대 한 자세한 내용은 참조 하세요 [클라우드 디자인 패턴](https://docs.microsoft.com/azure/architecture/patterns)합니다.
