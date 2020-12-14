---
title: Azure storage blob을 검색 하는 방법
description: Azure 부서의 범위 data catalog에서 Azure blob storage를 검색 하는 방법을 알아봅니다.
author: hophanms
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/25/2020
ms.openlocfilehash: 1bcd8390a298d7fc46f9c04633f610eb4492d33d
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/14/2020
ms.locfileid: "97400712"
---
# <a name="register-and-scan-azure-blob-storage"></a>Azure Blob Storage 등록 및 검사

이 문서에서는 부서의 범위에 Azure Blob Storage 계정을 등록 하 고 검색을 설정 하는 방법을 설명 합니다.

## <a name="supported-capabilities"></a>지원되는 기능

Azure Blob Storage는 메타 데이터 및 스키마를 캡처하기 위한 전체 및 증분 검색을 지원 합니다. 또한 시스템 및 사용자 지정 분류 규칙을 기반으로 데이터를 자동으로 분류 합니다.

## <a name="prerequisites"></a>필수 구성 요소

- 데이터 원본을 등록 하기 전에 Azure 부서의 범위 계정을 만듭니다. 부서의 범위 계정을 만드는 방법에 대 한 자세한 내용은 [빠른 시작: Azure 부서의 범위 계정 만들기](create-catalog-portal.md)를 참조 하세요.
- Azure 부서의 범위 데이터 원본 관리자 여야 합니다.

## <a name="setting-up-authentication-for-a-scan"></a>검색에 대 한 인증 설정

Azure blob 저장소에 대 한 인증을 설정 하는 방법에는 다음 세 가지가 있습니다.

- 관리 ID
- 계정 키
- 서비스 주체

### <a name="managed-identity-recommended"></a>관리 Id (권장)

**관리 id** 를 선택 하 여 연결을 설정 하려면 먼저 부서의 범위 계정에 데이터 원본을 검색할 수 있는 권한을 부여 해야 합니다.

1. 스토리지 계정으로 이동합니다.
1. 왼쪽 탐색 메뉴에서 **Access Control (IAM)** 을 선택 합니다. 
1. **+ 추가** 를 선택합니다.
1. **역할** 을 **저장소 Blob 데이터 판독기** 로 설정 하 고 입력 상자 **선택** 상자에 Azure 부서의 범위 계정 이름을 입력 합니다. 그런 다음, **저장** 을 선택 하 여 부서의 범위 계정에이 역할 할당을 제공 합니다.

