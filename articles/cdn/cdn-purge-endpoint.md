<properties
	pageTitle="Azure CDN 끝점 제거"
	description="CDN 끝점에서 캐시된 콘텐츠를 모두 제거하는 방법을 알아봅니다."
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
	ms.date="05/11/2016"
	ms.author="casoper"/>

# Azure CDN 끝점 제거

## 개요

Azure CDN 가장자리 노드는 자산의 TTL(Time-to-Live)이 만료될 때 자산을 캐시합니다. 자산의 TTL이 만료된 후에 클라이언트가 가장자리 노드에서 자산을 요청하는 경우 가장자리 노드는 새로 업데이트된 자산 사본을 검색하여 클라이언트 요청을 처리하고 저장소는 캐시를 새로 고칩니다.

경우에 따라 모든 가장자리 노드에서 캐시된 콘텐츠를 제거하고 이러한 콘텐츠가 모두 새로 업데이트된 자산을 검색하도록 강제하려 할 수 있습니다. 웹 응용 프로그램에 대한 업데이트 또는 잘못된 정보가 포함된 신속한 업데이트 자산 때문일 수 있습니다.

> [AZURE.TIP] 제거는 CDN 에지 서버에 캐시된 콘텐츠만 지웁니다. 프록시 서버와 로컬 브라우저 캐시 같은 다운스트림 캐시에는, 캐시된 파일 사본이 유지될 수 있습니다. 파일의 TTL(Time-To-Live)을 설정할 때 이것을 기억하는 것이 중요합니다. 업데이트할 때마다 고유한 이름을 부여하거나 [쿼리 문자열 캐싱](cdn-query-string.md)의 이점을 활용하여, 다운스트림 클라이언트가 파일의 최신 버전을 요청하도록 할 수 있습니다.

이 자습서는 끝점의 모든 가장자리 노드에서 자산을 제거하는 과정을 안내합니다.

## 연습

1. [Azure 포털](https://portal.azure.com)에서 제거하려는 끝점을 포함하는 CDN 프로필로 이동합니다.

2. CDN 프로필 블레이드에서 제거 단추를 클릭합니다.

	![CDN 프로필 블레이드](./media/cdn-purge-endpoint/cdn-profile-blade.png)

	제거 블레이드가 열립니다.

	![CDN 제거 블레이드](./media/cdn-purge-endpoint/cdn-purge-blade.png)

3. 제거 블레이드에서 URL 드롭다운 목록에서 제거하려는 서비스 주소를 선택합니다.

	![양식 제거](./media/cdn-purge-endpoint/cdn-purge-form.png)

	> [AZURE.NOTE] 또한 CDN 끝점 블레이드에서 **제거** 단추를 클릭하여 제거 블레이드로 가져올 수 있습니다. 이 경우에 **URL** 필드는 해당 특정 끝점의 서비스 주소로 미리 채워질 수 있습니다.

4. 가장자리 노드에서 제거하려는 자산을 선택합니다. 모든 자산을 지우려면 **모두 제거** 확인란을 클릭합니다. 그렇지 않은 경우 제거하려는 각 자산의 전체 경로(예: `/pictures/kitten.png`)를 **경로** 텍스트 상자에 입력합니다.

	> [AZURE.TIP] 여러 자산 목록을 작성할 수 있도록 하는 텍스트를 입력한 후에 더 많은 **경로** 텍스트 상자가 표시됩니다. 목록에서 줄임표(...) 단추를 클릭하여 자산을 삭제할 수 있습니다.
	>
	> 경로는 다음 [정규식](https://msdn.microsoft.com/library/az24scfc.aspx)에 맞는 상대 URL이어야 합니다. `^\/(?:[a-zA-Z0-9-_.\u0020]+\/)**$";`. **Verizon의 Azure CDN**(Standard 및 Premium)의 경우, 별표(*)를 와일드카드로 사용할 수 있습니다(예: `/music/*`). 와일드카드 및 **모두 제거**는 **Akamai의 Azure CDN**에 허용되지 않습니다.
	
5. **제거** 단추를 클릭합니다.

	![제거 단추](./media/cdn-purge-endpoint/cdn-purge-button.png)

> [AZURE.IMPORTANT] 제거 요청은 **Verizon의 Azure CDN**(Standard 및 Premium)으로 처리하려면 약 2-3분, **Akamai의 Azure CDN**으로 처리하려면 약 7분이 소요됩니다. Azure CDN은 동시 제거 요청이 항상 50개로 제한됩니다.

## 참고 항목
- [Azure CDN 끝점에 자산 미리 로드](cdn-preload-endpoint.md)
- [Azure CDN REST API 참조 - 끝점 제거 또는 미리 로드](https://msdn.microsoft.com/library/mt634451.aspx)

<!---HONumber=AcomDC_0518_2016-->