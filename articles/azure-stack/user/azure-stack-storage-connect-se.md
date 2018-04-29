---
title: 저장소 탐색기는 Azure 스택 구독 또는 저장소 계정에 연결 | Microsoft Docs
description: Azure 스택 구독에 저장소 탐색기를 연결 하는 방법에 알아봅니다
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/20/2018
ms.author: mattbriggs
ms.reviewer: xiaofmao
ms.openlocfilehash: 8b670ec7040aab7eca26d411c9e31a934052be19
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2018
---
# <a name="connect-storage-explorer-to-an-azure-stack-subscription-or-a-storage-account"></a>저장소 탐색기는 Azure 스택 구독 또는 저장소 계정에 연결

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

Azure 저장소 탐색기는 Windows, macOS 등 및 Linux에서 Azure 스택 저장소 데이터에 쉽게 사용할 수 있도록 하는 독립 실행형 앱입니다. Azure 스택 저장소에서 데이터를 이동할 수 있는 여러 도구가 있습니다. 자세한 내용은 참조 [데이터 전송 스택 Azure 저장소에 대 한 도구](azure-stack-storage-transfer.md)합니다.

이 문서에서는 Azure 스택 구독 및 저장소 탐색기를 사용 하 여 저장소 계정에 연결 하는 방법에 설명 합니다. 

