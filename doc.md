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
        <source>-features/
        <source>-text/
        <source>-metadata/
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

#### `english-text/`
The `english-text` folder holds the tokenized plaintext on the webpages that images showed up on.
The structure is identical to that of `english-features`, where each English word is identified by a `<section_ID>/<word_ID>` pair.
Each image index in `english-features` corresponds to one `<image_ID>.gz` file, though text crawling failed for some images.

#### `english-metadata/`
The `english-metadata` folder holds the URLs of the images and corresponding websites for English images and words in the dataset.
The folder has the structure `<Section_ID>/<word_id>.json`, so each English word has a single JSON metadata file.
Each metadata file is a dictionary of the form:
```
{
    '<image_ID_1>': {
         'google': {
               'ru': <referring web page URL>
               }
         'image_url': <URL of downloaded image>
         }
    '<image_ID_2>': { ... }
    ...
    '<image_ID_100>': { ... }
}
```
     
         

#### `<source>-features/`

The `source-features/` folder holds the feature files for images of source language words.
The `source-features/` folder contains `word_ID` files, each of which is a `.pkl` matrix that is of dimension `(k,4096)` where `k` is the number of images that represent the word.
In the medium view, these images have already been filtered to those whose corresponding website had text in the `source` language.
The mapping between `word_ID` and source language literal word is given in `dictionaries/dict.<source_lang_ID` by the index of the word in the dictionary order.

```
    <source>-features/
       <word_ID>.combined.pkl
```

#### `<source>-text/`
The `<source>-text` folder holds the tokenized plaintext on the webpages that images for the `<source>` language showed up on.
Each `word_ID` is a folder, in which each image has a single `.txt` file containing all text for that image.
Thus, the structure is:
```
    <source>-text/
        <word_ID>/
            <image_ID>.txt
```

#### `<source>-metadata/`
The `<source>-metadata` folder holds the URLs of the images and corresponding websites for `<source>` language images and words in the dataset.
Each word has a single metadata file, `<word_id>.json`, in the directory.
Each metadata file is of identical structure to the English metadata files, above.


#### `dictionary/`
The `dictionary/` folder holds the gold-standard translations between `source` language words and English words in `dict.<lang_ID>`. It also contains a mapping from language name to language ID, used in our software, at `langcodes.csv`.


### Running a translation experiment

(in progress)

```
python code/evaluate_package_cnn_combined.py 
     -f /scratch-shared/users/bcal/alexnet-combined-2/Gujarati/  \
     -e /nlp    /data/bcal/features/alexnet/  \
     -d /nlp/users/johnhew/image-translation/multilingual-google-image-scraper/dictionaries/ \
     -o /scratch-shared/users/johnhew/small-vocab-results/Gujarati \
     -t 25 \
     -tc 25 \
```
