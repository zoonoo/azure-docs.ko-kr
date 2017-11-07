---
title: "저장소 탐색기는 스택 Azure 구독에 연결"
description: "저장소 Exporer 스택 Azure 구독에 연결 하는 방법에 알아봅니다"
services: azure-stack
documentationcenter: 
author: xiaofmao
manager: 
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 9/25/2017
ms.author: xiaofmao
ms.openlocfilehash: c7e6d70148d39fd74f6409a0a239833f8e9f7614
ms.sourcegitcommit: d03907a25fb7f22bec6a33c9c91b877897e96197
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/12/2017
---
# <a name="connect-storage-explorer-to-an-azure-stack-subscription"></a>저장소 탐색기는 스택 Azure 구독에 연결

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

Azure 저장소 탐색기 (미리 보기)는 Windows, macOS 등 및 Linux에서 Azure 스택 저장소 데이터에 쉽게 사용할 수 있도록 하는 독립 실행형 앱입니다. Azure 스택 저장소에서 데이터를 이동 하려면 여러 도구 사용할 수가 있습니다. 자세한 내용은 참조 [데이터 전송 스택 Azure 저장소에 대 한 도구](azure-stack-storage-transfer.md)합니다.

이 문서에서는 저장소 탐색기를 사용 하 여 Azure 스택 저장소 계정에 연결 하는 방법에 설명 합니다. 

저장소 탐색기를 아직 설치 하지 않은 경우 [다운로드](http://www.storageexplorer.com/) 및 설치 합니다.

Azure 스택 구독에 연결한 후 사용할 수 있습니다는 [Azure 저장소 탐색기 문서](../../vs-azure-tools-storage-manage-with-storage-explorer.md) Azure 스택 데이터로 작업할 수 있습니다. 

## <a name="prepare-an-azure-stack-subscription"></a>Azure 스택 구독 준비

Azure 스택 호스트 컴퓨터의 데스크톱 또는 스택 Azure 구독에 액세스 하려면 저장소 탐색기에 대 한 VPN 연결에 액세스할 수 있어야 합니다. Azure Stack에 VPN 연결을 설정하는 방법은 [VPN을 사용하여 Azure Stack에 연결](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn)을 참조하세요.

Azure 스택 개발 키트에 대 한 Azure 스택 기관 루트 인증서를 내보낼 필요가 있습니다.

### <a name="to-export-and-then-import-the-azure-stack-certificate"></a>내보낸 후 Azure 스택 인증서를 가져옵니다

1. 열기 `mmc.exe` Azure 스택 호스트 컴퓨터를 또는 Azure 스택에 VPN 연결을 사용 하는 로컬 컴퓨터에 있습니다. 

2. **파일**을 선택 **스냅인 추가/제거**, 한 다음 추가 **인증서** 관리할 **내 사용자 계정**합니다.



3. 아래 **콘솔 Root\Certificated (로컬 컴퓨터) \Trusted Root Certification Authorities\Certificates** 찾을 **AzureStackSelfSignedRootCert**합니다.

    ![mmc.exe를 통해 Azure Stack 루트 인증서 로드][25]

4. 인증서를 마우스 오른쪽 단추로 클릭, 선택 **모든 작업** > **내보내기**를 사용 하 여 인증서를 내보내려면 지침을 따르세요 **e-64로 인코딩된 X.509 (합니다. CER)**합니다.  

    내보낸 인증서는 다음 단계에서 사용됩니다.
5. 저장소 탐색기 (미리 보기)를 시작 있고이 **Azure 저장소에 연결** 대화 상자에서 취소 합니다.

6. 에 **편집** 메뉴에서 **SSL 인증서**, 클릭 하 고 **가져오기 인증서**합니다. 파일 선택 대화 상자를 사용하여 이전 단계에서 내보낸 인증서를 찾아 엽니다.

    인증서를 가져오면 저장소 탐색기를 다시 시작하라는 메시지가 표시됩니다.

    ![저장소 탐색기(미리 보기)로 인증서 가져오기][27]

이제 저장소 탐색기 구독에 연결 하는 Azure 스택 준비가 되었습니다.

### <a name="to-connect-an-azure-stack-subscription"></a>Azure 스택 구독을 연결 하려면


1. 저장소 탐색기(미리 보기)가 다시 시작되면 **편집** 메뉴를 선택하고, **대상 Azure Stack**이 선택되었는지 확인합니다. 선택되지 않았으면 선택한 다음 저장소 탐색기를 다시 시작하여 변경 내용을 적용합니다. 이 구성은 Azure Stack 환경과의 호환성을 위해 필요합니다.

    ![대상 Azure Stack이 선택되었는지 확인][28]

7. 왼쪽 창에서 **계정 관리**를 선택합니다.  
    로그인된 모든 Microsoft 계정이 표시됩니다.

8. Azure Stack 계정에 연결하려면 **계정 추가**를 선택합니다.

    ![Azure Stack 계정 추가][29]

9. 에 **Azure 저장소에 연결** 대화 상자의 **Azure 환경**선택, **사용 하 여 Azure 스택 환경**, 클릭 하 고 **다음**.

10. 활성 Azure 스택 구독을 하나 이상에 연관 된 Azure 스택 계정으로 로그인 하려면 입력에서 **Azure 스택 환경에 로그인** 대화 상자.  

    각 필드에 대한 세부 정보는 다음과 같습니다.

    * **환경 이름**: 사용자가 필드를 사용자 지정할 수 있습니다.
    * **ARM 리소스 끝점**: Azure Resource Manager 리소스 끝점의 샘플입니다.

        * 클라우드 운영자:<br> https://adminmanagement.local.azurestack.external   
        * 테 넌 트:<br> https://management.local.azurestack.external
 
    * **테 넌 트 Id**: 선택 사항입니다. 디렉터리를 지정해야 하는 경우 값을 지정합니다.

12. Azure Stack 계정으로 성공적으로 로그인하면 왼쪽 창이 해당 계정과 연결된 Azure Stack 구독으로 채워집니다. 작업하려는 Azure Stack 구독을 선택한 후 **적용**을 선택합니다. (**모든 구독** 확인란을 선택하면 나열된 모든 Azure Stack 구독이 선택되고, 선택 취소하면 아무 구독도 선택되지 않습니다.)

    ![클라우드 환경 사용자 지정 대화 상자에 정보를 입력한 후 Azure Stack 구독 선택][30]  
    왼쪽 창은 선택한 Azure Stack 구독과 연결된 저장소 계정을 표시합니다.

    ![Azure Stack 구독 계정을 포함한 저장소 계정 목록][31]

## <a name="next-steps"></a>다음 단계
* [저장소 탐색기 (미리 보기) 시작](../../vs-azure-tools-storage-manage-with-storage-explorer.md)
* [Azure 스택 저장소: 차이점과 고려 사항](azure-stack-acs-differences.md)


* Azure 저장소에 대 한 자세한 참조 [Microsoft Azure 저장소 소개](../../storage/common/storage-introduction.md)

[25]: ./media/azure-stack-storage-connect-se/add-certificate-azure-stack.png
[26]: ./media/azure-stack-storage-connect-se/export-root-cert-azure-stack.png
[27]: ./media/azure-stack-storage-connect-se/import-azure-stack-cert-storage-explorer.png
[28]: ./media/azure-stack-storage-connect-se/select-target-azure-stack.png
[29]: ./media/azure-stack-storage-connect-se/add-azure-stack-account.png
[30]: ./media/azure-stack-storage-connect-se/select-accounts-azure-stack.png
[31]: ./media/azure-stack-storage-connect-se/azure-stack-storage-account-list.png
