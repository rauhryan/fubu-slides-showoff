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
        
