@{
    Layout = "post";
    Title = "WPF ViewModel of an F# Record";
    Date = "2015-03-17T03:36:57";
    Tags = "";
    Description = "";
}

How does one expose an F# record in a WPF view model? My F# code uses immutable records. But, I need to bind those records to a WPF user interface in a view model. What is the right way to do that?

<!--more-->

##Background

I have an F# record that looks something like this:

    type MyOptions = 
        { FreezePanes : bool
          ShowHeaders : bool }

I want to bind this to an WPF user interface with two check boxes.

I **do not want** my view model to look like this:

    type MyOptionsViewModelVerbose(opts : MyOptions) =

        let propertyChangedEvent = Event<PropertyChangedEventHandler, PropertyChangedEventArgs>()

        let mutable freezePanes = opts.FreezePanes
        let mutable showHeaders = opts.ShowHeaders

        member x.FreezePanes
            with get() = freezePanes
            and set(v) = 
                if v <> freezePanes then
                    freezePanes <- v
                    propertyChangedEvent.Trigger(x, PropertyChangedEventArgs("FreezePanes"))
                
        member x.ShowHeaders
            with get() = showHeaders
            and set(v) = 
                if v <> showHeaders then
                    showHeaders <- v
                    propertyChangedEvent.Trigger(x, PropertyChangedEventArgs("ShowHeaders"))
                
        interface INotifyPropertyChanged with
            [<CLIEvent>]
            member x.PropertyChanged : IEvent<PropertyChangedEventHandler, PropertyChangedEventArgs> = 
                propertyChangedEvent.Publish

That is too much code! And in my real project, my record has a dozen members.

##Proposed Solution

Instead, bind directly to a copy of the record.

First, decorate the record definition to allow WPF to modify the immutable type. That's the purpose of the `[<CLIMutable>]` attribute. Also add `Default` and `Clone()` members to the record. The reason for these two members becomes apparent in the next step.

    [<CLIMutable>]
    type MyOptions = 
        { FreezePanes : bool
          ShowHeaders : bool }
    
        static member Default = 
            { FreezePanes = false
              ShowHeaders = false }
    
        member x.Clone() = { x with FreezePanes = x.FreezePanes }

Define a ViewModel class which publicly exposes a copy of the record value.

    type MyOptionsViewModel(options : MyOptions) =
        new() = MyOptionsViewModel(MyOptions.Default)
        member val Options = options.Clone()

The `opts` record is cloned because WPF is going to be changing the values of the record. We do not want to break the immutability assumptions that the calling code makes about the `opts` record.

##Sample Usage

Here is some code to test this approach.

    let winXaml = "
        <Window xmlns=\"http://schemas.microsoft.com/winfx/2006/xaml/presentation\"
	            xmlns:x=\"http://schemas.microsoft.com/winfx/2006/xaml\"
                xmlns:mbo=\"clr-namespace:MyBusinessObjects;assembly=MyOptionsTest\">
            <Window.DataContext>
                <mbo:MyOptionsViewModel />
            </Window.DataContext>
            <StackPanel>
                <CheckBox IsChecked=\"{Binding Options.FreezePanes}\"
                    Content=\"Freeze Headers\" />
                <CheckBox IsChecked=\"{Binding Options.ShowHeaders}\"
                    Content=\"Show Headers\" />
            </StackPanel>
        </Window>"
    
    [<EntryPoint; STAThread>]
    let main argv = 
        let win = XamlReader.Parse(winXaml) :?> Window
        
        let options = 
            { FreezePanes = true
              ShowHeaders = true }
        win.DataContext <- MyOptionsViewModel(options)
        let app = new Application()
        app.Run(win) |> ignore
        let vm = win.DataContext :?> MyOptionsViewModel
        printfn "Before: %A" options
        printfn "After: %A" vm.Options
        Console.ReadKey(false) |> ignore
        0  

##Final note

Keep in mind that you may need to think about your own implementation of `Clone()`. It may need to be a deep copy, depending on what the user interface could potentially modify.
