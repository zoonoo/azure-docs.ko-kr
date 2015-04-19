<properties 
	pageTitle="모바일 서비스 .NET 백 엔드가 포함된 기존 SQL 데이터베이스를 사용해서 서비스 구축 - Azure 모바일 서비스" 
	description=".NET 기반 모바일 서비스에서 기존 클라우드 또는 온-프레미스 SQL 데이터베이스를 사용하는 방법에 대해 알아봅니다." 
	services="mobile-services, biztalk-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor="mollybos"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-multiple" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="11/22/2014" 
	ms.author="glenga"/>


# 모바일 서비스 .NET 백 엔드가 포함된 기존 SQL 데이터베이스를 사용해서 서비스 구축

모바일 서비스 .NET 백 엔드를 사용하면 모바일 서비스를 구축할 때 기존 자산을 쉽게 활용할 수 있습니다. 특히 한 가지 흥미로운 시나리오는 다른 응용 프로그램에서 이미 사용 중일 수 있는 기존의 SQL 데이터베이스(온-프레미스 또는 클라우드)를 사용해서 기존 데이터를 모바일 클라이언트에서 도 사용할 수 있다는 점입니다. 이 경우에는 기존 솔루션이 계속 작동할 수 있도록 데이터베이스 모델(또는  *schema*)이 변경되지 않은 상태로 유지되어야 합니다.

이 자습서는 다음과 같은 섹션으로 구성되어 있습니다.

