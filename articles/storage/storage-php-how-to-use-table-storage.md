<properties 
	pageTitle="PHP에서 테이블 저장소를 사용하는 방법 | Microsoft Azure&quot;" 
	description="PHP의 테이블 서비스를 사용하여 테이블을 작성 및 삭제하고 테이블을 삽입하고 삭제하고 쿼리하는 방법에 대해 알아봅니다." 
	services="storage" 
	documentationCenter="php" 
	authors="tfitzmac" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="03/11/2015" 
	ms.author="tomfitz"/>


# PHP에서 테이블 저장소를 사용하는 방법

[AZURE.INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]

## 개요

이 가이드에서는 Azure 테이블 서비스를 사용하여 일반 시나리오를 수행하는 방법을 보여 줍니다. 샘플은 PHP로 작성되었으며 [PHP용 Azure SDK][download]를 사용합니다. **테이블 만들기 및 삭제, 테이블에서 엔터티 삽입, 삭제 및 쿼리** 등의 시나리오를 다룹니다. Azure 테이블 서비스에 대한 자세한 내용은 [다음 단계](#NextSteps) 섹션을 참조하세요.

[AZURE.INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## PHP 응용 프로그램 만들기

Azure 테이블 서비스에 액세스하는 PHP 응용 프로그램을 만들기 위해 충족해야 할 유일한 요구 사항은 코드 내에서 PHP용 Azure SDK의 클래스를 참조하는 것입니다. 응용 프로그램을 만드는 데는 메모장을 포함한 어떠한 개발 도구도 사용할 수 있습니다.

이 가이드에서는 PHP 응용 프로그램 내에서 로컬로 또는 Azure 웹 역할, 작업자 역할 또는 웹 사이트 내에서 실행되는 코드에서 호출할 수 있는 테이블 서비스 기능을 사용합니다.

## Azure 클라이언트 라이브러리 가져오기

[AZURE.INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## 응용 프로그램에서 테이블 서비스에 액세스하도록 구성

Azure 테이블 서비스 API를 사용하려면 다음을 수행해야 합니다.

1. [require_once][require_once] 문을 사용하여 자동 로더 파일 참조
2. 사용할 수 있는 모든 클래스 참조

다음 예제에서는 자동 로더 파일을 포함하고 **ServicesBuilder** 클래스를 참조하는 방법을 보여 줍니다.

> [AZURE.NOTE]이 예제 및 이 문서의 다른 예제에서는 Azure용 PHP 클라이언트 라이브러리를 작성기를 통해 설치했다고 가정합니다. 라이브러리를 수동으로 또는 PEAR 패키지로 설치한 경우 <code>WindowsAzure.php</code> 자동 로더 파일을 참조해야 합니다.

	require_once 'vendor\autoload.php';
	use WindowsAzure\Common\ServicesBuilder;


아래 예제에서 `require_once`문은 항상 표시되지만 예제를 실행하는 데 필요한 클래스만 참조됩니다.

## Azure 저장소 연결 설정

Azure 테이블 서비스 클라이언트를 인스턴스화하려면 먼저 유효한 연결 문자열이 있어야 합니다. 테이블 서비스 연결 문자열 형식은 다음과 같습니다.

Live 서비스에 액세스하는 경우:

	DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]

에뮬레이터 저장소에 액세스하는 경우:

	UseDevelopmentStorage=true


Azure 서비스 클라이언트를 만들려면 **ServicesBuilder** 클래스를 사용해야 합니다. 다음을 수행할 수 있습니다.

* 연결 문자열을 직접 전달할 수 있습니다.
* **CloudConfigurationManager(CCM)**를 사용하여 여러 외부 소스에서 연결 문자열을 확인할 수 있습니다.
	* 기본적으로 하나의 외부 소스, 환경 변수에 대한 지원이 제공됩니다.
	* **ConnectionStringSource** 클래스를 확장하여 새 소스를 추가할 수 있습니다.

여기에 설명된 예제의 경우 연결 문자열이 직접 전달됩니다.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;

	$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);


## 방법: 테이블 만들기

**TableRestProxy** 개체를 통해 **createTable** 메서드를 사용하여 테이블을 만들 수 있습니다. 테이블을 만드는 중에 테이블 서비스 제한 시간을 설정할 수 있습니다. 테이블 서비스 제한 시간에 대한 자세한 내용은 [테이블 서비스 작업의 제한 시간 설정][table-service-timeouts]을 참조하세요.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create table REST proxy.
	$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

	try	{
		// Create table.
		$tableRestProxy->createTable("mytable");
	}
	catch(ServiceException $e){
		$code = $e->getCode();
		$error_message = $e->getMessage();
		// Handle exception based on error codes and messages.
		// Error codes and messages can be found here: 
		// http://msdn.microsoft.com/library/azure/dd179438.aspx
	}

테이블 이름의 제한 사항에 대한 자세한 내용은 [테이블 서비스 데이터 모델 이해][table-data-model]를 참조하십시오.

## 방법: 테이블에 엔티티 추가

테이블에 엔터티를 추가하려면 새 **Entity** 개체를 만들어 **TableRestProxy->insertEntity**에 전달합니다. 엔터티를 만들 때 `PartitionKey`및`RowKey`를 지정해야 한다는 점에 주의하세요. 이 두 키는 엔터티의 고유한 식별자이며, 다른 엔터티 속성보다 훨씬 더 빠르게 쿼리할 수 있는 값입니다. 시스템은 `PartitionKey`를 사용하여 여러 저장소 노드를 통해 테이블의 엔터티를 자동으로 분산합니다. 동일한`PartitionKey`의 엔터티는 동일한 노드에 저장됩니다. 동일한 노드에 저장된 여러 엔터티에 대해 작업을 수행하는 것이 여러 노드에 걸쳐 저장된 엔터티에 대해 작업을 수행하는 것보다 성능 면에서 우수합니다. `RowKey`는 파티션 내에서 엔터티의 고유한 ID입니다.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\Table\Models\Entity;
	use WindowsAzure\Table\Models\EdmType;

	// Create table REST proxy.
	$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);
	
	$entity = new Entity();
	$entity->setPartitionKey("tasksSeattle");
	$entity->setRowKey("1");
	$entity->addProperty("Description", null, "Take out the trash.");
	$entity->addProperty("DueDate", 
						 EdmType::DATETIME, 
						 new DateTime("2012-11-05T08:15:00-08:00"));
	$entity->addProperty("Location", EdmType::STRING, "Home");
	
	try{
		$tableRestProxy->insertEntity("mytable", $entity);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/azure/dd179438.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
	}

