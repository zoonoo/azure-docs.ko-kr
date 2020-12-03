---
title: 등록 및 검색 Azure Data Lake Storage (ADLS) Gen1
description: 이 자습서에서는 Azure Data Lake Storage Gen1에서 Azure 부서의 범위로 데이터를 검색 하는 방법을 설명 합니다.
author: kchandra
ms.author: kchandra
ms.service: data-catalog
ms.subservice: data-catalog-gen2
ms.topic: how-to
ms.date: 11/30/2020
ms.openlocfilehash: ee0b9238deb7805113f0cbfa28d0b60a114820a9
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96555095"
---
# <a name="register-and-scan-azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1 등록 및 검색

이 문서에서는 Azure 부서의 범위에서 Azure Data Lake Storage Gen1를 데이터 원본으로 등록 하 고 검색을 설정 하는 방법을 설명 합니다.

> [!Note]
> 이제 Azure Data Lake Storage Gen2가 일반 공급됩니다. 오늘부터 사용을 시작하는 것이 좋습니다. 자세한 내용은 [제품 페이지](https://azure.microsoft.com/services/storage/data-lake-storage/)를 참조하세요.

## <a name="supported-capabilities"></a>지원되는 기능

Azure Data Lake Storage Gen1 데이터 원본은 다음과 같은 기능을 지원 합니다.

- Azure Data Lake Storage Gen1에서 메타 데이터 및 분류를 캡처하기 위한 **전체 및 증분 검색**

- ADF 복사/데이터 흐름 작업을 위한 데이터 자산 간 **계보**

## <a name="prerequisites"></a>필수 조건

- 데이터 원본을 등록 하기 전에 Azure 부서의 범위 계정을 만듭니다. 부서의 범위 계정을 만드는 방법에 대 한 자세한 내용은 [빠른 시작: Azure 부서의 범위 계정 만들기](create-catalog-portal.md)를 참조 하세요.
- Azure 부서의 범위 데이터 원본 관리자 여야 합니다.

## <a name="setting-up-authentication-for-a-scan"></a>검색에 대 한 인증 설정

Azure Data Lake Storage Gen1에 대해 지원 되는 인증 방법은 다음과 같습니다.

- 관리 ID
- 서비스 사용자

### <a name="managed-identity-recommended"></a>관리 Id (권장)

쉽고 보안을 위해 부서의 범위 MSI를 사용 하 여 데이터 저장소에 인증할 수 있습니다.

Data Catalog의 MSI를 사용 하 여 검색을 설정 하려면 먼저 부서의 범위 계정에 데이터 원본을 검색할 수 있는 권한을 부여 해야 합니다. 검사를 설정 *하기 전에* 이 단계를 수행 해야 합니다. 그렇지 않으면 검색이 실패 합니다.

#### <a name="adding-the-data-catalog-msi-to-an-azure-data-lake-storage-gen1-account"></a>Azure Data Lake Storage Gen1 계정에 Data Catalog MSI 추가

검색 권한을 부여할 대상에 따라 구독, 리소스 그룹 또는 리소스 수준에서 카탈로그의 MSI를 추가할 수 있습니다.

> [!Note]
> Azure 리소스에 관리 되는 id를 추가할 수 있으려면 구독의 소유자 여야 합니다.

1. [Azure Portal](https://portal.azure.com)에서 카탈로그를 검색할 수 있도록 허용 하려는 구독, 리소스 그룹 또는 리소스 (예: Azure Data Lake Storage Gen1 저장소 계정)를 찾습니다.

2. **개요** 를 클릭 한 다음 **데이터 탐색기** 를 선택 합니다.

   :::image type="content" source="./media/register-scan-adls-gen1/access-control.png" alt-text="액세스 제어 선택":::

3. 위쪽 탐색에서 **액세스** 를 클릭 합니다.

   :::image type="content" source="./media/register-scan-adls-gen1/access.png" alt-text="액세스를 클릭 합니다.":::

4. **추가** 를 클릭합니다. 사용자 또는 그룹 선택 선택 영역에 **부서의 범위 카탈로그** 를 추가 합니다. **읽기** 및 **실행** 권한을 선택 합니다. 아래 스크린샷에 표시 된 것 처럼 다음에 추가 옵션에서 **이 폴더와 모든 자식을** 선택 하 고 **확인** 
    :::image type="content" source="./media/register-scan-adls-gen1/managed-instance-authentication.png" alt-text="MSI 인증 세부 정보"::: 를 클릭 합니다.

5. 키 자격 증명 모음이 부서의 범위에 아직 연결 되지 않은 경우에는 [새 key vault 연결을 만들어야](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)합니다.

6. 마지막으로, 서비스 주체를 사용 하 여 [새 자격 증명을 만들어](manage-credentials.md#create-a-new-credential) 검색을 설정 합니다.
> [!Note]
> 데이터 원본에 카탈로그의 MSI를 추가한 후에는 검색을 설정 하기 전에 권한이 전파 될 때까지 최대 15 분이 소요 됩니다.

약 15 분 후 카탈로그에는 리소스를 검색할 수 있는 적절 한 권한이 있어야 합니다.

### <a name="service-principal"></a>서비스 사용자

서비스 주체를 사용 하려면 먼저 다음 단계를 수행 해야 합니다.

1. [Azure Portal](https://portal.azure.com)로 이동합니다.

2. 왼쪽 메뉴에서 **Azure Active Directory** 를 선택 합니다.

3. **앱 등록** 을 선택합니다.

4. **+ 새 애플리케이션 등록** 을 선택합니다.

5. **응용 프로그램** 의 이름 (서비스 사용자 이름)을 입력 합니다.

6. **이 조직 디렉터리 에서만 계정을** 선택 하십시오.

7. **URI 리디렉션** 에 대해 **웹** 을 선택 하 고 원하는 URL을 입력 합니다. 실제 또는 작업 일 필요는 없습니다.

8. 그런 다음, **등록** 을 선택합니다.

9. 표시 이름과 응용 프로그램 ID 모두에서 값을 복사 합니다.

#### <a name="adding-the-data-catalog-service-principal-to-an-azure-data-lake-storage-gen1-account"></a>Azure Data Lake Storage Gen1 계정에 Data Catalog 서비스 주체 추가
1. [Azure Portal](https://portal.azure.com)에서 카탈로그를 검색할 수 있도록 허용 하려는 구독, 리소스 그룹 또는 리소스 (예: Azure Data Lake Storage Gen1 저장소 계정)를 찾습니다.

2. **개요** 를 클릭 한 다음 **데이터 탐색기** 를 선택 합니다.

   :::image type="content" source="./media/register-scan-adls-gen1/access-control.png" alt-text="액세스 제어 선택":::

3. 위쪽 탐색에서 **액세스** 를 클릭 합니다.

   :::image type="content" source="./media/register-scan-adls-gen1/access.png" alt-text="액세스를 클릭 합니다.":::

4. **추가** 를 클릭합니다. 사용자 또는 그룹 선택 선택에 **서비스 사용자 응용 프로그램** 을 추가 합니다. **읽기** 및 **실행** 권한을 선택 합니다. 아래 스크린샷에 표시 된 것 처럼 다음에 추가 옵션에서 **이 폴더와 모든 자식을** 선택 하 고 **확인** 
    :::image type="content" source="./media/register-scan-adls-gen1/service-principal-authentication.png" alt-text="서비스 주체 인증 세부 정보"::: 를 클릭 합니다.

5. 키 자격 증명 모음이 부서의 범위에 아직 연결 되지 않은 경우에는 [새 key vault 연결을 만들어야](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)합니다.

6. 마지막으로, 서비스 주체를 사용 하 여 [새 자격 증명을 만들어](manage-credentials.md#create-a-new-credential) 검색을 설정 합니다.

## <a name="register-azure-data-lake-storage-gen1-data-source"></a>Azure Data Lake Storage Gen1 데이터 원본 등록

데이터 카탈로그에 새 ADLS Gen1 계정을 등록 하려면 다음을 수행 합니다.

1. 부서의 범위 Data Catalog로 이동 합니다.
2. 왼쪽 탐색 영역에서 **소스** 를 선택 합니다.
3. **등록** 을 선택합니다.
4. **소스 등록** 에서 **Azure Data Lake Storage Gen1** 를 선택 합니다. 
5. **계속** 을 선택합니다.

소스 등록 (Azure Data Lake Storage Gen1) 화면에서 다음을 수행 합니다.

1. 카탈로그에서 데이터 원본이 나열 될 **이름을** 입력 합니다.
2. 저장소 계정을 필터링 할 구독을 선택 합니다.
3. 스토리지 계정 선택
4. 컬렉션을 선택 하거나 새 컬렉션을 만듭니다 (선택 사항).
5. 데이터 원본 등록을 완료 합니다.

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>다음 단계

- [Azure 부서의 범위 Data catalog 찾아보기](how-to-browse-catalog.md)
- [Azure 부서의 범위 Data Catalog 검색](how-to-search-catalog.md)
