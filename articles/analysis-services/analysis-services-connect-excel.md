---
title: "Excel을 사용하여 Azure Analysis Services에 연결 | Microsoft Docs"
description: "Excel을 사용하여 Azure Analysis Services 서버에 연결하는 방법에 대해 알아봅니다."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 05/09/2017
ms.author: owend
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 88d0c2495a13ccf02f7d0e56ce7158ac80dcc2d5
ms.contentlocale: ko-kr
ms.lasthandoff: 05/10/2017


---
# <a name="connect-with-excel"></a>Excel로 연결

Azure에서 서버를 만들고 테이블 형식 모델을 배포하면 데이터를 연결하고 탐색할 준비가 되었습니다. 


## <a name="connect-in-excel"></a>Excel에서 연결

Excel 2016에서 데이터 가져오기 또는 이전 버전의 파워 쿼리를 사용하여 Excel에서 서버에 연결하도록 지원합니다. 파워 피벗에서 테이블 가져오기 마법사를 사용하여 연결하는 작업은 지원되지 않습니다. 

**Excel 2016에서 연결하려면**

1. Excel 2016의 **데이터** 리본 메뉴에서 **외부 데이터 가져오기** > **다른 원본에서** > **Analysis Services에서**를 클릭합니다.

2. 데이터 연결 마법사에서 **서버 이름**에 프로토콜 및 URI를 포함하여 서버 이름을 입력합니다. 그런 다음 **로그온 자격 증명**에서 **다음 사용자 이름 및 암호 사용**을 선택하고 조직 사용자 이름(예: nancy@adventureworks.com 및 암호)을 입력합니다.

    ![Excel 로그온에서 연결](./media/analysis-services-connect-excel/aas-connect-excel-logon.png)

3. **데이터베이스 및 테이블 선택**에서 데이터베이스, 모델 또는 큐브 뷰를 선택한 다음 **마침**을 클릭합니다.
   
    ![Excel 선택 모델에서 연결](./media/analysis-services-connect-excel/aas-connect-excel-select.png)


## <a name="see-also"></a>참고 항목
[클라이언트 라이브러리](analysis-services-data-providers.md)   
[서버 관리](analysis-services-manage.md)     