저장소 탐색기를 아직 설치 하지 않은 경우 [다운로드](http://www.storageexplorer.com/) 하 고 설치 합니다.

Azure 스택 구독 또는 저장소 계정에 연결한 후 사용할 수 있습니다는 [Azure 저장소 탐색기 문서](../../vs-azure-tools-storage-manage-with-storage-explorer.md) Azure 스택 데이터로 작업할 수 있습니다. 

## <a name="prepare-for-connecting-to-azure-stack"></a>Azure 스택에 연결 하기 위한 준비

Azure 스택 구독에 액세스 하려면 Azure 스택 또는 저장소 탐색기에 대 한 VPN 연결에 대 한 직접 액세스를 해야 합니다. Azure Stack에 VPN 연결을 설정하는 방법은 [VPN을 사용하여 Azure Stack에 연결](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn)을 참조하세요.

Azure 스택 개발 키트에 대 한 Azure 스택 기관 루트 인증서를 내보낼 필요가 있습니다.

### <a name="export-and-then-import-the-azure-stack-certificate"></a>내보낸 후 Azure 스택 인증서를 가져옵니다

1. 열기 `mmc.exe` Azure 스택 호스트 컴퓨터를 또는 Azure 스택에 VPN 연결을 사용 하는 로컬 컴퓨터에 있습니다. 

2. **파일**을 선택 **스냅인 추가/제거**, 한 다음 추가 **인증서** 관리할 **내 사용자 계정**합니다.

3. 아래 **콘솔 Root\Certificated (로컬 컴퓨터) \Trusted Root Certification Authorities\Certificates** 찾을 **AzureStackSelfSignedRootCert**합니다.

    ![mmc.exe를 통해 Azure Stack 루트 인증서 로드](./media/azure-stack-storage-connect-se/add-certificate-azure-stack.png)

4. 인증서를 마우스 오른쪽 단추로 클릭, 선택 **모든 작업** > **내보내기**를 사용 하 여 인증서를 내보내려면 지침을 따르세요 **e-64로 인코딩된 X.509 (합니다. CER)** 합니다.  

    내보낸 인증서는 다음 단계에서 사용됩니다.

5. 저장소 탐색기를 시작한 있고이 **Azure 저장소에 연결** 대화 상자에서 취소 합니다.

6. 에 **편집** 메뉴에서 **SSL 인증서**를 선택한 후 **가져오기 인증서**합니다. 파일 선택 대화 상자를 사용하여 이전 단계에서 내보낸 인증서를 찾아 엽니다.

    인증서를 가져오면 저장소 탐색기를 다시 시작하라는 메시지가 표시됩니다.

    ![저장소 탐색기로 인증서 가져오기](./media/azure-stack-storage-connect-se/import-azure-stack-cert-storage-explorer.png)

7. 저장소 탐색기는 다음 작업을 다시 시작 되 면 선택는 **편집** 메뉴 되어 있는지를 확인 하 고 **대상 Azure 스택** 을 선택 합니다. 선택되지 않았으면 선택한 다음 저장소 탐색기를 다시 시작하여 변경 내용을 적용합니다. 이 구성은 Azure Stack 환경과의 호환성을 위해 필요합니다.

    ![대상 Azure Stack이 선택되었는지 확인](./media/azure-stack-storage-connect-se/target-azure-stack.png)

## <a name="connect-to-an-azure-stack-subscription"></a>Azure Stack 구독에 연결

이제 저장소 탐색기 구독에 연결 하는 Azure 스택 준비가 되었습니다.

1. 저장소 탐색기의 왼쪽된 창에서 선택 **계정 관리**합니다.  
    로그인 하면 모든 Microsoft 구독 표시 됩니다.

2. Azure 스택 구독에 연결 하려면 **계정 추가**합니다.

    ![Azure Stack 계정 추가](./media/azure-stack-storage-connect-se/add-azure-stack-account.png)

3. Azure 저장소 대화 상자에 대 한 연결에서에서 **Azure 환경**을 선택 **Azure** 또는 **Azure 중국**, 사용 되는 Azure 스택 계정에 종속 되 고 그런 다음 선택 **로그인**합니다. 에 하나 이상의 활성 스택 Azure 구독과 연결 된 Azure 스택 계정을 사용해 로그인 합니다.

    ![Azure 저장소에 연결](./media/azure-stack-storage-connect-se/azure-stack-connect-to-storage.png)

4. Azure Stack 계정으로 성공적으로 로그인하면 왼쪽 창이 해당 계정과 연결된 Azure Stack 구독으로 채워집니다. Azure 스택 구독 작업할 원하는 선택한 다음 적용을 선택 합니다. (선택 하거나 모두 지정 하거나 나열 된 Azure 스택 구독을 선택 하면 모든 구독 확인란 토글 선택 취소 합니다.)

    ![클라우드 환경 사용자 지정 대화 상자에 정보를 입력한 후 Azure Stack 구독 선택](./media/azure-stack-storage-connect-se/select-accounts-azure-stack.png)  

    왼쪽 창은 선택한 Azure Stack 구독과 연결된 저장소 계정을 표시합니다.

    ![Azure Stack 구독 계정을 포함한 저장소 계정 목록](./media/azure-stack-storage-connect-se/azure-stack-storage-account-list.png)

## <a name="connect-to-an-azure-stack-storage-account"></a>Azure 스택 저장소 계정에 연결

저장소 계정 이름과 키 쌍을 사용 하 여 Azure 스택 저장소 계정에 연결할 수 있습니다.

1.  저장소 탐색기의 왼쪽된 창에서 계정 관리를 선택 합니다. 에 로그인 하는 모든 Microsoft 계정 표시 됩니다.

    ![계정 추가](./media/azure-stack-storage-connect-se/azure-stack-sub-add-an-account.png)

2.  Azure 스택 구독에 연결 하려면 **계정 추가**합니다.
 
    ![계정 추가](./media/azure-stack-storage-connect-se/azure-stack-use-a-storage-and-key.png)

3.  Azure 저장소 대화 상자에 대 한 연결을 선택 **저장소 계정 이름과 키를 사용 하 여**합니다.

4. 계정 이름을 입력 된 **계정 이름**에 계정 키를 붙여는 **계정 키** 텍스트 상자 **다른 (아래에 입력)** 에 **저장소 끝점 도메인** 하며 Azure 스택 끝점을 입력 합니다.  

    두 요소를 포함 하는 Azure 스택 끝점: 영역 및 Azure 스택 도메인 이름입니다. Azure 스택 개발 키트에 기본 끝점은 **local.azurestack.external**합니다. 확실 하지 않은 끝점에 대 한 경우 클라우드 관리자에 게 문의 합니다.

    ![이름 및 키를 연결 합니다.](./media/azure-stack-storage-connect-se/azure-stack-attach-name-and-key.png)

5.  **연결**을 선택합니다.
6.  저장소 계정에 연결 되 면 저장소 계정으로 표시 됩니다 (**외부, 기타**) 이름에 추가 됩니다.

    ![VMWINDISK](./media/azure-stack-storage-connect-se/azure-stack-vmwindisk.png)

## <a name="next-steps"></a>다음 단계
* [저장소 탐색기를 시작](../../vs-azure-tools-storage-manage-with-storage-explorer.md)
* [Azure 스택 저장소: 차이점과 고려 사항](azure-stack-acs-differences.md)
* Azure 저장소에 대 한 자세한 참조 [Microsoft Azure 저장소 소개](../../storage/common/storage-introduction.md)
