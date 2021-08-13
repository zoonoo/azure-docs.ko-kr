---
title: Azure 파일을 검색하는 방법
description: 이 방법 가이드에서는 Azure 파일을 검사하는 방법에 대해 자세히 설명합니다.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 05/08/2021
ms.openlocfilehash: c88134e978615d53bdfbde26492096212c3f582f
ms.sourcegitcommit: 3de22db010c5efa9e11cffd44a3715723c36696a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2021
ms.locfileid: "109656413"
---
# <a name="register-and-scan-azure-files"></a>Azure Files 등록 및 검사

## <a name="supported-capabilities"></a>지원되는 기능

Azure Files는 시스템 및 고객 분류에 따라 메타데이터를 캡처하고 메타데이터에 분류를 적용하기 위한 전체 및 증분 검사를 지원합니다.

## <a name="prerequisites"></a>필수 구성 요소

- 데이터 원본을 등록하기 전에 Azure Purview 계정을 만듭니다. Purview 계정을 만드는 방법에 관한 자세한 내용은 [빠른 시작: Azure Purview 계정 만들기](create-catalog-portal.md)를 참조하세요.
- 검사를 설정하고 예약하려면 데이터 원본 관리자여야 합니다. 자세한 내용은 [카탈로그 권한](catalog-permissions.md)을 참조하세요.

## <a name="register-an-azure-files-storage-account"></a>Azure Files 스토리지 계정 등록

새 Azure Files 계정을 데이터 카탈로그에 등록하려면 다음을 수행합니다.

1. Purview Data Catalog로 이동합니다.
1. 왼쪽 탐색에서 **관리 센터** 를 선택합니다.
1. **원본 및 검사** 에서 **데이터 원본** 을 선택합니다.
1. **+새로 만들기** 를 선택합니다.
1. **원본 등록** 에서 **Azure Files** 를 선택합니다. **계속** 을 선택합니다.

:::image type="content" source="media/register-scan-azure-files/register-new-data-source.png" alt-text="새 데이터 원본 등록" border="true":::

**원본 등록(Azure Files)** 화면에서 다음을 수행합니다.

1. 카탈로그에서 나열되는 데이터 원본의 **이름** 을 입력합니다.
2. Azure Storage 계정을 필터링하려면 Azure 구독을 선택합니다.
3. Azure Storage 계정을 선택합니다.
4. 컬렉션을 선택하거나 새로 만듭니다(선택 사항).
5. **등록** 을 선택하여 데이터 원본을 등록합니다.

:::image type="content" source="media/register-scan-azure-files/register-sources.png" alt-text="원본 등록 옵션" border="true":::

## <a name="set-up-authentication-for-a-scan"></a>검사 인증 설정

계정 키를 사용하여 Azure Files Storage에 대한 인증을 설정하려면 다음을 수행합니다.

1. 인증 방법을 **계정 키** 로 선택합니다.
2. **Azure 구독에서** 옵션을 선택합니다.
3. Azure Files 계정이 있는 Azure 구독을 선택합니다.
4. 목록에서 스토리지 계정 이름을 선택합니다.
5. **Finish** 를 클릭합니다.

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>다음 단계

- [Azure Purview 데이터 카탈로그 찾아보기](how-to-browse-catalog.md)
- [Azure Purview Data Catalog 검색](how-to-search-catalog.md)