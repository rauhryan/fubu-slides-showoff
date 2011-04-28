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

# One model in, on model out

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
