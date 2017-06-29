---
title: "Azure Analysis Services란? | Microsoft Docs"
description: "Azure에서 Analysis Services의 큰 그림을 가져옵니다."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 83d7a29c-57ae-4aa0-8327-72dd8f00247d
ms.service: analysis-services
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 05/26/2017
ms.author: owend
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: 34726377836d00d484ca01edb098f6c7cbfa9dbf
ms.contentlocale: ko-kr
ms.lasthandoff: 06/17/2017


---
# <a name="what-is-azure-analysis-services"></a>Azure Analysis Services란?
![Azure Analysis Services](./media/analysis-services-overview/aas-overview-aas-icon.png)

검증된 Microsoft SQL Server Analysis Services 분석 엔진을 기반으로 구축된 Azure Analysis Services는 클라우드에서 엔터프라이즈급 데이터 모델링을 제공합니다. 

이 비디오를 통해 Azure Analysis Services에서 전반적인 Microsoft BI 기능과 연동하는 방법과 데이터 모델을 클라우드로 가져올 때 얻을 수 있는 많은 이점에 대해 알아보세요.


>[!VIDEO https://channel9.msdn.com/series/Azure-Analysis-Services/Azure-Analysis-Services-overview/player]
>
>


## <a name="built-on-sql-server-analysis-services"></a>SQL Server Analysis Services에 구축
Azure Analysis Services는 이미 알고 있는 것과 동일한 SQL Server Analysis Services Enterprise Edition과 호환되며, 1200 이상 호환성 수준의 테이블 형식 모델을 지원합니다. DirectQuery, 파티션, 행 수준 보안, 양방향 관계 및 변환을 모두 지원합니다.

## <a name="use-the-tools-you-already-know"></a>이미 알고 있는 도구 사용
![BI 개발자 도구](./media/analysis-services-overview/aas-overview-dev-tools.png)

Azure Analysis Services의 데이터 모델을 만들 때 SQL Server Analysis Services에서 사용한 것과 같은 도구를 사용 합니다. [SSDT(SQL Server Data Tools)](https://msdn.microsoft.com/library/mt204009.aspx)를 사용하여 모델을 작성하고 배포합니다. [SSMS(SQL Server Management Studio)](https://msdn.microsoft.com/library/mt238290.aspx)를 사용하여 서버 및 모델 데이터베이스를 관리합니다. [PowerShell](analysis-services-powershell.md) 및 [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) 템플릿을 사용하여 작업 자동화 

## <a name="supports-the-latest-features"></a>최신 기능 지원
1200 및 1400 미리 보기 호환성 수준의 테이블 형식 모델을 지원합니다.

**테이블 형식1200** - SQL Server 2016 Analysis Services에 처음으로 포함된 1200은 TOM(테이블 형식 개체 모델)을 도입하여 테이블, 열 및 관계와 같은 모델 개체를 설명했습니다. 테이블 형식 개체 모델은 JSON에서 [Microsoft.AnalysisServices.Tabular](https://msdn.microsoft.com/library/microsoft.analysisservices.tabular.aspx) 네임스페이스를 통해 [TMSL(테이블 형식 모델 스크립팅 언어)](https://docs.microsoft.com/sql/analysis-services/tabular-model-scripting-language-tmsl-reference) 및 AMO 데이터 정의 언어로 노출됩니다.

**테이블 형식1400 미리 보기** - 세부 정보 행, 개체 수준 보안, 비정형 계층 지원, 데이터 연결을 위한 최신 데이터 가져오기 환경 및 다른 많은 향상된 기능에 대한 지원을 소개합니다. 모든 최신 기능을 활용하려면 최신 [SSDT(SQL Server Data Tools)](https://msdn.microsoft.com/library/mt204009.aspx)를 사용해야 합니다. 테이블 형식 1400은 아직 미리 보기이므로 관련 기능이 빠르게 변하고 있습니다. 최신 정보를 얻기 위해 [블로그 게시물](https://azure.microsoft.com/blog/1400-models-in-azure-as/)을 확인해 주세요.

## <a name="data-sources"></a>데이터 원본
Azure에서 서버에 배포한 데이터 모델은 조직이나 클라우드의 온-프레미스 데이터 원본에 대한 연결을 지원합니다. 하이브리드 BI 솔루션을 위한 온-프레미스 및 클라우드 데이터 원본의 데이터를 모두 결합합니다.

서버가 클라우드에 있기 때문에 클라우드 데이터 원본에 원활하게 연결됩니다. 온-프레미스 데이터 원본에 연결할 때 [온-프레미스 데이터 게이트웨이](analysis-services-gateway.md)를 사용하면 클라우드에서 서버를 통해 신속하고 안전하게 연결할 수 있습니다. 지원되는 온-프레미스 데이터 원본에 대한 자세한 내용을 보려면 [Azure Analysis Services에서 지원되는 데이터 원본](analysis-services-datasource.md)을 참조하세요.


## <a name="explore-your-data-from-anywhere"></a>어디에서나 데이터 탐색
어디에서나 서버에서 연결하여 데이터를 가져옵니다. Azure Analysis Services는 Power BI Desktop, Excel, 사용자 지정 응용 프로그램 및 브라우저 기반 도구에 대한 연결을 지원합니다.

![데이터 시각화](./media/analysis-services-overview/aas-overview-visualization.png)


## <a name="secure"></a>보안
#### <a name="user-authentication"></a>사용자 인증
Azure Analysis Services에 대한 사용자 인증은 [AAD(Azure Active Directory)](../active-directory/active-directory-whatis.md)에서 처리됩니다. Azure Analysis Services 데이터베이스에 로그인하려고 시도할 때 사용자는 액세스하려는 데이터베이스에 대한 액세스 권한이 있는 조직의 계정 ID를 사용합니다. 이러한 사용자 ID는 구독에서 Azure Analysis Services 서버가 있는 기본 Azure Active Directory의 구성원이어야 합니다. AAD와 온-프레미스 Active Directory 간의 [디렉터리 통합](https://technet.microsoft.com/library/jj573653.aspx)은 사용자가 Azure Analysis Services 데이터베이스에 액세스할 수 있는 매우 좋은 방법이지만 모든 시나리오에서 반드시 필요하지는 않습니다.

사용자는 사용자 계정과 암호의 UPN(사용자 계정 이름)으로 로그인합니다. 온-프레미스 Active Directory와 동기화하는 경우 사용자의 UPN은 종종 조직의 전자 메일 주소입니다.

Analysis Services 서버 리소스를 관리할 수 있는 권한은 Azure 구독 내에서 사용자를 역할에 할당함으로써 처리됩니다. Azure에서 구독 관리자는 기본적으로 서버 리소스에 대해 소유자 권한을 가집니다. Azure Resource Manager를 사용하면 사용자를 추가할 수 있습니다.

#### <a name="data-security"></a>데이터 보안
Azure Analysis Services에서는 Azure Blob 저장소를 활용하여 Analysis Services 데이터베이스의 저장소와 메타데이터를 유지합니다. Blob에 있는 데이터 파일은 Azure Blob SSE(Server Side Encryption)를 통해 암호화됩니다. 직접 쿼리 모드를 사용하는 경우 메타데이터만 저장됩니다. 실제 데이터는 쿼리 시 데이터 원본에서 액세스됩니다.

#### <a name="on-premises-data-sources"></a>온-프레미스 데이터 원본
[온-프레미스 데이터 게이트웨이](analysis-services-gateway.md)를 설치하고 구성하면 조직의 온-프레미스에 상주하는 데이터에 대한 보안 액세스를 구현할 수 있습니다. 게이트웨이는 직접 쿼리 모드와 메모리 내 모드 둘 다의 데이터 액세스를 제공합니다. Azure Analysis Services 모델이 온-프레미스 데이터 원본에 연결되는 경우 쿼리는 온-프레미스 데이터 원본에 대해 암호화된 자격 증명과 함께 만들어집니다. 게이트웨이 클라우드 서비스에서 쿼리를 분석하고 Azure Service Bus에 대한 요청을 보냅니다. 온-프레미스 데이터 게이트웨이에서는 보류 중인 요청을 위해 Azure Service Bus를 폴링합니다. 그러면 게이트웨이에서 쿼리를 가져오고, 자격 증명의 암호를 해독한 다음 쿼리를 실행할 데이터 원본에 연결합니다. 끝으로 그 결과가 데이터 원본에서 게이트웨이로 다시 전송된 후에 Azure Analysis Services 데이터베이스로 전송됩니다.

Azure Analysis Services는 [Microsoft Online Services 약관](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) 및 [Microsoft Online Services 개인 정보 취급 방침](https://www.microsoft.com/privacystatement/OnlineServices/Default.aspx)에 준하여 사용할 수 있습니다.
Azure 보안에 대한 자세한 내용은 [Microsoft 보안 센터](https://www.microsoft.com/trustcenter/Security/AzureSecurity)를 참조하세요.

## <a name="get-help"></a>도움말 보기

### <a name="documentation"></a>설명서
Azure Analysis Services에서는 간단하게 설정하고 관리할 수 있습니다. 서버를 만들고 관리하는 데 필요한 모든 정보는 여기서 찾을 수 있습니다. 서버에 배포할 데이터 모델을 만드는 것은 온-프레미스 서버에 배포할 데이터 모델을 만드는 것과 매우 비슷합니다. 개념, 절차, 자습서 및 참조 문서에 대한 광범위한 자료는 [Analysis Services](https://docs.microsoft.com/sql/analysis-services/analysis-services)에 있습니다.

### <a name="videos"></a>비디오
[Channel 9의 Azure Analysis Services](https://channel9.msdn.com/series/Azure-Analysis-Services)에서 유용한 동영상을 확인하세요.

### <a name="blogs"></a>블로그
상황이 빠르게 변화하고 있습니다. 최신 정보는 언제든지 [Analysis Services 팀 블로그](https://blogs.msdn.microsoft.com/analysisservices/) 및 [Azure 블로그](https://azure.microsoft.com/blog/)에서 얻을 수 있습니다.

### <a name="community"></a>커뮤니티
Analysis Services에는 활발한 사용자 커뮤니티가 있습니다. [Azure Analysis Services 포럼](https://aka.ms/azureanalysisservicesforum)에서 주최하는 대화에 참여해 보세요.

## <a name="feedback"></a>사용자 의견
제안할 사항이나 요청하려는 기능이 있습니까? 여러분의 의견을 [Azure Analysis Services 피드백](https://aka.ms/azureanalysisservicesfeedback)에 남겨주세요.

문서에 대해 제안할 사항이 있습니까? 각 문서의 아래쪽에 있는 Livefyre를 사용하여 주석을 추가할 수 있습니다.

## <a name="next-steps"></a>다음 단계
Azure Analysis Services에 대해 자세히 이해했으므로 이제는 이 서비스를 시작할 단계입니다. Azure에서 [서버를 만들고](analysis-services-create-server.md) 이 서버에 [테이블 형식 모델을 배포하는](analysis-services-deploy.md) 방법에 대해 알아봅니다.

