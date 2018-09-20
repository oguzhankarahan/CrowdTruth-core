# Getting Started with CrowdTruth

The **[CrowdTruth](http://crowdtruth.org/)** framework implements an approach to machine-human computing for collecting annotation data on text, images and videos. The central part of the framework is the collection of  **CrowdTruth metrics** that capture and interpret inter-annotator disagreement in crowdsourcing. The CrowdTruth metrics model the inter-dependency between the three main components of a crowdsourcing system -- workers, input data, and annotations. The goal of the metrics is to capture the degree of ambiguity in each of these three components.

This document shows how to get started using the [CrowdTruth Python package](https://github.com/CrowdTruth/CrowdTruth-core) to process data collected from crowdsourcing microtasks. A detailed description of the CrowdTruth metrics is available in [this paper](https://arxiv.org/abs/1808.06080). Other useful resources are:

* [Papers](http://crowdtruth.org/papers/) about CrowdTruth
* [Datasets](http://data.crowdtruth.org/) collected using CrowdTruth
* [CrowdTruth project homepage](http://crowdtruth.org/)

If you use this software in your research, please consider citing:

```
@article{CrowdTruth2,
  author    = {Anca Dumitrache and Oana Inel and Lora Aroyo and Benjamin Timmermans and Chris Welty},
  title     = {CrowdTruth 2.0: Quality Metrics for Crowdsourcing with Disagreement},
  year      = {2018},
  url       = {https://arxiv.org/abs/1808.06080},
}
```

## Installation

To install the stable version from PyPI, install *pip* for your OS, then install package using:
```
pip install crowdtruth
```

To install the latest version from source, download the library and install it using:
```
python setup.py install
```


## How to run

After installing the CrowdTruth package, you can run the metrics on your own crowdsourced data. We currently support automated processing of files generated by [Amazon Mechanical Turk](https://www.mturk.com/) and [Figure Eight](https://www.figure-eight.com/). It is also possible to define your own [custom file format](TODO:add_link).

### 1. Define the configuration

The pre-processing configuration defines how to interpret the raw crowdsourcing input. To do this, we need to define a configuration class.

```
import crowdtruth
from crowdtruth.configuration import DefaultConfig

class TestConfig(DefaultConfig):
  ...
```

Our test class inherits the default configuration `DefaultConfig`. The following attributes can be used to customize the configuration to the task:

* **`inputColumns`:** list of input columns from the .csv file with the input data
* **`outputColumns`:** list of output columns from the .csv file with the answers from the workers
* **`csv_file_separator`:** string that separates between the columns in the file, default value is `,`
* **`annotation_separator`:** string that separates between the crowd annotations (the columns defined in `outputColumns`), default value is `,`
* **`none_token`:** string corresponding to the name of the annotation vector component that counts how many workers picked no answer for a given unit; set to `NONE` by default
* **`remove_empty_rows`:** boolean variable controlling whether to remove empty judgments from the data, or to replace them with `none_token`; default value is `True`
* **`open_ended_task`:** boolean variable defining whether the task is open-ended (i.e. the possible crowd annotations are not known beforehand, like in the case of free text input) or not (i.e. the crowd picks from a pre-selected list of annotations)
* **`annotation_vector`:** list of possible crowd answers, obligatory when `open_ended_task` is `False`
* **`processJudgments`:** method that defines additional processing of the raw crowd data


### 2. Pre-process the data

After declaring the configuration of our input file, we are ready to pre-process the crowd data:

```
data, config = crowdtruth.load(
    file = ...,
    config = TestConfig()
)
```

To process all of the files in one folder with the same pre-defined configuration, replace the `file` attribute of `crowdtruth.load` with `directory`.


### 3. Calculate the metrics

The pre-processed data can then be used to calculate the CrowdTruth metrics:

```
results = crowdtruth.run(data, config)
```

The `crowdtruth.run` method returns a dictionary object with the following keys:

* `units`: quality metrics for the input units
* `workers`: quality metrics for the workers
* `annotations`: quality metrics for the crowd annotations

## Example tasks

Below you can find a collection of Jupyter Notebooks that show how to use the CrowdTruth package on different types of crowdsourcing tasks:

**Binary choice tasks:** the crowd picks 1 annotation out of 2 choices (e.g. `True` and `False`)

* [Person identification in videos](https://github.com/CrowdTruth/CrowdTruth-core/blob/master/tutorial/notebooks/Binary%20Choice%20Task%20-%20Person%20Identification%20in%20Video.ipynb)
* [Relation extraction from sentences](https://github.com/CrowdTruth/CrowdTruth-core/blob/master/tutorial/notebooks/Binary%20Choice%20Task%20-%20Relation%20Extraction.ipynb)

**Ternary choice tasks:** the crowd picks 1 annotation out of 3 choices, (e.g. `True`, `False` and `None/Other`)

* [Person identification in videos](https://github.com/CrowdTruth/CrowdTruth-core/blob/master/tutorial/notebooks/Ternary%20Choice%20Task%20-%20Person%20Identification%20in%20Video.ipynb)

**Multiple choice tasks:** the crowd picks multiple annotation out of a set list of choices that are *the same* for every input unit

* [Person identification in videos](https://github.com/CrowdTruth/CrowdTruth-core/blob/master/tutorial/notebooks/Multiple%20Choice%20Task%20-%20Person%20Type%20Annotation%20in%20Video.ipynb)
* [Relation extraction from sentences](https://github.com/CrowdTruth/CrowdTruth-core/blob/master/tutorial/notebooks/Multiple%20Choice%20Task%20-%20Relation%20Extraction.ipynb)

**Sparse multiple choice tasks:** the crowd picks multiple annotation out of a set list of choices that are *different* across input units

* [Person identification in videos](https://github.com/CrowdTruth/CrowdTruth-core/blob/master/tutorial/notebooks/Sparse%20Multiple%20Choice%20Task%20-%20Person%20Annotation%20in%20Video.ipynb)
* [Relation extraction from sentences](https://github.com/CrowdTruth/CrowdTruth-core/blob/master/tutorial/notebooks/Sparse%20Multiple%20Choice%20Task%20-%20Relation%20Extraction.ipynb)
* [Event extraction from sentences](https://github.com/CrowdTruth/CrowdTruth-core/blob/master/tutorial/notebooks/Sparse%20Multiple%20Choice%20Task%20-%20Event%20Extraction.ipynb)

**Open-ended extraction tasks:** the crowd creates different combinations of annotations based on the input unit (e.g. extracting named entities from a paragraph, identifying objects in an image by drawing bounding boxes)

* [Person identification by highlighting words in text](https://github.com/CrowdTruth/CrowdTruth-core/blob/master/tutorial/notebooks/Dimensionality%20Reduction%20-%20Stopword%20Removal%20from%20Media%20Unit%20%26%20Annotation.ipynb)
* [Event extraction by highlighting words in text](https://github.com/CrowdTruth/CrowdTruth-core/blob/master/tutorial/notebooks/Highlighting%20Task%20-%20Event%20Extraction.ipynb)

**Free input:** the crowd inputs all possible annotations for an input unit

* [Person identification in videos](https://github.com/CrowdTruth/CrowdTruth-core/blob/master/tutorial/notebooks/Free%20Input%20Task%20-%20Person%20Annotation%20in%20Video.ipynb)