테이블 속성 및 유형에 대한 자세한 내용은 [테이블 서비스 데이터 모델 이해][table-data-model]를 참조하십시오.

**TableRestProxy** 클래스는 엔터티 삽입을 위한 2 개의 대체 메서드를 제공합니다: **insertOrMergeEntity** 및 **insertOrReplaceEntity**. 이러한 메서드를 사용하려면 새 **Entity**를 만들어 두 메서드 중 하나에 매개 변수로 전달합니다. 각 메서드는 엔터티가 없을 경우 해당 엔터티를 삽입합니다. 엔터티가 이미 있을 경우, **insertOrMergeEntity**는 속성이 이미 있으면 속성 값을 업데이트하고 속성이 없으면 새 속성을 추가하지만 **insertOrReplaceEntity**는 기존 엔터티를 완전히 바꿉니다. 다음 예제에서는 **insertOrMergeEntity**를 사용하는 방법을 보여 줍니다. `PartitionKey`"tasksSeattle”과 `RowKey`"1"인 엔터티가 아직 없으면 이 엔터티가 삽입됩니다. 그러나 위의 예제와 같이 엔터티가 이미 삽입되어 있으면 `DueDate`속성이 업데이트되고 `Status`속성이 추가됩니다. `Description`및`Location` 속성도 업데이트되지만 실질적으로 변경하지 않은 상태로 유지하는 값으로 업데이트됩니다. 이 2개의 속성이 예제와 같이 추가되지는 않지만 대상 엔터티에 이미 있는 경우, 두 속성의 기존 값은 변경되지 않습니다.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\Table\Models\Entity;
	use WindowsAzure\Table\Models\EdmType;

	// Create table REST proxy.
	$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);
	
	//Create new entity.
	$entity = new Entity();
	
	// PartitionKey and RowKey are required.
	$entity->setPartitionKey("tasksSeattle");
	$entity->setRowKey("1");
	
	// If entity exists, existing properties are updated with new values and
	// new properties are added. Missing properties are unchanged.
	$entity->addProperty("Description", null, "Take out the trash.");
	$entity->addProperty("DueDate", EdmType::DATETIME, new DateTime()); // Modified the DueDate field.
	$entity->addProperty("Location", EdmType::STRING, "Home");
	$entity->addProperty("Status", EdmType::STRING, "Complete"); // Added Status field.
	
	try	{
		// Calling insertOrReplaceEntity, instead of insertOrMergeEntity as shown,
		// would simply replace the entity with PartitionKey "tasksSeattle" and RowKey "1".
		$tableRestProxy->insertOrMergeEntity("mytable", $entity);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/azure/dd179438.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}
	   

## 방법: 단일 엔터티 검색

