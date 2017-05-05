---
title: "Azure Analysis Services에 배포 | Microsoft Docs"
description: "Azure Analysis Services 서버에 테이블 형식 모델을 배포하는 방법에 대해 알아봅니다."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 5f1f0ae7-11de-4923-a3da-888b13a3638c
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 04/17/2017
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: 766120913d419ec8090a4f25d304f2d3cf9b6693
ms.lasthandoff: 04/18/2017


---
# <a name="deploy-to-azure-analysis-services"></a>Azure Analysis Services에 배포
Azure 구독에서 서버를 만들면 여기에 테이블 형식 모델 데이터베이스를 배포할 준비가 되었습니다. SSDT(SQL Server 데이터 도구)를 사용하여 작업하는 테이블 형식 모델 프로젝트를 빌드하고 배포할 수 있습니다. 또는 SSMS(SQL Server Management Studio)를 사용하여 Analysis Services 인스턴스에서 기존 테이블 형식 모델 데이터베이스를 배포할 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에
시작하려면 다음이 필요합니다.

* Azure의 **Analysis Services 서버** 자세한 내용은 [Azure에서 Analysis Services 만들기](analysis-services-create-server.md)를 참조하세요.
* SSDT의 **테이블 형식 모델 프로젝트** 또는 Analysis Services 인스턴스에 대한 1200 호환성 수준의 기존 테이블 형식 모델 만들어 본 적이 없나요? [Adventure Works 자습서](https://msdn.microsoft.com/library/hh231691.aspx)를 사용해 봅니다.
* **온-프레미스 게이트웨이** - 하나 이상의 데이터 원본이 조직 네트워크의 온-프레미스에 있는 경우 [온-프레미스 데이터 게이트웨이](analysis-services-gateway.md)를 설치해야 합니다. 온-프레미스 데이터 원본에 대한 클라우드 연결에 있는 서버가 모델에서 데이터를 처리하고 새로 고치는 데 게이트웨이가 필요합니다.

## <a name="to-deploy-a-tabular-model-from-ssdt"></a>SSDT에서 테이블 형식 모델을 배포하려면
SSDT에서 배포하려면 [최신 버전](https://msdn.microsoft.com/library/mt204009.aspx)을 사용하고 있어야 합니다.

> [!TIP]
> 배포하기 전에 테이블에서 데이터를 처리할 수 있는지 확인합니다. SSDT에서 **모델** > **처리** > **모두 처리**를 클릭합니다. 처리가 실패하는 경우 배포도 실패합니다.
> 
> 

1. 배포하기 전에 서버 이름을 가져와야 합니다. **Azure Portal** > 서버 > **개요** > **서버 이름**에서 서버 이름을 복사합니다.
   
    ![Azure에서 서버 이름 가져오기](./media/analysis-services-deploy/aas-deploy-get-server-name.png)
2. SSDT > **솔루션 탐색기**에서 프로젝트 > **속성**을 마우스 오른쪽 단추로 클릭합니다. 그런 다음 **배포** > **서버**에서 서버 이름을 붙여 넣습니다.   
   
    ![배포 서버 속성에 서버 이름을 붙여 넣습니다](./media/analysis-services-deploy/aas-deploy-deployment-server-property.png)
3. **솔루션 탐색기**에서 **속성**을 마우스 오른쪽 단추로 클릭한 후 **배포**를 클릭합니다. Azure에 로그인하라는 메시지가 표시될 수 있습니다.
   
    ![서버에 배포](./media/analysis-services-deploy/aas-deploy-deploy.png)
   
    배포 상태는 출력 창 및 배포 모두에 표시됩니다.
   
    ![배포 상태](./media/analysis-services-deploy/aas-deploy-status.png)

이제 모든 작업을 마쳤습니다.

## <a name="to-deploy-using-xmla-script"></a>XMLA 스크립트를 사용하여 배포하려면
1. SSMS에서 배포하려는 테이블 형식 모델 데이터베이스를 마우스 오른쪽 단추로 클릭하고 **스크립트** > **데이터베이스 스크립트** > **다음 형식으로 만들기**를 클릭한 다음 위치를 선택합니다.
2. 배포하려는 서버 인스턴스에 대한 쿼리를 실행합니다. 동일한 서버에 배포할 경우 최소한 XMLA 스크립트에서 **이름** 속성을 변경해야 합니다.  

## <a name="but-something-went-wrong"></a>문제가 발생했습니다.
메타데이터를 배포하는 데 실패한 경우 SSDT가 서버에 연결할 수 없기 때문일 수 있습니다. SSMS를 사용하여 서버에 연결할 수 있는지 확인합니다. 프로젝트에 대한 배포 서버 속성이 정확한지 확인합니다.

테이블에서 배포에 실패한 경우 서버가 데이터 원본에 연결할 수 없기 때문일 수 있습니다. 데이터 원본이 조직의 온-프레미스에 있는 경우 [온-프레미스 데이터 게이트웨이](analysis-services-gateway.md)를 설치해야 합니다.

## <a name="next-steps"></a>다음 단계
테이블 형식 모델을 서버에 배포했으므로 연결할 준비가 되었습니다. [SSMS과 연결](analysis-services-manage.md)하여 관리할 수 있습니다. 그리고 Power BI, Power BI Desktop 또는 Excel과 같은 [클라이언트 도구를 사용하여 연결](analysis-services-connect.md)하고 보고서를 만들기 시작할 수 있습니다.


