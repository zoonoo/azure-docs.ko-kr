---
title: Cloud 파트너 포털의 Azure에 대한 가상 머신 Marketplace 탭 | Microsoft Docs
description: Azure Marketplace VM 제안을 만드는 데 사용되는 Marketplace 탭에 대해 설명합니다.
services: Azure, Marketplace, Cloud Partner Portal, virtual machine
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 10/19/2018
ms.author: pbutlerm
ms.openlocfilehash: aa43a6f1f7c757366e321e7da0fb1e47d4928cee
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2018
ms.locfileid: "49639359"
---
# <a name="virtual-machine-marketplace-tab"></a>가상 머신 Marketplace 탭

**새 제안** 페이지의 **Marketplace** 탭을 사용하면 잠재 고객에게 마케팅, 판매 및 법적 정보와 계약을 제공하고 마켓플레이스에서 창출된 잠재 고객을 관리할 수 있습니다. 이 긴 양식은 **개요**, **마케팅 아티팩트**, **잠재 고객 관리** 및 **법적 정보**의 4개 섹션으로 나뉩니다. 

## <a name="overview-section"></a>개요 섹션
이 섹션에서는 Azure Marketplace 제안에 대한 일반적인 정보를 입력합니다.  필드 이름에 별표(*)가 추가되면 필수 항목임을 나타냅니다.

![가상 머신에 대한 새 제안 양식에 있는 Marketplace 탭의 개요 섹션](./media/publishvm_008.png)

다음 표에서는 이러한 필드의 용도와 내용을 설명합니다.

|  **필드**                |     **설명**                                                          |
|  ---------                |     ---------------                                                          |
| **제목**                 | 제안의 제목이며. 종종 긴 정식 이름입니다. 이 제목은 마켓플레이스에서 눈에 띄게 표시됩니다.  최대 길이는 50자입니다. |
| **요약**               | 솔루션에 대한 간략한 목적 또는 기능입니다.  최대 길이는 100자입니다. |
| **긴 요약**          | 솔루션에 대한 목적 또는 기능입니다.  최대 길이는 256자입니다. |
| **설명**           | 솔루션에 대한 설명입니다.  최대 길이는 3,000자이며, 단순 HTML 형식을 지원합니다. |
| **마케팅 식별자**  | 이 제안과 연결할 고유 URL이며, 일반적으로 조직 및 솔루션 이름을 포함하고 최대 길이는 50자입니다.  예:  <br/> `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sampleApp`  |
| **미리 보기 구독 ID** | 1-100개의 미리 보기 구독 식별자를 추가합니다. 제안이 게시된 후 라이브 상태가 되기 전에 이러한 허용 목록에 추가된 구독에서 해당 제안에 액세스할 수 있습니다. |
| **유용한 링크**          | 제안이 가장 적합하게 연결될 수 있는 비즈니스 및 기술 범주에 대한 다중 선택입니다.  최대 5개까지 허용됩니다.  |
|  |  |


## <a name="marketing-artifacts-section"></a>마케팅 아티팩트 섹션

이 두 번째 섹션은 **로고**, **스크린샷**, **비디오**의 3개 섹션으로 나뉩니다. 로고는 유일하게 필요한 마케팅 아티팩트이지만, 모든 아티팩트가 고객에게 가장 적절하게 호소하는 데 권장됩니다.

![가상 머신에 대한 새 제안 양식에 있는 Marketplace 탭의 마케팅 아티팩트 섹션](./media/publishvm_009.png)

|  **필드**                |     **설명**                                                          |
|  ---------                |     ---------------                                                          |
| *로고*  |  |
| **소형**                 | 40x40 픽셀.ico 비트맵                                                      |
| **중형**                | 90x90 픽셀.ico 비트맵                                                      |
| **대형**                 | 115x115 픽셀.ico 비트맵                                                   |
| **넓게**                  | 255x115 픽셀.ico 비트맵                                                    |
| **대표**                  | 815x290 픽셀 비트맵  선택 사항이지만, 일단 업로드되면 대표 로고를 삭제할 수 없습니다. |
| *스크린샷*  | 선택 사항이지만, SKU당 최대 5개의 스크린샷을 제공합니다. |
| **Name**                  | 이름 또는 제목입니다. <!-- TODO - max char length? none specified in UI -->                               |
| **이미지**                 | 화면 캡처 이미지, 533x324 픽셀                                         |
| *비디오*  |  |
| **Name**                  | 이름 또는 제목입니다. <!-- TODO - max char length? -->                              |
| **링크**                  | YouTube 또는 Vimeo에서 호스팅되는 비디오 URL입니다.                                        |
| **썸네일**             | 533x324 픽셀 비트맵                                                               |
|  |  |


### <a name="logo-guidelines"></a>로고 지침

<!-- TD: It seems like this section could be better located in some common area, maybe a AMP Marketing/Design section 
+1 this should all be in a common area and referenced from here to that location.-->

Cloud 파트너 포털에 업로드되는 모든 로고는 다음 지침을 따라야 합니다.

