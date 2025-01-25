# Introduction to ASKAP and VAST
The Australian Square Kilometre Array Pathfinder (ASKAP) is a radio telescope in Western Australia engineered by CSIRO. The Variable and Slow Transients survey (VAST) uses ASKAP to probe new regions of space to find variable and transient phenomena, taking advantage of its wide field of view<sup>1</sup>.

# Table of contents
1. [Introduction to The Classification Pipeline](#introduction)
2. [Install](#install)
3. [The Folder Structure](#folder)
4. [Step-by-step Instructions](#stepbystep)
    1. [Obtaining The Trained Classifiers](#obtain_classifiers)
    2. [Using The Trained Classifiers on New Data](#using_new_data)
5. [Extra Information](#extra)
    1. [Contact](#contact)
6. [References](#references)

# 1. Introduction to The Classification Pipeline <a name="introduction"></a>

The VAST transient classification pipeline applies various machine learning classifiers to obtain possible transient candidates based on available data ranging from selected features to light curves to fits image cubes (images taken at each time point in an observation). Once the transient candidates are obtained, the user may manually check whether such candidates are actually transients or not. 

This pipeline should provide a means of significantly shortening the time required to identify transients from the large amounts of data from the VAST survey.

# 2. Install <a name="install"></a>
This pipeline should be run in a virtual environment on the Anaconda platform. The required libraries and modules are found in the __*"requirements.txt"*__ file.

To do this in the Anaconda prompt, use the code:

`conda create –name <virtual_environment_name> –file requirements.txt`

# 3. The Folder Structure <a name="folder"></a>
Before starting, the user must ensure that they have the following pre-existing folder structure. The guide to the diagram is as follows:

```
Folder
- Subfolder
```

```
Folder
- Subfolder
```

# 4. Step-by-step Instructions <a name="stepbystep"></a>
The most accurate classifiers are the __decision tree, MiniRocket and chi square map ResNet__ classifiers which require data in the form of a relevant feature dataframe, peak flux light curves and chi square maps. Examples of these are shown below:
<!-- New line here... -->

---

<!-- ... and new line here. -->

![Example Light curve](https://github.com/user-attachments/assets/4de89d72-33cf-4a4f-86b7-45628c137c6f)

__Figure 4.1.__ An example light curve that may be fed into the MiniRocket classifier. 
<!-- New line here... -->

---

<!-- ... and new line here. -->

![Obtaining the chi square maps](https://github.com/user-attachments/assets/5d80cc88-fd41-463b-a717-1b28fd067387)

__Figure 4.2.__ The chi square maps are calculated using the image cubes which contain images of a source taken at repeated intervals during an observation<sup>2</sup>. These maps are used to train a convolutional neural network with ResNet50 architecture.

<!-- New line here... -->

---

<!-- ... and new line here. -->

An overview to how to use the pipeline on new data would be as follows:
## 4.1. Obtaining The Trained Classifiers: <a name="obtain_classifiers"></a>
1. Collect all the feature data from the csv files in the __*"VAST_10s_CSV"*__ folder by running the script __*"VAST_Transient_Cleaned_Data.ipynb"*__.
2. Run the decision tree classifier script (__*"VAST_Decision_Tree_Classifier.ipynb"*__) which will be trained on the feature data from step 1, and save the decision tree in the __*"Models\Tree_clf"*__ folder.
3. Delete the __*"Rocket_Model"*__ folder in the __*"Models"*__ folder.
4. Run the MiniRocket classifier using the script __*"MiniRocket_Time_Series_Classification.ipynb"*__ which will train the classifier on the light curve data in the __*"VAST 10s lightcurve"*__ folder and the new classifier will be saved in the __*"Models\Rocket_Model"*__ folder.
5. Run the chi square map processing script (__*"Chi_Square_Map_and_Peak_Map_Processing.ipynb"*__) to obtain the train, validation and test data for the chi square maps of shape __(120,120,3)__.
6. Run the chi square map ResNet script (__*"Chi_Square_Map_and_Peak_Map_ResNet.ipynb"*__) which will train a ResNet convolutional neural network on the chi square maps and save the network in the __*"Models\CSM_Resnet"*__ folder. The best hyper parameters for the model will be saved in the __*"Models\CSM_Resnet_hyperparams"*__ folder.

## 4.2. Using The Trained Classifiers on New Data: <a name="using_new_data"></a>
7. To begin using the saved classifiers on new data, the user needs to have the feature data in the form of a csv similar to the format of the files in the __*"VAST_10s_CSV"*__.
8. For each candidate of the new data, the user must obtain their corresponding light curves and fits image cubes which have a shape of (number of time points, 120, 120). The light curves should be saved in __*VAST 10s light curve*__ or another folder and the fits image cubes should be saved in __*"VAST 10s fitscube"*__ or another folder.
9. Open the __*"Transient_Classifier_Pipeline.ipynb"*__ script and enter the name of the feature data csv for the new data into the variable: __*"data_file_name"*__.
10. Enter the name of the folder where the light curves are stored into the variable __*lc_folder_name*__ and enter the name of the folder where the fits cubes are stored into the variable __*"fits_folder_name"*__.
11. Run the __*"Transient_Classifier_Pipeline.ipynb"*__ script and the unique candidates from the decision tree, MiniRocket and chi square map ResNet classifiers will be saved in __*"Transient_candidates/candidates.csv"*__

The final saved dataframe of candidates should look like the following:
<!-- New line here... -->

---

<!-- ... and new line here. -->
![Candidate dataframe](https://github.com/user-attachments/assets/df92b4e9-8a52-40ce-a421-3342b22789d4)

__Figure 4.3.__ The final dataframe containing the SBID, beam ID and name of possible transients.
<!-- New line here... -->

---

<!-- ... and new line here. -->

# 5. Extra Information <a name="extra"></a>

For more information on the classifiers, there are descriptions available in their respective scripts. Also, the chi square map ResNet50 model in __*"Chi_Square_Map_and_Peak_Map_ResNet.ipynb"*__ after saving and loading in __*"Transient_Classifier_Pipeline.ipynb"*__ may have different predictive accuracy which is currently an unresolved issue. If you do not wish to use the ResNet50 model for this reason and you only wish to use the candidates from the decision tree and the MiniRocket classifiers, in line 49 of __*"Transient_Classifier_Pipeline.ipynb"*__, you can alter the code to:

```python
cand_df = pd.concat([tree_cand_df, rocket_cand_df, resnet_cand_df], ignore_index=True, axis=0)
```

## 5.1. Contact: <a name="contact"></a>
To obtain the data used to train the classifiers in the __*"VAST_10s_CSV"*__, __*"VAST 10s lightcurve"*__ and __*"VAST 10s fitscube"*__ folders, 
please email: yuanmingwang@swin.edu.au

# 6. References: <a name="references"></a>

