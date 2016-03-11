@{
    Layout = "post";
    Title = "WPF menus that go up";
    Date = "2010-02-10";
    Tags = "WPF";
    Description = "";
}

 I needed a menu docked to the bottom of an application's window. Docking to the bottom of the window is no problem. However, it did not work well, because by default, WPF menus drop down. I needed it to drop up.

<!--more-->

I need behavior like the Windows Start menu when it is docked on the bottom of the Windows workspace. I looked for a `PopupDirection` or some such property -- no luck.


I found a solution.


Using Blender, edit a copy of a menu's `ItemContainerStyle`. The default style has two `Popup` elements with a `Placement` property. `Placement = "Top"` is a valid option.