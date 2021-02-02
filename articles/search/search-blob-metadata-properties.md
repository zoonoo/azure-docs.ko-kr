---
title: 콘텐츠 메타 데이터 속성
titleSuffix: Azure Cognitive Search
description: Blob의 메타 데이터 속성은 검색 인덱스의 필드에 콘텐츠를 제공 하거나 런타임에 인덱싱 동작을 알려주는 정보를 제공할 수 있습니다. 이 문서에서는 Azure Cognitive Search에서 지원 되는 메타 데이터 속성을 나열 합니다.
manager: nitinme
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/03/2021
ms.openlocfilehash: 79443785dbd8619e22358631c9c36a3da4ef2e84
ms.sourcegitcommit: 445ecb22233b75a829d0fcf1c9501ada2a4bdfa3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99477306"
---
# <a name="content-metadata-properties-used-in-blob-indexing-in-azure-cognitive-search"></a>Azure Cognitive Search의 blob 인덱싱에 사용 되는 콘텐츠 메타 데이터 속성

Blob은 다양 한 콘텐츠를 포함할 수 있으며, 대부분의 콘텐츠 형식에는 blob 인덱싱에 유용할 수 있는 메타 데이터 속성이 있습니다. 와 같은 표준 blob 속성에 대 한 검색 필드를 만드는 것 처럼 **`metadata_storage_name`** 문서 형식과 관련 된 메타 데이터 속성에 대 한 필드를 만들 수 있습니다.

## <a name="supported-document-formats"></a>지원되는 문서 형식

Cognitive Search는 다음 문서 형식에 대 한 blob 인덱싱을 지원 합니다.

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="properties-by-document-format"></a>문서 형식의 속성

다음 표에서는 각 문서 형식에 대 한 처리 작업을 요약 하 고 blob 인덱서에 의해 추출 된 메타 데이터 속성을 설명 합니다.

| 문서 형식/콘텐츠 형식 | 추출 된 메타 데이터 | 처리 세부 정보 |
| --- | --- | --- |
| HTML (텍스트/html) |`metadata_content_encoding`<br/>`metadata_content_type`<br/>`metadata_language`<br/>`metadata_description`<br/>`metadata_keywords`<br/>`metadata_title` |HTML 태그를 제거하고 텍스트 추출 |
| PDF (응용 프로그램/pdf) |`metadata_content_type`<br/>`metadata_language`<br/>`metadata_author`<br/>`metadata_title` |포함된 문서를 비롯한 텍스트 추출(이미지 제외) |
| DOCX(application/vnd.openxmlformats-officedocument.wordprocessingml.document) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |포함된 문서를 비롯한 텍스트 추출 |
| DOC(application/msword) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |포함된 문서를 비롯한 텍스트 추출 |
| .DOCM (application/vnd.ms-word.document macroenabled) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |포함된 문서를 비롯한 텍스트 추출 |
| WORD XML (application/vnd word2006ml) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |XML 태그를 제거하고 텍스트 추출 |
| WORD 2003 XML (application/vnd. ms-wordml) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date` |XML 태그를 제거하고 텍스트 추출 |
| XLSX(application/vnd.openxmlformats-officedocument.spreadsheetml.sheet) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |포함된 문서를 비롯한 텍스트 추출 |
| XLS(application/vnd.ms-excel) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |포함된 문서를 비롯한 텍스트 추출 |
| .XLSM (application/vnd. vnd.ms-excel. macroenabled) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |포함된 문서를 비롯한 텍스트 추출 |
| PPTX(application/vnd.openxmlformats-officedocument.presentationml.presentation) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |포함된 문서를 비롯한 텍스트 추출 |
| PPT(application/vnd.ms-powerpoint) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |포함된 문서를 비롯한 텍스트 추출 |
| PPTM (application/vnd. ms-powerpoint macroenabled) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |포함된 문서를 비롯한 텍스트 추출 |
| MSG(application/vnd.ms-outlook) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_from_email`<br/>`metadata_message_to`<br/>`metadata_message_to_email`<br/>`metadata_message_cc`<br/>`metadata_message_cc_email`<br/>`metadata_message_bcc`<br/>`metadata_message_bcc_email`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_subject` |첨부 파일에서 추출한 텍스트를 포함 하는 텍스트를 추출 합니다. `metadata_message_to_email``metadata_message_cc_email`및 `metadata_message_bcc_email` 는 문자열 컬렉션입니다. 나머지 필드는 문자열입니다.|
| ODT (application/vnd. oasis) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |포함된 문서를 비롯한 텍스트 추출 |
| ODS (application/vnd. oasis) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |포함된 문서를 비롯한 텍스트 추출 |
| ODP (application/vnd. oasis) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`title` |포함된 문서를 비롯한 텍스트 추출 |
| ZIP(application/zip) |`metadata_content_type` |보관 파일의 모든 문서에서 텍스트 추출 |
| RELEASE.TAR.GZ (응용 프로그램/gzip) |`metadata_content_type` |보관 파일의 모든 문서에서 텍스트 추출 |
| EPUB (application/EPUB + zip) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_title`<br/>`metadata_description`<br/>`metadata_language`<br/>`metadata_keywords`<br/>`metadata_identifier`<br/>`metadata_publisher` |보관 파일의 모든 문서에서 텍스트 추출 |
| XML(application/xml) |`metadata_content_type`<br/>`metadata_content_encoding`<br/> |XML 태그를 제거하고 텍스트 추출 |
| JSON(application/json) |`metadata_content_type`<br/>`metadata_content_encoding` |텍스트 추출<br/>참고: JSON BLOB에서 여러 문서 필드를 추출해야 하는 경우 자세한 내용은 [JSON BLOB 인덱싱](search-howto-index-json-blobs.md)을 참조하세요. |
| EML(메시지/rfc822) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_creation_date`<br/>`metadata_subject` |첨부 파일을 비롯한 텍스트 추출 |
| RTF(application/rtf) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_page_count`<br/>`metadata_word_count`<br/> | 텍스트 추출|
| 일반 텍스트(text/plain) |`metadata_content_type`<br/>`metadata_content_encoding`<br/> | 텍스트 추출|

## <a name="see-also"></a>참고 항목

* [Azure Cognitive Search의 인덱서](search-indexer-overview.md)
* [AI를 사용 하 여 blob 이해](search-blob-ai-integration.md)
* [Blob 인덱싱 개요](search-blob-storage-integration.md)