**TableRestProxy->getEntity** 메서드를 사용하면 `PartitionKey`및`RowKey`를 쿼리하여 단일 엔터티를 검색할 수 있습니다. 아래 예제에서는 파티션 키`tasksSeattle` 및 행 키`1` 이 **getEntity** 메서드에 전달됩니다.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create table REST proxy.
	$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);
	
	try	{
		$result = $tableRestProxy->getEntity("mytable", "tasksSeattle", 1);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/azure/dd179438.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}
	
	$entity = $result->getEntity();

	echo $entity->getPartitionKey().":".$entity->getRowKey();

## 방법: 파티션의 모든 엔터티 검색

엔터티 쿼리는 필터를 사용하여 구성됩니다(자세한 내용은 [테이블 및 엔터티 쿼리][filters] 참조). 파티션의 모든 엔터티를 검색하려면 "PartitionKey eq *partition_name*" 필터를 사용합니다. 다음 예제에서는 필터를 **queryEntities** 메서드에 전달하여 `tasksSeattle`파티션의 모든 엔터티를 검색하는 방법을 보여 줍니다.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create table REST proxy.
	$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);
	
	$filter = "PartitionKey eq 'tasksSeattle'";
	
	try	{
		$result = $tableRestProxy->queryEntities("mytable", $filter);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/azure/dd179438.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}
	
	$entities = $result->getEntities();
	
	foreach($entities as $entity){
		echo $entity->getPartitionKey().":".$entity->getRowKey()."<br />";
	}

## 파티션의 엔터티 하위 집합 검색하는 방법

이전 예제에서 사용된 동일한 패턴을 파티션의 엔터티 하위 집합 검색에 사용할 수 있습니다. 검색되는 엔터티 하위 집합은 사용하는 필터에 의해 결정됩니다(자세한 내용은 [Querying Tables and Entities][filters] 참고). 다음 예제에서는 필터를 사용하여 특정 값`Location`과 `DueDate`지정한 날짜보다 작은 값을 갖는 모든 엔터티를 검색하는 방법을 보여 줍니다.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create table REST proxy.
	$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);
	
	$filter = "Location eq 'Office' and DueDate lt '2012-11-5'";
	
	try	{
		$result = $tableRestProxy->queryEntities("mytable", $filter);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/azure/dd179438.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}
	
	$entities = $result->getEntities();
	
	foreach($entities as $entity){
		echo $entity->getPartitionKey().":".$entity->getRowKey()."<br />";
	}

## 엔터티 속성 하위 집합 검색하는 방법

쿼리를 통해 엔터티 속성 하위 집합을 검색할 수 있습니다. *프로젝션*이라고 하는 이 기술은 특히 대역폭을 줄이며 큰 엔터티에 대한 쿼리 성능을 향상시킬 수 있습니다. 검색할 속성을 지정하려면 속성 이름을 **Query->addSelectField** 메서드에 전달합니다. 이 메서드를 여러 번 호출하여 더 많은 속성을 추가할 수 있습니다. **TableRestProxy->queryEntities**를 실행한 후에 반환된 엔터티에는 선택한 속성만 있게 됩니다. 테이블 엔터티 하위 집합을 반환하려는 경우에는 위의 쿼리와 같이 필터를 사용합니다.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\Table\Models\QueryEntitiesOptions;

	// Create table REST proxy.
	$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);
	
	$options = new QueryEntitiesOptions();
	$options->addSelectField("Description");
	
	try	{
		$result = $tableRestProxy->queryEntities("mytable", $options);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/azure/dd179438.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}
	
	// All entities in the table are returned, regardless of whether 
	// they have the Description field.
	// To limit the results returned, use a filter.
	$entities = $result->getEntities();

	foreach($entities as $entity){
		$description = $entity->getProperty("Description")->getValue();
		echo $description."<br />";
	}

## 엔터티를 업데이트하는 방법

