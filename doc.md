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

### Running a translation evaluation

(temporary example)

```
python code/evaluate_package_cnn_combined.py 
     -f /scratch-shared/users/bcal/alexnet-combined-2/Gujarati/  \
     -e /nlp    /data/bcal/features/alexnet/  \
     -d /nlp/users/johnhew/image-translation/multilingual-google-image-scraper/dictionaries/ \
     -o /scratch-shared/users/johnhew/small-vocab-results/Gujarati \
     -t 25 \
     -tc 25 \
```
