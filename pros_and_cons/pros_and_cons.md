!SLIDE subsection

# Pro's and Con's

!SLIDE center 

# Real World Need
![dogfood](eating_dog_food.jpg)

!SLIDE

# "Best Practices"
!SLIDE

# It's Open Source #
### and they take pull requests! ###

!SLIDE center

### That's US! ###
![github](github_contributors.png) 

!SLIDE  code smaller

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

!SLIDE code smaller

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
