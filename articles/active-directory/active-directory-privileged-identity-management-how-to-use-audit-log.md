<properties
   pageTitle="Azure 권한 있는 ID 관리: 감사 로그를 사용하는 방법"
   description="Azure 권한 있는 ID 관리 확장에서 감사 로그를 사용하는 방법을 알아봅니다."
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="na"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="01/21/2016"
   ms.author="kgremban"/>

# Azure Privileged Identity Management: 감사 로그를 사용하는 방법

Azure Privileged Identity Management(PIM) 감사 로그를 사용하면 지정된 기간 내의 모든 사용자 할당 및 활성화를 볼 수 있습니다.

## 감사 로그로 이동
PIM 대시보드에서 **Audit history**(감사 기록)을 클릭하여 감사 로그에 액세스할 수 있습니다.

## 감사 로그 그래프
감사 로그를 사용하여 전체 활성화, 일일 최대 활성화, 일일 평균 활성화를 선 그래프로 보도록 사용할 수 있습니다. 감사 기록에 역할이 둘 이상인 경우 역할별로 데이터를 필터링할 수도 있습니다.

**time**(시간), **action**(작업) 또는 **role**(역할) 단추를 사용하여 시간, 작업 또는 역할을 기준으로 정렬합니다.

## 감사 로그 목록
감사 로그 목록의 열은 다음과 같습니다.

- **Requestor** - 역할 활성화를 요청한 사람.
- **User** - 역할 활성화의 사용자.
- **Role** - 사용자에게 할당된 역할.
- **Action** - 역할/사용자로 수행된 작업.
- **Time** - 작업이 발생한 시간.
- **Reasoning** - 활성화 중에 reason(이유) 필드에 입력한 텍스트가 있는 경우 여기에 표시됩니다.
- **Expiration** - 만료되는 연도가 9999인 경우 사용자는 영구적으로 역할을 갖습니다.

## 감사 로그 필터링

**필터** 단추를 사용하여 감사 로그에 표시되는 정보를 필터링할 수 있습니다. **Update chart parameters blade**(차트 매개 변수 업데이트 블레이드)가 표시됩니다.

### 날짜 범위 변경
**Today**(오늘), **Past Week**(지난 주), **Past Month**(지난 달), **Custom**(사용자 지정) 단추 중 하나를 선택하여 감사 로그의 시간 범위를 변경합니다. **Custom**(사용자 지정) 단추를 선택하면 로그에 대해 원하는 날짜 범위를 지정하도록 **From**(시작) 날짜 필드와 **To**(끝) 날짜 필드가 제공됩니다. MM/DD/YYYY 형식으로 날짜를 입력하거나 **달력** 아이콘을 클릭하여 달력에서 날짜를 선택할 수 있습니다.

### 로그에 포함된 역할 변경

로그에 포함시키거나 제외시키려는 각 역할의 옆에 있는 **역할** 확인란을 선택 또는 선택 해제합니다.

감사 로그 세트에 대한 필터가 모두 준비되면 업데이트를 클릭하여 로그의 데이터를 필터링합니다. 데이터가 바로 표시되지 않으면 페이지를 새로 고칩니다.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 다음 단계
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0128_2016-->