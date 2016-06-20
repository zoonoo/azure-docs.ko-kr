<properties
   pageTitle="Azure 마켓플레이스에서 가상 컴퓨터 이미지 관리 | Microsoft Azure"
   description="초기 게시 후 Azure 마켓플레이스에서 가상 컴퓨터 이미지를 관리하는 방법에 대한 자세한 가이드입니다."
   services="Azure Marketplace"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="Azure"
   ms.workload="na"
   ms.date="06/01/2016"
   ms.author="hascipio;"/>

# Azure 마켓플레이스의 가상 컴퓨터 제품에 대한 프로덕션 이후 가이드

이 문서에서는 Azure 마켓플레이스의 라이브 가상 컴퓨터 제품을 업데이트하는 방법을 설명합니다. 또한 기존 제품에 하나 이상의 새 SKU를 추가 및 Azure 마켓플레이스에서 라이브 가상 컴퓨터 제품 또는 SKU를 제거하는 과정을 안내합니다.

**SKU가 Azure 마켓플레이스에서 라이브 상태이면 아래에 지정된 세부 정보를 업데이트할 수 없습니다.**

- **SKU 식별자**
- **게시자 식별자**
- **제품 식별자**
- **가격 변경**
- **청구 모델 변경**
- **청구 지역 제거**


## 1\. SKU의 기술 세부 정보를 업데이트하는 방법

VM 이미지를 업데이트하고 아래 제공된 단계에 따라 제품을 다시 게시할 수 있습니다.

