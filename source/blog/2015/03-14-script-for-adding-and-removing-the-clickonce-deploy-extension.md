@{
    Layout = "post";
    Title = "Script for adding and removing the ClickOnce .deploy extension";
    Date = "2015-03-14T10:50:12";
    Tags = "F#, ClickOnce";
    Description = "";
}

I was recently debugging a ClickOnce deployment. I needed a way to quickly add and remove the `*.deploy` extension from all the files in a deployment folder. Well, not *all* the files. The `*.manifest` file is treated differently.

<!--more-->

Here is the F# script that I used.

	open System
	open System.IO
	
	let args = fsi.CommandLineArgs
	
	let showUsage() = 
	    printfn "Usage: fsi.exe DeployExtension -- Dir Cmd"
	    printfn "       where Dir is the name of the application directory"
	    printfn "         and Cmd is either 'Add' or 'Remove'"
	
	match fsi.CommandLineArgs.Length with
	| 4 -> 
	    let dir = fsi.CommandLineArgs.[2]
	    let cmd = fsi.CommandLineArgs.[3]
	    printfn "%s the deploy extension to '%s'? " cmd dir
	    match Console.ReadKey(true).Key with
	    | ConsoleKey.Y -> 
	        for f in Directory.EnumerateFiles dir do
	            let ext = Path.GetExtension(f)
	            match cmd.ToLower() with
	            | "add" -> 
	                match ext with
	                | ".deploy" | ".manifest" -> ()
	                | _ -> 
	                    printfn "Adding deploy extension to %s" f
	                    File.Move(f, sprintf "%s.deploy" f)
	            | "remove" -> 
	                match ext with
	                | ".deploy" -> 
	                    printfn "Removing deploy extension from %s" f
	                    File.Move(f, (Path.ChangeExtension(f, null)))
	                | _ -> ()
	            | _ -> showUsage()
	    | _ -> ()
	| _ -> showUsage()
   