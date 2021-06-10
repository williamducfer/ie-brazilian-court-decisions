# Information Extraction from Brazilian Court decisions

System to extract and visualize information from Brazilian Court decisions.

---

## Experiments

### Neural network

*architecture_grid_search.py* script is used to perform the experiments to find the best combination of word embedding and neural network architecture, trained and evaluated through cross-validation. Inside the script, *define_model* method establishes candidate architectures to be assessed. The script produces a very detailed output. If one would like to compare the candidate models in a fast way, he/she to run the script *generate_qualities_csv_from_crf_grid_search_log_file.sh* . It generates a CSV with model ID, precision, recall, and F-score.

After finding the best word embedding and the best neural network architecture through cross-validation, one has to train the model, using the whole training data. *train_best_models.py* script was created for that purpose. In that script, one has to specify the architectures to be trained, dataset directories, and where files of models will be stored after training.

After training the model, it has to be evaluated, using the test data. *evaluate_best_models.py* script deals with models evaluation. In that script, one has to specify dataset directories to be evaluated, files of models, and char embedding information about each model.

### Conditional random fields (CRF)

*crf.py* script with *grid_search* parameter is used to find the best C1 and C2 hyperparameters for the model. If one would like to compare the candidate models in a fast way, he/she has to run the script *generate_qualities_csv_from_crf_grid_search_log_file.sh* that generates a CSV for fast comparison.

After finding the best C1 and C2 parameters in the previous step, one has to run *crf.py* script with *evaluate* parameter. It will train the CRF model with the specified C1 and C2 parameters on the provided training directory, and will evaluate the model in the provided evaluation directory.

---

## Predictions (neural network model)

*predict_with_best_models.py* script is responsible for generating predictions with a trained model. In that script, one has to specify dataset directories to generate predictions, files of models, char embedding information about each model, and output directories to save the predictions. At the end of the execution, for each prediction directory, there are the same files than in the original directories with an extra column that corresponds to predictions, and an additional file called *extracted_annotations.csv*. That file has all the predictions, one file per line. That CSV file is the base for all following steps.

---

## Data post-processing

Data post-processing is performed after the predictions are generated by a trained neural network. The objective is to add extra-columns that will be used by the dashboard in the next step. 

*preprocess_value_columns_in_extracted_annotations.py* script preprocesses the generated file *extracted_annotations.csv*, converting moral damage values and material damage values to decimal values, generating a new CSV with the original content and those two new columns. 

*add_international_classification_of_diseases_and_grouping_to_extracted_annotations.py* script processes *extracted_annotations.csv* file or a file derived of it (for instance, a file generated by *preprocess_value_columns_in_extracted_annotations.py* script), generating a new CSV with the original content and two additional columns: international classification of diseases (ICD) and grouping of ICDs. It is important to notice that script is only used in extracted annotations of Court reports, since the judge specifies *the health cause that motivated the judicial procedure* only in those reports.

---

## Dashboard

**structure**  
The directory's root must contain the R script files and two empty folders <in> and <out>, which will store the script output files.

**order**  
First, execute preprocessing_for_dashboard.R to generate the input files for the other R scripts.

**files**  
*preprocessing_for_dashboard.R* script preprocesses extracted annotations generated in the previous step of lower Court decisions, Appellate Court reports, and Appellate Court decisions.

The files required as input to preprocessing must have the following names: report_preprocessed.csv; lower_preprocessed.csv; appellate_preprocessed.csv. And they must be stored in the <in> folder. Next, we list the column names for each one. All types are strings.

report_preprocessed.csv columns name:
- Filename
- #CAUSA_SAUDE_MOTIVACAO
- #PEDIU_MEDICAMENTO_EXAME
- #PEDIU_PROCEDIMENTO
- #PEDIU_REEMBOLSO
- #PEDIU_TRATAMENTO
- #VALOR_DANO_MORAL
- #VALOR_HONORARIOS
- #VALOR_REEMBOLSO
- CID_CLASSIFICACAO
- CID_GRUPO
- #VALOR_DANO_MORAL_PREPROCESSED
- #VALOR_REEMBOLSO_PREPROCESSED

lower_preprocessed.csv columns name:
- Filename
- #MEDICAMENTO_EXAME
- #PROCEDIMENTO
- #REEMBOLSO
- #TRATAMENTO
- #VALOR_DANO_MORAL
- #VALOR_HONORARIOS
- #VALOR_REEMBOLSO
- #VALOR_DANO_MORAL_PREPROCESSED
- #VALOR_REEMBOLSO_PREPROCESSED

appellate_preprocessed.csv columns name:
- Filename
- #EXCLUIU
- #REEMBOLSO
- #VALOR_DANO_MORAL
- #VALOR_HONORARIOS
- #VALOR_REEMBOLSO
- #VALOR_DANO_MORAL_PREPROCESSED
- #VALOR_REEMBOLSO_PREPROCESSED

*dashboard_final.R* script generates an HTML dashboard that let one analyze the decisions made by a Brazilian Court.
The files required as input were generated from preprocessing_for_dashboard.R and stored in the <in> folder.

