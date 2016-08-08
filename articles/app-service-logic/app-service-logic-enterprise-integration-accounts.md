<properties 
	pageTitle="통합 계정 및 엔터프라이즈 통합 팩 개요 | Microsoft Azure 앱 서비스 | Microsoft Azure" 
	description="통합 계정, 엔터프라이즈 통합 팩 및 논리 앱에 대해 모두 알아보기" 
	services="app-service\logic" 
	documentationCenter=".net,nodejs,java"
	authors="msftman" 
	manager="erikre" 
	editor="cgronlun"/>

<tags 
	ms.service="logic-apps" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/08/2016" 
	ms.author="deonhe"/>

# 통합 계정 개요

## 통합 계정이란?
통합 계정은 엔터프라이즈 통합 앱에서 스키마, 맵, 인증서, 파트너 및 규약을 포함한 아티팩트를 관리할 수 있는 Azure 계정입니다. 예를 들어 만든 모든 통합 앱은 스키마, 맵 또는 인증서에 액세스하기 위해 통합 계정을 사용해야 합니다.

## 통합 계정 만들기 
1. **찾아보기**를 선택합니다. ![](./media/app-service-logic-enterprise-integration-accounts/account-1.png)
2. 필터 검색 상자에 **통합**을 입력하고 결과 목록에서 **통합 계정**을 선택합니다. ![](./media/app-service-logic-enterprise-integration-accounts/account-2.png)
3. 페이지 위쪽에 있는 메뉴에서 *추가* 단추를 선택합니다. ![](./media/app-service-logic-enterprise-integration-accounts/account-3.png)
4. **이름**을 입력하고 사용하려는 **구독**을 선택하고 새 **리소스 그룹**을 만들거나 기존 리소스 그룹을 선택하고 통합 계정을 호스팅할 **위치**를 선택하고 **가격 책정 계층**을 선택한 다음 **만들기** 단추를 선택합니다.

  이 시점에서 선택한 위치에서 통합 계정이 프로비전됩니다. 1분 내에 완료해야 합니다. ![](./media/app-service-logic-enterprise-integration-accounts/account-4.png)
5. 페이지를 새로 고칩니다. 나열된 새로운 통합 계정이 표시됩니다. 축하합니다! ![](./media/app-service-logic-enterprise-integration-accounts/account-5.png)

## 논리 앱에 통합 계정을 연결하는 방법
논리 앱이 통합 계정에 있는 맵, 스키마, 계약 및 다른 아티팩트에 액세스하기 위해 먼저 논리 앱에 통합 계정을 연결해야 합니다.

### 다음은 논리 앱에 통합 계정을 연결하는 단계입니다. 

#### 필수 조건
- 통합 계정
- 논리 앱

>[AZURE.NOTE]통합 계정 및 논리 앱은 시작 하기 전에 **동일한 Azure 위치**에 있어야 합니다.

1. 논리 앱의 메뉴에서 **설정** 링크를 선택합니다. ![](./media/app-service-logic-enterprise-integration-accounts/linkaccount-1.png)
2. 설정 블레이드에서 **통합 계정** 항목을 선택합니다. ![](./media/app-service-logic-enterprise-integration-accounts/linkaccount-2.png)
3. **통합 계정 선택** 드롭다운 목록 상자에서 논리 앱에 연결하려는 통합 계정을 선택합니다. ![](./media/app-service-logic-enterprise-integration-accounts/linkaccount-3.png)
4. 작업을 저장합니다. ![](./media/app-service-logic-enterprise-integration-accounts/linkaccount-4.png)
5. 통합 계정을 논리 앱에 연결하고 통합 계정의 모든 아티팩트를 이제 논리 앱에서 사용할 수 있음을 나타내는 알림이 표시됩니다. ![](./media/app-service-logic-enterprise-integration-accounts/linkaccount-5.png)

이제 통합 계정을 논리 앱에 연결하면 논리 앱으로 이동하여 B2B 기능으로 앱을 만들기 위해 XML 유효성 검사, 플랫 파일 인코딩/디코딩 또는 변환 등의 B2B 커넥터를 사용할 수 있습니다.
    
## 통합 계정 삭제 방법
1. **찾아보기**를 선택합니다. ![](./media/app-service-logic-enterprise-integration-overview/overview-1.png)
2. 필터 검색 상자에 **통합**을 입력하고 결과 목록에서 **통합 계정**을 선택합니다. ![](./media/app-service-logic-enterprise-integration-overview/overview-2.png)
3. 삭제하려는 **통합 계정**을 선택합니다. ![](./media/app-service-logic-enterprise-integration-overview/overview-3.png)
4. 메뉴에 있는 **삭제** 링크를 선택합니다. ![](./media/app-service-logic-enterprise-integration-accounts/delete.png)
5. 선택을 확인합니다.

## 통합 계정 이동 방법
통합 계정을 새 구독 및 새 리소스 그룹에 쉽게 이동할 수 있습니다. 통합 계정을 이동하는 경우 다음 단계를 따릅니다.

>[AZURE.IMPORTANT] 통합 계정을 이동한 후에 새 리소스 ID를 사용하여 모든 스크립트를 업데이트해야 합니다.

1. **찾아보기**를 선택합니다. ![](./media/app-service-logic-enterprise-integration-overview/overview-1.png)
2. 필터 검색 상자에 **통합**을 입력하고 결과 목록에서 **통합 계정**을 선택합니다. ![](./media/app-service-logic-enterprise-integration-overview/overview-2.png)
3. 삭제하려는 **통합 계정**을 선택합니다. ![](./media/app-service-logic-enterprise-integration-overview/overview-3.png)
4. 메뉴에 있는 **이동** 링크를 선택합니다. ![](./media/app-service-logic-enterprise-integration-accounts/move.png)
5. 선택을 확인합니다.

## 다음 단계
- [엔터프라이즈 통합 팩에 대해 자세히 알아보기](./app-service-logic-enterprise-integration-overview.md "엔터프라이즈 통합 팩에 대해 알아보기")
- [규약에 대해 자세히 알아보기](./app-service-logic-enterprise-integration-agreements.md "엔터프라이즈 통합 규약에 대해 알아보기")


 

<!---HONumber=AcomDC_0727_2016-->