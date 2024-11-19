# Instructions on how to run Anafora.Evaluate

The ```evaluate``` script comes from [anaforatools](https://github.com/bethard/anaforatools/tree/master).   Description of ```anafora.evaluate``` from GitHub: 
> compares two sets of Anafora XML files in terms of precision, recall, etc.

```anafora.evaluate``` compares one directory of Anafora XML annotations to another and prints statistics such as precision, recall and F-measure. It can also be used with a single Anafora XML directory to compute inter-annotator agreement.

## Installation
If anaforatools isn't installed yet, install it by running the following command:
```
pip install anaforatools
```

To see a more detailed explanation on how to run the ```evalute script```, the ```--help``` command can provide more information. For example:
```
$ python -m anafora.evaluate --help
```

Running the ```--help``` argument will print the output below, showing that anafora.evaluate takes the following mandatory and optional arguments to evaluate the Anafora XML documents:

```
evaluate.py [-h] -r DIR [-p DIR] [-t DIR] [-i EXPR [EXPR ...]] [-e EXPR [EXPR ...]] [-x REGEX] [--temporal-closure] [--per-document] [--verbose] [--overlap]
```


## Arguments
The example code below shows the arguments needed to run the script:
```
$ python -m anafora.evaluate --reference reference --predicted predicted 
```
```anafora.evaluate``` takes the following commands: 
| Argument | Usage |
| ------ | ------ | 
| ```--r``` DIR, ```--reference``` DIR | Required. The root of a set of Anafora XML directories representing reference annotations. Input the file directory containing the Anafora folder and .XML notes contained within the folder. Running only ```--r``` DIR will compare annotator vs annotator, as long as there is more than 1 annotator in the folder. |
| ```--p``` DIR, ```--predicted``` DIR | The root of a set of Anafora XML directories representing system-predicted annotations. Input the file directory containing the Anafora folder and .XML notes contained within the folder. |
| ```--t```, ```--text```  | A flat directory containing the raw text. By default, the reference directory is assumed to contain the raw text. (Text is typically only needed with --verbose.) |
| ```--per-document``` |  Optional. Prints out scores for each document, rather than overall scores, which ```anafora.evaluate``` does by default.  For example, if there are 10 documents, prints out the difference per document rather than an overall difference. |
| ```--verbose```  | Optional. Include more information in the output, such as the reference expressions that were and the predicted expressions that were not in the reference. |
|  ```-i``` EXPR [EXPR ...], ```--include``` EXPR [EXPR ...]  | Optional. An expression identifying types of annotations to be included in the evaluation. The expression takes the form type[:property[:value]. For example, TLINK would only include TLINK annotations (and TLINK properties and property values) in the evaluation, while TLINK:Type:CONTAINS would only include TLINK annotations with a Type property that has the value CONTAINS. |
| ```-e``` EXPR [EXPR ...], ```--exclude``` EXPR [EXPR ...]  | Optional. An expression identifying types of annotations to be excluded from the evaluation. The expression takes the form type[:property[:value]. |
|  ```-x``` REGEX, ```--xml-name-regex``` REGEX | Optional.  A regular expression for matching XML files in the subdirectories, typically used to restrict the evaluation to a subset of the available files (default: '[.]xml$') |
| ```--temporal-closure``` | Optional.  Apply temporal closure on the reference annotations when calculating precision, and apply temporal closure on the predicted annotations when calculating recall. This must be combined with --include to restrict the evaluation to a Type:Property whose values are valid temporal relations (BEFORE, AFTER, INCLUDES, etc.) |
| ```--overlap``` | Optional.  Count predicted annotation spans as correct if they overlap by one character or more with a reference annotation span. Not intended as a real evaluation method (since what to do with multiple matches is not well defined) but useful for debugging purposes. |

The name of the folders do not have to match the same as the above code, as long as the directories point to the Anafora folders and files. For example, the -r FOLDER and -p FOLDER do not have to be labeled *predicted* and *reference* like the code block above example. 

The code will still run if a note is labeled *note_011_dis.Temporal_Relation.dave.completed*, because ```anafora.evaluate``` uses regex to match the relevant part of the Anafora file, *note_011_dis* with the .XML file format. *note_011_dis* needs to be in a folder named *note_011_dis* in the predicted directory if it already exists in the reference directory, if you call both arguments ```--reference``` and ```--predicted```. If the folders are named differently in the two arguments, then the code will not run properly.  

To better illustrate the example, the below directory will work when 
 ```anafora.evaluate``` is called upon with the following command:
```
$ python -m anafora.evaluate --reference dave --predicted gaby 
```
Correct folder naming:
 ```
├── dave/
│   └── note_011_dis/
│       └── note_011_dis.Temporal_Relation.dave.completed.XML
└── gaby/
│   └── note_011_dis/
│       └── note_011_dis.Temporal_Relation.gaby.completed.XML
```

The following command will give an error because the folder names are different between the two directories, shown in the folder structure below.
```
$ python -m anafora.evaluate --reference Reference --predicted Predicted 
```
Incorrect folder naming:
```
├── Reference/
│   └── note_011_dis/
│       └── note_011_dis.Temporal_Relation.dave.completed.XML
└── Predicted/
│     └── note_11_dis/
│          └── note_011_dis.Temporal_Relation.gaby.completed.XML
 ```
The file should be note_011_dis for both directories.

By default, anafora.evaluate will compare all types of relations (ALINK, EVENT, TIMEEX3, TLINK). Use ```--include``` or ```--exclude``` to modify the number of relations that will be evaluated. 

To make it easier to move text to the right directories, ```anafora.copy_text``` can be used, which "copies text into Anafora directory structure". ```anafora.copy_text``` takes two positional arguments, the original directory where the files are contained and the new directory in which the text is copied. The original directory can contain only text documents, and ```anafora.copy_text``` will copy the text and position the files into the correct folders. 

```
$ python -m anafora.copy_text originaltextdir new_text_directory
```
