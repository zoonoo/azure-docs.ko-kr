---
title: Azure Data Catalog 개발자 샘플
description: 이 문서에서는 데이터 카탈로그 REST API에 대해 사용 가능한 개발자 샘플의 개요를 제공합니다.
ms.service: data-catalog
author: JasonWHowell
ms.author: jasonh
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 15b48bc41e230ca5b9003675e2caab25741bcbfd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "104674770"
---
# <a name="azure-data-catalog-developer-samples"></a>Azure Data Catalog 개발자 샘플

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

Data Catalog REST API를 사용하여 Azure Data Catalog 앱 개발을 시작합니다. 데이터 카탈로그 REST API는 데이터 카탈로그 리소스에 대한 등록, 주석 달기 및 데이터 자산을 프로그래밍 방식으로 검색하기 위해 프로그래밍 방식으로 액세스를 제공하는 REST 기반 API입니다.

## <a name="samples-available-on-githubcom"></a>GitHub.com에서 사용할 수 있는 샘플

* [Azure Azure Data Catalog 시작](https://github.com/Azure-Samples/data-catalog-dotnet-get-started/)
  
   이 시작 샘플은 Data Catalog REST API를 사용하여 데이터 자산을 등록, 검색 및 삭제하기 위해 Azure AD로 인증하는 방법을 보여 줍니다.
   
* [서비스 주체를 사용하여 Azure Data Catalog 시작](https://github.com/Azure-Samples/data-catalog-dotnet-service-principal-get-started/)

   이 샘플에서는 Data Catalog REST API를 사용하여 데이터 자산을 등록, 검색 및 삭제하는 방법을 보여 줍니다. 이 샘플에서는 서비스 주체 인증을 사용합니다.

* [Azure Data Catalog에 대한 Import/Export 도구](https://github.com/Azure-Samples/data-catalog-dotnet-import-export/)

   이 샘플에서는 Azure Data Catalog에서 자산을 가져오고 파일로 직렬화하는 Data Catalog REST API를 사용하는 방법을 보여 줍니다. 또한 JSON으로 직렬화된 자산 집합을 가져오고 카탈로그에 게시하는 방법을 보여 줍니다. 검색 쿼리를 사용하여 카탈로그의 하위 집합 내보내기를 지원합니다.

* [Azure Data Catalog에서 대량 등록 및 주석 달기](https://github.com/Azure-Samples/data-catalog-dotnet-excel-register-data-assets/)
  
   이 샘플은 Data Catalog REST API 및 Open XML을 사용하여 Excel 통합 문서에서 데이터 자산을 대량 등록하는 방법을 보여 줍니다.
  
* [Azure Data Catalog에 용어를 대량으로 가져오기](https://github.com/Azure-Samples/data-catalog-bulk-import-glossary/)

   이 샘플에서는 CSV 파일에서 ADC 용어집으로 용어를 가져오는 방법을 보여 줍니다.

* [Azure Data Catalog로 관계를 대량으로 가져오기](https://github.com/Azure-Samples/data-catalog-bulk-import-relationship/)

   이 샘플에서는 CSV 파일에서 데이터 카탈로그로 관계 정보를 프로그래밍 방식으로 가져오는 방법을 보여 줍니다.

* [Azure Data Catalog에 관계 게시](https://github.com/Azure-Samples/data-catalog-dotnet-publish-relationships/)

   이 샘플에서는 데이터 카탈로그에 관계 정보를 프로그래밍 방식으로 게시할 수 있는 방법을 보여 줍니다.
   
## <a name="next-steps"></a>다음 단계
[Azure Data Catalog REST API 참조](/rest/api/datacatalog/)
