# BerryPy

## What does BerryPy stand for?

**B**asic **er**ror and **r**esponse time anal**y**ses in **Py**thon

## What is BerryPy?

A Jupyter notebook for computing individual RTs and error rates, and collating these data across participants.

## What can BerryPy do?

- Reject extreme values (below/above a given RT threshold)
- Reject outlier RTs (based on SDs, trimming, or median absolute deviations)
- Define conditions based on information in the input file
- Compute mean or median RTs
- Compute accuracies/error rates
  - Note: BerryPy rejects extreme values *before* calculating accuracies

BerryPy reads input files produced by an experiment control programme (e.g., [PsychoPy](http://www.psychopy.org/)), and produces a .csv output file that can be imported into SPSS or other statistical software packages.

BerryPy looks for its input files in the folder `input`, and writes its output to the folder `output`.

## Aims and objectives

Aims | Objectives
-----|-----------
Easy to use | Typical user interaction via text configuration files
Flexible | Defining conditions quite powerful out of the box
Extensible | Open source – modify code, add code

Additional aims from a teaching perspective | Objectives
--------------------------------------------|-----------
Think about preprocessing steps | Need to define parameters in config files
No unnecessary user interaction | Automatically processes all .csv files in input directory
Visualise individual data | Creates Markdown output file with RT distributions and plausibility checks

## Limitations

- Works best with [PsychoPy](http://www.psychopy.org/) output files
  - BerryPy expects the input files to have a header
  - BerryPy expects one trial per line

## How does it work?

BerryPy uses three configuration text files to produce its output.

### File 1: General settings

- Info about relevant column names in input (e.g. "response.corr" for accuracy of response)
- Thresholds for extreme values (e.g., exclude anything below 100 ms and above 2000 ms)
- Measure of central tendency (i.e., mean or median)
- Outlier rejection: SD, trimmed mean, median absolute deviation

### File 2: RT analysis

- Name and define conditions for output file
- Basic syntax:
    - `<columnName>: [operator]<columnValue>`, where `<columnName>` is the column name in the input file (e.g., `response.corr`) and `<columnValue>` is the value (e.g., `0` for incorrect)
- Operators
    - equal (default): `=`
    - does not equal: `!=`
    - below: `<`
    - below or equal: `<=`
    - above: `>`
    - above or equal: `>=`

### File 3: Error analysis

- Same syntax rules and operators as for RTs
- Major difference: need to define ratio using `/`
  - Basic syntax: `<trialsOfInterest>/<allTrials>`

## Examples

Minimal input file example:

condition | response.corr | response.rt | trials.thisN | stimVisibility | stimLocation
----------|--------------:|------------:|-------------:|:--------------:|:-----------:
congruent | 1 | 0.624 | 0 | high | left
incongruent | 0 | 0.683 | 1 | low | left
incongruent | 1 | 0.727 | 2 | high | right
congruent | 1 | 0.386 | 3 | low | centre
incongruent | 1 | 0.495 | 4 | low | centre
incongruent | 1 | 0.528 | 5 | high | right
incongruent | 0 | 0.581 | 6 | high | centre
congruent | 0 | 0.669 | 7 | low | right
congruent | 1 | 0.421 | 8 | high | left
... |||||
incongruent | 1 | 0.484 | 99 | low | centre

### RT analysis

Please note that the measure of central tendency and the name of the RT column need to be specified in the general settings file.

**Example content of RT configuration file**

```
# mean congruent and incongruent RTs for correct trials only
# conRT and inconRT will be the column headers in the output file
# not necessary to add default operator equal
conRT; condition: congruent; response.corr: 1
inconRT; condition: incongruent; response.corr: 1

# as above, but exclude the first 20 trials (i.e., trial numbers 0 to 19)
conRT; condition: congruent; response.corr: 1; trials.thisN: [>=]20
inconRT; condition: incongruent; response.corr: 1; trials.thisN: [>=]20

# mean con RTs separately for low and high visibility trials
conRT_lowVis; condition: congruent; response.corr: 1; stimVisibility: low
conRT_highVis; condition: congruent; response.corr: 1; stimVisibility: high

# mean con RTS for lateralised stimuli
conRT_lateral; condition: congruent; response.corr: 1; stimLocation: left,right

# alternatively
conRT_lateral; condition: congruent; response.corr: 1; stimLocation: [!=]centre
```

### Error analysis

**Example content of error configuration file**

```
# accuracies for congruent and incongruent trials
conAcc; condition: congruent; response.corr: 1/0,1
inconAcc; condition: incongruent; response.corr: 1/0,1

# alternatively, using error rates
conErr; condition: congruent; response.corr: 0/0,1
inconAcc; condition: incongruent; response.corr: 0/0,1

# accuracies for high-vis trials only
conAcc_highVis; condition: congruent; response.corr: 1/0,1; stimVisibility: high
inconAcc_highVis; condition: incongruent; response.corr: 1/0,1; stimVisibility: high
```

## The output

* In the folder `output`, there will be a file called `output.csv`: This file contains the means or medians (dependent on what you chose in the general settings file) and error rates (if you asked for them) for every participant and condition
* In the same folder, there will be a Markdown file called `report.md`
  * It provides basic sanity checks for every participant (e.g., overall percentage of timeouts and errors)
  * It provides information about the distribution of RTs
  * It tells you how many trials were rejected as outliers
* You can view the Markdown output file using a dedicated Markdown viewer, or a Markdown extension for your browser