*  Azure 디자인은 단순한 색 팔레트를 사용합니다. 로고의 기본 색상과 보조 색상 수는 적게 유지합니다.
*  Azure 포털의 테마 색은 흰색과 검은색입니다. 따라서 이러한 색을 로고의 배경색으로 사용하지 않습니다. Azure 포털에서 로고가 돋보이도록 하는 색을 사용합니다. 간단한 기본 색을 사용하는 것이 좋습니다. 투명한 배경을 사용하는 경우 로고/텍스트가 흰색, 검은색 또는 파란색이 아닌지 확인합니다.
*  로고의 배경에 그라데이션 효과를 사용하지 않습니다.
*  로고에 회사 또는 브랜드 이름이 포함된 텍스트는 배치하지 않습니다. 로고의 모양과 느낌은 "평면적"이어야 하며 그라데이션은 사용하지 않습니다.
*  로고를 늘리지 않습니다.

#### <a name="hero-logo"></a>대표 로고

대표 로고는 선택 사항이지만, 일단 업로드되면 대표 로고를 삭제할 수 없습니다.  대표 로고 아이콘은 다음 지침을 따라야 합니다.

*  대표 아이콘에는 검은색, 흰색 및 투명한 배경이 허용되지 않습니다.
*  밝은 색을 대표 아이콘의 배경으로 사용하지 않습니다.  게시자 표시 이름, 계획 제목 및 긴 제안 요약은 흰색 글꼴 색으로 표시되며 배경에서 눈에 잘 띄어야 합니다.
*  대표 로고를 디자인하는 동안 대부분의 텍스트를 사용하지 않습니다.  제안이 나열되면 대표 로고 내에 게시자 이름, 계획 제목, 긴 제안 요약 및 만들기 단추가 프로그래밍 방식으로 포함됩니다. 
* 대표 아이콘의 오른쪽에는 크기가 415x100 픽셀이고 왼쪽에서 370픽셀만큼 오프셋된 사용되지 않는 사각형이 포함됩니다.  

예를 들어 Azure Container Service의 대표 아이콘은 다음과 같습니다.  <!-- TD: It would be nice to have the raw bitmap, e.g.before and after embedding. -->

![Azure Container Service에 대한 대표 아이콘 예제](./media/publishvm_010.png)


### <a name="marketing-information-example"></a>마케팅 정보의 예 

다음 이미지에서는 Microsoft Windows Server 주요 제품 페이지에 마케팅 정보가 표시되는 방법을 보여 줍니다.

![Microsoft Windows Server에 대한 제품 페이지의 예](./media/publishvm_011.png)


## <a name="lead-management-section"></a>잠재 고객 관리 섹션
<!-- this all should be referenced in a common location for lead management, not in this file. nothing unique for a vm specifically. -->

세 번째 섹션에서는 Azure Marketplace 제안에서 창출된 잠재 고객을 수집할 수 있습니다. 이 잠재 고객 정보에 대한 드롭다운 목록에서 제공하는 저장소 옵션은 다음과 같습니다.

* **없음** - 잠재 고객 정보가 기본적으로 수집되지 않습니다.
* Azure 테이블 - 연결 문자열로 지정된 Azure 테이블에 기록됩니다.
* Dynamics CRM Online - URL 및 인증 자격 증명으로 지정된 [Microsoft Dynamics 365 Online](https://dynamics.microsoft.com/) 인스턴스에 기록됩니다.
* HTTPS 엔드포인트 - JSON 페이로드로 지정된 HTTPS 엔드포인트에 기록됩니다.
* Marketo - 서버 ID, munchkin ID 및 양식 ID로 지정된 [Marketo](https://www.marketo.com/) 인스턴스에 기록됩니다.
* Salesforce - 개체 식별자로 지정된 [Salesforce](https://www.salesforce.com/) 데이터베이스에 기록됩니다.

제안이 성공적으로 게시되면 잠재 고객 연결의 유효성을 검사하고 테스트 잠재 고객을 구성된 대상으로 자동으로 보냅니다. 잠재 고객 정보는 지속적으로 관리해야 하며, 고객 관리 아키텍처가 변경될 때마다 이러한 설정을 즉시 업데이트해야 합니다.

<!-- TD: For more info, see [Need a topic on lead information and processing that mimics the Appendix of the VM Pub Guide]. -->

## <a name="legal-section"></a>법적 정보 섹션

이 마지막 섹션에서는 각 제안에 필요한 두 가지 법적 문서, 즉 개인정보처리방침 및 사용 약관을 제공할 수 있습니다.

|  **필드**                |     **설명**                                                          |
|  ---------                |     ---------------                                                          |
| **개인정보처리방침 URL**    | 게시된 개인정보처리방침에 대한 URL입니다.                                            |
| **사용 약관**          | 일반 텍스트 또는 단순 HTML 형식으로 작성된 정책입니다.  <!-- TODO - max char length? -->       |
|  |  |

<br/>

다음 [지원](./cpp-support-tab.md) 탭에서는 제안에 대한 기술 및 사용자 지원 리소스를 제공합니다.

