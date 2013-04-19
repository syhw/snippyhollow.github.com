---
layout: page
title: "htk pipeline"
date: 2013-04-17 12:39
comments: true
sharing: true
footer: true
---

If you don't have it, clone it from:

    git clone gitolite@habilis:htk-pipeline.git

### Summary: the workflow

    make prepare dataset=PATH_TO_YOUR_DATASET
    make train_monophones dataset_train_folder=PATH_TO_YOUR_DATASET/train
    make test_monophones dataset_test_folder=PATH_TO_YOUR_DATASET/test

### Your dataset

HTK needs MFC files (`.mfc`, encoded with `HCopy` preferably) the corresponding 
labels. From that we produce one master label format (`.mlf`) files, which is 
just the concatenation of all labels in a gigantic file, with corresponding 
paths; and one `.scp` file which gives the paths to the `.mfc`. 

So, to begin the training with HTK, you need two folders `train` and `test`, 
containing at least `.mfc` files and the corresponding the two `.scp` and 
`.mlf` files. Fortunately, `htk-tools` includes several dataset preparation 
scripts. 

### Preparation study case: TIMIT

TIMIT comes with `.wav` files (at 16Khz, without correct headers) and `.phn` 
files for the phones labels (annotated in frames). Let us have a look at 
what `make prepare` does:

    python src/mfcc_and_gammatones.py --htk-mfcc $(dataset)/train
    python src/mfcc_and_gammatones.py --htk-mfcc $(dataset)/test

This uses the `wav_config` file and `HCopy` to produce MFCC from all the wave 
files that are contained (recursively explore) the `train` and `test` folders 
of the dataset. These python scripts can also produce the gammatones and should 
be where all the signal processing goes.

	python src/timit_to_htk_labels.py $(dataset)/train
	python src/timit_to_htk_labels.py $(dataset)/test

This step is TIMIT specific: as the `.phn` files are in frames, it converts 
the labeling in nanoseconds and saves that as `.lab` files.

	python src/substitute_phones.py $(dataset)/train --sentences
	python src/substitute_phones.py $(dataset)/test --sentences

This step substitutes phones according to the dictionary at the beginning of 
the script. Here, it maps all the phones from TIMIT to the 39 phones of the 
English subset that everyone agrees on. The `--sentences` is optional and 
makes it that we use `<s>` and `</s>` as start and end of sentences symbols. 

We are almost done, now:

	python src/create_phonesMLF_list_labels.py $(dataset)/train
	python src/create_phonesMLF_list_labels.py $(dataset)/test

Will create `${folder}.mlf`, `${folder}.scp`, `labels` and `dict` files in 
`$folder_path`, for both the `train` and `test` folders.

### Training

Once the preparation described above is done, the training should be dataset 
agnostic, with the exception of having to decide for good triphones clustering 
(which, of all the possible triphones, should be separated, and which should be 
tied to each other). 

Train monophones HMM:

    make train_monophones dataset_train_folder=PATH_TO_YOUR_DATASET/train
    make test_monophones dataset_test_folder=PATH_TO_YOUR_DATASET/test

Or, train triphones:

    TODO
    make train_triphones dataset_train_folder=PATH_TO_YOUR_DATASET/train
    make test_triphones dataset_test_folder=PATH_TO_YOUR_DATASET/test

In the end, I wish to have a simple "make train / make test" with state of the 
art results.


### Results you can expect

On TIMIT, for phones recognition, the results I get are:

| [Phones]      | [Representation]    | [start/end] | [tune sil]  | [bigram LM] | [% correct]   |
| ------------- | ------------------- |:-----------:|:-----------:|:-----------:|:-------------:|
| Mono 39       | mono Gaussian       | no          | no          | no          | 50.90         |
| Mono 39       | 17 comp. GMM        | no          | yes         | no          | 70.53         |
| Mono 41       | mono Gaussian       | yes         | no          | no          | 60.29         |
| Mono 41       | mono Gaussian       | yes         | no          | yes         | 61.88         |
| Mono 41       | 17 comp. GMM        | yes         | yes         | no          | 71.51         |
| Mono 41       | 17 comp. GMM        | yes         | yes         | yes         | 71.81         |
| Mono 62       | 17 comp. GMM        | yes         | yes         | no          | 66.70 / 75.22 |
| Mono 62       | 17 comp. GMM        | yes         | yes         | yes         | 68.42 / 75.75 |
| Tri           | mono Gaussian       | yes         | yes         | no          |               |
| Tri           | mono Gaussian       | yes         | yes         | yes         |               |
| Tri           | 17 comp. GMM        | yes         | yes         | no          |               |
| Tri           | 17 comp. GMM        | yes         | yes         | yes         |               |

