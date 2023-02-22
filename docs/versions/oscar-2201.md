# OSCAR 22.01

OSCAR 2201 is the OSCAR version from January, 2022, the [November/December 2021 dump of Common Crawl](https://commoncrawl.org/2021/12/nov-dec-2021-crawl-archive-now-available/).
It features a **different** file layout that makes it **not** backward compatible with code designed to run with previous OSCAR versions.

[Request access](#){ .md-button }
[🤗 Datasets](#){ .md-button }
[Read the paper](#){ .md-button }

## Summary

OSCAR 22.01 is **document-oriented**, which means that rather than extracting lines and sorting them in language subcorpora, we identify documents as a whole. The main differences are that sentences in a document are contiguous and should make sense one after another, but sentences are not guaranteed to be of the subcorpus' language.

!!! note 
    As an example, the English Wikipedia page about [La Marseillaise](https://en.wikipedia.com/wiki/La_Marseillaise) contains sentences in French (The anthem's lyrics). In line-oriented corpora, these sentences would have been put in the French subcorpus. In OSCAR 22.01, they should be along with the article, in a document classified as English.

## Layout

As previous corpora, there is one subcorpus per language, plus **one new subcorpus for multilingual documents**.
Subcorpora are distributed in [JSONLines](https://jsonlines.org), split into 1GB chunks, then gzipped.

!!! note
    Splits are completely independent and self-contained: It is possible to only download `en_meta_134.jsonl.gz` and to do processing on it.

### Example document

```js
{
  "content":"newline\nseparaaaaaaaaaaated\ncontent", // (1)
  "warc_headers":{ // (2) 
    "warc-refers-to":"<urn:uuid:83f2e1d4-5ed3-41db-86ff-f7826c4c20f9>", 
    "warc-date":"2021-09-16T11:07:14Z",
    "warc-block-digest":"sha1:X3OWP47FG2O5LBNMFSNB44FJF2SSRC26",
    "warc-type":"conversion",
    "warc-identified-content-language":"eng",
    "content-length":"1694",
    "warc-target-uri":"https://foo.bar",
    "warc-record-id":"<urn:uuid:3304bc27-17d0-4ffd-a692-340381478a5f>",
    "content-type":"text/plain"
  },
  "metadata":{
    // (3)
    "identification":{
      "label":"en",
      "prob":0.6268374
    },

    // (4)
    "annotation":[
      "short_sentences",
      "footer"
    ],

    // (5)
    "sentence_identifications":[
      {
        "label":"en",
        "prob":0.93925816
      },
      null,
      {
        "label":"en",
        "prob":0.9606543
      }
    ]
  }
}

```

1. Content. Lines are separated by `\n`.
2. Headers from the crawler. Note that nothing is changed, so the content length may be incorrect.
3. Document-wide identification. `prob` is the weighted average of the confidence of identified lines.
4. Annotations of the document. `null` if no annotation.
5. Line-by-line identifications. `null` for each line that has no identification.

## Annotations

* `tiny`: The document has a low (<5) number of lines.
* `short_sentences`: The document has a high number (>50%) of short lines (<400 bytes)
* `header`: The document has a high number of short lines at its head, suggesting the presence of low quality content.
* `footer`: The document has a high number of short lines at its tail, suggesting the presence of low quality content.
* `noisy`: The document has a high percentage of punctuation (>50%)
* `adult`: The document contains adult content. This annotation uses a blocklist and labels a tiny part of the corpus: It does not catch most of the adult content.

More information about the thresholds and annotators are present in our [paper](https://oscar-project.org/publication/2022/arxiv/towards/).

## Filtering

!!! tip
    Filtering can be done using [`oscar-tools`](https://github.com/oscar-corpus/oscar-tools), a high performance toolkit that provides rapid and efficient ways of transforming corpora into what you need. More info [here](oscar-tools).

Filtering can be done using classic Python tools, such as [`ujson`](https://github.com/ultrajson/ultrajson). 
While we don't supply a Python library enabling easy filtering/transformation for OSCAR 22.01, we provide some filtering examples that you can change to better suit your needs.

### Getting documents that come from Wikipedia only

Using filters on `warc_headers.warc-target-uri` makes filtering on URLs easy.

```py
TODO
```

### Extracting lines from non-annotated documents

Non-annotated documents are suspected to be cleaner than annotated ones, so extracting their content should be interesting to do. We extract lines from documents where `metadata.annotations == null`.

```py
TODO
```

### Getting Alemannic lines from the German corpus

As detailed in our paper, we found that the German corpus has a (relative to the Alemannic corpus size) important amount of Alemannic. We use a filter on `metadata.sentence_identifications` to extract those sentences.

```py
TODO
```

## Languages

OSCAR 22.01 has subcorpora for 142 languages (counting the Multilingual corpus).
The following table exhibits the size, number of documents and number of words for each of them.

Note that the size accounts for the raw uncompressed file size, counting metadata.

??? note "Language table"

    | Language                    | Size      | # Documents | # Words         |
    |:----------------------------|:----------|:------------|:----------------|
    | _Multilingual_              | 12.1 GB   | 1,210,685   | 936,187,711     |
    | Afrikaans                   | 47.0 MB   | 12,393      | 6,227,310       |
    | Albanian                    | 3.0 GB    | 437,287     | 326,325,149     |
    | Alemannic / Swiss German    | 363.6 kB  | 139         | 37,381          |
    | Amharic                     | 461.0 MB  | 37,513      | 30,481,153      |
    | Arabic                      | 84.2 GB   | 8,718,929   | 6,103,711,887   |
    | Aragonese                   | 10.6 kB   | 12          | 51              |
    | Armenian                    | 4.7 GB    | 379,267     | 268,031,270     |
    | Assamese                    | 221.2 MB  | 17,084      | 11,109,557      |
    | Asturian                    | 73.6 kB   | 77          | 3,919           |
    | Avaric                      | 18.6 kB   | 14          | 582             |
    | Azerbaijani                 | 3.5 GB    | 491,847     | 291,927,692     |
    | Bangla                      | 15.1 GB   | 1,171,501   | 751,877,226     |
    | Bashkir                     | 95.5 MB   | 11,198      | 5,418,474       |
    | Basque                      | 1.1 GB    | 233,658     | 97,092,942      |
    | Belarusian                  | 1.8 GB    | 180,046     | 107,227,860     |
    | Bihari languages            | 24.2 kB   | 27          | 569             |
    | Bishnupriya                 | 2.0 MB    | 271         | 98,419          |
    | Bosnian                     | 10.3 kB   | 10          | 422             |
    | Breton                      | 33.7 MB   | 16,119      | 3,111,619       |
    | Bulgarian                   | 35.1 GB   | 2,887,115   | 2,405,981,285   |
    | Burmese                     | 1.9 GB    | 158,733     | 44,835,970      |
    | Catalan                     | 13.9 GB   | 2,627,307   | 1,508,919,864   |
    | Cebuano                     | 44.6 MB   | 5,742       | 5,253,785       |
    | Central Kurdish             | 716.4 MB  | 84,950      | 43,913,025      |
    | Chechen                     | 14.0 MB   | 4,086       | 798,766         |
    | Chinese                     | 900.9 GB  | 56,524,518  | 23,149,203,886  |
    | Chuvash                     | 41.8 MB   | 4,750       | 2,465,782       |
    | Cornish                     | 1.4 kB    | 2           | 55              |
    | Croatian                    | 11.2 MB   | 11,462      | 505,369         |
    | Czech                       | 58.6 GB   | 10,381,916  | 5,452,724,456   |
    | Danish                      | 12.6 GB   | 2,265,479   | 1,454,439,292   |
    | Dimli (individual language) | 706 Bytes | 1           | 19              |
    | Divehi                      | 217.2 MB  | 24,067      | 10,112,205      |
    | Dutch                       | 114.0 GB  | 20,206,532  | 12,329,127,151  |
    | Eastern Mari                | 11.3 MB   | 1,612       | 641,525         |
    | Egyptian Arabic             | 2.8 MB    | 1,256       | 176,096         |
    | English                     | 3.2 TB    | 431,992,659 | 377,376,402,775 |
    | Esperanto                   | 558.3 MB  | 111,932     | 58,416,628      |
    | Estonian                    | 9.2 GB    | 1,362,524   | 820,975,443     |
    | Filipino                    | 646.5 MB  | 70,394      | 81,881,278      |
    | Finnish                     | 37.8 GB   | 4,948,961   | 2,900,615,928   |
    | French                      | 382.2 GB  | 52,037,098  | 41,713,990,658  |
    | Galician                    | 255.2 MB  | 88,803      | 27,051,212      |
    | Georgian                    | 7.1 GB    | 488,588     | 281,430,479     |
    | German                      | 496.7 GB  | 70,075,424  | 46,826,676,844  |
    | Goan Konkani                | 787.2 kB  | 46          | 38,831          |
    | Greek                       | 78.3 GB   | 6,738,546   | 5,031,242,803   |
    | Guarani                     | 9.0 kB    | 10          | 374             |
    | Gujarati                    | 4.8 GB    | 136,467     | 301,170,777     |
    | Hebrew                      | 30.3 GB   | 3,132,396   | 2,249,377,984   |
    | Hindi                       | 23.3 GB   | 1,529,907   | 1,534,799,198   |
    | Hungarian                   | 53.9 GB   | 6,866,062   | 4,598,787,907   |
    | Icelandic                   | 2.0 GB    | 396,183     | 210,365,124     |
    | Ido                         | 77.3 kB   | 105         | 2,690           |
    | Iloko                       | 97.9 kB   | 75          | 8,592           |
    | Indonesian                  | 17.4 GB   | 2,244,622   | 1,984,195,207   |
    | Interlingua                 | 40.2 kB   | 6           | 10,125          |
    | Irish                       | 45.6 MB   | 12,233      | 4,877,850       |
    | Italian                     | 229.3 GB  | 28,502,092  | 24,294,684,830  |
    | Japanese                    | 258.7 GB  | 36,328,931  | 5,592,948,356   |
    | Javanese                    | 152.7 kB  | 70          | 10,441          |
    | Kalmyk                      | 9.3 kB    | 9           | 250             |
    | Kannada                     | 2.6 GB    | 150,850     | 108,450,571     |
    | Karachay-Balkar             | 119.6 kB  | 91          | 4,089           |
    | Kazakh                      | 2.9 GB    | 261,085     | 157,267,307     |
    | Khmer                       | 1.9 GB    | 121,910     | 30,564,131      |
    | Komi                        | 119.9 kB  | 127         | 3,335           |
    | Korean                      | 51.8 GB   | 5,881,481   | 3,854,968,649   |
    | Kurdish                     | 150.3 MB  | 29,906      | 17,390,759      |
    | Kyrgyz                      | 518.6 MB  | 62,244      | 28,028,986      |
    | Lao                         | 337.1 MB  | 28,914      | 6,682,982       |
    | Latin                       | 4.1 MB    | 4,397       | 187,446         |
    | Latvian                     | 8.2 GB    | 1,032,987   | 707,361,898     |
    | Lezghian                    | 375.5 kB  | 124         | 19,250          |
    | Limburgish                  | 1.4 kB    | 2           | 41              |
    | Lithuanian                  | 20.0 GB   | 2,303,070   | 1,712,802,056   |
    | Lojban                      | 1.9 MB    | 570         | 260,542         |
    | Lombard                     | 2.6 kB    | 2           | 225             |
    | Low German                  | 9.0 MB    | 1,938       | 1,012,561       |
    | Lower Sorbian               | 707 Bytes | 1           | 17              |
    | Luxembourgish               | 15.8 MB   | 5,108       | 1,545,946       |
    | Macedonian                  | 3.6 GB    | 341,775     | 244,058,579     |
    | Maithili                    | 21.6 kB   | 23          | 483             |
    | Malagasy                    | 57.3 MB   | 3,028       | 7,279,056       |
    | Malay                       | 5.3 MB    | 5,228       | 217,818         |
    | Malayalam                   | 4.1 GB    | 250,972     | 137,831,247     |
    | Maltese                     | 2.5 MB    | 2,208       | 118,190         |
    | Marathi                     | 3.3 GB    | 250,376     | 160,179,233     |
    | Mazanderani                 | 128.2 kB  | 76          | 7,337           |
    | Minangkabau                 | 6.0 MB    | 585         | 614,613         |
    | Mingrelian                  | 7.6 MB    | 2,550       | 253,333         |
    | Mongolian                   | 2.8 GB    | 237,719     | 176,405,432     |
    | Nahuatl languages           | 8.7 kB    | 12          | 179             |
    | Nepali                      | 3.7 GB    | 391,947     | 177,885,116     |
    | Newari                      | 5.7 MB    | 1,134       | 273,837         |
    | Norwegian                   | 2.8 GB    | 973,188     | 279,182,902     |
    | Norwegian Nynorsk           | 6.8 MB    | 5,835       | 459,183         |
    | Occitan                     | 2.1 MB    | 373         | 31,061          |
    | Odia                        | 487.9 MB  | 52,942      | 23,755,902      |
    | Ossetic                     | 13.9 MB   | 3,560       | 800,430         |
    | Pashto                      | 490.3 MB  | 50,312      | 46,293,249      |
    | Persian                     | 77.4 GB   | 7,665,871   | 6,430,164,396   |
    | Piedmontese                 | 1.7 MB    | 698         | 188,270         |
    | Polish                      | 139.0 GB  | 19,301,137  | 12,584,498,906  |
    | Portuguese                  | 170.3 GB  | 23,735,707  | 18,441,864,893  |
    | Punjabi                     | 1.1 GB    | 68,094      | 70,068,604      |
    | Quechua                     | 744 Bytes | 1           | 14              |
    | Romanian                    | 49.2 GB   | 4,624,764   | 5,261,803,995   |
    | Russia Buriat               | 32.9 kB   | 39          | 785             |
    | Russian                     | 1.1 TB    | 76,060,844  | 62,811,122,663  |
    | Sakha                       | 65.6 MB   | 6,284       | 3,473,813       |
    | Sanskrit                    | 136.0 MB  | 4,472       | 5,671,369       |
    | Scottish Gaelic             | 137.7 kB  | 136         | 7,769           |
    | Serbian                     | 6.9 GB    | 577,472     | 482,932,670     |
    | Serbian (Latin)             | 931.8 kB  | 738         | 92,875          |
    | Sicilian                    | 1.5 kB    | 2           | 50              |
    | Sindhi                      | 117.1 MB  | 15,516      | 10,685,611      |
    | Sinhala                     | 2.0 GB    | 108,593     | 113,179,741     |
    | Slovak                      | 16.5 GB   | 2,409,555   | 1,619,121,944   |
    | Slovenian                   | 1.2 GB    | 351,894     | 118,400,246     |
    | Somali                      | 2.1 kB    | 3           | 109             |
    | South Azerbaijani           | 14.1 MB   | 5,381       | 693,746         |
    | Spanish                     | 381.9 GB  | 51,386,247  | 42,829,835,316  |
    | Sundanese                   | 5.0 MB    | 263         | 547,145         |
    | Swahili                     | 1.3 MB    | 462         | 123,050         |
    | Swedish                     | 48.0 GB   | 7,541,278   | 5,078,331,128   |
    | Tajik                       | 870.9 MB  | 46,366      | 56,627,727      |
    | Tamil                       | 11.4 GB   | 556,772     | 452,343,748     |
    | Tatar                       | 915.3 MB  | 76,398      | 51,875,265      |
    | Telugu                      | 3.4 GB    | 249,756     | 137,752,065     |
    | Thai                        | 66.1 GB   | 5,030,254   | 1,626,779,846   |
    | Tibetan                     | 234.5 MB  | 18,683      | 2,286,269       |
    | Turkish                     | 75.1 GB   | 10,826,031  | 6,421,221,358   |
    | Turkmen                     | 4.4 MB    | 2,485       | 276,632         |
    | Ukrainian                   | 48.8 GB   | 4,558,214   | 2,879,585,992   |
    | Emiliano-Romagnolo[eml]     | 901 Bytes | 1           | 53              |
    | Upper Sorbian               | 132.8 kB  | 110         | 8,825           |
    | Urdu                        | 3.4 GB    | 336,994     | 332,816,354     |
    | Uyghur                      | 201.9 MB  | 18,556      | 11,240,889      |
    | Uzbek                       | 19.9 MB   | 9,526       | 1,370,842       |
    | Vietnamese                  | 98.9 GB   | 9,587,233   | 12,283,185,482  |
    | Volapük                     | 825.9 kB  | 661         | 57,039          |
    | Walloon                     | 105.7 kB  | 138         | 4,386           |
    | Waray                       | 7.6 MB    | 933         | 830,872         |
    | Welsh                       | 409.3 MB  | 90,378      | 49,488,495      |
    | Western Frisian             | 75.3 MB   | 21,946      | 6,357,929       |
    | Western Mari                | 743.5 kB  | 155         | 43,916          |
    | Western Panjabi             | 46.7 MB   | 6,790       | 4,060,419       |
    | Wu Chinese                  | 137.2 kB  | 88          | 3,056           |
    | Yiddish                     | 232.5 MB  | 23,418      | 15,809,780      |
    | Yoruba                      | 24.7 kB   | 26          | 1,042           |
    | Multilingual                | 12.1 GB   | 1,210,685   | 936,187,711     |
