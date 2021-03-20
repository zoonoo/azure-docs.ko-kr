---
title: Azure Cloud Services (확장 지원) 역할 스키마 | Microsoft Docs
description: Cloud Services에 대 한 역할 스키마와 관련 된 정보 (확장 지원)
ms.topic: article
ms.service: cloud-services-extended-support
ms.date: 10/14/2020
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 2567f5bb817a34f6274d5e265a266d67a9c81413
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98744448"
---
# <a name="azure-cloud-services-extended-support-config-role-schema"></a>Azure Cloud Services (확장 지원) 구성 역할 스키마

구성 파일의 `Role` 요소는 서비스의 각 역할에 대해 배포할 역할 인스턴스의 수, 구성 설정의 값 및 역할에 연결된 인증서의 지문을 지정합니다.

Azure 서비스 구성 스키마에 대 한 자세한 내용은 [클라우드 서비스 (확장 지원) 구성 스키마](schema-cscfg-file.md)를 참조 하세요. Azure 서비스 정의 스키마에 대 한 자세한 내용은 [클라우드 서비스 (확장 지원) 정의 스키마](schema-csdef-file.md)를 참조 하세요.

##  <a name="role-element"></a><a name="Role"></a> role 요소
다음 예제는 `Role` 요소와 해당 자식 요소를 보여 줍니다.

```xml 
<ServiceConfiguration>
  <Role name="<role-name>" vmName="<vm-name>">
    <Instances count="<number-of-instances>"/>
    <ConfigurationSettings>
      <Setting name="<setting-name>" value="<setting-value>" />
    </ConfigurationSettings>
    <Certificates>
      <Certificate name="<certificate-name>" thumbprint="<certificate-thumbprint>" thumbprintAlgorithm="<algorithm>"/>
    </Certificates>
  </Role>
</ServiceConfiguration>
```

다음 표에서는 `Role` 요소의 특성을 설명합니다.

| 특성 | 설명 |
| --------- | ----------- |
| name   | 필수 요소. 역할 이름을 지정합니다. 이름은 서비스 정의 파일에서 역할에 대해 제공된 이름과 일치해야 합니다.|
| vmName | 선택 사항입니다. Virtual Machine에 대한 DNS 이름을 지정합니다. 이름은 10자 이하여야 합니다.|

다음 테이블에서는 `Role` 요소의 자식 요소에 대해 설명합니다.

| 요소 | 설명 |
| ------- | ----------- |
| 인스턴스 | 필수 요소. 역할에 대해 배포할 인스턴스 수를 지정합니다. 인스턴스의 수는 `count` 특성에 대한 정수로 정의됩니다.|
| 설정   | 선택 사항입니다. 역할에 대한 설정의 컬렉션에서 설정 이름 및 값을 지정합니다. 설정 이름은 `name` 특성에 대한 문자열로 정의되며, 설정 값은 `value` 특성에 대한 문자열로 정의됩니다.|
| 인증서 | 선택 사항입니다. 역할에 연결될 서비스 인증서의 이름, 지문 및 알고리즘을 지정합니다. 인증서 이름은 `name` 특성에 대한 문자열로 정의됩니다. 인증서 지문은 공백을 포함하지 않고 `thumbprint` 특성에 대한 16진수 숫자의 문자열로 정의됩니다. 16진수 숫자는 숫자 및 알파벳 대문자를 사용해야 합니다. 인증서 알고리즘은 `thumbprintAlgorithm` 특성에 대한 문자열로 정의됩니다.|

## <a name="see-also"></a>참고 항목
[클라우드 서비스 (확장 지원) 구성 스키마](schema-cscfg-file.md).