1. [기존 데이터베이스 모델 내보내기](#ExistingModel)
2. [모바일 서비스를 위한 DTO(데이터 전송 개체) 만들기](#DTOs)
3. [DTO와 모델 간 매핑 설정](#Mapping)
4. [도메인별 논리 구현](#DomainManager)
5. [DTO를 사용하여 TableController 구현](#Controller)

<a name="ExistingModel"></a>
## 기존 데이터베이스 모델 내보내기

이 자습서에서는 모바일 서비스에서 생성된 데이터베이스를 사용하지만 생성된 기본 모델을 사용하지 않습니다. 대신 사용자가 이미 갖고 있을 수 있는 기존 응용 프로그램을 나타내는 임의의 모델을 수동으로 만듭니다. 대신 온-프레미스 데이터베이스에 연결하는 방법에 대한 자세한 내용은 [하이브리드 연결을 사용하여 Azure 모바일 서비스에서 온-프레미스 SQL Server에 연결](mobile-services-dotnet-backend-hybrid-connections-get-started.md)을 참조하세요.

1. 시작하려면 **Visual Studio 2013 업데이트 2**에서 모바일 서비스 서버 프로젝트를 만들거나 [Azure 관리 포털](http://manage.windowsazure.com)의 모바일 서비스 탭에서 다운로드할 수 있는 퀵 스타트 프로젝트를 사용합니다. 이 자습서에서는 서버 프로젝트 이름이 **ShoppingService**라고 가정합니다.

2. **Models** 폴더 안에 **Customer.cs** 파일을 만들고 다음 구현을 사용합니다. **System.ComponentModel.DataAnnotations**에 대한 어셈블리 참조를 프로젝트에 추가해야 합니다.

        using System.Collections.Generic;
        using System.ComponentModel.DataAnnotations;

        namespace ShoppingService.Models
        {
            public class Customer
            {
                [Key]
                public int CustomerId { get; set; }
                
                public string Name { get; set; }

                public virtual ICollection<Order> Orders { get; set; }

            }
        }

3. **Models** 폴더 안에 **Order.cs** 파일을 만들고 다음 구현을 사용합니다.
    
        using System.ComponentModel.DataAnnotations;

        namespace ShoppingService.Models
        {
            public class Order
            {
                [Key]
                public int OrderId { get; set; }

                public string Item { get; set; }

                public int Quantity { get; set; }

                public bool Completed { get; set; }

                public int CustomerId { get; set; }
              
                public virtual Customer Customer { get; set; }

            }
        }

    이러한 두 클래스에서 모든 **Order**의  *relationship*:이 단일 **Customer**에 연결되어 있고 단일 **Customer**를 여러 **Orders**에 연결할 수 있습니다. 관계 설정은 기존의 데이터 모델에서 도 일반적인 특성입니다.

4. **Models** 폴더 안에 **ExistingContext.cs** 파일을 만들고 다음과 같이 구현합니다.

        using System.Data.Entity;

        namespace ShoppingService.Models
        {
            public class ExistingContext : DbContext
            {
                public ExistingContext()
                    : base("Name=MS_TableConnectionString")
                {
                }

                public DbSet<Customer> Customers { get; set; }

                public DbSet<Order> Orders { get; set; }

            }
        }

위 구조는 기존 응용 프로그램에 이미 사용 중일 수 있는 기존의 Entity Framework 모델과 비슷하게 만들어졌습니다. 이 단계에서 이 모델은 어떠한 방식으로도 모바일 서비스를 인지하지 못합니다. 

<a name="DTOs"></a>
## 모바일 서비스를 위한 DTO(데이터 전송 개체) 만들기

모바일 서비스에 사용하려는 데이터 모델은 임의의 복합성을 갖습니다. 즉, 수백 개의 엔터티를 포함하며, 이들 간에 다양한 관계가 존재할 수 있습니다. 모바일 앱을 작성할 때는 일반적으로 앱과 서비스 간에 전송되는 페이로드를 최소화하기 위해 데이터 모델을 단순화하고 관계를 제거(하거나 수동으로 처리)하는 것이 좋습니다. 이 섹션에서는 데이터베이스에 있는 데이터에 매핑되어 있지만 모바일 앱에 필요한 최소한의 속성 집합만 포함하는 간소화된 개체 집합("데이터 전송 개체" 또는 "DTO")을 만듭니다.

1. 서비스 프로젝트의 **DataObjects** 폴더 안에 **MobileCustomer.cs** 파일을 만들고 다음 구현을 사용합니다.

        using Microsoft.WindowsAzure.Mobile.Service;

        namespace ShoppingService.DataObjects
        {
            public class MobileCustomer : EntityData
            {
                public string Name { get; set; }
            }
        }

    이 클래스는 모델에 있는 **Customer** 클래스와 비슷하지만 **Order**에 대한 관계 속성이 제거되어 있는 것이 다릅니다. 모바일 서비스 오프라인 동기화 시 개체가 올바르게 작동하기 위해서는 낙관적 동시성을 위해  *system properties* 집합이 필요하므로 DTO가 해당 속성을 포함하는 [**EntityData**](http://msdn.microsoft.com/library/microsoft.windowsazure.mobile.service.entitydata.aspx)로부터 상속됨을 알 수 있습니다. 원본 모델의 int 기반 **CustomerId** 속성은 모바일 서비스에서 사용할 **Id**가 되는 **EntityData**의 문자열 기반 **Id** 속성으로 바뀝니다.

2. 서비스 프로젝트의 **DataObjects** 폴더에 **MobileOrder.cs** 파일을 만듭니다.

        using Microsoft.WindowsAzure.Mobile.Service;
        using Newtonsoft.Json;
        using System.ComponentModel.DataAnnotations;
        using System.ComponentModel.DataAnnotations.Schema;

        namespace ShoppingService.DataObjects
        {
            public class MobileOrder : EntityData
            {
                public string Item { get; set; }

                public int Quantity { get; set; }

                public bool Completed { get; set; }

                [NotMapped]
                public int CustomerId { get; set; }

                [Required]
                public string MobileCustomerId { get; set; }

                public string MobileCustomerName { get; set; }
            }
        }

    **Customer** 관계 속성은 클라이언트에서 관계를 수동으로 모델링하는 데 사용할 수 있는 **Customer** 이름 및 **MobileCustomerId** 속성으로 바뀌었습니다. **CustomerId** 속성은 나중에 사용되므로 지금은 무시해도 됩니다. 

3. **EntityData** 기본 클래스에 시스템 속성이 추가되어 DTO에는 이제 모델 유형보다 많은 속성이 포함되었습니다. 확실히 이러한 속성을 저장할 공간이 필요하므로, 원본 데이터베이스에 열을 몇 개 추가해야 합니다. 이렇게 하면 데이터베이스가 변경되긴 해도 변경 사항이 단순히 추가된 항목(스키마에 새로운 열 추가)이므로 기존 응용 프로그램의 작업이 중단되지는 않을 것입니다. 이를 위해서는 **Customer.cs** 및 **Order.cs** 위에 다음 문을 추가합니다.
    
        using System.ComponentModel.DataAnnotations.Schema;
        using Microsoft.WindowsAzure.Mobile.Service.Tables;
        using System.ComponentModel.DataAnnotations;
        using System;

    Then, add these extra properties to each of the classes:

        [DatabaseGenerated(DatabaseGeneratedOption.Identity)]
        [Index]
        [TableColumn(TableColumnType.CreatedAt)]
        public DateTimeOffset? CreatedAt { get; set; }

        [TableColumn(TableColumnType.Deleted)]
        public bool Deleted { get; set; }

        [Index]
        [TableColumn(TableColumnType.Id)]
        [MaxLength(36)]
        public string Id { get; set; }

        [DatabaseGenerated(DatabaseGeneratedOption.Computed)]
        [TableColumn(TableColumnType.UpdatedAt)]
        public DateTimeOffset? UpdatedAt { get; set; }

        [TableColumn(TableColumnType.Version)]
        [Timestamp]
        public byte[] Version { get; set; }

4. 지금 추가한 시스템 속성은 데이터베이스 작업에 영향을 주지 않고 수행되는 몇 가지 기본 제공되는 동작(예: 생성/업데이트 시 자동 업데이트)을 포함합니다. 이러한 동작을 사용하도록 설정하기 위해서는 **ExistingContext.cs**를 변경해야 합니다. 파일 맨 위에 다음을 추가합니다.
    
        using System.Data.Entity.ModelConfiguration.Conventions;
        using Microsoft.WindowsAzure.Mobile.Service.Tables;
        using System.Linq;

    그런 다음 **ExistingContext** 본문에서 [**OnModelCreating**](http://msdn.microsoft.com/library/system.data.entity.dbcontext.onmodelcreating.aspx)을 재정의합니다.

        protected override void OnModelCreating(DbModelBuilder modelBuilder)
        {	
            modelBuilder.Conventions.Add(
                new AttributeToColumnAnnotationConvention<TableColumnAttribute, string>(
                    "ServiceTableColumn", (property, attributes) => attributes.Single().ColumnType.ToString()));

            base.OnModelCreating(modelBuilder);
        } 

5. 이제 데이터베이스에 몇 가지 예제 데이터를 채웁니다. **WebApiConfig.cs** 파일을 엽니다. 새로운 [**IDatabaseInitializer**](http://msdn.microsoft.com/library/gg696323.aspx)를 만들고 아래 표시된 것처럼 **Register** 메서드에서 구성합니다.

        using Microsoft.WindowsAzure.Mobile.Service;
        using ShoppingService.Models;
        using System;
        using System.Collections.Generic;
        using System.Collections.ObjectModel;
        using System.Data.Entity;
        using System.Web.Http;

        namespace ShoppingService
        {
            public static class WebApiConfig
            {
                public static void Register()
                {
                    ConfigOptions options = new ConfigOptions();

                    HttpConfiguration config = ServiceConfig.Initialize(new ConfigBuilder(options));

                    Database.SetInitializer(new ExistingInitializer());
                }
            }

            public class ExistingInitializer : ClearDatabaseSchemaIfModelChanges<ExistingContext>
            {
                protected override void Seed(ExistingContext context)
                {
                    List<Order> orders = new List<Order>
                    {
                        new Order { OrderId = 10, Item = "Guitars", Quantity = 2, Id = Guid.NewGuid().ToString()},
                        new Order { OrderId = 20, Item = "Drums", Quantity = 10, Id = Guid.NewGuid().ToString()},
                        new Order { OrderId = 30, Item = "Tambourines", Quantity = 20, Id = Guid.NewGuid().ToString() }
                    };

                    List<Customer> customers = new List<Customer>
                    {
                        new Customer { CustomerId = 1, Name = "John", Orders = new Collection<Order> { 
                            orders[0]}, Id = Guid.NewGuid().ToString()},
                        new Customer { CustomerId = 2, Name = "Paul", Orders = new Collection<Order> { 
                            orders[1]}, Id = Guid.NewGuid().ToString()},
                        new Customer { CustomerId = 3, Name = "Ringo", Orders = new Collection<Order> { 
                            orders[2]}, Id = Guid.NewGuid().ToString()},
                    };

                    foreach (Customer c in customers)
                    {
                        context.Customers.Add(c);
                    }

                    base.Seed(context);
                }
            }
        }

<a name="Mapping"></a>
## DTO와 모델 간 매핑 설정

이제 **Customer** 및 **Order** 모델 유형과 **MobileCustomer** 및 **MobileOrder** DTO가 준비되었지만 백 엔드가 이 두 항목 사이의 변환을 자동화하도록 지정해야 합니다. 여기에서 모바일 서비스에는 프로젝트에 이미 참조된 개체 관계 매퍼인 [**AutoMapper**](http://automapper.org/)가 사용됩니다.

1. **WebApiConfig.cs** 맨 위에 다음을 추가합니다.

        using AutoMapper;
        using ShoppingService.DataObjects;

2. 매핑을 정의하기 위해 **WebApiConfig** 클래스의 **Register** 메서드에 다음을 추가합니다. 

        Mapper.Initialize(cfg =>
        {
            cfg.CreateMap<MobileOrder, Order>();
            cfg.CreateMap<MobileCustomer, Customer>();
            cfg.CreateMap<Order, MobileOrder>()
                .ForMember(dst => dst.MobileCustomerId, map => map.MapFrom(x => x.Customer.Id))
                .ForMember(dst => dst.MobileCustomerName, map => map.MapFrom(x => x.Customer.Name));
            cfg.CreateMap<Customer, MobileCustomer>();

        });

AutoMapper가 이제 개체를 다른 개체에 매핑합니다. 이름이 일치하는 모든 속성이 매치됩니다. 예를 들어 **MobileOrder.CustomerId**는 **Order.CustomerId**에 자동으로 매핑됩니다. 위에 표시된 것처럼 사용자 지정 매핑을 구성하여, **MobileCustomerName** 속성을 **Customer** 관계 속성의 **Name** 속성에 매핑할 수 있습니다.

<a name="DomainManager"></a>
## 도메인별 논리 구현

다음 단계에서는 매핑된 데이터 저장소와 클라이언트의 HTTP 트래픽을 처리할 컨트롤러 사이의 추상화 계층으로 작동하는 [**MappedEntityDomainManager**](http://msdn.microsoft.com/library/dn643300.aspx)를 구현합니다. 다음 섹션에서는 순수하게 DTO 측면에서 컨트롤러를 작성하고 여기에서 추가하는 **MappedEntityDomainManager**가 원본 데이터 저장소와의 통신을 처리하고, 이와 관련된 논리를 구현할 수 있는 위치를 제공할 것입니다.

1. 프로젝트의 **Models** 폴더에 **MobileCustomerDomainManager.cs**를 추가합니다. 다음 구현에 붙여 넣습니다.

        using AutoMapper;
        using Microsoft.WindowsAzure.Mobile.Service;
        using ShoppingService.DataObjects;
        using System.Data.Entity;
        using System.Linq;
        using System.Net.Http;
        using System.Threading.Tasks;
        using System.Web.Http;
        using System.Web.Http.OData;

        namespace ShoppingService.Models
        {
            public class MobileCustomerDomainManager : MappedEntityDomainManager<MobileCustomer, Customer>
            {
                private ExistingContext context;

                public MobileCustomerDomainManager(ExistingContext context, HttpRequestMessage request, ApiServices services)
                    : base(context, request, services)
                {
                    Request = request;
                    this.context = context;
                }

                public static int GetKey(string mobileCustomerId, DbSet<Customer> customers, HttpRequestMessage request)
                {
                    int customerId = customers
                       .Where(c => c.Id == mobileCustomerId)
                       .Select(c => c.CustomerId)
                       .FirstOrDefault();

                    if (customerId == 0)
                    {
                        throw new HttpResponseException(request.CreateNotFoundResponse());
                    }
                    return customerId;
                }

                protected override T GetKey<T>(string mobileCustomerId)
                {
                    return (T)(object)GetKey(mobileCustomerId, this.context.Customers, this.Request);
                }
                
                public override SingleResult<MobileCustomer> Lookup(string mobileCustomerId)
                {
                    int customerId = GetKey<int>(mobileCustomerId);
                    return LookupEntity(c => c.CustomerId == customerId);
                }

                public override async Task<MobileCustomer> InsertAsync(MobileCustomer mobileCustomer)
                {
                    return await base.InsertAsync(mobileCustomer);
                }

                public override async Task<MobileCustomer> UpdateAsync(string mobileCustomerId, Delta<MobileCustomer> patch)
                {
                    int customerId = GetKey<int>(mobileCustomerId);

                    Customer existingCustomer = await this.Context.Set<Customer>().FindAsync(customerId);
                    if (existingCustomer == null)
                    {
                        throw new HttpResponseException(this.Request.CreateNotFoundResponse());
                    }

                    MobileCustomer existingCustomerDTO = Mapper.Map<Customer, MobileCustomer>(existingCustomer);
                    patch.Patch(existingCustomerDTO);
                    Mapper.Map<MobileCustomer, Customer>(existingCustomerDTO, existingCustomer);

                    await this.SubmitChangesAsync();

                    MobileCustomer updatedCustomerDTO = Mapper.Map<Customer, MobileCustomer>(existingCustomer);

                    return updatedCustomerDTO;
                }

                public override async Task<MobileCustomer> ReplaceAsync(string mobileCustomerId, MobileCustomer mobileCustomer)
                {
                    return await base.ReplaceAsync(mobileCustomerId, mobileCustomer);
                }

                public override async Task<bool> DeleteAsync(string mobileCustomerId)
                {
                    int customerId = GetKey<int>(mobileCustomerId);
                    return await DeleteItemAsync(customerId);
                }
            }
        }

    이 클래스에서 중요한 부분은 원본 데이터 모델에서 개체의 ID 속성을 찾는 방법을 나타내는 **GetKey** 메서드입니다. 

2. 프로젝트의 **Models** 폴더에 **MobileOrderDomainManager.cs**를 추가합니다.

        using AutoMapper;
        using Microsoft.WindowsAzure.Mobile.Service;
        using ShoppingService.DataObjects;
        using System.Linq;
        using System.Net.Http;
        using System.Threading.Tasks;
        using System.Web.Http;
        using System.Web.Http.OData;

        namespace ShoppingService.Models
        {
            public class MobileOrderDomainManager : MappedEntityDomainManager<MobileOrder, Order>
            {
                private ExistingContext context;

                public MobileOrderDomainManager(ExistingContext context, HttpRequestMessage request, ApiServices services)
                    : base(context, request, services)
                {
                    Request = request;
                    this.context = context;
                }

                protected override T GetKey<T>(string mobileOrderId)
                {
                    int orderId = this.context.Orders
                        .Where(o => o.Id == mobileOrderId)
                        .Select(o => o.OrderId)
                        .FirstOrDefault();

                    if (orderId == 0)
                    {
                        throw new HttpResponseException(this.Request.CreateNotFoundResponse());
                    }
                    return (T)(object)orderId;
                }

                public override SingleResult<MobileOrder> Lookup(string mobileOrderId)
                {
                    int orderId = GetKey<int>(mobileOrderId);
                    return LookupEntity(o => o.OrderId == orderId);
                }

                private async Task<Customer> VerifyMobileCustomer(string mobileCustomerId, string mobileCustomerName)
                {
                    int customerId = MobileCustomerDomainManager.GetKey(mobileCustomerId, this.context.Customers, this.Request);
                    Customer customer = await this.context.Customers.FindAsync(customerId);
                    if (customer == null)
                    {
                        throw new HttpResponseException(Request.CreateBadRequestResponse("Customer with name '{0}' was not found", mobileCustomerName));
                    }
                    return customer;
                }

                public override async Task<MobileOrder> InsertAsync(MobileOrder mobileOrder)
                {
                    Customer customer = await VerifyMobileCustomer(mobileOrder.MobileCustomerId, mobileOrder.MobileCustomerName);
                    mobileOrder.CustomerId = customer.CustomerId;
                    return await base.InsertAsync(mobileOrder);
                }

                public override async Task<MobileOrder> UpdateAsync(string mobileOrderId, Delta<MobileOrder> patch)
                {
                    Customer customer = await VerifyMobileCustomer(patch.GetEntity().MobileCustomerId, patch.GetEntity().MobileCustomerName);

                    int orderId = GetKey<int>(mobileOrderId);

                    Order existingOrder = await this.Context.Set<Order>().FindAsync(orderId);
                    if (existingOrder == null)
                    {
                        throw new HttpResponseException(this.Request.CreateNotFoundResponse());
                    }

                    MobileOrder existingOrderDTO = Mapper.Map<Order, MobileOrder>(existingOrder);
                    patch.Patch(existingOrderDTO);
                    Mapper.Map<MobileOrder, Order>(existingOrderDTO, existingOrder);

                    // This is required to map the right Id for the customer
                    existingOrder.CustomerId = customer.CustomerId;

                    await this.SubmitChangesAsync();

                    MobileOrder updatedOrderDTO = Mapper.Map<Order, MobileOrder>(existingOrder);

                    return updatedOrderDTO;
                }

                public override async Task<MobileOrder> ReplaceAsync(string mobileOrderId, MobileOrder mobileOrder)
                {
                    await VerifyMobileCustomer(mobileOrder.MobileCustomerId, mobileOrder.MobileCustomerName);

                    return await base.ReplaceAsync(mobileOrderId, mobileOrder);
                }

                public override Task<bool> DeleteAsync(string mobileOrderId)
                {
                    int orderId = GetKey<int>(mobileOrderId);
                    return DeleteItemAsync(orderId);
                }
            }
        }

    여기에서는 각 **Order**가 올바른 연관된 **Customer**를 포함하도록 관계를 강제 적용하는 **InsertAsync** 및 **UpdateAsync** 메서드가 중요합니다. **InsertAsync**에서는 **Order.CustomerId** 속성에 매핑되는 **MobileOrder.CustomerId** 속성을 채웁니다. 이 값은 **MobileOrder.MobileCustomerId**가 일치하는 **Customer**를 조회하여 가져옵니다. 그 이유는 클라이언트가 기본적으로 **Order**의 외래 키(**MobileOrder.CustomerId**)를 **Customer**로 설정하는 데 필요한 실제 기본 키와 다른 **Customer**의 모바일 서비스 ID(**MobileOrder.MobileCustomerId**)만 인지하기 때문입니다. 이 ID는 삽입 작업을 지원하기 위해 서비스 내에서 내부적으로만 사용됩니다.

이제 DTO를 클라이언트에 제공하기 위한 컨트롤러를 만들 준비가 되었습니다.

<a name="Controller"></a>
## DTO를 사용하여 TableController 구현

1. **Controllers** 폴더에서 **MobileCustomerController.cs** 파일을 추가합니다.

        using Microsoft.WindowsAzure.Mobile.Service;
        using Microsoft.WindowsAzure.Mobile.Service.Security;
        using ShoppingService.DataObjects;
        using ShoppingService.Models;
        using System.Linq;
        using System.Threading.Tasks;
        using System.Web.Http;
        using System.Web.Http.Controllers;
        using System.Web.Http.OData;

        namespace ShoppingService.Controllers
        {
            public class MobileCustomerController : TableController<MobileCustomer>
            {
                protected override void Initialize(HttpControllerContext controllerContext)
                {
                    base.Initialize(controllerContext);
                    var context = new ExistingContext();
                    DomainManager = new MobileCustomerDomainManager(context, Request, Services);
                }

                public IQueryable<MobileCustomer> GetAllMobileCustomers()
                {
                    return Query();
                }

                public SingleResult<MobileCustomer> GetMobileCustomer(string id)
                {
                    return Lookup(id);
                }

                [AuthorizeLevel(AuthorizationLevel.Admin)]
                protected override Task<MobileCustomer> PatchAsync(string id, Delta<MobileCustomer> patch)
                {
                    return base.UpdateAsync(id, patch);
                }

                [AuthorizeLevel(AuthorizationLevel.Admin)]
                protected override Task<MobileCustomer> PostAsync(MobileCustomer item)
                {
                    return base.InsertAsync(item);
                }

                [AuthorizeLevel(AuthorizationLevel.Admin)]
                protected override Task DeleteAsync(string id)
                {
                    return base.DeleteAsync(id);
                }
            }
        }

    여기에서는 컨트롤러에서 삽입/업데이트/삭제 작업에 대한 공용 액세스를 제한하기 위해 AuthorizeLevel 특성이 사용된 것을 알 수 있습니다. 이 시나리오에서는 Customers 목록이 읽기 전용이지만 새로운 Orders를 만들고 이를 기존 고객과 연관시킬 수 있도록 허용합니다. 

2. **Controllers** 폴더에서 **MobileOrderController.cs** 파일을 추가합니다.

        using Microsoft.WindowsAzure.Mobile.Service;
        using ShoppingService.DataObjects;
        using ShoppingService.Models;
        using System.Linq;
        using System.Threading.Tasks;
        using System.Web.Http;
        using System.Web.Http.Controllers;
        using System.Web.Http.Description;
        using System.Web.Http.OData;

        namespace ShoppingService.Controllers
        {
            public class MobileOrderController : TableController<MobileOrder>
            {
                protected override void Initialize(HttpControllerContext controllerContext)
                {
                    base.Initialize(controllerContext);
                    ExistingContext context = new ExistingContext();
                    DomainManager = new MobileOrderDomainManager(context, Request, Services);
                }

                public IQueryable<MobileOrder> GetAllMobileOrders()
                {
                    return Query();
                }

                public SingleResult<MobileOrder> GetMobileOrder(string id)
                {
                    return Lookup(id);
                }

                public Task<MobileOrder> PatchMobileOrder(string id, Delta<MobileOrder> patch)
                {
                    return UpdateAsync(id, patch);
                }

                [ResponseType(typeof(MobileOrder))]
                public async Task<IHttpActionResult> PostMobileOrder(MobileOrder item)
                {
                    MobileOrder current = await InsertAsync(item);
                    return CreatedAtRoute("Tables", new { id = current.Id }, current);
                }

                public Task DeleteMobileOrder(string id)
                {
                    return DeleteAsync(id);
                }
            }
        }

3. 이제 서비스를 실행할 준비가 되었습니다. **F5** 키를 누르고 도움말 페이지에 기본 제공된 테스트 클라이언트를 사용해서 데이터를 수정합니다.

두 컨트롤러 구현 모두 **MobileCustomer** 및 **MobileOrder** DTO를 배타적으로 사용하며, 기본 모델을 인지하지 않습니다. 이러한 DTO는 JSON에 즉시 직렬화되며 모든 플랫폼에서 모바일 서비스 클라이언트 SDK와 데이터를 교환하는 데 사용할 수 있습니다. 예를 들어 Windows 스토어 앱을 작성할 때 해당 클라이언트 쪽 유형은 아래 표시된 것과 같습니다. 유형은 다른 클라이언트 플랫폼에서 도 유사합니다. 

    using Microsoft.WindowsAzure.MobileServices;
    using System;

    namespace ShoppingClient
    {
        public class MobileCustomer
        {
            public string Id { get; set; }

            public string Name { get; set; }

            [CreatedAt]
            public DateTimeOffset? CreatedAt { get; set; }

            [UpdatedAt]
            public DateTimeOffset? UpdatedAt { get; set; }

            public bool Deleted { get; set; }
            
            [Version]
            public string Version { get; set; }

        }

    }

다음 단계에서는 서비스에 액세스하기 위한 클라이언트 앱을 작성할 수 있습니다. 

<!--HONumber=42-->
