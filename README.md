# Fellowship.ai-challenge---Language-Detection
Fellowship.ai challenge problem - Language Detection

In this project, we have 21 European languages: Romanic (French, Italian, Spanish, Portuguese, Romanian), Germanic (English, Dutch, German, Danish, Swedish), Slavik (Bulgarian, Czech, Polish, Slovak, Slovene), Finni-Ugric (Finnish, Hungarian, Estonian), Baltic (Latvian, Lithuanian), and Greek. And we need to build a language detection engines to identify different languages of text data. 

#### The data resource is European Parliament Proceedings Parallel Corpus 1996-2011 (http://www.statmt.org/europarl/)

For this task, the training corpus is processed in such a way: 1. Strip empty lines and their correspondences; 2. Remove lines with XML-Tags (starting with "<"); 3. Lowercase the text; 4. Split the text data to sentences by period; 5. Remove noisy characters and punctuation; 6. Transform long space to single space; 7. Control the length of each sentence ( > 1)

To balance the data, we control the number of sentences for each language. After preprocess the raw data, we split the `50000 * 21 = 1050000` sentences into train and validation part. 80% of the full dataset is train data and 20% of the full dataset is validation data, which means we have `50000 * 0.8 * 21 = 840000` sentences in the train data and `50000 * 0.2 * 21 = 210000` sentences in the test data.

Based on the literature 'Improved Text Language Identification for the South African Languages' [1] and Google Compact Language Detector 2 [2], we decide use `N-gram (TfidfVectorizer) + Naive Bayes` model to build the language detection engineer.

* [1] Duvenhage, Bernardt, Mfundo Ntini, and Phala Ramonyai. "Improved text language identification for the South African languages." Pattern Recognition Association of South Africa and Robotics and Mechatronics (PRASA-RobMech), 2017. IEEE, 2017.
* [2] https://github.com/CLD2Owners/cld2

In the `TfidfVectorizer` part,  we consider two parameters. One parameter is `analyzer`:  we use the  `char_wb` analyzer that  creates character n-grams only from text inside word boundaries; n-grams at the edges of words are padded with space. The other parameter is `ngram_range`: This parameter indicates the lower and upper boundary of the range of n-values for different n-grams to be extracted. Here we will tune the upper boundary of `ngram_range`(the largest N-gram that will be considered in feature engineering) from 1 to 6.  In the result, we find the performance didn't improve much from N > 4 on the valildation data. Thus, we choose n = 4 in the final model and there is 496647 N-gram ( 1 <= N <= 4) features in total.

Finally, we can see the performance of `N-gram (TfidfVectorizer) + Naive Bayes` is really good. It achieved accuracy with 0.9978 and F1 score with on 210000 validation data and accuracy with 0.9996 and F1 score with 0.9996 on test data.

# Future Work

According to the heatmap and the F1 score of each language on the validation data, we can see that many Lithuanian sentences was mislabeled as ohter languages. On the other hand,  we find that the recall for 'English' (en) is the best but the precision is the worst, which mean some sentences was mislabeled as 'en'. So we may add more instances for other language espicially Lithuanian data.

Moreover, we can try more amazing deep learning or NLP models, e.g. FastText, TextCNN, TextRNN, or make a ensembled model  based on them. I think the performance will aslo be good.
