@{
    Layout = "post";
    Title = "Calculating nice chart axis ranges";
    Date = "2010-02-24";
    Tags = "charting";
    Description = "";
}

I was working on a project that required calculating nice ranges for chart axes. I found a useful discussion [here](http://stackoverflow.com/questions/611878/reasonable-optimized-chart-scaling).

Here is what I used in C# and so far it is working adequately.

<!--more-->

    var numGridLines = 5;
    var magnitude = Math.Floor(Math.Log10((maxValue - minValue) * 1.1));
    var basis = Math.Pow(10, (magnitude - 1));
    var margin = (maxValue - minValue) * 0.1;
    var axisMax = Math.Ceiling((maxValue + margin) / basis) * basis;
    var axisMin = Math.Floor((minValue - margin) / 1.1 / basis) * basis;
    var majorGrid = Math.Floor((axisMax - axisMin) / basis / numGridLines) * basis;
