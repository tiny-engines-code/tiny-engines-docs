
< [Back](main.md)
<br>


![NFL Workflow](../.gitbook/assets/NFL/nfl.png)
<br>

The jobs are meant to run autonomously, but for demo purposes we can manually run the ETL and feature selection jobs from the notebooks/nfl_load_nflverse_data_demo.ipynb notebook.
- <font color=teal>**nfl_load_nflverse_data_demo.ipynb**</font> - demos manually running the load and build jobs (nfl_00 - 01)
- <font color=teal>**nfl_perform_feature_selection_demo.ipynb**</font> - demos manually running weekly stats and feature selection jobs (nfl_02 - 04)

| Job                                                         | Stage         | Description                                                  |
|-------------------------------------------------------------|---------------|--------------------------------------------------------------|
| <font color=teal>nfl_main.py</font>                         | orchestration | runs the ETL and feature selection for the win/loss model    |
| <font color=teal>nfl_00_load_nflverse_data.py</font>        | ETL           | downloads the nflverse data into local storage               |
| <font color=teal>nfl_01_build_nfl_database.py</font>        | ETL           | builds the nfl database from the nflverse data               |
| <font color=teal>nfl_02_prepare_weekly_stats.py</font>      | Experiment 2  | merges metrics from all datasets into a single stats dataset |
| <font color=teal>nfl_03_perform_feature_selection.py</font> | Experiment 2  | performs feature selection on the nfl data                   |
| <font color=teal>nfl_04_merge_game_features.py</font>       | Experiment 2  | merge our features with the core nfl play actions dataset    |


<br>

Finally, we can run the [Experiment 2 notebook](https://github.com/cjlcoursework/nflverse_project/blob/14786c6c93beb48f63fd321036c0f2882376921b/notebooks/nfl_win_loss_classification_experiment2.ipynb) 

