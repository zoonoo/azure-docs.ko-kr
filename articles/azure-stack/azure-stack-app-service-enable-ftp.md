---
title: "Azure Stack의 App Service에서 FTP를 사용하도록 설정 | Microsoft Docs"
description: "Azure Stack의 App Service에서 FTP를 사용하도록 설정하기 위해 완료할 단계"
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: stefsch
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 4/6/2017
ms.author: erikje
ms.openlocfilehash: 9cadc57831ac7f7e5d32b10a4a87dab3fac02958
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="enable-ftp-in-app-service-on-azure-stack"></a>Azure Stack의 App Service에서 FTP를 사용하도록 설정

FTP 게시를 사용하도록 설정하려는 경우 Azure Stack에서 App Service를 성공적으로 배포했으면 테넌트에서 응용 프로그램 파일 및 콘텐츠를 업로드할 수 있으므로 완료해야 하는 추가 단계가 있습니다.  이후 릴리스에서는 이러한 단계가 자동화됩니다.

> [!NOTE]
> 이러한 단계는 Azure Stack 리소스 공급자에서 App Service를 구성하는 서비스 또는 엔터프라이즈 관리자를 위한 것입니다.

## <a name="enable-ftp"></a>FTP를 사용하도록 설정

1.  서비스 관리자로 Azure Stack 포털에 로그인합니다.
2.  **네트워크 인터페이스**로 이동한 후 **리소스 그룹** - **앱 서비스-로컬**에서 **FTP-NIC**를 선택합니다. ![Azure Stack 네트워크 인터페이스][1]
3.  **FTP-NIC**의 **공용 IP 주소**를 적어둡니다. 
![Azure Stack 네트워크 인터페이스 정보][2]
4.  다음에는 **Virtual Machines**로 이동한 후 **FTP0-VM**을 선택합니다. ![Azure Stack Virtual Machines][3]
5.  **연결** 단추를 사용하여 VM에 대한 원격 데스크톱 세션을 열고 App Service 배포 중에 설정한 관리자 자격 증명을 사용하여 세션에 로그인합니다.  
![Azure Stack 가상 컴퓨터 정보][4]
6.  FTP VM(FTP0-VM)에서 **IIS(인터넷 정보 서비스) 관리자**를 엽니다.
7.  **사이트**에서 **FTP 호스트 사이트**를 선택합니다.
8.  **FTP 방화벽 지원**을 엽니다. ![App Service FTP0-VM의 IIS 관리자][5]
9.  FTP-NIC의 공용 IP 주소를 입력하고 **적용** ![IIS 관리자 FTP 방화벽 지원][6]을 클릭합니다.

## <a name="validate-the-enabling-of-ftp"></a>FTP 사용 유효성 검사

1.  서비스 관리자 또는 테넌트로 Azure Stack 포털에 로그인합니다.
2.  **App Services**로 이동한 후 만든 웹앱, 모바일 앱 또는 API 앱을 선택합니다. ![App Services][7]
3.  응용 프로그램 세부 정보에서 **FTP 호스트 이름** 및 **FTP/배포 사용자 이름**을 적어둡니다. ![App Service 앱 정보][8]
> [!NOTE]
> **FTP/배포 사용자 이름** 아래에 아무 항목도 표시되지 않으면 먼저 **배포 자격 증명** 블레이드를 사용해서 배포 자격 증명을 설정해야 합니다.

4.  Windows 탐색기를 열고 파일 주소 표시줄에 FTP 호스트 이름(예: ftp://ftp.appservice.azurestack.local)을 입력합니다.
5.  **배포 자격 증명**을 입력하도록 요구되면 3단계에서 적어둔 정보를 입력합니다. 이 기능이 사용되도록 설정되면 App Service의 응용 프로그램 콘텐츠를 포함하는 디렉터리 목록이 표시됩니다. ![FTP 파일 나열][9]
<!--Image references-->
[1]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-network-interfaces.png
[2]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-network-interface-details.png
[3]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-virtual-machines.png
[4]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-virtual-machines-FTP0-VM.png
[5]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-IIS-Manager.png
[6]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-IIS-Manager-FTP-Firewall-Support.png
[7]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-validate-app-services.png
[8]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-validate-app-service-app-detail.png
[9]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-validate-ftp-file-listing.png
