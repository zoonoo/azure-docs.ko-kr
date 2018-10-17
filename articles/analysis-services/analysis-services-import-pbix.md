---
title: Azure Analysis Services로 Power BI Desktop 파일 가져오기 | Microsoft Docs
description: Azure Portal을 사용하여 Power BI Desktop 파일(pbix)을 가져오는 방법을 설명합니다.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 09/27/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: e164488a1bf176d5b6c0e28a84cd1ec22cae4cce
ms.sourcegitcommit: 42405ab963df3101ee2a9b26e54240ffa689f140
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2018
ms.locfileid: "47423581"
---
# <a name="import-a-power-bi-desktop-file"></a>Power BI Desktop 파일 가져오기

Azure Analysis Services로 Power BI Desktop 파일(pbix)의 데이터 모델을 가져올 수 있습니다. 모델 메타데이터, 캐시된 데이터 및 데이터 원본 연결이 가져오기됩니다. 보고서 및 시각화는 가져오기되지 않습니다. Power BI Desktop에서 가져온 데이터 모델은 1400 호환성 수준만 적용됩니다.

**제한 사항**   

- pbix 파일에서 가져오기는 현재 **미리 보기**로 제공되는 포털의 웹 디자이너 기능을 사용합니다. 기능이 제한됩니다. 고급 모델을 개발하고 테스트하려면 Visual Studio(SSDT) 및 SSMS(SQL Server Management Studio)를 사용하는 것이 가장 좋습니다.
- Power BI Desktop 2018년 7월 업데이트(2.60.5169.3201) 이후 버전에서 데이터 모델을 만드는 경우 사용하도록 설정된 미리 보기 기능이 없는지 확인합니다. 미리 보기 기능은 Azure Analysis Services에서 아직 지원되지 않습니다.  
가져오기를 진행할 때 다음 오류가 표시되는 경우에는 Azure Analysis Services에서 아직 지원되지 않는 미리 보기 기능이 pbix 파일에서 사용하도록 설정되어 있는 것입니다.

    ![호환성 수준 경고](./media/analysis-services-import-pbix/aas-import-pbix-cl-warning.png)   
- pbix 파일에서 가져오려면 서버 관리자 권한이 있어야 합니다.
- pbix 모델은 **Azure SQL Database** 및 **Azure SQL Data Warehouse** 데이터 원본에만 연결할 수 있습니다.
- pbix 모델에는 라이브 연결이나 DirectQuery 연결이 불가능합니다. 


## <a name="to-import-from-pbix"></a>pbix를 가져오려면

1. 서버의 **개요** > **웹 디자이너**에서 **열기**를 클릭합니다.

    ![Azure Portal에서 모델 만들기](./media/analysis-services-create-model-portal/aas-create-portal-overview-wd.png)

2. **웹 디자이너** > **모델**에서 **+ 추가**를 클릭합니다.

    ![Azure Portal에서 모델 만들기](./media/analysis-services-create-model-portal/aas-create-portal-models.png)

3. **새 모델**에서 모델 이름을 입력한 다음, Power BI Desktop 파일을 선택합니다.

    ![Azure Portal의 새 모델 대화 상자](./media/analysis-services-import-pbix/aas-import-pbix-new-model.png)

4. **가져오기**에서 파일을 찾아 선택합니다.

     ![Azure Portal의 연결 대화 상자](./media/analysis-services-import-pbix/aas-import-pbix-select-file.png)

## <a name="change-credentials"></a>자격 증명 변경

기본적으로 pbix 파일에서 데이터 모델을 가져올 때 데이터 원본에 연결하는 데 사용되는 자격 증명이 ServiceAccount으로 설정됩니다. pbix에서 모델을 가져온 후에는 다음 방법을 사용하여 자격 증명을 변경할 수 있습니다.

- SSMS 2018년 7월(버전 17.8.1) 이상 버전을 사용하여 자격 증명을 편집합니다. 가장 쉬운 방법입니다.
- [DataSources 개체](https://docs.microsoft.com/sql/analysis-services/tabular-models-scripting-language-objects/datasources-object-tmsl)에 대해 TMSL [Alter 명령](https://docs.microsoft.com/sql/analysis-services/tabular-models-scripting-language-commands/alter-command-tmsl)을 사용하여 연결 문자열 속성을 수정합니다. 
- Visual Studio에서 모델을 열고, 데이터 원본 연결에 대한 자격 증명을 편집하고, 모델을 다시 배포합니다.

SSMS를 사용하여 자격 증명을 변경하려면 다음과 같이 합니다. 

1. SSMS에서 데이터베이스 > **연결**을 확장합니다. 
2. 데이터베이스 연결을 마우스 오른쪽 단추로 클릭하고 **자격 증명 새로 고침**을 클릭합니다. 

    ![자격 증명 새로 고침](./media/analysis-services-import-pbix/aas-import-pbix-creds.png)

3. 자격 증명 대화 상자에서 자격 증명 유형을 선택하고 자격 증명을 입력합니다. SQL 인증인 경우 데이터베이스를 선택합니다. 조직 계정(OAuth)인 경우 Microsoft 계정을 선택합니다.
    ![자격 증명 편집](./media/analysis-services-import-pbix/aas-import-pbix-edit-creds.png)

Power BI Desktop의 2018년 7월 버전에는 데이터 원본 권한을 변경하는 새 기능이 포함되어 있습니다. **홈** 탭에서 **쿼리 편집**  > **데이터 원본 설정**을 클릭합니다. 데이터 원본 연결을 선택한 다음, **권한 편집**을 클릭합니다.


## <a name="see-also"></a>참고 항목

[Azure Portal에서 모델 만들기](analysis-services-create-model-portal.md)   
[Azure Analysis Services에 연결](analysis-services-connect.md)  