(66.70 / 75.22 means 66.70% correctness on 62 phones and 75.22% correctness by grouping the phones down to 39).

I encourage everyone to give me their results so that I can update this page 
with other datasets.

### Alignment and Forced Alignment

If you want to get phones alignments with a trained model, you can simply do:

    make reco_align input_scp=PATH_TO_SCP_FILE output_mlf=ALIGNED_MLF 

However, keep in mind that the results are only as good as your recognition 
rate!

If you have an input MLF with at least the phones, and possibly a "bad" 
alignment, you can force align it with:

    make align input_scp=PATH_TO_SCP_FILE output_mlf=ALIGNED_MLF input_mlf=PATH_TO_UNALIGNED_MLF

and you will get the aligned phones in the `ALIGNED_MLF` file. 


### /!\ Warnings /!\

 - If you compile HTK yourself, make sure that it works correctly. 
Unfortunately there is no test case. Try `echo "( < a | b > )" > gram && HParse gram wdnet` 
and see if you get a segfault. Particularly, you need to compile with GCC 
and not LLVM-GCC (otherwise silent errors ensue).
 - There is a bad case of "HTKism" in the fact that the wordnets produced by 
`HBuild dict wdnet` and `HParse gram wdnet` with a simple "OR(phones)" grammar 
gives very different results, respectively 68.18% and 70.53% for monophones 
with a maximum of 17 components GMM without start symbols on TIMIT. For 
monophones with 17 components GMM _with_ start symbols, I get respectively 69.92% 
vs 71.52%.


### More details about HTK

Of course there is the [HTK book](http://htk.eng.cam.ac.uk/docs/docs.shtml) 
to consult, but here are some details of the training and testing parts.

When working with HTK, there is the same information repeated over and over, 
scattered at different locations.

    TODO, clean and explain the following:

	# on training (3 times)
	HERest -I $(TMP_TRAIN_FOLDER)/train.mlf -S $(TMP_TRAIN_FOLDER)/train.scp -H $(TMP_TRAIN_FOLDER)/hmm_mono_simple0/macros -H $(TMP_TRAIN_FOLDER)/hmm_mono_simple0/hmmdefs -M $(TMP_TRAIN_FOLDER)/hmm_mono_simple1 $(TMP_TRAIN_FOLDER)/monophones0 

    # on silence models
    AT 2 4 0.2 {sil.transP}
    AT 4 2 0.2 {sil.transP}

    # on mixtures
	HHEd -H $(TMP_TRAIN_FOLDER)/hmm_mono_mix0/hmmdefs $(TMP_TRAIN_FOLDER)/TRMU2.hed $(TMP_TRAIN_FOLDER)/monophones0

    # on triphones
	HLEd -n $(TMP_TRAIN_FOLDER)/triphones0 -l '*' -i $(TMP_TRAIN_FOLDER)/wintri.mlf mktri.led $(TMP_TRAIN_FOLDER)/train.mlf

    # on contexts tying (triphones)
	python src/adapt_quests.py $(TMP_TRAIN_FOLDER)/monophones0 quests_example.hed $(TMP_TRAIN_FOLDER)/quests.hed
	mkclscript TB 350.0 $(TMP_TRAIN_FOLDER)/monophones0 > $(TMP_TRAIN_FOLDER)/tb_contexts.hed
    python create_contexts_tying.py QUESTIONS_FILE THRESHOLDS_FILE OUTPUT_FILE [FOLDER]

    # on bigram language models
	HLStats -b $(TMP_TRAIN_FOLDER)/bigram $(TMP_TRAIN_FOLDER)/dict $(TMP_TRAIN_FOLDER)/train.mlf
