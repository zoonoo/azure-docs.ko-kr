---
title: Visual Studio를 설치 하 고 Azure 스택에 연결 | Microsoft Docs
description: Visual Studio를 설치 하 고 Azure 스택에 연결 하는 데 필요한 단계에 알아봅니다
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/08/2018
ms.author: mabrigg
ms.reviewer: unknown
ms.openlocfilehash: cbd5e5dbcdd2565e8066b0721f45863569bfd90a
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/11/2018
ms.locfileid: "35295033"
---
# <a name="install-visual-studio-and-connect-to-azure-stack"></a>Visual Studio를 설치 하 고 Azure 스택에 연결

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

Visual Studio를 사용 하 여 작성 하 고 Azure 리소스 관리자 배포 [템플릿](azure-stack-arm-templates.md) Azure 스택에 있습니다. 이 문서의 단계 과정을 단계별로에 Visual Studio를 설치는 [Azure 스택](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), 또는 통해 Azure 스택 하려는 경우 외부 컴퓨터는 [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn)합니다.

## <a name="install-visual-studio"></a>Visual Studio 설치

1. 다운로드 및 실행 된 [웹 플랫폼 설치 관리자](https://www.microsoft.com/web/downloads/platform.aspx)합니다.  

2. 열기는 **Microsoft 웹 플랫폼 설치 관리자**합니다.

3. 검색할 **2.9.6-Microsoft Azure SDK와 Visual Studio Community 2015**합니다. 클릭 **추가**, 및 **설치**합니다.

4. 제거는 **Microsoft Azure PowerShell** Azure SDK의 일부로 설치 된 합니다.

    ![WebPI 스크린샷 설치 단계](./media/azure-stack-install-visual-studio/image1.png) 

5. [Azure Stack용 PowerShell 설치](azure-stack-powershell-install.md)

6. 설치가 완료 되는 운영 체제를 다시 시작 합니다.

## <a name="connect-to-azure-stack-with-azure-ad"></a>Azure AD와 Azure 스택에 연결

1. Visual Studio를 시작합니다.

2. **보기** 메뉴 선택 **클라우드 탐색기**합니다.

3. 새 창에서 선택 **계정 추가** 및 Azure Active Directory (Azure AD) 자격 증명을 사용 하 여 로그인 합니다.  

    ![클라우드 탐색기의 스크린 샷에서 한 번에 로그인 하 고 Azure 스택에 연결](./media/azure-stack-install-visual-studio/image2.png)

로그인 한 다음을 할 수 있습니다 [템플릿을 배포할](azure-stack-deploy-template-visual-studio.md) 하거나 사용자 고유의 템플릿을 만들 수 있는 사용 가능한 리소스 유형과 리소스 그룹을 찾습니다.  

## <a name="connect-to-azure-stack-with-ad-fs"></a>AD FS와 Azure 스택에 연결

1. Visual Studio를 시작합니다.

2. **도구**선택, **옵션**합니다.

3. 확장 **환경** 에 **탐색 창** 선택 **계정**합니다.

4. 선택 **추가**, 사용자 Azure 리소스 관리자 끝점을 입력 하 고 있습니다.  
  Azure 스택 개발 키트에 대 한 URL이: `https://management.local.azurestack/external`합니다.  
  Azure 스택 통합 시스템 URL은: `https://management.[Region}.[External FQDN]`합니다.

    ![X](./media/azure-stack-install-visual-studio/image5.png)

5. **추가**를 선택합니다.  

    Visual Studio는 Azure 리소스 관리자를 호출 하 고 인증 끝점을 포함 하 여 Azure Directory Federated Services (AD FS)에 대 한 끝점을 검색 합니다.

    ![클라우드 탐색기의 스크린 샷에서 한 번에 로그인 하 고 Azure 스택에 연결](./media/azure-stack-install-visual-studio/image6.png)

6. 선택 **클라우드 탐색기** 에서 **보기** 메뉴.
7. 선택 **계정 추가** 및 AD FS 자격 증명을 사용 하 여 로그인 합니다.  

    ![X](./media/azure-stack-install-visual-studio/image7.png)

    클라우드 탐색기 사용 가능한 구독을 쿼리합니다. 하나를 관리 하는 사용 가능한 구독을 선택할 수 있습니다.

    ![X](./media/azure-stack-install-visual-studio/image8.png)

8. 기존 리소스, 리소스 그룹을 검색 또는 템플릿을 배포 합니다.

## <a name="next-steps"></a>다음 단계

 - 에 대해 자세히 알아보세요 [공존](https://msdn.microsoft.com/library/ms246609.aspx) 다른 Visual Studio 버전과 함께 합니다.
 - [Azure Stack용 템플릿 개발](azure-stack-develop-templates.md)