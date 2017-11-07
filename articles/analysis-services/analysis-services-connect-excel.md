---
title: "Excel을 사용하여 Azure Analysis Services에 연결 | Microsoft Docs"
description: "Excel을 사용하여 Azure Analysis Services 서버에 연결하는 방법에 대해 알아봅니다."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 11/01/2017
ms.author: owend
ms.openlocfilehash: 7597792a525583497ec6a400e668eda2adc9a93e
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2017
---
# <a name="connect-with-excel"></a>Excel로 연결

Azure에서 서버를 만들고 테이블 형식 모델을 배포하면 데이터를 연결하고 탐색할 준비가 되었습니다.


## <a name="connect-in-excel"></a>Excel에서 연결

Excel 2016에서 Get Data를 사용하여 Excel에서 서버에 연결합니다. 파워 피벗에서 테이블 가져오기 마법사를 사용하여 연결하는 작업은 지원되지 않습니다. 

**Excel 2016에서 연결하려면**

1. Excel 2016의 **데이터** 리본 메뉴에서 **외부 데이터 가져오기** > **다른 원본에서** > **Analysis Services에서**를 클릭합니다.

2. 데이터 연결 마법사에서 **서버 이름**에 프로토콜 및 URI를 포함하여 서버 이름을 입력합니다. 그런 다음 **로그온 자격 증명**에서 **다음 사용자 이름 및 암호 사용**을 선택하고 조직 사용자 이름(예: nancy@adventureworks.com 및 암호)을 입력합니다.

    ![Excel 로그온에서 연결](./media/analysis-services-connect-excel/aas-connect-excel-logon.png)

3. **데이터베이스 및 테이블 선택**에서 데이터베이스, 모델 또는 큐브 뷰를 선택한 다음 **마침**을 클릭합니다.
   
    ![Excel 선택 모델에서 연결](./media/analysis-services-connect-excel/aas-connect-excel-select.png)


## <a name="see-also"></a>참고 항목
[클라이언트 라이브러리](analysis-services-data-providers.md)   
[서버 관리](analysis-services-manage.md)     


