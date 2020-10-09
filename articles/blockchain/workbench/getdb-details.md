---
title: Azure Blockchain Workbench 데이터베이스 세부 정보 가져오기
description: Azure Blockchain 워크 벤치 미리 보기 데이터베이스 및 데이터베이스 서버 정보를 가져오는 방법에 대해 알아봅니다.
ms.date: 09/05/2019
ms.topic: how-to
ms.reviewer: mmercuri
ms.openlocfilehash: 898360d8e22803e17ff4da7d3b63f5595cad47ba
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85254653"
---
# <a name="get-information-about-your-azure-blockchain-workbench-database"></a>Azure Blockchain Workbench 데이터베이스에 대한 정보 가져오기

이 문서에서는 Azure Blockchain 워크 벤치 미리 보기 데이터베이스에 대 한 자세한 정보를 가져오는 방법을 보여 줍니다.

## <a name="overview"></a>개요

애플리케이션, 워크플로 및 스마트 계약 실행에 대한 정보는 Blockchain Workbench SQL DB의 데이터베이스 보기를 사용하여 제공됩니다. 개발자는 Microsoft Excel, Power BI, Visual Studio 및 SQL Server Management Studio 같은 도구를 사용할 때 이 정보를 사용할 수 있습니다.

개발자는 데이터베이스에 연결하려면 다음이 필요합니다.

* 데이터베이스 방화벽에서 허용되는 외부 클라이언트 액세스. 데이터베이스 방화벽 구성에 대한 이 문서에서는 액세스를 허용하는 방법에 대해 설명합니다.
* 데이터베이스 서버 이름 및 데이터베이스 이름.

## <a name="connect-to-the-blockchain-workbench-database"></a>Blockchain Workbench 데이터베이스에 연결

데이터베이스에 연결하려면:

1. Azure Blockchain 워크 벤치 리소스에 대 한 **소유자** 권한이 있는 계정으로 Azure Portal에 로그인 합니다.
2. 왼쪽의 탐색 창에서 **리소스 그룹**을 선택합니다.
3. Blockchain Workbench 배포에 대한 리소스 그룹의 이름을 선택합니다.
4. **형식**을 선택하여 리소스 목록을 정렬한 다음, **SQL 서버**를 선택합니다. 다음 화면 캡처의 정렬 된 목록에는 "master"와 "lhgn"를 **리소스 접두사로**사용 하는 두 개의 데이터베이스가 표시 됩니다.

   ![정렬된 Blockchain Workbench 리소스 목록](./media/getdb-details/sorted-workbench-resource-list.png)

5. Blockchain Workbench 데이터베이스에 대한 상세 정보를 보려면 Blockchain Workbench를 배포하기 위해 입력한 **리소스 접두사**를 사용하는 데이터베이스 링크를 선택합니다.

   ![데이터베이스 세부 정보](./media/getdb-details/workbench-db-details.png)

개발 또는 보고 도구를 사용하여 데이터베이스 서버 이름 및 데이터베이스 이름으로 Blockchain Workbench 데이터베이스에 연결할 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Blockchain Workbench의 데이터베이스 뷰](database-views.md)