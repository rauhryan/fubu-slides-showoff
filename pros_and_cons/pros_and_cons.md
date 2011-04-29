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
