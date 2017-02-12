Word2Vec: Paragraph Vectors + Logistic Regression
接下來我們要用具有將整個句子轉成 vector 能力的修改版 word2vec 來處理句子。值得注意的是，iclr15 中的 word2vec 版本，在句子數目太多時，會因為 vocabulary 數量太大，而造成許多句子被丟棄而無法正確轉成 embedding。

為了解決這個問題，我修改了程式碼 word2vec@shaform，讓我們可以用 @@SE 為開頭標示用來訓練 word embeddings 而不需要產生 paragraph vectors 的句子；以 @@SS 為開頭來標示需要產生 paragraph vectors 的句子。這樣一來就可以同時擁有大量的訓練資料，又可以完整產生所有指定的 paragraph vectors。不過在這個實驗中，為了方便，我們就不額外引進大量訓練資料，所以所有句子都會以 @@SS 開頭。

cd word2vec
cat ../data/train_pos.txt ../data/train_neg.txt ../data/test_pos.txt ../data/test_neg.txt | nl -v0 -s' ' -w1 | sed 's/^/@@SS-/' | shuf > all.txt
time ./word2vec -train all.txt -output vectors.txt -cbow 0 -size 400 -window 10 -negative 5 -hs 1 -sample 1e-3 -threads 24 -binary 0 -iter 20 -min-count 1 -sentence-vectors 1
grep '@@SS-' vectors.txt | sed -e 's/^@@SS-//' | sort -n > sentence_vectors.txt









Tools for computing distributed representtion of words
------------------------------------------------------

We provide an implementation of the Continuous Bag-of-Words (CBOW) and the Skip-gram model (SG), as well as several demo scripts.

Given a text corpus, the word2vec tool learns a vector for every word in the vocabulary using the Continuous
Bag-of-Words or the Skip-Gram neural network architectures. The user should to specify the following:
 - desired vector dimensionality
 - the size of the context window for either the Skip-Gram or the Continuous Bag-of-Words model
 - training algorithm: hierarchical softmax and / or negative sampling
 - threshold for downsampling the frequent words 
 - number of threads to use
 - the format of the output word vector file (text or binary)

Usually, the other hyper-parameters such as the learning rate do not need to be tuned for different training sets. 

The script demo-word.sh downloads a small (100MB) text corpus from the web, and trains a small word vector model. After the training
is finished, the user can interactively explore the similarity of the words.

More information about the scripts is provided at https://code.google.com/p/word2vec/

