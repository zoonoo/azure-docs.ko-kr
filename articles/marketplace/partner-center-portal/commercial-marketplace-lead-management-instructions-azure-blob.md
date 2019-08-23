---
title: 상용 marketplace 프로그램의 Azure 테이블 | Azure Marketplace
description: Azure Blob에 대 한 리드 관리 구성
services: Azure, Marketplace, commercial marketplace, Partner Center
author: qianw211
ms.service: marketplace
ms.topic: conceptual
ms.date: 7/30/2019
ms.author: evansma
ms.openlocfilehash: c67855422808f5ec4c81242edcece4e447f2f44f
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69902365"
---
# <a name="lead-management-instructions-for-azure-blob"></a>Azure Blob에 대 한 리드 관리 지침

>[!Caution]
>Marketplace 제품의 리드를 처리 하는 Azure Blob 옵션은 더 이상 사용 되지 않습니다. Azure Blob에 대 한 리드 관리 구성과 함께 게시 된 제품이 있는 경우 더 이상 고객 리드를 받고 있지 않습니다. 리드 관리 구성을 다른 리드 관리 옵션으로 업데이트 하세요. [리드 관리 방문 페이지](./commercial-marketplace-get-customer-leads.md)에서 다른 옵션에 대해 알아봅니다. "

CRM (고객 관계 관리) 시스템을 파트너 센터에서 명시적으로 지원 하지 않고 Azure Marketplace 및 AppSource 리드를 수신 하는 경우 Azure Blob을 사용 하 여 이러한 잠재 고객을 처리할 수 있습니다. 그런 다음 데이터를 내보내 CRM 시스템으로 가져오도록 선택할 수 있습니다. 이 문서의 지침에서는 Azure Storage 계정을 만드는 과정을 안내 하 고 해당 계정으로 Azure Blob을 제공 합니다. 또한 Microsoft Flow를 사용 하 여 새 흐름을 만들어 제품이 잠재 고객을 받을 때 전자 메일 알림을 보낼 수 있습니다.


## <a name="how-to-configure-azure-blob"></a>Azure Blob을 구성 하는 방법

1. Azure 계정이 없으면 [평가판 계정을 생성](https://azure.microsoft.com/pricing/free-trial/)할 수 있습니다.
1. Azure 계정이 활성화 되 면 [Azure Portal](https://portal.azure.com)에 로그인 합니다.
1. Azure Portal에서 다음 절차를 사용 하 여 저장소 계정을 만듭니다.  
    1. 왼쪽 메뉴 모음에서 **+ 리소스 만들기** 를 선택 합니다.  **새** 창 (블레이드)이 오른쪽에 표시 됩니다.
    2. **새** 창에서 **저장소** 를 선택 합니다.  **추천** 목록이 오른쪽에 표시 됩니다.
    3. 계정 만들기를 시작 하려면 **Storage 계정을** 선택 하세요.  [저장소 계정 만들기](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)문서의 지침을 따릅니다.

    ![Azure Storage 계정을 만드는 단계](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-create.png)

    저장소 계정에 대 한 자세한 내용을 보려면 [빠른 시작 자습서](https://docs.microsoft.com/azure/storage/)를 선택 합니다.  스토리지 가격에 대한 자세한 내용은 [스토리지 가격](https://azure.microsoft.com/pricing/details/storage/)을 참조하세요.

4. 저장소 계정이 프로 비전 될 때까지 기다린 후 일반적으로 몇 분 정도 소요 되는 프로세스입니다.  그런 다음, **모든 리소스 보기** 를 선택 하거나 Azure Portal의 왼쪽 탐색 모음에서 **모든 리소스** 를 선택 하 여 Azure Portal의 **홈** 페이지에서 저장소 계정에 액세스 합니다.

    ![Azure 저장소 계정에 액세스](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-access.png)

5. 저장소 계정 창에서 **액세스 키** 를 선택 하 고 키에 대 한 *연결 문자열* 값을 복사 합니다. Marketplace 제품에 대 한 잠재 고객을 받기 위해 게시 포털에서 제공 해야 하는 *저장소 계정 연결 문자열* 값으로이 값을 저장 합니다.

     연결 문자열 예는 다음과 같습니다.

     ```sql
     DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
     ```

    ![Azure Storage 키](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-keys-2.png)

6. 저장소 계정 페이지에서 **blob**을 선택 합니다.

   ![Azure Storage 키](./media/commercial-marketplace-lead-management-instructions-azure-blob/select-blobs.png)

7. Blob 페이지에서 **+ 컨테이너** 단추를 선택 합니다.

8. 새 컨테이너의 **이름을** 입력 합니다. 컨테이너 이름은 소문자여야 하고, 문자 또는 숫자로 시작해야 하며, 문자, 숫자 및 대시(-) 문자만 포함할 수 있습니다. 컨테이너 및 BLOB 이름에 대한 자세한 내용은 [컨테이너, BLOB, 메타데이터 이름 지정 및 참조](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)를 참조하세요.

    Marketplace 제품에 대 한 잠재 고객을 받기 위해 게시 포털에서 제공 해야 하는 *컨테이너 이름* 값으로이 값을 저장 합니다.

9. 컨테이너에 대 한 공용 액세스 수준을 **Private (익명 액세스 없음)** 로 설정 합니다.

10. **확인**을 선택하여 컨테이너를 만듭니다.

    ![새 컨테이너](./media/commercial-marketplace-lead-management-instructions-azure-blob/new-container.png)

## <a name="configure-your-offer-to-send-leads-to-the-azure-blob"></a>Azure Blob에 잠재 고객을 보내도록 제품 구성

게시 포털에서 제품에 대 한 리드 관리 정보를 구성할 준비가 되 면 다음 단계를 수행 합니다.

1. 제품에 대 한 **제품 설치** 페이지로 이동 합니다.
2. 리드 관리 섹션에서 **연결** 을 선택 합니다.

    ![제품 연결](./media/commercial-marketplace-lead-management-instructions-azure-blob/connect-offer.png)

3. 연결 정보 팝업 창에서 리드 대상에 대해 **Azure Blob** 을 선택 합니다.

    ![연결 정보](./media/commercial-marketplace-lead-management-instructions-azure-blob/connect-details.png) 

4. 이러한 지침에 따라 가져온 **컨테이너 이름** 및 **저장소 계정 연결 문자열** 을 제공 합니다.

    * 컨테이너 이름 예:`marketplaceleadcontainer`
    * 저장소 계정 연결 문자열 예: `DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net`![연결 정보](./media/commercial-marketplace-lead-management-instructions-azure-blob/connection-details.png) 

5. **저장**을 선택합니다.

    > [!NOTE]
    > 제안에 대 한 잠재 고객을 받으려면 먼저 나머지 제품을 구성 하 고 게시 해야 합니다.


