---
title: Storage 탐색기는 Azure Stack 구독 또는 저장소 계정에 연결 | Microsoft Docs
description: Storage 탐색기는 Azure Stack 구독에 연결 하는 방법 알아보기
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/24/2019
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.lastreviewed: 01/24/2019
ms.openlocfilehash: d1bddc8331fc1a9ded37949a8065636947074852
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55246738"
---
# <a name="connect-storage-explorer-to-an-azure-stack-subscription-or-a-storage-account"></a>Storage 탐색기는 Azure Stack 구독 또는 저장소 계정에 연결

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

이 문서에서는 Azure Stack 구독 및 storage 탐색기를 사용 하 여 저장소 계정에 연결 하는 방법에 알아봅니다. Azure storage 탐색기는 Windows, macOS 및 Linux에서 Azure Stack 저장소 데이터로 쉽게 작업할 수 있는 독립 실행형 앱입니다.

> [!NOTE]  
> Azure Stack storage 간에 데이터를 이동할 수 있는 여러 도구가 있습니다. 자세한 내용은 [데이터 전송 Azure Stack 저장소에 대 한 도구](azure-stack-storage-transfer.md)합니다.

Storage 탐색기를 아직 설치 하지 않은 경우 [storage 탐색기 다운로드](http://www.storageexplorer.com/) 하 고 설치 합니다.

Azure Stack 구독에 저장소 계정에 연결한 후 사용할 수 있습니다 합니다 [Azure storage 탐색기 문서](../../vs-azure-tools-storage-manage-with-storage-explorer.md) Azure Stack 데이터와 함께 작동 하도록 합니다. 

## <a name="prepare-for-connecting-to-azure-stack"></a>Azure Stack에 연결 하기 위한 준비

Azure Stack 또는 Azure Stack 구독에 액세스 하려면 storage 탐색기에 대 한 VPN 연결에 대 한 직접 액세스를 해야 합니다. Azure Stack에 VPN 연결을 설정하는 방법은 [VPN을 사용하여 Azure Stack에 연결](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn)을 참조하세요.

에 Azure Stack 개발 키트 ASDK (), Azure Stack 기관 루트 인증서 내보내기 해야 합니다.

> [!Note]  
> ASDK에 대 한 VPN 통해 프로그램 ASDK에 연결 하는 경우 VPN 설치 프로세스 중 생성 된 루트 인증서 (CA.cer)를 사용 하지 마세요.  DER로 인코딩된 인증서를 이며 Azure Stack 구독을 검색할 저장소 탐색기를 허용 하지 않습니다. Storage 탐색기를 사용 하는 Base-64로 인코딩된 인증서를 내보내려면 다음 단계를 수행 합니다.

### <a name="export-and-then-import-the-azure-stack-certificate"></a>내보내기 및 다음 Azure Stack 인증서를 가져오기

1. 열기 `mmc.exe` Azure Stack 호스트 컴퓨터를 또는 Azure Stack에 VPN 연결을 사용 하 여 로컬 컴퓨터입니다. 

2. **파일**를 선택 **스냅인 추가/제거**를 추가한 다음 **인증서** 관리 하 **내 사용자 계정**합니다.

3.  아래 **Root\Certificated (Local Computer) \Trusted Root Certification Authorities\Certificates 콘솔**합니다.

    - ASDK, 찾습니다 **AzureStackSelfSignedRootCert**합니다.

        ![mmc.exe를 통해 Azure Stack 루트 인증서 로드](./media/azure-stack-storage-connect-se/add-certificate-azure-stack.png)

    - 통합된 시스템에 대 한 외부 인증서의 루트 인증서를 찾습니다. 
    
        ![mmc.exe를 통해 Azure Stack 루트 인증서 로드](./media/azure-stack-storage-connect-se/azure-stack-storage-cert-location-is.png)
        

4. 인증서를 마우스 오른쪽 단추로 클릭 한 다음를 선택 합니다 **모든 작업** > **내보내기**를 사용 하 여 인증서를 내보내려면 지침에 따라 **Base-64로 인코딩된 X.509 (. CER)** 합니다.

    내보낸 인증서는 다음 단계에서 사용됩니다.

5. Storage 탐색기 시작 표시 하는 경우는 **Azure Storage에 연결** 대화 상자에서 취소 합니다.

6. 에 **편집할** 메뉴에서 **SSL 인증서**를 선택한 후 **인증서 가져오기**. 파일 선택 대화 상자를 사용하여 이전 단계에서 내보낸 인증서를 찾아 엽니다.

    인증서를 가져온 후 storage 탐색기를 다시 시작할 것인지 묻는 메시지가 나타납니다.

    ![Storage 탐색기로 인증서 가져오기](./media/azure-stack-storage-connect-se/import-azure-stack-cert-storage-explorer.png)

7. Storage 탐색기는 다음 작업을 다시 시작 되 면 선택 합니다 **편집** 하는지 확인 하 고 메뉴에서 **대상 Azure Stack** 을 선택 합니다. 그렇지 않은 경우 선택 **대상 Azure Stack**, 한 다음 변경 내용을 적용 하려면 storage 탐색기를 다시 시작 합니다. 이 구성은 Azure Stack 환경과의 호환성을 위해 필요합니다.

    ![대상 Azure Stack이 선택되었는지 확인](./media/azure-stack-storage-connect-se/target-azure-stack.png)

## <a name="connect-to-an-azure-stack-subscription-with-azure-ad"></a>Azure AD 사용 하 여 Azure Stack 구독에 연결

Storage 탐색기는 Azure Active Directory (Azure AD) 계정에 속한 Azure Stack 구독에 연결 하려면 다음 단계를 사용 합니다.

1. Storage 탐색기의 왼쪽된 창에서 선택 **계정 관리**합니다. 
    모든 Microsoft 구독에 로그인을 표시 됩니다.

2. Azure Stack 구독에 연결 하려면 선택 **계정 추가**합니다.

    ![Azure Stack 계정 추가](./media/azure-stack-storage-connect-se/add-azure-stack-account.png)

3. Azure Storage 대화 상자에 연결에서 아래 **Azure 환경**를 선택 **Azure** 또는 **Azure 중국**사용 되는 Azure Stack 계정에 따라 다름, 선택 **로그인** 하나 이상의 활성 Azure Stack 구독과 연결 된 Azure Stack 계정으로 로그인 합니다.

    ![Azure Storage에 연결](./media/azure-stack-storage-connect-se/azure-stack-connect-to-storage.png)

4. Azure Stack 계정으로 성공적으로 로그인하면 왼쪽 창이 해당 계정과 연결된 Azure Stack 구독으로 채워집니다. 작업하려는 Azure Stack 구독을 선택한 후 **적용**을 선택합니다. (**모든 구독** 확인란을 선택하면 나열된 모든 Azure Stack 구독이 선택되고, 선택 취소하면 아무 구독도 선택되지 않습니다.)

    ![클라우드 환경 사용자 지정 대화 상자에 정보를 입력한 후 Azure Stack 구독 선택](./media/azure-stack-storage-connect-se/select-accounts-azure-stack.png)

    왼쪽 창은 선택한 Azure Stack 구독과 연결된 저장소 계정을 표시합니다.

    ![Azure Stack 구독 계정을 포함한 저장소 계정 목록](./media/azure-stack-storage-connect-se/azure-stack-storage-account-list.png)

## <a name="connect-to-an-azure-stack-subscription-with-ad-fs-account"></a>ADFS 계정 사용 하 여 Azure Stack 구독에 연결

> [!Note]  
> Azure 페더레이션 서비스 (AD FS)의 로그인 환경 1.2.0 Storage 탐색기 또는 Azure Stack 1804 또는 최신 업데이트를 사용 하 여 최신 버전을 지원합니다.
Storage 탐색기는 ADFS 계정에 속한 Azure Stack 구독에 연결 하려면 다음 단계를 사용 합니다.

1. 선택 **계정 관리**합니다. 탐색기는 Microsoft 구독에 로그인을 나열 합니다.
2. 선택 **계정 추가** Azure Stack 구독에 연결 합니다.

    ![계정 추가](media/azure-stack-storage-connect-se/add-an-account.png)

3. **다음**을 선택합니다. Azure Storage 대화 상자에 연결에서 아래 **Azure 환경**를 선택 **사용 하 여 사용자 지정 환경**, 클릭 **다음**합니다.

    ![Azure Storage에 연결](media/azure-stack-storage-connect-se/connect-to-azure-storage.png)

4. Azure Stack 사용자 지정 환경의 필요한 정보를 입력 합니다. 

    | 필드 | 메모 |
    | ---   | ---   |
    | 환경 이름 | 사용자가 필드를 사용자 지정할 수 있습니다. |
    | Azure Resource Manager 끝점 | Azure Stack 개발 키트의 Azure Resource Manager 리소스 끝점의 샘플입니다.<br>연산자: https://adminmanagement.local.azurestack.external <br> 사용자: https://management.local.azurestack.external |

    작업 하는 경우 Azure Stack 통합 시스템 및 없습니다 관리 끝점을 알고, 귀하가 운영자에 게 문의 합니다.

    ![계정 추가](./media/azure-stack-storage-connect-se/custom-environments.png)

5. 선택 **로그인**하나 이상의 활성 Azure Stack 구독과 연결 된 Azure Stack 계정에 연결 합니다.



6. 사용 하려는 Azure Stack 구독을 선택 합니다. **적용**을 선택합니다.

    ![계정 관리](./media/azure-stack-storage-connect-se/account-management.png)

    왼쪽 창은 선택한 Azure Stack 구독과 연결된 저장소 계정을 표시합니다.

    ![연결 된 구독 목록](./media/azure-stack-storage-connect-se/list-of-associated-subscriptions.png)

## <a name="connect-to-an-azure-stack-storage-account"></a>Azure Stack 저장소 계정에 연결

저장소 계정 이름 및 키 쌍을 사용 하 여 Azure Stack 저장소 계정에 연결할 수 있습니다.

1. Storage 탐색기의 왼쪽된 창에서 계정 관리를 선택 합니다. 사용자가 로그인 하는 모든 Microsoft 계정이 표시 됩니다.

    ![계정 추가](./media/azure-stack-storage-connect-se/azure-stack-sub-add-an-account.png)

2. Azure Stack 구독에 연결 하려면 선택 **계정 추가**합니다.

    ![계정 추가](./media/azure-stack-storage-connect-se/azure-stack-use-a-storage-and-key.png)

3. Azure Storage 대화 상자에 대 한 연결을 선택 **저장소 계정 이름과 키를 사용 하 여**입니다.

4. 계정 이름을 입력 합니다 **계정 이름**, 계정 키를 붙여 넣습니다 합니다 **계정 키** 텍스트 상자에서 **다른 (아래 입력)** 에서 **저장소 끝점 도메인** 및 Azure Stack 끝점을 입력 합니다.

    두 부분을 포함 하는 Azure Stack 끝점: 지역 및 Azure Stack 도메인의 이름입니다. 기본 끝점의 Azure Stack 개발 키트 **local.azurestack.external**합니다. 확실 하지 않은 끝점에 대 한 경우 클라우드 관리자에 게 문의 합니다.

    ![이름 및 키를 연결 합니다.](./media/azure-stack-storage-connect-se/azure-stack-attach-name-and-key.png)

5. **연결**을 선택합니다.
6. 저장소 계정이 성공적으로 연결 된 저장소 계정을 사용 하 여 표시 됩니다 (**외부의 다른**) 이름에 추가 됩니다.

    ![VMWINDISK](./media/azure-stack-storage-connect-se/azure-stack-vmwindisk.png)

## <a name="next-steps"></a>다음 단계

* [Storage 탐색기 시작](../../vs-azure-tools-storage-manage-with-storage-explorer.md)
* [Azure Stack 저장소: 차이점 및 고려 사항](azure-stack-acs-differences.md)
* Azure storage에 대 한 자세한 내용은를 참조 하세요. [Microsoft Azure storage 소개](../../storage/common/storage-introduction.md)
