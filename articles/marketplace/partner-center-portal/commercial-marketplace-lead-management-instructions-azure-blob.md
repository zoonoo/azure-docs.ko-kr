---
title: Azure Blob Storage를 사용하여 잠재 고객 관리 -Microsoft 상업용 Marketplace
description: Azure Blob을 사용하여 Microsoft AppSource 및 Azure Marketplace에 대한 잠재 고객을 구성하는 방법을 알아봅니다.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/01/2020
ms.author: dsindona
ms.openlocfilehash: 9ac7640dd8b63c72bac83c98e07cf861fb26c870
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/26/2020
ms.locfileid: "83845795"
---
# <a name="use-azure-blob-storage-to-manage-commercial-marketplace-leads"></a>Azure Blob Storage를 사용하여 상업용 Marketplace 잠재 고객 관리

>[!Caution]
>Azure Blob Storage에 대한 상업용 Marketplace 지원은 이제 사용되지 않으며 더 이상 제품의 잠재 고객을 처리하는 옵션이 아닙니다. 현재 Azure Blob에 대해 잠재 고객이 구성된 상업용 Marketplace 제품을 사용하는 경우 더 이상 잠재 고객을 받지 못합니다. 잠재 고객 관리 구성을 다른 잠재 고객 관리 옵션으로 업데이트하세요. [잠재 고객 관리 방문 페이지](./commercial-marketplace-get-customer-leads.md)에 있는 다른 옵션에 대해 알아보세요."

 파트너 센터에서 CRM(고객 관계 관리) 시스템을 명시적으로 지원하지 않아 Microsoft AppSource 및 Azure Marketplace 잠재 고객을 받을 수 없는 경우 Azure Blob Storage를 사용할 수 있습니다. 그런 다음 데이터를 내보내 CRM 시스템으로 가져오도록 선택할 수 있습니다. 이 문서의 지침에서는 Azure Storage 계정을 만들고 해당 계정 아래에 Blob을 만드는 프로세스를 안내합니다. 또한 Power Automate를 사용하여 새 흐름을 만들어 제품이 잠재 고객을 받으면 이메일 알림을 보낼 수 있습니다.

>[!NOTE]
>이러한 지침에서 사용되는 Power Automate 커넥터를 이용하려면 Power Automate에 대한 유료 구독이 필요합니다. 이 문서의 지침을 따르기 전에 이를 고려해야 합니다.

## <a name="configure-azure-blob-storage"></a>Azure Blob Storage 구성

1. Azure 계정이 없으면 [평가판 계정을 생성](https://azure.microsoft.com/pricing/free-trial/)할 수 있습니다.

2. Azure 계정이 활성화되면 [Azure Portal](https://portal.azure.com)에 로그인합니다.

3. Azure Portal에서 다음 절차를 사용하여 스토리지 계정을 만듭니다.  
    1. 왼쪽 메뉴 모음에서 **+리소스 만들기**를 선택합니다.  오른쪽에 **새로 만들기** 창(블레이드)이 표시됩니다.
    2. **새로 만들기** 창에서 **스토리지**를 선택합니다.  오른쪽에 **추천** 목록이 표시됩니다.
    3. **스토리지 계정**을 선택하여 계정 만들기를 시작합니다.  [스토리지 계정 만들기](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal) 문서의 지침을 따르세요.

    ![Azure Storage 계정을 만드는 단계](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-create.png)

    스토리지 계정에 대한 자세한 내용을 보려면 [빠른 시작 자습서](https://docs.microsoft.com/azure/storage/)를 선택하세요.  스토리지 가격에 대한 자세한 내용은 [스토리지 가격](https://azure.microsoft.com/pricing/details/storage/)을 참조하세요.

4. 스토리지 계정이 프로비전될 때까지 기다립니다. 이 프로세스는 일반적으로 몇 분 정도 걸립니다.  그런 다음 Azure Portal의 **홈** 페이지에서 **모든 리소스 보기**를 선택하거나 왼쪽 탐색 모음에서 **모든 리소스**를 선택하여 스토리지 계정에 액세스합니다.

    ![Azure 스토리지 계정에 액세스](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-access.png)

5. 스토리지 계정 창에서 **액세스 키**를 선택하고 키의 연결 문자열 값을 복사합니다. 이 값을 마켓플레이스 제품에 대한 잠재 고객을 받기 위해 게시 포털에서 제공해야 하는 스토리지 계정 연결 문자열 값으로 저장합니다.

     연결 문자열의 예는 다음과 같습니다.

     ```sql
     DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
     ```

    ![Azure Storage 키](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-keys-2.png)

6. 스토리지 계정 페이지에서 **Blob**을 선택합니다.

   ![Azure Storage 키](./media/commercial-marketplace-lead-management-instructions-azure-blob/select-blobs.png)

7. Blob 페이지에서 **+ 컨테이너** 단추를 선택합니다.

8. 새 컨테이너의 **이름**을 입력합니다. 컨테이너 이름은 소문자여야 하고, 문자 또는 숫자로 시작해야 하며, 문자, 숫자 및 대시(-) 문자만 포함할 수 있습니다. 컨테이너 및 BLOB 이름에 대한 자세한 내용은 [컨테이너, BLOB, 메타데이터 이름 지정 및 참조](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)를 참조하세요.

    이 값은 마켓플레이스 제품에 대한 잠재 고객을 받기 위해 게시 포털에서 제공해야 하는 컨테이너 이름 값이므로 저장합니다.

9. 컨테이너에 대한 공용 액세스 수준을 **개인(익명 액세스 없음)** 으로 설정합니다.

10. **확인**을 선택하여 컨테이너를 만듭니다.

    ![새 컨테이너](./media/commercial-marketplace-lead-management-instructions-azure-blob/new-container.png)

## <a name="configure-your-offer-to-send-leads-to-azure-blob-storage"></a>Azure Blob Storage에 잠재 고객을 보내도록 제품 구성

게시 포털에서 제품에 대한 잠재 고객 관리 정보를 구성할 준비가 되면 아래 단계를 수행합니다.

1. 제품에 대한 **제품 설정** 페이지로 이동합니다.
2. **잠재 고객** 섹션에서 **연결**을 선택합니다.

    :::image type="content" source="./media/commercial-marketplace-lead-management-instructions-azure-blob/customer-leads.png" alt-text="잠재 고객":::

3. 연결 세부 정보 팝업 창에서 잠재 고객 대상으로 **Azure Blob**을 선택합니다.

    ![연결 세부 정보](./media/commercial-marketplace-lead-management-instructions-azure-blob/connect-details.png) 

4. 다음 지침을 따라 얻은 **컨테이너 이름** 및 **스토리지 계정 연결 문자열**을 제공합니다.

    * 컨테이너 이름 예: `marketplaceleadcontainer`
    * 스토리지 계정 연결 문자열 예: `DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net` ![연결 세부 정보](./media/commercial-marketplace-lead-management-instructions-azure-blob/connection-details.png) 

5. **저장**을 선택합니다.

    > [!NOTE]
    > 제품에 대한 잠재 고객을 받으려면 먼저 나머지 제품 구성을 끝내고 게시해야 합니다.


