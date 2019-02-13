---
title: Azure Stack 사용자에 게 가상 컴퓨터를 사용할 수 있도록 | Microsoft Docs
description: Azure Stack에서 virtual machines를 사용할 수 있도록 하는 방법 알아보기
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: unknown
ms.lastreviewed: 09/11/2018
ms.custom: mvc
ms.openlocfilehash: a24b7314775ea7667ab992742586e5b5c8bbb369
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56205160"
---
# <a name="tutorial-make-virtual-machines-available-to-your-azure-stack-users"></a>자습서: 사용자에 게 가상 머신을 사용할 수 있는 Azure Stack

Azure Stack 클라우드 관리자 (테 넌 트 라고도 함) 사용자가 구독할 수 있는 제품을 만들 수 있습니다. 제품을 구독 하 여 사용자가 제공 하는 서비스를 제공 하는 Azure Stack 서비스를 사용할 수 있습니다.

이 자습서에는 가상 컴퓨터에 대 한 제품을 만들고 다음 제품을 테스트 하려면 사용자로 로그인 하는 방법을 보여 줍니다.

학습할 내용:

> [!div class="checklist"]
> * 제품 만들기
> * 이미지 추가
> * 제품 테스트

Azure Stack에서 서비스 구독, 제품 및 계획을 사용 하 여 사용자에 게 전달 됩니다. 사용자는 여러 제품을 구독할 수 있습니다. 제품에는 하나 이상의 계획이 있을 수 있습니다 하 고 계획을 하나 이상의 서비스를 포함할 수 있습니다.

![구독, 제품 및 계획](media/azure-stack-key-features/image4.png)

자세한 내용은 참조 하세요 [주요 기능 및 Azure Stack에서 개념](azure-stack-key-features.md)합니다.

## <a name="create-an-offer"></a>제품 만들기

제품은 공급자를 구입 하거나 구독 하는 사용자에 게 제공 하는 하나 이상의 계획의 그룹입니다. 제품을 만드는 프로세스는 몇 가지 단계가 있습니다. 먼저 묻는 제품을 고 계획을 마지막으로 할당량을 만듭니다.

1. [에 로그인](azure-stack-connect-azure-stack.md) 클라우드 관리자와 선택 포털로 **+ 리소스 만들기** > **제공 + 계획** > **제공**합니다.

   ![새 제안](media/azure-stack-tutorial-tenant-vm/image01.png)

1. **새 제품**, 입력을 **표시 이름** 및 **리소스 이름**를 선택한 다음 새 또는 기존 **리소스 그룹**합니다. 표시 이름은 제품의 친숙 한 이름이입니다. 클라우드 운영자만 관리자는 Azure 리소스 관리자 리소스로 제품 작업을 하는 데 사용 하는 이름인 리소스 이름을 볼 수 있습니다.

   ![표시 이름](media/azure-stack-tutorial-tenant-vm/image02.png)

1. 선택 **기본 계획**, 및는 **계획** 섹션에서 **추가** 제품에 새 계획을 추가 합니다.

   ![계획 추가](media/azure-stack-tutorial-tenant-vm/image03.png)

1. 에 **새 계획** 섹션을 채웁니다 **표시 이름** 하 고 **리소스 이름**합니다. 표시 이름은 사용자에 게 표시 하는 계획의 친숙 한 이름이입니다. 클라우드 운영자만 클라우드 운영자는 Azure 리소스 관리자 리소스로 계획 작업을 하는 데 사용 하는 이름인 리소스 이름을 볼 수 있습니다.

   ![표시 이름 계획](media/azure-stack-tutorial-tenant-vm/image04.png)

1. 선택 **Services**합니다. 서비스 목록에서 선택 **Microsoft.Compute**를 **Microsoft.Network**, 및 **Microsoft.Storage**합니다. 선택할 **선택** 계획에 이러한 서비스를 추가 합니다.

   ![계획 서비스](media/azure-stack-tutorial-tenant-vm/image05.png)

1. 선택 **할당량**를 선택한 다음 첫 번째 서비스에 대 한 할당량을 만들려고 할 수는 있습니다. IaaS 할당량을 계산, 네트워크 및 저장소 서비스에 대 한 할당량을 구성 하는 데 다음 예에서는 가이드로 사용 합니다.

   - 계산 서비스에 대 한 할당량을 먼저 만듭니다. 네임 스페이스 목록에서 선택 **Microsoft.Compute** 선택한 후 **새 할당량 만들기**합니다.

     ![새 할당량 만들기](media/azure-stack-tutorial-tenant-vm/image06.png)

   - **만들기 할당량**을 할당량에 대 한 이름을 입력 합니다. 변경 하거나 만들 할당량에 대해 표시 되는 할당량 값을 받아들일 수 있습니다. 이 예제에서는 기본 설정을 적용 하 고 선택 **확인**합니다.

     ![할당량 이름](media/azure-stack-tutorial-tenant-vm/image07.png)

   - 선택할 **Microsoft.Compute** 네임 스페이스 목록에 사용자가 만든 할당량을 선택 합니다. 할당량 계산 서비스에 연결합니다.

     ![할당량 선택](media/azure-stack-tutorial-tenant-vm/image08.png)

      네트워크 및 저장소 서비스에 대해 이러한 단계를 반복 합니다. 완료 되 면, 선택 **확인** 에 **할당량** 모든 할당량을 저장 합니다.

