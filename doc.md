---
title: Documentation
---

Due to the size and scale of MMID, we provide a few different views of the dataset.

 - **<span style="color:#B08519">[raw]</span>** On the large end, we have raw image and web crawls for up to 10,000 words in each of 100 languages.
This is about **21TB**. 
 - **<span style="color:#B08519">[med]</span>** For 30 languages, we extracted CNN features and plaintext for all words of a language. Using these, you can recreate or improve on the translation results of our ACL paper. This is about **TBD TB**.
 - **<span style="color:#B08519">[small]</span>** For the same 30 languages, we provide a "toy" sample for development, with 1 image representing each word. The format of this view is the same as \[med\]. This is about **TBD TB**.


## **<span style="color:#B08519">[raw]</span>** Documentation
In this view of the data, we present images unfiltered by language, and webpage crawls without any pre-processing, providing MMID data in its entirety.

### Image dataset structure

Each language has its own image package, named `<LANGUAGE>-package.tar`.
The structure of each iamge package is as follows, where `n` is the number of words represented for the language in the dataset:

```
    /
        0.tar.gz
            word.txt
            metadata.json
            errors.json
            /<DD>.<EXT> # image
            ...
            /<DD>.<EXT>
        ...
        <n-1>.tar.gz
```
In this structure, each word in the dataset has its own gzipped tarball, named by the index of the word in the language's dictionary, e.g., `0.tar.gz`.
In the tarball is `word.txt`, which contains the plaintext of the word, as well as `errors.json`, a log of errors encountered during the image scrape.

The tarball also contains `metadata.json`, which includes crawl metadata like the URLs of the images stored in the tarball, with the following structure:

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
Finally, the tarball contains up to 100 image files, of the form `<D>.<EXT>` where `<DD>` is a two- or three-digit numeral between 01 and 100, and EXT is the filetype of the image, which varies.

### Text crawl dataset
In the raw dump, we present the text crawl corresponding to our web crawl in a completely unadulterated form.
We crawled web pages using the Nutch crawler, and release the output of the crawls in [WARC](https://www.loc.gov/preservation/digital/formats/fdd/fdd000236.shtml), a standard, readable, maintainable format.

The data for each language is contained at `<LANGUAGE>-text.tar`.
The crawl for each language is split arbitrarily into multiple segments.
Each segment name has a name similar to `20170223072936-part-00000.seg-00000.attempt-00000.warc.gz`, but the names are arbitrary.
While we omit an in-depth description of the WARC format here, each segment consists of plain text (after unzipping) similar to

```
WARC/1.0
WARC-Record-ID: <urn:uuid:4b83c423-3f22-4ee1-b8ea-c1a659774d7a>
Content-Length: 65536
WARC-Date: 2017-02-25T18:01:51Z
WARC-Type: resource
WARC-Target-URI: http://02elf.net/headlines/politics-headlines/nrw-cdu-will-fluechtlinge-rigoros-zurueckfuehren-962524

<!DOCTYPE html>
<html  xmlns="http://www.w3.org/1999/xhtml" prefix="og: http://ogp.me/ns# fb: https://www.facebook.com/2008/fbml" lang="de-DE" class="no-js">
<head>
...content...
```

Thus, the HTML of each page is preceded by metadata, and succeeded by the metadata of the next page. 


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
     -f <path_to_source_features_lang_name>  \
     -e /nlp  <path_to_English_features>  \
     -d <path_to_dictionaries_folder> \
     -o <path_to_desired_results_folder> \
     -l <word_limit--try 100>
     -t 25 \
     -tc 25 \
```
