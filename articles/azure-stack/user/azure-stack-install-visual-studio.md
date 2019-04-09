---
title: Visual Studio를 설치 하 고 Azure Stack에 연결 | Microsoft Docs
description: Visual Studio를 설치 하 고 Azure Stack에 연결 하는 데 필요한 단계에 알아봅니다.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.custom: vs-azure
ms.workload: azure-vs
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/08/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: da17d114c1ffb920fbaae85a6cdcbc35a66631a4
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59257997"
---
# <a name="install-visual-studio-and-connect-to-azure-stack"></a>Visual Studio를 설치 하 고 Azure Stack에 연결

*적용 대상 Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

Visual Studio를 사용 하 여 작성 하 고 Azure Resource Manager 배포 [템플릿](azure-stack-arm-templates.md) Azure Stack에 있습니다. 이 문서의 단계에 Visual Studio를 설치 하는 방법에 설명 [Azure Stack](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), 또는 Azure Stack을 통해 사용 하려는 경우 외부 컴퓨터 [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn)합니다.

## <a name="install-visual-studio"></a>Visual Studio 설치

1. 다운로드 및 실행 합니다 [웹 플랫폼 설치 관리자](https://www.microsoft.com/web/downloads/platform.aspx)합니다.  

2. 엽니다는 **Microsoft 웹 플랫폼 설치 관리자**합니다.

3. 검색할 **Visual Studio Community 2015 및 Microsoft Azure SDK-2.9.6**합니다. 클릭 **추가할**, 및 **설치**합니다.

4. 제거 된 **Microsoft Azure PowerShell** Azure SDK의 일부로 설치 되는 합니다.

    ![스크린 샷의 WebPI 설치 단계](./media/azure-stack-install-visual-studio/image1.png)

5. [Azure Stack 용 PowerShell 설치](azure-stack-powershell-install.md)

6. 설치가 완료 된 후 운영 체제를 다시 시작 합니다.

## <a name="connect-to-azure-stack-with-azure-ad"></a>Azure AD 사용 하 여 Azure Stack에 연결

1. Visual Studio를 시작합니다.

2. **뷰** 메뉴에서 **클라우드 탐색기**합니다.

3. 새 창에서 선택 **계정 추가** 및 Azure Active Directory (Azure AD) 자격 증명으로 로그인 합니다.  

    ![로그인 및 Azure Stack에 연결 되 면 클라우드 탐색기의 스크린샷](./media/azure-stack-install-visual-studio/image2.png)

로그인을 수행할 수 있습니다 [템플릿 배포](azure-stack-deploy-template-visual-studio.md) 하거나 사용자 고유의 템플릿을 만들 수 있는 사용 가능한 리소스 형식과 리소스 그룹을 찾습니다.  

## <a name="connect-to-azure-stack-with-ad-fs"></a>AD FS 사용 하 여 Azure Stack에 연결

1. Visual Studio를 시작합니다.

2. **도구가**를 선택 **옵션**합니다.

3. 확장 **환경** 에 **탐색창** 선택한 **계정**합니다.

4. 선택 **추가**, 사용자 Azure 리소스 관리자 끝점을 입력 합니다. Azure Stack 개발 키트에 대 한 URL은: `https://management.local.azurestack/external`합니다.  URL은 Azure Stack 통합 시스템의 경우: `https://management.[Region}.[External FQDN]`합니다.

    ![X](./media/azure-stack-install-visual-studio/image5.png)

5. **추가**를 선택합니다.  

    Visual Studio는 Azure 리소스 관리자를 호출 하 고 Azure Directory Federated Services (AD FS)에 대 한 인증 끝점을 포함 하 여 끝점을 검색 합니다.

    ![로그인 및 Azure Stack에 연결 되 면 클라우드 탐색기의 스크린샷](./media/azure-stack-install-visual-studio/image6.png)

6. 선택 **클라우드 탐색기** 에서 합니다 **보기** 메뉴.

7. 선택 **계정 추가** ADFS 자격 증명으로 로그인 합니다.  

    ![Cloud Explorer](./media/azure-stack-install-visual-studio/image7.png)

    클라우드 탐색기 사용 가능한 구독을 쿼리합니다. 관리 하는 사용 가능한 구독을 선택할 수 있습니다.

    ![Cloud Explorer](./media/azure-stack-install-visual-studio/image8.png)

8. 기존 리소스, 리소스 그룹을 통해 찾아보거나 템플릿을 배포 합니다.

## <a name="next-steps"></a>다음 단계

- Visual Studio에 대해 자세히 알아보세요 [나란히](/visualstudio/install/install-visual-studio-versions-side-by-side) 다른 Visual Studio 버전을 사용 하 여 합니다.
- [Azure Stack 용 템플릿 개발](azure-stack-develop-templates.md)합니다.