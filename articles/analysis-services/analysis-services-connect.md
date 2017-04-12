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
ms.date: 02/13/2017
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: eccc181e2203a97de58005f9597613775338ebcc
ms.lasthandoff: 03/25/2017


---
# <a name="get-data-from-azure-analysis-services"></a>Azure Analysis Services에서 데이터 가져오기

Azure에서 서버를 만들고 테이블 형식 모델을 배포하면 조직의 사용자는 데이터를 연결하고 탐색할 준비가 되었습니다.

## <a name="data-providers-aka-client-libraries"></a>데이터 공급자(즉, 클라이언트 라이브러리)

Power BI Desktop 및 Microsoft Excel과 같은 클라이언트 응용 프로그램은 업데이트된 AMO, ADOMD.NET 및 OLEDB 공급자를 사용하여 Analysis Services를 연결하고 사용합니다. 일부 오래된 Excel 버전 또는 사용자 지정 응용 프로그램의 경우 Azure Analysis Services에 연결하려면 최신 데이터 공급자를 설치해야 합니다. 자세한 내용은 [데이터 공급자](analysis-services-data-providers.md)를 참조하세요.

## <a name="server-name"></a>서버 이름

Azure에서 Analysis Services 서버를 만들 경우 고유한 이름 및 만들 서버 지역을 지정합니다. 연결에서 서버 이름을 지정할 경우 서버 명명 구성표는 다음과 같습니다.

```
<protocol>://<region>/<servername>
```
 프로토콜이 문자열 **asazure**인 경우 지역은 서버를 만든 지역의 URI(예: 미국 서부의 경우 westus.asazure.windows.net)이고 서버 이름은 지역 내의 고유 서버 이름입니다.

## <a name="get-the-server-name"></a>서버 이름 가져오기

먼저 서버 이름을 가져온 후에 연결할 수 있습니다. **Azure Portal** > 서버 > **개요** > **서버 이름**에서 전체 서버 이름을 복사합니다. 조직의 다른 사용자가 이 서버에 연결하는 경우 그들과 이 서버 이름을 공유할 수 있습니다. 서버 이름을 지정할 경우 전체 경로를 사용해야 합니다.

![Azure에서 서버 이름 가져오기](./media/analysis-services-deploy/aas-deploy-get-server-name.png)

## <a name="connect-in-power-bi-desktop"></a>Power BI Desktop에서 연결

> [!NOTE]
> 이 기능은 미리 보기 상태입니다.
> 
> 

1. [Power BI Desktop](https://powerbi.microsoft.com/desktop/)에서 **데이터 가져오기** > **Azure** > **Microsoft Azure Analysis Services 데이터베이스**를 클릭합니다.
2. **서버**에서 클립보드의 서버 이름을 붙여 넣습니다.
3. **데이터베이스**에서 연결하려는 테이블 형식 모델 데이터베이스 및 큐브 뷰의 이름을 알고 있는 경우 여기에 붙여 넣습니다. 그렇지 않으면 이 필드를 비워 두면 됩니다. 다음 화면에서 데이터베이스 또는 큐브 뷰를 선택할 수 있습니다.
4. 기본 **라이브 연결** 옵션을 선택한 대로 두고 **연결**을 누릅니다. 계정을 입력하라는 메시지가 표시되면 조직 계정을 입력합니다.
5. **탐색기**에서 서버를 확장한 다음 연결하려는 모델 또는 큐브 뷰를 선택하고 **연결**을 클릭합니다. 모델 또는 큐브 뷰를 클릭하면 해당 보기에 대한 모든 개체가 표시됩니다.

## <a name="connect-in-power-bi"></a>Power BI에서 연결

1. 서버의 모델에 대한 라이브 연결을 포함하는 Power BI Desktop 파일을 만듭니다.
2. [Power BI](https://powerbi.microsoft.com)에서 **데이터 가져오기** > **파일**을 클릭합니다. 파일을 찾아 선택합니다.

## <a name="connect-in-excel"></a>Excel에서 연결

Excel 2016에서 데이터 가져오기 또는 이전 버전의 파워 쿼리를 사용하여 Excel에서 Azure Analysis Services 서버에 연결하도록 지원합니다. [MSOLAP.7 공급자](analysis-services-data-providers.md)가 필요합니다. 파워 피벗에서 테이블 가져오기 마법사를 사용하여 연결하는 작업은 지원되지 않습니다.

> [!NOTE]
> 일부 조직에서는 지연된 채널에서 Office 365 업데이트를 배포하므로 버전 업데이트는 현재 버전부터 최대 4개월까지 지연됩니다. Excel 2016 버전 1609.7369.2115 이하 또는 Excel 2013의 경우 .odc 파일을 만들고 MSOLAP.7 공급자를 수동으로 업데이트하여 서버에 연결할 수 있습니다. 자세한 내용은 [.odc 파일 만들기](analysis-services-odc.md)를 참조하세요.
> 
> 

**Excel 2016에서 연결하려면**

1. Excel 2016의 **데이터** 리본 메뉴에서 **외부 데이터 가져오기** > **다른 원본에서** > **Analysis Services에서**를 클릭합니다.
2. 데이터 연결 마법사의 **서버 이름**에서 클립보드의 서버 이름을 붙여 넣습니다. 그런 다음 **로그온 자격 증명**에서 **다음 사용자 이름 및 암호 사용**을 선택하고 조직 사용자 이름(예: nancy@adventureworks.com 및 암호)을 입력합니다.

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

## <a name="next-steps"></a>다음 단계

[서버 관리](analysis-services-manage.md)


