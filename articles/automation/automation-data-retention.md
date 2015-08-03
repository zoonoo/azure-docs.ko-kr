<properties 
   pageTitle="Azure 자동화 데이터 보존"
   description="Azure 자동화에 대한 데이터 보존 정책을 설명합니다."
   services="automation"
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/13/2015"
   ms.author="bwren" />

# Azure 자동화 데이터 보존

Azure 자동화에서 리소스를 삭제할 경우 영구적으로 제거하기 전에 감사를 위해 90일간 보존됩니다. 이 기간 동안에는 리소스를 보거나 사용할 수 없습니다. 이 정책은 삭제되는 자동화 계정에 속한 리소스에도 적용됩니다.

Azure 자동화는 90일이 넘은 작업을 자동으로 삭제하고 영구적으로 제거합니다.

다음 표에는 여러 리소스에 대한 보존 정책이 요약되어 있습니다.

|Data|정책|
|:---|:---|
|계정|사용자가 계정을 삭제한 후 90일이 지나면 영구적으로 제거됩니다.|
|자산|사용자가 자산을 삭제한 후 90일이 지나거나 사용자가 자산을 유지하는 계정을 삭제한 후 90일이 지나면 영구적으로 제거됩니다.|
|모듈|사용자가 모듈을 삭제한 후 90일이 지나거나 사용자가 모듈을 유지하는 계정을 삭제한 후 90일이 지나면 영구적으로 제거됩니다.|
|Runbook|사용자가 리소스를 제거한 후 90일이 지나거나 사용자가 리소스를 유지하는 계정을 삭제한 후 90일이 지나면 영구적으로 제거됩니다.|
|작업|마지막으로 수정한지 90일이 지나면 삭제 및 영구 제거됩니다. 이는 작업이 완료되거나 중지되거나 일시 중단된 이후일 수 있습니다.|

보존 정책은 모든 사용자에게 적용되며 지금은 사용자 지정할 수 없습니다.

## 관련된 문서
- [Azure 자동화 백업](https://msdn.microsoft.com/library/dn643635.aspx) 

<!---HONumber=July15_HO4-->