1. [게시 포털](https://publish.windowsazure.com)에 로그인합니다.
2. **VIRTUAL MACHINES** 탭으로 이동하고 제품을 선택합니다.
3. 왼쪽 메뉴에서 **VM IMAGES** 탭을 클릭합니다.
4. VM 이미지 탭의 **SKUs** 섹션에서 업데이트하려는 SKU를 찾습니다. 그 다음 SKU의 버전 번호를 업데이트합니다. 새 버전은 X, Y, Z가 정수인 X.Y.Z 형식이어야 합니다. 버전 변경은 증분되어야만 합니다.
5. **OS VHD URL** 상자에 운영 체제 VHD에 대해 만들어진 공유 액세스 서명 URI를 업데이트하고 변경 내용을 저장합니다.
6. 변경 후 **PUBLISH** 탭으로 이동하고 **PUSH TO STAGING** 단추를 클릭합니다. 스테이징 환경에서 제품 테스트에 대한 자세한 지침은 이 [링크](marketplace-publishing-vm-image-test-in-staging.md)를 참조하세요.
7. 스테이징에서 제품을 테스트한 후 게시 포털의 **PUBLISH** 탭으로 이동하고 **REQUEST APPROVAL TO PUSH TO PRODUCTION** 단추를 클릭하여 Azure 마켓플레이스에 제품을 다시 게시합니다.

    ![그리기](media/marketplace-publishing-vm-image-post-publishing/img01.png)

## 2\. 제품 또는 SKU의 비기술적인 세부 정보를 업데이트하는 방법

Azure 마켓플레이스에서 라이브 제품 또는 SKU의 비기술적인(마케팅, 법률, 지원, 범주) 세부 정보를 업데이트할 수 있습니다.

### 2\.1 제품 설명 및 로고 업데이트

제품 세부 정보를 업데이트하고 아래 제공된 단계에 따라 제품을 다시 게시할 수 있습니다.

1. [게시 포털](https://publish.windowsazure.com)에 로그인합니다.
2. **VIRTUAL MACHINES** 탭으로 이동하고 제품을 선택합니다.
3. 왼쪽 메뉴에서 **MARKETING** 탭을 클릭합니다.
4. **영어(미국)** 단추를 클릭합니다.
5. 왼쪽 메뉴에서 **DETAILS** 탭을 클릭합니다. **DETAILS** 탭의 *DESCRIPTION* 섹션 아래에서 제품 제목, 제품 요약, 제품 세부 요약을 업데이트하고 변경 내용을 저장할 수 있습니다.

    >[AZURE.NOTE] SKU 세부 정보를 업데이트하는 동안 다음을 주의하세요. **제품 설명 및 SKU 설명에 중복 텍스트는 입력하지 않습니다. SKU 제목 및 제품 세부 요약에 중복 텍스트는 입력하지 않습니다. SKU 제목 및 제품 요약에 중복 텍스트는 입력하지 않습니다.**

    ![그리기](media/marketplace-publishing-vm-image-post-publishing/img02.png)

6. **DETAILS** 탭의 *LOGOS* 섹션 아래에서 로고를 업데이트할 수 있습니다. 그러나 로고가 [Azure 마켓플레이스 지침](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content)을 따르는지 확인합니다(1단계: 마켓플레이스 마케팅 콘텐츠 제공 -> 세부 정보 -> Azure 마켓플레이스 로고 지침 섹션 참고).

    >[AZURE.NOTE] 대표 아이콘은 선택 사항입니다. 대표 아이콘을 업로드하지 않아도 됩니다. 그러나 대표 아이콘을 업로드하면 게시 포털에서 삭제할 프로비전이 없습니다. 이 경우 [대표 아이콘 지침](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content)을 따라야 합니다(1단계: 마켓플레이스 마케팅 콘텐츠 제공 -> 세부 정보 -> 대표 로고 배너에 대한 추가 지침 섹션 참고).

7. 변경 후 **PUBLISH** 탭으로 이동하고 **PUSH TO STAGING** 단추를 클릭합니다. 스테이징 환경에서 제품 테스트에 대한 자세한 지침은 이 [링크](marketplace-publishing-vm-image-test-in-staging.md)를 참조하세요.
8. 스테이징에서 제품을 테스트한 후 게시 포털의 **PUBLISH** 탭으로 이동하고 **REQUEST APPROVAL TO PUSH TO PRODUCTION** 단추를 클릭하여 Azure 마켓플레이스에 제품을 다시 게시합니다.

    ![그리기](media/marketplace-publishing-vm-image-post-publishing/img02.1.png)

### 2\.2. SKU 설명 업데이트

SKU 세부 정보를 업데이트하고 아래 제공된 단계에 따라 제품을 다시 게시할 수 있습니다.

1. [게시 포털](https://publish.windowsazure.com)에 로그인합니다.
2. **VIRTUAL MACHINES** 탭으로 이동하고 제품을 선택합니다.
3. 왼쪽 메뉴에서 **MARKETING** 탭을 클릭합니다.
4. **영어(미국)** 단추를 클릭합니다.
5. 왼쪽 메뉴에서 **PLANS** 탭을 클릭합니다. **PLANS** 탭의 *SKUs* 섹션 아래에서 SKU 제목, SKU 요약 및 SKU 설명 세부 정보를 업데이트하고 변경 내용을 저장할 수 있습니다.

    >[AZURE.NOTE] SKU 세부 정보를 업데이트하는 동안 다음을 주의하세요. **제품 설명 및 SKU 설명에 중복 텍스트는 입력하지 않습니다. SKU 제목 및 제품 세부 요약에 중복 텍스트는 입력하지 않습니다. SKU 제목 및 제품 요약에 중복 텍스트는 입력하지 않습니다.**

6. 변경 후 **PUBLISH** 탭으로 이동하고 **PUSH TO STAGING** 단추를 클릭합니다. 스테이징 환경에서 제품 테스트에 대한 자세한 지침은 이 링크를 참조하세요.
7. 스테이징에서 제품을 테스트한 후 게시 포털의 **PUBLISH** 탭으로 이동하고 **REQUEST APPROVAL TO PUSH TO PRODUCTION** 단추를 클릭하여 Azure 마켓플레이스에 제품을 다시 게시합니다.

    ![그리기](media/marketplace-publishing-vm-image-post-publishing/img02.2.png)

### 2\.3 기존 링크 변경 또는 새 링크 추가

기존 링크를 변경하거나 새 링크를 추가한 다음 아래 단계를 수행하여 제품을 다시 게시할 수 있습니다.

1. [게시 포털](https://publish.windowsazure.com)에 로그인합니다.
2. **VIRTUAL MACHINES** 탭으로 이동하고 제품을 선택합니다.
3. 왼쪽 메뉴에서 **MARKETING** 탭을 클릭합니다.
4. **영어(미국)** 단추를 클릭합니다.
5. 왼쪽 메뉴에서 **LINKS** 탭을 클릭합니다.
6. 새 링크를 추가하려는 경우 *Links* 섹션 아래에서 **ADD LINK** 단추를 클릭합니다. *"Add Link"* 대화 상자가 열립니다. 이 대화 상자에서 링크 제목 및 URL 필드를 추가하고 변경 내용을 저장할 수 있습니다. 고객에게 도움이 될 수 있는 정보를 포함하는 모든 링크를 입력할 수 있습니다.
7. 기존 링크를 업데이트 또는 삭제하려는 경우 적절한 링크를 선택하고 편집 단추 또는 삭제 단추를 적절하게 클릭합니다.

    >[AZURE.NOTE] 이러한 링크는 프로덕션 요청 프로세스 동안 유효성 검사를 받으므로 이 섹션에서 입력한 링크가 제대로 작동하는지 확인합니다.

8. 변경 후 **PUBLISH** 탭으로 이동하고 **PUSH TO STAGING** 단추를 클릭합니다. 스테이징 환경에서 제품 테스트에 대한 자세한 지침은 이 [링크](marketplace-publishing-vm-image-test-in-staging.md)를 참조하세요.
9. 스테이징에서 제품을 테스트한 후 게시 포털의 **PUBLISH** 탭으로 이동하고 **REQUEST APPROVAL TO PUSH TO PRODUCTION** 단추를 클릭하여 Azure 마켓플레이스에 제품을 다시 게시합니다.

    ![그리기](media/marketplace-publishing-vm-image-post-publishing/img02.3.png)

    ![그리기](media/marketplace-publishing-vm-image-post-publishing/img02.3-2.png)

### 2\.4 기존 샘플 이미지 변경 또는 새 샘플 이미지 추가

기존 샘플 이미지를 변경하거나 새 샘플 이미지를 추가한 다음 아래 단계를 수행하여 제품을 다시 게시할 수 있습니다.

>[AZURE.NOTE] 하나의 샘플 이미지만 [https://portal.azure.com](https://portal.azure.com)에 표시됩니다.

1. [게시 포털](https://publish.windowsazure.com)에 로그인합니다.
2. **VIRTUAL MACHINES** 탭으로 이동하고 제품을 선택합니다.
3. 왼쪽 메뉴에서 **MARKETING** 탭을 클릭합니다.
4. **영어(미국)** 단추를 클릭합니다.
5. 왼쪽 메뉴에서 **SAMPLE IMAGES** 탭을 클릭합니다.
6. 새 샘플 이미지를 추가하려는 경우 *Sample Images* 섹션 아래에서 **UPLOAD A NEW IMAGE** 단추를 클릭한 다음 변경 내용을 저장합니다.

    >[AZURE.NOTE] 샘플 이미지를 포함하는 것은 선택적인 단계입니다.

7. 기존 샘플 이미지를 업데이트 또는 삭제하려는 경우 적절한 샘플 이미지를 찾은 다음 **REPLACE IMAGE** 단추 또는 삭제 단추를 적절하게 클릭합니다.

8. 변경 후 **PUBLISH** 탭으로 이동하고 **PUSH TO STAGING** 단추를 클릭합니다. 스테이징 환경에서 제품 테스트에 대한 자세한 지침은 이 [링크](marketplace-publishing-vm-image-test-in-staging.md)를 참조하세요.
9. 스테이징에서 제품을 테스트한 후 게시 포털의 **PUBLISH** 탭으로 이동하고 **REQUEST APPROVAL TO PUSH TO PRODUCTION** 단추를 클릭하여 Azure 마켓플레이스에 제품을 다시 게시합니다.

    ![그리기](media/marketplace-publishing-vm-image-post-publishing/img02.4.png)

### 2\.5 법적 콘텐츠 업데이트

법적 콘텐츠를 업데이트하고 아래 제공된 단계에 따라 제품을 다시 게시할 수 있습니다.

1. [게시 포털](https://publish.windowsazure.com)에 로그인합니다.
2. **VIRTUAL MACHINES** 탭으로 이동하고 제품을 선택합니다.
3. 왼쪽 메뉴에서 **MARKETING** 탭을 클릭합니다.
4. **영어(미국)** 단추를 클릭합니다.
5. 왼쪽 메뉴에서 **LEGAL** 탭을 클릭합니다. *Legal* 섹션 아래에서 정책/사용 약관을 업데이트할 수 있습니다. *사용 약관* 텍스트 상자에 정책/용어를 입력하거나 붙여 넣고 변경 내용을 저장합니다.
6. 사용 약관의 문자 제한은 1,000,000자입니다.
7. 변경 후 **PUBLISH** 탭으로 이동하고 **PUSH TO STAGING** 단추를 클릭합니다. 스테이징 환경에서 제품 테스트에 대한 자세한 지침은 이 [링크](marketplace-publishing-vm-image-test-in-staging.md)를 참조하세요.
8. 스테이징에서 제품을 테스트한 후 게시 포털의 **PUBLISH** 탭으로 이동하고 **REQUEST APPROVAL TO PUSH TO PRODUCTION** 단추를 클릭하여 Azure 마켓플레이스에 제품을 다시 게시합니다.

    ![그리기](media/marketplace-publishing-vm-image-post-publishing/img02.5.png)

### 2\.6 지원 정보 업데이트

지원 정보를 업데이트하고 아래 제공된 단계에 따라 제품을 다시 게시할 수 있습니다.

1. [게시 포털](https://publish.windowsazure.com)에 로그인합니다.
2. **VIRTUAL MACHINES** 탭으로 이동하고 제품을 선택합니다.
3. 왼쪽 메뉴에서 **SUPPORT** 탭을 클릭합니다.
4. **SUPPORT** 탭의 *엔지니어링 연락처* 섹션 아래에서 연락처 세부 정보를 업데이트할 수 있습니다. 세부 정보는 파트너와 Microsoft 간의 내부 통신에만 사용됩니다.
5. **SUPPORT** 탭의 *고객 지원* 섹션 아래에서 **이름, 전자 메일, 전화 번호** 및 **지원 URL**과 같은 지원 연락처 세부 정보를 업데이트할 수 있습니다. 세부 정보는 파트너와 Microsoft 간의 내부 통신에만 사용됩니다.

    >[AZURE.NOTE] 전자 메일 지원만 제공하려는 경우 **고객 지원** 섹션 아래에 더미 전화 번호를 제공합니다. 이 경우에 제공된 전자 메일이 대신 사용됩니다.

6. 변경 후 **PUBLISH** 탭으로 이동하고 **PUSH TO STAGING** 단추를 클릭합니다. 스테이징 환경에서 제품 테스트에 대한 자세한 지침은 이 [링크](marketplace-publishing-vm-image-test-in-staging.md)를 참조하세요.
7. 스테이징에서 제품을 테스트한 후 게시 포털의 **PUBLISH** 탭으로 이동하고 **REQUEST APPROVAL TO PUSH TO PRODUCTION** 단추를 클릭하여 Azure 마켓플레이스에 제품을 다시 게시합니다.

    ![그리기](media/marketplace-publishing-vm-image-post-publishing/img02.7.png)

### 2\.7 범주 업데이트

제품에 대한 범주 섹션을 업데이트하고 아래 제공된 단계에 따라 제품을 다시 게시할 수 있습니다.

1. [게시 포털](https://publish.windowsazure.com)에 로그인합니다.
2. **VIRTUAL MACHINES** 탭으로 이동하고 제품을 선택합니다.
3. 왼쪽 메뉴에서 **CATEGORIES** 탭을 클릭합니다.
4. *범주* 섹션 아래에서 제품에 대한 범주를 업데이트하고 변경 내용을 저장할 수 있습니다. Azure 마켓플레이스 갤러리에 대해 최대 5개의 범주를 선택할 수 있습니다.
5. 변경 후 **PUBLISH** 탭으로 이동하고 **PUSH TO STAGING** 단추를 클릭합니다. 스테이징 환경에서 제품 테스트에 대한 자세한 지침은 이 [링크](marketplace-publishing-vm-image-test-in-staging.md)를 참조하세요.
6. 스테이징에서 제품을 테스트한 후 게시 포털의 **PUBLISH** 탭으로 이동하고 **REQUEST APPROVAL TO PUSH TO PRODUCTION** 단추를 클릭하여 Azure 마켓플레이스에 제품을 다시 게시합니다.

    ![그리기](media/marketplace-publishing-vm-image-post-publishing/img02.7.png)

## 3\. 라이브 제품 아래에 새로운 SKU를 추가하는 방법

아래에서 제공하는 단계를 수행하여 라이브 제품 아래에 새로운 SKU를 추가할 수 있습니다.

1. [게시 포털](https://publish.windowsazure.com)에 로그인합니다.
2. **VIRTUAL MACHINES** 탭으로 이동하고 제품을 선택합니다.
3. 왼쪽 메뉴에서 **SKUs** 탭을 클릭합니다. 그 다음에 **ADD A SKU** 단추를 클릭합니다. 새 대화 상자가 열립니다. 소문자로 SKU 식별자를 입력합니다. BYOL 청구 모델로 새 SKU를 게시하려는 경우 BYOL(Bring-your-own billing model)에 대한 확인란을 선택합니다. 그렇지 않으면 BYOL에 대한 확인란의 선택을 취소합니다. 그 다음 대화 상자의 확인 표시를 클릭하여 새로운 SKU를 만듭니다. 새로운 SKU에 대한 BYOL 청구 모델에 대해 선택하지 않은 경우 청구 모델은 새 SKU에 대해 자동으로 Hourly로 설정됩니다. Hourly 청구 모델에 대해 30days 무료 평가판을 사용하도록 설정하려는 경우 "무료 평가판이 있나요?"에 대해 "1개월" 옵션을 클릭합니다. 그렇지 않은 경우 "평가판 없음"을 선택합니다. [참고: 새 SKU를 만드는 동안 대화 상자에서 BYOL을 선택하지 않은 경우에만 "무료 평가판이 있나요?" 옵션이 표시됩니다.]

    >[AZURE.IMPORTANT] Azure 마켓플레이스에서 솔루션 템플릿 제품 게시에 대해 승인한 경우 "솔루션 템플릿을 통해 항상 구입되어야 하기 때문에 마켓플레이스에서 이 SKU를 숨깁니다" 옵션은 "예"로 표시되어야 합니다. 그렇지 않은 경우 이 옵션은 항상 "아니요"로 표시되어야 합니다.

4. 이제 왼쪽 메뉴에서 **VM IMAGES** 탭을 클릭하고 생성한 새로운 SKU를 찾습니다.
5. 새로운 SKU를 설정하려면 이 [링크](marketplace-publishing-vm-image-creation.md#5-obtain-certification-for-your-vm-image)의 5단계의 지침을 참조하세요.
6. 새로운 SKU에 대한 마케팅 자료를 추가하려면 1단계: 마켓플레이스 마케팅 콘텐츠 제공 -> 세부 정보 -> 이 [링크](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content)의 2~5 섹션을 참조하세요.
7. 새로운 SKU에 대한 가격 정보를 추가하려면 2.1 섹션을 참조하세요. 이 [링크](marketplace-publishing-push-to-staging.md#step-2-set-your-prices)의 VM 가격 설정
8. 변경 후 **PUBLISH** 탭으로 이동하고 **PUSH TO STAGING** 단추를 클릭합니다. 스테이징 환경에서 제품 테스트에 대한 자세한 지침은 이 [링크](marketplace-publishing-vm-image-test-in-staging.md)를 참조하세요.
9. 스테이징에서 제품을 테스트한 후 게시 포털의 **PUBLISH** 탭으로 이동하고 **REQUEST APPROVAL TO PUSH TO PRODUCTION** 단추를 클릭하여 Azure 마켓플레이스에 제품을 다시 게시합니다.

    ![그리기](media/marketplace-publishing-vm-image-post-publishing/img03.1-1.png)

    ![그리기](media/marketplace-publishing-vm-image-post-publishing/img03.1-2.png)

## 4\. Azure 마켓플레이스에서 라이브 제품 또는 SKU를 삭제하는 방법

라이브 제품을 제거하는 요청 시 해결해야 하는 다양한 측면이 있습니다. Azure 마켓플레이스에서 라이브 제공/SKU를 제거하려면 다음 단계를 수행하여 지원 팀의 지침을 확인하세요.

1.	이 [링크](https://support.microsoft.com/ko-KR/getsupport?wf=0&tenant=ClassicCommercial&oaspworkflow=start_1.0.0.0&locale=ko-KR&supportregion=ko-KR&pesid=15635&ccsid=635993707583706681)를 사용하여 지원 티켓 제기
2.	**"제품 관리"**로 문제 유형 선택 및 **"프로덕션 환경에서 제품 및/또는 SKU 수정"**으로 범주 선택
3.	요청 제출

지원 팀은 제품/SKU 삭제 프로세스를 안내합니다.

>[AZURE.NOTE] 제품이 초안 상태(예: 스테이징 또는 프로덕션에 있지 않은 경우)에 있는 동안 **HISTORY** 탭 아래의 **DISCARD DRAFT** 단추를 클릭하여 항상 제품을 삭제할 수 있습니다.

## 참고 항목
- [시작: Azure 마켓플레이스에 제품을 게시하는 방법](marketplace-publishing-getting-started.md)
- [판매자 통찰력 보고 이해](marketplace-publishing-report-seller-insights.md)
- [지급 보고 이해](marketplace-publishing-report-payout.md)
- [클라우드 솔루션 공급자 대리점 인센티브를 변경하는 방법](marketplace-publishing-csp-incentive.md)
- [마켓플레이스에서 일반적인 게시 문제 해결](marketplace-publishing-support-common-issues.md)
- [게시자로 지원 받기](marketplace-publishing-get-publisher-support.md)
- [온-프레미스로 VM 이미지 만들기](marketplace-publishing-vm-image-creation-on-premise.md)
- [Azure Preview 포털에서 Windows를 실행하는 가상 컴퓨터 만들기](../virtual-machines/virtual-machines-windows-hero-tutorial.md)

<!---HONumber=AcomDC_0608_2016-->