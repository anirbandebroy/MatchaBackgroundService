# Matcha Background Service Plugin for Xamarin.Forms

A plugin library to simplify Backgrounding in Xamarin.Forms. 
 

## Get Started
 
Ever wonder how facebook and twitter process there background to fetch a new content? And it looks so slick that when you refresh it was snappy and smooth, Making the user believed that the content is refreshed and updated in a snap when in fact it was done in the background. 

The secret behind it was the background service. And so we have created Matcha.BackgroundService to make our backgrounding task be simple and maintenable.

## Build & Test Status

* [![Build status](https://winstongubantes.visualstudio.com/MatchaBackgroundService/_apis/build/status/MatchaBackgroundService-CI)](https://winstongubantes.visualstudio.com/MatchaBackgroundService/_build/latest?definitionId=3)

## Setup

* NuGet: [Matcha.BackgroundService](http://www.nuget.org/packages/Matcha.BackgroundService) [![NuGet](https://img.shields.io/nuget/v/Matcha.BackgroundService.svg?label=NuGet)](https://www.nuget.org/packages/Matcha.BackgroundService/)
* `PM> Install-Package Matcha.BackgroundService`
* Install into ALL of your projects, include client projects.
 
 ## For Android
You call the "Init" method before all libraries initialization in MainActivity class.

```csharp
public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsAppCompatActivity
{
    protected override void OnCreate(Bundle bundle)
    {
        BackgroundAggregator.Init(this);
        
        base.OnCreate(bundle);
        ....// Code for init was here
    }
}
```
 
## For iOS
 
You call the "Init" method before all libraries initialization in FinishedLaunching method in FormsApplicationDelegate class.
 
```csharp
public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate
{
    public override bool FinishedLaunching(UIApplication app, NSDictionary options)
    {
         BackgroundAggregator.Init(this);
         
        ....// Code for init was here
         return base.FinishedLaunching(app, options);
    }
}
```

## For Mac
 
You call the "Init" method before all libraries initialization in DidFinishLaunching method in FormsApplicationDelegate class.
 
```csharp
public partial class AppDelegate : global::Xamarin.Forms.Platform.MacOS.FormsApplicationDelegate
{
    public override void DidFinishLaunching(NSNotification notification)
    {
        BackgroundAggregator.Init(this);
        Forms.Init();
        LoadApplication(new App());
        base.DidFinishLaunching(notification);
    }
}
```

## For Tizen
 
You call the "Init" method before all libraries initialization in OnCreate method in FormsApplication class.
 
```csharp
class Program : global::Xamarin.Forms.Platform.Tizen.FormsApplication
{
    protected override void OnCreate()
    {
        base.OnCreate();
        BackgroundAggregator.Init(this);
        LoadApplication(new Sample.App());
    }

    static void Main(string[] args)
    {
        var app = new Program();
        global::Xamarin.Forms.Platform.Tizen.Forms.Init(app);
        app.Run(args);
    }
}
```

## For Gtk
 
You call the "Init" method before all libraries initialization in Main method in MainClass class.
 
```csharp
class MainClass
{
    [STAThread]
    public static void Main(string[] args)
    {
        Gtk.Application.Init();
        Forms.Init();
        
        BackgroundAggregator.Init();

        var app = new App();
        var window = new FormsWindow();
        window.LoadApplication(app);
        window.SetApplicationTitle("Game of Life");
        window.Show();

        Gtk.Application.Run();
    }
}
```

## For UWP
 
First, You call the "Init" method before all libraries initialization in MainPage class.
 
```csharp
public sealed partial class MainPage
{
    public MainPage()
    {
        this.InitializeComponent();
        
        BackgroundAggregator.Init(this);
        
        LoadApplication(new SampleBackground.App(new UwpInitializer()));
    }
}
```

Then you put the line "BackgroundAggregatorService.Instance.Start()" in OnBackgroundActivated method under App.cs of UWP project.
 
```csharp
protected override void OnBackgroundActivated(BackgroundActivatedEventArgs args)
{
    base.OnBackgroundActivated(args);
    BackgroundAggregatorService.Instance.Start();
}
```

## Create Periodic Task
 
You will have to inherit IPeriodicTask interface in which you will supply and implement the interval and StartJob, Periodic Task will be execute every interval once it is registered.
 
```csharp
public class PeriodicWebCall : IPeriodicTask
{
    public PeriodicWebCallTest(int seconds)
    {
        Interval = TimeSpan.FromSeconds(seconds);
    }

    public TimeSpan Interval { get; set; }

    public Task<bool> StartJob()
    {
        // YOUR CODE HERE
        // THIS CODE WILL BE EXECUTE EVERY INTERVAL
        return true; //return false when you want to stop or trigger only once
    }
}
```

## Register Periodic Task
 
After you have implemented the Periodic Task you will need to register it to Background Aggregator Service,  We define it on OnStart() method under App.cs.
 
```csharp
protected override void OnStart()
{
    //Register Periodic Tasks
    BackgroundAggregatorService.Add(() => new PeriodicWebCall(3));
    BackgroundAggregatorService.Add(() => new PeriodicCall2(4));

    //Start the background service
    BackgroundAggregatorService.StartBackgroundService();
}
```

## Stop Periodic Task
 
We can stop the Periodic Task anytime by calling StopBackgroundService method, on our sample we place it on OnSleep() method under App.cs.
 
```csharp
protected override void OnSleep()
{
    BackgroundAggregatorService.StopBackgroundService();
}
```

## Quirks and Limitation
 
Keep in mind that the plugin was not design to communicate with UI thread, one way of dealing the transfer of information is through storage (e.g. Sqlite or Settings plugin). Our sample project is using Monkey-Cache storage.

Starting with Android Oreo it has already introduced the background execution limits similar to iOS background time limits assuming the app is in background mode or app is closed or minimized, as discuss on this [article](https://blog.xamarin.com/replacing-services-jobs-android-oreo-8-0/). 

UWP backgrounding uses In-Process backgrounding which is a bit less resilient than the Out-Process, however In-Process provides simplier approach and this is why we intend to support this platform using this approach.

For recently supported Gtk Mac and Tizen are very straightforward it doesn't require native backgrounding, however it runs the task on a separate thread to immitate backgrounding process.

For more info about Backgrounding in Android please check the link [HERE](https://docs.microsoft.com/en-us/xamarin/android/app-fundamentals/services/). 

For more info about Backgrounding in iOS please check the link [HERE](https://docs.microsoft.com/en-us/xamarin/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/). 

For more info about Backgrounding in UWP please check the link [HERE](https://docs.microsoft.com/en-us/windows/uwp/launch-resume/create-and-register-an-inproc-background-task). 

## That's it
 
You can now run your app that runs a Periodic Task every interval in the Background Service.  We have provided a few good samples to for you to dig in.

## Our Sample

We have created a sample app that has 3 Periodic Task in the background that gets an RSS feed from news outlet like BBC News, CNN News and Washington Post. It refreshes the data every 3 minutes.

<img src="https://github.com/winstongubantes/MatchaBackgroundService/blob/master/images/newsfeed.gif" width="400" title="md">


## Platform Supported

|Platform|Version|
| ------------------- | :-----------: |
|Xamarin.iOS|iOS 7+|
|Xamarin.Mac|All|
|Xamarin.Android|API 15+|
|Windows 10 UWP|10+|
|Tizen|4.0+|
|Gtk|All|
|.NET Standard|2.0+|
