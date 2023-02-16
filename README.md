gleam
=====

Gleam lets you build interactive web visualizations of data using Python: no knowledge of HTML or JS necessary! You can choose a number of inputs your users can control, then use any Python graphing library to create plots based on those inputs. Gleam puts it all together creates a web interface that lets anyone play with your data in real time. Now it's easier than ever to help others understand and interpret your data. Gleam was inspired by the [Shiny](http://www.rstudio.com/shiny/) package in R.

(You can find the code for the demo in [examples/baseball.py](examples/baseball.py)).

The original code was described by dgrtwo as the result of a hackathon project. It was for Python 2 and 'ggplot'. This fork makes changes to get at least partial functionality of the 'baseball.py' example using Python 3 and 'plotnine'. Tested with Python 3.8. I'm (WRE) still figuring out git, merges, and pull requests, so the Python 3/Plotnine functionality for the moment is only in the 'python3-plotnine' branch. If you simply clone and install, you are getting the Python 2/ggplot original.

There are alternatives to look at. Both 'Shiny for Python' and 'Streamlit' are actively maintained projects of larger scope. I'm (WRE) unlikely to expand this fork much given that there are good alternatives.

Installation
------------

In your Python 3 environment, install dependencies:
* flask
* jinja2
* wtforms

Optional (but needed for the example):
* plotnine

Then do the following:

    git clone <repo>
    cd <repo>
    git checkout python3-plotnine
    python setup.py install

If you don't do the branch checkout, you won't get the Python 3 compatible files.

Python will complain about not using standard setup/distribution tooling, but it should work. You probably don't want to do this in a Python environment that you would be mad about cloning or recreating if a conflict was introduced.

Example
---------

Let's make an interactive visualization of a scatter plot. Here we'll have three inputs that can be controlled by the user:

* The plot's title
* What variable goes on the y axis
* Whether we add a smoothing curve to the data

Start by importing a few packages. Gleam uses the [wtforms](http://wtforms.readthedocs.org/en/latest/) package to provide form inputs. You can use any Python graphing package you want with Gleam, such as [matplotlib](http://matplotlib.org/), but we recommend the intuitive [ggplot](https://github.com/yhat/ggplot/).

    from wtforms import fields
    from ggplot import *

Then import what you'll need from the gleam package:

    from gleam import Page, panels

### Inputs

An `Inputs` panel lets you specify the inputs that the user can control. Here, we add a string input for the title, a multiple choice select field for the Y-axis variable, and a checkbox for the smoother:

    class ScatterInput(panels.Inputs):
        title = fields.StringField(label="Title of plot:")
        yvar = fields.SelectField(label="Y axis",
                                  choices=[("beef", "Beef"),
                                           ("pork", "Pork")])
        smoother = fields.BooleanField(label="Smoothing Curve")

### Output

The output is where your actual plotting goes. It comes in the form of a `plot` method, which takes an argument `inputs` containing the inputs from above. Here we use ggplot to make the plot, taking the arguments into consideration. (You could use *any* Python graphing packages in this function- the sky's the limit, as long as it creates a plot).

    class ScatterPlot(panels.Plot):
        name = "Scatter"

        def plot(self, inputs):
            p = ggplot(meat, aes(x='date', y=inputs.yvar))
            if inputs.smoother:
                p = p + stat_smooth(color="blue")
            p = p + geom_point() + ggtitle(inputs.title)
            return p

### Tying it together in a Page

Constructing an HTML page to allow this control is as simple as combining the input and the output:

    class ScatterPage(Page):
        input = ScatterInput()
        output = ScatterPlot()

### Run the app

You can then run the app with:

     ScatterPage.run()

By default, it will create a local server hosted at [http://127.0.0.1:5000/](http://127.0.0.1:5000/): you can visit it there to see your cool visualization, which will look something like this:

![plot](http://i.imgur.com/aiEmxbw.png?1)

Try changing the title, the Y axis selector, or try checking the box: you'll see the plot react in real time.

You can add more inputs to the ScatterInput class, and then use them to further customize the plot in the ScatterPlot class.

*Enjoy!*
