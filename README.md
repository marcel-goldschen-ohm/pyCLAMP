# pyCLAMP
Time series recordings analysis in Python with UI similar to pCLAMP.

# !!! WARNING !!!
Data structure is under conversion to utilize Zarr format. This may introduce breaking changes in the current version of pyCLAMP.

# What is pyCLAMP for?
pyCLAMP is generally useful for analysis of any *(x,y)* data series that may be recorded in multiple channels and in respose to repeated episodes or triggered recording periods.

More specifically, pyCLAMP aims to be a drop-in replacement (and improvement) for electrophysiology recording software such as pCLAMP, Patchmaster, Axograph, WinWCP, etc.

# Can you aquire data recordings with pyCLAMP?
Not yet. Currently pyCLAMP can only view/analyze previously recorded data series. *But keep an eye out as the ability to aquire data is one of the things that pyCLAMP hopes to be capable of in the future.*

# Why use pyCLAMP?
As of now you still need a different program to aquire data anyway, and as pyCLAMP is in its infancy it is undoubtably the case that it cannot yet do everything that other programs such as pCLAMP can. So why use pyCLAMP at all?

1. pyCLAMP saves your data in a simple self-describing and highly flexible and extensible data structure that is easily loaded and explored and will always be retrievable independent of pyCLAMP ([see below for pyCLAMP Data Structure](#pyclamp-data-structure)). You will never have to rely on having access to specific recording software to analyze your data and you will have the power of Python or MATLAB at your fingertips to write whatever analysis code you want.
2. pyCLAMP stores data in MATLAB `*.mat` file format for simple loading in either MATLAB or Python ([see below for pyCLAMP data in Python and MATLAB](#pyclamp-data-in-python-and-matlab)).
3. pyCLAMP provides a well designed UI for exploring and analyzing your data that in certain areas is already more feature rich than conventional alternatives.
4. pyCLAMP already provides tools such as curve fitting, baseline detrending, statistics measurement, and event tagging.
5. pyCLAMP is open source and entirely in Python, so with a bit of Python know how you can easily modify or customize anything to your liking. The UI is entirely in PyQt, so it is also fairly simple to customize the UI as you see fit. Please contribute your additions so that pyCLAMP's cababilities can grow for everyone.
6. Need a specific capability? Just ask for it and it may be provided in short order.
7. pyCLAMP is under active development, so check back often to see what's improved!

# Install pyCLAMP
`pip install pyclamp`

This should install everything you need including all [requirements](requirements.txt).

## Requirements
* [numpy](https://numpy.org)
* [scipy](https://scipy.org)
* [lmfit](https://lmfit.github.io/lmfit-py/) - for curve fitting
* [PyQt6](https://pypi.org/project/PyQt6/) - for the UI
* [pyqtgraph](https://www.pyqtgraph.org) - for plotting
* [qtawesome](https://github.com/spyder-ide/qtawesome) - for icons
* [pyABF](https://swharden.com/pyabf/) - for ABF files

# Run the pyCLAMP UI
`python -m pyclamp`

This will launch the UI. To load some data open the menu (button in the upper left corner) and select `File -> Open` or `File -> Import ...`

# Data file compatibility
| File Type | Import | Export |
| --------- | ------ | ------ |
| Axon Binary File (ABF) | under construction: [pyABF](https://swharden.com/pyabf/) | |
| HEKA PatchMaster | [heka_reader](https://github.com/campagnola/heka_reader) | |
| WinWCP | | |

# pyCLAMP Data Structure
- Data is stored as nested dictionaries or lists of dictionaries for maximum flexiblity and extensibility.
    - Need new functionality? Just add a new key at whichever level of the heirarchy is appropriate.
    - The data is entirely composed only of `dict`, `list`, `str`, `int`, `float`, and numpy `ndarray` values and thus can be easily serialized to JSON, `.mat`, etc.
    - The data is entirely self-describing and can be easily explored and manipulated independent of the UI or any other code in this package.
- Data series traces are organized heirarchically by episode, channel, trace which should cover most experimental recording paradigms.
    - EPISODE: A single recording sweep.
        - CHANNEL: A single recorded signal (e.g., current, voltage, etc.).
            - TRACE: A single data trace.
                - Overlay TRACE: A single data trace associated with a parent trace (e.g., fit, measurement, etc.).
            - EVENT: A labeled x-axis interval.
- For memory efficiency any data arrays that are shared amongst traces (e.g., XData) should all be references/views to the same shared array. These should generally be numpy ndarrays.
- In most cases each (EPISODE, CHANNEL) pair will contain a list with only a single trace (i.e., `CHANNEL['Traces'] = [TRACE]`) and all fits, etc. will be handled with overlaid child traces of that single trace. Multiple traces per (EPISODE, CHANNEL) pair (i.e., `CHANNEL['Traces'] = [TRACE, TRACE, ...]`) are for things like triggered recordings of multiple segments within each sweep.

The basic tree structure of nested dictionary objects or lists of dictionary objects:

```python
DATA = {
    'Type': 'Data'
    'Episodes': [EPISODE, ...]
    'Notes': notes
}
```
```python
EPISODE = {
    'Type': 'Episode'
    'Channels': [CHANNEL, ...]
}
```
```python
CHANNEL = {
    'Type': 'Channel'
    'Traces': [TRACE, ...]
    'Events': [EVENT, ...]
}
```
```python
TRACE = {
    'Type': 'Trace'
    'Name': trace label (defaults to 'Data')
    'XData': ndarray[sample] OR sample interval (defaults to sample indexes)
    'YData': ndarray[sample]
    'XLabel': x-axis label
    'YLabel': y-axis label
    'XUnit': x-axis unit
    'YUnit': y-axis unit
    'XZero': x-axis zero point, XData -> XData - XZero
    'YZero': ndarray[sample] OR constant baseline, YData -> YData - YZero
    'YScale': ndarray[sample] OR constant scale factor, YData -> YData * YScale
    'Mask': logical mask, YData -> YData[Mask]
    'Style': STYLE
    'Traces': [TRACE, ...]
}
```
Nested traces in `TRACE['Traces']` are for overlay traces associated with their parent trace (e.g., fits, measurements, etc.).
```python
EVENT = {
    'Type': 'Event'
    'Group': event group label (defaults to 0)
    'XStart': x-axis event start
    'XStop': x-axis event stop (defaults to XStart)
    'Text': event info
}
```
Style dicts define a trace's plot style in the UI.
```python
STYLE = {
    'Type': 'Style'
    'Color': (r, g, b) or (r, g, b, a) in 0-255 or 'auto' (defaults to 'auto' => based on colormap)
    'LineStyle': one of ['none', '-', '--', ':', '-.'] (defaults to '-')
    'LineWidth': (defaults to 1)
    'Marker': one of ['none', 'o', 't', 't1', 't2', 't3', 's', 'p', 'h', 'star', '+', 'd', 'x'] (defaults to 'none')
    'MarkerSize': (defaults to 10)
    'MarkerEdgeWidth': (defaults to LineWidth)
    'MarkerEdgeColor': (r, g, b) or (r, g, b, a) in 0-255 or 'auto' (defaults to 'auto' => Color)
    'MarkerFaceColor': (r, g, b) or (r, g, b, a) in 0-255 or 'auto' (defaults to 'auto' => MarkerEdgeColor with alpha=0)
}
```
For example, to access the *kth* trace in the *jth* channel for the *ith* episode:
```python
DATA['Episodes'][i]['Channels'][j]['Traces'][k]['YData'] = ...
```
Because all `dict` objects are passed by reference, it is generally simpler to store references to objects and then manipulate their data through these refereces:
```python
TRACE = DATA['Episodes'][i]['Channels'][j]['Traces'][k]
TRACE['YData'] = ...
```
If the above trace contained a fit curve as its first child trace, then you would access the data in the fit curve as:
```python
fit = TRACE['Traces'][0]
fit['YData'] = ...
```
Similarly, to access the *kth* event in the *jth* channel for the *ith* episode:
```python
EVENT = DATA['Episodes'][i]['Channels'][j]['Events'][k]
EVENT['XStart'] = ...
```
References from child to parent dict objects are stored separately from the data structure itself in a list of `(CHILD, PARENT)` tuples. These are very useful for manipulating the data structure, but are not required and can be derived from the data structure as needed. Were they to be included in the data structure itself, then they would prevent simple serialization to JSON, `.mat`, etc.

!!! *If you manipulate the data structure with custom code, then it is up to you to insure that these `(CHILD, PARENT)` references are up-to-date for the UI to work as expected.* Note that `DataModel.refreshParents()` will refresh these references for the entire data structure whenever needed.

# pyCLAMP data in Python and MATLAB
The data structure is serialized/deserialized to/from MATLAB `.mat` file format so that it can be easily loaded in MATLAB if desired. Nested dicts and lists of dicts in Python become nested structs and structarrays in MATLAB. To load in python see the function `DataModel.loadmat()` which uses `scipy.loadmat()` to load the data from a previously serialized `.mat` file. The load function also strips the data of uneeded MATLAB-specific tokens (which is nice but does not affect the actual data) and ensures that certain objects are lists of dicts rather than a simple dict. The latter is required for the UI to function correctly, but it is NOT required to explore the dataset and understand its structure or access its contents.

# TODO
- !!! Use Zarr data format. This will be a major conversion of the data structure.
- plots into scrollarea in case lots of channels
- set XZero
- group episodes (e.g., episodes are repeated series of increasing stimulus pulses)
- auto detect and fix single dict -> list of dict where necessary
    - anyway to ensure this via scipy.loadmat? probably not though
- autoscale all plots
- Fusion style or another or none?
- Menu section titles not showing when fusion style not set?
- distribute/stack traces vertically (or 3-D effect with both vertical and horizontal offset)
- events for all traces in an episode defined at episode level, etc.
- option to not fit overlay traces
- export trace to new window
- trace x/y table(s)
- curve fit option to specify xfit as linspace or logspace
- trace mask
- yscale
- selection list boxes not always growing vertically to fit content?
- option to show all episodes/traces in background
    - can click to select background trace?
- managing colormaps?
- persistent baseline style?
- set global styles?
- filtering?
- wrap trace (e.g., for single channel analysis?), or maybe this is best left to its own special UI?
- fix ylabel overlapping ytick labels due to fixed left axis width
    - if left axes can autosize again will have to left-align axes after each zoom/pan
- test curve fit bounds
- curve fit fixed parameters
- curve fit constraint expressions
- baseline nodes (allow manual adjustment of baseline)
- left click to select a single trace (while displaying multiple)?
- reorder channels???
- reorder trace names???
- aquisition???