1. **새 계획**를 선택 **확인**합니다.

1. 아래 **계획**, 새 계획 선택 차례로 **선택**합니다.

1. **새 제안을**를 선택 **만들기**합니다. 제품 만들어지면 알림이 표시 됩니다.

1. 대시보드 메뉴에서 선택 **제공** 다음 만든 제품을 선택 합니다.

1. 선택 **상태 변경**를 선택한 **공용**합니다.

    ![공용 상태](media/azure-stack-tutorial-tenant-vm/image09.png)

## <a name="add-an-image"></a>이미지 추가

가상 컴퓨터 프로 비전 할 수 있습니다, 전에 Azure Stack marketplace에 이미지를 추가 해야 합니다. Azure Marketplace의 Linux 이미지를 포함 하 여 선택한 이미지를 추가할 수 있습니다.

연결 된 시나리오에서 작동 하는 경우 및 Azure Stack 인스턴스를 Azure에 등록 하는 경우 다음에서 다운로드할 수 있습니다 Windows Server 2016 VM 이미지를 Azure Marketplace에 설명 된 단계를 사용 하 여는 [다운로드 marketplace 항목을 Azure에서 Azure Stack](azure-stack-download-azure-marketplace-item.md) 항목입니다.

Marketplace에 다양 한 항목을 추가 하는 방법에 대 한 내용은 [The Azure Stack Marketplace](azure-stack-marketplace.md)합니다.

## <a name="test-the-offer"></a>제품 테스트

제품을 만들었으므로 이제이 테스트할 수 있습니다. 사용자로 로그인 하 게 됩니다 제품을 구독 하 고 가상 컴퓨터를 추가 합니다.

1. **제품 구독**

   a. 사용자 계정 사용 하 여 사용자 포털에 로그인 하 고 선택 합니다 **구독** 바둑판식으로 배열 합니다.
   - 통합된 시스템에 대 한 URL을 연산자의 지역 및 외부 도메인 이름에 따라 달라 집니다 및 형식 이어야 합니다 https://portal.&lt; *지역*&gt;.&lt; *FQDN*&gt;합니다.
   - Azure Stack 개발 키트를 사용 하는 경우 포털 주소가 https://portal.local.azurestack.external합니다.

   ![구독 가져오기](media/azure-stack-tutorial-tenant-vm/image10.png)

   b. **Subscription 얻기**에서 구독의 이름을 입력 합니다 **표시 이름** 필드입니다. 선택 **제공**에서 제안 중 하나를 선택 합니다 **제품을 선택** 목록. **만들기**를 선택합니다.

   ![제품 만들기](media/azure-stack-tutorial-tenant-vm/image11.png)

   다. 구독을 보려면 선택 **모든 서비스**, 한 다음는 **일반** 범주 선택 **구독**합니다. 서비스 구독에 포함 된 참조를 새 구독을 선택 합니다.

   >[!NOTE]
   >제품 구독 후 새 구독의 일부인 서비스를 확인 하려면 포털을 새로 고치려면 해야 합니다.

1. **가상 머신 프로비전**

   사용자 포털에서 새 구독을 사용 하 여 가상 컴퓨터 프로 비전 할 수 있습니다.

   a. 사용자 계정 사용 하 여 사용자 포털에 로그인 합니다.
      - 통합된 시스템에 대 한 URL을 연산자의 지역 및 외부 도메인 이름에 따라 달라 집니다 및 형식 이어야 합니다 https://portal.&lt; *지역*&gt;.&lt; *FQDN*&gt;합니다.
   - Azure Stack 개발 키트를 사용 하는 경우 포털 주소가 https://portal.local.azurestack.external합니다.

   b.  대시보드에서 선택 **+ 리소스 만들기** > **계산** > **Windows Server 2016 Datacenter 평가판**를 선택한 다음 **만들**합니다.

   다. **기본 사항**, 다음 정보를 제공 합니다.
      - 입력을 **이름**
      - 입력을 **사용자 이름**
      - 입력을 **암호**
      -  **구독**
      - 만들기는 **리소스 그룹** (또는 기존 항목을 선택 합니다.) 
      - 선택 **확인** 이 정보를 저장 합니다.

   d. **크기 선택**를 선택 **A1 표준**를 차례로 **선택**합니다.  

   e. **설정을**를 선택 **Virtual network**합니다.

   f. **가상 네트워크 선택**를 선택 **새로 만들기**합니다.

   g. **가상 네트워크 만들기**에서 모든 기본값을 적용 하 고 선택 **확인**합니다.

   h. 선택 **확인** 에 **설정** 네트워크 구성을 저장 합니다.

      i. **요약**를 선택 **확인** 가상 머신을 만듭니다.  

   j. 새 가상 컴퓨터를 보려면 선택 **모든 리소스**합니다. 가상 컴퓨터에 대 한 검색 하 고 검색 결과에서 해당 이름을 선택 합니다.

   
## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 제품 만들기
> * 이미지 추가
> * 제품 테스트

에 대해 알아보려면 다음 자습서로 이동 하는 방법.
> [!div class="nextstepaction"]
> [Azure Stack 사용자에 게 SQL database를 사용할 수 있도록](azure-stack-tutorial-sql-server.md)