---
title: "Azure Analysis Services에서 데이터 가져오기 | Microsoft Docs"
description: "Azure의 Analysis Services 서버에서 데이터에 연결하고 가져오는 방법에 대해 알아봅니다."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: b37f70a0-9166-4173-932d-935d769539d1
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 11/28/2016
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: 193c939065979dc48243d31e7f97cd87d96bf9a8
ms.openlocfilehash: 6b65689c75247af81e916730f125ff39bb49489f


---
# <a name="get-data-from-azure-analysis-services"></a>Azure Analysis Services에서 데이터 가져오기
Azure에서 서버를 만들고 테이블 형식 모델을 배포하면 조직의 사용자는 데이터를 연결하고 탐색할 준비가 되었습니다.

Azure Analysis Services는 xmla를 통해 서버에 연결하도록 [업데이트된 개체 모델](#client-libraries)(TOM, AMO, Adomd.Net 또는 MSOLAP)을 사용하는 클라이언트 연결을 지원합니다. 예를 들어 Power BI, Power BI Desktop, Excel 또는 개체 모델에서 지원하는 모든 타사 클라이언트 응용 프로그램입니다.

## <a name="server-name"></a>서버 이름
Azure에서 Analysis Services 서버를 만들 경우 고유한 이름 및 만들 서버 지역을 지정합니다. 연결에서 서버 이름을 지정할 경우 서버 명명 구성표는 다음과 같습니다.

```
<protocol>://<region>/<servername>
```
 프로토콜이 문자열 **asazure**인 경우 지역은 서버를 만든 지역의 URI(예: 미국 서부의 경우 westus.asazure.windows.net)이고 서버 이름은 지역 내의 고유 서버 이름입니다.

## <a name="get-the-server-name"></a>서버 이름 가져오기
먼저 서버 이름을 가져온 후에 연결할 수 있습니다. **Azure Portal** > 서버 > **개요** > **서버 이름**에서 전체 서버 이름을 복사합니다. 조직의 다른 사용자가 이 서버에 연결하는 경우 이 서버 이름을 공유합니다. 서버 이름을 지정할 경우 전체 경로를 사용해야 합니다.

![Azure에서 서버 이름 가져오기](./media/analysis-services-deploy/aas-deploy-get-server-name.png)

## <a name="connect-in-power-bi-desktop"></a>Power BI Desktop에서 연결
> [!NOTE]
> 이 기능은 미리 보기 상태입니다.
> 
> 

1. [Power BI Desktop](https://powerbi.microsoft.com/desktop/)에서 **데이터 가져오기** > **데이터베이스** > **Azure Analysis Services**를 클릭합니다.
2. **서버**에서 클립보드의 서버 이름을 붙여 넣습니다.
3. **데이터베이스**에서 연결하려는 테이블 형식 모델 데이터베이스 및 큐브 뷰의 이름을 알고 있는 경우 여기에 붙여 넣습니다. 그렇지 않으면 이 필드를 비워 두면 됩니다. 다음 화면에서 데이터베이스 또는 큐브 뷰를 선택할 수 있습니다.
4. 기본 **라이브 연결** 옵션을 선택한 대로 두고 **연결**을 누릅니다. 계정을 입력하라는 메시지가 표시되면 조직 계정을 입력합니다.
5. **탐색기**에서 서버를 확장한 다음 연결하려는 모델 또는 큐브 뷰를 선택하고 **연결**을 클릭합니다. 모델 또는 큐브 뷰를 한 번 클릭하면 해당 보기에 대한 모든 개체를 표시합니다.

## <a name="connect-in-power-bi"></a>Power BI에서 연결
1. 서버의 모델에 대한 라이브 연결을 포함하는 Power BI Desktop 파일을 만듭니다.
2. [Power BI](https://powerbi.microsoft.com)에서 **데이터 가져오기** > **파일**을 클릭합니다. 파일을 찾아 선택합니다.

## <a name="connect-in-excel"></a>Excel에서 연결
Excel 2016에서 데이터 가져오기 또는 이전 버전의 파워 쿼리를 사용하여 Excel에서 Azure Analysis Services 서버에 연결하도록 지원합니다. [MSOLAP.7 공급자](https://aka.ms/msolap)가 필요합니다. 파워 피벗에서 테이블 가져오기 마법사를 사용하여 연결하는 작업은 지원되지 않습니다.

1. Excel 2016의 **데이터** 리본 메뉴에서 **외부 데이터 가져오기** > **다른 원본에서** > **Analysis Services에서**를 클릭합니다.
2. 데이터 연결 마법사의 **서버 이름**에서 클립보드의 서버 이름을 붙여 넣습니다. 그런 다음 **로그온 자격 증명**에서 **다음 사용자 이름 및 암호 사용**을 선택하고 조직 사용자 이름(예: nancy@adventureworks.com, 및 암호)을 입력합니다.
   
    ![Excel 로그온에서 연결](./media/analysis-services-connect/aas-connect-excel-logon.png)
3. **데이터베이스 및 테이블 선택**에서 데이터베이스, 모델 또는 큐브 뷰를 선택한 다음 **마침**을 클릭합니다.
   
    ![Excel 선택 모델에서 연결](./media/analysis-services-connect/aas-connect-excel-select.png)

## <a name="connection-string"></a>연결 문자열
테이블 형식 개체 모델을 사용하여 Azure Analysis Services에 연결할 경우 다음 연결 문자열 서식을 사용합니다.

###### <a name="integrated-azure-active-directory-authentication"></a>통합 Azure Active Directory 인증
```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;"
```
사용할 수 있는 경우 통합 인증에서 Azure Active Directory 자격 증명 캐시를 선택합니다. 그렇지 않은 경우 Azure 로그인 창이 표시됩니다.

###### <a name="azure-active-directory-authentication-with-username-and-password"></a>사용자 이름 및 암호를 포함한 Azure Active Directory 인증
```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;User ID=<user name>;Password=<password>;Persist Security Info=True; Impersonation Level=Impersonate;";
```

## <a name="client-libraries"></a>클라이언트 라이브러리
Excel 또는 TOM, AsCmd, ADOMD.NET와 같은 다른 인터페이스에서 Azure Analysis Services를 연결할 경우 최신 공급자 클라이언트 라이브러리를 설치해야 합니다. 최신 항목 가져오기:  

[MSOLAP(amd64)](https://go.microsoft.com/fwlink/?linkid=829576)</br>
[MSOLAP(x86)](https://go.microsoft.com/fwlink/?linkid=829575)</br>
[AMO](https://go.microsoft.com/fwlink/?linkid=829578)</br>
[ADOMD](https://go.microsoft.com/fwlink/?linkid=829577)</br>

## <a name="next-steps"></a>다음 단계
[서버 관리](analysis-services-manage.md)




<!--HONumber=Nov16_HO3-->


