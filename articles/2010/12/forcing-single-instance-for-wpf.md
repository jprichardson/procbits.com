<!--
author: JP Richardson
publish: Wed Dec 29 2010 17:43:49 GMT-0600 (CST)
status: publish
type: post
link: https://procbits.wordpress.com/2010/12/29/forcing-single-instance-for-wpf/
tags: C#, WPF
slug: 2010/12/29/forcing-single-instance-for-wpf
title: Forcing Single Instance for WPF Apps
-->



Sometimes you may not want to allow multiple instances for your WPF
apps. You can use a Mutex to accomplish this. Credit goes to this
[StackOverflow question: "What is the correct way to create a single
instance
application?"](http://stackoverflow.com/questions/19147/what-is-the-correct-way-to-create-a-single-instance-application)
for mashing these ideas together.

```csharp
public partial class App : Application
{
    [DllImport("user32.dll")]
    private static extern bool ShowWindow(IntPtr hWnd, int cmdShow);
    private const int SW_MAXIMIZE = 3;
    private const int SW_SHOWNORMAL = 1;

    private static Mutex singleInstanceMutex = new Mutex(true, "AnyUniqueStringToYourApp");

    protected override void OnStartup(StartupEventArgs e) {
        base.OnStartup(e);
        if (singleInstanceMutex.WaitOne(TimeSpan.Zero, true))
            Program.OnStartup();
        else {
            var procs = Process.GetProcessesByName(Process.GetCurrentProcess().ProcessName);
            foreach (var p in procs.Where(p => p.MainWindowHandle != IntPtr.Zero)) {
                ShowWindow(p.MainWindowHandle, SW_MAXIMIZE);
                    Application.Current.Shutdown();
                    return;
                }
            }   
        }
    }
}
```

The basic idea is that the startup code checks the mutex and maximizes
the application if the app isn't already open. You can view the [MSDN
docs for
ShowWindow](http://msdn.microsoft.com/en-us/library/ms633548(VS.85).aspx)
and pass any of the associated constants to alter the behavior of the
window if it is already running.


