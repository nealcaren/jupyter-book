---
redirect_from:
  - "getting-started/setup"
interact_link: content/getting_started/setup.ipynb
kernel_name: python3
title: 'Installation'
prev_page:
  url: /getting_started/overview
  title: 'Getting started'
next_page:
  url: /getting_started/python
  title: 'Python 101'
comment: "***PROGRAMMATICALLY GENERATED, DO NOT EDIT. SEE ORIGINAL FILES IN /content***"
---
# Setup

Before attending the workshp you should set up a scientific Python computing environment using the [Anaconda python distribution by Continuum Analytics](https://www.continuum.io/downloads). This page describes how. If this doesn't work, let [me](mailto:neal.caren@gmail.com) know and I will set you up with a virtual environment you can use on my server.




## Why Python?
As is true in human language, there are hundreds of computer programming languages. While each has its own merit, the major languages for scientific computing are C, C++, R, MATLAB, Python, Java, and Fortran. MATLAB and Python are similar in syntax and typically read as if they were written in plain english. This makes both languages a useful tool for teaching but they are also very powerful languages and are very actively used in real-life research. MATLAB is proprietary while Python is open source. A benefit of being open source is that anyone can write and release Python packages. For science, there are many wonderful community-driven packages such as NumPy, SciPy, scikit-image, and Pandas just to name a few.


## Installing Python 3.7 with Anaconda

There are several scientific Python distributions available for MacOS, Windows, and Linux. The  most popular, [Anaconda](https://www.continuum.io/why-anaconda), is specifically designed for scientific computing and data science work. For this course, we will use the Anaconda Python 3.7 distribution. To install the correct version, follow the instructions below.
1. Navigate to the [Anaconda download page](https://www.anaconda.com/distribution/) and download the Python 3.7 graphical installer.
2. Launch the installer and follow the onscreen instructions.
3. Congratulations! You now have the beginnings of a scientific Python distribution.

## What is a Jupyter notebook?

[Jupyter](http://jupyter.org/) is a browser-based system to write code, math, and text in the same document so you can clearly explain the concepts and practices used in your program. Jupyter is not only for Python, but can be used with R, Juila, MATLAB, and about 35 other languages as of this writing. All files are saved as a [JSON](http://www.json.org/) formatted text file with the extension `.ipynb`.



## How to launch the notebook

A Jupyter Notebook server can either be launched from the command line or from a GUI program installed along with anaconda called Navigator.



### Launching from the Anaconda Navigator

Installing Python 3 from Anaconda should also install a GUI application called [Anaconda Navigator](https://docs.continuum.io/anaconda/navigator). From here, you can launch several applications such as a QTconsole, the Spyder IDE, and a data visualization software called GlueViz. We are interested in the Jupyter Notebook application tab, which is shown boxed in red below:

![](http://www.rpgroup.caltech.edu/bige105/code/images/anaconda_navigator.png)

By clicking on 'Launch', you will instantiate a Jupyter notebook server which should open in a new window.






### Launching from the terminal

To launch a notebook server from the command line, simply open a terminal emulator (Terminal.app on OSX or gitbash on windows) and navigate to the directory you would like to set up a server by typing `cd path/to/folder`
Once you are in the correct folder, you can launch a notebook server by typing:

```
jupyter notebook
```

This will open a screen in your default internet browser with a server containing your notebooks. Its address will be [`http://localhost:8888`](http://localhost:8888/) and is only available on your computer. **Note that once you start a server, you must keep the terminal window open.** This is where the 'guts' of the python kernel is.



## Interacting with the notebook
If everything launched correctly, you should be able to see a screen which looks something like this:

![](http://www.rpgroup.caltech.edu/bige105/code/images/starting_notebook.png)

To start a new python window, click on the right-hand side of the application window and select `New`. This will give you a bunch of options for new notebook kernels. In the above screen shot, there are two available Python kernels and one Matlab kernel. When starting a notebook, you should choose `Python 3` if it is available. If you have just a tab that says "Python", choose that one.

Once you start a new notebook, you will be brought to the following screen.

![](http://www.rpgroup.caltech.edu/bige105/code/images/toolbars.png)

Welcome to the Jupyter notebook! There are many available buttons for you to click. However, the three most important components of the notebook are highlighted in colored boxes. In blue is the name of the notebook. By clicking this, you can rename the notebook. In red is the cell formatting assignment. By default, it is registered as code, but it can also be set to markdown as described later.

Finally, in purple, is the code cell. In this cell, you can type an execute Python code as well as text that will be formatted in a nicely readable format.

## Writing code

All code you write in the notebook will be in the code cell. You can write single lines, to entire loops, to complete functions. As an example, we can write and evaluate a print statement in a code cell, as is shown below. To exectue the code, we can simply hit `shift + enter` while our cursor is in the code cell.




{:.input_area}
```python
# This is a comment and is not read by Python
print('Hello! This is the print function. Python will print this line below')
```


The box with the gray background contains the python code while the output is in the box with the white background.
 

## Next Steps

Now that you have a Python environment up and running, proceed to the [Python] notebook to learn the basics of the language. 

*Note: This is a modified version of Griffin Chure's [Setting Up Python For Scientific Computing for Bi 1 - Principles of Biology](http://bi1.caltech.edu/code/t0a_setting_up_python.html). This work is licensed under a [Creative Commons Attribution License CC-BY 4.0](https://creativecommons.org/licenses/by/4.0/).*
