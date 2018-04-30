---
title: Documentation
---

Due to the size and scale of Imgt, we provide a few different views of the dataset.

 - **<span style="color:#B08519">[raw]</span>** On the large end, we have raw image and web crawls for up to 10,000 words in each of 100 languages.
This is about **26TB**. 
 - **<span style="color:#B08519">[med]</span>** For 30 languages, we extracted CNN features and plaintext for all words of a language. Using these, you can recreate or improve on the translation results of our ACL paper. This is about **TBD TB**.
 - **<span style="color:#B08519">[small]</span>** For the same 30 languages, we provide a "toy" sample for development, with 1 image representing each word. The format of this view is the same as \[med\]. This is about **TBD TB**.



## **<span style="color:#B08519">[med]</span>** Documentation

Download the dataset [here](downloads.html).
For the example translation experiment below, we assume that you've downloaded a medium language pack.

### Dataset structure

In this section, we'll discuss how to work with the medium view image feature downloads.
Each language download has the following macro file structure, each folder of which is described in more depth below.

```
    /
        english-features/
        english-text/
        english-metadata/
        <language>-features/
        <language>-text/
        <language>-metadata/
        dictionary/
```

####  `english-features/`

The `english-features/` folder contains CNN image features (the FC7 layer of an pretrained AlexNet network).
The image features are distributed across 27 sections of the English vocabulary, labelled `English-01` through `English-27`.
Each section contains some unique portion of the English vocabulary.
Each word of a section has an ID that is unique within the section but not across sections. These `word_ID`s are integers, and each `word_ID` is a folder within a section.
Each such `word_ID` folder has up to 100 `.pkl` files, one for each image that represents the given word.
The mapping between `<Section_ID>/<word_ID>` pairs and English word literals is given in `dictionaries/english_path_index.tsv`.

```
    english-features/
         English-01/
             <word_ID_1>
                <img1>.pkl
                <img2>.pkl
                ...
                <img100>.pkl
             <word_ID_2>
         English-02/
             ...
         ...
         English-27/
             ...
```

### `english-text/`

### `english-metadata/`

### `<source>-features/`

### `<source>-text/`

### `<source>-metadata/`


# Old docs

```
    /
        english-features/
           English-01/
               <word_ID>/
                 1.jpg.pkl
                 2.jpg.pkl
                 ...
                 100.jpg.pkl
               <word_ID>/
               ...
           English-02/
               ...
           ...
           English-27/
        source-features/
              <word_ID>.combined.pkl
              ...
        dictionary/
           dict.<lang_ID>
           langcodes.csv
           english_path_index.tsv
```

#### `english/`
The `english/` folder holds the feature files for images of English words.
The `english/` folder contains 27 subfolders, under each of which are `word_ID` folders.
Each `word_ID` folder contains all the image feature files for a single English word. Each feature file is a `.pkl` containing a vector that is of dimension `(1,4096)`.
You'll notice that not all English words are present for each language; by default, only the English words that are translations of some word in the downloaded language are present in the `english/` folder.
However, the identification of English words throug subfolder and `word_ID` is unique across all potentially downloadable languages, so you can combine your English directories across languages if desired.

#### `source/`
The `source/` folder holds the feature files for images of source language words.
The `source/` folder contains `word_ID` files, each of which is a matrix that is of dimension `(k,4096)` where `k` is the number of images that represent the word.
In the medium view, these images have already been filtered to those whose corresponding website had text in the `source` language.

#### `dictionary/`
The `dictionary/` folder holds the gold-standard translations between `source` language words and English words in `dict.<lang_ID>`. It also contains a mapping from language name to language ID, used in our software, at `langcodes.csv`.


### Running a translation experiment
```
python code/evaluate_package_cnn_combined.py 
     -f /scratch-shared/users/bcal/alexnet-combined-2/Gujarati/  \
     -e /nlp    /data/bcal/features/alexnet/  \
     -d /nlp/users/johnhew/image-translation/multilingual-google-image-scraper/dictionaries/ \
     -o /scratch-shared/users/johnhew/small-vocab-results/Gujarati \
     -t 25 \
     -tc 25 \
```
