# mRAT-SQL+GAP - Multilingual version of the RAT-SQL+GAP
Code and model from our BRACIS 2021 [paper published in Springer Lecture Notes in Computer Science](https://link.springer.com/chapter/10.1007%2F978-3-030-91699-2_35), [here the pre-print in arXiv](https://arxiv.org/abs/2110.03546).

Based on: RAT-SQL+GAP: [Github](https://github.com/awslabs/gap-text2sql). Paper: [AAAI 2021 paper](https://arxiv.org/abs/2012.10309)


## Abstract

mRAT-SQL+GAP is a multilingual version of the RAT-SQL+GAP, wich start with Portuguese Language. Here is available the code, dataset and the results.


## Directory Structure
Go to the directory where you want to install the structure
```bash
git clone https://github.com/C4AI/gap-text2sql
cd gap-text2sql/mrat-sql-gap 
```

## Conda mtext2slq Environment Setup
```bash
conda create --name mtext2sql python=3.7
conda activate mtext2sql
conda install pytorch=1.5 cudatoolkit=10.2 -c pytorch
pip install gdown
conda install -c conda-forge jsonnet
pip install -r requirements.txt
python -c "import nltk; nltk.download('stopwords'); nltk.download('punkt')"
conda install jupyter notebook
conda install -c conda-forge jupyter_contrib_nbextensions

```



## Setup Script
Just run this script below, it will copy the datasets.
The original version of the Spider dataset is distributed under the [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/legalcode) license.
The modified versions (translated to Portuguese, Spanish, French, double-size(English and Portuguese) and quad-size (English, Portuguese, Spanish and French)) of train_spider.json, train_others.json, and dev.json are distributed under the [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/legalcode) license, respecting ShareAlike.

```bash
chmod +x setup.sh
./setup.sh
```

## Specific setup
The models and checkpoints have big files (GBytes), so if you have enough disk space you can run all shell scripts. To understand how things work, run just BART_large.sh and after run the others.
```bash
./BART_large.sh
./mBART50MtoM-large.sh
./mT5_large.sh
./BERTimbau-base.sh
./BERTimbau-large.sh
```

## Environment Test
Now the environment is ready for training (fine-tune) and inferences. The training is very slow more than 60 hours for BART, BERTimbau, mBART50, and more than 28 hours for mT5. Therefore I recommend testing the environment with the inference.

### Preprocess dataset
This preprocess step is necessary both for inference and for training. It will take some time, maybe 40 minutes.
I will use the script for BART, but you can use the other, look the directory experiments/spider-configs
```bash
python run.py preprocess experiments/spider-configs/spider-BART-large-en-train_en-eval.jsonnet
```
You can see the files processed in the paths:
`data/spider-en/nl2code-1115,output_from=true,fs=2,emb=bart,cvlink`

## Inference
I will use the script for BART again. 
Note: We are making inferences using the checkpoint already trained (directory logdir) and defined in:
`experiments/spider-configs/spider-BART-large-en-train_en-eval.jsonnet`
`logdir: "logdir/BART-large-en-train",` and  
`eval_steps: [40300],`
```bash
python run.py eval experiments/spider-configs/spider-BART-large-en-train_en-eval.jsonnet
```

You then get the inference results and evaluation results in the paths:

`ie_dirs/BART-large-en-train/bart-large-en_run_1_true_1-step41000.infer` 

and 

`ie_dirs/BART-large-en-train/bart-large-en_run_1_true_1-step41000.eval`.

## Training
Execute if it is really necessary, if you want to fine-tune the model, this will take a long time. But if you have a good machine available and want to see different checkpoints in the logdir, do it.

```bash
python run.py train experiments/spider-configs/spider-BART-large-en-train_en-eval.jsonnet
```
You then get the training checkpoints in the paths:
`logdir/BART-large-en-train`

## Results

The results are in the directory [inference-results](https://github.com/C4AI/gap-text2sql/tree/main/mrat-sql-gap/inference-results).


## Checkpoints

The checkpoints are available here (ESM - Exact Set Matching Accuracy):
Paper mRAT-SQL+GAP - Multilingual version of the RAT-SQL+GAP
* BART-large trained in English [, ESM all: 0.718]
	* Checkpoint: [40300](https://drive.google.com/file/d/1F4R-WkJKtJ4lFni3q4lBug6tzSo0H5Qe/view?usp=sharing)
		* Inference - English: [ESM all: 0.718 - Baseline](https://github.com/C4AI/gap-text2sql/blob/main/mrat-sql-gap/inference-results/BART-large-en-train/spider_eval_match_ratsqlgap-BART-ori-step40300.txt)
* BERTimbau-base trained in Portuguese 
	* Checkpoint: [24100](https://drive.google.com/file/d/1gIZS0RuIxdjmm7sNbA3R6p6--9iMJmW8/view?usp=sharing)
		* Inference - Portuguese: [ESM all: 0.417](https://github.com/C4AI/gap-text2sql/blob/main/mrat-sql-gap/inference-results/BERTimbau-base-pt-train/spider_eval_match_ratsqlgap-bertimbau-base-step24100.txt)
* mBART50MtoM-large trained in English
	* Checkpoint [23100](https://drive.google.com/file/d/16mQf1gMTVGkvONUGpzELzkjCFX5M74cO/view?usp=sharing)
		* Inference - English: [ESM all: 0.651](https://github.com/C4AI/gap-text2sql/blob/main/mrat-sql-gap/inference-results/mBART50MtoM-large-en-train/spider_eval_match_ratsqlgap-mBART50MtoM-large-en-ori-data-step23100.txt)
* mBART50MtoM-large trained in Portuguese 
	* Checkpoint [39100](https://drive.google.com/file/d/1fWPH4bG9-UjW-p6OgmpINWLLsnOopWLh/view?usp=sharing)
		* Inference - Portuguese: [ESM all: 0.588](https://github.com/C4AI/gap-text2sql/blob/main/mrat-sql-gap/inference-results/mBART50MtoM-large-pt-train/spider_eval_match_ratsqlgap-mBART50MtoM-largeSimplemmaPtEn-step39100.txt)
* mBART50MtoM-large trained in English and Portuguese (together) 
	* Checkpoint [41000](https://drive.google.com/file/d/1szb44h_2t3fK2Vc02PdaAjDqnkWqM-0U/view?usp=sharing)
		* Inference - English: [ESM all: 0.664](https://github.com/C4AI/gap-text2sql/blob/main/mrat-sql-gap/inference-results/mBART50MtoM-large-en-pt-train/41000/spider_eval_match_ratsqlgap-mBART50MtoM-largeSimplemmaPtEn-pt-en-Eval-en-step41000.txt)
		* Inference - Portuguese: [ESM all: 0.595 Best inferences in Portuguese](https://github.com/C4AI/gap-text2sql/blob/main/mrat-sql-gap/inference-results/mBART50MtoM-large-en-pt-train/41000/spider_eval_match_ratsqlgap-mBART50MtoM-largeSimplemmaPtEn-pt-en-Eval-pt-step41000.txt)
	* Checkpoint [21100](https://drive.google.com/file/d/1MeLkvGf9-5it1JXnUvU9AmXVnnbAAfP0/view?usp=sharing)
		* Inference - English: [ESM all: 0.678 Best inferences in English](https://github.com/C4AI/gap-text2sql/blob/main/mrat-sql-gap/inference-results/mBART50MtoM-large-en-pt-train/21100/spider_eval_match_ratsqlgap-mBART50MtoM-largeSimplemmaPtEn-pt-en-Eval-en-step21100.txt)
		* Inference - Portuguese: [ESM all: 0.581](https://github.com/C4AI/gap-text2sql/blob/main/mrat-sql-gap/inference-results/mBART50MtoM-large-en-pt-train/21100/spider_eval_match_ratsqlgap-mBART50MtoM-largeSimplemmaPtEn-pt-en-Eval-pt-step21100.txt)

Future work of the paper mRAT-SQL+GAP
* BERTimbau-large trained in Portuguese
	* Checkpoint: [Checkpoint 40100](https://drive.google.com/file/d/1q1NOxisOcIdkMftzGPVxBDn989LDDG3X/view?usp=sharing)
		* Inference - Portuguese: [ESM all: 0.418](https://github.com/C4AI/gap-text2sql/blob/main/mrat-sql-gap/inference-results/BERTimbau-large-pt-train/spider_eval_match_ratsqlgap-BERTimbau-large-pt-train-Eval-pt-step40100.txt)
* mBART50MtoM-large trained in English, Portuguese, Spanish and French (together)
	* Checkpoint: [39100](https://drive.google.com/file/d/18nioEDEpZf-6CNH_sU3IMZxsSNts_a4y/view?usp=sharing)
		* Inference - English: [ESM all: 0.696](https://github.com/C4AI/gap-text2sql/blob/main/mrat-sql-gap/inference-results/mBART50MtoM-large-en-pt-es-fr-train/39100/spider_eval_match_ratsqlgap-mBART50MtoM-large-en-pt-es-fr-train_en-eval-step39100.txt)
	* Checkpoint: [42100](https://drive.google.com/file/d/1AmJjyVHiP9V-FzW9Q1sXge4YMWAP-srg/view?usp=sharing)
		* Inference - Portuguese: [[ESM all pt: 0.626](https://github.com/C4AI/gap-text2sql/blob/main/mrat-sql-gap/inference-results/mBART50MtoM-large-en-pt-es-fr-train/42100/spider_eval_match_ratsqlgap-mBART50MtoM-large-en-pt-es-fr-train_pt-eval-step42100.txt)
		* Inference - Spanish: [ESM all: 0.628](https://github.com/C4AI/gap-text2sql/blob/main/mrat-sql-gap/inference-results/mBART50MtoM-large-en-pt-es-fr-train/42100/spider_eval_match_ratsqlgap-mBART50MtoM-large-en-pt-es-fr-train_es-eval-step42100.txt)
	* Checkpoint: [44100](https://drive.google.com/file/d/1P0F218tNkW42Pb7okn3uFyTT5sy4zGZR/view?usp=sharing)
		* Inference - French: [ESM all: 0.649](https://github.com/C4AI/gap-text2sql/blob/main/mrat-sql-gap/inference-results/mBART50MtoM-large-en-pt-es-fr-train/44100/spider_eval_match_ratsqlgap-mBART50MtoM-large-en-pt-es-fr-train_fr-eval-step44100.txt)
	

Artigo mRAT-SQL-FIT


* mT5-large trained in English 51Ksteps[Checkpoint 50100, ESM all: 0.684](https://drive.google.com/file/d/1BZ519XxYtXpxxO1iiBy8kSLG4eq34yEX/view?usp=sharing)


* mT5-large trained in English, Portuguese, Spanish and French (together) 120Ksteps[Checkpint 77500, ESM all: 0.718 Best inferences in English](https://drive.google.com/file/d/1eUYr_i5O9U1ldm_pBdGozmiRt_42BCh8/view?usp=sharing)
* mT5-large trained in English, Portuguese, Spanish and French (together) 120Ksteps[Checkpint 85500, ESM all: 0.675 Best inferences in Portuguese](https://drive.google.com/file/d/1n55OlnyE3RDQtUXMHPwC99Za0xfQavrK/view?usp=sharing)
* mT5-large trained in English, Portuguese, Spanish and French (together) 120Ksteps[Checkpint 76500, ESM all: 0.675 Best inferences in Spanish](https://drive.google.com/file/d/1Qs-f2gIgWTJWiWrYGsiULxTBwwpgGatc/view?usp=sharing)
* mT5-large trained in English, Portuguese, Spanish and French (together) 120Ksteps[Checkpint 67500, ESM all: 0.681 Best inferences in French](https://drive.google.com/file/d/1cpTEXMhJXVbJfDc8sW1nfSX91p5VSJtn/view?usp=sharing)

* mT5-large trained in English, Portuguese, Spanish and French (together) FIT 120Ksteps[Checkpint 102100, ESM all: 0.687 Best inferences in Portuguese](https://drive.google.com/file/d/1VCfLnQgZsrb8lJFkhxzPoyfqc7dEY_K-/view?usp=sharing)
* mT5-large trained in English, Portuguese, Spanish and French (together) FIT 120Ksteps:
	* Checkpoint: [105100](https://drive.google.com/file/d/1h0knsFfD6XCXxoEVSFR_I1WdYvMOkcvA/view?usp=sharing)
	* Inference (simplemma.load_data('en','pt','es','fr')): [ESM all: 0.735 Best inferences in English](https://github.com/C4AI/gap-text2sql/blob/main/mrat-sql-gap/inference-results/mT5-large-FIT-en-pt-es-fr-120Ksteps/105100/spider_eval_match_ratsqlgap-mT5-large-NoGAP-120Ksteps-FIT-en-pt-es-fr_Div-en-eval-step105100.txt)
	* Inference (simplemma.load_data('en'): [ESM all: 0.736 Best inferences in English](https://github.com/C4AI/gap-text2sql/blob/main/mrat-sql-gap/inference-results/mT5-large-FIT-en-pt-es-fr-120Ksteps/105100/0.736/spider_eval_match_ratsqlgap-mT5-large-NoGAP-120Ksteps-FIT-en-pt-es-fr_Div-en-eval-step105100.txt)

* mT5-large trained in English, Portuguese, Spanish and French (together) FIT 120Ksteps[Checkpint 114100, ESM all: 0.689 Best inferences in Spanish and 0.698 in French](https://drive.google.com/file/d/13DIB5p97bUnquLpd-dO0-Q1bA9LWjIXD/view?usp=sharing)

* mT5-large trained in English, Portuguese, Spanish and French (together) 2048TKs - 480Ksteps
	* Checkpoint: [290100](https://drive.google.com/file/d/19Uvfw7QL-8i3yKvybRe9ADLDuSa-MeJv/view?usp=sharing)


## Security

See [CONTRIBUTING](CONTRIBUTING.md#security-issue-notifications) for more information.

## License

This project is licensed under the Apache-2.0 License.
