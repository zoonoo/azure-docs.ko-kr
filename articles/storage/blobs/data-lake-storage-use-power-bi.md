---
title: Power BI를 사용 하 여 Azure Data Lake Storage Gen2 데이터 분석 Microsoft Docs
description: Power BI를 사용 하 여 Azure Data Lake Storage Gen2에 저장 된 데이터 분석
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 07/18/2019
ms.author: normesta
ms.openlocfilehash: ea8ceddbcbc26b2dfad5ac0618b30cb8bdc052e7
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68336466"
---
# <a name="analyze-data-in-azure-data-lake-storage-gen2-by-using-power-bi"></a>Power BI를 사용 하 여 Azure Data Lake Storage Gen2 데이터 분석

이 문서에서는 Power BI Desktop를 사용 하 여 계층적 네임 스페이스 (Azure Data Lake Storage Gen2)가 있는 저장소 계정에 저장 된 데이터를 분석 하 고 시각화 하는 방법을 알아봅니다.

## <a name="prerequisites"></a>필수 구성 요소

이 자습서를 시작하기 전에 다음이 있어야 합니다.

> [!div class="checklist"]
> * Azure 구독. [Azure 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.
> * 계층적 네임 스페이스를 포함 하는 저장소 계정입니다. [다음](data-lake-storage-quickstart-create-account.md) 지침에 따라 새로 만듭니다.
> 이 문서에서는 라는 `myadlsg2`계정을 만들었다고 가정 합니다.
> * 저장소 계정에 있는 라는 `Drivers.txt` 샘플 데이터 파일
> [Azure Data Lake Git 리포지토리에서](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceDataDrivers.txt)이 샘플을 다운로드 한 다음 해당 파일을 저장소 계정에 업로드할 수 있습니다.
> * **Power BI Desktop**. [Microsoft 다운로드 센터](https://www.microsoft.com/download/details.aspx?id=45331)에서 다운로드할 수 있습니다. 

## <a name="create-a-report-in-power-bi-desktop"></a>Power BI Desktop에서 보고서 만들기

1. 컴퓨터에서 Power BI Desktop을 시작합니다.
2. 리본 메뉴의 **홈** 탭에서 **데이터 가져오기**를 클릭 한 다음 **자세히**를 클릭 합니다.
3. **데이터 가져오기** 대화 상자에서 **Azure**를 클릭 하 고 **Azure Data Lake Store Gen2 (베타)** 를 클릭 한 다음 **연결**을 클릭 합니다.

    ![데이터 가져오기 페이지](media/data-lake-storage-use-power-bi/get-data-page.png)

4. **Azure Data Lake Storage Gen2** 대화 상자에서 파일 시스템 끝점 형식을 사용 하 여 Azure Data Lake Storage Gen2 계정, 파일 시스템 또는 하위 폴더에 대 한 URL을 제공할 수 있습니다. Data Lake Storage Gen2에 대 한 url에는 `https://<accountname>.dfs.core.windows.net/<filesystemname>/<subfolder>` 다음 패턴이 있으며 **확인**을 클릭 합니다.

    ![URL](media/data-lake-storage-use-power-bi/adls-url.png)

5. 다음 대화 상자에서 **로그인** 을 클릭 하 여 저장소 계정에 로그인 합니다. 조직의 로그인 페이지로 리디렉션됩니다. 프롬프트에 따라 계정에 로그인합니다.

    ![로그인 페이지](media/data-lake-storage-use-power-bi/sign-in.png)

6. 성공적으로 로그인 한 후에 **연결**을 클릭 합니다.

    ![로그인 페이지](media/data-lake-storage-use-power-bi/signed-in.png)

7. 다음 대화 상자에는 저장소 계정에 업로드 한 파일을 포함 하 여 위의 4 단계에서 제공한 URL 아래의 모든 파일이 표시 됩니다. 정보를 확인 하 고 **로드**를 클릭 합니다.

    ![파일 시스템](media/data-lake-storage-use-power-bi/file-systems.png)

8. 데이터가 성공적으로 Power BI 로드 된 후 **필드** 탭에 다음 필드가 표시 됩니다.

    ![필드 탭](media/data-lake-storage-use-power-bi/fields.png)

    그러나 데이터를 시각화 하 고 분석 하려면 다음 필드에 따라 데이터를 사용 하는 것이 좋습니다.

    ![필드](media/data-lake-storage-use-power-bi/preferred-fields.png)

    다음 단계에서는 가져온 데이터를 원하는 형식으로 변환하기 위해 쿼리를 업데이트합니다.

9. 리본 메뉴의 **홈** 탭에서 **쿼리 편집**을 클릭 합니다.

    ![쿼리](media/data-lake-storage-use-power-bi/queries.png)

10. **쿼리 편집기**의 **콘텐츠** 열 아래에서 **이진**을 클릭 합니다. 파일은 자동으로 CSV로 검색 되며 아래와 같이 출력이 표시 됩니다. 이제 시각화를 만드는 데 사용할 수 있는 형식으로 데이터가 준비되었습니다.

    ![출력](media/data-lake-storage-use-power-bi/binary.png)

11. 리본 메뉴의 **홈** 탭에서 **닫기** 및 **적용**을 클릭 한 다음 **닫기** 및 **적용**을 클릭 합니다.

    ![닫기 및 적용](media/data-lake-storage-use-power-bi/close-apply.png)

12. 쿼리가 업데이트되면, 시각화에 사용할 수 있는 새로운 필드가 **Fields** (필드) 탭에 표시됩니다.

    ![새 필드](media/data-lake-storage-use-power-bi/new-fields.png)

13. 특정 국가에 대한 각 도시의 드라이버를 나타내는 원형 차트를 만들겠습니다. 이렇게 하려면, 다음을 선택합니다.

    **시각화** 탭에서 원형 차트에 대 한 기호를 클릭 합니다.

    ![시각화](media/data-lake-storage-use-power-bi/visualizations.png)

    사용할 열은 열 4 (도시 이름) 및 열 7 (국가 이름)입니다. 아래와 같이 열을 **필드** 탭에서 **시각화** 탭으로 끌어갑니다.

    ![필드 끌기](media/data-lake-storage-use-power-bi/visualizations-drag-fields.png)

    이제 원형 차트가 아래와 비슷하게 표시되는 것을 볼 수 있습니다.

    ![원형 차트](media/data-lake-storage-use-power-bi/pie-chart.png)

14. 페이지 수준 필터에서 특정 국가를 선택하면, 선택한 국가의 각 도시 내 드라이버의 수를 볼 수 있습니다. 예를 들어 **시각화** 탭의 **페이지 수준 필터**에서 **브라질**을 선택합니다.

    ![페이지 필터](media/data-lake-storage-use-power-bi/page-filters.png)

15. 원형 차트가 브라질의 도시 내 드라이버를 나타내도록 자동으로 업데이트됩니다.

    ![브라질](media/data-lake-storage-use-power-bi/pie-chart-updated.png)

16. **파일** 메뉴에서 **저장**을 클릭하여 시각화를 Power BI Desktop 파일로 저장합니다.

## <a name="publish-report-to-power-bi-service"></a>Power BI 서비스에 보고서 게시

Power BI Desktop에서 시각화를 만든 후에는 Power BI 서비스에 게시 하 여 다른 사용자와 공유할 수 있습니다. 수행하는 방법에 대한 지침은 [Power BI Desktop에서 게시](https://powerbi.microsoft.com/documentation/powerbi-desktop-upload-desktop-files/)를 참조하세요.