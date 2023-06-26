# pyCLAMP
Time series recordings analysis in Python with UI similar to pCLAMP.

# What is pyCLAMP for?
pyCLAMP is generally useful for analysis of any *(x,y)* data series that may be recorded in multiple channels and in respose to repeated episodes or triggered recording periods.

More specifically, pyCLAMP aims to be a drop-in replacement (and improvement) for electrophysiology recording software such as pCLAMP, Patchmaster, Axograph, WinWCP, etc.

# Can you aquire data recordings with pyCLAMP?
Not yet. Currently pyCLAMP can only view/analyze previously recorded data series. *But keep an eye out as the ability to aquire data is one of the things that pyCLAMP hopes to be capable of in the future.*

# Why use pyCLAMP?
As of now you still need a different program to aquire data anyway, and as pyCLAMP is in its infancy it is undoubtably the case that it cannot yet do everything that other programs such as pCLAMP can. So why use pyCLAMP at all?

1. pyCLAMP saves your data in a simple self-describing and highly flexible and extensible data structure that is easily loaded and explored in Python or MATLAB and will always be retrievable independent of pyCLAMP. You will never have to rely on having access to specific recording software to analyze your data and you will have the power of Python or MATLAB at your fingertips to write whatever analysis code you want. See the section below on [pyCLAMP Data Structure](#pyclamp-data-structure).
2. pyCLAMP provides a well designed UI for exploring and analyzing your data that in certain areas is already more feature rich than conventional alternatives.
3. pyCLAMP already provides tools such as baseline fitting and detrending that are superior to the options in conventional alternatives, and which are still being developed further nonetheless.
4. pyCLAMP is open source, so you have access to everything and can modify or customize the UI and analysis options to your liking. Please contribute your additions so that pyCLAMP's cababilities can grow for everyone. Eventually, pyCLAMP will be more powerful than the expensive closed source options that are currently available.
5. Need a specific capability? Just ask for it and it may be provided in short order.

# INSTALL pyCLAMP
`pip install pyclamp`

This should install everything you need including all requirements.

## Requirements
* [numpy](https://numpy.org)
* [scipy](https://scipy.org)
* [lmfit](https://lmfit.github.io/lmfit-py/)
* [PyQt6](https://pypi.org/project/PyQt6/)
* [pyqtgraph](https://www.pyqtgraph.org)
* [qtawesome](https://github.com/spyder-ide/qtawesome)

# Run the pyCLAMP UI
Just run the command `pyclamp` to bring up the UI.

# pyCLAMP Data Structure
TODO