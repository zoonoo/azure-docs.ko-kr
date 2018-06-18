---
title: Azure 스택 사용자에 게 가상 컴퓨터를 사용할 수 있도록 | Microsoft Docs
description: Azure 스택에서 가상 컴퓨터를 사용할 수 있도록 하는 방법을 알아봅니다
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
ms.date: 06/07/2018
ms.author: jeffgilb
ms.reviewer: ''
ms.custom: mvc
ms.openlocfilehash: 9329cb0dbfa24cf239b820573ef7f642cdca9103
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/08/2018
ms.locfileid: "35248162"
---
# <a name="tutorial-make-virtual-machines-available-to-your-azure-stack-users"></a>자습서: 사용자에 게 가상 컴퓨터 사용 가능한 Azure 스택

Azure 스택 클라우드 관리자 (테 넌 트 라고도 함) 사용자가 구독할 수 있는 서비스를 만들 수 있습니다. 제공 하는 서비스를 구독 하 여 사용자가 제공 하는 서비스를 제공 하는 Azure 스택 서비스를 사용할 수 있습니다.

이 자습서에는 가상 컴퓨터에 대 한 제안을 만들고 다음 제품을 테스트 하려면 사용자로 로그인 하는 방법을 보여 줍니다.

학습 내용:

> [!div class="checklist"]
> * 제품 만들기
> * 이미지 추가
> * 제품 테스트

Azure 스택 서비스 구독, 이벤트, 및 계획을 사용 하 여 사용자에 게 배달 됩니다. 사용자가 여러 제품을 구독할 수 있습니다. 제공 하는 하나 이상의 계획을 가질 수 있습니다 및 계획을 하나 이상의 서비스를 가질 수 있습니다.

![구독, 이벤트, 및 계획](media/azure-stack-key-features/image4.png)

자세한 내용은 참고 [주요 기능 및 개념 Azure 스택의](azure-stack-key-features.md)합니다.

## <a name="create-an-offer"></a>제품 만들기

제안은 공급자를 구입 하거나 구독 하는 사용자에 게 제공 하는 하나 이상의 계획의 그룹입니다. 제공 하는 서비스를 만드는 과정은 몇 가지 단계가 있습니다. 첫째, 만들고 제품을 한 후 계획을 마지막으로, 할당량 로그인할지 묻는 합니다.

1. [로그인](azure-stack-connect-azure-stack.md) 클라우드 관리자와 선택으로 포털에 **새로** > **제공 + 계획** > **제공**합니다.

   ![새 제품](media/azure-stack-tutorial-tenant-vm/image01.png)

2. **새 제공**를 입력 한 **표시 이름** 및 **리소스 이름**, 선택한 다음 새 또는 기존 **리소스 그룹**합니다. 표시 이름은 제품의 친숙한 이름입니다. 클라우드 운영자만 리소스 이름을 볼 수 있습니다. 관리자가 Azure 리소스 관리자 리소스로 제품 작업을 하는 데 사용하는 이름입니다.

   ![표시 이름](media/azure-stack-tutorial-tenant-vm/image02.png)

3. 선택 **계획 기본**, 및는 **계획** 섹션에서 **추가** 제품에는 새 계획을 추가 하려면.

   ![계획 추가](media/azure-stack-tutorial-tenant-vm/image03.png)

4. 에 **새 계획** 섹션에서 입력 **표시 이름** 및 **리소스 이름**합니다. 표시 이름은는 사용자에 게 표시 하는 계획의 이름입니다. 클라우드 운영자만 리소스 이름을 볼 수 있습니다. 클라우드 운영자는 Azure 리소스 관리자 리소스에 따라 계획을 사용 하려면 사용 하는 이름입니다.

   ![계획 표시 이름입니다.](media/azure-stack-tutorial-tenant-vm/image04.png)

5. 선택 **서비스**합니다. 서비스 목록에서 선택 **Microsoft.Compute**, **Microsoft.Network**, 및 **Microsoft.Storage**합니다. 선택 **선택** 계획에 이러한 서비스를 추가 합니다.

   ![계획 서비스](media/azure-stack-tutorial-tenant-vm/image05.png)

6. 선택 **할당량**, 한 다음에 대 한 할당량을 만들려고 할 첫 번째 서비스를 선택 합니다. IaaS 할당량에 대 한 다음 예제에서는 계산, 네트워크 및 저장소 서비스에 대 한 할당량을 구성 하기 위한 가이드로 사용 합니다.

   - 계산 서비스에 대 한 할당량을 먼저 만듭니다. 네임 스페이스 목록에서 선택 **Microsoft.Compute** 선택한 후 **새 할당량 만들기**합니다.

     ![새 할당량 만들기](media/azure-stack-tutorial-tenant-vm/image06.png)

   - **할당량 만들기**, 할당량에 대 한 이름을 입력 합니다. 만드는 할당량에 대해 표시 되는 할당량 값 중 하나를 사용 하거나 변경 수 있습니다. 이 예제에서는 기본 설정을 그대로 적용 하 고 선택 **확인**합니다.

     ![할당량 이름](media/azure-stack-tutorial-tenant-vm/image07.png)

   - 선택 **Microsoft.Compute** 네임 스페이스 목록과 만든 할당량을 선택 합니다. 할당량 계산 서비스에 연결합니다.

     ![할당량을 선택 합니다.](media/azure-stack-tutorial-tenant-vm/image08.png)

      네트워크 및 저장소 서비스에 대해 이러한 단계를 반복 합니다. 완료 되 면 선택 **확인** 에 **할당량** 모든 할당량을 저장 합니다.

