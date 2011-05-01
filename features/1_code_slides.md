!SLIDE smallest

# Wrapping all request with in a transaction
    
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

# Every request == Unit of work 
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

!SLIDE smaller

# One model in, one model out

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


!SLIDE   center smaller

	@@@ html 
	<%= this.LinkTo(new HomeInputModel()).Text("Home") %>


!SLIDE   center 
# DRY up your htmls 
![html conventions](html_conventions.png)
        
