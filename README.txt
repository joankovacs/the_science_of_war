                    ______   __  __     ______    
                    /\__  _\ /\ \_\ \   /\  ___\   
                    \/_/\ \/ \ \  __ \  \ \  __\   
                       \ \_\  \ \_\ \_\  \ \_____\ 
                        \/_/   \/_/\/_/   \/_____/ 
                                                
 ______     ______     __     ______     __   __     ______     ______    
/\  ___\   /\  ___\   /\ \   /\  ___\   /\ "-.\ \   /\  ___\   /\  ___\   
\ \___  \  \ \ \____  \ \ \  \ \  __\   \ \ \-.  \  \ \ \____  \ \  __\   
 \/\_____\  \ \_____\  \ \_\  \ \_____\  \ \_\\"\_\  \ \_____\  \ \_____\ 
  \/_____/   \/_____/   \/_/   \/_____/   \/_/ \/_/   \/_____/   \/_____/ 
                                                                          
                        ______     ______  
                        /\  __ \   /\  ___\ 
                        \ \ \/\ \  \ \  __\ 
                         \ \_____\  \ \_\   
                          \/_____/   \/_/   
                        
                    __     __     ______     ______    
                    /\ \  _ \ \   /\  __ \   /\  == \   
                    \ \ \/ ".\ \  \ \  __ \  \ \  __<   
                     \ \__/".~\_\  \ \_\ \_\  \ \_\ \_\ 
                      \/_/   \/_/   \/_/\/_/   \/_/ /_/ 
                                                        

        
        
TABLE OF CONTENTS:
    [0] PROJECT PARAMETERS & DESCRIPTION
    [1] REVIEW OF SUBMISSION FILES
    [2] PROBLEM STATEMENT
    [3] REVIEW OF PROCESS & METHODOLOGY
    [4] RESULTS AND CONCLUSIONS
    
    
==========================================================================================
SECTION [0]:  PROJECT PARAMETERS & DESCRIPTION

The objective of this project is to use the MIDs dataset from the Correlates of War
project to predict the highest level of action a given side of a conflict will commit in 
the course of a MID (militarized interstate dispute) or one of the separate incidents that
are recorded within the dataset that comprise the MIDs.

The Correlates of War project, initially founded by J. David Singer in 1976, collectively
records data from miltarized interstate disputes from 1816-2014.  The third iteration of
the data collection process, MID3 (starting in 1993) began to collect separate incident
information - representing individual clashes, standoffs, maneuvers, or actions - within
the scope of the overarching narrative of conflict between two or more countries (the 
dispute).  Since 1993, there have been ~800 record disputes and ~4000 incidents that com-
prise those disputes. Because of the relative wealth of record entries from the incidents,
this project primarily focuses on predicting military actions of the incidents, rather
than the disputes.

The target variable of prediction is as follows, listed as 'hiact' in some of the datasets
and 'action' in others:
    [0]  No militarized action 
    [1]  Threat to use force 
    [2]  Threat to blockade 
    [3]  Threat to occupy territory 
    [4]  Threatto declare war 
    [5]  Threat to use CBR weapons 
    [6]  Threat to join war 
    [7]  Show of force 
    [8]  Alert 
    [9]  Nuclear alert 
    [10] Mobilization 
    [11] Fortify border 
    [12] Border violation 
    [13] Blockade 
    [14] Occupation of territory 
    [15] Seizure 
    [16] Attack 
    [17] Clash 
    [18] Declaration of war 
    [19] Use of CBR weapons 
    [20] Begin interstate war 
    [21] Join interstate war
    [-9] Missing

==========================================================================================
SECTION [1]:  REVIEW OF SUBMISSION FILES

'primary_models.ipynb'  -  Contains the best models run + some used for presenting
'README.txt' - This file.
'The Science of War.pptx' - Annotated powerpoint presenting the findings.

