<properties
	pageTitle="Azure CDN 끝점에 자산 미리 로드 | Microsoft Azure"
	description="CDN 끝점에 캐시된 콘텐츠를 미리 로드하는 방법을 알아봅니다."
	services="cdn"
	documentationCenter=""
	authors="camsoper"
	manager="erikre"
	editor=""/>

<tags
	ms.service="cdn"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/28/2016"
	ms.author="casoper"/>

# Azure CDN 끝점에 자산 미리 로드

[AZURE.INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

기본적으로 요청되었으므로 자산이 먼저 캐시됩니다. 즉, 가장자리 서버는 캐시된 콘텐츠가 없고 요청을 원본 서버에 전달해야 하므로 각 지역의 첫 번째 요청은 오래 걸릴 수 있습니다. 콘텐츠를 미리 로드하면 첫 번째 적중 대기 시간이 발생하지 않습니다.

더 나은 고객 경험을 제공할 뿐 아니라 캐시된 자산을 미리 로드하면 원본 서버에서 네트워크 트래픽을 줄일 수 있습니다.

> [AZURE.NOTE] 자산을 미리 로드하는 것은 대규모 이벤트 또는 새로운 동영상 릴리스나 소프트웨어 업데이트와 같이 많은 수의 사용자가 동시에 사용할 수 있는 콘텐츠에 유용 합니다.

이 자습서는 모든 Azure CDN 에지 노드에 캐시된 콘텐츠 미리 로드에 대해 안내합니다.

## 연습

1. [Azure 포털](https://portal.azure.com)에서 미리 로드하려는 끝점을 포함하는 CDN 프로필로 이동합니다. 프로필 블레이드가 열립니다.

2. 목록에서 끝점을 클릭합니다. 끝점 블레이드가 열립니다.

3. CDN 끝점 블레이드에서 로드 단추를 클릭합니다.

	![CDN 끝점 블레이드](./media/cdn-preload-endpoint/cdn-endpoint-blade.png)

	로드 블레이드가 열립니다.

	![CDN 로드 블레이드](./media/cdn-preload-endpoint/cdn-load-blade.png)

4. 로드하려는 각 자산의 전체 경로(예: `/pictures/kitten.png`)를 **경로** 텍스트 상자에 입력합니다.

	> [AZURE.TIP] 여러 자산 목록을 작성할 수 있도록 하는 텍스트를 입력한 후에 더 많은 **경로** 텍스트 상자가 표시됩니다. 목록에서 줄임표(...) 단추를 클릭하여 자산을 삭제할 수 있습니다.
	>
	> 경로는 다음 [정규식](https://msdn.microsoft.com/library/az24scfc.aspx)에 맞는 상대 URL이어야 합니다. `^(?:\/[a-zA-Z0-9-_.\u0020]+)+$` 각 자산에는 자체 경로가 있어야 합니다. 미리 로드한 자산에 대한 와일드카드 기능은 없습니다.

    ![로드 단추](./media/cdn-preload-endpoint/cdn-load-paths.png)

5. **로드** 단추를 클릭합니다.

	![로드 단추](./media/cdn-preload-endpoint/cdn-load-button.png)

> [AZURE.NOTE] 로드 요청은 CDN 프로필별로 분당 10개로 제한됩니다.

## 참고 항목
- [Azure CDN 끝점 제거](cdn-purge-endpoint.md)
- [Azure CDN REST API 참조 - 끝점 제거 또는 미리 로드](https://msdn.microsoft.com/library/mt634451.aspx)

<!---HONumber=AcomDC_0803_2016-->