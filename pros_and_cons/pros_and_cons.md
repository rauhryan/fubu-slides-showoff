!SLIDE 

# Pro's > Con's

!SLIDE center

# Pros

!SLIDE center 

# Real World Need
![dogfood](eating_dog_food.jpg)

!SLIDE

# "Best Practices"
!SLIDE center

# It's Open Source #
### and how! They take pull requests! ###
>GET YOURS TODAY!

!SLIDE center

### That's US! ###
![github](github_contributors.png) 

!SLIDE smallest
# Really nice configuration DSL

	@@@ csharp
        public FubuMusicStoreRegistry()
        {
            IncludeDiagnostics(true);

            Actions.IncludeTypesNamed(x => x.EndsWith("Action"));

            Routes
                .IgnoreNamespaceText("FubuMusicStore.Actions")
                .IgnoreControllerNamesEntirely()
                .IgnoreClassSuffix("Action")
                .IgnoreMethodsNamed("Execute")
                .IgnoreMethodSuffix("Post")
                .IgnoreMethodSuffix("Get")
                .IgnoreMethodSuffix("Delete")
                .IgnoreMethodSuffix("Put")
                .ConstrainToHttpMethod(action => action.Method.Name.Equals("Post"), "POST")
                .ConstrainToHttpMethod(action => action.Method.Name.Equals("Put"), "PUT")
                .ConstrainToHttpMethod(action => action.Method.Name.Equals("Get"), "GET")
                .ConstrainToHttpMethod(action => action.Method.Name.Equals("Delete"), "DELETE");
            
            Routes.HomeIs<HomeAction>(x => x.Get(null));

            Views.TryToAttachWithDefaultConventions();
        }

!SLIDE  smallest

## Thin controllers ##

    @@@ csharp
    public class HomeAction
    {
        private readonly IRepository _repository;

        public HomeAction(IRepository repository )
        {
            _repository = repository;
        }

        public HomeViewModel Get(HomeRequest request)
        {

            var topSellingAlbums = _repository.Query<Album>()
                .OrderByDescending(a => a.OrderDetails.Count)
                .Take(request.Count)
                .ToList();

            return new HomeViewModel(){ Albums = topSellingAlbums};
        }
    }

!SLIDE smallest

## No yucky base class
	@@@ csharp
	namespace FubuMVC.HelloWorld.Controllers.Home
	{
	    public class HomeController
	    {
			public HomeViewModel Home(HomeInputModel model)
			{
			    return new HomeViewModel();
			}
	    }
	}

!SLIDE smallest
## No ViewData 
	@@@ csharp
	
			public ActionResult Index() {

				  ViewData["Message"] = "Some Message";

				  return View();

			}

### YUCK!
!SLIDE smallest

## Every view is strongly typed
	@@@ csharp
	namespace FubuMVC.Core.View
	{
		    public interface IFubuView
		    {
			
		    }

		    public interface IFubuViewWithModel : IFubuView
		    {
				void SetModel(IFubuRequest request);
				void SetModel(object model);
		    }

		    public interface IFubuView<VIEWMODEL> : IFubuViewWithModel
			where VIEWMODEL : class
		    {
				VIEWMODEL Model { get; }
		    }

		    public interface IFubuPage : IFubuView
		    {
				string ElementPrefix { get; set; }
				IServiceLocator ServiceLocator { get; set; }
				T Get<T>();
				T GetNew<T>();
				IUrlRegistry Urls { get; }
		    }

		    public interface IFubuPage<TViewModel> : 
				IFubuPage, IFubuView<TViewModel> where TViewModel : class
		    {

		    }
	}

!SLIDE   smallest

## No logic in attributes
	@@@ csharp
	    public class TransactionFilter : ActionFilterAttribute
	    {

			public override void OnActionExecuting(ActionExecutingContext filterContext)
			{
			    var session = MvcApplication.SessionFactory.GetCurrentSession();
			    session.BeginTransaction();
			}
			public override void OnActionExecuted(ActionExecutedContext filterContext)
			{
			    var session = MvcApplication.SessionFactory.GetCurrentSession();
			    
			    using (session.Transaction)
			    {
					if (filterContext.Exception == null)
					{
					    session.Transaction.Commit();
					}
					else
					{
					    session.Transaction.Rollback();
					}
			    }
			}
	    }

!SLIDE smallest

## Wrapping all request with in a transaction
    
    @@@ csharp	
    public class TransactionalContainerBehavior : IActionBehavior
    {
        private readonly ServiceArguments _arguments;
        private readonly Guid _behaviorId;
        private readonly IContainer _container;

        public TransactionalContainerBehavior(IContainer container, 
						ServiceArguments arguments, Guid behaviorId)
        {
            _container = container;
            _arguments = arguments;
            _behaviorId = behaviorId;
        }

        public void Invoke()
        {
            _container.ExecuteInTransaction<IContainer>(invokeRequestedBehavior);
        }

        public void InvokePartial()
        {
            // Just go straight to the inner behavior here. 
            // Assuming that the transaction & principal are already set up
            invokeRequestedBehavior(_container);
        }

        private void invokeRequestedBehavior(IContainer c)
        {
            var behavior = c.GetInstance<IActionBehavior>(_arguments.ToExplicitArgs(),
			 _behaviorId.ToString());
            behavior.Invoke();
        }
    }


!SLIDE smallest

## Every request == Unit of work 
    @@@csharp
        private void execute(Action<IContainer> action)
        {
            IContainer container = null;
            lock (_locker)
            {
                container = _container;
            }
            // This is using the new "Nested" Container feature of StructureMap
            // to create an entirely new Container object that is "scoped" to
            // this action
            using (IContainer nestedContainer = container.GetNestedContainer())

            using (var boundary = nestedContainer.GetInstance<ITransactionBoundary>())
            {
                try
                {
                    boundary.Start();
                    action(nestedContainer);
                    boundary.Commit();
                }
                catch
                {
                    boundary.Rollback();
                    throw;
                }
                finally
                {
                    boundary.Dispose();
                }
            }
        }
!SLIDE center

# Very active mail list
[http://groups.google.com/group/fubumvc-devel](http://groups.google.com/group/fubumvc-devel)

### People will help, but they won't hand it to you.

!SLIDE center 

# Con's

!SLIDE center

# "For us, by us"
![not for you](haters_gonna_hate.png)

!SLIDE center
![honey badger](honey_badger.jpg)

!SLIDE

![beat down!](beatdown.gif)
![haters gonna hate](haters-gonna-hate.gif)
<p class="center">we will do this to you. srsly.</p>

!SLIDE center
# Not mainstream
![google_search](google_controls.png)

!SLIDE center

# Very little documentation

!SLIDE center

# Not released yet.

### 1.0 is coming soon

!SLIDE bullets

# Show me the links

* [fubumvc.com](http://fubumvc.com)
* [guides.fubumvc.com](http://guides.fubumvc.com)
* [github.com/DarthFubuMVC/fubumvc](http://github.com/DarthFubuMVC/fubumvc)