> [!Note]
> 자세한 내용은 [Azure Active Directory를 사용 하 여 blob 및 큐에 대 한 액세스 권한 부여](https://docs.microsoft.com/azure/storage/common/storage-auth-aad) 의 단계를 참조 하세요.

### <a name="account-key"></a>계정 키

선택 된 인증 방법이 **계정 키** 인 경우 액세스 키를 가져와서 key vault에 저장 해야 합니다.

1. 저장소 계정으로 이동 합니다.
1. **설정 > 액세스 키** 선택
1. *키* 를 복사 하 고 다음 단계를 위해 어딘가에 저장 합니다.
1. 키 자격 증명 모음으로 이동
1. **설정 > 비밀** 선택
1. **+ 생성/가져오기** 를 선택 하 고 저장소 계정의 *키* 로 **이름과** **값** 을 입력 합니다.
1. **만들기** 를 선택 하 여 완료 합니다.
1. 키 자격 증명 모음이 부서의 범위에 아직 연결 되지 않은 경우 [새 키 자격 증명 모음 연결을 만들어야](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account) 합니다.
1. 마지막으로, 키를 사용 하 여 [새 자격 증명을 만들어](manage-credentials.md#create-a-new-credential) 검색을 설정 합니다.

### <a name="service-principal"></a>서비스 사용자

서비스 주체를 사용 하려면 기존 항목을 사용 하거나 새 서비스 주체를 만들 수 있습니다. 

> [!Note]
> 새 서비스 주체를 만들어야 하는 경우 다음 단계를 수행 하세요.
> 1. [Azure Portal](https://portal.azure.com)로 이동합니다.
> 1. 왼쪽 메뉴에서 **Azure Active Directory** 를 선택 합니다.
> 1. **앱 등록** 을 선택합니다.
> 1. **+ 새 애플리케이션 등록** 을 선택합니다.
> 1. **응용 프로그램** 의 이름 (서비스 사용자 이름)을 입력 합니다.
> 1. **이 조직 디렉터리 에서만 계정을** 선택 하십시오.
> 1. URI 리디렉션에 대해 **웹** 을 선택 하 고 원하는 URL을 입력 합니다. 실제 또는 작업 일 필요는 없습니다.
> 1. 그런 다음, **등록** 을 선택합니다.

서비스 주체의 응용 프로그램 ID 및 암호를 가져오는 데 필요 합니다.

1. [Azure Portal](https://portal.azure.com) 에서 서비스 사용자로 이동 합니다.
1. **인증서 & 암호** 의 **개요** 및 **클라이언트 암호** 에서 **응용 프로그램 (클라이언트) ID** 의 값을 복사 합니다.
1. 키 자격 증명 모음으로 이동
1. **설정 > 비밀** 선택
1. **+ 생성/가져오기** 를 선택 하 고 서비스 사용자의 **클라이언트 암호로** 선택 및 **값** 의 **이름을** 입력 합니다.
1. **만들기** 를 선택 하 여 완료 합니다.
1. 키 자격 증명 모음이 부서의 범위에 아직 연결 되지 않은 경우 [새 키 자격 증명 모음 연결을 만들어야](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account) 합니다.
1. 마지막으로, 서비스 주체를 사용 하 여 [새 자격 증명을 만들어](manage-credentials.md#create-a-new-credential) 검색을 설정 합니다.

#### <a name="granting-the-service-principal-access-to-your-blob-storage"></a>Blob 저장소에 대 한 서비스 사용자 액세스 권한 부여

1. 스토리지 계정으로 이동합니다.
1. 왼쪽 탐색 메뉴에서 **Access Control (IAM)** 을 선택 합니다. 
1. **+ 추가** 를 선택합니다.
1. **역할** 을 **저장소 Blob 데이터 판독기** 로 설정 하 고 입력 상자 **선택** 상자에 서비스 사용자 이름 또는 개체 ID를 입력 합니다. 그런 다음, **저장** 을 선택 하 여 서비스 사용자에 게이 역할 할당을 제공 합니다.

## <a name="firewall-settings"></a>방화벽 설정

> [!NOTE]
> 저장소 계정에 대해 방화벽을 사용 하도록 설정한 경우에는 검색을 설정할 때 **관리 id** 인증 방법을 사용 해야 합니다.

1. 에서 저장소 계정으로 이동 [Azure Portal](https://portal.azure.com)
1. **설정 > 네트워킹** 으로 이동 합니다.
1. 다음 **에서 액세스 허용에서** **선택한 네트워크** 를 선택 합니다.
1. **방화벽** 섹션에서 **신뢰할 수 있는 Microsoft 서비스에서이 저장소 계정에 액세스 하도록 허용** 을 선택 하 고 **저장** 을 누릅니다.

:::image type="content" source="./media/register-scan-azure-blob-storage-source/firewall-setting.png" alt-text="방화벽 설정을 보여 주는 스크린샷":::

## <a name="register-an-azure-blob-storage-account"></a>Azure Blob Storage 계정 등록

데이터 카탈로그에 새 blob 계정을 등록 하려면 다음을 수행 합니다.

1. 부서의 범위 계정으로 이동 합니다.
1. 왼쪽 탐색 영역에서 **원본** 선택
1. **등록** 을 선택합니다.
1. **소스 등록** 에서 **Azure Blob Storage** 를 선택 합니다.
1. **계속** 을 선택합니다.

**소스 등록 (Azure Blob Storage)** 화면에서 다음을 수행 합니다.

1. 카탈로그에서 데이터 원본이 나열 될 **이름을** 입력 합니다. 
1. 저장소 계정을 필터링 할 구독을 선택 합니다.
1. 스토리지 계정 선택
1. 컬렉션을 선택 하거나 새 컬렉션을 만듭니다 (선택 사항).
1. 데이터 원본 등록을 **완료** 합니다.

:::image type="content" source="media/register-scan-azure-blob-storage-source/register-sources.png" alt-text="소스 등록 옵션" border="true":::

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>다음 단계

- [Azure 부서의 범위 Data catalog 찾아보기](how-to-browse-catalog.md)
- [Azure 부서의 범위 Data Catalog 검색](how-to-search-catalog.md)