엔터티에 대해 **Entity->setProperty** 및 **Entity->addProperty** 메서드를 사용하고 **TableRestProxy->updateEntity**를 호출하면 기존 엔터티를 업데이트할 수 있습니다. 다음 예제에서는 엔터티를 검색한 후, 속성 1개를 수정하고 다른 속성을 제거하고 새 속성을 추가합니다. 참고로, 속성을 제거할 때는 속성 값을 **null**로 설정하면 됩니다.

	require_once 'vendor\autoload.php';
	
	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\Table\Models\Entity;
	use WindowsAzure\Table\Models\EdmType;

	// Create table REST proxy.
	$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);
	
	$result = $tableRestProxy->getEntity("mytable", "tasksSeattle", 1);
	
	$entity = $result->getEntity();
	
	$entity->setPropertyValue("DueDate", new DateTime()); //Modified DueDate.
	
	$entity->setPropertyValue("Location", null); //Removed Location.
	
	$entity->addProperty("Status", EdmType::STRING, "In progress"); //Added Status.

	try	{
		$tableRestProxy->updateEntity("mytable", $entity);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/azure/dd179438.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

## 방법: 엔티티 삭제

엔터티를 삭제하려면 테이블 이름과 엔터티의 `PartitionKey`및`RowKey`를 **TableRestProxy->deleteEntity** 메서드에 전달합니다.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create table REST proxy.
	$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);
	
	try	{
		// Delete entity.
		$tableRestProxy->deleteEntity("mytable", "tasksSeattle", "2");
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/azure/dd179438.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

동시성 검사가 필요한 경우, **DeleteEntityOptions->setEtag** 메서드를 사용하고 **DeleteEntityOptions** 개체를 네 번째 매개 변수로 **deleteEntity**에 전달하여 삭제될 엔터티에 대해 Etag를 설정할 수 있습니다.

## 배치 테이블 작업하는 방법

**TableRestProxy->batch** 메서드를 통해 단일 요청으로 여러 작업을 실행할 수 있습니다. 여기에서 사용되는 패턴은 작업을 **BatchRequest** 개체에 추가한 후, **BatchRequest** 개체를 **TableRestProxy->batch** 메서드에 전달하는 것입니다. 작업을 **BatchRequest** 개체에 추가하려면 다음 메서드 중 하나를 여러 번 호출하면 됩니다.

* **addInsertEntity**(insertEntity 작업 추가)
* **addUpdateEntity**(updateEntity 작업 추가)
* **addMergeEntity**(mergeEntity 작업 추가)
* **addInsertOrReplaceEntity**(insertOrReplaceEntity 작업 추가)
* **addInsertOrMergeEntity**(insertOrMergeEntity 작업 추가)
* **addDeleteEntity**(deleteEntity 작업 추가)

다음 예제에서는 **insertEntity** 및 **deleteEntity** 작업을 단일 요청으로 실행하는 방법을 보여 줍니다.

	require_once 'vendor\autoload.php';
	
	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\Table\Models\Entity;
	use WindowsAzure\Table\Models\EdmType;
	use WindowsAzure\Table\Models\BatchOperations;

 	// Create table REST proxy.
	$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);
	
	// Create list of batch operation.
	$operations = new BatchOperations();
	
	$entity1 = new Entity();
	$entity1->setPartitionKey("tasksSeattle");
	$entity1->setRowKey("2");
	$entity1->addProperty("Description", null, "Clean roof gutters.");
	$entity1->addProperty("DueDate", 
						  EdmType::DATETIME, 
						  new DateTime("2012-11-05T08:15:00-08:00"));
	$entity1->addProperty("Location", EdmType::STRING, "Home");
	
	// Add operation to list of batch operations.
    $operations->addInsertEntity("mytable", $entity1);

	// Add operation to list of batch operations.
	$operations->addDeleteEntity("mytable", "tasksSeattle", "1");
	
	try	{
		$tableRestProxy->batch($operations);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/azure/dd179438.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

테이블 일괄 작업에 대한 자세한 내용은 [엔터티 그룹 트랜잭션 수행][entity-group-transactions]을 참조하십시오.

## 방법: 테이블 삭제

끝으로, 테이블을 삭제하려면 테이블 이름을 **TableRestProxy->deleteTable** 메서드에 전달합니다.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create table REST proxy.
	$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);
	
	try	{
		// Delete table.
		$tableRestProxy->deleteTable("mytable");
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/azure/dd179438.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

## 다음 단계

이제 Azure 테이블 서비스의 기본 사항을 배웠으므로 다음 링크를 따라 좀 더 복잡한 저장소 작업에 대해 알아보세요.

- 다음 MSDN 참조를 확인하세요. [Azure 저장소](http://msdn.microsoft.com/library/azure/gg433040.aspx)
- [Azure 저장소 팀 블로그](http://blogs.msdn.com/b/windowsazurestorage/)(영문)를 방문하세요.

[download]: http://go.microsoft.com/fwlink/?LinkID=252473
[Storing and Accessing Data in Azure]: http://msdn.microsoft.com/library/azure/gg433040.aspx
[require_once]: http://php.net/require_once
[table-service-timeouts]: http://msdn.microsoft.com/library/azure/dd894042.aspx

[table-data-model]: http://msdn.microsoft.com/library/azure/dd179338.aspx
[filters]: http://msdn.microsoft.com/library/azure/dd894031.aspx
[entity-group-transactions]: http://msdn.microsoft.com/library/azure/dd894038.aspx
 

<!---HONumber=July15_HO2-->