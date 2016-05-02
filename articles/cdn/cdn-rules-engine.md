<properties
	pageTitle="CDN - 규칙 엔진을 사용하여 기본 HTTP 동작 재정의"
	description="규칙 엔진을 사용하면 특정 유형의 콘텐츠 전달 차단과 같이 HTTP 요청이 처리되는 방식을 사용자 지정하여 캐싱 정책을 정의하고 HTTP 헤더를 수정할 수 있습니다."
	services="cdn"
	documentationCenter=".NET"
	authors="camsoper"
	manager="erikre"
	editor=""/>

<tags
	ms.service="cdn"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/15/2016" 
	ms.author="casoper"/>

# 규칙 엔진을 사용하여 기본 HTTP 동작 재정의

## 개요

규칙 엔진을 사용하면 특정 유형의 콘텐츠 전달 차단과 같이 HTTP 요청이 처리되는 방식을 사용자 지정하여 캐싱 정책을 정의하고 HTTP 헤더를 수정할 수 있습니다. 이 자습서에서는 CDN 자산의 캐싱 동작을 변경하는 규칙 만들기를 보여 줍니다. "[참고 항목](#see-also)" 섹션에는 동영상 콘텐츠도 있습니다.

> [AZURE.NOTE] 규칙 엔진은 프리미엄 CDN 계층의 기능입니다. 표준 및 프리미엄 CDN 기능을 비교하려면 [Azure CDN 개요](cdn-overview.md)를 참조하세요.

## 자습서

1. CDN 프로필 블레이드에서 **관리** 단추를 클릭합니다.

	![CDN 프로필 블레이드 관리 단추](./media/cdn-rules-engine/cdn-rules-manage-btn.png)

	CDN 관리 포털이 열립니다.

2. **HTTP Large**(HTTP 크게) 탭, **규칙 엔진**을 차례로 클릭합니다.

	새 규칙에 대한 옵션이 표시됩니다.

	![CDN 새 규칙 옵션](./media/cdn-rules-engine/cdn-new-rule.png)

3. **이름/설명** 텍스트 상자에 이름을 입력합니다.

4. 규칙이 적용되는 요청의 유형을 식별합니다. 기본적으로 **항상** 일치 조건이 선택되어 있습니다. 이 자습서에서는 **항상**을 사용하므로 선택된 상태 그대로 두면 됩니다.

	![CDN 일치 조건](./media/cdn-rules-engine/cdn-request-type.png)

	>[AZURE.TIP] 드롭다운 목록에서 사용할 수 있는 다양한 유형의 일치 조건이 있습니다. 일치 조건의 왼쪽에 있는 파란색 정보 아이콘을 클릭하면 현재 선택한 조건을 자세히 설명합니다.
	>
	>일치 조건의 전체 목록을 자세하게 보려면 [규칙 엔진 일치 조건 및 기능 세부 정보](cdn-rules-engine-details.md#match-conditions)를 참조하세요.

5.  **기능** 옆에 있는 **+** 단추를 클릭하여 새 기능을 추가합니다. 왼쪽의 드롭다운 목록에서 **Force Internal Max-Age**(내부 Max-Age 강제)를 선택합니다. 표시되는 텍스트 상자에 **300**을 입력합니다. 나머지 기본값을 그대로 둡니다.

	![CDN 기능](./media/cdn-rules-engine/cdn-new-feature.png)

	>[AZURE.NOTE] 일치 조건처럼 새 기능의 왼쪽에 있는 파란색 정보 아이콘을 클릭하면 이 기능에 대한 세부 정보가 표시됩니다. **Force Internal Max-Age**(내부 Max-Age 강제)의 경우 자산의 **Cache-Control** 및 **Expires** 헤더를 재정의하여 CDN 에지 노드가 원본에서 자산을 새로 고치는 시기를 제어합니다. 300초의 경우 원본에서 자산을 새로 고치기 전에 CDN 에지 노드가 자산을 5분 동안 캐시한다는 의미입니다.
	>
	>기능의 전체 목록을 자세하게 보려면 [규칙 엔진 일치 조건 및 기능 세부 정보](cdn-rules-engine-details.md#features)를 참조하세요.

6.  **추가** 단추를 클릭하여 새 규칙을 저장합니다. 이제 새 규칙이 승인 대기 중입니다. 승인되면 상태가 **보류 중인 XML**에서 **활성 XML**로 변경됩니다.

## 고려 사항

- 여러 규칙이 나열된 순서는 규칙이 처리되는 방식에 영향을 줍니다. 후속 규칙은 이전 규칙에서 지정된 동작을 재정의할 수 있습니다.

## 참고 항목
* [Azure Fridays: Azure CDN의 강력하고 새로운 프리미엄 기능](../../videos/azure-cdns-powerful-new-premium-features/)(동영상)
* [규칙 엔진 일치 조건 및 기능 세부 정보](cdn-rules-engine-details.md)

<!---HONumber=AcomDC_0420_2016-->