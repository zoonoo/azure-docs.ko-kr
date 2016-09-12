<properties
    pageTitle="DocumentDB 포털 문제 해결 | Microsoft Azure"
    description="DocumentDB Azure 포털에서 문제를 해결하는 방법에 대해 알아봅니다." 
    services="documentdb"
    documentationCenter=""
    authors="mimig1"
    manager="jhubbard"
    editor="monicar"/>

<tags
    ms.service="documentdb"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/29/2016"
    ms.author="mimig"/>

# Azure DocumentDB 포털 문제 해결 팁

이 문서에서는 Azure 포털에서 DocumentDB 문제를 해결하는 방법을 설명합니다.

## 리소스가 없음

**증상**: 포털 블레이드에 데이터베이스나 컬렉션이 없습니다.

**해결 방법**: 컬렉션의 최대 처리량 할당량보다 낮은 처리량에서 작동하도록 응용 프로그램 사용량을 줄입니다.

**설명**: 포털도 응용 프로그램이므로 다른 응용 프로그램과 마찬가지로 DocumentDB 데이터베이스와 컬렉션을 호출합니다. 다른 응용 프로그램에서 수행하는 호출로 인해 현재 요청이 제한되고 있다면 포털도 제한될 수 있으므로 리소스가 포털에 표시되지 않습니다. 이 문제를 해결하려면 처리량이 많이 사용되는 원인을 해결한 후에 포털 블레이드를 새로 고칩니다. 처리량 사용을 측정하고 줄이는 방법에 대한 자세한 내용은 [성능 팁](documentdb-performance-tips.md) 문서의 [처리량](documentdb-performance-tips.md#throughput) 섹션에 나와 있습니다.
 
## 페이지 또는 블레이드가 로드되지 않음

**증상**: 포털의 페이지와 블레이드가 표시되지 않습니다.

**해결 방법**: 컬렉션의 최대 처리량 할당량보다 낮은 처리량에서 작동하도록 응용 프로그램 사용량을 줄입니다.

**설명**: 포털도 응용 프로그램이므로 다른 응용 프로그램과 마찬가지로 DocumentDB 데이터베이스와 컬렉션을 호출합니다. 다른 응용 프로그램에서 수행하는 호출로 인해 현재 요청이 제한되고 있다면 포털도 제한될 수 있으므로 리소스가 포털에 표시되지 않습니다. 이 문제를 해결하려면 처리량이 많이 사용되는 원인을 해결한 후에 포털 블레이드를 새로 고칩니다. 처리량 사용을 측정하고 줄이는 방법에 대한 자세한 내용은 [성능 팁](documentdb-performance-tips.md) 문서의 [처리량](documentdb-performance-tips.md#throughput) 섹션에 나와 있습니다.

## 컬렉션 추가 단추를 사용할 수 없음

**증상**: 데이터베이스 블레이드에서 **컬렉션 추가** 단추를 사용할 수 없습니다.

**설명**: Azure 구독이 MSDN 구독에서 제공되는 무료 크레딧 등의 혜택 크레딧에 연결되어 있는데 해당 월의 크레딧을 모두 사용한 경우에는 DocumentDB에서 추가 컬렉션을 만들 수 없습니다.

**해결 방법**: 계정에서 지출 한도를 제거합니다.

1. 이렇게 하려면 Azure 포털의 이동 표시줄에서 **구독**을 클릭하고 DocumentDB 데이터베이스와 연결된 구독을 클릭한 후에 **구독** 블레이드에서 **관리**를 클릭합니다. ![DocumentDB는 선택 가능한 여러 개의 잘 정의된(관대한) 일관성 모델을 제공합니다.](./media/documentdb-portal-troubleshooting/documentdb-change-billing.png)

2. 새 브라우저 창에 남은 크레딧이 없다고 표시됩니다. **지출 한도 제거** 단추를 클릭하여 현재 청구 기간에 한해 또는 무기한으로 지출 한도를 제거합니다. 그런 후에 마법사의 단계를 완료하여 신용 카드 정보를 추가하거나 확인합니다. ![DocumentDB는 선택 가능한 여러 개의 잘 정의된(관대한) 일관성 모델을 제공합니다.](./media/documentdb-portal-troubleshooting/documentdb-remove-spending-limit.png)

 
## 쿼리 탐색기 완료 시 오류 발생

[쿼리 탐색기 문제 해결](documentdb-query-collections-query-explorer.md#troubleshoot)을 참조하세요.

## 모니터링 타일에서 데이터를 사용할 수 없음

[모니터링 타일 문제 해결](documentdb-monitor-accounts.md#troubleshooting)을 참조하세요.

## 문서 탐색기에서 문서가 반환되지 않음

[문서 탐색기 문제 해결](documentdb-view-json-document-explorer.md#troubleshoot)을 참조하세요.

## 다음 단계

포털에서 문제가 계속 발생하는 경우에는 [askdocdb@microsoft.com](mailto:askdocdb@microsoft.com)으로 이메일을 보내 지원을 요청하거나 **찾아보기**, **도움말 + 지원**, **지원 요청 만들기**를 차례로 클릭하여 포털에서 지원 요청을 접수하시기 바랍니다.

<!---HONumber=AcomDC_0831_2016-->