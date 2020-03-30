---
title: 상용 마켓플레이스 프로그램의 Azure 테이블 | Azure 마켓플레이스
description: Azure Blob에 대한 잠재 고객 관리 구성
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 7/30/2019
ms.author: dsindona
ms.openlocfilehash: 062252b007e22fcd2644c8b647fc0ecc2f5938cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80285251"
---
# <a name="lead-management-instructions-for-azure-blob"></a>Azure Blob에 대한 잠재 고객 관리 지침

>[!Caution]
>마켓플레이스 오퍼에서 잠재 고객을 처리하는 Azure Blob 옵션은 더 이상 사용되지 않습니다. Azure Blob에 대한 잠재 고객 관리 구성으로 게시된 오퍼가 현재 있는 경우 더 이상 고객 잠재 고객을 받지 않습니다. 잠재 고객 관리 구성을 다른 잠재 고객 관리 옵션으로 업데이트하십시오. [잠재 고객 관리 방문 페이지에서](./commercial-marketplace-get-customer-leads.md)다른 옵션에 대해 알아봅니다."

CrM(고객 관계 관리) 시스템이 Azure Marketplace 및 AppSource 잠재 고객을 수신하기 위해 파트너 센터에서 명시적으로 지원되지 않는 경우 Azure Blob을 사용하여 이러한 잠재 고객을 처리할 수 있습니다. 그런 다음 데이터를 내보내고 CRM 시스템으로 가져올 수 있습니다. 이 문서의 지침은 Azure Storage 계정 및 해당 계정 아래의 Azure Blob을 만드는 프로세스를 통해 제공합니다. 또한 Microsoft Flow를 사용하여 새 흐름을 만들어 오퍼가 잠재 고객용을 받을 때 전자 메일 알림을 보낼 수 있습니다.


## <a name="how-to-configure-azure-blob"></a>Azure Blob을 구성하는 방법

1. Azure 계정이 없으면 [평가판 계정을 생성](https://azure.microsoft.com/pricing/free-trial/)할 수 있습니다.
1. Azure 계정이 활성화되면 [Azure 포털에](https://portal.azure.com)로그인합니다.
1. Azure 포털에서 다음 절차를 사용하여 저장소 계정을 만듭니다.  
    1. 왼쪽 메뉴 모음에서 **+리소스 만들기를** 선택합니다.  **새** 창(블레이드)이 오른쪽에 표시됩니다.
    2. **새** 창에서 **저장소를** 선택합니다.  **추천** 목록이 오른쪽에 표시됩니다.
    3. 계정 만들기를 시작하려면 **저장소 계정을** 선택합니다.  [저장소 계정 만들기](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)문서의 지침을 따릅니다.

    ![Azure Storage 계정을 만드는 단계](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-create.png)

    저장소 계정에 대한 자세한 내용은 [빠른 시작 자습서를](https://docs.microsoft.com/azure/storage/)선택합니다.  스토리지 가격에 대한 자세한 내용은 [스토리지 가격](https://azure.microsoft.com/pricing/details/storage/)을 참조하세요.

4. 저장소 계정이 프로비전될 때까지 기다립니다( 일반적으로 몇 분 정도 소요되는 프로세스).  그런 다음 모든 리소스 보기를 선택하거나 Azure 포털의 왼쪽 탐색 메뉴모음에서 **모든 리소스를** 선택하여 Azure 포털의 **홈** 페이지에서 저장소 계정에 **액세스합니다.**

    ![Azure 저장소 계정에 액세스](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-access.png)

5. 저장소 계정 창에서 **액세스 키를** 선택하고 키에 대한 *연결 문자열* 값을 복사합니다. 이 값은 마켓플레이스 오퍼에 대한 잠재 고객을 받기 위해 게시 포털에서 제공해야 하는 *저장소 계정 연결 문자열* 값입니다.

     연결 찌르기의 예는 다음과 같은 것입니다.

     ```sql
     DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
     ```

    ![Azure Storage 키](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-keys-2.png)

6. 저장소 계정 페이지에서 **Blob을 선택합니다.**

   ![Azure Storage 키](./media/commercial-marketplace-lead-management-instructions-azure-blob/select-blobs.png)

7. Blob 페이지에서 **+ 컨테이너** 단추를 선택합니다.

8. 새 컨테이너의 **이름을** 입력합니다. 컨테이너 이름은 소문자여야 하고, 문자 또는 숫자로 시작해야 하며, 문자, 숫자 및 대시(-) 문자만 포함할 수 있습니다. 컨테이너 및 Blob 이름에 대한 자세한 내용은 [컨테이너, Blob 및 메타데이터의 이름 지정 및 참조를](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)참조하십시오.

    이 값은 게시 포털에서 마켓플레이스 오퍼에 대한 잠재 고객을 받기 위해 제공해야 하는 *컨테이너 이름* 값입니다.

9. 컨테이너에 대한 공용 액세스 수준을 **Private(익명 액세스 없음)으로**설정합니다.

10. **확인**을 선택하여 컨테이너를 만듭니다.

    ![새 컨테이너](./media/commercial-marketplace-lead-management-instructions-azure-blob/new-container.png)

## <a name="configure-your-offer-to-send-leads-to-the-azure-blob"></a>Azure Blob에 잠재 고객을 보내도록 오퍼 구성

게시 포털에서 오퍼의 잠재 고객 관리 정보를 구성할 준비가 되면 다음 단계를 따르십시오.

1. **오퍼의 제안 설정** 페이지로 이동합니다.
2. 잠재 고객 관리 섹션에서 **연결을** 선택합니다.

    ![연결 오퍼](./media/commercial-marketplace-lead-management-instructions-azure-blob/connect-offer.png)

3. 연결 세부 정보 팝업 창에서 잠재 고객 대상에 대한 **Azure Blob을** 선택합니다.

    ![세부 정보 연결](./media/commercial-marketplace-lead-management-instructions-azure-blob/connect-details.png) 

4. 다음 지침에서 얻은 **컨테이너 이름** 및 저장소 계정 **연결 문자열을** 제공합니다.

    * 컨테이너 이름 예제:`marketplaceleadcontainer`
    * 저장소 계정 연결 `DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net` ![문자열 예제: 연결 세부 정보](./media/commercial-marketplace-lead-management-instructions-azure-blob/connection-details.png) 

5. **저장**을 선택합니다.

    > [!NOTE]
    > 오퍼의 나머지 부분을 구성을 완료하고 게시해야 쿠폰에 대한 잠재 고객을 받을 수 있습니다.


