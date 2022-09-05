# VisDA 2022 Challenge 

**[[Challenge website]](https://ai.bu.edu/visda-2022/)**
## Datasets preparation

The pytorch implementation of the ZeroWaste dataset can be found in [here](zerowaste_dataloader.py).

## Baseline (DAFormer)
### Set up the environment
We use the official implementation of the state-of-the-art transformer-based domain adaptation methods [DAFormer](https://arxiv.org/abs/2111.14887) as our baseline. 
To get started, please follow the instructions on how to set up the environment on the DAFormer [github page](https://github.com/lhoyer/DAFormer). 
### Download checkpoints
To download all relevant checkpoints, please use our script.
```shell
sh tools/download_checkpoints.sh
```



## Setup Datasets
**ZeroWaste V1:** Please, download zerowaste-f.zip (7 Gb) from [here](http://csr.bu.edu/ftp/recycle/visda-2022/) and extract them to `data/zerowaste-f`.

**ZeroWaste V2 (unlabeled)** Please, download zerowaste-v2-trainval.zip (12 Gb) from
[here](http://csr.bu.edu/ftp/recycle/visda-2022/) and extract
it to `data/zerowaste-v2-splits/`.

**SynthWaste:** Please, download synthwaste_splits.zip (48 Gb) from
[here](http://csr.bu.edu/ftp/recycle/visda-2022/) and extract it to `data/synthwaste`.

**SynthWaste-aug:** Please, download 	synthwaste_aug.zip (33Gb) from
[here](http://csr.bu.edu/ftp/recycle/visda-2022/) and extract it to `data/synthwaste-aug` (this folder contains only the augmented train set).

Note: if you want to retrain the model on the combination of SynthWaste-aug and ZeroWaste-f, please combine the folders of ZeroWaste V1 and SynthWaste-aug to the folder `data/synthwaste_aug_zerowastev1`. 


## Training

We provide the following config files:
 
1. `configs/daformer/zerowaste_to_zerowastev2_daformer_mit5.py` for training DAFormer on ZeroWaste V1 as source domain and ZeroWaste V2 as target domain.
2. `configs/source_only/zerowaste_to_zerowastev2_segformer.json` to train SegFormer on ZeroWaste V1 and test on ZeroWaste V2 (source-only). 
3. `configs/daformer/synthzerowaste_to_zerowastev2_daformer.py` to train DAFormer on the combination of SynthWaste-aug and ZeroWaste V1 as source domain and ZeroWaste V2 as target domain.
4. `configs/source_only/synthzerowaste_segformer.json` to train SegFormer on the combination of SynthWaste-aug and ZeroWaste V1 and test on ZeroWaste V2 (source-only). 

To train the model with a desired configuration, run
```shell
python -m tools.train /path/to/config/file --work-dir /path/to/experiment/folder
```
The checkpoints, full config file and other relevant data will be stored in the experiment folder. By default, the experiments will be saved to the `work_dirs` folder.
For more details on how to use the code, please see the [official DAFormer guide](https://github.com/lhoyer/DAFormer). 


## Testing & Predictions

To evaluate the model and output the visual examples of the predictions, run the following command:

```shell
python -m tools.test /path/to/config/file /path/to/checkpoint.pth --eval mIoU --show-dir /path/to/output/predictions --opacity 1
```
To get the predictions for the validation set of ZeroWasteV2, please use the corresponding `*_val.json` config file or modify the config file in `configs/_base_/datasets/*.json` to point to the validation data_root in the test dataset configuration.
To produce final predictions in the original label mapping (0 = 'background', 1 = 'rigid_plastic', 2 = 'cardboard', 3 = 'metal', 4 = 'soft_plastic'), use the following script:
```shell
python -m tools.convert_visuals_to_labels /path/to/output/predictions /output/label/path/
```

## Submit on eval.ai
For evaluation, participants are required to join [VisDA-2022](https://eval.ai/web/challenges/challenge-page/1806/overview) the challenge on eval.ai. Please create an account on [eval.ai](https://eval.ai/) with the same email address you used to sign up for the challenge. Under the VisDA-2022 challenge, go to "Participate" and create a team with the same name as the one you registered. All members of a team need not be on eval.ai as long as one member in the team can submit. 

Information regarding challenge phases can be found under "Phases". We are currently in Phase-1, the development phase of the challenge. For evaluating your method, you can submit a zip file of your predictions in the format described below. eval.ai allows you to submit either via the web UI or via a [command line interface](https://cli.eval.ai/). In Phase-1 each time has a limit of 5 submissions per day. 

Once submitted, you can see the status of your submission under "My Submissions". Each evaluation takes around 9 mins during which your submission will be in the "Running" state. The server handles one submission at a time, so you may see your submission in the "Submitted" state before it gets to "Running".

Refer to evalai documentation for more information on how to [participate](https://evalai.readthedocs.io/en/latest/participate.html) and how to [make submissions public](https://evalai.readthedocs.io/en/latest/make_submission_public_private_baseline.html). We encourage participants to make their best submissions publicly visible on the leaderboard in the dev phase. Note that this will be a requirement in the test phase, for a participating solution to be considered for a prize.

Feel free to contact the visda organizers via [email](mailto:visda-organizers@googlegroups.com), or a github issue for questions regarding the interface.


## Submission format
For both phases, we ask the participants to submit their predictions in the following format: the submitted file should be a zip archive containing two folders: **source_only** and **uda** containing predictions of the source-only and adaptation version of their solution. Each folder should contain the predicted label maps in the original label mapping (0 = 'background', 1 = 'rigid_plastic', 2 = 'cardboard', 3 = 'metal', 4 = 'soft_plastic') that should have the same name and file extension as the corresponding input images. 

## Baseline results
The baseline results on the test and validation sets of ZeroWaste V2 are as follows:
| Experiment            |   test mIoU     |  test  Acc      |  val mIoU     |  val  Acc      |
| -----------           | ----------- | ----------- |  ----------- | ----------- |
| SegFormer V1          |     45.49   |     91.64   |        45.56      |      87.85       |
| SegFormer Synthwaste_aug+V1 |      42.61   |      91.22   |   41.2   |    87.19    |
| DAFormer V1->V2       |       52.26      |      91.2       |   50.84   |   87.29    |
| DAFormer SynthWaste_aug+V1->V2 |      48.31     |    90.63     |   46.29  |   87.0   |



## Custom simulation
We also allow the participants to generate their custom synthetic datasets to achieve the best performance. Please see how to use our simulation software [here](simulation/readme.txt). 
