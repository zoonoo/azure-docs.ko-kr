---
title: "VHD를 만드는 동안 일반적인 문제 해결 방법 | Microsoft Docs"
description: "VHD 만드는 동안 문제를 해결하는 일반적인 질문 및 문제에 대답합니다."
services: Azure Marketplace
documentationcenter: 
author: HannibalSII
manager: 
editor: 
ms.assetid: e39563d8-8646-4cb7-b078-8b10ac35b494
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 09/26/2016
ms.author: hascipio; v-divte
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 3acef7399d5e8757ebad3d6178b22eb2c5c68660


---
# <a name="how-to-troubleshoot-common-issues-encountered-during-vhd-creation"></a>VHD를 만드는 동안 발생하는 일반적인 문제 해결 방법
이 문서는 가상 컴퓨터 솔루션을 게시하거나 관리하는 동안 문제가 발생하거나 관련된 일반적인 질문이 있는 Azure Marketplace 게시자 및/또는 공동 관리자를 돕기 위해 제공됩니다.

1. 호스트의 이름을 변경하려면 어떻게 해야 하나요?
   
    VM이 만들어지면 사용자는 호스트의 이름을 업데이트할 수 없습니다.
2. 원격 데스크톱 서비스 또는 해당 로그인 암호를 다시 설정하려면 어떻게 해야 하나요?
   
   * [Windows VM에 대한 참조](https://azure.microsoft.com/en-us/documentation/articles/virtual-machines-windows-reset-rdp/)
   * [Linux VM에 대한 참조](https://azure.microsoft.com/en-us/documentation/articles/virtual-machines-linux-classic-reset-access/)
3. 새 SSH 인증서를 생성하려면 어떻게 해야 하나요?
   
   [https://azure.microsoft.com/en-us/documentation/articles/virtual-machines-linux-classic-reset-access/](https://azure.microsoft.com/en-us/documentation/articles/virtual-machines-linux-classic-reset-access/) 링크를 참조하세요.
4. 열린 VPN 인증서를 구성하려면 어떻게 해야 하나요?
   
   [https://azure.microsoft.com/en-us/documentation/articles/vpn-gateway-point-to-site-create/](https://azure.microsoft.com/en-us/documentation/articles/vpn-gateway-point-to-site-create/) 링크를 참조하세요.
5. Microsoft Azure 가상 컴퓨터 환경(infrastructure-as-a-service)에서 Microsoft 서버 소프트웨어를 실행하기 위한 지원 정책은 무엇인가요?
   
   [https://support.microsoft.com/en-us/kb/2721672](https://support.microsoft.com/en-us/kb/2721672) 링크를 참조하세요.
6. 가상 컴퓨터는 고유 식별자를 가지고 있나요?
   
   Azure는 모든 VM에 Azure VM 고유 ID를 인코딩합니다. 이 블로그 및 문서에서 세부 정보를 참조하세요.
7. VM에서 시작 태스크에 있는 사용자 지정 스크립트 확장을 어떻게 관리할 수 있나요?
   
   [https://azure.microsoft.com/en-us/documentation/articles/virtual-machines-windows-extensions-customscript/](https://azure.microsoft.com/en-us/documentation/articles/virtual-machines-windows-extensions-customscript/) 링크를 참조하세요.
8. Premium Storage에 업로드된 VHD를 사용하여 Azure Portal에서 VM을 만들려면 어떻게 해야 하나요?
   
   이 기능을 지원하지 않습니다.
9. 32비트 앱은 Azure Marketplace에서 지원되나요?
   
   지원 정책에 대한 자세한 정보는 [https://support.microsoft.com/en-us/kb/2721672](https://support.microsoft.com/en-us/kb/2721672) 링크를 참조하세요.
10. 내 VHD에서 이미지를 만들려고 할 때마다 PowerShell에서 "리소스인 이미지 리포지토리로 VHD를 이미 등록했습니다." 오류가 표시됩니다. 이미지를 만들지 않았거나 Azure에서 이 이름을 가진 이미지를 찾지 못했습니다. 이 문제를 해결하려면 어떻게 해야 하나요?
    
    이 문제는 일반적으로 사용자가 이 VHD에서 VM을 프로비전하고 해당 VHD에 대한 잠금이 있는 경우에 발생합니다. 이 VHD에서 할당된 VM이 없는지 확인합니다. 오류가 계속되는 경우 이 링크를 사용하거나 관련된 게시 포털에서 지원 티켓을 제기합니다.(질문 11의 답변에서 세부 정보를 제공).
11. 내 문제에 대한 지원 티켓을 제기하려면 어떻게 해야 하나요?
    
    다음 단계를 따르세요.
    
    * 지원 티켓을 제기하기 전에 Azure 인증 파트너인지 확인합니다. 그렇지 않은 경우 이 [링크](https://azure.microsoft.com/en-us/documentation/articles/marketplace-publishing-azure-certification/)를 사용하여 Azure 인증 프로그램에 적용합니다.
    * 승인된 Azure 인증 파트너인 경우 Microsoft 계정을 사용하여 [게시 포털](https://publish.windowsazure.com/)에 로그인합니다.Microsoft 계정이 없는 경우 이 [링크](https://signup.live.com/signup?uaid=0089f09ccae94043a0f07c2aaf928831&lic=1)를 사용하여 계정을 만들 수 있습니다.
    * 사용자 이름 아래에 있는 메뉴에서 "지원 받기" 링크를 클릭합니다(아래 스크린샷 참조).
      
      ![drawing](media/marketplace-publishing-vm-image-creation-troubleshooting/img11_01.png)
    * 문제 유형을 "VM 이미지 인증"으로, 범주를 "Azure VM 마켓플레이스에 대한 VHD 준비"로 선택하고 "요청 시작" 단추를 클릭합니다.
    * 세부 정보를 입력하고 티켓을 제출합니다. 지원 팀이 문제를 해결하도록 도움을 줍니다.




<!--HONumber=Nov16_HO3-->