FOLDER: correlates_data - Contains the original data from the Correlates of War site
    FOLDER: codebooks - Information on the data
        'MID 5.0 Codebook.pdf' - Describes the values in the dataset
        'MID5 Draft Article of Record with Title Page.pdf' - Introduces MID5
    FOLDER: narratives - PDFs containing a short bit of text information about each dispute
        'MID Narratives 2011-2014.pdf' - Narratives from 2011-2014
        'MID_Narratives_1993-2001.pdf' - Narratives from 1993-2001
        'MID_Narratives_2002-2010.pdf' - Narratives from 2002-2010
    'ccodes.csv' - Names of the countries corresponding to country codes in the datasets
    'MIDA 5.0.csv' - One record per dispute
    'MIDB 5.0.csv' - One record per country per dispute
    'MIDI 5.0.csv' - One record per incident
    'MIDIP 5.0.csv' - One record per country per incident

FOLDER: final_data - csv files created by me for modelling
    'disputes.csv' - One record per dispute; hold information about each side.
    'incidents.csv' - One record per incident; hold information about each side.
    'incids_by_country.csv' - Brief foray into predicting hiact per country, not per side

FOLDER: initial_eda+models
    'feat_eng_disp.ipynb' - notebook used to create disputes.csv
    'feat_eng_incid.ipynb' - notebook used to create incidents.csv
    'model_ordinal_regression.ipynb' - notebook for experimental ordinal regression model
    'models_disputes.ipynb' - initial foray into modelling parameters for disputes
    'models_incidents.ipynb' - initial foray into modelling parameters for incidents

==========================================================================================
SECTION [2]:  REVIEW OF PROCESS & METHODOLOGY

The first step of the process was creating new dataframes to be used for modelling.  
Because I was predicting results for each record of dispute or incident, I had to add
information about each side (from MIDB and MIDIP) onto this new dataframe in a way that
resulted in one result per dispute or incident, not per country.  In my opinion, this is 
where most of the programmatically interesting work occurred.  The notebooks used for this
process are included.

Second, there presented a major problem in the form of categorical data: the countries for
each side are vital information to be represented in the data; however, there were over
150 unique country codes within the data.  With both sides represented for each conflict,
this resulted in a dummy-encoded dataframe with over 300 features.  This, predictably,
resulted in poor models.  Categorizing into continents would not represent actual war-
behavioral groups, either.  An attempt with DBScan grouping + labels was made; however,
the data are clustered tightly and the overwhelming majority of incidents were represented
in 1 or 2 groups.  Ultimately, the process chosen was dummifying the countries, dropping
columns that summed to 3 or less (countries with low representation that therefore should
not be given great weight in the predictions), and running PCA on the remainder for dim-
ensionality reduction.

Modelling:

Some initial, unrecorded forays into classification modelling yielded miserable accuracy results, which makes sense, given the sheer number of classes to predict.  No further
attempt at optimizing classification models was made.

An experimental model written by Fabian Pedregosa ( http://fa.bianp.net/blog/2013/logistic-ordinal-regression/ ) was also used.  It performed better than classification models but significantly underperformed any regression models.

The regression models used included:  RidgeCV, XGBoost Regressor, and SVR.  Among them,
SVR performed by far the best.  Post-prediction accuracy was improved by capping predict-
ion values at a minimum of 0 and a maximum of 21 (to be within the scope of the target).  
SVR resulted in a mean absolute error of well under 1 for each side.  Furthermore, the
predictions were rounded to the nearest whole number to obtain an accuray score, which
ranged from 60-90% (for more information, review the powerpoint).

Autosklearn ( https://automl.github.io/auto-sklearn/master/index.html ) was also used.  
It is worth mentioning because it performed on par with SVR, my best model, with no need
to meddle with hyperparameters, select a model, or even handle NaN values.  However, it
did not work without my preprocessing the data beforehand with dummy encoding and PCA 
because it resulted in asymmetries between train and test data due to the train test 
split.  

==========================================================================================
SECTION [3]:  RESULTS AND CONCLUSIONS

Relatively few features regarding the countries or conflict in question were represented
in the data.  Notably missing are: alliance networks, motives, military forces at hand,
positioning of military forces, industrial production, access to resources, technological
level, social or political disposition on the dispute, and other factors.  Despite this,
the model predicted how states would act with a very high degree of accuracy the over-
whelming majority of the time.  This suggests that states collectively behave in machine-
predictable ways, especially in warfare. 