7. **새 계획**선택, **확인**합니다.

8. 아래 **계획**, 새 계획을 선택 및 **선택**합니다.

9. **새 제안을**선택, **만들기**합니다. 제안을 만들어지면 알림이 표시 됩니다.

10. 대시보드 메뉴에서 선택 **제공** 선택한 후 만든 제안을 선택 하십시오.

11. 선택 **상태 변경**를 선택한 다음 **공용**합니다.

    ![공개 상태](media/azure-stack-tutorial-tenant-vm/image09.png)

## <a name="add-an-image"></a>이미지 추가

가상 컴퓨터를 프로 비전 수 전에 Azure 스택 마켓플레이스 이미지를 추가 해야 합니다. Azure 마켓플레이스에서 Linux 이미지를 포함 하 여 원하는 이미지를 추가할 수 있습니다.

연결 된 시나리오에서 작동 하는 경우와 Azure 스택 인스턴스를 Azure에 등록 하는 경우 다음 다운로드할 수 있습니다 Windows Server 2016 VM 이미지는 Azure 마켓플레이스의 설명 된 단계를 사용 하 여는 [다운로드 마켓플레이스 항목을 Azure에서 Azure 스택](azure-stack-download-azure-marketplace-item.md) 항목입니다.

시장에 다른 항목을 추가 하는 방법에 대 한 정보를 참조 하십시오. [The Azure 스택 마켓플레이스](azure-stack-marketplace.md)합니다.

## <a name="test-the-offer"></a>제품 테스트

구독을 만든 했으므로 테스트할 수 있습니다. 사용자로 로그인 하 고, 제품을 구독 하 고, 다음 가상 컴퓨터를 추가 합니다.

1. **제품 구독**

   a. 사용자 계정 사용 하 여 사용자 포털에 로그인 하 고 선택 된 **구독** 바둑판식으로 배열입니다.
   - 통합된 된 시스템에 대 한 URL 운영자의 지역 및 외부 도메인 이름에 따라 달라 집니다 및 형식에 포함 될 https://portal.&lt; *지역*&gt;.&lt; *FQDN*&gt;합니다.
   - Azure 스택 개발 키트를 사용 하는 경우 포털 주소는 https://portal.local.azurestack.external합니다.

   ![구독 가져오기](media/azure-stack-subscribe-plan-provision-vm/image01.png)

   나. **구독**, 구독에 대 한 이름을 입력 된 **표시 이름** 필드입니다. 선택 **제공**에 제공 하는 중 하나를 선택 하 고는 **제안을 선택** 목록입니다. **만들기**를 선택합니다.

   ![제품 만들기](media/azure-stack-subscribe-plan-provision-vm/image02.png)

   다. 구독을 보려면 선택 **더 많은 서비스**를 선택한 후 **구독**합니다. 서비스는 구독에 포함 된 참조를 새 구독을 선택 합니다.

   >[!NOTE]
   >제공 하는 서비스를 구독 한 후에 새 구독의 일부인 서비스를 확인 하기 위해 포털을 새로 고쳐야 할 수 있습니다.

2. **가상 머신 프로비전**

   사용자 포털에서 새 구독을 사용 하 여 가상 컴퓨터 프로 비전 할 수 있습니다.

   a. 사용자 계정 사용 하 여 사용자 포털에 로그인 합니다.
      - 통합된 된 시스템에 대 한 URL 운영자의 지역 및 외부 도메인 이름에 따라 달라 집니다 및 형식에 포함 될 https://portal.&lt; *지역*&gt;.&lt; *FQDN*&gt;합니다.
   - Azure 스택 개발 키트를 사용 하는 경우 포털 주소는 https://portal.local.azurestack.external합니다.

   나.  대시보드에서 선택 **새로** > **계산** > **Windows Server 2016 데이터 센터 Eval**를 선택한 후 **만들기**.

   다. **기본 사항**, 다음 정보를 제공 합니다.
      - 입력 한 **이름**
      - 입력 한 **사용자 이름**
      - 입력 한 **암호**
      - **구독**
      - 만들기는 **리소스 그룹** (또는 기존 템플릿을 선택 합니다.) 
      - 선택 **확인** 이 정보를 저장 합니다.

   d. **크기를 선택**선택, **표준 A1**, 차례로 **선택**합니다.  

   e. **설정**선택, **가상 네트워크**합니다.

   f. **가상 네트워크 선택**선택, **새로 만들기**합니다.

   g. **가상 네트워크 만들기**모든 기본값을 그대로 사용 하 고 선택 **확인**합니다.

   h. 선택 **확인** 에 **설정을** 네트워크 구성을 저장 합니다.

   ![가상 네트워크 만들기](media/azure-stack-provision-vm/image04.png)

   i. **요약**선택, **확인** 가상 컴퓨터를 만듭니다.  

   j. 새 가상 컴퓨터를 보려면 선택 **모든 리소스**합니다. 가상 컴퓨터에 대 한 검색 하 고 검색 결과에서 해당 이름을 선택 합니다.

   ![모든 리소스](media/azure-stack-provision-vm/image06.png)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 제품 만들기
> * 이미지 추가
> * 제품 테스트

자세한 내용은 다음 자습서로 이동 하는 방법:
> [!div class="nextstepaction"]
> [Azure 스택 사용자에 게 SQL 데이터베이스를 사용할 수 있게](azure-stack-tutorial-sql-server.md)